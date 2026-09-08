# Categoría: RRHH (Recursos Humanos)

Problem statements típicos: rotación de personal, análisis de clima laboral, optimización de nómina, detección de ausentismo, talent analytics.

---

## Modelo de Datos

### Tablas

```sql
CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.EMPLEADOS (
    EMPLEADO_ID     VARCHAR(15) PRIMARY KEY,
    NOMBRE          VARCHAR(100) NOT NULL,
    PUESTO          VARCHAR(80) NOT NULL,
    DEPARTAMENTO    VARCHAR(50) NOT NULL,
    NIVEL           VARCHAR(20),           -- JUNIOR, MID, SENIOR, LEAD, MANAGER, DIRECTOR
    FECHA_INGRESO   DATE NOT NULL,
    FECHA_BAJA      DATE,
    MOTIVO_BAJA     VARCHAR(30),           -- RENUNCIA, DESPIDO, JUBILACION, REESTRUCTURA, NULL
    SALARIO_MENSUAL NUMBER(12,2),
    TIPO_CONTRATO   VARCHAR(20),           -- INDEFINIDO, TEMPORAL, PRACTICANTE, OUTSOURCING
    UBICACION       VARCHAR(50),
    MANAGER_ID      VARCHAR(15),
    GENERO          VARCHAR(1),            -- M, F, X
    EDAD            NUMBER(3),
    SCORE_DESEMPENO NUMBER(3,1)            -- 1.0 a 5.0
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.ASISTENCIA (
    REGISTRO_ID     VARCHAR(20) PRIMARY KEY,
    EMPLEADO_ID     VARCHAR(15) NOT NULL,
    FECHA           DATE NOT NULL,
    HORA_ENTRADA    TIME,
    HORA_SALIDA     TIME,
    TIPO_DIA        VARCHAR(15),           -- NORMAL, FERIADO, VACACION, INCAPACIDAD, FALTA
    HORAS_EXTRA     NUMBER(4,2) DEFAULT 0,
    TARDANZA_MIN    NUMBER(4) DEFAULT 0,
    UBICACION_CHECK VARCHAR(50)
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.ENCUESTA_CLIMA (
    ENCUESTA_ID     VARCHAR(20) PRIMARY KEY,
    EMPLEADO_ID     VARCHAR(15) NOT NULL,
    FECHA           DATE NOT NULL,
    PERIODO         VARCHAR(10),           -- Q1-2026, Q2-2026, etc.
    SATISFACCION    NUMBER(2),             -- 1-10
    CARGA_TRABAJO   NUMBER(2),             -- 1-10 (10=sobrecargado)
    RELACION_JEFE   NUMBER(2),             -- 1-10
    CRECIMIENTO     NUMBER(2),             -- 1-10
    BALANCE_VIDA    NUMBER(2),             -- 1-10
    COMENTARIO      VARCHAR(500),
    RECOMIENDA      BOOLEAN                -- eNPS proxy
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.NOMINA (
    NOMINA_ID       VARCHAR(20) PRIMARY KEY,
    EMPLEADO_ID     VARCHAR(15) NOT NULL,
    PERIODO         VARCHAR(10) NOT NULL,  -- 2026-01, 2026-02, etc.
    SALARIO_BASE    NUMBER(12,2),
    BONOS           NUMBER(12,2) DEFAULT 0,
    DEDUCCIONES     NUMBER(12,2) DEFAULT 0,
    HORAS_EXTRA_PAGO NUMBER(12,2) DEFAULT 0,
    ISR             NUMBER(12,2),
    IMSS            NUMBER(12,2),
    NETO            NUMBER(12,2),
    FECHA_PAGO      DATE
);
```

### Foreign Keys

```
ASISTENCIA.EMPLEADO_ID → EMPLEADOS.EMPLEADO_ID
ENCUESTA_CLIMA.EMPLEADO_ID → EMPLEADOS.EMPLEADO_ID
NOMINA.EMPLEADO_ID → EMPLEADOS.EMPLEADO_ID
EMPLEADOS.MANAGER_ID → EMPLEADOS.EMPLEADO_ID (self-ref)
```

---

## Volúmenes Recomendados

| Tabla | Filas | Notas |
|-------|-------|-------|
| EMPLEADOS | 300 | Incluir 50 con FECHA_BAJA (rotación) |
| ASISTENCIA | 18,000 | ~3 meses, 300 empleados × 60 días hábiles |
| ENCUESTA_CLIMA | 600 | 2 periodos × 300 empleados |
| NOMINA | 1,800 | 6 meses × 300 empleados |

---

## Patrones Intencionales (Anomalías)

1. **Fuga de talento silenciosa**: 8 empleados SENIOR del mismo DEPARTAMENTO con SCORE_DESEMPENO > 4.0 y FECHA_BAJA en el mismo mes (problema de manager)
2. **Ausentismo crónico**: 5 empleados con > 15 FALTA en 3 meses pero sin MOTIVO_BAJA (retención problemática)
3. **Clima tóxico localizado**: 1 departamento con SATISFACCION promedio < 4 y RELACION_JEFE < 3 en Q2 (caída vs Q1 que era > 7)
4. **Nómina fantasma**: 2 registros de NOMINA para empleados con FECHA_BAJA anterior al PERIODO de pago
5. **Horas extra excesivas**: 10 empleados con promedio > 4 HORAS_EXTRA/día durante 2 meses seguidos (riesgo legal/burnout)
6. **Brecha salarial**: Mismos PUESTO + NIVEL + DEPARTAMENTO con diferencia > 30% en SALARIO_MENSUAL correlacionado con GENERO

---

## Validation Queries

```sql
-- Verificar volúmenes
SELECT 'EMPLEADOS' AS tabla, COUNT(*) AS filas FROM COCOTHON_DB.{{EQUIPO}}.EMPLEADOS
UNION ALL
SELECT 'ASISTENCIA', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.ASISTENCIA
UNION ALL
SELECT 'ENCUESTA_CLIMA', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.ENCUESTA_CLIMA
UNION ALL
SELECT 'NOMINA', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.NOMINA;

-- Verificar fuga de talento (seniors del mismo depto en mismo mes)
SELECT DEPARTAMENTO, DATE_TRUNC('month', FECHA_BAJA) AS mes_baja, COUNT(*) AS bajas
FROM COCOTHON_DB.{{EQUIPO}}.EMPLEADOS
WHERE NIVEL = 'SENIOR' AND SCORE_DESEMPENO > 4.0 AND FECHA_BAJA IS NOT NULL
GROUP BY 1, 2
HAVING COUNT(*) >= 5;

-- Verificar nómina fantasma
SELECT n.NOMINA_ID, n.EMPLEADO_ID, n.PERIODO, e.FECHA_BAJA
FROM COCOTHON_DB.{{EQUIPO}}.NOMINA n
JOIN COCOTHON_DB.{{EQUIPO}}.EMPLEADOS e ON n.EMPLEADO_ID = e.EMPLEADO_ID
WHERE e.FECHA_BAJA < TO_DATE(n.PERIODO || '-01', 'YYYY-MM-DD');

-- Verificar brecha salarial
SELECT PUESTO, NIVEL, DEPARTAMENTO, GENERO,
       AVG(SALARIO_MENSUAL) AS salario_promedio, COUNT(*) AS n
FROM COCOTHON_DB.{{EQUIPO}}.EMPLEADOS
WHERE FECHA_BAJA IS NULL
GROUP BY 1, 2, 3, 4
HAVING COUNT(*) >= 2;
```

---

## Ejemplos de Output

### Output 1: Streamlit Dashboard — "People Analytics 360"

```
Pantalla principal:
┌─────────────────────────────────────────────────┐
│  👥 People Analytics - {{EMPRESA}}              │
├─────────────────────────────────────────────────┤
│  KPIs:                                          │
│  [Rotación: 18%] [eNPS: 32] [Ausentismo: 4.2%]│
│  [Costo HE: $890K] [Headcount: 250 activos]   │
├─────────────────────────────────────────────────┤
│  Tab: Rotación | Clima | Nómina | Asistencia   │
│                                                 │
│  Heatmap: rotación por depto × mes             │
│  Scatter: satisfacción vs score desempeño      │
│  Alert cards: anomalías detectadas por AI      │
├─────────────────────────────────────────────────┤
│  Drill-down por departamento                    │
└─────────────────────────────────────────────────┘
```

**Tecnologías Snowflake**: Streamlit, Cortex AI (sentiment analysis de comentarios en encuestas), Dynamic Tables para métricas rolling.

### Output 2: Cortex Agent — "HR Business Partner Virtual"

```
Prompt: "¿Qué departamento tiene mayor riesgo de fuga de talento este trimestre?"

Agent response:
"El departamento de Ingeniería tiene el mayor riesgo de fuga:
 - 8 empleados Senior se fueron en junio (todos con score > 4.0)
 - El clima laboral cayó de 7.8 a 3.2 en relación con jefe (Q1→Q2)
 - 12 empleados activos tienen > 4 horas extra diarias promedio
 
Factores correlacionados:
 - Manager (MGR-0045) tiene 0% retención de seniors en 6 meses
 - Salarios 22% debajo de mercado para el nivel

Recomendación: Intervención inmediata con el manager + revisión salarial
para los 12 empleados en riesgo. Costo de reemplazo estimado: $2.4M."
```

**Tecnologías Snowflake**: Cortex Agent con Semantic View, AI_COMPLETE para análisis de sentimiento en comentarios de encuesta.
