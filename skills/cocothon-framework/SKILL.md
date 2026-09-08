---
name: cocothon-framework
title: Cocothon Framework
summary: Plan, run, and document Cocothons — structured hackathons where teams solve business problems with Cortex Code.
description: "Use when planning, executing, or documenting Cocothons — structured hackathons with Cortex Code for solving business problems. Interactive guide for AEs. Triggers: cocothon, hackathon snowflake, organizar hackathon, planificar cocothon, cocotón, crear cocothon, evaluar hackathon, propuesta hackathon."
tools: [Read, Write, Edit, Bash, Glob, Grep, web_search, web_fetch, AskUserQuestion, sql_execute]
prompt: "$cocothon-framework"
version: "3.0.0"
language: es
status: Published
author: JP Castaneda
type: snowflake
---

# Cocothon Framework

## About / Acerca de / Sobre

**English:** Skill for planning, running, and documenting Cocothons — structured hackathons where teams solve real business problems using Cortex Code and the Snowflake platform. Guides AEs through the full event lifecycle: proposal generation, account selection, data preparation, event execution, evaluation, and results reporting. Includes pre-built archetypes for quick setup, QA checklists, branded HTML output, and post-event analytics.

**Español:** Skill para planificar, ejecutar y documentar Cocothons — hackathons estructurados donde equipos resuelven problemas de negocio reales usando Cortex Code y la plataforma Snowflake. Guía a los AEs a través del ciclo completo del evento: generación de propuesta, selección de cuentas, preparación de datos, ejecución del evento, evaluación y reporte de resultados. Incluye arquetipos pre-configurados para setup rápido, checklists de QA, output HTML con marca Snowflake y analytics post-evento.

**Português (BR):** Skill para planejar, executar e documentar Cocothons — hackathons estruturados onde equipes resolvem problemas reais de negócios usando Cortex Code e a plataforma Snowflake. Guia os AEs por todo o ciclo do evento: geração de proposta, seleção de contas, preparação de dados, execução do evento, avaliação e relatório de resultados. Inclui arquétipos pré-configurados para setup rápido, checklists de QA, output HTML com marca Snowflake e analytics pós-evento.

---

**Load**: [references/cocothon-framework.md](references/cocothon-framework.md) para el marco de referencia completo.
**Load**: [references/anti-patterns.md](references/anti-patterns.md) para errores comunes y cómo evitarlos.
**Load**: [references/qa-checklist.md](references/qa-checklist.md) para validación pre-entrega.
**Load**: [references/error-fallbacks.md](references/error-fallbacks.md) para recuperación ante fallos.
**Load**: [references/archetypes.md](references/archetypes.md) para arquetipos pre-configurados (setup rápido).
**Load**: [references/parallel-generation.md](references/parallel-generation.md) para generación paralela de outputs.
**Load**: [references/component-library.md](references/component-library.md) para componentes HTML reutilizables.
**Load**: [references/tone-voice.md](references/tone-voice.md) para guía de tono por audiencia.
**Load**: [references/chart-templates.md](references/chart-templates.md) para charts del reporte de resultados.
**Load**: [profiles/cocothon-organizer.md](profiles/cocothon-organizer.md) para el perfil interactivo del organizador.

---

## Pre-Execution: Memory Recall

Antes de iniciar cualquier flujo:

1. Run `cortex memory recall "cocothon hackathon event planning"`
2. Aplicar lecciones encontradas:
   - Tipo `error-fix` o `gotcha` → **HARD RULES** (nunca violar)
   - Tipo `pattern` → **PREFERRED APPROACH** (usar salvo que haya razón contraria)
   - Tipo `preference` → **DEFAULT** (aplicar a menos que el usuario override)

---

## Dependencies

| Skill | Cuándo | Propósito | Trigger |
|-------|--------|-----------|---------|
| `snowflake-brand-guidelines` | Siempre | CSS palette, tipografía, logo para outputs HTML | Al inicio de cualquier generación HTML |
| `html-to-pdf` | Si piden PDF | Conversión Chrome headless de propuesta/materiales | Cuando usuario dice 'pdf' o 'imprimir' |
| `account-research` | Pre-evento (opcional) | Intel del cliente para personalizar PS | Después de Paso 1 si hay lista de asistentes con emails |
| `snowflake-hol-generator` | Si generan datos sintéticos | Patterns de generación + validación SQL | En Paso 2 si datos = sintéticos |

---

## ¿Qué es una Cocothon?

Una Cocothon es un hackathon estructurado (mínimo 3 horas) diseñado para **clientes y prospectos**, donde equipos mixtos (perfiles de negocio + técnicos) resuelven problemas reales de su organización usando Cortex Code y la plataforma Snowflake. El AE es el organizador del evento y el SE actúa como facilitador técnico.

**ISF = Industry Solutions Framework**: Repositorio centralizado de Snowflake para demos orientadas a outcomes, skills de Cortex Code, y assets de GTM por industria. La Cocothon es el mecanismo "Push" que lo alimenta con soluciones validadas en campo.

---

## Modo de Operación

Este skill opera de forma **INTERACTIVA**. El flujo es conversacional — pregunta paso a paso, valida, sugiere, y avanza. NO presentes formularios largos de golpe. Sigue la guía detallada en [profiles/cocothon-organizer.md](profiles/cocothon-organizer.md).

Si el usuario es un **AE** (no técnico): usa lenguaje de negocio, explica conceptos técnicos solo si pregunta, enfócate en valor para su cliente.

Si el usuario es un **SE** (técnico): puedes ser más directo con SQL, configuración, y detalles de implementación.

---

## Workflow (8 Pasos)

### Archetype Selector (Opcional — Setup Rápido)

Antes de iniciar el flujo interactivo, ofrecer al usuario:

> "¿Quieres elegir un arquetipo pre-configurado para ir más rápido, o prefieres configurar paso a paso?"

Si elige arquetipo → **Load** [references/archetypes.md](references/archetypes.md), presentar tabla resumen de los 6 arquetipos, pre-llenar configuración con los valores del arquetipo elegido, confirmar, y saltar directamente al Paso 2.

Si prefiere paso a paso → proceder con Paso 1 interactivo normalmente.

### Paso 1: Configuración Interactiva del Evento ⚠️ STOP — Recopilar antes de continuar

Guía al usuario (AE organizador) de forma conversacional (una pregunta a la vez):

1. **Empresa/Prospecto** — "¿Para qué cliente o prospecto es esta Cocothon?"
2. **Industria** — Ofrece opciones relevantes con sugerencias
3. **Fecha tentativa** — Cuándo planean realizarla
4. **Modalidad** — Standard (3-4h) | Full Day (6-8h) | Extended (2-3d). Explica brevemente cada una.
5. **Formato** — Presencial | Virtual | Híbrido
6. **# de equipos** — Sugiere según modalidad (Standard: 3-5, Full Day: 4-7, Extended: 5-10)
7. **Participantes del cliente** — Perfiles de negocio + técnicos. ¿Quiénes participarán de cada lado?
8. **Partners** — ¿Hay SIs o ISVs involucrados?
9. **Ownership de Problem Statements** — "¿Quién define los retos a resolver?"
   - A) **Snowflake-Led** — Nosotros proponemos problem statements pre-diseñados basados en la industria (modelo estándar)
   - B) **Customer-Led** — Los participantes del cliente proponen sus propios casos de uso (requiere curación)
   - C) **Mix** — Algunos retos los definimos nosotros + espacio para que propongan los suyos
10. **Jurado** — Quiénes evaluarán (sugerir: sponsor del cliente + SE/Industry Principal)
11. **Premios** — Sugiere opciones

NO avances al Paso 2 hasta tener respuestas a puntos 1-8 como mínimo.

**Si eligió Snowflake-Led (A):** Pasa a sugerir problem statements basados en la industria (Paso 2).
**Si eligió Customer-Led (B) o Mix (C):** Activa el **Paso 1C: Curación de Casos**.

### Express Mode (--express)

Si el usuario provee un JSON o dice "express mode", saltar todas las preguntas interactivas:

```json
{
  "empresa": "Company Name",
  "industria": "Retail",
  "fecha": "2026-08-15",
  "modalidad": "standard",
  "formato": "presencial",
  "idioma": "es",
  "ownership": "customer-led",
  "equipos": [
    {"nombre": "Finanzas", "miembros": ["Persona A", "Persona B", "Persona C"], "caso": "Conciliación automática"},
    {"nombre": "Logística", "miembros": ["Persona D", "Persona E", "Persona F"], "caso": "Optimización de despacho"}
  ],
  "cuentas": "hol",
  "region": "AWS_US_WEST_2",
  "jurado": ["Sponsor Cliente", "SE Lead"],
  "premios": "Snowstore $200 + fast-track POC"
}
```

**Skip logic:**
1. Saltar Fases 1-3 (toda la recopilación interactiva)
2. Validar que JSON tiene campos requeridos: `empresa`, `industria`, `fecha`, `modalidad`, `equipos`
3. Presentar resumen para aprobación antes de generar outputs
4. Saltar directamente a Paso 2 (datos) o Paso 4 (propuesta) según lo que el usuario pida

### Paso 1B: Solicitud de Cuentas HOL (vía The Lift)

Pregunta: "¿Cómo manejaremos las cuentas Snowflake?"
- A) Cuenta existente del cliente
- B) Cuenta interna Snowflake (demo)
- C) **Solicitar cuentas HOL dedicadas vía The Lift** ← Opción recomendada
- D) Mix

**Si el usuario necesita cuentas HOL**, generar la solicitud siguiendo el proceso oficial de The Lift:

**Proceso de solicitud (Innovation Showcase Team):**

1. **Submeter Lift Ticket** en: https://lift.snowflake.com/lift?id=esc_sc_cat_item&table=sc_cat_item&sys_id=86ff324b1b5466d05f6111f3b24bcb86&searchTerm=hol
2. **El equipo de Innovation Showcase** revisa y provisiona las cuentas automáticamente dentro de una Org HOL dedicada
3. **Cada asistente recibe una cuenta individual** con 2 usuarios: attendee user + admin user (ambos con ACCOUNTADMIN)
4. **Se genera una web app "Go Live"** donde los asistentes reclaman sus cuentas el día del evento (con QR code)
5. **Deprovisioning automático** en la fecha de decomisión seleccionada en el ticket

**Datos requeridos para el Lift Ticket:**

- Nombre del evento
- Fecha del evento
- Número de cuentas requeridas (1 por asistente o 1 por equipo)
- Región: AWS_US_WEST_2 (requerida para Cortex AI completo)
- Edición: Enterprise (mínimo para Cortex AI)
- Fecha de decomisión (sugerir: fecha evento + 7 días)
- Setup personalizado: indicar si se necesita SQL scripts, notebooks, o Streamlit pre-desplegados
- Contacto del SE responsable

**Custom Setup (opcional pero recomendado para Cocothon):**
- El equipo de Innovation Showcase crea un repositorio donde el SE sube: SQL scripts, notebooks, Streamlit apps
- Se despliegan automáticamente en TODAS las cuentas de asistentes
- Incluir: datos sintéticos pre-cargados + scripts iniciales por equipo

**Notas importantes:**
- NO usar trial self-service (tienen límite de ~10 AI credits/día, no soportan Cortex Code CLI)
- Las cuentas HOL están en una Org dedicada, sin policies de seguridad de SE
- Contacto para dudas: swathi.jasti@snowflake.com, emily.liu@snowflake.com
- Referencia: https://docs.google.com/presentation/d/1SJryTtDIaNeKHRj4J8VhH2RsxKoqFNmdvswCtvUARH8/edit

**Features que verificar post-provisioning:**
- Cortex AI Functions habilitadas
- CORTEX_ENABLED_CROSS_REGION = 'ANY_REGION'
- Snowflake Intelligence inicializado desde UI
- Modelos LLM disponibles (claude-haiku-4-5, llama, mistral)

Recopila la información necesaria de forma interactiva (no pidas todo de golpe).

### Paso 1C: Curación de Casos (solo si Customer-Led o Mix)

Cuando los participantes proponen sus propios casos de uso, se requiere un proceso de curación para formar equipos coherentes. Sigue la **Fase 2B** en [profiles/cocothon-organizer.md](profiles/cocothon-organizer.md).

**Proceso:**

1. **Recibir lista de casos** — Pide al organizador la lista de participantes + caso propuesto (Excel, CSV, texto, o Google Sheets)
2. **Clasificar por afinidad temática** — Agrupa los casos en categorías funcionales (típicamente 5-9 categorías). Categorías comunes cross-industria:
   - Finanzas / Tesorería / Compras
   - Talento Humano / Nómina / RRHH
   - Logística / Supply Chain / Operaciones
   - Comercial / Ventas / CRM
   - Calidad / Producto / QA
   - Analytics / BI / Reporting
   - Servicio al Cliente / CX
   - AI / Automatización / Agentes
3. **Formar equipos** — Regla: equipos de 3-4 personas. Fórmula: `num_equipos_por_categoria = ceil(personas / 3.5)`
   - Categorías con 3-4 personas → 1 equipo
   - Categorías con 5-7 personas → 2 equipos
   - Categorías con 8-10 personas → 3 equipos
4. **Fusionar duplicados** — Si 2+ personas proponen esencialmente el mismo caso → mismo equipo, co-lideran
5. **Manejar participantes sin caso** — Se asignan como refuerzo a equipos existentes por afinidad de área
6. **Lista de espera** — Si hay cupo limitado, priorizar participantes con caso concreto. Sin caso → espera o refuerzo
7. **Presentar propuesta** — Mostrar estructura de equipos al organizador para validación antes de continuar

⚠️ STOP — No continuar hasta que el organizador apruebe la estructura de equipos.

### Paso 1D: Planeación y Countdown

Una vez aprobada la configuración (equipos, PS, cuentas), generar el **plan de ejecución** con timeline, responsables y dependencias. Seguir la guía detallada en [profiles/cocothon-organizer.md](profiles/cocothon-organizer.md) sección "Countdown de Planeación".

**Verificación de calendario:** Si Glean está disponible (mcp__glean__), verificar conflictos del AE/SE en las fechas clave del countdown (D-14, D-7, D-2, Día D) antes de generar el timeline.

**Timeline de referencia (4 semanas antes del evento):**

| Semana | AE | SE | Entregable |
|--------|----|----|------------|
| **-4 sem** | Confirmar fecha + sponsor con el cliente | Definir región y edición de cuentas | Fecha bloqueada en calendarios |
| **-3 sem** | Enviar formulario de inscripción (si Customer-Led) | Solicitar cuentas HOL + iniciar setup | Cuentas solicitadas |
| **-2 sem** | Cerrar lista de participantes + enviar invitaciones | Curar equipos + generar datos sintéticos + validar Cortex AI | Propuesta HTML aprobada, equipos formados |
| **-1 sem** | Enviar correo pre-evento a participantes | Generar scripts por equipo + preparar demo apertura + cheat sheet | Materiales listos, dry-run completado |
| **-3 días** | Confirmar logística (sala, wifi, proyector) | Dry-run técnico completo (cuentas, permisos, AI, scripts) | Todo validado |
| **-1 día** | Recordatorio final a participantes | Verificación final: cuentas activas, datos frescos, Cortex OK | Green light |
| **Día D** | MC + entrega de scripts + networking | Demo apertura + soporte técnico + mentoring | Evento ejecutado |
| **+1 día** | Enviar correo de resultados | Documentar soluciones ganadoras como skills | Follow-up iniciado |

**Dependencias críticas:**
- Cuentas HOL deben estar listas **mínimo 1 semana antes** (SE necesita tiempo para configurar)
- Participantes deben tener CoCo Desktop instalado **mínimo 3 días antes**
- Dry-run técnico es **obligatorio** — nunca ir al evento sin haber probado todo
- Scripts por equipo se generan **después** de cerrar la lista de equipos

**Output de este paso:** Tabla de planeación compartida entre AE y SE con fechas concretas y status. Presentar al organizador para alineación.

⚠️ STOP — Confirmar que AE y SE están alineados en el timeline antes de avanzar a preparación de datos.

### Paso 2: Preparación de Datos

Según la configuración:

**Si datos = Sintéticos:**
1. Diseña modelo de datos apropiado para la industria y problem statements
2. Genera SQL con `CREATE TABLE` + `INSERT` usando `GENERATOR(ROWCOUNT)`, `SEQ4()`, `UNIFORM()`, `ARRAY_CONSTRUCT()`
3. Incluye anomalías y patrones interesantes que los equipos puedan descubrir
4. Volúmenes realistas: 10K-100K filas en hechos, 100-1000 en dimensiones
5. Ejecuta el SQL en la cuenta target
6. Verifica con `SELECT COUNT(*)` y queries de validación

**Si datos = Reales:**
1. Verifica acceso a las tablas/vistas del cliente
2. Crea vistas o clones para aislar datos del hackathon
3. Documenta el modelo de datos disponible

**Si datos = Mix:**
1. Usa datos reales como base
2. Enriquece con columnas sintéticas o tablas adicionales
3. Genera documentos (PDFs) sintéticos si aplica para Cortex Search

### Paso 3: Setup Técnico

1. **Base de datos y schemas**: Crear DB dedicada o schema por equipo
2. **Warehouses**: XS warehouse compartido (o uno por equipo si hay presupuesto)
3. **Roles y permisos**: Cada equipo con role propio y acceso a sus datos
4. **Validación de AI**: Verificar que Cortex AI funciona en la cuenta/región
5. **Skills preinstalados**: Verificar que los skills ISF relevantes están disponibles en Coco Desktop de los participantes
6. **Trial accounts** (si aplica): Crear con datos precargados

SQL de setup base:
```sql
-- Estructura base para Cocothon
CREATE DATABASE IF NOT EXISTS COCOTHON_{EVENTO};
CREATE WAREHOUSE IF NOT EXISTS COCOTHON_WH 
  WITH WAREHOUSE_SIZE = 'XSMALL' 
  AUTO_SUSPEND = 60 
  AUTO_RESUME = TRUE;

-- Schema por equipo (repetir por cada equipo)
CREATE SCHEMA IF NOT EXISTS COCOTHON_{EVENTO}.TEAM_{N};

-- Role por equipo
CREATE ROLE IF NOT EXISTS COCOTHON_TEAM_{N}_ROLE;
GRANT USAGE ON DATABASE COCOTHON_{EVENTO} TO ROLE COCOTHON_TEAM_{N}_ROLE;
GRANT USAGE ON SCHEMA COCOTHON_{EVENTO}.TEAM_{N} TO ROLE COCOTHON_TEAM_{N}_ROLE;
GRANT ALL ON SCHEMA COCOTHON_{EVENTO}.TEAM_{N} TO ROLE COCOTHON_TEAM_{N}_ROLE;
GRANT USAGE ON WAREHOUSE COCOTHON_WH TO ROLE COCOTHON_TEAM_{N}_ROLE;

-- Datos compartidos (todos los equipos los ven)
CREATE SCHEMA IF NOT EXISTS COCOTHON_{EVENTO}.SHARED_DATA;
GRANT USAGE ON SCHEMA COCOTHON_{EVENTO}.SHARED_DATA TO ROLE COCOTHON_TEAM_{N}_ROLE;
GRANT SELECT ON ALL TABLES IN SCHEMA COCOTHON_{EVENTO}.SHARED_DATA TO ROLE COCOTHON_TEAM_{N}_ROLE;
```

### Paso 4: Propuesta para el Cliente ⚠️ STOP — Presentar para aprobación del AE

**Generación paralela:** Si hay 3+ outputs pendientes (Propuesta + Agenda + Tutorial), usar generación paralela según [references/parallel-generation.md](references/parallel-generation.md) — lanzar 3 Task calls en paralelo con el mismo JSON de contexto.

Genera una **propuesta/invitación HTML profesional** para que el AE presente al cliente. Sigue las instrucciones detalladas en [profiles/cocothon-organizer.md](profiles/cocothon-organizer.md) sección "Generación de Propuesta para el Cliente". Usar template parametrizado de `templates/resultados_template.html` como base de estructura HTML.

La propuesta debe:
- Estar escrita en lenguaje de NEGOCIO (no técnico)
- Explicar qué es una Cocothon y por qué participar
- Mostrar la agenda visual del evento
- Listar los problem statements en lenguaje del cliente
- Especificar qué perfiles necesita el cliente (no requieren ser técnicos)
- Incluir logística (fecha, hora, lugar, duración)
- Usar branding Snowflake (colores #11567F / #29B5E8, Montserrat + Lato)
- Ser un HTML self-contained imprimible
- Mencionar el track record: "7 equipos, 7 soluciones en 2 horas (París 2026)"

Presenta la propuesta al AE para su aprobación antes de enviarla al cliente.

### Paso 5: Generación de Materiales Internos

**Nota:** Si Paso 4 ya fue generado en paralelo junto con Agenda y Tutorial, este paso se enfoca solo en los materiales restantes (briefing, rúbrica, cheat sheet). De lo contrario, usar generación paralela aquí si hay 3+ outputs pendientes.

Genera:

1. **Briefing técnico para SEs** (HTML o MD):
   - Agenda del evento con tiempos exactos
   - Problem statements detallados con contexto de datos
   - Modelo de datos disponible (ERD simplificado)
   - Skills ISF recomendados por problem statement
   - Tips y comandos útiles de Coco (cheat sheet)

2. **Rúbrica para el jurado** (tabla imprimible):
   - Business Impact (30%)
   - Feasibility (25%)
   - Innovation (20%)
   - Completeness (15%)
   - Reusability (10%)

3. **Cheat sheet para participantes** (1 página):
   - Comandos frecuentes de Cortex Code
   - Patterns SQL comunes (GENERATOR, AI_COMPLETE, Semantic Views)
   - Links útiles (docs, marketplace)

Solo proceder cuando el organizador apruebe los materiales.

### Paso 5B: Validación QA ⚠️ OBLIGATORIO antes de entregar

Ejecutar [references/qa-checklist.md](references/qa-checklist.md) sobre TODOS los outputs generados. Consultar [references/anti-patterns.md](references/anti-patterns.md) para validar que no se cometió ningún error frecuente.

**Reglas:**
- Si hay **Critical** → NO entregar. Corregir primero.
- Si hay **Major** → Corregir antes de entregar al cliente.
- Si hay **Minor** → Entregar con advertencia.

Generar reporte QA y presentar al organizador.

### Paso 6: Ejecución y Evaluación

**Durante el evento:**
- Estar disponible para resolver issues técnicos
- Sugerir approaches si un equipo está atascado (sin dar ventaja)
- Recordar tiempos al organizador

**Post-presentaciones:**
- Recopilar scores del jurado por criterio
- Calcular ranking final: `Σ (puntuación × peso) / 5 × 100`
- Anunciar ganadores

### Paso 7: Documentación Post-evento

Genera:

1. **Correo de resultados** para todos los participantes y stakeholders
2. **Documento de resultados** (HTML branded con logo Snowflake):
   - Resumen ejecutivo
   - Equipos y problem statements
   - Rankings y scores
   - Descripción de cada solución
   - Fotos/screenshots si disponibles
   - Próximos pasos

3. **Skill replicable** (para cada solución ganadora):
   - Convertir la solución en un skill de Coco Desktop documentado
   - Incluir SQL, prompts, y instrucciones de uso
   - Hacer handoff al FCTO para inclusión en ISF

### Paso 8: Extracción de Resultados desde Cuentas HOL ⚠️ STOP — Solicitar credenciales

**Cuándo usar:** Después del evento, cuando se necesita generar el reporte de consumo y reconstruir los prompts/soluciones de cada equipo.

**Flujo interactivo:**

1. **Solicitar listado de cuentas** — Pedir al organizador:
   - Lista de Account IDs con formato: `ACCOUNT_ID, status, assigned_to, URL`
   - Usuario y contraseña compartidos (típicamente `USER` / contraseña genérica para cuentas HOL)
   - Indicar cuáles cuentas excluir (la del SE organizador, las decommissioned)

2. **Conectarse en paralelo a todas las cuentas** usando Python connector:
```python
import snowflake.connector
import concurrent.futures

def query_account(account_id, user, password):
    conn = snowflake.connector.connect(user=user, password=os.environ['SNOWFLAKE_PASSWORD'], account=account_id)
    cur = conn.cursor()
    
    # 1. Créditos por servicio
    cur.execute("""
        SELECT SERVICE_TYPE, SUM(CREDITS_USED) as TOTAL
        FROM SNOWFLAKE.ACCOUNT_USAGE.METERING_HISTORY
        GROUP BY SERVICE_TYPE ORDER BY TOTAL DESC
    """)
    credits = cur.fetchall()
    
    # 2. Queries AI (prompts y agentes)
    cur.execute("""
        SELECT QUERY_TEXT, USER_NAME, START_TIME
        FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
        WHERE QUERY_TEXT ILIKE '%CORTEX.COMPLETE%' 
           OR QUERY_TEXT ILIKE '%CREATE%AGENT%'
           OR QUERY_TEXT ILIKE '%PARSE_DOCUMENT%'
        ORDER BY START_TIME DESC LIMIT 30
    """)
    ai_queries = cur.fetchall()
    
    # 3. Databases creadas
    cur.execute("SHOW DATABASES")
    databases = cur.fetchall()
    
    conn.close()
    return credits, ai_queries, databases

# Paralelizar con ThreadPoolExecutor(max_workers=N)
with concurrent.futures.ThreadPoolExecutor(max_workers=len(accounts)) as executor:
    results = list(executor.map(lambda a: query_account(a[1], user, pwd), accounts))
```

3. **Para cuentas decommissioned** — Intentar conexión; si falla con "marked for decommission", marcar como no accesible y reportar N/A.

4. **Generar HTML de resultados** con la plantilla de reporte (misma estructura CSS/JS de los HOL):
   - Hero section con totales del evento
   - Sidebar navegable con una sección por cuenta
   - Por cada cuenta:
     - Tabla de créditos desglosada por servicio (SERVICE_TYPE + CREDITS_USED)
     - Descripción de la solución (inferida de queries AI + databases)
     - Prompt resumen para regenerar la solución (reconstruido del QUERY_TEXT)
   - Resumen general con tabla comparativa y distribución porcentual por categoría
   - **Charts de visualización** (ver [references/chart-templates.md](references/chart-templates.md)):
     - Pie chart: distribución de créditos por categoría (Cortex Code, Warehouse, AI, SPCS)
     - Bar chart horizontal: créditos por cuenta (ordenado mayor a menor)
     - Timeline: actividad por hora del evento
   - Logos Snowflake embebidos como base64 (NUNCA URLs externas)

5. **Guardar resultado** en: `~/Documents/COCO/cocothon/{empresa_slug}/resultados/resultados_{empresa}_{fecha}.html`

**Notas importantes:**
- Usar Python connector directo (NO `snow sql` CLI — falla con 401 en cuentas HOL)
- La vista `SNOWFLAKE.ACCOUNT_USAGE.METERING_HISTORY` tiene todos los créditos estándar
- La vista `CORTEX_FUNCTIONS_USAGE_HISTORY` puede tener latencia de 24-48h post-evento
- Para logos en HTML: embeber como `data:image/png;base64,...` usando PNGs de `~/Documents/COCO/HOL/godoy/`
- Excluir la cuenta del SE organizador del reporte (es de preparación, no del evento)
- El report es entregable al cliente — lenguaje profesional, sin datos internos de Snowflake

---

## Stopping Points

| Paso | Condición | Acción |
|------|-----------|--------|
| Paso 1 | Falta información del evento | ⚠️ STOP — Recopilar todos los inputs conversacionalmente |
| Paso 1B | Usuario necesita cuentas HOL | ⚠️ STOP — Generar solicitud y presentar para revisión |
| Paso 1C | Estructura de equipos propuesta (Customer-Led) | ⚠️ STOP — Organizador debe aprobar equipos antes de continuar |
| Paso 1D | Timeline de planeación generado | ⚠️ STOP — AE y SE deben alinearse en el countdown |
| Paso 4 | Propuesta para cliente generada | ⚠️ STOP — AE debe aprobar antes de enviar al cliente |
| Paso 5 | Materiales internos generados | ⚠️ STOP — Organizador debe aprobar antes del evento |
| Paso 5B | QA ejecutado | ⚠️ STOP — Si hay Critical, no entregar |
| Paso 6 | Scores recopilados | ⚠️ STOP — Confirmar ranking antes de anunciar |
| Paso 8 | Extracción de resultados | ⚠️ STOP — Solicitar listado de cuentas + credenciales al organizador |

---

## Output

Los entregables finales de una Cocothon son:

1. **Propuesta para el cliente** (HTML branded) — para que el AE invite al cliente
2. **Solicitud de cuentas HOL** (si aplica) — para solicitar trials con features habilitados
3. **Agenda del evento** (HTML) — visual con bloques, tiempos, entregables y criterios de evaluación
4. **Tutorial CoCo + Scripts por equipo** (HTML) — documento único con: demo de apertura, tutorial interactivo, y un script inicial personalizado para cada equipo con datos de prueba consistentes
5. **Base de datos configurada** con datos y permisos
6. **Cheat sheet para participantes** (1 página PDF)
7. **Rúbrica de evaluación** completada
8. **Documento de resultados** (HTML branded)
9. **Skills replicables** de las soluciones ganadoras
10. **Correo de resultados** para distribución
11. **Reporte de consumo y soluciones** (HTML autocontenido) — generado automáticamente desde cuentas HOL con Paso 8. Incluye créditos por servicio, prompts reconstruidos y prompt-resumen para regenerar cada solución.

---

## Generación de Outputs (detalle técnico)

### Output 3: Agenda del Evento (HTML)

Generar archivo `cocothon_{empresa}_agenda.html` usando la estructura de `templates/cocothon_agenda_template.html` como referencia. Debe incluir:

- **Header** con nombre del evento y número de equipos/participantes
- **7 bloques de agenda** con badges de color, tiempo, duración y descripción
- **Sidebar** con entregables esperados y criterios de evaluación
- **Tarjetas de equipo** (una por equipo) con: nombre, categoría, miembros, y prompt inicial resumido
- Dark theme, Montserrat + Lato, colores Snowflake
- Print-friendly

### Output 4: Tutorial CoCo + Scripts (HTML)

Generar archivo `tutorial_cocothon_{empresa}.html` usando la estructura de `templates/tutorial_template.html` como referencia. Debe incluir:

**Sección 0 — Demo de Apertura (prompt genérico, 5 min):**
- Un solo prompt que genera datos + Streamlit en vivo
- NO específico del cliente — usar ejemplo genérico (tienda de café, restaurante, etc.)
- El SE lo copia y ejecuta sin preparación previa

**Secciones 1-4 — Tutorial:**
- Qué es CoCo (lenguaje no-técnico)
- Conceptos clave de Snowflake (database, table, warehouse — en analogías)
- Cómo escribir buenos prompts (estructura: qué + con qué datos + cómo)
- 5 patrones reutilizables (Dashboard KPIs, Agente SI, Anomalías, Alertas, Clasificación IA)

**Sección 5 — Scripts Iniciales (uno por equipo):**
- Cada script genera datos de prueba **consistentes** con el dominio del cliente:
  - Tablas con volúmenes realistas (5K-30K filas en hechos, 100-500 en dimensiones)
  - Columnas en español, nombradas de forma que reflejen el negocio
  - Patrones intencionales (anomalías, estacionalidad, bajo desempeño) para que los equipos descubran insights
  - Foreign keys coherentes entre tablas
- Cada script produce una primera entrega ejecutable (Streamlit o Agente SI)
- Formato: un `prompt-box` por equipo, listo para copiar/pegar

**Secciones 6-7 — Tips y Presentación:**
- 6 tips para las 2 horas de build
- Estructura de la presentación de 3 min (Problema → Solución → Demo → Impacto)
- Recordatorio de criterios de evaluación

---

## Modalidades de Ownership Probadas

| Modalidad | Descripción | Cuándo usar | Escala típica |
|-----------|-------------|-------------|---------------|
| **Snowflake-Led** | Snowflake define los problem statements pre-diseñados | Eventos cortos (Standard), cuentas nuevas, demos de capacidad | 3-7 equipos |
| **Customer-Led** | Participantes proponen sus propios casos de uso | Eventos Extended, clientes con muchos stakeholders, ownership es prioridad | 7-15 equipos |
| **Mix** | Snowflake propone algunos + espacio para casos del cliente | Balance entre dirección y autonomía | 4-10 equipos |

**Lecciones clave del modelo Customer-Led:**
- Genera mayor ownership y engagement porque el participante resuelve SU problema
- Requiere proceso de curación (clasificación + agrupación) antes del evento
- Escala mejor para eventos grandes (20+ participantes)
- Los casos duplicados son comunes y son una oportunidad (equipos más fuertes)
- Participantes sin caso definido no son un problema — actúan como refuerzo

---

## Notas Importantes

- **ISF (Industry Solutions Framework)**: Repositorio central de Snowflake para demos, skills y assets de GTM orientados a outcomes de negocio. La Cocothon alimenta el ISF con soluciones validadas en campo.
- **Multi-industria**: Este framework funciona para CUALQUIER industria. Los problem statements se adaptan según el vertical.
- **Multi-región**: Adaptar idioma y ejemplos según la región (LATAM, España, Francia, DACH, etc.)
- **Cortex Code es el hero**: El diferenciador es la velocidad con la que se construyen soluciones usando Coco. Siempre enfatizar esto.
- **Datos antes que código**: El 80% del éxito de una Cocothon depende de tener buenos datos preparados. Invertir tiempo aquí.
- **Consultar anti-patterns**: Antes de entregar, revisar [references/anti-patterns.md](references/anti-patterns.md) para evitar errores comunes.
- **Recovery ante fallos**: Si algo falla durante el evento, consultar [references/error-fallbacks.md](references/error-fallbacks.md).

---

## Agenda Modelo — 4 Horas de Impacto

Agenda sugerida para modalidad **Standard/Full Day (4h)**. Adaptar tiempos según modalidad.

| Bloque | Tiempo | Duración | Actividad | Detalle |
|--------|--------|----------|-----------|---------|
| 1 | 0:00–0:10 | 10 min | **Apertura + Demo** | Demo RÁPIDA en vivo de CoCo (un solo prompt → resultado funcional) |
| 2 | 0:10–0:25 | 15 min | **Retos + Equipos** | Presentación de retos, formación de equipos, entrega de scripts iniciales |
| 3 | 0:25–0:40 | 15 min | **Tutorial CoCo** | Hands-on: cómo pedir cosas a CoCo, patterns básicos, cheat sheet |
| 4 | 0:40–2:50 | 2h 10min | **CoCo TIME** | Equipos construyen soluciones — mentores circulan. Cada equipo arranca con su script inicial |
| 5 | 2:50–3:05 | 15 min | **Prep Demos** | Equipos pulen su presentación y demo |
| 6 | 3:05–3:40 | 35 min | **Demo Day** | Cada equipo presenta en vivo desde CoCo (5-7 min) |
| 7 | 3:40–4:00 | 20 min | **Premiación** | Jurado, reconocimiento y próximos pasos |

**Entregables esperados por equipo:**
- App funcional (Streamlit app o notebook ejecutable en CoCo)
- Al menos 1 hallazgo/insight accionable respaldado con datos
- Plan de implementación (cómo llevar la solución a producción)

**Criterios de Evaluación (defaults — personalizables):**

Pregunta al organizador: "¿Quieres usar los criterios de evaluación estándar o personalizarlos?"
- Si acepta los defaults → usar tal cual
- Si quiere personalizar → pedir criterios y pesos (deben sumar 100%)

Defaults:
- 30% — Impacto de negocio
- 25% — Uso creativo de CoCo / AI
- 25% — Demo funcional (corre en vivo)
- 20% — Presentación y storytelling

**Variantes de agenda:**
- **Sprint (90 min):** Eliminar Tutorial CoCo, reducir CoCo TIME a 45 min, Demo Day 3 min/equipo
- **Extended (2-3 días):** Día 1 = Kick-off + Tutorial + ideación. Día 2 = CoCo TIME (6h). Día 3 = Demo Day + Premiación

---

## Demo de Apertura (Bloque 1 — máx 10 min)

Demo RÁPIDA en vivo. No es un tutorial largo — es un "wow moment" de 10 minutos para que los participantes vean lo que van a poder hacer. El SE ejecuta UN SOLO prompt que genera algo funcional de punta a punta.

**Principio:** Un solo prompt → resultado funcional visible. Sin explicaciones largas. Speed > completitud.

**Script (3 pasos, 10 min):**

```
1. [2 min] Contexto: "Voy a resolver un problema real de [INDUSTRIA] en menos de 5 minutos usando CoCo..."
2. [5 min] UN SOLO PROMPT largo que: crea datos + entrena modelo/crea dashboard + despliega resultado
3. [3 min] Mostrar el resultado funcionando (Streamlit corriendo, agente respondiendo, gráfico visible)
```

**Prompt de demo sugerido (copiar y pegar en CoCo):**

```
Crea una base de datos DEMO_COCOTHON con datos sintéticos de [INDUSTRIA]: 
una tabla de hechos con 10,000 filas y 2 dimensiones. Luego crea un 
Streamlit app que muestre los KPIs principales en tarjetas con tendencia, 
un gráfico de barras por [DIMENSIÓN] y una tabla con los top 10 registros 
ordenados por [MÉTRICA]. Todo en una sola pantalla.
```

**El SE adapta [INDUSTRIA], [DIMENSIÓN], [MÉTRICA] según el cliente.** No hay que preparar nada — CoCo genera todo en vivo.

---

## Generación de Script Inicial por Equipo

Después de la demo de apertura y la formación de equipos, el SE **genera un script/prompt inicial personalizado para CADA equipo** basado en su categoría y problem statement. Este script es el punto de partida del CoCo TIME.

**Proceso:**
1. Tomar la lista de equipos con sus PS (de Paso 1C o Paso 1)
2. Para cada equipo, generar un prompt contextualizado que:
   - Crea los datos sintéticos relevantes a su caso
   - Sugiere la primera acción concreta (dashboard, agente, pipeline)
   - Incluye nombres de tablas/columnas coherentes con su dominio
3. Entregar el script a cada equipo al inicio del CoCo TIME (por chat, email, o impreso)

**Template de generación (para que CoCo genere los scripts):**

```
Genera un prompt inicial para un equipo de Cocothon con estos datos:
- Empresa: [EMPRESA]
- Equipo: [NOMBRE_EQUIPO]  
- Caso de uso: [DESCRIPCIÓN_PS]
- Participantes: [NOMBRES]

El prompt debe:
1. Crear una base de datos con tablas relevantes al caso (datos sintéticos, 5K-10K filas)
2. Sugerir la primera entrega: un Streamlit dashboard O un agente SI O un pipeline automatizado
3. Ser ejecutable directamente en Cortex Code sin modificaciones
4. Usar nombres de tablas y columnas en español que reflejen el dominio del equipo
```

**Ejemplo de script generado para un equipo de "Cobranzas":**

```
Crea una base de datos COCOTHON_COBRANZAS con:
- Tabla CARTERA (10,000 filas): id_obligacion, cliente, monto_deuda, 
  dias_mora, casa_cobranza, estado, fecha_ultimo_pago, canal_contacto
- Tabla GESTIONES (50,000 filas): id_gestion, id_obligacion, fecha, 
  tipo_gestion, resultado, agente, duracion_min
- Tabla CASAS_EXTERNAS (10 filas): id_casa, nombre, comision_pct, 
  zona_asignada

Luego crea un Streamlit app que muestre:
- KPI cards: total cartera, % recuperación, días mora promedio
- Gráfico de barras: recuperación por casa de cobranza
- Tabla: top 20 obligaciones con mayor monto en mora
- Filtros: rango de fechas, casa de cobranza, rango de mora
```

**Ejemplo de script generado para un equipo de "Agente Comercial":**

```
Crea una base de datos COCOTHON_VENTAS con:
- Tabla PRODUCTOS (500 filas): id_producto, nombre, categoria, precio, 
  stock_disponible, margen_pct, descripcion
- Tabla CLIENTES (1,000 filas): id_cliente, nombre, segmento, 
  credito_aprobado, credito_usado, ultima_compra
- Tabla VENTAS_HISTORICAS (20,000 filas): id_venta, id_cliente, 
  id_producto, fecha, cantidad, valor_total, canal

Luego crea un agente de Snowflake Intelligence que:
- Responda preguntas sobre inventario y disponibilidad
- Sugiera cross-sell basado en historial del cliente
- Alerte sobre productos con bajo stock
- Use lenguaje natural, responda en español
```

---

## Prompts Starter por Categoría

**Load**: `categories/{categoria}.md` para el modelo de datos completo, anomalías intencionales, validation queries y ejemplos de output de cada categoría.

Categorías disponibles (ver [categories/README.md](categories/README.md) para índice completo):
- `categories/finanzas.md` — Conciliación, fraude, forecasting, auditoría
- `categories/rrhh.md` — Rotación, clima, nómina, ausentismo
- `categories/logistica.md` — Rutas, inventario, demanda, entregas
- `categories/comercial.md` — Segmentación, churn, pipeline, pricing
- `categories/calidad.md` — SPC, trazabilidad, no-conformidades, causa raíz
- `categories/analytics.md` — Self-service, KPIs, data catalog, text-to-SQL
- `categories/servicio-cliente.md` — Sentimiento, SLA, chatbot, escalaciones

Cada archivo contiene: CREATE TABLE DDL, volúmenes, FKs, patrones intencionales (anomalías a insertar), validation queries, y 2 ejemplos de output (Streamlit + Agent).

---

## Output Directory Structure

```
~/Documents/COCO/cocothon/
  {empresa_slug}/
    v1/
      YYYY-MM-DD_cocothon_{empresa}_agenda.html    ← Agenda visual con equipos
      YYYY-MM-DD_tutorial_cocothon_{empresa}.html   ← Tutorial + scripts por equipo
      YYYY-MM-DD_propuesta_{empresa}.html           ← Propuesta para el cliente
      YYYY-MM-DD_propuesta_{empresa}.pdf            ← (si piden PDF)
      contexto.md                                    ← Contexto del evento (equipos, participantes, casos)
      briefing_tecnico.md
      cheatsheet_participantes.pdf
      rubrica_evaluacion.html
      qa_report.md
    resultados/
      YYYY-MM-DD_resultados_{empresa}.html
      skills_generados/
        skill_equipo_1/
        skill_equipo_2/
```

Reglas:
- **TODOS los outputs llevan prefijo `YYYY-MM-DD_` en su nombre de archivo** (fecha del evento o fecha de generación)
- `empresa_slug`: nombre sin espacios ni caracteres especiales (snake_case, lowercase)
- Incrementar versión (v1, v2, v3) si se regeneran outputs
- Verificar existencia con `ls ~/Documents/COCO/cocothon/{slug}/` antes de escribir
- Cada cliente tiene su propia carpeta independiente

---

## Post-Execution: Persist Lessons

Después de entregar outputs o ejecutar un evento:

1. Clasificar learnings nuevos:
   - `error-fix` — algo que falló y cómo se resolvió
   - `pattern` — un approach que funcionó bien
   - `gotcha` — algo no obvio que puede sorprender
   - `optimization` — forma más eficiente de hacer algo

2. Persistir: `cortex memory remember "<lesson descriptiva>" --rule` si es crítica, sin `--rule` si es preferencia.

3. Actualizar este skill si el learning es generalizable (no específico de un cliente).

4. **Actualizar `skill_evidence.yaml`** con datos del evento ejecutado:
   ```yaml
   - empresa: {empresa_slug}
     fecha: YYYY-MM-DD
     modalidad: standard|full_day|sprint|extended
     ownership: snowflake-led|customer-led|mix
     equipos: N
     soluciones: N
     creditos_total: N.NN
     resultados: ~/Documents/COCO/cocothon/{empresa_slug}/resultados/YYYY-MM-DD_resultados_{empresa}.html
   ```

---

## Auto-Mejora Continua

Este skill se mejora automáticamente después de cada ejecución. El flujo es:

### Triggers de Auto-Mejora

Después de **cada cocothon ejecutado** (Paso 8 completado), evaluar:

| Señal | Acción |
|-------|--------|
| Una categoría nueva fue usada (no existe en `categories/`) | Crear nuevo archivo `categories/{nueva}.md` con el modelo de datos real del evento |
| Un prompt de equipo tuvo resultado excepcional (>4.5/5 en evaluación) | Agregar como ejemplo a la categoría correspondiente |
| Un anti-pattern nuevo se descubrió | Agregar a `references/anti-patterns.md` |
| Un componente HTML nuevo fue creado ad-hoc | Agregar a `references/component-library.md` |
| Los criterios de evaluación personalizados funcionaron mejor que los defaults | Proponer como nuevos defaults o agregar como archetype alternativo en `references/archetypes.md` |
| El consumo de créditos difiere >30% del benchmark histórico | Agregar nota en `references/cocothon-framework.md` con el nuevo rango esperado |
| Un error de Paso 8 (cuenta inaccesible, query diferente) | Agregar fallback a `references/error-fallbacks.md` |

### Proceso de Auto-Mejora (ejecutar después de cada evento)

```
1. Revisar skill_evidence.yaml — ¿cuántos eventos van? ¿hay patrones?
2. Comparar resultados del evento actual vs benchmarks en `skill_evidence.yaml` (primer benchmark: 141 credits, 10 soluciones, 4h, 11 participantes)
3. Si hay mejoras identificadas:
   a. Editar el archivo correspondiente (references/, categories/, templates/)
   b. NO modificar SKILL.md a menos que sea un cambio de workflow
   c. Incrementar version en frontmatter solo si cambia el workflow (X.Y.Z → X.Y.Z+1 para fixes, X.Y+1.0 para features)
4. Persistir el learning en memory:
   cortex memory remember "<descripción del learning>" --title "<título corto>" --tags cocothon auto-mejora
5. Si el cambio aplica a TODOS los cocothons futuros → editar skill
   Si el cambio es específico de una industria/cliente → editar solo la categoría o crear un archivo en el directorio del cliente
```

### Métricas de Calidad del Skill (revisar cada 3 ejecuciones)

| Métrica | Target | Cómo medir |
|---------|--------|------------|
| Tiempo de setup (Paso 1) | <5 min con archetype, <15 min interactivo | Timestamp primera pregunta → config completa |
| Outputs generados sin errores QA | >90% | qa_report.md sin Critical/Major |
| Reutilización de templates | >80% de outputs usan template parametrizado | Verificar si se usó `{{TOKEN}}` vs HTML ad-hoc |
| Precisión de datos sintéticos | 0 errores FK, volumes ±10% de spec | Validation queries de categories/ |
| Satisfacción del organizador | Pregunta post-evento | "¿El skill te ahorró tiempo? ¿Qué faltó?" |

### Auto-Enriquecimiento de Categorías

Cuando un equipo construye una solución exitosa en una categoría existente:

1. Extraer el prompt final del QUERY_HISTORY (Paso 8)
2. Comparar con el ejemplo actual en `categories/{cat}.md`
3. Si el prompt real es mejor (más completo, mejor modelo de datos):
   - Reemplazar o agregar como segundo ejemplo
   - Actualizar DDL si las tablas del evento fueron más ricas
4. Si el equipo usó una técnica nueva (ej: PARSE_DOCUMENT, Cortex Search, Dynamic Tables):
   - Agregar como "Variante avanzada" en la categoría

### Auto-Actualización del Profile

Después de cada evento, verificar si el profile bundled necesita:
- Actualización de track record (número de cocothons ejecutados)
- Nuevos templates de correo basados en lo que funcionó
- Nuevos tips en la sección "Next Steps Post-Evento" basados en lo que el AE realmente hizo
