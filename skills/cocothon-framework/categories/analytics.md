# Categoría: Analytics

Problem statements típicos: self-service analytics, data democratization, KPI monitoring, executive dashboards, data catalog/discovery, text-to-SQL.

---

## Modelo de Datos

### Tablas

```sql
CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.EVENTOS_WEB (
    EVENTO_ID       VARCHAR(30) PRIMARY KEY,
    SESSION_ID      VARCHAR(30) NOT NULL,
    USUARIO_ID      VARCHAR(15),
    TIMESTAMP_EVT   TIMESTAMP_NTZ NOT NULL,
    TIPO_EVENTO     VARCHAR(20) NOT NULL,  -- PAGE_VIEW, CLICK, SCROLL, FORM_SUBMIT, SEARCH, ADD_CART, PURCHASE
    PAGINA          VARCHAR(200),
    ELEMENTO        VARCHAR(100),
    VALOR           VARCHAR(200),
    DISPOSITIVO     VARCHAR(15),           -- DESKTOP, MOBILE, TABLET
    NAVEGADOR       VARCHAR(20),
    PAIS            VARCHAR(30),
    CIUDAD          VARCHAR(50),
    CAMPANA_UTM     VARCHAR(50),
    FUENTE_UTM      VARCHAR(30)
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.METRICAS_NEGOCIO (
    METRICA_ID      VARCHAR(20) PRIMARY KEY,
    FECHA           DATE NOT NULL,
    DEPARTAMENTO    VARCHAR(30) NOT NULL,
    METRICA_NOMBRE  VARCHAR(50) NOT NULL,  -- REVENUE, COSTO, MARGEN, TICKETS, NPS, CONVERSIÓN, CAC, LTV
    VALOR_ACTUAL    NUMBER(15,2),
    VALOR_ANTERIOR  NUMBER(15,2),
    META            NUMBER(15,2),
    UNIDAD          VARCHAR(10),           -- USD, %, UNIDADES, DIAS, SCORE
    FRECUENCIA      VARCHAR(10),           -- DIARIO, SEMANAL, MENSUAL
    FUENTE_DATOS    VARCHAR(30)            -- ERP, CRM, WEB, MANUAL, BI_TOOL
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.CONSULTAS_BI (
    QUERY_ID        VARCHAR(30) PRIMARY KEY,
    USUARIO_ID      VARCHAR(15) NOT NULL,
    TIMESTAMP_QUERY TIMESTAMP_NTZ NOT NULL,
    HERRAMIENTA     VARCHAR(20),           -- TABLEAU, POWERBI, LOOKER, SNOWSIGHT, EXCEL, CUSTOM
    DASHBOARD       VARCHAR(100),
    QUERY_TEXT      VARCHAR(5000),
    DURACION_SEG    NUMBER(6,2),
    FILAS_RETORNADAS NUMBER(10),
    WAREHOUSE_SIZE  VARCHAR(10),
    CREDITOS_USADOS NUMBER(8,4),
    EXITO           BOOLEAN DEFAULT TRUE,
    ERROR_MSG       VARCHAR(300)
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.DATASETS_CATALOGO (
    DATASET_ID      VARCHAR(20) PRIMARY KEY,
    NOMBRE          VARCHAR(100) NOT NULL,
    SCHEMA_NAME     VARCHAR(50),
    TABLA_NAME      VARCHAR(50),
    OWNER           VARCHAR(50),
    DESCRIPCION     VARCHAR(500),
    CLASIFICACION   VARCHAR(15),           -- PUBLICO, INTERNO, CONFIDENCIAL, RESTRINGIDO
    FRESCURA_HORAS  NUMBER(4),
    FILAS_APROX     NUMBER(12),
    COLUMNAS        NUMBER(4),
    ULTIMA_ACTUALIZACION TIMESTAMP_NTZ,
    VECES_CONSULTADO_30D NUMBER(6),
    TAGS            VARCHAR(200),
    TIENE_SEMANTIC_VIEW BOOLEAN DEFAULT FALSE
);
```

### Foreign Keys

```
EVENTOS_WEB.USUARIO_ID → (tabla de usuarios externa, nullable para anónimos)
CONSULTAS_BI.USUARIO_ID → (tabla de usuarios de la org)
No hay FKs estrictas entre tablas — este dominio es más de análisis cruzado
```

---

## Volúmenes Recomendados

| Tabla | Filas | Notas |
|-------|-------|-------|
| EVENTOS_WEB | 100,000 | 1 mes de tráfico web |
| METRICAS_NEGOCIO | 3,000 | 6 meses × 5 deptos × ~100 métricas distintas |
| CONSULTAS_BI | 15,000 | 3 meses de query history |
| DATASETS_CATALOGO | 200 | Inventario del data lake |

---

## Patrones Intencionales (Anomalías)

1. **Dashboard zombie**: 5 DATASETS_CATALOGO con VECES_CONSULTADO_30D = 0 pero CLASIFICACION = 'PUBLICO' y FRESCURA_HORAS < 24 (se actualiza pero nadie lo usa)
2. **Query ineficiente repetida**: 1 QUERY_TEXT que aparece 500+ veces en CONSULTAS_BI con DURACION_SEG > 60 y CREDITOS_USADOS > 2 (candidato a materialización)
3. **Métrica inconsistente**: METRICA_NOMBRE = 'REVENUE' con 3 FUENTE_DATOS distintas que reportan valores diferentes para la misma FECHA
4. **Embudo roto**: Tasa de conversión PAGE_VIEW → ADD_CART normal (8%) pero ADD_CART → PURCHASE < 1% en MOBILE (UX issue)
5. **Data freshness gap**: 10 DATASETS con ULTIMA_ACTUALIZACION > 7 días y FRECUENCIA reportada como 'DIARIO'
6. **Self-service bloqueado**: 80% de CONSULTAS_BI vienen de 3 usuarios (concentración; el resto no consulta)

---

## Validation Queries

```sql
-- Verificar volúmenes
SELECT 'EVENTOS_WEB' AS tabla, COUNT(*) AS filas FROM COCOTHON_DB.{{EQUIPO}}.EVENTOS_WEB
UNION ALL
SELECT 'METRICAS_NEGOCIO', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.METRICAS_NEGOCIO
UNION ALL
SELECT 'CONSULTAS_BI', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.CONSULTAS_BI
UNION ALL
SELECT 'DATASETS_CATALOGO', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.DATASETS_CATALOGO;

-- Verificar dashboards zombie
SELECT NOMBRE, FRESCURA_HORAS, VECES_CONSULTADO_30D
FROM COCOTHON_DB.{{EQUIPO}}.DATASETS_CATALOGO
WHERE VECES_CONSULTADO_30D = 0 AND FRESCURA_HORAS < 24 AND CLASIFICACION = 'PUBLICO';

-- Verificar queries repetidas costosas
SELECT LEFT(QUERY_TEXT, 100) AS query_preview, COUNT(*) AS repeticiones,
       AVG(DURACION_SEG) AS duracion_promedio, SUM(CREDITOS_USADOS) AS creditos_total
FROM COCOTHON_DB.{{EQUIPO}}.CONSULTAS_BI
GROUP BY LEFT(QUERY_TEXT, 100)
HAVING COUNT(*) > 100 AND AVG(DURACION_SEG) > 30
ORDER BY creditos_total DESC;

-- Verificar métrica inconsistente
SELECT FECHA, METRICA_NOMBRE, FUENTE_DATOS, VALOR_ACTUAL
FROM COCOTHON_DB.{{EQUIPO}}.METRICAS_NEGOCIO
WHERE METRICA_NOMBRE = 'REVENUE'
QUALIFY COUNT(DISTINCT FUENTE_DATOS) OVER (PARTITION BY FECHA, METRICA_NOMBRE) > 1
ORDER BY FECHA, FUENTE_DATOS;
```

---

## Ejemplos de Output

### Output 1: Streamlit Dashboard — "Data Platform Health"

```
Pantalla principal:
┌─────────────────────────────────────────────────┐
│  📊 Data Platform Health - {{EMPRESA}}          │
├─────────────────────────────────────────────────┤
│  KPIs:                                          │
│  [Datasets: 200] [Fresh: 87%] [Users: 45]     │
│  [Queries/Día: 1,200] [Créditos/Día: 34]      │
├─────────────────────────────────────────────────┤
│  Tab: Adopción | Costos | Calidad | Catálogo   │
│                                                 │
│  Chart: queries por usuario (distribución)     │
│  Table: top 10 queries más costosas            │
│  Freshness heatmap: dataset × día              │
├─────────────────────────────────────────────────┤
│  Recommendations panel (AI-powered)            │
│  "Materializar query X ahorra $450/mes"        │
└─────────────────────────────────────────────────┘
```

**Tecnologías Snowflake**: Streamlit, ACCOUNT_USAGE views, Cortex AI para recomendaciones de optimización, Dynamic Tables para métricas de adopción.

### Output 2: Cortex Agent — "Data Catalog Navigator"

```
Prompt: "¿Qué tabla tiene información de revenue por departamento? Quiero hacer un reporte trimestral."

Agent response:
"Para revenue por departamento, tienes 2 fuentes:

1. **METRICAS_NEGOCIO** (recomendada)
   - Tiene METRICA_NOMBRE = 'REVENUE' desglosado por DEPARTAMENTO
   - Frecuencia: MENSUAL, 6 meses de historia
   - ⚠️ Nota: hay 3 fuentes distintas (ERP, CRM, MANUAL) — usa FUENTE_DATOS = 'ERP' 
     como single source of truth (las otras tienen discrepancias de hasta 5%)

2. **EVENTOS_WEB** (solo revenue digital)
   - Puedes calcular revenue de TIPO_EVENTO = 'PURCHASE'
   - Solo cubre canal digital, no presencial

Query sugerida para tu reporte:
```sql
SELECT DEPARTAMENTO, DATE_TRUNC('quarter', FECHA) AS trimestre,
       SUM(VALOR_ACTUAL) AS revenue
FROM METRICAS_NEGOCIO
WHERE METRICA_NOMBRE = 'REVENUE' AND FUENTE_DATOS = 'ERP'
GROUP BY 1, 2 ORDER BY 2, 1;
```"
```

**Tecnologías Snowflake**: Cortex Agent con Semantic View sobre catálogo + métricas, Cortex Search sobre descripciones de datasets para discovery.
