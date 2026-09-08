# Peer Comparison Reference

Compare the POC customer's consumption against similar Snowflake customers in the same industry and revenue range. This adds credibility to the readout by showing the customer where they stand relative to peers.

## When to Use

- Config menu: `peer_comparison: true`
- Requires: Salesforce Account ID and SNOWHOUSE connection
- Best for: Prospects evaluating Snowflake cost; RVP/executive audience

## Queries

### PC.1 Find Peer Accounts (Same Industry, Similar Revenue)

**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
WITH target AS (
    SELECT INDUSTRY, ANNUAL_REVENUE
    FROM FIVETRAN.SALESFORCE.ACCOUNT
    WHERE ID = '<salesforce_account_id>'
)
SELECT
    a.NAME AS PEER_ACCOUNT,
    a.INDUSTRY,
    a.ANNUAL_REVENUE,
    a.NUMBER_OF_EMPLOYEES,
    cu.TOTAL_CREDITS_ALL_TIME,
    cu.LATEST_ACTIVE_MONTH
FROM FIVETRAN.SALESFORCE.ACCOUNT a
JOIN (
    SELECT
        SALESFORCE_ACCOUNT_NAME,
        SUM(TOTAL_CREDITS) AS TOTAL_CREDITS_ALL_TIME,
        MAX(MONTH) AS LATEST_ACTIVE_MONTH
    FROM SALES.RAVEN.A360_CREDIT_USAGE_VIEW
    GROUP BY 1
) cu ON cu.SALESFORCE_ACCOUNT_NAME = a.NAME
CROSS JOIN target t
WHERE a.INDUSTRY = t.INDUSTRY
  AND a.ANNUAL_REVENUE BETWEEN t.ANNUAL_REVENUE * 0.5 AND t.ANNUAL_REVENUE * 2.0
  AND a.IS_DELETED = FALSE
  AND a.TYPE = 'Customer'
  AND cu.TOTAL_CREDITS_ALL_TIME > 100
ORDER BY ABS(a.ANNUAL_REVENUE - t.ANNUAL_REVENUE)
LIMIT 10;
```

### PC.2 Peer Credit Consumption Distribution

**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY TOTAL_CREDITS) AS P25_CREDITS,
    PERCENTILE_CONT(0.50) WITHIN GROUP (ORDER BY TOTAL_CREDITS) AS MEDIAN_CREDITS,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY TOTAL_CREDITS) AS P75_CREDITS,
    AVG(TOTAL_CREDITS) AS AVG_CREDITS,
    COUNT(*) AS PEER_COUNT
FROM (
    SELECT SUM(TOTAL_CREDITS) AS TOTAL_CREDITS
    FROM SALES.RAVEN.A360_CREDIT_USAGE_VIEW
    WHERE SALESFORCE_ACCOUNT_NAME IN (<peer_account_names_list>)
    GROUP BY SALESFORCE_ACCOUNT_NAME
);
```

### PC.3 Peer Feature Adoption

**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    PRODUCT_CATEGORY,
    COUNT(DISTINCT SALESFORCE_ACCOUNT_NAME) AS ACCOUNTS_USING,
    ROUND(COUNT(DISTINCT SALESFORCE_ACCOUNT_NAME) * 100.0 / <peer_count>, 1) AS PCT_ADOPTION
FROM SALES.RAVEN.A360_DAILY_ACCOUNT_PRODUCT_CATEGORY_REVENUE_VIEW
WHERE SALESFORCE_ACCOUNT_NAME IN (<peer_account_names_list>)
  AND REVENUE > 0
GROUP BY 1
ORDER BY PCT_ADOPTION DESC;
```

## HTML Component

Use inside Section 4 (Cost/Benefit) or as a standalone Section 4.5.

```html
<div class="callout callout-consideration">
  <div class="callout-title">
    <span class="callout-icon">&#128200;</span> Peer Benchmark
  </div>
  <div class="callout-content">
    Compared against <strong>{{PEER_COUNT}}</strong> similar {{INDUSTRY}} companies
    with annual revenue between ${{REV_LOW}}M and ${{REV_HIGH}}M.
  </div>
</div>

<div class="metric-grid">
  <div class="metric-card">
    <h4>Your POC Credits</h4>
    <div class="metric-value">{{TOTAL_CREDITS}}</div>
    <div class="metric-desc">During {{POC_DURATION}} day POC</div>
  </div>
  <div class="metric-card green">
    <h4>Peer Median (Annual)</h4>
    <div class="metric-value">{{MEDIAN_CREDITS}}</div>
    <div class="metric-desc">50th percentile of peers</div>
  </div>
  <div class="metric-card orange">
    <h4>Peer P75 (Annual)</h4>
    <div class="metric-value">{{P75_CREDITS}}</div>
    <div class="metric-desc">75th percentile of peers</div>
  </div>
</div>

<h3 style="font-family:'Texta',sans-serif; font-weight:800; color:var(--sf-mid-blue); margin:24px 0 12px;">Feature Adoption Among Peers</h3>
<table class="data-table">
  <thead>
    <tr>
      <th>Feature Category</th>
      <th>Peers Using</th>
      <th>Adoption Rate</th>
    </tr>
  </thead>
  <tbody>
    {{PEER_FEATURE_ROWS}}
  </tbody>
</table>
```

## Anti-Patterns

- **DO NOT** show peer account names to the customer — only show anonymized aggregates
- **DO NOT** compare a 2-week POC to annual peer consumption without normalizing (divide peer by 26 for bi-weekly comparison)
- **DO NOT** include peers with < 100 total credits (noise from inactive accounts)
- **DO NOT** use if fewer than 5 peers found — the comparison is not statistically meaningful
- If industry or revenue data is missing from Salesforce, skip peer comparison entirely
