---
name: poc-readout
title: POC Readout Generator
summary: Generate branded HTML/PDF POC readout documents with KPIs, architecture, criteria evaluation, and next steps.
description: "Use when creating a professional POC readout document for a Snowflake implementation. Produces branded HTML with KPIs, timeline, criteria evaluation, architecture diagram, object inventory, governance evidence, and next steps. Triggers: readout, POC readout, POC report, POC summary, generate readout, create readout, implementation report."
tools: [Read, Write, Edit, Bash, Glob, Grep, web_search, web_fetch, AskUserQuestion, sql_execute, Task]
prompt: "$poc-readout"
language: en
status: Published
author: JP Castaneda
type: snowflake
---

# POC Readout Generator

## About / Acerca de / Sobre

**English:** Generate a branded, print-ready HTML readout document summarizing a Snowflake POC implementation. Includes KPIs, timeline, criteria evaluation, architecture diagram, object inventory, governance evidence, and next steps. Produces HTML, PDF, and optionally DOCX output.

**Español:** Genera un documento HTML profesional con marca Snowflake que resume la implementación de un POC. Incluye KPIs, cronograma, evaluación de criterios, diagrama de arquitectura, inventario de objetos, evidencia de gobernanza y próximos pasos. Produce salida HTML, PDF y opcionalmente DOCX.

**Português (BR):** Gera um documento HTML profissional com marca Snowflake que resume a implementação de um POC. Inclui KPIs, cronograma, avaliação de critérios, diagrama de arquitetura, inventário de objetos, evidência de governança e próximos passos. Produz saída HTML, PDF e opcionalmente DOCX.

## When to Use

- Wrapping up a POC and need a branded HTML/PDF summary for the customer
- Creating an internal stakeholder summary of POC results and recommendations
- Producing a formal deliverable with KPIs, architecture, and next steps after a Snowflake implementation

---

## Pre-Execution: Memory Recall

Before starting any work on this skill:

1. **Query CoCo memory**:
   - Run `cortex memory recall "POC readout HTML report Snowflake branded"`

2. **Apply as constraints**:
   - Lessons marked `error-fix` or `gotcha` → HARD RULES (never violate)
   - Lessons marked `pattern` → PREFERRED APPROACH
   - Lessons marked `preference` → DEFAULT (apply unless user overrides)

---

## Configuration Menu / Menú de Configuración

**Present this menu to the user BEFORE starting any work.** Each toggle enables/disables a feature. Apply recipe defaults first (see `assets/recipes/poc-archetypes.md`), then let the user override.

```
═══════════════════════════════════════════════════════
  POC READOUT — Configuration / Configuración
═══════════════════════════════════════════════════════

  SECTIONS (include in readout):
  ─────────────────────────────────────────────────────
  [x] 1.  Executive Summary        always on
  [x] 2.  Execution Timeline        always on
  [x] 3.  Criteria — Usability
  [x] 4.  Criteria — Cost/Benefit
  [x] 5.  Criteria — Ease of Use
  [x] 6.  Success Metrics
  [ ] 7.  Security & Governance      auto-detect*
  [ ] 8.  ML & AI                    auto-detect*
  [ ] 9.  Data Application           auto-detect*
  [x] 10. Full Object Inventory
  [x] 11. Next Steps
  [x] 12. Reference Architecture

  OPTIONAL FEATURES:
  ─────────────────────────────────────────────────────
  [ ] wins_risks_ask          Wins/Risks/Ask grid in Executive Summary
  [ ] health_score            Quantitative POC Health Score (0-100)
  [ ] engagement_timeline     Meeting cadence & SE hours (requires SNOWHOUSE)
  [ ] peer_comparison         Benchmark against industry peers (requires SFDC ID)
  [ ] chart_generation        Generate matplotlib PNG charts
  [ ] multi_account           Aggregate data across multiple Snowflake accounts
  [ ] docx_output             Generate Word document in addition to HTML/PDF
  [ ] customer_safe_mode      Strip internal data (SFDC details, SE comments, ACV)

  OUTPUT FORMAT:
  ─────────────────────────────────────────────────────
  [x] HTML      always on
  [x] PDF       always on
  [ ] DOCX      requires docx_output toggle above
  [x] .drawio   architecture diagram (editable)

  PERSONA (affects tone and detail level):
  ─────────────────────────────────────────────────────
  ( ) executive       High-level, scannable in 2 min, no SQL, no technical detail
  (x) technical       Full detail, SQL evidence, performance metrics
  ( ) customer_safe   Like technical but strips all internal-only data

  LANGUAGE / IDIOMA:
  ─────────────────────────────────────────────────────
  ( ) English
  ( ) Spanish / Español
  ( ) Auto-detect (match conversation language)

  POC ARCHETYPE (pre-fills section toggles):
  ─────────────────────────────────────────────────────
  ( ) analytics_only      Data loading, modeling, BI
  ( ) ai_ml_platform      Cortex AI, Agents, Semantic Views, ML
  ( ) governance_security  Tags, policies, DMFs, lineage
  ( ) full_platform        Everything (kitchen sink)
  ( ) migration            Performance benchmarks, cost comparison
  ( ) data_sharing         Secure sharing, Marketplace
  ( ) custom               User picks individual sections

  * auto-detect: Section is included if relevant objects are
    found during Step 0 discovery. Can be forced on/off.
═══════════════════════════════════════════════════════
```

**Menu handling rules:**
1. Present the menu as an `AskUserQuestion` with the archetype as the primary question
2. Apply archetype recipe defaults from `assets/recipes/poc-archetypes.md`
3. Show the resolved config and let user toggle individual items
4. Store final config as a local variable — reference throughout all steps
5. Sections marked `auto-detect` are resolved after Step 0 queries run

---

## Tone & Voice Guidelines / Guías de Tono y Voz

**Load** `references/tone-voice-guidelines.md` — apply to ALL generated text.

---

## Prerequisites / Prerrequisitos

- Active Snowflake connection to the POC account / Conexión activa de Snowflake a la cuenta del POC
- ACCOUNTADMIN or role with access to ACCOUNT_USAGE views / ACCOUNTADMIN o rol con acceso a vistas de ACCOUNT_USAGE
- Knowledge of what was implemented (or ability to discover via SQL) / Conocimiento de lo implementado (o capacidad de descubrirlo via SQL)

## Setup

1. **Load** `references/error-fallbacks.md` — review before any query execution
2. **Load** `references/anti-patterns.md` — review before building
3. **Load** `references/component-library.md` for CSS classes and HTML patterns
4. **Copy** `assets/readout-template.html` as the starting template

## Bundled Assets

The `assets/` directory contains reusable files for every readout build:

| File | Description | When to Use |
|------|-------------|-------------|
| `readout-template.html` | Complete HTML template with `{{PLACEHOLDER}}` tokens | Starting point for every readout — copy and replace tokens |
| `snowflake-logo-full.png` | Snowflake icon + "snowflake" wordmark (blue on transparent) | Header RIGHT logo — apply `filter: brightness(0) invert(1)` on dark backgrounds |
| `snowflake-linebreak.webp` | Horizontal rule with centered Snowflake icon | Section divider between every section |
| `bg-texture.svg` | Dot-gradient texture pattern | Header background texture |
| `recipes/poc-archetypes.md` | Pre-configured section toggles for 6 POC types | Config menu — load before presenting to user |

When building a readout, copy all asset files into the output directory alongside the HTML.

## Dependencies (Invoke as Needed)

Invoke dependencies using the `skill` tool (e.g., `skill: snowflake-brand-guidelines`). Only invoke skills relevant to the readout being built.

| Skill | When | Purpose |
|-------|------|---------|
| `snowflake-brand-guidelines` | Always | CSS palette, typography, logo handling, header, footer |
| `html-to-pdf` | Always (final step) | Print CSS injection + Chrome headless PDF conversion |
| `customer-usage` | When enriching with A360/RAVEN consumption data | Feature usage breakdown, credit categories, org discovery |
| `se-poc-planner` | When a POC plan exists | Import session definitions, success criteria, timeline |
| `cortex-agent` | If POC includes Cortex Agents | Verify agent configuration |
| `semantic-view` | If POC includes Semantic Views | Verify semantic view structure |
| `data-governance` | If POC includes governance features | Tags, policies, classification details |
| `data-quality` | If POC includes DMFs | Data quality monitoring evidence |

## References

| File | Purpose | When to Load |
|------|---------|-------------|
| `references/queries.md` | 27 SQL queries organized by section | Step 2 (query execution) |
| `references/execution-testing.md` | Query validation + data cross-checks | Before Step 2 and after Step 3 |
| `references/error-fallbacks.md` | Error handling for every failure mode | Setup (always) |
| `references/anti-patterns.md` | Common mistakes to avoid | Setup (always) |
| `references/quality-checklist.md` | Final validation before delivery | Step 5 (polish) |
| `references/component-library.md` | 17 HTML/CSS components | Step 3 (build HTML) |
| `references/chart-generation.md` | Matplotlib chart templates | Step 3 if `chart_generation: true` |
| `references/peer-comparison.md` | Peer benchmark queries + HTML component | Step 2.5 if `peer_comparison: true` |
| `references/engagement-timeline.md` | Meeting cadence queries + HTML component | Step 2.5 if `engagement_timeline: true` |

---

## Workflow / Flujo de Trabajo

### Step 0 / Paso 0: Configuration & Smart Discovery / Configuración y Descubrimiento Inteligente

**0a. Present Configuration Menu** (see above). Resolve archetype + user overrides.

**0b. Auto-discovery queries (run in parallel):**

1. **Active databases** — identify POC databases:
   ```sql
   SHOW DATABASES;
   ```
2. **Recent warehouse activity** — infer POC date range:
   ```sql
   SELECT MIN(USAGE_DATE) AS FIRST_ACTIVITY, MAX(USAGE_DATE) AS LAST_ACTIVITY,
       SUM(CREDITS_USED) AS TOTAL_CREDITS
   FROM SNOWFLAKE.ACCOUNT_USAGE.METERING_DAILY_HISTORY
   WHERE CREDITS_USED > 0;
   ```
3. **Account info** — region and edition:
   ```sql
   SELECT CURRENT_ACCOUNT(), CURRENT_REGION();
   ```
4. **Object scan** — detect what was built (for auto-detect sections):
   ```sql
   -- Run these in parallel against each candidate database
   SHOW CORTEX SEARCH SERVICES IN DATABASE <DB>;
   SHOW AGENTS IN DATABASE <DB>;
   SHOW SEMANTIC VIEWS IN DATABASE <DB>;
   SHOW STREAMLIT IN DATABASE <DB>;
   SHOW ROW ACCESS POLICIES IN DATABASE <DB>;
   SHOW MASKING POLICIES IN DATABASE <DB>;
   SHOW DYNAMIC TABLES IN DATABASE <DB>;
   ```

**0c. Resolve auto-detect sections:**
- If Agents/Search/Semantic Views found → enable Section 8 (ML & AI)
- If Streamlit found → enable Section 9 (Data App)
- If tags/masking/RAP/DMF found → enable Section 7 (Governance)

**0d. Multi-account discovery** (if `multi_account: true`):
   ```sql
   -- On SNOWHOUSE connection
   SELECT SNOWFLAKE_ACCOUNT_NAME, SNOWFLAKE_DEPLOYMENT, SUM(TOTAL_CREDITS) AS CREDITS
   FROM SALES.RAVEN.A360_CREDIT_USAGE_VIEW
   WHERE SALESFORCE_ACCOUNT_NAME ILIKE '%<account_name>%'
   GROUP BY 1, 2
   ORDER BY 3 DESC;
   ```
   Present all accounts to user. Let them select which to include.

Present discovered context + resolved config to the user before proceeding.

**STOP**: Confirm configuration and context before Step 1.

### Step 1 / Paso 1: Gather POC Context / Recopilar Contexto del POC

**Ask user for / Preguntar al usuario** (skip items already auto-discovered):

1. **Customer name** and POC description / **Nombre del cliente** y descripción del POC
2. **Customer logo file** (PNG with transparent background) / **Logo del cliente** (PNG con fondo transparente)
3. **Snowflake connection name** to query account metadata / **Nombre de conexión Snowflake** para consultar metadatos
4. **Date range** of the POC (start and end dates) / **Rango de fechas** del POC (inicio y fin)
5. **Key databases/schemas** used / **Bases de datos/schemas** principales usados
6. **Credit price** ($/credit) — do NOT assume $3.00 / **Precio del crédito** — NO asumir $3.00
7. **Evidence screenshots** (optional path to `img/` folder) / **Capturas de evidencia** (ruta opcional a carpeta `img/`)
8. **Output path** for the HTML file / **Ruta de salida** para el archivo HTML
9. **Salesforce Account ID** (optional, for enrichment & peer comparison) / **ID de cuenta Salesforce** (opcional)

**Defaults if not provided / Valores por defecto si no se proporcionan:**
- Dates / Fechas: Use auto-discovered range from Step 0
- Connection / Conexión: Use active connection / Usar conexión activa
- Language / Idioma: From config menu selection
- Output path: `<cwd>/poc_readout_<customer_slug>/`

**STOP**: Confirm context before proceeding. / Confirmar contexto antes de continuar.

### Step 2 / Paso 2: Query Account Metadata / Consultar Metadatos de la Cuenta

**Load** `references/queries.md` for the complete query reference.
**Load** `references/execution-testing.md` — run Phase 1 (compilation checks) first.

Run these query groups in parallel against the POC account:

**Group A — Consumption (Section 2.1–2.3):**
- Total credits consumed
- Credits by warehouse
- Credits by day

**Group B — Storage (Section 3.1):**
- Average storage in GB

**Group C — Object Inventory (Section 4.1–4.11):**
- Tables and views, Warehouses, Dynamic Tables
- Streams, Tasks, Stages
- Functions and Procedures
- Cortex Search Services, Streamlit Apps, Semantic Views, Agents

**Group D — Governance (Section 5.1–5.5):** (if Section 7 enabled)
- Row Access Policies, Masking Policies
- Tags and tag references
- Data Metric Functions (DMFs)

**Group E — Performance (Section 6.1–6.2):**
- Query performance summary
- Spilling analysis

**Group F — Continuous Work Hours (KPI card):**
- Run Query 6.3 (gap analysis with 15-min inactivity threshold)
- This measures REAL continuous work time, not hours with at least one query
- **CRITICAL**: NEVER use `COUNT(DISTINCT HOUR(START_TIME))` — this overcounts by 2-4x
- Use the `CONTINUOUS_HOURS` result for the `{{POC_HOURS}}` KPI card
- Format as `~Xh` (round to nearest half-hour, e.g., 4.8h → "~5h")
- Use `TOTAL_SESSIONS`, `ACTIVE_DAYS` for the `{{POC_HOURS_DETAIL}}` text
- Run the daily breakdown variant to validate per-day figures are realistic

**After all queries complete:** Run Phase 2 cross-checks from `references/execution-testing.md`. Present validation report to user.

### Step 2.5 / Paso 2.5: Data Enrichment / Enriquecimiento de Datos (conditional / condicional)

Run enrichment only if enabled features require SNOWHOUSE data.

**If `peer_comparison: true`:**
- **Load** `references/peer-comparison.md`
- Run queries PC.1–PC.3 on SNOWHOUSE connection
- Skip if < 5 peers found (not statistically meaningful)

**If `engagement_timeline: true`:**
- **Load** `references/engagement-timeline.md`
- Run queries ET.1–ET.3 on SNOWHOUSE connection
- Skip if 0 engagements found

**If Salesforce Account ID provided:**
- Run `references/queries.md` Sections 1, 7 for account info and use cases
- 3-tier fallback: RAVEN → Vivun → SFDC (see `references/error-fallbacks.md`)

**STOP**: Present enrichment summary and confirm scope before building.

### Step 3 / Paso 3: Build HTML Document / Construir Documento HTML

**Invoke** the `snowflake-brand-guidelines` skill for CSS foundation.

1. Copy `assets/readout-template.html` as the starting template
2. Copy all assets (`snowflake-logo-full.png`, `snowflake-linebreak.webp`, `bg-texture.svg`) to output directory
3. Replace all `{{PLACEHOLDER}}` tokens with queried data and user-provided values

**Conditional tokens** — replace with content or empty string based on config:

| Token | Config Flag | Content if enabled | If disabled |
|-------|------------|-------------------|-------------|
| `{{WINS_RISKS_ASK}}` | `wins_risks_ask` | WRA grid HTML (Component 16) | empty string |
| `{{HEALTH_SCORE}}` | `health_score` | Health gauge HTML (Component 17) | empty string |
| `{{ENGAGEMENT_TIMELINE}}` | `engagement_timeline` | KPI cards + engagement table | empty string |
| `{{PEER_COMPARISON}}` | `peer_comparison` | Peer benchmark callout + table | empty string |

**If `chart_generation: true`:**
- **Load** `references/chart-generation.md`
- Generate PNG charts using matplotlib
- Embed in HTML as `<figure class="screenshot"><img src="chart_*.png" ...></figure>`

**Required sections / Secciones requeridas:**

| # | Section (EN) | Sección (ES) | Content Source |
|---|-------------|--------------|----------------|
| 1 | Executive Summary | Resumen Ejecutivo | KPIs + scope + WRA + Health Score |
| 2 | Execution Timeline | Timeline de Ejecución | Gantt + engagement timeline |
| 3 | Criteria: Usability | Criterio: Usabilidad | 2 cards: Data Querying + Modeling |
| 4 | Criteria: Cost/Benefit | Criterio: Costo/Beneficio | Credit breakdowns + peer comparison |
| 5 | Criteria: Ease of Use | Criterio: Facilidad de Uso | Performance metrics |
| 6 | Success Metrics | Métricas de Éxito | 4 metric cards |
| 7 | Security & Governance | Seguridad y Gobernanza | Governance queries + evidence |
| 8 | ML & AI | ML e IA | AI object inventory |
| 9 | Data App | Data App | Streamlit apps |
| 10 | Full Object Inventory | Inventario Completo | All objects |
| 11 | Next Steps | Próximos Pasos | 3 recommendation cards |
| 12 | Reference Architecture | Arquitectura de Referencia | Auto-generated diagram |

**Disabled section handling:** If a section is toggled off in config, remove the entire `<section>` block AND its preceding `<img class="section-divider">` from the HTML. Do not leave empty sections.

**Empty data handling:** If a section is enabled but queries returned no data, show: "No [feature] was implemented during this POC." / "No se implementó [funcionalidad] durante este POC."

**Credit cost calculation:** Use the credit price confirmed in Step 1. Format: `TOTAL_CREDITS × $PRICE/credit = $TOTAL`.

### Step 3.5 / Paso 3.5: Build Architecture Diagram / Construir Diagrama de Arquitectura

Create a 4-column CSS architecture diagram dynamically from the object inventory:

| Column (EN) | Columna (ES) | Auto-populate from |
|-------------|-------------|---------------------|
| Source System | Sistema Fuente | Stages (infer sources), user input |
| ETL/Integration | ETL/Integración | Streams, Tasks, Dynamic Tables |
| Snowflake | Snowflake | All objects grouped by zone |
| Consumers | Consumidores | Streamlit, Semantic Views, BI tools |

**Auto-detection rules:**
- S3/Azure/GCS stages → cloud storage source nodes
- CDC streams → "Change Data Capture" in ETL column
- Dynamic Tables → "Dynamic Table Pipeline" in ETL column
- Cortex Search/Agents → "Cortex AI" in Snowflake column
- Governance data → "Horizon" sub-section in Snowflake column

Also generate a `.drawio` XML file for the customer to edit.

### Step 4 / Paso 4: Governance Section / Sección de Gobernanza (if enabled)

If Section 7 is enabled and governance features detected:

1. Query tags applied to columns → classification table
2. Embed evidence screenshots from `img/` folder
3. Add Horizon sub-section with criteria cards
4. Add governance to architecture diagram

### Step 5 / Paso 5: Final Polish / Pulido Final

**Load** `references/quality-checklist.md` — run through every item.
**Load** `references/execution-testing.md` — run Phase 3 (token check) and Phase 4 prep.

1. Verify all KPI numbers match queried data
2. Verify object counts in inventory match KPI card
3. Verify Gantt dates match the POC date range
4. Verify architecture diagram reflects actual objects
5. Verify credit breakdowns sum to total credits
6. Verify POC hours are from continuous gap analysis (Query 6.3), NOT from counting distinct hours
7. Verify all text matches the chosen language (no mixing)
8. Verify persona rules applied (executive = no SQL blocks, customer_safe = no internal data)
9. Add print CSS (already in template — verify completeness)
10. Add footer with account ID, region, and date

**STOP**: Present the completed HTML path to user for review.

### Step 6 / Paso 6: Generate PDF / Generar PDF

**Invoke** the `html-to-pdf` skill.

1. Verify the HTML has complete print CSS (the template includes it)
2. Generate PDF using Chrome headless (see `references/error-fallbacks.md` for Chrome path fallbacks)
3. Verify PDF page count and visual fidelity
4. Report file size to user

**If `docx_output: true`:**

Generate a Word document using `python-docx`. Structure: title page → one heading per enabled section → tables and paragraphs from the same data used for HTML → embedded chart PNGs. The DOCX is a simplified version — no CSS styling replication.

**STOP**: Present PDF (and DOCX if enabled) to user for final review.

### Step 7 / Paso 7: Deliver / Entregar

Report to the user:
- File paths and sizes for all artifacts
- Remind the SE to **review the PDF** before sharing with the customer
- If `customer_safe_mode` was NOT enabled, remind to **remove internal-only data** before delivery
- Note: compliance toggles are interactive in HTML but static in PDF

## Parameterization

**Load** `references/parameterization.md` for the full token reference (core, KPI, and section content parameters).

## Stopping Points / Puntos de Parada

| Step | Condition | Action |
|------|-----------|--------|
| Step 0 | Config menu + auto-discovery complete | Present config + discovered context; confirm |
| Step 1 | All inputs gathered | Confirm parameters and date range |
| Step 2.5 | Enrichment complete | Confirm scope and enrichment data |
| Step 5 | HTML built and polished | Review HTML before PDF generation |
| Step 6 | PDF (+ DOCX) generated | Final review before delivery |

## Output / Salida

```
<output_path>/
├── poc_readout_<customer_slug>.html     — Interactive HTML readout
├── poc_readout_<customer_slug>.pdf      — Print-ready PDF
├── poc_readout_<customer_slug>.docx     — Word document (if docx_output: true)
├── architecture_<customer_slug>.drawio  — Editable architecture diagram
├── chart_daily_credits.png              — Credit trend chart (if chart_generation: true)
├── chart_warehouse_credits.png          — Warehouse donut (if chart_generation: true)
├── chart_health_score.png               — Health gauge (if chart_generation + health_score)
├── chart_feature_adoption.png           — Peer adoption (if chart_generation + peer_comparison)
├── snowflake-logo-full.png              — Logo asset
├── snowflake-linebreak.webp             — Section divider asset
├── bg-texture.svg                       — Header texture asset
├── <customer_logo>.png                  — Customer logo
└── img/                                 — Evidence screenshots (if provided)
    ├── lineage.png
    ├── data-quality.png
    └── tags-policies.png
```

## Notes / Notas

- All text in the generated readout should match the POC language (Spanish for LATAM, English for US)
- Credit cost: use the price confirmed in Step 1 — never assume $3.00
- Storage cost: $23/TB/month standard unless user specifies otherwise
- The HTML is self-contained (no external CSS/JS dependencies beyond fonts)
- Print CSS ensures clean output via `html-to-pdf` skill
- ACCOUNT_USAGE views have up to 45-minute latency — run queries after POC window closes
- A360/RAVEN queries require `SNOWHOUSE_AWS_US_WEST_2` connection
- **Remove internal-only data** before customer delivery (or enable `customer_safe_mode`)
- DOCX output is a simplified version — tables and text, not full CSS styling
- If matplotlib is unavailable, fall back to CSS-only charts (see `references/chart-generation.md`)
- Peer comparison requires ≥ 5 peers to be statistically meaningful
- Engagement timeline queries may return 0 results for accounts not tracked in RAVEN

---

## Post-Execution: Persist Lessons

After delivering the final readout to the user:

1. **Analyze this session**: Review errors caught, corrections made, user feedback, patterns discovered
2. **Classify each learning**: error-fix, pattern, preference, gotcha, optimization
3. **Persist to CoCo memory**: Use `cortex memory remember "<lesson>" --rule` for critical learnings
4. **Report**: Summarize lessons learned to the user
