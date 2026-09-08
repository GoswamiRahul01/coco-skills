# Anti-Patterns

Rules on what to avoid when building POC readout documents. These come from iterative builds and common mistakes.

## Data Anti-Patterns

| Avoid | Why | Do Instead |
|-------|-----|------------|
| **Inventing KPI values** | Destroys credibility if customer cross-checks | Always query real data; if a query returns 0 or NULL, show "No data available" |
| **Hardcoding credit price at $3.00** | Pricing varies by edition, cloud, and contract | Ask the user for the credit price or read it from the Salesforce opportunity |
| **Hardcoding storage price at $23/TB** | Same — varies by contract | Ask the user or use the standard price with an explicit note |
| **Assuming POC dates** | Gantt and credit sums will be wrong | Query `METERING_DAILY_HISTORY` for actual activity range, then confirm with user |
| **Summing credits from different sources** | A360 credits and ACCOUNT_USAGE credits may overlap or differ | Use ONE authoritative source per metric; note the source in the footer |
| **Ignoring ACCOUNT_USAGE latency** | Views have up to 45-minute delay | Run queries after the POC window closes; warn the user if the POC ended today |
| **Counting distinct hours as "work hours"** | `COUNT(DISTINCT HOUR(START_TIME))` dramatically overcounts (2-4x). A query at 10:01 and another at 11:55 counts as 2 hours, but the SE was idle for 114 minutes | Use Query 6.3 (gap analysis with 15-min inactivity threshold) to measure real continuous work sessions. Sum only the time within each session. |
| **Using ILIKE for exact account matches** | Returns multiple accounts for common names | Use ILIKE for discovery, then switch to exact `=` match for data queries |

## Branding Anti-Patterns

| Avoid | Why | Do Instead |
|-------|-----|------------|
| **`--sf-navy: #0D2B45`** | Not an official brand variable; inconsistent with `snowflake-brand-guidelines` | Use `--sf-mid-blue: #11567F` for headers and accents |
| **logo.wine URL for Snowflake logo** | External dependency; may go offline; not official | Use the PNG from `assets/snowflake-logo-full.png` |
| **Hand-drawn SVG snowflake** | Never looks right; not official branding | Use the official PNG with `filter: brightness(0) invert(1)` on dark backgrounds |
| **Non-Texta heading fonts** | Breaks brand consistency | Use Texta from Snowflake CDN for all headings |
| **Lowercase section headings** | Brand guidelines require uppercase headings | Apply `text-transform: uppercase; letter-spacing: 0.5px` to all `h1`, `h2` |
| **Missing copyright footer** | Every branded document requires it | Add `© YEAR Snowflake Inc. All Rights Reserved.` |

## Content Anti-Patterns

| Avoid | Why | Do Instead |
|-------|-----|------------|
| **Empty sections with no explanation** | Looks like a bug; customer wonders if something broke | Show "No [feature] was implemented during this POC" with a brief note |
| **Generic next steps** ("Try Snowpark!", "Explore Cortex!") | Feels like marketing, not a technical readout | Recommend specific next steps tied to the customer's actual use cases and POC findings |
| **Architecture diagram with placeholder nodes** | "Source System", "ETL Tool" without real names look lazy | Auto-detect from object inventory; ask user for any unknowns |
| **Listing objects without context** | A raw inventory table means nothing to an executive | Group objects by function (ETL, Analytics, ML, Governance) and add brief descriptions |
| **Claiming compliance without evidence** | "COMPLIANT" toggle without supporting data is risky | Only enable compliance toggles when queries confirm the criteria is met |
| **Mixing languages** | "Resumen Ejecutivo" header with "Credits consumed" label | Choose ONE language for the entire document; all text must match |
| **Including internal-only data** | Salesforce opportunity details, SE comments, ACV figures | The readout is customer-facing; remove all internal sales data before delivery |

## Visual Anti-Patterns

| Avoid | Why | Do Instead |
|-------|-----|------------|
| **Container borders in print CSS** | Chrome draws continuous border lines at page breaks | `border: none` on all container elements in `@media print` |
| **Box shadows in print CSS** | Renders as gray blocks in PDF | `box-shadow: none` in `@media print` |
| **Compliance toggle buttons in print** | Non-functional in a static PDF | Hide `.compliance-btn` in `@media print` |
| **Evidence images without borders** | Look floating and disconnected from content | Apply `border-radius: 8px; border: 1px solid #ddd; box-shadow: 0 2px 8px rgba(0,0,0,0.1)` |
| **Oversized evidence images** | Break page layout in PDF | Set `max-width: 100%; height: auto` and consider `max-height: 400px` |
| **Color-only status indicators** | Not accessible; colorblind users can't distinguish | Combine color with text labels: green "OK", orange "WARNING", red "ERROR" |

## Architecture Diagram Anti-Patterns

| Avoid | Why | Do Instead |
|-------|-----|------------|
| **Static hardcoded diagram** | Doesn't reflect actual POC implementation | Build dynamically from object inventory queries |
| **Missing arrow dividers** | Columns look disconnected | Use `→` arrow dividers between every column |
| **"Snowflake" column with no sub-zones** | Oversimplifies the architecture | Show RAW → ANALYTICS zones; add Security, Governance, Agent, App sub-sections as applicable |
| **Omitting Horizon section** | If tags/policies exist, governance should be visible in the diagram | Check governance queries; if any return data, add Horizon sub-section to Snowflake column |
| **Consumer column with only "Users"** | Too vague; doesn't show the value chain | Identify specific personas: Analyst, Executive, BI Tool (Tableau/PowerBI), SQL/Notebook, Streamlit App |

## Gantt Anti-Patterns

| Avoid | Why | Do Instead |
|-------|-----|------------|
| **Dates outside POC range** | Activities can't happen before or after the POC | Clip all bars to the POC date range |
| **Single-color bars** | No visual distinction between activity types | Use category colors: ETL (blue), Modeling (green), ML (purple), SV/Agent (orange), App (red), Security (teal), Audit (gray) |
| **Gantt without legend** | Colors are meaningless without labels | Include a legend row at the bottom of the Gantt |
| **Activities without duration** | A bar spanning 1 day for a 2-week activity is misleading | Use actual start/end dates from the POC timeline; estimate if exact dates unknown |

## Query Anti-Patterns

| Avoid | Why | Do Instead |
|-------|-----|------------|
| **Running RAVEN queries on POC connection** | RAVEN views exist only on SNOWHOUSE | Use `SNOWHOUSE_AWS_US_WEST_2` connection for all RAVEN/FIVETRAN/SNOWSCIENCE queries |
| **Running ACCOUNT_USAGE queries on SNOWHOUSE** | ACCOUNT_USAGE is per-account | Use the POC account connection for all ACCOUNT_USAGE queries |
| **Querying INFORMATION_SCHEMA without excluding INFORMATION_SCHEMA** | Returns system metadata rows | Always add `WHERE TABLE_SCHEMA NOT IN ('INFORMATION_SCHEMA')` |
| **SHOW commands without database scope** | Returns objects across all databases | Use `SHOW ... IN DATABASE <DATABASE>` to scope results |
