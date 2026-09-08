# Categoría: Logística

Problem statements típicos: optimización de rutas, visibilidad de inventario, predicción de demanda, gestión de almacén, seguimiento de entregas.

---

## Modelo de Datos

### Tablas

```sql
CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.PEDIDOS (
    PEDIDO_ID       VARCHAR(20) PRIMARY KEY,
    CLIENTE_ID      VARCHAR(15) NOT NULL,
    FECHA_PEDIDO    TIMESTAMP_NTZ NOT NULL,
    FECHA_PROMETIDA DATE NOT NULL,
    FECHA_ENTREGA   DATE,
    ESTATUS         VARCHAR(20) NOT NULL,  -- PENDIENTE, EN_PREPARACION, EN_TRANSITO, ENTREGADO, CANCELADO, DEVUELTO
    PRIORIDAD       VARCHAR(10),           -- NORMAL, EXPRESS, SAME_DAY
    MONTO_TOTAL     NUMBER(12,2),
    CIUDAD_DESTINO  VARCHAR(50),
    ESTADO_DESTINO  VARCHAR(30),
    CP_DESTINO      VARCHAR(5),
    ALMACEN_ORIGEN  VARCHAR(20),
    CANAL           VARCHAR(15)            -- ECOMMERCE, TIENDA, MAYOREO, B2B
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.INVENTARIO (
    SKU             VARCHAR(20) NOT NULL,
    ALMACEN_ID      VARCHAR(20) NOT NULL,
    FECHA           DATE NOT NULL,
    STOCK_DISPONIBLE NUMBER(8) NOT NULL,
    STOCK_RESERVADO NUMBER(8) DEFAULT 0,
    STOCK_EN_TRANSITO NUMBER(8) DEFAULT 0,
    PUNTO_REORDEN   NUMBER(8),
    COSTO_UNITARIO  NUMBER(10,2),
    CATEGORIA       VARCHAR(30),           -- ELECTRONICA, ALIMENTOS, TEXTIL, HOGAR, FARMACIA
    PERECEDERO      BOOLEAN DEFAULT FALSE,
    FECHA_CADUCIDAD DATE,
    PRIMARY KEY (SKU, ALMACEN_ID, FECHA)
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.ENVIOS (
    ENVIO_ID        VARCHAR(20) PRIMARY KEY,
    PEDIDO_ID       VARCHAR(20) NOT NULL,
    TRANSPORTISTA   VARCHAR(30),           -- FEDEX, DHL, ESTAFETA, PROPIO, UBER
    FECHA_DESPACHO  TIMESTAMP_NTZ,
    FECHA_ENTREGA_REAL TIMESTAMP_NTZ,
    ESTATUS         VARCHAR(20),           -- PREPARANDO, RECOLECTADO, EN_RUTA, EN_CENTRO, ULTIMA_MILLA, ENTREGADO, FALLIDO
    INTENTOS_ENTREGA NUMBER(2) DEFAULT 0,
    COSTO_ENVIO     NUMBER(8,2),
    PESO_KG         NUMBER(6,2),
    DISTANCIA_KM    NUMBER(6,1),
    LATITUD_DEST    NUMBER(9,6),
    LONGITUD_DEST   NUMBER(9,6),
    MOTIVO_FALLO    VARCHAR(50)            -- DIRECCION_INCORRECTA, NO_ENCONTRADO, RECHAZADO, DANADO
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.ALMACENES (
    ALMACEN_ID      VARCHAR(20) PRIMARY KEY,
    NOMBRE          VARCHAR(50) NOT NULL,
    CIUDAD          VARCHAR(50),
    ESTADO          VARCHAR(30),
    CAPACIDAD_PALLETS NUMBER(6),
    OCUPACION_PCT   NUMBER(5,2),
    TIPO            VARCHAR(20),           -- CENTRAL, REGIONAL, CROSS_DOCK, DARK_STORE
    LATITUD         NUMBER(9,6),
    LONGITUD        NUMBER(9,6)
);
```

### Foreign Keys

```
PEDIDOS.ALMACEN_ORIGEN → ALMACENES.ALMACEN_ID
ENVIOS.PEDIDO_ID → PEDIDOS.PEDIDO_ID
INVENTARIO.ALMACEN_ID → ALMACENES.ALMACEN_ID
```

---

## Volúmenes Recomendados

| Tabla | Filas | Notas |
|-------|-------|-------|
| ALMACENES | 12 | Mix de tipos y regiones |
| INVENTARIO | 36,000 | 500 SKUs × 12 almacenes × 6 fechas snapshot |
| PEDIDOS | 25,000 | 3 meses de pedidos |
| ENVIOS | 22,000 | ~88% de pedidos tienen envío (algunos cancelados) |

---

## Patrones Intencionales (Anomalías)

1. **Stockout prevenible**: 8 SKUs con STOCK_DISPONIBLE = 0 en almacén CENTRAL pero > 500 unidades en almacén REGIONAL que no atiende esa zona
2. **Entrega fallida sistemática**: 1 CP_DESTINO con > 40% de MOTIVO_FALLO = 'DIRECCION_INCORRECTA' (problema de geocoding)
3. **Promesa incumplida**: 200 pedidos EXPRESS con FECHA_ENTREGA > FECHA_PROMETIDA + 2 días
4. **Almacén saturado**: 2 almacenes con OCUPACION_PCT > 98% mientras otros del mismo ESTADO están al 40%
5. **Costo de envío anómalo**: 15 envíos con COSTO_ENVIO > $5,000 para DISTANCIA_KM < 10 (probable error de facturación)
6. **Inventario fantasma**: 3 SKUs PERECEDERO con FECHA_CADUCIDAD vencida pero STOCK_DISPONIBLE > 0 (merma no registrada)

---

## Validation Queries

```sql
-- Verificar volúmenes
SELECT 'ALMACENES' AS tabla, COUNT(*) AS filas FROM COCOTHON_DB.{{EQUIPO}}.ALMACENES
UNION ALL
SELECT 'INVENTARIO', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.INVENTARIO
UNION ALL
SELECT 'PEDIDOS', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.PEDIDOS
UNION ALL
SELECT 'ENVIOS', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.ENVIOS;

-- Verificar stockout con exceso en otro almacén
SELECT i1.SKU, i1.ALMACEN_ID AS almacen_sin_stock, i2.ALMACEN_ID AS almacen_con_exceso, i2.STOCK_DISPONIBLE
FROM COCOTHON_DB.{{EQUIPO}}.INVENTARIO i1
JOIN COCOTHON_DB.{{EQUIPO}}.INVENTARIO i2 ON i1.SKU = i2.SKU AND i1.ALMACEN_ID != i2.ALMACEN_ID
WHERE i1.STOCK_DISPONIBLE = 0 AND i2.STOCK_DISPONIBLE > 500
AND i1.FECHA = (SELECT MAX(FECHA) FROM COCOTHON_DB.{{EQUIPO}}.INVENTARIO);

-- Verificar promesas incumplidas
SELECT COUNT(*) AS entregas_tardias, AVG(DATEDIFF('day', FECHA_PROMETIDA, FECHA_ENTREGA)) AS dias_atraso_prom
FROM COCOTHON_DB.{{EQUIPO}}.PEDIDOS
WHERE PRIORIDAD = 'EXPRESS' AND FECHA_ENTREGA > DATEADD('day', 2, FECHA_PROMETIDA);

-- Verificar costos anómalos
SELECT ENVIO_ID, COSTO_ENVIO, DISTANCIA_KM, COSTO_ENVIO / NULLIF(DISTANCIA_KM, 0) AS costo_por_km
FROM COCOTHON_DB.{{EQUIPO}}.ENVIOS
WHERE COSTO_ENVIO > 5000 AND DISTANCIA_KM < 10;
```

---

## Ejemplos de Output

### Output 1: Streamlit Dashboard — "Control Tower Logístico"

```
Pantalla principal:
┌─────────────────────────────────────────────────┐
│  📦 Control Tower - {{EMPRESA}}                 │
├─────────────────────────────────────────────────┤
│  KPIs:                                          │
│  [On-Time: 82%] [Fill Rate: 94%] [OTIF: 78%]  │
│  [Costo/Envío: $145] [Entregas Fallidas: 6%]  │
├─────────────────────────────────────────────────┤
│  Mapa: envíos en tránsito (puntos por estatus) │
│  Heatmap: ocupación por almacén                │
│  Alertas: stockouts inminentes (< punto reorden)│
├─────────────────────────────────────────────────┤
│  Tabla: pedidos en riesgo (promesa < hoy)       │
└─────────────────────────────────────────────────┘
```

**Tecnologías Snowflake**: Streamlit con pydeck (mapas), Dynamic Tables para KPIs rolling, Cortex AI para predicción de fecha de entrega.

### Output 2: Cortex Agent — "Supply Chain Advisor"

```
Prompt: "¿Qué productos van a tener stockout esta semana?"

Agent response:
"Basado en velocidad de venta y stock actual, 5 SKUs tienen riesgo de
stockout en los próximos 7 días:

| SKU | Almacén | Stock Actual | Venta/Día | Días Restantes |
|-----|---------|-------------|-----------|----------------|
| SKU-0234 | CENTRAL-MX | 45 | 12 | 3.7 |
| SKU-0891 | REGIONAL-MTY | 22 | 8 | 2.8 |
| SKU-0445 | DARK-STORE-GDL | 8 | 4 | 2.0 |
| SKU-0112 | CENTRAL-MX | 30 | 7 | 4.3 |
| SKU-0667 | REGIONAL-QRO | 15 | 5 | 3.0 |

Acciones sugeridas:
1. Transferir SKU-0234 desde REGIONAL-PUE (tiene 520 unidades)
2. Activar reorden urgente para SKU-0891 y SKU-0445
3. SKU-0667 tiene stock en CENTRAL-MX, iniciar cross-dock"
```

**Tecnologías Snowflake**: Cortex Agent con forecasting (SNOWFLAKE.ML.FORECAST), Semantic View sobre inventario + pedidos.
