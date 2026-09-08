# Engagement Timeline Reference

Show the customer-facing meeting cadence and SE investment during the POC. Demonstrates Snowflake's commitment and provides a narrative arc for the engagement.

## When to Use

- Config menu: `engagement_timeline: true`
- Requires: SNOWHOUSE connection + Salesforce Account ID
- Best for: Executive-facing readouts where meeting cadence matters

## Queries

### ET.1 All Engagements (Meetings, Calls, Emails)

**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    ENGAGEMENT_DATE,
    ENGAGEMENT_TYPE,
    ENGAGEMENT_TITLE,
    ATTENDEE_COUNT,
    DURATION_MINUTES,
    SOURCE
FROM SALES.RAVEN.ALL_ENGAGEMENTS_PREPED_VIEW
WHERE SALESFORCE_ACCOUNT_ID = '<salesforce_account_id>'
  AND ENGAGEMENT_DATE BETWEEN '<START_DATE>' AND '<END_DATE>'
ORDER BY ENGAGEMENT_DATE;
```

### ET.2 SE Activity Hours (Vivun)

**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    ACTIVITY_DATE,
    ACTIVITY_TYPE_C,
    HOURS_C,
    DESCRIPTION_C
FROM SALES.SE_REPORTING.VIVUN_HERO_ACTIVITIES
WHERE ACCOUNT_ID = '<salesforce_account_id>'
  AND ACTIVITY_DATE BETWEEN '<START_DATE>' AND '<END_DATE>'
ORDER BY ACTIVITY_DATE;
```

### ET.3 Engagement Summary Stats

**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    COUNT(*) AS TOTAL_ENGAGEMENTS,
    COUNT(DISTINCT ENGAGEMENT_DATE) AS ACTIVE_DAYS,
    SUM(DURATION_MINUTES) AS TOTAL_MINUTES,
    ROUND(SUM(DURATION_MINUTES) / 60.0, 1) AS TOTAL_HOURS,
    COUNT(CASE WHEN ENGAGEMENT_TYPE = 'Meeting' THEN 1 END) AS MEETINGS,
    COUNT(CASE WHEN ENGAGEMENT_TYPE = 'Call' THEN 1 END) AS CALLS,
    COUNT(CASE WHEN ENGAGEMENT_TYPE = 'Email' THEN 1 END) AS EMAILS
FROM SALES.RAVEN.ALL_ENGAGEMENTS_PREPED_VIEW
WHERE SALESFORCE_ACCOUNT_ID = '<salesforce_account_id>'
  AND ENGAGEMENT_DATE BETWEEN '<START_DATE>' AND '<END_DATE>';
```

## HTML Component

Insert as Section 2.5 (between Execution Timeline and Usability) or append to Section 2.

```html
<h3 style="font-family:'Texta',sans-serif; font-weight:800; color:var(--sf-mid-blue); margin:24px 0 12px;">Engagement Summary</h3>

<div class="kpi-grid">
  <div class="kpi-card">
    <div class="kpi-value">{{TOTAL_MEETINGS}}</div>
    <div class="kpi-label">Meetings</div>
    <div class="kpi-detail">During POC period</div>
  </div>
  <div class="kpi-card">
    <div class="kpi-value">{{TOTAL_HOURS}}h</div>
    <div class="kpi-label">Total Hours</div>
    <div class="kpi-detail">SE + customer combined</div>
  </div>
  <div class="kpi-card">
    <div class="kpi-value">{{ACTIVE_DAYS}}</div>
    <div class="kpi-label">Active Days</div>
    <div class="kpi-detail">Days with at least one interaction</div>
  </div>
  <div class="kpi-card">
    <div class="kpi-value">{{SE_HOURS}}h</div>
    <div class="kpi-label">SE Investment</div>
    <div class="kpi-detail">From Vivun activity log</div>
  </div>
</div>

<table class="data-table">
  <thead>
    <tr>
      <th>Date</th>
      <th>Type</th>
      <th>Description</th>
      <th>Duration</th>
    </tr>
  </thead>
  <tbody>
    {{ENGAGEMENT_ROWS}}
  </tbody>
</table>
```

## Notes

- This section is **internal-context heavy** — if `customer_safe_mode: true`, strip SE hours and replace "SE Investment" with a generic "Snowflake Support" label.
- If engagement queries return 0 rows, skip this section entirely (do not show empty engagement data).
- Engagement data is supplementary — the readout should be complete without it.
