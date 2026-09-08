# Tone & Voice Guidelines / Guías de Tono y Voz

All text written into the readout document must follow these rules:

| Rule | Example (DO) | Example (DON'T) |
|------|-------------|-----------------|
| **Active voice** | "Snowflake loaded 10M rows in 42s" | "10M rows were loaded by the platform" |
| **Imperative verbs for next steps** | "Configure network policies" | "You should consider configuring..." |
| **No hedging** | "Query performance meets the SLA" | "Query performance seems to meet the SLA" |
| **Quantify everything** | "Avg query time: 1.2s on XS warehouse" | "Queries ran quickly" |
| **Customer-facing tone** | Professional, factual, confident | Casual, salesy, or overly promotional |
| **No filler words** | "simply", "just", "basically", "actually" — remove all | |
| **Bilingual consistency** | If Spanish: ALL text in Spanish, including KPI labels | Mix of "Resumen Ejecutivo" + "Credits consumed" |

## Persona-specific adjustments

| Persona | Adjustments |
|---------|------------|
| `executive` | Remove SQL code blocks, technical footnotes, spilling analysis. Keep: KPIs, Wins/Risks/Ask, Health Score, Next Steps. Max 8 pages. |
| `technical` | Full detail. Include SQL evidence, performance metrics, object inventory, architecture. No page limit. |
| `customer_safe` | Same as `technical` but strip: Salesforce IDs, SE comments, ACV figures, opportunity details, engagement hours. Replace SE names with "Snowflake Team". |
