# Categoría: Servicio al Cliente

Problem statements típicos: análisis de sentimiento, reducción de tiempo de resolución, chatbot inteligente, routing de tickets, predicción de escalaciones.

---

## Modelo de Datos

### Tablas

```sql
CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.TICKETS (
    TICKET_ID       VARCHAR(20) PRIMARY KEY,
    CLIENTE_ID      VARCHAR(15) NOT NULL,
    FECHA_CREACION  TIMESTAMP_NTZ NOT NULL,
    FECHA_RESOLUCION TIMESTAMP_NTZ,
    CANAL_ENTRADA   VARCHAR(15) NOT NULL,  -- CHAT, EMAIL, TELEFONO, REDES_SOCIALES, APP, PRESENCIAL
    CATEGORIA       VARCHAR(30),           -- FACTURACION, SOPORTE_TECNICO, QUEJAS, INFO, CAMBIOS, CANCELACION
    SUBCATEGORIA    VARCHAR(50),
    PRIORIDAD       VARCHAR(10),           -- BAJA, MEDIA, ALTA, CRITICA
    ESTATUS         VARCHAR(15),           -- ABIERTO, EN_PROGRESO, PENDIENTE_CLIENTE, RESUELTO, CERRADO, ESCALADO
    AGENTE_ID       VARCHAR(15),
    EQUIPO          VARCHAR(30),           -- NIVEL_1, NIVEL_2, NIVEL_3, ESPECIALISTA, RETENCION
    SLA_HORAS       NUMBER(4),
    CUMPLE_SLA      BOOLEAN,
    SATISFACCION    NUMBER(2),             -- 1-5 (CSAT post-resolución)
    PRIMER_CONTACTO BOOLEAN DEFAULT FALSE, -- FCR: resuelto en primer contacto
    VECES_REABIERTO NUMBER(2) DEFAULT 0
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.INTERACCIONES (
    INTERACCION_ID  VARCHAR(20) PRIMARY KEY,
    TICKET_ID       VARCHAR(20) NOT NULL,
    TIMESTAMP_INT   TIMESTAMP_NTZ NOT NULL,
    TIPO            VARCHAR(15),           -- MENSAJE_CLIENTE, RESPUESTA_AGENTE, NOTA_INTERNA, TRANSFER, ESCALACION
    CANAL           VARCHAR(15),
    CONTENIDO       VARCHAR(2000),
    SENTIMIENTO     VARCHAR(10),           -- POSITIVO, NEUTRAL, NEGATIVO, FRUSTRADO, ENOJADO
    DURACION_SEG    NUMBER(6),             -- Para llamadas
    AGENTE_ID       VARCHAR(15),
    ES_BOT          BOOLEAN DEFAULT FALSE
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.AGENTES (
    AGENTE_ID       VARCHAR(15) PRIMARY KEY,
    NOMBRE          VARCHAR(80) NOT NULL,
    EQUIPO          VARCHAR(30),
    TURNO           VARCHAR(10),           -- MATUTINO, VESPERTINO, NOCTURNO
    FECHA_INGRESO   DATE,
    ESPECIALIDADES  VARCHAR(200),          -- Categorías que domina
    TICKETS_MAX_SIMULTANEOS NUMBER(3),
    RATING_PROMEDIO NUMBER(3,2),
    ACTIVO          BOOLEAN DEFAULT TRUE
);

CREATE OR REPLACE TABLE COCOTHON_DB.{{EQUIPO}}.FAQ_KNOWLEDGE_BASE (
    FAQ_ID          VARCHAR(20) PRIMARY KEY,
    CATEGORIA       VARCHAR(30),
    PREGUNTA        VARCHAR(300) NOT NULL,
    RESPUESTA       VARCHAR(2000) NOT NULL,
    VECES_USADA     NUMBER(6) DEFAULT 0,
    UTIL            NUMBER(5) DEFAULT 0,   -- Veces marcada como útil
    NO_UTIL         NUMBER(5) DEFAULT 0,   -- Veces marcada como no útil
    ULTIMA_ACTUALIZACION DATE,
    ACTIVA          BOOLEAN DEFAULT TRUE,
    TAGS            VARCHAR(200)
);
```

### Foreign Keys

```
TICKETS.AGENTE_ID → AGENTES.AGENTE_ID
INTERACCIONES.TICKET_ID → TICKETS.TICKET_ID
INTERACCIONES.AGENTE_ID → AGENTES.AGENTE_ID
TICKETS.CATEGORIA → FAQ_KNOWLEDGE_BASE.CATEGORIA (relación lógica, no FK estricta)
```

---

## Volúmenes Recomendados

| Tabla | Filas | Notas |
|-------|-------|-------|
| AGENTES | 50 | Mix de equipos y turnos |
| TICKETS | 15,000 | 3 meses de tickets |
| INTERACCIONES | 75,000 | ~5 interacciones/ticket promedio |
| FAQ_KNOWLEDGE_BASE | 300 | Base de conocimiento existente |

---

## Patrones Intencionales (Anomalías)

1. **Escalación evitable**: 200 tickets ESCALADO donde la respuesta estaba en FAQ_KNOWLEDGE_BASE con VECES_USADA > 50 (agentes no consultan la KB)
2. **Agente sobrecargado**: 2 AGENTES con 3x el promedio de tickets simultáneos y SATISFACCION promedio < 2.5 (burnout + mala atención)
3. **SLA gaming**: 100 tickets marcados como RESUELTO justo antes del SLA pero con VECES_REABIERTO > 0 (cierran prematuramente para cumplir métrica)
4. **Sentimiento en escalada**: Secuencia de INTERACCIONES donde SENTIMIENTO va NEUTRAL → NEGATIVO → FRUSTRADO → ENOJADO en un mismo ticket (oportunidad de intervención temprana)
5. **Bot inefectivo**: Canal CHAT con ES_BOT = TRUE donde 70% de las interacciones terminan en TRANSFER a agente humano (bot no resuelve)
6. **FAQ obsoleta**: 20 FAQs con NO_UTIL > UTIL × 3 y ULTIMA_ACTUALIZACION > 6 meses (información desactualizada)

---

## Validation Queries

```sql
-- Verificar volúmenes
SELECT 'AGENTES' AS tabla, COUNT(*) AS filas FROM COCOTHON_DB.{{EQUIPO}}.AGENTES
UNION ALL
SELECT 'TICKETS', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.TICKETS
UNION ALL
SELECT 'INTERACCIONES', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.INTERACCIONES
UNION ALL
SELECT 'FAQ_KNOWLEDGE_BASE', COUNT(*) FROM COCOTHON_DB.{{EQUIPO}}.FAQ_KNOWLEDGE_BASE;

-- Verificar escalación evitable
SELECT COUNT(*) AS escalaciones_evitables
FROM COCOTHON_DB.{{EQUIPO}}.TICKETS t
WHERE t.ESTATUS = 'ESCALADO'
  AND t.CATEGORIA IN (SELECT CATEGORIA FROM COCOTHON_DB.{{EQUIPO}}.FAQ_KNOWLEDGE_BASE WHERE VECES_USADA > 50 AND ACTIVA = TRUE);

-- Verificar SLA gaming
SELECT COUNT(*) AS sla_gaming_tickets
FROM COCOTHON_DB.{{EQUIPO}}.TICKETS
WHERE CUMPLE_SLA = TRUE AND VECES_REABIERTO > 0;

-- Verificar sentimiento en escalada (tickets con degradación)
SELECT TICKET_ID, 
       ARRAY_AGG(SENTIMIENTO) WITHIN GROUP (ORDER BY TIMESTAMP_INT) AS secuencia_sentimiento
FROM COCOTHON_DB.{{EQUIPO}}.INTERACCIONES
WHERE TIPO = 'MENSAJE_CLIENTE' AND SENTIMIENTO IS NOT NULL
GROUP BY TICKET_ID
HAVING ARRAY_SIZE(ARRAY_AGG(DISTINCT SENTIMIENTO)) >= 3;
```

---

## Ejemplos de Output

### Output 1: Streamlit Dashboard — "Customer Experience Command Center"

```
Pantalla principal:
┌─────────────────────────────────────────────────┐
│  🎧 CX Command Center - {{EMPRESA}}            │
├─────────────────────────────────────────────────┤
│  KPIs:                                          │
│  [CSAT: 4.1] [FCR: 62%] [Avg Resolution: 4.2h]│
│  [SLA Compliance: 89%] [NPS: +28]             │
├─────────────────────────────────────────────────┤
│  Real-time: tickets abiertos por prioridad     │
│  Sentiment timeline: tono del cliente en vivo  │
│  Agent workload: heatmap agente × hora         │
├─────────────────────────────────────────────────┤
│  Alert: tickets con sentimiento ENOJADO + >2h  │
│  Trend: CSAT por semana + predicción           │
└─────────────────────────────────────────────────┘
```

**Tecnologías Snowflake**: Streamlit, Cortex AI (AI_SENTIMENT + AI_COMPLETE para clasificación), Cortex Search sobre knowledge base, Dynamic Tables para métricas en tiempo real.

### Output 2: Cortex Agent — "Support Supervisor Assistant"

```
Prompt: "¿Qué tickets abiertos tienen mayor riesgo de escalación?"

Agent response:
"Identifiqué 8 tickets con alto riesgo de escalación basado en patrones históricos:

Top 3 por urgencia:
1. TKT-14523 (FACTURACION, ALTA) - 3 interacciones, sentimiento degradando
   (NEUTRAL→NEGATIVO→FRUSTRADO). Lleva 3.5h, SLA en 4h.
   → Acción: Transferir a RETENCION antes de que sea ENOJADO

2. TKT-14501 (SOPORTE_TECNICO, MEDIA) - Reabierto 2 veces, misma queja
   → Acción: Asignar a NIVEL_2 especialista, no a NIVEL_1 de nuevo

3. TKT-14489 (CANCELACION, ALTA) - Cliente VIP (LTV $45K), primera queja
   → Acción: Escalar proactivamente a gerente + ofrecer compensación

Patrón: 5 de 8 tickets son del canal EMAIL sin respuesta en >2h.
Sugerencia: Asignar agente dedicado al backlog de email este turno."
```

**Tecnologías Snowflake**: Cortex Agent con Semantic View, AI_SENTIMENT para scoring en tiempo real, SNOWFLAKE.ML.CLASSIFICATION para predicción de escalación.
