# Execution Testing

Validate all queries and template rendering before building the final HTML readout.

## Phase 1: Query Compilation (Before Step 2)

Run every query from `references/queries.md` with `only_compile: true` to verify syntax before executing. This catches column renames, missing views, and permission issues early.

### Test Matrix

| Query | Connection | Compile Check | Common Failure |
|-------|-----------|---------------|----------------|
| 2.1 Total Credits | POC account | `SELECT SUM(CREDITS_USED) FROM SNOWFLAKE.ACCOUNT_USAGE.METERING_DAILY_HISTORY WHERE 1=0` | Missing ACCOUNTADMIN role |
| 2.2 Credits by WH | POC account | Same pattern with `WHERE 1=0` | Same |
| 2.3 Credits by Day | POC account | Same | Same |
| 3.1 Storage | POC account | `SELECT AVG(STORAGE_BYTES) FROM SNOWFLAKE.ACCOUNT_USAGE.STORAGE_USAGE WHERE 1=0` | Same |
| 4.1 Tables/Views | POC account | `SELECT TABLE_NAME FROM <DB>.INFORMATION_SCHEMA.TABLES WHERE 1=0` | Wrong database name |
| 5.1–5.5 Governance | POC account | `SHOW ROW ACCESS POLICIES IN DATABASE <DB>` | Insufficient privileges |
| 6.1–6.2 Performance | POC account | Same as 2.x pattern | Same |
| 1.1–1.3 Discovery | SNOWHOUSE | `SELECT 1 FROM SALES.RAVEN.A360_DAILY_ACCOUNT_PRODUCT_CATEGORY_REVENUE_VIEW WHERE 1=0` | Wrong connection |
| 7.1–7.4 Use Cases | SNOWHOUSE | Similar pattern | Wrong connection / view missing |
| 8.1–8.2 Trial Org | SNOWHOUSE | Similar pattern | View access revoked |

### Execution Protocol

```
FOR each query group (A, B, C, D, E):
  1. Set correct connection (POC or SNOWHOUSE)
  2. Run compile-only check (WHERE 1=0 or LIMIT 0)
  3. IF compile fails:
     a. Log the error
     b. Check: wrong connection? → switch and retry
     c. Check: missing role? → report to user, suggest ACCOUNTADMIN
     d. Check: missing view? → skip query, mark section as "Data unavailable"
  4. IF compile succeeds:
     a. Run actual query with real date filters
     b. Validate result is not empty (warn if so — may be latency)
```

## Phase 2: Data Validation (After Step 2)

After collecting all query results, run cross-checks before building HTML.

### Cross-Check Rules

| Check | Formula | Action if Failed |
|-------|---------|-----------------|
| Credits sum | `SUM(credits_by_warehouse) ≈ total_credits` (±1% tolerance) | Warn user; use total_credits as authoritative |
| Credits by day sum | `SUM(credits_by_day) ≈ total_credits` (±1% tolerance) | Same |
| Object count | `COUNT(inventory_rows) = TOTAL_OBJECTS KPI` | Recalculate KPI from inventory |
| Row count | `SUM(row_count from inventory) = TOTAL_ROWS KPI` | Recalculate KPI from inventory |
| Date range | `MIN(credits_by_day.date) >= START_DATE` | Warn if activity outside POC range |
| Warehouse names | `credits_by_warehouse.names ⊆ inventory.warehouse_names` | Flag mismatches |
| Governance consistency | If tags query returns data → governance section must be non-empty | Auto-populate governance section |

### Validation Output

Report validation results to user before proceeding to Step 3:

```
═══ Data Validation Report ═══
✓ Total credits: 1,247.0 (warehouse sum: 1,247.0, delta: 0.0%)
✓ Object count: 47 (inventory: 47)
✓ Row count: 12.4M (inventory sum: 12,412,350)
⚠ Storage: 0.00 GB — ACCOUNT_USAGE may have latency (POC ended < 45 min ago)
✓ Date range: 2026-01-15 to 2026-02-28 (all within POC range)
✓ Warehouse names consistent
✓ Governance: 3 tags, 2 masking policies → Section 7 will be populated
```

## Phase 3: Template Rendering Validation (After Step 3)

After replacing all `{{PLACEHOLDER}}` tokens, verify completeness.

### Token Check

```python
import re
html = open('readout.html').read()
remaining = re.findall(r'\{\{[A-Z_]+\}\}', html)
if remaining:
    print(f"ERROR: {len(remaining)} unreplaced tokens: {remaining}")
else:
    print("OK: All tokens replaced")
```

### Visual Checks (manual)

- [ ] Open HTML in browser — all sections render without broken layout
- [ ] KPI cards show actual numbers (not "{{TOTAL_CREDITS}}")
- [ ] Gantt bars are within the chart boundaries
- [ ] Architecture diagram arrows display correctly
- [ ] Evidence images load (relative paths correct)
- [ ] Footer shows correct account ID and region
- [ ] Table of contents links navigate to correct sections

## Phase 4: PDF Validation (After Step 6)

- [ ] PDF page count is reasonable (8-20 pages for typical POC)
- [ ] First page has full-bleed header (no white margin at top)
- [ ] Background colors render in print
- [ ] No page breaks in middle of KPI cards or criteria cards
- [ ] Compliance toggle buttons are hidden
- [ ] Evidence images fit within page margins
- [ ] Footer renders on last page
