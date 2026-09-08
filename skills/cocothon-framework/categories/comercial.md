# Categoría: Comercial

Problem statements típicos: segmentación de clientes, churn prediction, análisis de pipeline, pricing optimization, market basket analysis.

---

## Modelo de Datos

### Tablas

```sql
CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.CLIENTES_COMERCIAL (
    CLIENTE_ID      VARCHAR(15) PRIMARY KEY,
    RAZON_SOCIAL    VARCHAR(150),
    NOMBRE_CONTACTO VARCHAR(100),
    INDUSTRIA       VARCHAR(30),           -- RETAIL, MANUFACTURA, SERVICIOS, TECH, SALUD, EDUCACION
    TAMANO          VARCHAR(15),           -- MICRO, PYME, MEDIANA, GRANDE, ENTERPRISE
    FECHA_ALTA      DATE NOT NULL,
    FECHA_ULTIMA_COMPRA DATE,
    LTV_ESTIMADO    NUMBER(12,2),
    SEGMENTO        VARCHAR(20),           -- VIP, A, B, C, INACTIVO
    VENDEDOR_ID     VARCHAR(15),
    REGION          VARCHAR(20),
    CANAL_ADQUISICION VARCHAR(20),         -- REFERIDO, COLD_CALL, INBOUND, EVENTO, PARTNER
    NPS_ULTIMO      NUMBER(3)              -- -100 a 100
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.VENTAS (
    VENTA_ID        VARCHAR(20) PRIMARY KEY,
    CLIENTE_ID      VARCHAR(15) NOT NULL,
    FECHA           TIMESTAMP_NTZ NOT NULL,
    PRODUCTO_ID     VARCHAR(20) NOT NULL,
    CANTIDAD        NUMBER(8) NOT NULL,
    PRECIO_UNITARIO NUMBER(10,2) NOT NULL,
    DESCUENTO_PCT   NUMBER(5,2) DEFAULT 0,
    MONTO_FINAL     NUMBER(12,2) NOT NULL,
    VENDEDOR_ID     VARCHAR(15),
    CANAL_VENTA     VARCHAR(15),           -- ONLINE, TIENDA, TELEFONO, FIELD_SALES
    ESTATUS         VARCHAR(15)            -- COMPLETADA, CANCELADA, DEVOLUCION
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.PRODUCTOS (
    PRODUCTO_ID     VARCHAR(20) PRIMARY KEY,
    NOMBRE          VARCHAR(100) NOT NULL,
    CATEGORIA       VARCHAR(30),           -- SOFTWARE, HARDWARE, SERVICIO, CONSUMIBLE, LICENCIA
    PRECIO_LISTA    NUMBER(10,2),
    COSTO           NUMBER(10,2),
    MARGEN_PCT      NUMBER(5,2),
    ACTIVO          BOOLEAN DEFAULT TRUE,
    FECHA_LANZAMIENTO DATE
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.PIPELINE (
    OPP_ID          VARCHAR(20) PRIMARY KEY,
    CLIENTE_ID      VARCHAR(15) NOT NULL,
    VENDEDOR_ID     VARCHAR(15) NOT NULL,
    ETAPA           VARCHAR(20) NOT NULL,  -- PROSPECTO, CALIFICACION, PROPUESTA, NEGOCIACION, CERRADA_GANADA, CERRADA_PERDIDA
    MONTO_ESTIMADO  NUMBER(12,2),
    PROBABILIDAD    NUMBER(3),             -- 0-100
    FECHA_CREACION  DATE NOT NULL,
    FECHA_CIERRE_ESPERADA DATE,
    FECHA_CIERRE_REAL DATE,
    MOTIVO_PERDIDA  VARCHAR(50),           -- PRECIO, COMPETENCIA, TIMING, NO_PRESUPUESTO, INACCION
    COMPETIDOR      VARCHAR(50),
    DIAS_EN_ETAPA   NUMBER(4)
);
```

### Foreign Keys

```
VENTAS.CLIENTE_ID → CLIENTES_COMERCIAL.CLIENTE_ID
VENTAS.PRODUCTO_ID → PRODUCTOS.PRODUCTO_ID
VENTAS.VENDEDOR_ID → CLIENTES_COMERCIAL.VENDEDOR_ID (vendedor asignado)
PIPELINE.CLIENTE_ID → CLIENTES_COMERCIAL.CLIENTE_ID
```

---

## Volúmenes Recomendados

| Tabla | Filas | Notas |
|-------|-------|-------|
| CLIENTES_COMERCIAL | 1,000 | Mix de segmentos y tamaños |
| PRODUCTOS | 150 | Catálogo variado |
| VENTAS | 40,000 | 6 meses de transacciones |
| PIPELINE | 500 | Oportunidades abiertas + cerradas |

---

## Patrones Intencionales (Anomalías)

1. **Churn silencioso**: 30 clientes segmento A/VIP sin compra en > 90 días (FECHA_ULTIMA_COMPRA antigua) pero sin marca de INACTIVO
2. **Descuento abusivo**: 1 vendedor con promedio DESCUENTO_PCT > 35% y win-rate igual al promedio (descuenta sin necesidad)
3. **Pipeline inflado**: 20 oportunidades en NEGOCIACION con DIAS_EN_ETAPA > 90 y sin actividad
4. **Canibalización de producto**: 2 PRODUCTOS de la misma CATEGORIA lanzados en fechas cercanas donde uno crece y el otro cae
5. **Vendedor estrella / riesgo**: 1 vendedor concentra > 40% del revenue total (riesgo de persona clave)
6. **Market basket oculto**: Clientes que compran PRODUCTO A + B siempre juntos pero no se ofrece bundle

---

## Validation Queries

```sql
-- Verificar volúmenes
SELECT 'CLIENTES_COMERCIAL' AS tabla, COUNT(*) AS filas FROM COCOTHON_DB.{{EQUIPO}}.CLIENTES_COMERCIAL
UNION ALL
SELECT 'PRODUCTOS', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.PRODUCTOS
UNION ALL
SELECT 'VENTAS', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.VENTAS
UNION ALL
SELECT 'PIPELINE', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.PIPELINE;

-- Verificar churn silencioso
SELECT COUNT(*) AS clientes_en_riesgo
FROM COCOTHON_DB.{{EQUIPO}}.CLIENTES_COMERCIAL
WHERE SEGMENTO IN ('A', 'VIP')
  AND FECHA_ULTIMA_COMPRA < DATEADD('day', -90, CURRENT_DATE())
  AND SEGMENTO != 'INACTIVO';

-- Verificar descuento abusivo
SELECT VENDEDOR_ID, AVG(DESCUENTO_PCT) AS descuento_promedio, COUNT(*) AS n_ventas
FROM COCOTHON_DB.{{EQUIPO}}.VENTAS
WHERE ESTATUS = 'COMPLETADA'
GROUP BY VENDEDOR_ID
HAVING AVG(DESCUENTO_PCT) > 35;

-- Verificar pipeline estancado
SELECT COUNT(*) AS opps_estancadas, SUM(MONTO_ESTIMADO) AS monto_total_estancado
FROM COCOTHON_DB.{{EQUIPO}}.PIPELINE
WHERE ETAPA = 'NEGOCIACION' AND DIAS_EN_ETAPA > 90
  AND FECHA_CIERRE_REAL IS NULL;
```

---

## Ejemplos de Output

### Output 1: Streamlit Dashboard — "Revenue Intelligence"

```
Pantalla principal:
┌─────────────────────────────────────────────────┐
│  💰 Revenue Intelligence - {{EMPRESA}}          │
├─────────────────────────────────────────────────┤
│  KPIs:                                          │
│  [Revenue MTD: $4.2M] [Pipeline: $12M]         │
│  [Win Rate: 34%] [Churn Risk: 30 cuentas]     │
├─────────────────────────────────────────────────┤
│  Funnel: pipeline por etapa (monto + count)    │
│  Cohort: retención de clientes por mes de alta │
│  Scatter: descuento vs win-rate por vendedor   │
├─────────────────────────────────────────────────┤
│  Tabla: top 10 cuentas en riesgo de churn      │
│  + Recomendaciones AI por cuenta               │
└─────────────────────────────────────────────────┘
```

**Tecnologías Snowflake**: Streamlit, Cortex AI (churn scoring con classification), Dynamic Tables para métricas de pipeline.

### Output 2: Cortex Agent — "Sales Strategy Advisor"

```
Prompt: "¿Qué clientes VIP están en riesgo y qué debería hacer el vendedor?"

Agent response:
"Identifiqué 12 clientes VIP con riesgo alto de churn:

Top 3 por impacto (LTV):
1. ACME Corp (LTV $850K) - Sin compra en 105 días, NPS bajó de 72 a 31
   → Vendedor: Juan Pérez. Acción: llamada ejecutiva + oferta de renewal
2. TechnoMex (LTV $620K) - Pipeline perdido por PRECIO hace 2 meses
   → Acción: revisar pricing, ofrecer esquema volumétrico
3. GlobalFoods (LTV $540K) - Devolvió 3 pedidos consecutivos (calidad)
   → Acción: escalar a CS + reunión presencial con gerente de cuenta

Patrón detectado: 8 de 12 son del canal FIELD_SALES en región NORTE.
Posible problema sistémico con la atención de esa región."
```

**Tecnologías Snowflake**: Cortex Agent con Semantic View, AI_COMPLETE para recomendaciones personalizadas, Cortex Search sobre notas de CRM.
