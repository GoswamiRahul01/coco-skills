# POC Readout Component Library

Reusable HTML/CSS patterns for POC readout documents. All components use the Snowflake brand CSS custom properties defined in the readout template.

**Typography:** Headings use `Texta` (from Snowflake CDN, weights 400/800/900). Body uses `Lato` at 16px.

**Key CSS Variables:**

| Variable | Value | Usage |
|----------|-------|-------|
| `--sf-blue` | `#29B5E8` | Brand accent |
| `--sf-mid-blue` | `#11567F` | Primary accent, step badges, table headers |
| `--sf-dark-bg` | `#212C36` | Header background |
| `--sf-body-text` | `#333333` | Body text |
| `--sf-border` | `#E0E7EF` | Card borders |
| `--sf-light` | `#F4F7FA` | Table header backgrounds |
| `--sf-green` | `#00C9A7` | Success, compliance |
| `--sf-orange` | `#FF9F36` | Warning |
| `--sf-red` | `#E74C3C` | Error, alert |
| `--sf-gray` | `#8A999E` | Muted text, labels |

---

## 1. KPI Card

Single metric card showing a value, label, and detail line. Use within a KPI Grid (Component 2).

**Variants by icon color:**
- Credits: `var(--sf-blue)` / `#29B5E8`
- Rows: `var(--sf-green)` / `#00C9A7`
- Objects: `#7D44CF` (purple)
- Storage: `var(--sf-orange)` / `#FF9F36`

```html
<div class="kpi-card">
  <div class="kpi-icon" style="background: var(--sf-blue);">
    <!-- SVG or emoji icon -->
    &#9889;
  </div>
  <div class="kpi-value">1,247</div>
  <div class="kpi-label">Credits Consumed</div>
  <div class="kpi-detail">$3,741 estimated cost</div>
</div>
```

```css
.kpi-card {
  background: #fff;
  border: 1px solid var(--sf-border);
  border-radius: 12px;
  padding: 24px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
}
.kpi-icon {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto 12px;
  font-size: 22px;
  color: #fff;
}
.kpi-value {
  font-family: 'Texta', sans-serif;
  font-weight: 900;
  font-size: 32px;
  color: var(--sf-mid-blue);
  line-height: 1.1;
}
.kpi-label {
  font-size: 14px;
  color: var(--sf-gray);
  margin-top: 4px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}
.kpi-detail {
  font-size: 13px;
  color: var(--sf-body-text);
  margin-top: 8px;
  opacity: 0.7;
}
```

---

## 2. KPI Grid

Container for KPI cards. 4 columns on desktop, 2 on smaller screens.

```html
<div class="kpi-grid">
  <div class="kpi-card"><!-- Credits card --></div>
  <div class="kpi-card"><!-- Rows card --></div>
  <div class="kpi-card"><!-- Objects card --></div>
  <div class="kpi-card"><!-- Storage card --></div>
</div>
```

```css
.kpi-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
  margin: 24px 0;
}
@media (max-width: 900px) {
  .kpi-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
```

---

## 3. Criteria Card

White card for success criteria checklists with a compliance toggle.

**Bilingual note:** Use `COMPLIANT` for English readouts, `CUMPLE` for Spanish readouts.

```html
<div class="criteria-card">
  <div class="criteria-header">
    <span class="criteria-icon">&#9989;</span>
    <h3>Data Loading & Ingestion</h3>
    <span class="compliance-badge compliant">COMPLIANT</span>
  </div>
  <ul class="criteria-list">
    <li class="criteria-item checked">Load 10M+ rows in under 60 seconds</li>
    <li class="criteria-item checked">Support CSV, Parquet, and JSON formats</li>
    <li class="criteria-item checked">Automated schema detection on load</li>
  </ul>
</div>
```

```css
.criteria-card {
  background: #fff;
  border: 1px solid var(--sf-border);
  border-radius: 12px;
  padding: 24px;
  margin-bottom: 16px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
}
.criteria-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 16px;
}
.criteria-header h3 {
  flex: 1;
  margin: 0;
  font-family: 'Texta', sans-serif;
  font-weight: 800;
  font-size: 18px;
  color: var(--sf-mid-blue);
}
.criteria-icon {
  font-size: 24px;
}
.compliance-badge {
  padding: 4px 14px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}
.compliance-badge.compliant {
  background: var(--sf-green);
  color: #fff;
}
.compliance-badge.non-compliant {
  background: var(--sf-red);
  color: #fff;
}
.compliance-badge.partial {
  background: var(--sf-orange);
  color: #fff;
}
.criteria-list {
  list-style: none;
  padding: 0;
  margin: 0;
}
.criteria-item {
  padding: 8px 0 8px 28px;
  position: relative;
  font-size: 15px;
  color: var(--sf-body-text);
  border-bottom: 1px solid var(--sf-border);
}
.criteria-item:last-child {
  border-bottom: none;
}
.criteria-item.checked::before {
  content: "\2713";
  position: absolute;
  left: 0;
  color: var(--sf-green);
  font-weight: 700;
  font-size: 16px;
}
.criteria-item.unchecked::before {
  content: "\2717";
  position: absolute;
  left: 0;
  color: var(--sf-red);
  font-weight: 700;
  font-size: 16px;
}
```

---

## 4. Gantt Chart

CSS Grid-based timeline for POC activity visualization.

**Category colors:**
| Class | Color | Usage |
|-------|-------|-------|
| `cat-etl` | `#29B5E8` | Data loading / ETL |
| `cat-model` | `#00C9A7` | Data modeling |
| `cat-ml` | `#7D44CF` | ML / Cortex AI |
| `cat-sv` | `#FF9F36` | Semantic views |
| `cat-app` | `#E74C3C` | Streamlit apps |
| `cat-sec` | `#17a2b8` | Security / governance |
| `cat-audit` | `#8A999E` | Audit / review |

**Calculating grid columns:** Given a start date and end date, each day is one column. The total columns = `(end - start) + 1` days. Activity bars use `grid-column: <start-day> / <end-day + 1>`.

```html
<div class="gantt-chart" style="--gantt-cols: 14;">
  <!-- Date headers -->
  <div class="gantt-header" style="grid-column: 1 / 4;">Week 1</div>
  <div class="gantt-header" style="grid-column: 4 / 8;">Week 2</div>
  <div class="gantt-header" style="grid-column: 8 / 11;">Week 3</div>
  <div class="gantt-header" style="grid-column: 11 / 15;">Week 4</div>

  <!-- Activity rows -->
  <div class="gantt-label">Data Loading</div>
  <div class="gantt-bar cat-etl" style="grid-column: 1 / 5;">ETL Pipeline</div>

  <div class="gantt-label">Data Modeling</div>
  <div class="gantt-bar cat-model" style="grid-column: 3 / 8;">Dimensional Model</div>

  <div class="gantt-label">ML / AI</div>
  <div class="gantt-bar cat-ml" style="grid-column: 6 / 11;">Cortex Functions</div>

  <div class="gantt-label">Semantic Views</div>
  <div class="gantt-bar cat-sv" style="grid-column: 8 / 12;">Analyst Setup</div>

  <div class="gantt-label">App Development</div>
  <div class="gantt-bar cat-app" style="grid-column: 10 / 14;">Streamlit Dashboard</div>

  <!-- Legend -->
  <div class="gantt-legend" style="grid-column: 1 / -1;">
    <span class="legend-item"><span class="legend-dot cat-etl"></span>ETL</span>
    <span class="legend-item"><span class="legend-dot cat-model"></span>Modeling</span>
    <span class="legend-item"><span class="legend-dot cat-ml"></span>ML/AI</span>
    <span class="legend-item"><span class="legend-dot cat-sv"></span>Semantic</span>
    <span class="legend-item"><span class="legend-dot cat-app"></span>App</span>
    <span class="legend-item"><span class="legend-dot cat-sec"></span>Security</span>
    <span class="legend-item"><span class="legend-dot cat-audit"></span>Audit</span>
  </div>
</div>
```

```css
.gantt-chart {
  display: grid;
  grid-template-columns: 140px repeat(var(--gantt-cols), 1fr);
  gap: 4px 0;
  background: #fff;
  border: 1px solid var(--sf-border);
  border-radius: 12px;
  padding: 16px;
  margin: 24px 0;
  overflow-x: auto;
}
.gantt-header {
  background: var(--sf-light);
  color: var(--sf-gray);
  font-size: 12px;
  font-weight: 700;
  text-align: center;
  padding: 8px 4px;
  border-radius: 4px;
}
.gantt-label {
  font-size: 13px;
  font-weight: 600;
  color: var(--sf-body-text);
  padding: 8px 8px 8px 0;
  display: flex;
  align-items: center;
}
.gantt-bar {
  border-radius: 6px;
  color: #fff;
  font-size: 12px;
  font-weight: 600;
  padding: 8px 12px;
  display: flex;
  align-items: center;
  white-space: nowrap;
  min-height: 32px;
}
.cat-etl { background: #29B5E8; }
.cat-model { background: #00C9A7; }
.cat-ml { background: #7D44CF; }
.cat-sv { background: #FF9F36; }
.cat-app { background: #E74C3C; }
.cat-sec { background: #17a2b8; }
.cat-audit { background: #8A999E; }

.gantt-legend {
  display: flex;
  gap: 16px;
  padding: 12px 0 0;
  border-top: 1px solid var(--sf-border);
  margin-top: 8px;
  flex-wrap: wrap;
}
.legend-item {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 12px;
  color: var(--sf-gray);
}
.legend-dot {
  width: 12px;
  height: 12px;
  border-radius: 3px;
  display: inline-block;
}
```

---

## 5. Architecture Diagram

4-column CSS grid showing data flow: Source -> ETL -> Snowflake -> Consumers.

**Dynamically populate:** Query the object inventory and group objects by schema/type to fill each column. The Snowflake column should contain sub-zones matching actual schemas (RAW, ANALYTICS, etc.).

```html
<div class="arch-diagram">
  <div class="arch-col">
    <div class="arch-col-title">Source Systems</div>
    <div class="arch-item">SAP ERP</div>
    <div class="arch-item">Salesforce</div>
    <div class="arch-item">CSV/Excel Files</div>
  </div>

  <div class="arch-arrow">&#10145;</div>

  <div class="arch-col">
    <div class="arch-col-title">Ingestion</div>
    <div class="arch-item">Snowpipe</div>
    <div class="arch-item">External Stages</div>
    <div class="arch-item">COPY INTO</div>
  </div>

  <div class="arch-arrow">&#10145;</div>

  <div class="arch-col arch-snowflake">
    <div class="arch-col-title">Snowflake</div>
    <div class="arch-zone">
      <div class="arch-zone-label">RAW</div>
      <div class="arch-item small">Raw Tables (12)</div>
    </div>
    <div class="arch-zone">
      <div class="arch-zone-label">ANALYTICS</div>
      <div class="arch-item small">Views (8)</div>
      <div class="arch-item small">Dynamic Tables (3)</div>
    </div>
    <div class="arch-zone">
      <div class="arch-zone-label">Security</div>
      <div class="arch-item small">Masking Policies</div>
      <div class="arch-item small">Row Access</div>
    </div>
    <div class="arch-zone">
      <div class="arch-zone-label">Governance</div>
      <div class="arch-item small">Tags, Classification</div>
    </div>
    <div class="arch-zone">
      <div class="arch-zone-label">Agent</div>
      <div class="arch-item small">Cortex Agent</div>
      <div class="arch-item small">Semantic View</div>
    </div>
    <div class="arch-zone">
      <div class="arch-zone-label">App</div>
      <div class="arch-item small">Streamlit Dashboard</div>
    </div>
  </div>

  <div class="arch-arrow">&#10145;</div>

  <div class="arch-col">
    <div class="arch-col-title">Consumers</div>
    <div class="arch-item">Snowflake Intelligence</div>
    <div class="arch-item">Streamlit App</div>
    <div class="arch-item">Power BI / Tableau</div>
  </div>
</div>
```

```css
.arch-diagram {
  display: grid;
  grid-template-columns: 1fr auto 1fr auto 2fr auto 1fr;
  gap: 12px;
  align-items: start;
  background: #fff;
  border: 1px solid var(--sf-border);
  border-radius: 12px;
  padding: 24px;
  margin: 24px 0;
  overflow-x: auto;
}
.arch-col {
  display: flex;
  flex-direction: column;
  gap: 8px;
}
.arch-col-title {
  font-family: 'Texta', sans-serif;
  font-weight: 800;
  font-size: 14px;
  color: var(--sf-mid-blue);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  padding-bottom: 8px;
  border-bottom: 2px solid var(--sf-blue);
  text-align: center;
}
.arch-item {
  background: var(--sf-light);
  border: 1px solid var(--sf-border);
  border-radius: 8px;
  padding: 10px 14px;
  font-size: 13px;
  color: var(--sf-body-text);
  text-align: center;
}
.arch-item.small {
  padding: 6px 10px;
  font-size: 12px;
}
.arch-arrow {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  color: var(--sf-blue);
  padding-top: 30px;
}
.arch-snowflake {
  background: linear-gradient(135deg, #f0f8ff 0%, #e8f4fd 100%);
  border: 2px solid var(--sf-blue);
  border-radius: 12px;
  padding: 16px;
}
.arch-zone {
  border: 1px dashed var(--sf-border);
  border-radius: 8px;
  padding: 10px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.arch-zone-label {
  font-size: 11px;
  font-weight: 700;
  color: var(--sf-mid-blue);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 4px;
}
```

---

## 6. Object Inventory Table

Sortable table showing all Snowflake objects created during the POC.

**Type badge classes:** `badge-table`, `badge-view`, `badge-dt` (Dynamic Table), `badge-stream`, `badge-task`, `badge-function`, `badge-procedure`, `badge-stage`.

```html
<table class="inventory-table">
  <thead>
    <tr>
      <th>Schema</th>
      <th>Object Name</th>
      <th>Type</th>
      <th>Row Count</th>
      <th>Detail</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>RAW</td>
      <td><code>SALES_TRANSACTIONS</code></td>
      <td><span class="type-badge badge-table">TABLE</span></td>
      <td>2,450,000</td>
      <td>Daily sales data from SAP</td>
    </tr>
    <tr>
      <td>ANALYTICS</td>
      <td><code>V_SALES_SUMMARY</code></td>
      <td><span class="type-badge badge-view">VIEW</span></td>
      <td>--</td>
      <td>Aggregated monthly summary</td>
    </tr>
    <tr>
      <td>ANALYTICS</td>
      <td><code>DT_DAILY_METRICS</code></td>
      <td><span class="type-badge badge-dt">DYNAMIC TABLE</span></td>
      <td>365</td>
      <td>Auto-refresh daily KPIs</td>
    </tr>
  </tbody>
</table>
```

```css
.inventory-table {
  width: 100%;
  border-collapse: collapse;
  margin: 24px 0;
  font-size: 14px;
}
.inventory-table thead th {
  background: var(--sf-light);
  color: var(--sf-mid-blue);
  font-family: 'Texta', sans-serif;
  font-weight: 800;
  font-size: 13px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  padding: 12px 16px;
  text-align: left;
  border-bottom: 2px solid var(--sf-border);
}
.inventory-table tbody tr:nth-child(even) {
  background: var(--sf-light);
}
.inventory-table tbody td {
  padding: 10px 16px;
  border-bottom: 1px solid var(--sf-border);
  color: var(--sf-body-text);
}
.inventory-table code {
  background: #eef3f8;
  padding: 2px 6px;
  border-radius: 4px;
  font-size: 13px;
}
.type-badge {
  display: inline-block;
  padding: 3px 10px;
  border-radius: 12px;
  font-size: 11px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.3px;
  color: #fff;
}
.badge-table { background: var(--sf-mid-blue); }
.badge-view { background: var(--sf-blue); }
.badge-dt { background: var(--sf-green); }
.badge-stream { background: #17a2b8; }
.badge-task { background: #7D44CF; }
.badge-function { background: var(--sf-orange); }
.badge-procedure { background: var(--sf-red); }
.badge-stage { background: var(--sf-gray); }
```

---

## 7. Credit Breakdown Table

Two variants for displaying credit consumption.

### Variant A: By Warehouse

```html
<table class="credit-table">
  <thead>
    <tr>
      <th>Warehouse</th>
      <th>Credits</th>
      <th>% of Total</th>
      <th>Est. Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>POC_ETL_WH</td>
      <td>487.2</td>
      <td>39%</td>
      <td>$1,461.60</td>
    </tr>
    <tr>
      <td>POC_ANALYTICS_WH</td>
      <td>412.5</td>
      <td>33%</td>
      <td>$1,237.50</td>
    </tr>
    <tr>
      <td>POC_ML_WH</td>
      <td>347.3</td>
      <td>28%</td>
      <td>$1,041.90</td>
    </tr>
    <tr class="total-row">
      <td>Total</td>
      <td>1,247.0</td>
      <td>100%</td>
      <td>$3,741.00</td>
    </tr>
  </tbody>
</table>
```

### Variant B: By Day

```html
<table class="credit-table">
  <thead>
    <tr>
      <th>Date</th>
      <th>Credits</th>
      <th>Cumulative</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2025-01-06</td>
      <td>89.3</td>
      <td>89.3</td>
    </tr>
    <tr>
      <td>2025-01-07</td>
      <td>124.7</td>
      <td>214.0</td>
    </tr>
    <tr class="total-row">
      <td>Total</td>
      <td>1,247.0</td>
      <td>--</td>
    </tr>
  </tbody>
</table>
```

```css
.credit-table {
  width: 100%;
  border-collapse: collapse;
  margin: 24px 0;
  font-size: 14px;
}
.credit-table thead th {
  background: var(--sf-mid-blue);
  color: #fff;
  font-family: 'Texta', sans-serif;
  font-weight: 800;
  font-size: 13px;
  text-transform: uppercase;
  padding: 12px 16px;
  text-align: left;
}
.credit-table tbody td {
  padding: 10px 16px;
  border-bottom: 1px solid var(--sf-border);
  color: var(--sf-body-text);
}
.credit-table tbody tr:nth-child(even) {
  background: var(--sf-light);
}
.credit-table .total-row {
  background: var(--sf-light) !important;
  font-weight: 700;
}
.credit-table .total-row td {
  border-top: 2px solid var(--sf-mid-blue);
  font-family: 'Texta', sans-serif;
  font-weight: 800;
  color: var(--sf-mid-blue);
}
```

---

## 8. Success Metric Card

4-card grid displaying qualitative assessments.

```html
<div class="kpi-grid">
  <div class="metric-card">
    <div class="metric-icon">&#9889;</div>
    <div class="metric-title">Load Time</div>
    <div class="metric-rating">
      <span class="star filled">&#9733;</span>
      <span class="star filled">&#9733;</span>
      <span class="star filled">&#9733;</span>
      <span class="star filled">&#9733;</span>
      <span class="star filled">&#9733;</span>
    </div>
    <div class="metric-desc">10M rows loaded in 42 seconds via Snowpipe</div>
  </div>

  <div class="metric-card">
    <div class="metric-icon">&#128200;</div>
    <div class="metric-title">Query Performance</div>
    <div class="metric-rating">
      <span class="star filled">&#9733;</span>
      <span class="star filled">&#9733;</span>
      <span class="star filled">&#9733;</span>
      <span class="star filled">&#9733;</span>
      <span class="star">&#9733;</span>
    </div>
    <div class="metric-desc">Avg query time 1.2s on XS warehouse</div>
  </div>
</div>
```

```css
.metric-card {
  background: #fff;
  border: 1px solid var(--sf-border);
  border-radius: 12px;
  padding: 24px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
}
.metric-icon {
  font-size: 32px;
  margin-bottom: 8px;
}
.metric-title {
  font-family: 'Texta', sans-serif;
  font-weight: 800;
  font-size: 16px;
  color: var(--sf-mid-blue);
  margin-bottom: 8px;
}
.metric-rating {
  margin-bottom: 12px;
}
.star {
  font-size: 20px;
  color: #ddd;
}
.star.filled {
  color: var(--sf-orange);
}
.metric-desc {
  font-size: 13px;
  color: var(--sf-gray);
  line-height: 1.4;
}
```

---

## 9. Next Steps Card

3-column grid for post-POC recommendations.

```html
<div class="next-steps-grid">
  <div class="next-step-card">
    <div class="next-step-icon">&#128218;</div>
    <h4 class="next-step-title">Production Migration</h4>
    <p class="next-step-desc">Move validated pipelines to production with CI/CD integration and monitoring.</p>
    <a class="next-step-link" href="#">Migration Guide &rarr;</a>
  </div>

  <div class="next-step-card">
    <div class="next-step-icon">&#128272;</div>
    <h4 class="next-step-title">Security Hardening</h4>
    <p class="next-step-desc">Implement network policies, MFA enforcement, and key-pair authentication.</p>
    <a class="next-step-link" href="#">Security Best Practices &rarr;</a>
  </div>

  <div class="next-step-card">
    <div class="next-step-icon">&#128640;</div>
    <h4 class="next-step-title">Expand Use Cases</h4>
    <p class="next-step-desc">Leverage Cortex AI for predictive analytics and anomaly detection.</p>
    <a class="next-step-link" href="#">Cortex AI Docs &rarr;</a>
  </div>
</div>
```

```css
.next-steps-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  margin: 24px 0;
}
.next-step-card {
  background: #fff;
  border: 1px solid var(--sf-border);
  border-radius: 12px;
  padding: 24px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0,0,0,0.04);
  transition: box-shadow 0.2s;
}
.next-step-card:hover {
  box-shadow: 0 4px 16px rgba(0,0,0,0.08);
}
.next-step-icon {
  font-size: 36px;
  margin-bottom: 12px;
}
.next-step-title {
  font-family: 'Texta', sans-serif;
  font-weight: 800;
  font-size: 16px;
  color: var(--sf-mid-blue);
  margin: 0 0 8px;
}
.next-step-desc {
  font-size: 14px;
  color: var(--sf-body-text);
  line-height: 1.5;
  margin: 0 0 12px;
}
.next-step-link {
  font-size: 13px;
  font-weight: 700;
  color: var(--sf-blue);
  text-decoration: none;
}
.next-step-link:hover {
  text-decoration: underline;
}
```

---

## 10. Governance Evidence

Image embeds for screenshots with captions, arranged in a grid.

```html
<!-- Single image -->
<div class="evidence-item">
  <img src="evidence/masking-policy.png" alt="Masking policy applied" class="evidence-img">
  <p class="evidence-caption">Figure 1: Dynamic masking policy applied to PII columns</p>
</div>

<!-- Grid of images (2 columns) -->
<div class="evidence-grid cols-2">
  <div class="evidence-item">
    <img src="evidence/row-access.png" alt="Row access policy" class="evidence-img">
    <p class="evidence-caption">Row access policy configuration</p>
  </div>
  <div class="evidence-item">
    <img src="evidence/tags.png" alt="Object tagging" class="evidence-img">
    <p class="evidence-caption">Sensitive data classification tags</p>
  </div>
</div>

<!-- Grid of images (3 columns) -->
<div class="evidence-grid cols-3">
  <!-- ... items ... -->
</div>
```

```css
.evidence-grid {
  display: grid;
  gap: 20px;
  margin: 24px 0;
}
.evidence-grid.cols-2 {
  grid-template-columns: repeat(2, 1fr);
}
.evidence-grid.cols-3 {
  grid-template-columns: repeat(3, 1fr);
}
.evidence-item {
  text-align: center;
}
.evidence-img {
  width: 100%;
  max-width: 100%;
  border: 1px solid var(--sf-border);
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
}
.evidence-caption {
  font-size: 13px;
  color: var(--sf-gray);
  margin-top: 8px;
  font-style: italic;
}
```

---

## 11. Section Header

Numbered section headings with badge and divider.

```html
<div class="section-header">
  <span class="section-num">1</span>
  <h2>EXECUTIVE SUMMARY</h2>
</div>
```

```css
.section-header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin: 40px 0 20px;
  padding-bottom: 12px;
  border-bottom: 2px solid var(--sf-border);
}
.section-num {
  background: var(--sf-mid-blue);
  color: #fff;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Texta', sans-serif;
  font-weight: 900;
  font-size: 16px;
  flex-shrink: 0;
}
.section-header h2 {
  margin: 0;
  font-family: 'Texta', sans-serif;
  font-weight: 900;
  font-size: 22px;
  color: var(--sf-mid-blue);
  text-transform: uppercase;
  letter-spacing: 1px;
}
```

---

## 12. Badges

Inline status badges for various states.

```html
<span class="badge badge-ok">OK</span>
<span class="badge badge-warning">WARNING</span>
<span class="badge badge-info">INFO</span>
<span class="badge badge-error">ERROR</span>
```

```css
.badge {
  display: inline-block;
  padding: 3px 10px;
  border-radius: 12px;
  font-size: 11px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.3px;
  color: #fff;
  vertical-align: middle;
}
.badge-ok { background: var(--sf-green); }
.badge-warning { background: var(--sf-orange); }
.badge-info { background: var(--sf-blue); }
.badge-error { background: var(--sf-red); }
```

---

## 13. Section Divider

Full-width decorative divider using the Snowflake linebreak image.

```html
<div class="section-divider">
  <img src="snowflake-linebreak.webp" alt="" class="divider-img">
</div>
```

```css
.section-divider {
  text-align: center;
  margin: 40px 0;
}
.divider-img {
  width: 100%;
  max-width: 600px;
  height: auto;
  opacity: 0.7;
}
```

**Fallback** if the image is not available:

```css
.section-divider-line {
  border: none;
  height: 2px;
  background: linear-gradient(90deg, transparent, var(--sf-blue), transparent);
  margin: 40px auto;
  max-width: 600px;
}
```

---

## 14. Callout Boxes

Three types for highlighting important information.

```html
<!-- Consideration (blue) -->
<div class="callout callout-consideration">
  <div class="callout-title">
    <span class="callout-icon">&#128161;</span> Consideration
  </div>
  <div class="callout-content">
    Consider using a dedicated warehouse for ETL workloads to isolate resource consumption.
  </div>
</div>

<!-- Important (orange) -->
<div class="callout callout-important">
  <div class="callout-title">
    <span class="callout-icon">&#9888;</span> Important
  </div>
  <div class="callout-content">
    Network policies must be configured before production deployment.
  </div>
</div>

<!-- Success (green) -->
<div class="callout callout-success">
  <div class="callout-title">
    <span class="callout-icon">&#9989;</span> Success
  </div>
  <div class="callout-content">
    All data loading tests passed within the defined SLA of 60 seconds.
  </div>
</div>
```

```css
.callout {
  border-radius: 8px;
  padding: 16px 20px;
  margin: 20px 0;
  border-left: 4px solid;
}
.callout-consideration {
  border-left-color: var(--sf-blue);
  background: rgba(41, 181, 232, 0.06);
}
.callout-important {
  border-left-color: var(--sf-orange);
  background: rgba(255, 159, 54, 0.06);
}
.callout-success {
  border-left-color: var(--sf-green);
  background: rgba(0, 201, 167, 0.06);
}
.callout-title {
  font-family: 'Texta', sans-serif;
  font-weight: 800;
  font-size: 15px;
  margin-bottom: 8px;
}
.callout-consideration .callout-title { color: var(--sf-mid-blue); }
.callout-important .callout-title { color: var(--sf-orange); }
.callout-success .callout-title { color: var(--sf-green); }
.callout-icon {
  margin-right: 6px;
}
.callout-content {
  font-size: 14px;
  color: var(--sf-body-text);
  line-height: 1.5;
}
```

---

## 15. Evidence Screenshot

Standalone screenshot embed with optional caption. Use this for individual evidence items outside a grid.

```html
<figure class="screenshot">
  <img src="evidence/query-performance.png" alt="Query performance results" class="screenshot-img">
  <figcaption class="screenshot-caption">Query execution time: 1.2s average on XS warehouse</figcaption>
</figure>
```

```css
.screenshot {
  margin: 24px 0;
  text-align: center;
}
.screenshot-img {
  max-width: 100%;
  width: auto;
  border: 1px solid var(--sf-border);
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.08);
}
.screenshot-caption {
  font-size: 13px;
  color: var(--sf-gray);
  margin-top: 10px;
  font-style: italic;
}
```

---

## 16. Wins / Risks / Ask Grid

3-column executive summary grid. Populates from POC findings and user input.

**When to use:** Config `wins_risks_ask: true`. Best for executive-facing readouts.

```html
<div class="wra-grid">
  <div class="wra-card wins">
    <h3>Wins</h3>
    <ul>
      <li>Loaded 10M rows in 42 seconds (target: 60s)</li>
      <li>Cortex Agent answers 92% of test questions correctly</li>
      <li>Zero data quality issues in DMF monitoring</li>
    </ul>
  </div>
  <div class="wra-card risks">
    <h3>Risks</h3>
    <ul>
      <li>Network policies not yet configured</li>
      <li>MFA not enforced for service accounts</li>
    </ul>
  </div>
  <div class="wra-card ask">
    <h3>Next Actions</h3>
    <ul>
      <li>Schedule production migration planning session</li>
      <li>Finalize contract terms with Snowflake AE</li>
      <li>Define production SLA requirements</li>
    </ul>
  </div>
</div>
```

**Population rules:**
- **Wins**: Auto-detect from success criteria met, performance exceeding targets, governance implemented
- **Risks**: Auto-detect from missing governance, spilling queries, unused warehouses
- **Next Actions**: User-provided or inferred from use cases and open opportunities

---

## 17. POC Health Score

Visual gauge + breakdown bars showing quantitative POC assessment.

**When to use:** Config `health_score: true`.

**Score calculation:**
| Dimension | Weight | Scoring |
|-----------|--------|---------|
| Criteria Met | 30% | `(criteria_passed / criteria_total) × 100` |
| Feature Coverage | 25% | `(features_used / features_available) × 100` |
| Performance | 20% | 100 if no spilling + avg query < 5s; deduct 20 per spill category |
| Governance | 15% | `(governance_features / 4) × 100` (tags, masking, RAP, DMF) |
| Engagement | 10% | 100 if ≥ 3 meetings; 50 if 1-2; 0 if none |

**Total:** Weighted sum, capped at 100.

```html
<div class="health-score-container">
  <div class="health-gauge">
    <svg viewBox="0 0 120 120">
      <!-- Background arc -->
      <circle cx="60" cy="60" r="50" fill="none" stroke="#E0E7EF" stroke-width="12"
              stroke-dasharray="235.6 78.5" stroke-linecap="round" transform="rotate(135 60 60)"/>
      <!-- Score arc (adjust stroke-dasharray for score) -->
      <circle cx="60" cy="60" r="50" fill="none" stroke="#00C9A7" stroke-width="12"
              stroke-dasharray="188.5 125.7" stroke-linecap="round" transform="rotate(135 60 60)"/>
    </svg>
    <div class="score-text">82</div>
    <div class="score-label">Health</div>
  </div>
  <div class="health-breakdown">
    <h3>Score Breakdown</h3>
    <div class="health-bar-row">
      <span class="health-bar-label">Criteria Met</span>
      <div class="health-bar-track"><div class="health-bar-fill green" style="width:90%"></div></div>
      <span class="health-bar-value">90</span>
    </div>
    <div class="health-bar-row">
      <span class="health-bar-label">Feature Coverage</span>
      <div class="health-bar-track"><div class="health-bar-fill blue" style="width:75%"></div></div>
      <span class="health-bar-value">75</span>
    </div>
    <div class="health-bar-row">
      <span class="health-bar-label">Performance</span>
      <div class="health-bar-track"><div class="health-bar-fill green" style="width:85%"></div></div>
      <span class="health-bar-value">85</span>
    </div>
    <div class="health-bar-row">
      <span class="health-bar-label">Governance</span>
      <div class="health-bar-track"><div class="health-bar-fill orange" style="width:50%"></div></div>
      <span class="health-bar-value">50</span>
    </div>
    <div class="health-bar-row">
      <span class="health-bar-label">Engagement</span>
      <div class="health-bar-track"><div class="health-bar-fill green" style="width:100%"></div></div>
      <span class="health-bar-value">100</span>
    </div>
  </div>
</div>
```

**SVG arc calculation:**
- Full arc (270°): `stroke-dasharray: 235.6 78.5` (circumference × 0.75, circumference × 0.25)
- Score arc: `stroke-dasharray: <235.6 × score/100> <314.16 - (235.6 × score/100)>`
- Color: green (`#00C9A7`) if ≥ 75, orange (`#FF9F36`) if ≥ 50, red (`#E74C3C`) if < 50

---

## Usage Notes

- All components are designed to work inside the `<div class="container">` wrapper of the readout template.
- Colors reference CSS custom properties defined in the template's `:root` block. Do not hardcode colors unless the variable is unavailable.
- For print/PDF output, the template includes `@media print` rules that adjust margins and hide interactive elements.
- The Gantt chart's `--gantt-cols` CSS variable must match the total number of day columns in the date range.
- Architecture diagram columns can be adjusted by changing the `grid-template-columns` value to accommodate fewer or more pipeline stages.
