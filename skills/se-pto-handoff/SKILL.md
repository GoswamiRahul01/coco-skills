---
name: se-pto-handoff
title: SE PTO Handoff
summary: Bidirectional PTO handoff — generate coverage packages or read and act on a colleague's handoff.
description: "Use when creating or reading PTO handoff packages. GENERATE mode helps the SE going on PTO create a complete handoff with per-account context. READ mode helps the covering SE/manager act on a handoff with next steps and relevant skills. Triggers: pto handoff, vacation handoff, handoff de vacaciones, cobertura vacaciones, cubrir cuenta, leer handoff, generar handoff, entrega de vacaciones, PTO, out of office."
tools: [Read, Write, Edit, Bash, Glob, Grep, web_search, web_fetch, AskUserQuestion, sql_execute]
prompt: "$se-pto-handoff"
language: es
status: Published
author: JP Castaneda
type: snowflake
---

# SE PTO Handoff

## About / Acerca de / Sobre

**English:** Bidirectional PTO handoff skill. GENERATE mode helps the SE going on PTO create a complete handoff package with per-account context files (opportunity data, Gong transcripts, local resources, suggested skills). READ mode helps the covering SE or manager read and act on a handoff, guiding them through context, next steps, and recommending relevant Cortex Code skills for each pending task.

**Español:** Skill bidireccional para handoffs de vacaciones/PTO entre SEs. El modo GENERAR ayuda al SE que sale de vacaciones a crear un paquete completo de handoff con archivos de contexto por cuenta (datos de oportunidad, transcripciones de Gong, recursos locales, skills sugeridos). El modo LEER ayuda al SE o manager de cobertura a leer y actuar sobre un handoff, guiándolo a través del contexto, próximos pasos y recomendando skills de Cortex Code relevantes para cada tarea pendiente.

**Português (BR):** Skill bidirecional para handoffs de férias/PTO entre SEs. O modo GERAR ajuda o SE que sai de férias a criar um pacote completo de handoff com arquivos de contexto por conta (dados de oportunidade, transcrições do Gong, recursos locais, skills sugeridos). O modo LER ajuda o SE ou gerente de cobertura a ler e agir sobre um handoff, guiando-o através do contexto, próximos passos e recomendando skills relevantes do Cortex Code para cada tarefa pendente.

---

## Mode Detection

Ask the user which mode:

1. **Generar handoff** — "Voy a salir de vacaciones y necesito dejar todo listo"
2. **Leer handoff / Cubrir cuenta** — "Necesito cubrir una cuenta durante vacaciones de un colega"

---

## Mode 1: GENERATE (SE que sale de PTO)

### Step 1: Collect Information ⚠️ STOP — Ask before proceeding

Ask the user:
1. **Nombre del SE que sale** (default: current user from memory)
2. **Fechas de PTO** (desde - hasta)
3. **Para quién es el handoff** (managers, SEs de cobertura)
4. **Cuentas a incluir** — listar o decir "todas mis cuentas activas"
5. **Directorio de salida** — default: `~/Documents/COCO/Handoff_PTO_{nombre}_{fecha}/`

### Step 2: Research Accounts

For each account listed:
1. Search Salesforce for opportunity data (stage, ACV, close date, next steps, SE comments)
2. Search Gong for recent transcripts (last 60 days)
3. Search email engagement data for recent threads
4. Check for local project folders: `~/Documents/COCO/`, `~/Documents/SIZING/`, `~/Documents/COCO/HOL/`
5. Check Snowflake objects if demos were built (agents, semantic views, Streamlit apps)

Use connection: SNOWHOUSE_AWS_US_WEST_2 for all queries.

### Step 3: Generate Structure

Create the output directory with this structure:
```
{output_dir}/
├── README.md                    # Main document for managers (no assigned owners)
├── SKILL.md                     # Copy of this skill for reference
├── calendario.md                # Calendar view of events during PTO
└── cuentas/
    └── {account_slug}.md        # One file per account
```

### Step 4: Generate README.md

The README is for **managers/directors** who triage and assign coverage. It must:
- NOT assign specific people to accounts (they decide)
- Show a priority table: Account | ACV | Stage | Next Event | Priority | Context File
- Show the calendar of events during PTO period
- Link to each account .md for deep context
- Include a "How to use" section explaining the covering SE should invoke this skill in READ mode

### Step 5: Generate per-account .md files

Each `cuentas/{slug}.md` must be self-contained with:

```markdown
# {Account Name}

## Quick Facts
| Campo | Detalle |
|-------|---------|
| Stage | ... |
| ACV | ... |
| Close Date | ... |
| AE | ... |
| Última actividad | ... |

## Contexto
[2-4 paragraphs: what has been done, current state, what's pending]

## Contactos
| Nombre | Email | Rol |
|--------|-------|-----|
...

## Actividad Pendiente Durante PTO
[What might happen, what events are scheduled]

## Qué Hacer Si...
| Si pasa esto... | Hacer esto... |
|-----------------|---------------|
...

## Recursos Locales
- [list of local files, folders, sizing docs, HOLs]

## Skills Sugeridos para el SE de Cobertura
[Which Cortex Code skills to invoke and with what parameters]
- For HOLs: invoke `snowflake-hol-generator` with industry={x}, client={y}, modules=[...]
- For sizing: invoke `jp-sizing` or `sizing-skill`
- For meeting prep: invoke `se-meeting-briefing` + `account-research` for deep account/contact research
- For account deep-dive: invoke `account-research` with company name — gets contacts, org chart, prior engagement, tech stack
- For demos: list the SQL/setup files to run
```

### Step 6: Generate calendario.md

Calendar view with:
- Table: Date | Account | Event | Participants | Notes
- Only events during the PTO period
- No "who covers" column — that's for managers to decide

### Step 7: Present for Review ⚠️ STOP

Show the user:
- Summary of what was generated
- Number of accounts covered
- Any gaps found (accounts without SFDC data, missing contacts, etc.)
- Ask if they want to adjust anything

---

## Mode 2: READ / COVER (SE o manager que cubre)

### Step 1: Locate Handoff

Ask the user:
1. **Path to the handoff directory** — e.g., `~/Documents/COCO/Handoff_PTO_JuanPa_Jun2026/`
2. Or: **Which account do you need to cover?**

If they give a directory, read the README.md to understand the full scope.
If they give an account name, find the matching `cuentas/{slug}.md`.

### Step 2: Load Account Context

Read the account .md file completely. Understand:
- Current state and history
- Pending activities
- Contacts and their roles
- Available resources

### Step 3: Guide the Covering SE

Based on what's pending, proactively suggest actions:

**If a HOL is needed:**
- Say: "Para este HOL, puedo invocar el skill `snowflake-hol-generator` con los datos de la cuenta. ¿Quieres que lo genere?"
- Pre-fill parameters from the account .md (industry, use cases, client name)

**If a meeting is coming:**
- Say: "Tienes una reunión próxima. Puedo invocar `se-meeting-briefing` para prepararte y `account-research` para investigar los contactos y la empresa. ¿Quieres?"
- Invoke `account-research` to get updated company info, contacts (CIO, CTO, Dir Analytics), org chart
- Provide talking points from the account context combined with fresh research

**If a demo/sustentación is needed:**
- List available resources (SQL files, sizing docs, agent specs)
- Offer to run setup scripts or explain the demo flow

**If it's a POC with potential blocker:**
- Provide account locator, admin contacts
- Explain the current state of the POC and what to check

**If it's pricing/commercial:**
- Highlight who to escalate to (DM, managers)
- Provide deal context for negotiation

### Step 4: Execute Actions

Help the SE actually do the work:
- Invoke suggested skills as needed
- Search for additional context in Gong/email if the SE needs more info
- Generate meeting briefs, HOLs, or other materials

### Step 5: Update Handoff (Optional)

If the covering SE took significant actions, offer to update the account .md with:
- What was done during the PTO
- New contacts discovered
- Changes in status
- Notes for when the original SE returns

---

## Key Principles

1. **Self-contained .md files** — Each account file must be readable without any other context
2. **No assigned owners** — The README never dictates who covers what
3. **Actionable** — Every pending item has a clear "what to do" instruction
4. **Skill-integrated** — Always suggest which Cortex Code skills can help the covering SE
5. **Bidirectional** — Works for both the person leaving AND the person covering
6. **Updatable** — The covering SE can add notes for the returning SE

---

## When to Use

- SE going on PTO and needs to prepare per-account handoff packages
- SE or manager covering accounts during a colleague's absence
- Preparing end-of-week context for accounts with upcoming activity

## Stopping Points

| Step | Condition | Action |
|------|-----------|--------|
| Step 1 (Mode 1) | Missing account info or PTO dates | ⚠️ STOP — Confirm accounts and PTO dates before researching |
| Step 7 (Mode 1) | Handoff package generated | ⚠️ STOP — Review completed package before sharing |
| Step 1 (Mode 2) | Handoff directory not confirmed | ⚠️ STOP — Confirm handoff directory path before loading context |

## Output

- **Mode 1 (GENERATE)**: Directory with `README.md`, per-account `.md` files in `cuentas/`, and `calendario.md`
- **Mode 2 (READ)**: Guided briefing with suggested next actions and relevant skill recommendations
