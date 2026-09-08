# React-in-SiS Demo App Framework (CCv2 Pattern)

Alternative to HTML HOL when the client needs a product-grade interactive demo app.

## When to Use React-in-SiS (instead of HTML HOL)

- Client asked for "una demo que se vea como producto"
- Client was burned by "prototypes that look like Streamlit"
- Demo requires interactivity (buttons, real-time AI calls, drill-downs)
- Demo needs to show a workflow (classify → route → generate → approve)
- Demo is presented by the SE, not run by the client hands-on

## When to Stick with HTML HOL

- Client needs a guided hands-on experience (they run the SQL)
- Demo is for a workshop where attendees execute code themselves
- Time constraint < 2 days to build

---

## Architecture

```
React app (visible)  ──▶  Window event bus  ──▶  Hidden RPC bridge component
                                                        │
                                                        ▼
                                                  @st.fragment
                                                        │
                                                        ▼
                                              Python RPC handler
                                                        │
                                                        ▼
                                                Snowpark Session
```

Streamlit hosts TWO `st.components.v2` components:
1. **Visible React app** — full SPA rendered into `<div id="react-root">`. User sees ONLY this.
2. **Hidden RPC bridge** — zero-height component that converts React RPC requests into Streamlit trigger values.

The visible app never calls Streamlit directly. It uses `requestRpc(procedure, input)` which queues requests through the window event bus.

Reference: Google Doc `1Q6f7mOtmLb3gBDiJPHUkBulNIsz0BX3T3z0x2OTf0qg` by Abhinav Bannerjee (Jul 2026).

---

## Tech Stack (fixed — do not deviate)

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Frontend | React 19 + TypeScript | UI rendering |
| Build | Vite 6 (lib mode, ES module) | Bundle into hashed JS + CSS |
| Data fetching | TanStack Query (React Query) | Caching, loading states |
| Validation | Zod (frontend) + dataclasses (Python) | Type-safe RPC |
| Icons | Lucide React | Consistent icon set |
| Host | Streamlit + st.components.v2 | SiS deploy, Snowpark access |
| Backend | Snowpark Python | SQL via authenticated session |

---

## Project Structure

```
{client}/app/
├── .streamlit/
│   └── config.toml              # theme + headless
├── frontend/
│   ├── src/
│   │   ├── index.tsx            # FrontendRenderer mount
│   │   ├── App.tsx              # Router / main view
│   │   ├── styles.css           # Full custom CSS (dark enterprise)
│   │   ├── rpcBridge.ts         # Hidden bridge renderer
│   │   ├── components/          # UI views (one per page/feature)
│   │   └── rpc/
│   │       ├── types.ts         # Zod schemas + RpcProcedureMap
│   │       ├── events.ts        # Window event bus
│   │       ├── client.ts        # requestRpc() with serial queue
│   │       └── hooks.ts         # TanStack Query hooks per procedure
│   ├── package.json
│   ├── tsconfig.json
│   ├── vite.config.ts           # Main app build (outDir: build)
│   └── vite.rpc.config.ts       # RPC bridge build (emptyOutDir: false)
├── rpc/
│   ├── __init__.py
│   ├── bridge.py                # handle_rpc_request + render_rpc_bridge
│   ├── schemas.py               # Dataclass I/O per procedure
│   └── procedures.py            # PROCEDURES dict + handler functions
├── streamlit_app.py             # Minimal host (CSS hide + 2 components)
├── environment.yml              # NO python version, NO strict versions
└── Makefile                     # build, stage, deploy
```

---

## Generation Algorithm (5 Phases)

### Phase 1: Data Infrastructure (can run in parallel with 2 & 3)
1. CREATE DATABASE + schemas (organize by domain: RAW, RULES, PROCESSED, APP, etc.)
2. CREATE tables + INSERT seed/synthetic data
3. Validate AI functions with test cases (AI_CLASSIFY, AI_COMPLETE, etc.)

### Phase 2: Frontend React (independent — no Snowflake dependency)
1. Scaffold: package.json, tsconfig.json, vite.config.ts, vite.rpc.config.ts
2. RPC layer: types.ts, events.ts, client.ts, hooks.ts, rpcBridge.ts
3. Components: Layout, Sidebar, one view per demo feature
4. styles.css with dark enterprise theme
5. index.tsx mount point with QueryClientProvider
6. `npm install && npm run build` to verify

### Phase 3: Backend Python RPC (independent)
1. streamlit_app.py — minimal host with CSS hide + @st.fragment
2. rpc/bridge.py — dispatch trigger values to handlers
3. rpc/schemas.py — dataclasses for each procedure's I/O
4. rpc/procedures.py — PROCEDURES dict with handler functions using Snowpark session
5. environment.yml + .streamlit/config.toml + Makefile

### Phase 4: Intelligence Layer (depends on Phase 1)
1. Semantic View over processed data (YAML via SYSTEM$CREATE_SEMANTIC_VIEW_FROM_YAML)
2. CREATE AGENT with cortex_analyst_text_to_sql tool
3. Validate with 2-3 natural language queries

### Phase 5: Integration + Deploy (depends on all)
1. Verify frontend build → `build/` directory (NOT `dist/`)
2. Validate RPC field names match between frontend and backend
3. PUT all files to Snowflake stage
4. CREATE STREAMLIT with QUERY_WAREHOUSE
5. Test end-to-end

---

## Default Design System (Enterprise Dark Theme)

```css
:root {
  --sidebar-bg: #0F1419;
  --content-bg: #1A1F25;
  --card-bg: #242A32;
  --border: #2E3640;
  --accent: #29B5E8;         /* Snowflake Blue */
  --accent-hover: #1DA1D4;
  --text-primary: #E8ECF0;
  --text-secondary: #8B95A1;
  --success: #10B981;
  --warning: #F59E0B;
  --danger: #EF4444;
}

/* Typography: Inter, system sans-serif */
/* Sidebar: 260px fixed, logo top */
/* Content: flex-grow, internal overflow-y: auto */
/* Cards: border-radius 12px, box-shadow subtle */
/* Tables: modern with row hover, colored badges per category */
/* KPI cards: icon + number + trend arrow */
```

---

## Critical Gotchas (MUST follow)

### Environment
1. **NEVER put python=3.11** in environment.yml — 3.11 does NOT exist in SiS
2. **NEVER put strict versions** (streamlit=1.35.0) — causes resolver conflicts
3. **Correct format**: just package names (streamlit, snowflake-snowpark-python, pandas)

### Build
4. **Vite outDir MUST be `build`** (NOT `dist`) — Makefile references `frontend/build/`
5. **Two Vite builds required**: main app (emptyOutDir: true) THEN bridge (emptyOutDir: false)
6. **vite.config.ts lib mode**: entry is index.tsx, format is 'es'

### Snowflake
7. **Owner-rights PRIMARY role only** — GRANT SELECT on ALL tables to Streamlit owner role
8. **get_active_session()** is the ONLY way to get Snowpark session in SiS

### RPC Bridge
9. **Calls serialize** — one trigger per Streamlit rerun. Batch initial queries.
10. **Field name alignment** — frontend types.ts procedure names MUST match backend PROCEDURES dict keys EXACTLY
11. **Debounce continuous inputs** — sliders/search boxes cause RPC storm without debounce (~350ms)

### CSS
12. **iframe height** — default `height=600` in `declare_component` cuts content. Use `height=900` minimum AND add CSS: `iframe { height: calc(100vh - 60px) !important; }` to fill viewport minus Snowsight header
13. **CSS MUST hide**: `[data-testid="stMainBlockContainer"]` padding, `[data-testid="stVerticalBlock"]` gap, `.element-container` margin, bridge component
14. **No min-height** — use fixed `height: 100vh` with `overflow-y: auto` inside the React app shell

### Serialization
15. **SafeEncoder REQUIRED in bridge.py** — Snowpark returns `Timestamp` and `Decimal` objects that `json.dumps()` cannot serialize. ALWAYS add a custom JSONEncoder that converts datetime→isoformat(), Decimal→float(). Pattern: `json.loads(json.dumps(result, cls=SafeEncoder))` before storing in session_state. Without this, navigating between views crashes with "Object of type Timestamp is not JSON serializable"

### v1 Protocol (warehouse runtime)
16. **CCv2 NOT available in warehouse runtime** — only in container runtime with COMPUTE_POOL. If using QUERY_WAREHOUSE, MUST use v1 protocol with `declare_component()`
17. **v1 postMessage format** — messages MUST include `isStreamlitMessage: true` and `apiVersion: 1`. Without these, Streamlit ignores the message and shows "hasn't received setComponentReady"
18. **Required v1 lifecycle** — call `Streamlit.setComponentReady()` after React mounts, then `Streamlit.setFrameHeight()` via ResizeObserver

---

## Parallel Team Assignment (for team-workflow)

| Worker | Phase | Dependency |
|--------|-------|-----------|
| Implementor A | Phase 1 (Data Infra) | None — blocks Phase 4 |
| Implementor B | Phase 2 (Frontend) | None — fully independent |
| Implementor C | Phase 3 (Backend) | None — fully independent |
| Implementor D | Phase 4 (Intelligence) | After Phase 1 completes |
| Verifier | Phase 5 (Integration) | After ALL complete |

---

## Deploy Commands

```bash
# Build frontend
cd {client}/app/frontend && npm run build

# Upload to stage (use ACCOUNTADMIN)
snow sql -q "USE ROLE ACCOUNTADMIN; PUT 'file://streamlit_app.py' @DB.SCHEMA.STAGE/app/ AUTO_COMPRESS=FALSE OVERWRITE=TRUE"
snow sql -q "PUT 'file://rpc/__init__.py' @DB.SCHEMA.STAGE/app/rpc/ AUTO_COMPRESS=FALSE OVERWRITE=TRUE"
snow sql -q "PUT 'file://rpc/bridge.py' @DB.SCHEMA.STAGE/app/rpc/ AUTO_COMPRESS=FALSE OVERWRITE=TRUE"
snow sql -q "PUT 'file://rpc/schemas.py' @DB.SCHEMA.STAGE/app/rpc/ AUTO_COMPRESS=FALSE OVERWRITE=TRUE"
snow sql -q "PUT 'file://rpc/procedures.py' @DB.SCHEMA.STAGE/app/rpc/ AUTO_COMPRESS=FALSE OVERWRITE=TRUE"
snow sql -q "PUT 'file://frontend/build/*' @DB.SCHEMA.STAGE/app/frontend/build/ AUTO_COMPRESS=FALSE OVERWRITE=TRUE"
snow sql -q "PUT 'file://environment.yml' @DB.SCHEMA.STAGE/app/ AUTO_COMPRESS=FALSE OVERWRITE=TRUE"
snow sql -q "PUT 'file://.streamlit/config.toml' @DB.SCHEMA.STAGE/app/.streamlit/ AUTO_COMPRESS=FALSE OVERWRITE=TRUE"

# Create Streamlit app
snow sql -q "CREATE OR REPLACE STREAMLIT DB.SCHEMA.APP_NAME
  FROM '@DB.SCHEMA.STAGE/app/'
  MAIN_FILE = 'streamlit_app.py'
  QUERY_WAREHOUSE = 'WH_NAME'
  TITLE = 'App Title'"
```

---

## Comparison: React-in-SiS vs SPCS vs Streamlit

| Criterion | React-in-SiS (CCv2) | SPCS Container | Pure Streamlit |
|-----------|---------------------|---------------|---------------|
| Look & Feel | Product-grade React | Product-grade | "Looks like Streamlit" |
| Deploy complexity | Low (PUT + CREATE STREAMLIT) | High (Docker + registry) | Lowest |
| Auth/RBAC | Automatic | Manual | Automatic |
| Backend | Python/Snowpark direct | Separate API | Python/Snowpark |
| Concurrency | Serialized (OK for demos) | Unlimited | Serialized |
| Build time | 1-2 days | 3-4 days | 0.5-1 day |
| Best for | Demos, internal tools | Production apps | Quick prototypes |

---

## Example: AXON SDH (Jul 2026)

First successful implementation of this pattern:
- **Client:** AXON 360 (BPO for SDH Bogota)
- **Use case:** PQR classification + routing + response generation
- **RPC procedures:** cases.list, cases.classify, cases.classifyAll, cases.route, cases.generateResponse, metrics.summary
- **AI functions:** AI_CLASSIFY (8 categories), AI_COMPLETE (llama3.1-70b)
- **Database:** AXON (schemas: RAW, RULES, TEMPLATES, PROCESSED, APP, INTELLIGENCE)
- **Files:** ~/Documents/COCO/AXON/app/
- **Deployed as:** AXON.APP.AXON_SDH_BACKOFFICE
