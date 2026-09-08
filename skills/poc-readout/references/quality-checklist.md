# Quality Checklist

Validate every item before delivering a POC readout to the customer.

## Data Accuracy
- [ ] Total credits KPI matches `METERING_DAILY_HISTORY` query result
- [ ] Object count KPI matches the number of rows in the object inventory table
- [ ] Storage KPI matches `STORAGE_USAGE` query result
- [ ] Row count KPI is consistent with `ROW_COUNT` sums from `INFORMATION_SCHEMA.TABLES`
- [ ] Credit breakdown by warehouse sums to total credits (within rounding tolerance)
- [ ] Credit breakdown by day sums to total credits (within rounding tolerance)
- [ ] Gantt timeline dates fall within the POC start and end dates
- [ ] Credit cost calculation uses confirmed credit price (not assumed $3.00)
- [ ] Storage cost calculation uses confirmed storage price (not assumed $23/TB)
- [ ] POC hours KPI uses Query 6.3 continuous gap analysis (NOT `COUNT(DISTINCT HOUR(...))`)
- [ ] POC hours daily breakdown validated — no single day shows implausibly high hours
- [ ] POC hours detail text includes session count and active days (e.g., "Continuous work across 4 days, 16 sessions")
- [ ] If A360 data was used, credit categories (AI Services, SPCS, Compute, Cloud Services) sum to total

## Branding
- [ ] CSS variables aligned with `snowflake-brand-guidelines` (`--sf-mid-blue: #11567F`, NOT `--sf-navy: #0D2B45`)
- [ ] Snowflake logo uses official PNG from `assets/snowflake-logo-full.png` (NOT logo.wine URL)
- [ ] Customer logo is at least as tall as or taller than the Snowflake logo (min 60px height)
- [ ] Headings use Texta font with `text-transform: uppercase; letter-spacing: 0.5px`
- [ ] Body text uses Lato at `color: #333333`
- [ ] Copyright footer present with current year
- [ ] `<base target="_blank">` in `<head>` for external links

## Content Completeness
- [ ] All 12 sections present (Executive Summary through Architecture)
- [ ] Sections with no data show "No [feature] was implemented during this POC" (not empty)
- [ ] ML & AI section reflects actual features found (Cortex Search, Agents, AI functions, etc.)
- [ ] Data App section reflects actual Streamlit apps found (or states "No apps deployed")
- [ ] Governance section reflects actual policies, tags, DMFs found
- [ ] Next Steps are specific to this customer's use cases (not generic recommendations)
- [ ] Architecture diagram reflects actual objects discovered in the inventory

## Cross-Section Consistency
- [ ] Object names in inventory match names in architecture diagram
- [ ] Warehouse names in credit breakdown match warehouses in object inventory
- [ ] Features mentioned in criteria cards are backed by evidence in the object inventory
- [ ] Gantt activities correspond to objects/features in the inventory
- [ ] If governance section claims tags exist, the tag query returned results
- [ ] If ML section claims models exist, the functions/procedures inventory confirms it

## Architecture Diagram
- [ ] Source column reflects actual data sources (not placeholders)
- [ ] ETL column reflects actual integration tools used
- [ ] Snowflake column includes all zones found in the object inventory
- [ ] Consumer column reflects actual consuming personas/tools
- [ ] Arrow dividers are present between all columns
- [ ] Horizon section included if governance features were detected
- [ ] `.drawio` XML file generated alongside HTML

## Print / PDF
- [ ] Print CSS preserves all background colors with `-webkit-print-color-adjust: exact`
- [ ] `break-inside: avoid` on KPI cards, criteria cards, architecture columns
- [ ] `border: none` on container elements in `@media print`
- [ ] `box-shadow: none` on all elements in print
- [ ] `tr:hover { background: inherit }` removes phantom highlights
- [ ] Compliance toggle buttons hidden in print
- [ ] Full-bleed header preserved on first page
- [ ] Evidence images render at correct size (not overflowing page)
- [ ] PDF generated via `html-to-pdf` skill with `--no-pdf-header-footer`

## Bilingual Support
- [ ] All text in the readout matches the POC language (Spanish for LATAM, English for US)
- [ ] Section headers, KPI labels, compliance buttons use correct language
- [ ] "COMPLIANT" for English / "CUMPLE" for Spanish
- [ ] Footer text matches document language

## Delivery
- [ ] HTML file is self-contained (no external CSS/JS dependencies beyond fonts)
- [ ] Evidence images referenced with relative paths (`img/<filename>.png`)
- [ ] `.drawio` architecture file generated and included
- [ ] PDF version generated via `html-to-pdf` skill
- [ ] File naming follows convention: `poc_readout_<customer_slug>.html`
- [ ] Output path confirmed with user before writing

## Config-Dependent Features
- [ ] If `wins_risks_ask: true` → WRA grid populated with real findings (not generic)
- [ ] If `health_score: true` → Score calculated from actual data, SVG arc matches score value
- [ ] If `engagement_timeline: true` → Data from SNOWHOUSE, stripped in customer_safe mode
- [ ] If `peer_comparison: true` → At least 5 peers found; no peer account names exposed
- [ ] If `chart_generation: true` → PNG files generated and embedded; fallback CSS charts if matplotlib unavailable
- [ ] If `multi_account: true` → All selected accounts aggregated; source noted per metric
- [ ] If `docx_output: true` → .docx generated with tables and headings (simplified layout)
- [ ] If `customer_safe_mode: true` → No SFDC IDs, SE names, ACV, opportunity details in output
- [ ] Persona rules applied: executive (no SQL blocks, max 8 pages), technical (full detail), customer_safe (no internal data)
- [ ] Disabled sections fully removed from HTML (no empty `<section>` blocks)
