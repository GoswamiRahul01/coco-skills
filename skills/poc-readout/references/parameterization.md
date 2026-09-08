# Parameterization / Parametrización

## Core Parameters (Always Required)

| Parameter | Token | Example |
|-----------|-------|---------|
| Customer name | `{{CUSTOMER_NAME}}` | Acme Corp |
| Customer logo | `{{CUSTOMER_LOGO}}` | acme-logo.png |
| POC description | `{{POC_DESCRIPTION}}` | Data Analytics & ML Platform |
| Start date | `{{START_DATE}}` | 2026-01-15 |
| End date | `{{END_DATE}}` | 2026-02-28 |
| Credit price | `{{CREDIT_PRICE}}` | 3.00 |
| Account ID | `{{ACCOUNT_ID}}` | ABC12345 |
| Region | `{{REGION}}` | AWS US West 2 |
| Year | `{{YEAR}}` | 2026 |
| Generated date | `{{GENERATED_DATE}}` | April 6, 2026 |

## KPI Parameters (From Queries)

| Parameter | Token | Source |
|-----------|-------|--------|
| Total credits | `{{TOTAL_CREDITS}}` | Query 2.1 |
| Total rows | `{{TOTAL_ROWS}}` | SUM(ROW_COUNT) from Query 4.1 |
| Total objects | `{{TOTAL_OBJECTS}}` | COUNT from Query 4.1 |
| Storage GB | `{{STORAGE_GB}}` | Query 3.1 |
| POC hours | `{{POC_HOURS}}` | Query 6.3 (continuous work hours via gap analysis) |
| POC hours detail | `{{POC_HOURS_DETAIL}}` | "Continuous work across N days (date range)" |

## Section Content Parameters (Dynamic)

| Parameter | Token | Config Required |
|-----------|-------|----------------|
| Scope paragraph | `{{SCOPE_PARAGRAPH}}` | always |
| Gantt start date | `{{GANTT_START}}` | always |
| Gantt end date | `{{GANTT_END}}` | always |
| Gantt rows | `{{GANTT_ROWS}}` | always |
| Usability: querying | `{{USABILITY_QUERYING}}` | section 3 enabled |
| Usability: modeling | `{{USABILITY_MODELING}}` | section 3 enabled |
| Wins/Risks/Ask | `{{WINS_RISKS_ASK}}` | `wins_risks_ask: true` |
| Health Score | `{{HEALTH_SCORE}}` | `health_score: true` |
| Engagement timeline | `{{ENGAGEMENT_TIMELINE}}` | `engagement_timeline: true` |
| Peer comparison | `{{PEER_COMPARISON}}` | `peer_comparison: true` |
| Credits by warehouse | `{{CREDITS_BY_WAREHOUSE}}` | always |
| Credits by day | `{{CREDITS_BY_DAY}}` | always |
| Ease of use table | `{{EASE_OF_USE_TABLE}}` | section 5 enabled |
| Success metrics | `{{SUCCESS_METRICS}}` | section 6 enabled |
| RAP content | `{{RAP_CONTENT}}` | section 7 enabled |
| Access control | `{{ACCESS_CONTROL}}` | section 7 enabled |
| Horizon section | `{{HORIZON_SECTION}}` | section 7 enabled |
| Governance images | `{{GOVERNANCE_IMAGES}}` | section 7 enabled |
| ML/AI table | `{{ML_AI_TABLE}}` | section 8 enabled |
| Data app table | `{{DATA_APP_TABLE}}` | section 9 enabled |
| Object inventory | `{{OBJECT_INVENTORY}}` | section 10 enabled |
| Next steps | `{{NEXT_STEPS}}` | always |
| Architecture diagram | `{{ARCHITECTURE_DIAGRAM}}` | section 12 enabled |
