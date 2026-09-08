# POC Readout SQL Queries

All queries used to populate POC readout data. Run against the POC account's Snowflake connection unless noted otherwise.

**Connection notes:**
- Queries against `SNOWFLAKE.ACCOUNT_USAGE` → use the POC account connection
- Queries against `SALES.RAVEN` or `FIVETRAN.SALESFORCE` → use `SNOWHOUSE_AWS_US_WEST_2` connection
- Queries against `SNOWSCIENCE` → use `SNOWHOUSE_AWS_US_WEST_2` connection

---

## 1. Account Discovery

### 1.1 Verify Account Name (RAVEN)

**Purpose:** Confirm exact Salesforce account name
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT DISTINCT SALESFORCE_ACCOUNT_NAME
FROM SALES.RAVEN.A360_DAILY_ACCOUNT_PRODUCT_CATEGORY_REVENUE_VIEW
WHERE SALESFORCE_ACCOUNT_NAME ILIKE '%<account_name>%'
LIMIT 10;
```

### 1.2 Org Discovery — All Snowflake Accounts

**Purpose:** Find all Snowflake accounts under the same customer org
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    SNOWFLAKE_ACCOUNT_NAME,
    SALESFORCE_ACCOUNT_NAME,
    SNOWFLAKE_DEPLOYMENT,
    CLOUD_PROVIDER,
    SUM(TOTAL_CREDITS) AS TOTAL_CREDITS_ALL_TIME,
    MIN(MONTH) AS FIRST_ACTIVE_MONTH,
    MAX(MONTH) AS LATEST_ACTIVE_MONTH
FROM SALES.RAVEN.A360_CREDIT_USAGE_VIEW
WHERE SALESFORCE_ACCOUNT_NAME ILIKE '%<account_name>%'
GROUP BY 1, 2, 3, 4
ORDER BY TOTAL_CREDITS_ALL_TIME DESC;
```

### 1.3 Salesforce Account Info

**Purpose:** Get account metadata (industry, size, revenue)
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    a.ID,
    a.NAME,
    a.INDUSTRY,
    a.NUMBER_OF_EMPLOYEES,
    a.ANNUAL_REVENUE,
    a.WEBSITE,
    a.BILLING_STATE,
    a.BILLING_COUNTRY,
    a.TYPE,
    a.DESCRIPTION
FROM FIVETRAN.SALESFORCE.ACCOUNT a
WHERE a.NAME ILIKE '%<account_name>%'
  AND a.IS_DELETED = FALSE
LIMIT 5;
```

---

## 2. Credit Consumption

### 2.1 Total Credits Consumed

**Purpose:** KPI card — total credits during POC period
**Connection:** POC account

```sql
SELECT SUM(CREDITS_USED) AS TOTAL_CREDITS
FROM SNOWFLAKE.ACCOUNT_USAGE.METERING_DAILY_HISTORY
WHERE USAGE_DATE BETWEEN '<START_DATE>' AND '<END_DATE>';
```

### 2.2 Credits by Warehouse

**Purpose:** Credit breakdown table by warehouse
**Connection:** POC account

```sql
SELECT
    WAREHOUSE_NAME,
    SUM(CREDITS_USED) AS CREDITS
FROM SNOWFLAKE.ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY
WHERE START_TIME BETWEEN '<START_DATE>' AND '<END_DATE>'
GROUP BY 1
ORDER BY 2 DESC;
```

### 2.3 Credits by Day

**Purpose:** Daily credit trend for Gantt overlay and cost section
**Connection:** POC account

```sql
SELECT
    USAGE_DATE,
    SUM(CREDITS_USED) AS CREDITS
FROM SNOWFLAKE.ACCOUNT_USAGE.METERING_DAILY_HISTORY
WHERE USAGE_DATE BETWEEN '<START_DATE>' AND '<END_DATE>'
GROUP BY 1
ORDER BY 1;
```

### 2.4 A360 Credit Usage (enriched — all credit types)

**Purpose:** Comprehensive credit breakdown including AI Services, SPCS, Cloud Services
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    MONTH,
    COMPUTE_CREDITS,
    CLOUD_SERVICES_CREDITS,
    AI_SERVICES_CREDITS,
    SNOWPARK_CONTAINER_SERVICES_CREDITS,
    SERVERLESS_TASK_CREDITS,
    TOTAL_CREDITS,
    SNOWFLAKE_DEPLOYMENT,
    CLOUD_PROVIDER
FROM SALES.RAVEN.A360_CREDIT_USAGE_VIEW
WHERE SNOWFLAKE_ACCOUNT_NAME = '<snowflake_account_name>'
ORDER BY MONTH;
```

### 2.5 Credits by Feature Category (A360)

**Purpose:** Feature-level revenue breakdown for usage analysis
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    DATE_TRUNC('week', GENERAL_DATE) AS WEEK_START,
    PRODUCT_CATEGORY,
    FEATURE,
    SUM(REVENUE) AS TOTAL_REVENUE,
    COUNT(DISTINCT GENERAL_DATE) AS ACTIVE_DAYS
FROM SALES.RAVEN.A360_DAILY_ACCOUNT_PRODUCT_CATEGORY_REVENUE_VIEW
WHERE SALESFORCE_ACCOUNT_NAME ILIKE '%<account_name>%'
  AND GENERAL_DATE BETWEEN '<START_DATE>' AND '<END_DATE>'
GROUP BY 1, 2, 3
ORDER BY WEEK_START, TOTAL_REVENUE DESC;
```

---

## 3. Storage

### 3.1 Average Storage Used

**Purpose:** KPI card — storage footprint
**Connection:** POC account

```sql
SELECT
    ROUND(AVG(STORAGE_BYTES + STAGE_BYTES + FAILSAFE_BYTES) / POWER(1024, 3), 2) AS AVG_GB
FROM SNOWFLAKE.ACCOUNT_USAGE.STORAGE_USAGE
WHERE USAGE_DATE BETWEEN '<START_DATE>' AND '<END_DATE>';
```

---

## 4. Object Inventory

### 4.1 Tables and Views

**Purpose:** Full object inventory section
**Connection:** POC account

```sql
SELECT
    TABLE_CATALOG,
    TABLE_SCHEMA,
    TABLE_NAME,
    TABLE_TYPE,
    ROW_COUNT,
    BYTES,
    CREATED,
    LAST_ALTERED
FROM <DATABASE>.INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA NOT IN ('INFORMATION_SCHEMA')
ORDER BY TABLE_SCHEMA, TABLE_TYPE, TABLE_NAME;
```

### 4.2 Warehouses

**Purpose:** Warehouse configuration for architecture diagram
**Connection:** POC account

```sql
SHOW WAREHOUSES;
```

### 4.3 Dynamic Tables

**Purpose:** Detect DT pipelines for architecture diagram
**Connection:** POC account

```sql
SHOW DYNAMIC TABLES IN DATABASE <DATABASE>;
```

### 4.4 Streams

**Purpose:** Detect CDC streams for architecture diagram
**Connection:** POC account

```sql
SHOW STREAMS IN DATABASE <DATABASE>;
```

### 4.5 Tasks

**Purpose:** Detect scheduled tasks for architecture diagram
**Connection:** POC account

```sql
SHOW TASKS IN DATABASE <DATABASE>;
```

### 4.6 Stages

**Purpose:** Detect data loading stages for architecture diagram
**Connection:** POC account

```sql
SHOW STAGES IN DATABASE <DATABASE>;
```

### 4.7 Functions and Procedures

**Purpose:** Detect UDFs and stored procedures
**Connection:** POC account

```sql
SHOW USER FUNCTIONS IN DATABASE <DATABASE>;
```

```sql
SHOW PROCEDURES IN DATABASE <DATABASE>;
```

### 4.8 Cortex Search Services

**Purpose:** Detect Cortex Search for AI section
**Connection:** POC account

```sql
SHOW CORTEX SEARCH SERVICES IN DATABASE <DATABASE>;
```

### 4.9 Streamlit Apps

**Purpose:** Detect Streamlit apps for Data App section
**Connection:** POC account

```sql
SHOW STREAMLIT IN DATABASE <DATABASE>;
```

### 4.10 Semantic Views

**Purpose:** Detect semantic views for AI/Analytics section
**Connection:** POC account

```sql
SHOW SEMANTIC VIEWS IN DATABASE <DATABASE>;
```

### 4.11 Agents

**Purpose:** Detect Cortex Agents for AI section
**Connection:** POC account

```sql
SHOW AGENTS IN DATABASE <DATABASE>;
```

---

## 5. Governance & Security

### 5.1 Row Access Policies

**Purpose:** Governance section — policy inventory
**Connection:** POC account

```sql
SHOW ROW ACCESS POLICIES IN DATABASE <DATABASE>;
```

### 5.2 Masking Policies

**Purpose:** Governance section — masking evidence
**Connection:** POC account

```sql
SHOW MASKING POLICIES IN DATABASE <DATABASE>;
```

### 5.3 Tags

**Purpose:** Classification/governance evidence
**Connection:** POC account

```sql
SELECT
    TAG_NAME,
    ALLOWED_VALUES
FROM <DATABASE>.INFORMATION_SCHEMA.TAGS;
```

### 5.4 Tag References

**Purpose:** Tag assignments to columns for classification table
**Connection:** POC account

```sql
SELECT
    OBJECT_NAME,
    COLUMN_NAME,
    TAG_NAME,
    TAG_VALUE
FROM TABLE(
    <DATABASE>.INFORMATION_SCHEMA.TAG_REFERENCES_ALL_COLUMNS(
        '<SCHEMA>.<TABLE>', 'TABLE'
    )
)
ORDER BY OBJECT_NAME, COLUMN_NAME;
```

### 5.5 Data Metric Functions (DMFs)

**Purpose:** Data quality monitoring evidence
**Connection:** POC account

```sql
SELECT *
FROM <DATABASE>.INFORMATION_SCHEMA.DATA_METRIC_FUNCTION_REFERENCES
WHERE METRIC_DATABASE_NAME = '<DATABASE>';
```

### 5.6 Access History (recent queries)

**Purpose:** Usage evidence — who queried what
**Connection:** POC account

```sql
SELECT
    USER_NAME,
    QUERY_START_TIME,
    DIRECT_OBJECTS_ACCESSED,
    BASE_OBJECTS_ACCESSED
FROM SNOWFLAKE.ACCOUNT_USAGE.ACCESS_HISTORY
WHERE QUERY_START_TIME BETWEEN '<START_DATE>' AND '<END_DATE>'
ORDER BY QUERY_START_TIME DESC
LIMIT 100;
```

---

## 6. Performance Metrics

### 6.1 Query Performance Summary

**Purpose:** Ease of Use section — execution times
**Connection:** POC account

```sql
SELECT
    WAREHOUSE_NAME,
    COUNT(*) AS QUERY_COUNT,
    ROUND(AVG(TOTAL_ELAPSED_TIME) / 1000, 2) AS AVG_SECONDS,
    ROUND(MEDIAN(TOTAL_ELAPSED_TIME) / 1000, 2) AS MEDIAN_SECONDS,
    ROUND(MAX(TOTAL_ELAPSED_TIME) / 1000, 2) AS MAX_SECONDS,
    ROUND(AVG(BYTES_SCANNED) / POWER(1024, 2), 2) AS AVG_MB_SCANNED
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
WHERE START_TIME BETWEEN '<START_DATE>' AND '<END_DATE>'
  AND EXECUTION_STATUS = 'SUCCESS'
  AND QUERY_TYPE IN ('SELECT', 'INSERT', 'CREATE_TABLE_AS_SELECT', 'MERGE')
GROUP BY 1
ORDER BY QUERY_COUNT DESC;
```

### 6.2 Spilling Analysis

**Purpose:** Performance evidence — any queries spilling to disk
**Connection:** POC account

```sql
SELECT
    WAREHOUSE_NAME,
    COUNT(*) AS SPILL_COUNT,
    ROUND(AVG(BYTES_SPILLED_TO_LOCAL_STORAGE) / POWER(1024, 2), 2) AS AVG_LOCAL_SPILL_MB,
    ROUND(AVG(BYTES_SPILLED_TO_REMOTE_STORAGE) / POWER(1024, 2), 2) AS AVG_REMOTE_SPILL_MB
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
WHERE START_TIME BETWEEN '<START_DATE>' AND '<END_DATE>'
  AND (BYTES_SPILLED_TO_LOCAL_STORAGE > 0 OR BYTES_SPILLED_TO_REMOTE_STORAGE > 0)
GROUP BY 1;
```

### 6.3 Continuous Work Hours (Gap Analysis)

**Purpose:** KPI card — real continuous work hours during the POC, NOT simply counting hours with at least one query.
**Connection:** POC account

**Why this matters:** Counting distinct hours with queries dramatically overcounts real work time. If a single query runs at 10:01 and the next at 11:55, counting 2 "active hours" is misleading — the SE was idle for most of that period. This query uses a 15-minute inactivity gap threshold to identify real work sessions and sums only the continuous work time within each session.

```sql
WITH ordered_queries AS (
    SELECT
        START_TIME,
        LAG(START_TIME) OVER (ORDER BY START_TIME) AS PREV_START_TIME
    FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
    WHERE START_TIME BETWEEN '<START_DATE>' AND '<END_DATE>'
      AND EXECUTION_STATUS = 'SUCCESS'
      AND QUERY_TYPE NOT IN ('DESCRIBE', 'SHOW', 'USE', 'ALTER_SESSION')
      AND USER_NAME NOT IN ('SYSTEM', 'SNOWFLAKE')
),
sessions AS (
    SELECT
        START_TIME,
        CASE
            WHEN PREV_START_TIME IS NULL THEN 1
            WHEN DATEDIFF('minute', PREV_START_TIME, START_TIME) > 15 THEN 1
            ELSE 0
        END AS NEW_SESSION
    FROM ordered_queries
),
session_ids AS (
    SELECT
        START_TIME,
        SUM(NEW_SESSION) OVER (ORDER BY START_TIME) AS SESSION_ID
    FROM sessions
),
session_summary AS (
    SELECT
        SESSION_ID,
        MIN(START_TIME) AS SESSION_START,
        MAX(START_TIME) AS SESSION_END,
        DATEDIFF('minute', MIN(START_TIME), MAX(START_TIME)) AS SESSION_MINUTES,
        COUNT(*) AS QUERY_COUNT,
        DATE_TRUNC('day', MIN(START_TIME)) AS SESSION_DATE
    FROM session_ids
    GROUP BY SESSION_ID
)
SELECT
    COUNT(DISTINCT SESSION_ID) AS TOTAL_SESSIONS,
    COUNT(DISTINCT SESSION_DATE) AS ACTIVE_DAYS,
    ROUND(SUM(SESSION_MINUTES) / 60.0, 1) AS CONTINUOUS_HOURS,
    SUM(SESSION_MINUTES) AS CONTINUOUS_MINUTES,
    SUM(QUERY_COUNT) AS TOTAL_QUERIES
FROM session_summary;
```

**Interpretation rules:**
- `CONTINUOUS_HOURS` is the value to use in the KPI card (rounded to nearest half-hour for display, e.g., 4.8 → "~5h")
- `TOTAL_SESSIONS` is the number of distinct work sessions
- `ACTIVE_DAYS` is the number of calendar days with at least one session
- A "session" is a continuous stretch of queries where no gap exceeds 15 minutes
- **NEVER** use `COUNT(DISTINCT HOUR(START_TIME))` or similar — this overcounts by 2-4x

**Daily breakdown variant** (for Cortex Code / productivity section):

```sql
WITH ordered_queries AS (
    SELECT
        START_TIME,
        DATE_TRUNC('day', START_TIME) AS QUERY_DATE,
        LAG(START_TIME) OVER (ORDER BY START_TIME) AS PREV_START_TIME
    FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
    WHERE START_TIME BETWEEN '<START_DATE>' AND '<END_DATE>'
      AND EXECUTION_STATUS = 'SUCCESS'
      AND QUERY_TYPE NOT IN ('DESCRIBE', 'SHOW', 'USE', 'ALTER_SESSION')
      AND USER_NAME NOT IN ('SYSTEM', 'SNOWFLAKE')
),
sessions AS (
    SELECT
        START_TIME, QUERY_DATE,
        CASE
            WHEN PREV_START_TIME IS NULL THEN 1
            WHEN DATEDIFF('minute', PREV_START_TIME, START_TIME) > 15 THEN 1
            ELSE 0
        END AS NEW_SESSION
    FROM ordered_queries
),
session_ids AS (
    SELECT
        START_TIME, QUERY_DATE,
        SUM(NEW_SESSION) OVER (ORDER BY START_TIME) AS SESSION_ID
    FROM sessions
),
session_summary AS (
    SELECT
        SESSION_ID,
        MIN(QUERY_DATE) AS SESSION_DATE,
        DATEDIFF('minute', MIN(START_TIME), MAX(START_TIME)) AS SESSION_MINUTES,
        COUNT(*) AS QUERY_COUNT
    FROM session_ids
    GROUP BY SESSION_ID
)
SELECT
    SESSION_DATE,
    COUNT(*) AS SESSIONS,
    SUM(SESSION_MINUTES) AS CONTINUOUS_MINUTES,
    ROUND(SUM(SESSION_MINUTES) / 60.0, 1) AS CONTINUOUS_HOURS,
    SUM(QUERY_COUNT) AS QUERIES
FROM session_summary
GROUP BY SESSION_DATE
ORDER BY SESSION_DATE;
```

---

## 7. Use Cases & Opportunities (Salesforce/Vivun)

### 7.1 Use Cases — RAVEN (Tier 1)

**Purpose:** POC use cases and their stages
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    USE_CASE_ID,
    USE_CASE_DESCRIPTION,
    USE_CASE_STAGE,
    USE_CASE_ACV,
    CREATED_DATE
FROM SALES.RAVEN.BOB_USE_CASES
WHERE SALESFORCE_ACCOUNT_NAME ILIKE '%<account_name>%'
  AND (IS_DELETED IS NULL OR IS_DELETED = FALSE)
ORDER BY USE_CASE_ACV DESC NULLS LAST;
```

### 7.2 Use Cases — Vivun (Tier 2)

**Purpose:** Fallback if RAVEN has no use cases
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    USE_CASE_NUMBER,
    USE_CASE_DESCRIPTION,
    USE_CASE_STATUS_C,
    USE_CASE_RISK,
    INDUSTRY_USE_CASE_C,
    ESTIMATED_ACV_C
FROM SALES.SE_REPORTING.VIVUN_DELIVERABLE_USE_CASE
WHERE ACCOUNT_ID = '<salesforce_account_id>'
  AND (_FIVETRAN_DELETED IS NULL OR _FIVETRAN_DELETED = FALSE)
ORDER BY ESTIMATED_ACV_C DESC NULLS LAST;
```

### 7.3 Open Opportunities

**Purpose:** Context for next steps and business alignment
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    o.OPPORTUNITY_NAME,
    o.STAGE_NAME,
    o.TOTAL_ACV,
    o.CLOSE_DATE,
    o.FORECAST_STATUS,
    o.NEXT_STEPS,
    DATEDIFF('day', CURRENT_DATE(), o.CLOSE_DATE) AS DAYS_TO_CLOSE
FROM SALES.RAVEN.BOB_OPPORTUNITIES o
WHERE o.SALESFORCE_ACCOUNT_ID IN (
    SELECT DISTINCT SALESFORCE_ACCOUNT_ID
    FROM SALES.RAVEN.BOB_USE_CASES
    WHERE SALESFORCE_ACCOUNT_NAME ILIKE '%<account_name>%'
)
  AND o.STAGE_NAME NOT IN ('Closed Won', 'Closed Lost')
ORDER BY o.CLOSE_DATE ASC NULLS LAST;
```

### 7.4 Solution Assessment (Salesforce Opportunity)

**Purpose:** POC status, risk level, SE comments
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    OPPORTUNITY_NAME,
    TECHNICAL_WIN_C,
    POC_OVERALL_STATUS_C,
    POC_START_DATE_C,
    POC_END_DATE_C,
    POC_RISK_LEVEL_C,
    POC_LED_BY_C,
    SE_COMMENTS_C,
    LAST_MODIFIED_DATE
FROM FIVETRAN.SALESFORCE.OPPORTUNITY
WHERE ACCOUNT_ID = '<salesforce_account_id>'
  AND STAGE_NAME NOT IN ('Closed Won', 'Closed Lost')
ORDER BY LAST_MODIFIED_DATE DESC
LIMIT 3;
```

---

## 8. Trial Org (if applicable)

### 8.1 Search Trial Org by Company Name

**Purpose:** Find trial org data when A360 has no data
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT DISTINCT
    COMPANY::VARCHAR AS COMPANY,
    ORGANIZATION_NAME,
    ORG_ID,
    MAX(TRIAL_END_DATE) AS TRIAL_END_DATE,
    MAX(TRIAL_DAY) AS LATEST_TRIAL_DAY
FROM SNOWSCIENCE.SELF_SERVICE.TRIAL_ORGS_USAGE
WHERE COMPANY::VARCHAR ILIKE '%<account_name>%'
   OR ORGANIZATION_NAME ILIKE '%<account_name>%'
GROUP BY 1, 2, 3
ORDER BY LATEST_TRIAL_DAY DESC
LIMIT 10;
```

### 8.2 Trial Org Daily Usage

**Purpose:** Daily credit trend for trial orgs
**Connection:** SNOWHOUSE_AWS_US_WEST_2

```sql
SELECT
    USAGE_DATE,
    TRIAL_DAY,
    COMPUTE_CREDITS,
    STORAGE_KB,
    TRIAL_END_DATE,
    ORGANIZATION_NAME
FROM SNOWSCIENCE.SELF_SERVICE.TRIAL_ORGS_USAGE
WHERE ORG_ID = '<org_id>'
ORDER BY USAGE_DATE;
```

---

## Notes

- Replace `<START_DATE>`, `<END_DATE>` with the POC date range (format: `YYYY-MM-DD`)
- Replace `<DATABASE>` with the POC database name
- Replace `<SCHEMA>` with the relevant schema
- Replace `<account_name>` with the customer/account name
- Replace `<salesforce_account_id>` with the 18-char SFDC ID
- Replace `<snowflake_account_name>` with the exact Snowflake account locator
- Replace `<org_id>` with the Snowflake Org ID from discovery
- ACCOUNT_USAGE views have up to 45 min latency — run queries after the POC window closes for complete data
- Some SHOW commands require appropriate role privileges (ACCOUNTADMIN recommended)
- A360 views require SNOWHOUSE connection; they do NOT exist on customer accounts
