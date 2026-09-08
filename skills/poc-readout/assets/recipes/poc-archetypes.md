# POC Archetype Recipes

Pre-configured section priorities and feature toggles for common POC types. Select one recipe at the start, then customize.

## How to Use

1. User tells you the POC type (or you infer from object inventory)
2. Load the matching recipe below
3. Apply as defaults to the Config Menu
4. User can override any setting

---

## Recipe: Analytics-Only

**Trigger:** POC focused on data loading, modeling, querying, and BI integration. No AI/ML, no governance, no apps.

```yaml
sections:
  executive_summary: true
  execution_timeline: true
  usability: true
  cost_benefit: true
  ease_of_use: true
  success_metrics: true
  security_governance: false    # likely empty
  ml_ai: false                  # not applicable
  data_app: false               # no Streamlit
  object_inventory: true
  next_steps: true
  architecture: true

features:
  engagement_timeline: false
  peer_comparison: false
  chart_generation: true        # credit trend is valuable
  health_score: true
  wins_risks_ask: true
  multi_account: false
  docx_output: false
  customer_safe_mode: true

emphasis:
  - Query performance (Ease of Use section) — show avg/median/p95 query times
  - Data loading speed — highlight COPY INTO / Snowpipe throughput
  - Cost efficiency — credits per query, credits per TB loaded
  - Warehouse sizing — show right-sizing recommendations in Next Steps
```

---

## Recipe: AI/ML Platform

**Trigger:** POC includes Cortex AI functions, Cortex Search, Cortex Agents, Semantic Views, ML models, or Snowpark ML.

```yaml
sections:
  executive_summary: true
  execution_timeline: true
  usability: true
  cost_benefit: true
  ease_of_use: true
  success_metrics: true
  security_governance: true     # AI governance matters
  ml_ai: true                   # PRIMARY section
  data_app: true                # likely has Streamlit
  object_inventory: true
  next_steps: true
  architecture: true

features:
  engagement_timeline: true
  peer_comparison: true         # show AI adoption vs peers
  chart_generation: true
  health_score: true
  wins_risks_ask: true
  multi_account: false
  docx_output: false
  customer_safe_mode: true

emphasis:
  - ML/AI section gets EXPANDED: Cortex functions used, AI credit breakdown, model accuracy
  - Architecture diagram must show Agent/Semantic View/Search Service nodes
  - Cost section should break out AI_SERVICES_CREDITS from A360
  - Next Steps: production deployment of agents, fine-tuning, RAG pipeline optimization
```

---

## Recipe: Governance & Security

**Trigger:** POC focused on data classification, masking, row access policies, tags, lineage, data quality (DMFs).

```yaml
sections:
  executive_summary: true
  execution_timeline: true
  usability: true
  cost_benefit: true
  ease_of_use: false            # less relevant for governance POC
  success_metrics: true
  security_governance: true     # PRIMARY section — expand heavily
  ml_ai: false
  data_app: false
  object_inventory: true
  next_steps: true
  architecture: true

features:
  engagement_timeline: true
  peer_comparison: false
  chart_generation: false
  health_score: true
  wins_risks_ask: true
  multi_account: false
  docx_output: true             # executives often want Word docs
  customer_safe_mode: true

emphasis:
  - Governance section gets EXPANDED: classification tags, masking policies, RAP, DMFs, lineage
  - Evidence screenshots are CRITICAL — include all governance UI captures
  - Architecture diagram must show Horizon sub-section prominently
  - Success criteria focus on compliance requirements met
  - Next Steps: production rollout of policies, GDPR/CCPA compliance, Horizon Catalog
```

---

## Recipe: Full Platform (Kitchen Sink)

**Trigger:** Comprehensive POC covering data loading, analytics, AI/ML, governance, apps, and collaboration.

```yaml
sections:
  executive_summary: true
  execution_timeline: true
  usability: true
  cost_benefit: true
  ease_of_use: true
  success_metrics: true
  security_governance: true
  ml_ai: true
  data_app: true
  object_inventory: true
  next_steps: true
  architecture: true

features:
  engagement_timeline: true
  peer_comparison: true
  chart_generation: true
  health_score: true
  wins_risks_ask: true
  multi_account: false
  docx_output: false
  customer_safe_mode: true

emphasis:
  - All sections active — ensure none feel thin
  - Architecture diagram should be comprehensive (all 4 columns populated)
  - Next Steps organized by workstream (Data, AI, Governance, App)
  - Executive Summary should highlight breadth of capabilities proven
```

---

## Recipe: Migration Assessment

**Trigger:** POC comparing Snowflake against current platform (Redshift, BigQuery, Databricks, Teradata). Focus on performance benchmarks and cost comparison.

```yaml
sections:
  executive_summary: true
  execution_timeline: true
  usability: true
  cost_benefit: true            # PRIMARY section — detailed cost comparison
  ease_of_use: true             # PRIMARY section — performance benchmarks
  success_metrics: true
  security_governance: false
  ml_ai: false
  data_app: false
  object_inventory: true
  next_steps: true
  architecture: true

features:
  engagement_timeline: false
  peer_comparison: true         # show peer consumption for sizing
  chart_generation: true        # performance comparison charts
  health_score: true
  wins_risks_ask: true
  multi_account: false
  docx_output: true             # migration decisions need Word docs
  customer_safe_mode: true

emphasis:
  - Cost section must compare Snowflake credits vs. current platform cost
  - Performance section must show query-by-query benchmark results
  - Include a "Migration Complexity" assessment in Next Steps
  - Architecture diagram should show current-state vs. Snowflake target-state
```

---

## Recipe: Data Sharing / Marketplace

**Trigger:** POC focused on Secure Data Sharing, Private Listings, or Snowflake Marketplace.

```yaml
sections:
  executive_summary: true
  execution_timeline: true
  usability: true
  cost_benefit: true
  ease_of_use: false
  success_metrics: true
  security_governance: true     # sharing requires governance
  ml_ai: false
  data_app: false
  object_inventory: true
  next_steps: true
  architecture: true

features:
  engagement_timeline: false
  peer_comparison: false
  chart_generation: false
  health_score: true
  wins_risks_ask: true
  multi_account: true           # sharing involves multiple accounts
  docx_output: false
  customer_safe_mode: true

emphasis:
  - Architecture diagram must show Provider → Share → Consumer flow
  - Security section: sharing policies, reader accounts, access controls
  - Object inventory should highlight shares, listings, reader accounts
  - Next Steps: production sharing setup, Marketplace listing, monetization
```

---

## Auto-Detection Rules

If the user doesn't specify a recipe, infer from the object inventory:

| Objects Found | Inferred Recipe |
|---------------|----------------|
| Cortex Search + Agents + Semantic Views | AI/ML Platform |
| Tags + Masking Policies + RAP | Governance & Security |
| Streamlit + Cortex + Tags + Tables | Full Platform |
| Tables + Views only (no AI, no governance) | Analytics-Only |
| Shares + Reader Accounts | Data Sharing |
| Performance benchmarks mentioned by user | Migration Assessment |
