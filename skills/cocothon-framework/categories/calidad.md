# Categoría: Calidad

Problem statements típicos: control estadístico de procesos, trazabilidad de lotes, gestión de no-conformidades, análisis de causa raíz, auditorías de calidad.

---

## Modelo de Datos

### Tablas

```sql
CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.PRODUCCION (
    LOTE_ID         VARCHAR(20) PRIMARY KEY,
    PRODUCTO        VARCHAR(80) NOT NULL,
    LINEA_PRODUCCION VARCHAR(20) NOT NULL,
    FECHA_INICIO    TIMESTAMP_NTZ NOT NULL,
    FECHA_FIN       TIMESTAMP_NTZ,
    CANTIDAD_PRODUCIDA NUMBER(8),
    CANTIDAD_DEFECTUOSA NUMBER(8) DEFAULT 0,
    OPERADOR_ID     VARCHAR(15),
    TURNO           VARCHAR(10),           -- MATUTINO, VESPERTINO, NOCTURNO
    TEMPERATURA_PROCESO NUMBER(5,2),
    PRESION_PROCESO NUMBER(5,2),
    VELOCIDAD_LINEA NUMBER(6,2),
    ESTATUS         VARCHAR(15)            -- EN_PROCESO, COMPLETADO, DETENIDO, RECHAZADO
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.INSPECCIONES (
    INSPECCION_ID   VARCHAR(20) PRIMARY KEY,
    LOTE_ID         VARCHAR(20) NOT NULL,
    FECHA           TIMESTAMP_NTZ NOT NULL,
    TIPO            VARCHAR(20),           -- ENTRADA, EN_PROCESO, FINAL, AUDITORIA
    INSPECTOR_ID    VARCHAR(15),
    RESULTADO       VARCHAR(15) NOT NULL,  -- APROBADO, RECHAZADO, CONDICIONAL, RETENIDO
    DEFECTOS_CRITICOS NUMBER(4) DEFAULT 0,
    DEFECTOS_MAYORES NUMBER(4) DEFAULT 0,
    DEFECTOS_MENORES NUMBER(4) DEFAULT 0,
    MUESTRA_SIZE    NUMBER(5),
    CRITERIO_AQL    NUMBER(4,2),           -- Acceptable Quality Level
    NOTAS           VARCHAR(500)
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.NO_CONFORMIDADES (
    NC_ID           VARCHAR(20) PRIMARY KEY,
    LOTE_ID         VARCHAR(20),
    FECHA_DETECCION DATE NOT NULL,
    FECHA_CIERRE    DATE,
    TIPO            VARCHAR(20),           -- PRODUCTO, PROCESO, PROVEEDOR, CLIENTE
    SEVERIDAD       VARCHAR(10),           -- CRITICA, MAYOR, MENOR
    DESCRIPCION     VARCHAR(500) NOT NULL,
    CAUSA_RAIZ      VARCHAR(200),
    ACCION_CORRECTIVA VARCHAR(300),
    RESPONSABLE_ID  VARCHAR(15),
    ESTATUS         VARCHAR(15),           -- ABIERTA, EN_ANALISIS, ACCION_DEFINIDA, CERRADA, REINCIDENTE
    DIAS_ABIERTA    NUMBER(4),
    COSTO_IMPACTO   NUMBER(10,2)
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.MEDICIONES_PROCESO (
    MEDICION_ID     VARCHAR(20) PRIMARY KEY,
    LOTE_ID         VARCHAR(20) NOT NULL,
    PARAMETRO       VARCHAR(30) NOT NULL,  -- TEMPERATURA, HUMEDAD, PH, VISCOSIDAD, PESO, DIMENSION
    VALOR_MEDIDO    NUMBER(10,4) NOT NULL,
    LIMITE_INFERIOR NUMBER(10,4),
    LIMITE_SUPERIOR NUMBER(10,4),
    UNIDAD          VARCHAR(10),
    TIMESTAMP_MED   TIMESTAMP_NTZ NOT NULL,
    FUERA_DE_SPEC   BOOLEAN DEFAULT FALSE,
    EQUIPO_MEDICION VARCHAR(30)
);
```

### Foreign Keys

```
INSPECCIONES.LOTE_ID → PRODUCCION.LOTE_ID
NO_CONFORMIDADES.LOTE_ID → PRODUCCION.LOTE_ID (nullable - NCs de proceso no siempre tienen lote)
MEDICIONES_PROCESO.LOTE_ID → PRODUCCION.LOTE_ID
```

---

## Volúmenes Recomendados

| Tabla | Filas | Notas |
|-------|-------|-------|
| PRODUCCION | 2,000 | 3 meses de lotes |
| INSPECCIONES | 6,000 | ~3 inspecciones/lote promedio |
| NO_CONFORMIDADES | 200 | Mix de severidades y estatus |
| MEDICIONES_PROCESO | 60,000 | ~30 mediciones/lote (muestreo continuo) |

---

## Patrones Intencionales (Anomalías)

1. **Drift de proceso**: LINEA_PRODUCCION 'LP-03' con TEMPERATURA_PROCESO que sube 0.5°C por semana durante 6 semanas (calibración perdida gradualmente)
2. **Turno problemático**: TURNO 'NOCTURNO' con 3x más DEFECTOS_CRITICOS que MATUTINO/VESPERTINO (posible fatiga o capacitación)
3. **NC reincidente**: 3 NO_CONFORMIDADES con ESTATUS = 'REINCIDENTE' y misma CAUSA_RAIZ (acción correctiva inefectiva)
4. **Inspector inconsistente**: 1 INSPECTOR_ID que aprueba lotes donde otros inspectores rechazan (criterio laxo)
5. **Fuera de spec ignorado**: 50 MEDICIONES_PROCESO con FUERA_DE_SPEC = TRUE pero INSPECCIONES.RESULTADO = 'APROBADO' para el mismo lote
6. **Proveedor de insumos**: Correlación entre TIPO = 'ENTRADA' rechazadas y un LOTE_ID específico de materia prima (trazabilidad inversa)

---

## Validation Queries

```sql
-- Verificar volúmenes
SELECT 'PRODUCCION' AS tabla, COUNT(*) AS filas FROM COCOTHON_DB.{{EQUIPO}}.PRODUCCION
UNION ALL
SELECT 'INSPECCIONES', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.INSPECCIONES
UNION ALL
SELECT 'NO_CONFORMIDADES', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.NO_CONFORMIDADES
UNION ALL
SELECT 'MEDICIONES_PROCESO', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.MEDICIONES_PROCESO;

-- Verificar drift de temperatura
SELECT LINEA_PRODUCCION, DATE_TRUNC('week', FECHA_INICIO) AS semana,
       AVG(TEMPERATURA_PROCESO) AS temp_promedio
FROM COCOTHON_DB.{{EQUIPO}}.PRODUCCION
WHERE LINEA_PRODUCCION = 'LP-03'
GROUP BY 1, 2
ORDER BY 2;

-- Verificar turno problemático
SELECT TURNO, SUM(CANTIDAD_DEFECTUOSA) AS total_defectos,
       SUM(CANTIDAD_PRODUCIDA) AS total_producido,
       SUM(CANTIDAD_DEFECTUOSA) * 100.0 / NULLIF(SUM(CANTIDAD_PRODUCIDA), 0) AS pct_defectos
FROM COCOTHON_DB.{{EQUIPO}}.PRODUCCION
GROUP BY 1;

-- Verificar fuera de spec aprobados
SELECT p.LOTE_ID, COUNT(*) AS mediciones_fuera_spec
FROM COCOTHON_DB.{{EQUIPO}}.MEDICIONES_PROCESO p
JOIN COCOTHON_DB.{{EQUIPO}}.INSPECCIONES i ON p.LOTE_ID = i.LOTE_ID
WHERE p.FUERA_DE_SPEC = TRUE AND i.RESULTADO = 'APROBADO' AND i.TIPO = 'FINAL'
GROUP BY 1
HAVING COUNT(*) >= 3;
```

---

## Ejemplos de Output

### Output 1: Streamlit Dashboard — "Quality Control Center"

```
Pantalla principal:
┌─────────────────────────────────────────────────┐
│  ✅ Quality Control - {{EMPRESA}}               │
├─────────────────────────────────────────────────┤
│  KPIs:                                          │
│  [Yield: 97.2%] [NCs Abiertas: 14] [PPM: 280] │
│  [Costo No-Calidad: $1.2M] [OEE: 84%]        │
├─────────────────────────────────────────────────┤
│  SPC Chart: parámetro seleccionado con LCL/UCL │
│  Pareto: top 5 causas de NC                    │
│  Heatmap: defectos por línea × turno           │
├─────────────────────────────────────────────────┤
│  Tabla: NCs abiertas > 30 días (color por sev) │
│  Trend: PPM por semana (últimos 3 meses)       │
└─────────────────────────────────────────────────┘
```

**Tecnologías Snowflake**: Streamlit, Cortex AI (anomaly detection en mediciones de proceso), Dynamic Tables para SPC en tiempo real.

### Output 2: Cortex Agent — "Quality Engineer Assistant"

```
Prompt: "¿Por qué la línea 3 tiene tantos rechazos esta semana?"

Agent response:
"La línea LP-03 tiene una tasa de rechazo de 8.4% esta semana vs 2.1% histórico.

Análisis de causa raíz:
1. TEMPERATURA_PROCESO ha subido gradualmente: de 72.1°C (hace 6 semanas) a 75.3°C (hoy)
   → Probable pérdida de calibración del controlador térmico
2. Los defectos son todos tipo 'DEFORMACIÓN' — consistente con exceso de temperatura
3. Las mediciones muestran 34 puntos FUERA_DE_SPEC en la última semana

Correlación encontrada: 100% de los rechazos ocurren cuando TEMPERATURA > 74.5°C
(límite superior de spec: 74.0°C)

Acción recomendada:
1. Detener LP-03 para recalibración del controlador térmico
2. Revisar lotes producidos con T > 74.0°C esta semana (12 lotes, ~4,800 unidades)
3. Crear NC preventiva y asignar mantenimiento"
```

**Tecnologías Snowflake**: Cortex Agent con Semantic View, SNOWFLAKE.ML.ANOMALY_DETECTION para detectar drift, tool use para correlaciones ad-hoc.
