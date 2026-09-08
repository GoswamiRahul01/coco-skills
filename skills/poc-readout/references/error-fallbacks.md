# Error Handling & Fallback Matrix

What to do when things go wrong during readout generation. Every error has a defined fallback — never leave the user blocked.

## Connection Errors

| Error | Cause | Fallback |
|-------|-------|----------|
| `SNOWHOUSE_AWS_US_WEST_2` connection fails | VPN not connected, token expired, connection not configured | Skip enrichment (Step 2.5). Build readout with POC account data only. Note: "Enrichment data unavailable — SNOWHOUSE connection failed." |
| POC account connection fails | Wrong connection name, expired credentials | Ask user to verify connection name. Run `cortex connections list` to show available connections. Cannot proceed without POC connection. |
| Connection timeout | Network issues, large result set | Retry once with 120s timeout. If still fails, try reducing date range or adding LIMIT. |

## Permission Errors

| Error | Cause | Fallback |
|-------|-------|----------|
| `ACCOUNT_USAGE` access denied | User role lacks `IMPORTED PRIVILEGES` on SNOWFLAKE database | Ask user to run: `GRANT IMPORTED PRIVILEGES ON DATABASE SNOWFLAKE TO ROLE <current_role>;` or switch to ACCOUNTADMIN. |
| `INFORMATION_SCHEMA` access denied | Role cannot see objects in target database | Ask user for correct database/role. Try `SHOW TABLES IN DATABASE <DB>` as alternative (less detail but wider access). |
| `SHOW` commands fail | Insufficient privileges | Use `INFORMATION_SCHEMA` queries as fallback where available. For objects without IS equivalent (Agents, Semantic Views), mark section as "Requires elevated privileges to inventory." |
| RAVEN/FIVETRAN views denied | SNOWHOUSE role lacks access | Skip enrichment. Proceed with POC account data only. |

## Empty Data

| Scenario | Likely Cause | Fallback |
|----------|-------------|----------|
| `METERING_DAILY_HISTORY` returns 0 rows | POC ended < 45 minutes ago; ACCOUNT_USAGE latency | Warn user: "ACCOUNT_USAGE has up to 45-minute latency. Re-run after the latency window." Show estimated availability time. |
| `METERING_DAILY_HISTORY` returns 0 credits | Wrong date range, or trial account with no compute | Verify date range with user. Try expanding range by ±7 days to check for activity. |
| Object inventory returns 0 tables | Wrong database name | Run `SHOW DATABASES` and present list to user for selection. |
| Governance queries return nothing | No governance features implemented | Populate Section 7 with: "No governance features (tags, policies, DMFs) were implemented during this POC. Consider implementing as a next step." |
| ML/AI queries return nothing | No AI features implemented | Populate Section 8 with: "No ML/AI features (Cortex Search, Agents, Semantic Views) were deployed during this POC." |
| Streamlit apps query returns nothing | No apps deployed | Populate Section 9 with: "No Streamlit applications were deployed during this POC." |
| RAVEN use cases returns 0 rows | Account not in RAVEN, or wrong account name | Fallback to Vivun (Query 7.2). If Vivun also empty, fallback to Salesforce opportunity (Query 7.3). If all empty, skip enrichment. |
| Trial Org query returns nothing | Not a trial account | Skip Section 8 (Trial Org). This is expected for paid accounts. |

## Asset Errors

| Error | Cause | Fallback |
|-------|-------|----------|
| Customer logo file not found | Wrong path, file not provided | Use a text-only header: `<h2 style="color:white; font-size:1.4em;">{{CUSTOMER_NAME}}</h2>` instead of `<img>`. |
| `snowflake-logo-full.png` missing | Assets not copied to output dir | Use inline SVG snowflake icon or text "Snowflake" in white. |
| `snowflake-linebreak.webp` missing | Assets not copied | Use CSS gradient divider: `<hr class="section-divider-line">` (see component-library.md Component 13 Fallback). |
| `bg-texture.svg` missing | Assets not copied | Header still works — the CSS has `linear-gradient` as base layer. Texture is decorative only. |
| Evidence screenshots missing | User didn't provide `img/` folder | Show placeholder: "Evidence screenshot not provided. Add screenshots to the img/ folder and rebuild." |

## PDF Generation Errors

| Error | Cause | Fallback |
|-------|-------|----------|
| Chrome not found | Not installed at expected path | Try alternative paths: `/Applications/Google Chrome.app/...`, `/usr/bin/google-chrome`, `/usr/bin/chromium-browser`. If none found, try `npx puppeteer` or instruct user to install Chrome. |
| PDF is blank | CSS variables not rendering, or `file://` path issue | Verify HTML path is absolute. Try opening HTML in browser first to confirm rendering. Check that CSS custom properties have fallback values. |
| PDF cuts off content | `break-inside: avoid` not working | Check that print CSS is inside `@media print` block. Try adding `page-break-inside: avoid` as legacy fallback. |
| PDF has white header | Background colors stripped | Verify `-webkit-print-color-adjust: exact` is set. Add `!important` to critical background declarations in print CSS. |
| PDF file is 0 bytes | Chrome crashed or path permission error | Check output path is writable. Try different output directory. Check Chrome error output for details. |

## Data Inconsistency

| Scenario | Detection | Resolution |
|----------|----------|------------|
| Credits by WH sum ≠ total credits | Cross-check in execution-testing Phase 2 | Use `METERING_DAILY_HISTORY` total as authoritative. Add footnote: "Warehouse breakdown may differ due to cloud services credits." |
| A360 credits ≠ ACCOUNT_USAGE credits | Different data sources, different aggregation windows | Use ONE source per section. ACCOUNT_USAGE for POC account sections, A360 for enrichment sections only. Add footnote explaining source. |
| Object inventory count ≠ KPI card | Objects created/dropped during query window | Recalculate KPI from inventory. Inventory is the authoritative source. |
| Warehouse in credits but not in inventory | Warehouse was dropped after use | Include in credit breakdown with note: "(dropped during POC)". |
| Dates in Gantt outside POC range | User-provided dates don't match actual activity | Clip Gantt bars to POC range. Warn user about date mismatch. |

## Graceful Degradation Priority

When multiple errors occur, degrade gracefully in this order:

1. **Always generate**: Executive Summary (Section 1) + Object Inventory (Section 10) + Footer
2. **Generate if possible**: Cost/Benefit (Section 4), Ease of Use (Section 5), Architecture (Section 12)
3. **Skip if data unavailable**: Enrichment sections, Engagement Timeline, Peer Comparison, Trial Org
4. **Never skip**: Customer name, dates, and document structure — even with zero data, produce a skeleton readout

The user should always receive a usable HTML file, even if some sections contain "Data unavailable" placeholders.
