# Categoría: Finanzas

Problem statements típicos: conciliación bancaria, detección de fraude, forecasting financiero, auditoría de gastos, análisis de cartera.

---

## Modelo de Datos

### Tablas

```sql
CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.TRANSACCIONES (
    TXN_ID          VARCHAR(20) PRIMARY KEY,
    FECHA           TIMESTAMP_NTZ NOT NULL,
    CUENTA_ORIGEN   VARCHAR(20) NOT NULL,
    CUENTA_DESTINO  VARCHAR(20),
    MONTO           NUMBER(15,2) NOT NULL,
    MONEDA          VARCHAR(3) DEFAULT 'MXN',
    TIPO            VARCHAR(20) NOT NULL,  -- TRANSFERENCIA, PAGO, RETIRO, DEPOSITO, CARGO
    CANAL           VARCHAR(15),           -- APP, SUCURSAL, ATM, WEB, POS
    ESTATUS         VARCHAR(15) DEFAULT 'COMPLETADA',  -- COMPLETADA, PENDIENTE, RECHAZADA, REVERSO
    COMERCIO        VARCHAR(100),
    CATEGORIA_GASTO VARCHAR(30),           -- ALIMENTOS, TRANSPORTE, SERVICIOS, ENTRETENIMIENTO, SALUD
    REFERENCIA_EXT  VARCHAR(50),
    CREATED_AT      TIMESTAMP_NTZ DEFAULT CURRENT_TIMESTAMP()
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.CUENTAS (
    CUENTA_ID       VARCHAR(20) PRIMARY KEY,
    CLIENTE_ID      VARCHAR(15) NOT NULL,
    TIPO_CUENTA     VARCHAR(20) NOT NULL,  -- AHORRO, CHEQUES, CREDITO, INVERSION, NOMINA
    SALDO_ACTUAL    NUMBER(15,2),
    LIMITE_CREDITO  NUMBER(15,2),
    FECHA_APERTURA  DATE NOT NULL,
    SUCURSAL        VARCHAR(50),
    ESTATUS         VARCHAR(10) DEFAULT 'ACTIVA',  -- ACTIVA, BLOQUEADA, CANCELADA
    SEGMENTO        VARCHAR(20)            -- PERSONAL, PYME, EMPRESARIAL, PREMIUM
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.CLIENTES (
    CLIENTE_ID      VARCHAR(15) PRIMARY KEY,
    NOMBRE          VARCHAR(100) NOT NULL,
    RFC             VARCHAR(13),
    TIPO            VARCHAR(15),           -- PERSONA_FISICA, PERSONA_MORAL
    FECHA_ALTA      DATE NOT NULL,
    INGRESO_MENSUAL NUMBER(12,2),
    SCORE_CREDITO   NUMBER(3),             -- 300-850
    EDAD            NUMBER(3),
    ESTADO          VARCHAR(30),
    CIUDAD          VARCHAR(50)
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.CONCILIACION_BANCARIA (
    CONC_ID         VARCHAR(20) PRIMARY KEY,
    FECHA_CORTE     DATE NOT NULL,
    CUENTA_ID       VARCHAR(20) NOT NULL,
    SALDO_CONTABLE  NUMBER(15,2),
    SALDO_BANCO     NUMBER(15,2),
    DIFERENCIA      NUMBER(15,2),
    PARTIDAS_TRANSITO NUMBER(5),
    ESTATUS         VARCHAR(15),           -- CUADRADA, DESCUADRADA, EN_REVISION
    NOTAS           VARCHAR(500)
);
```

### Foreign Keys

```
TRANSACCIONES.CUENTA_ORIGEN → CUENTAS.CUENTA_ID
TRANSACCIONES.CUENTA_DESTINO → CUENTAS.CUENTA_ID (nullable)
CUENTAS.CLIENTE_ID → CLIENTES.CLIENTE_ID
CONCILIACION_BANCARIA.CUENTA_ID → CUENTAS.CUENTA_ID
```

---

## Volúmenes Recomendados

| Tabla | Filas | Notas |
|-------|-------|-------|
| CLIENTES | 500 | Mix de segmentos |
| CUENTAS | 800 | ~1.6 cuentas/cliente promedio |
| TRANSACCIONES | 50,000 | 3 meses de historia |
| CONCILIACION_BANCARIA | 90 | 1 registro/día por cuenta (sample) |

---

## Patrones Intencionales (Anomalías)

Insertar estos patrones para que los equipos los descubran durante el hackathon:

1. **Fraude por velocidad**: 5 transacciones del mismo CUENTA_ORIGEN en < 2 minutos, montos > $10,000, destinos distintos
2. **Structuring (smurfing)**: 15 depósitos de exactamente $9,999 (justo bajo el umbral de reporte) en 3 días
3. **Conciliación descuadrada**: 3 cuentas con DIFERENCIA > $50,000 y ESTATUS = 'CUADRADA' (error de clasificación)
4. **Cliente fantasma**: 2 CLIENTES con FECHA_ALTA en futuro y transacciones ya realizadas
5. **Reversos sospechosos**: Cadena de TXN → REVERSO → TXN por monto mayor (lavado)
6. **Gasto anómalo**: 1 cliente PERSONAL con gastos en ENTRETENIMIENTO > $500,000/mes

---

## Validation Queries

```sql
-- Verificar volúmenes
SELECT 'CLIENTES' AS tabla, COUNT(*) AS filas FROM COCOTHON_DB.{{EQUIPO}}.CLIENTES
UNION ALL
SELECT 'CUENTAS', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.CUENTAS
UNION ALL
SELECT 'TRANSACCIONES', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.TRANSACCIONES
UNION ALL
SELECT 'CONCILIACION_BANCARIA', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.CONCILIACION_BANCARIA;

-- Verificar anomalía de fraude por velocidad
SELECT CUENTA_ORIGEN, COUNT(*) AS txns, 
       DATEDIFF('second', MIN(FECHA), MAX(FECHA)) AS span_seconds
FROM COCOTHON_DB.{{EQUIPO}}.TRANSACCIONES
WHERE MONTO > 10000
GROUP BY CUENTA_ORIGEN
HAVING COUNT(*) >= 5 AND span_seconds < 120;

-- Verificar structuring
SELECT CUENTA_ORIGEN, COUNT(*) AS depositos_9999
FROM COCOTHON_DB.{{EQUIPO}}.TRANSACCIONES
WHERE TIPO = 'DEPOSITO' AND MONTO = 9999
GROUP BY CUENTA_ORIGEN
HAVING COUNT(*) >= 10;

-- Verificar conciliación inconsistente
SELECT * FROM COCOTHON_DB.{{EQUIPO}}.CONCILIACION_BANCARIA
WHERE ABS(DIFERENCIA) > 50000 AND ESTATUS = 'CUADRADA';
```

---

## Ejemplos de Output

### Output 1: Streamlit Dashboard — "Monitor de Fraude en Tiempo Real"

```
Pantalla principal:
┌─────────────────────────────────────────────────┐
│  🔍 Monitor de Fraude - {{EMPRESA}}             │
├─────────────────────────────────────────────────┤
│  KPIs:                                          │
│  [Alertas Hoy: 12] [Monto en Riesgo: $2.3M]   │
│  [Tasa Falsos Positivos: 8%] [Tiempo Resp: 4s] │
├─────────────────────────────────────────────────┤
│  Tabla de alertas con scoring AI               │
│  + Filtros por canal, monto, tipo              │
│  + Detalle de cada alerta con explicación AI   │
├─────────────────────────────────────────────────┤
│  Timeline de transacciones sospechosas          │
│  (scatter plot: hora vs monto, color=riesgo)   │
└─────────────────────────────────────────────────┘
```

**Tecnologías Snowflake**: Cortex AI (AI_COMPLETE para scoring), Streamlit, Dynamic Tables para refresh.

### Output 2: Cortex Agent — "Auditor Financiero"

```
Prompt: "¿Hay cuentas con conciliaciones marcadas como cuadradas pero con diferencias significativas?"

Agent response:
"Encontré 3 cuentas con inconsistencias en la conciliación:
 - Cuenta CTA-0042: diferencia de $78,500 marcada como CUADRADA (corte 2026-06-15)
 - Cuenta CTA-0187: diferencia de $52,100 marcada como CUADRADA (corte 2026-06-18)
 - Cuenta CTA-0301: diferencia de -$61,200 marcada como CUADRADA (corte 2026-06-20)

Recomendación: Reclasificar a EN_REVISION y asignar a auditoría interna.
Query usada: [expandir]"
```

**Tecnologías Snowflake**: Cortex Agent con Semantic View sobre las 4 tablas, tool use para queries ad-hoc.
