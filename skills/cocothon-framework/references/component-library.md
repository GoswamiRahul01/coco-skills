# Component Library — Cocothon HTML Outputs

Componentes HTML reutilizables para todos los outputs del Cocothon. Cada componente incluye el snippet HTML, CSS requerido, y cuándo usarlo.

---

## CSS Variables Compartidas

Todas las páginas deben declarar estas variables en `:root`:

```css
:root {
    --sf-primary: #29B5E8;
    --sf-dark: #11567F;
    --sf-gradient-start: #11567F;
    --sf-gradient-end: #0a3a5c;
    --info-blue: #e8f4fd;
    --info-border: #29B5E8;
    --warning-orange: #fff3e0;
    --warning-border: #f57c00;
    --success-green: #e8f5e9;
    --success-border: #43a047;
    --code-bg: #1a1a2e;
    --radius: 12px;
    --navy: #0f172a;
    --green: #22c55e;
    --orange: #f97316;
    --purple: #8b5cf6;
    --pink: #ec4899;
    --yellow: #eab308;
    --teal: #14b8a6;
}
```

Fuentes estándar:
```html
<link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@600;700;800&family=Lato:wght@300;400;700&display=swap" rel="stylesheet">
```

---

## 1. Hero Section

**Cuándo usarlo:** Encabezado principal de cualquier documento HTML (agenda, tutorial, resultados).

```html
<div class="hero">
    <img src="snowflake_logo_white.png" alt="Snowflake">
    <h1>{{TITULO}}</h1>
    <p>{{SUBTITULO}}</p>
    <div class="meta">
        <span>{{FECHA}}</span>
        <span>{{N_EQUIPOS}} equipos</span>
        <span>{{N_PARTICIPANTES}} participantes</span>
    </div>
</div>
```

```css
.hero {
    background: linear-gradient(135deg, var(--sf-gradient-start), var(--sf-gradient-end));
    color: #fff;
    padding: 50px 40px;
    text-align: center;
}
.hero img { width: 180px; margin-bottom: 15px; }
.hero h1 { font-size: 2rem; margin-bottom: 10px; font-weight: 700; }
.hero p { font-size: 1rem; opacity: 0.9; max-width: 700px; margin: 0 auto; }
.hero .meta {
    margin-top: 18px;
    display: flex;
    justify-content: center;
    gap: 30px;
    font-size: 0.85rem;
    opacity: 0.8;
}
```

---

## 2. Team Card (N dinámico)

**Cuándo usarlo:** Sección de equipos en la agenda. Se repite N veces según el número de equipos.

```html
<div class="teams-grid">
    <!-- Repetir por cada equipo -->
    <div class="team-card" style="border-top: 3px solid var(--green)">
        <h3>{{EQUIPO_NOMBRE}}</h3>
        <div class="team-cat">{{CATEGORIA}}</div>
        <ul class="team-members">
            <li>{{MIEMBRO_1}}</li>
            <li>{{MIEMBRO_2}}</li>
            <li>{{MIEMBRO_3}}</li>
        </ul>
        <div class="prompt-box">{{PROMPT_INICIAL}}</div>
    </div>
</div>
```

```css
.teams-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 16px;
}
.team-card {
    background: rgba(255,255,255,0.04);
    border: 1px solid rgba(255,255,255,0.08);
    border-radius: 10px;
    padding: 16px 20px;
}
.team-card h3 {
    font-family: 'Montserrat', sans-serif;
    font-size: 0.95rem;
    font-weight: 700;
    margin-bottom: 4px;
}
.team-cat {
    font-size: 0.8rem;
    color: #94a3b8;
    margin-bottom: 10px;
}
.team-members { list-style: none; }
.team-members li {
    font-size: 0.85rem;
    padding: 3px 0;
    color: #cbd5e1;
}
.team-members li::before { content: "• "; color: var(--sf-primary); }
```

**Nota:** Asignar colores rotativos al `border-top` de cada card usando las variables de color (`--green`, `--orange`, `--purple`, `--pink`, `--yellow`, `--teal`).

---

## 3. Agenda Block

**Cuándo usarlo:** Cada bloque de tiempo en la agenda del evento.

```html
<div class="agenda-item">
    <div class="time-badge" style="background:var(--green)">{{HORA_INICIO}}–{{HORA_FIN}}</div>
    <div class="agenda-content">
        <div>
            <div class="agenda-title">{{TITULO_BLOQUE}}</div>
            <div class="agenda-desc">{{DESCRIPCION}}</div>
        </div>
        <div class="agenda-dur">{{DURACION}}</div>
    </div>
</div>
```

```css
.agenda-item {
    display: flex;
    align-items: stretch;
    margin-bottom: 16px;
    border-radius: 10px;
    overflow: hidden;
    background: rgba(255,255,255,0.03);
    border: 1px solid rgba(255,255,255,0.06);
}
.time-badge {
    min-width: 120px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 0.85rem;
    color: #fff;
    padding: 16px 12px;
}
.agenda-content {
    flex: 1;
    padding: 16px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}
.agenda-title {
    font-family: 'Montserrat', sans-serif;
    font-size: 1.25rem;
    font-weight: 700;
}
.agenda-desc { font-size: 0.9rem; color: #94a3b8; margin-top: 4px; }
.agenda-dur { font-size: 0.95rem; font-weight: 700; color: var(--sf-primary); white-space: nowrap; }
```

**Patrón de colores por tipo de bloque:**
- Apertura/cierre → `--green`
- Tutorial/capacitación → `--orange`
- CoCo TIME (build) → `--purple`
- Demos → `--yellow`
- Premiación → `--sf-primary` (#29B5E8)

---

## 4. Prompt Box (Copyable)

**Cuándo usarlo:** Mostrar prompts sugeridos para CoCo o queries SQL que el usuario debe copiar.

```html
<div class="prompt-box">
    <span class="prompt-label">Prompt sugerido</span>
    <div class="code-block">{{PROMPT_TEXT}}
        <button class="copy-btn" onclick="copyCode(this)">Copiar</button>
    </div>
</div>
```

```css
.prompt-label {
    display: inline-block;
    background: var(--sf-primary);
    color: #fff;
    padding: 3px 10px;
    border-radius: 4px;
    font-size: 11px;
    font-weight: 600;
    margin-bottom: 8px;
}
.code-block {
    position: relative;
    background: var(--code-bg);
    border: 1px solid #333;
    border-radius: 8px;
    padding: 16px 20px;
    margin: 12px 0;
    overflow-x: auto;
    font-family: 'SF Mono', 'Fira Code', monospace;
    font-size: 12px;
    line-height: 1.5;
    white-space: pre-wrap;
    color: #e0e0e0;
}
.code-block .copy-btn {
    position: absolute;
    top: 8px; right: 8px;
    background: rgba(255,255,255,0.1);
    border: 1px solid rgba(255,255,255,0.3);
    border-radius: 4px;
    padding: 4px 10px;
    font-size: 11px;
    cursor: pointer;
    color: #ccc;
    transition: all 0.2s;
}
.code-block .copy-btn:hover {
    background: var(--sf-primary);
    color: #fff;
    border-color: var(--sf-primary);
}
```

**JavaScript requerido:**
```javascript
function copyCode(btn) {
    const block = btn.parentElement;
    const text = block.innerText.replace('Copiar', '').trim();
    navigator.clipboard.writeText(text);
    btn.textContent = '✓';
    setTimeout(() => btn.textContent = 'Copiar', 2000);
}
```

---

## 5. KPI Card

**Cuándo usarlo:** Resumen ejecutivo de métricas clave (equipos, créditos, soluciones, duración).

```html
<div class="kpi-grid">
    <div class="kpi-card">
        <div class="kpi-value">{{VALOR}}</div>
        <div class="kpi-label">{{ETIQUETA}}</div>
    </div>
    <div class="kpi-card">
        <div class="kpi-value">{{VALOR}}</div>
        <div class="kpi-label">{{ETIQUETA}}</div>
    </div>
    <!-- Repetir 3-6 KPIs -->
</div>
```

```css
.kpi-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
    gap: 16px;
    margin: 20px 0;
}
.kpi-card {
    background: #fff;
    border-radius: var(--radius);
    padding: 20px;
    text-align: center;
    box-shadow: 0 2px 8px rgba(0,0,0,0.06);
    border-top: 3px solid var(--sf-primary);
}
.kpi-value {
    font-family: 'Montserrat', sans-serif;
    font-size: 2rem;
    font-weight: 800;
    color: var(--sf-dark);
}
.kpi-label {
    font-size: 0.8rem;
    color: #666;
    margin-top: 4px;
    text-transform: uppercase;
    letter-spacing: 0.5px;
}
```

**KPIs típicos de un Cocothon:** Equipos, Participantes, Soluciones entregadas, Créditos consumidos, Duración, Categorías.

---

## 6. Evaluation Table

**Cuándo usarlo:** Tabla de criterios de evaluación con pesos porcentuales.

```html
<div class="criteria">
    <h2>Criterios de Evaluación</h2>
    <div class="criteria-item">
        <div class="criteria-badge" style="background:var(--green)">30%</div>
        <div class="criteria-text">Impacto de negocio</div>
    </div>
    <div class="criteria-item">
        <div class="criteria-badge" style="background:var(--sf-primary)">25%</div>
        <div class="criteria-text">Uso creativo de CoCo / AI</div>
    </div>
    <div class="criteria-item">
        <div class="criteria-badge" style="background:var(--orange)">25%</div>
        <div class="criteria-text">Demo funcional (corre en vivo)</div>
    </div>
    <div class="criteria-item">
        <div class="criteria-badge" style="background:var(--purple)">20%</div>
        <div class="criteria-text">Presentación y storytelling</div>
    </div>
</div>
```

```css
.criteria-item {
    display: flex;
    align-items: center;
    gap: 12px;
    margin-bottom: 10px;
}
.criteria-badge {
    min-width: 50px;
    padding: 5px 10px;
    border-radius: 6px;
    font-weight: 700;
    font-size: 0.8rem;
    text-align: center;
    color: #fff;
}
.criteria-text { font-size: 0.9rem; }
```

---

## 7. Credit Table

**Cuándo usarlo:** Reporte de consumo de créditos por cuenta/servicio en el documento de resultados.

```html
<table>
    <thead>
        <tr>
            <th>Cuenta / Servicio</th>
            <th>Créditos</th>
            <th>Tipo</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>{{NOMBRE}}</td>
            <td class="credit-total">{{CREDITOS}}</td>
            <td><span class="badge-ai">Cortex AI</span></td>
        </tr>
        <!-- Repetir por fila -->
    </tbody>
</table>
```

```css
table {
    width: 100%;
    border-collapse: collapse;
    margin: 12px 0;
    font-size: 0.85rem;
}
th {
    background: #f0f4f8;
    padding: 10px 12px;
    text-align: left;
    font-weight: 600;
    border-bottom: 2px solid #ddd;
    color: var(--sf-dark);
}
td {
    padding: 8px 12px;
    border-bottom: 1px solid #eee;
}
tr:hover td { background: #f9fbfd; }
.credit-total { font-weight: 700; color: var(--sf-dark); }
.badge-ai { background: #e8f4fd; color: var(--sf-dark); padding: 2px 8px; border-radius: 10px; font-size: 11px; font-weight: 600; }
.badge-coco { background: #e8f5e9; color: #2e7d32; padding: 2px 8px; border-radius: 10px; font-size: 11px; font-weight: 600; }
.badge-wh { background: #fff3e0; color: #e65100; padding: 2px 8px; border-radius: 10px; font-size: 11px; font-weight: 600; }
```

**Nota:** Usar `th { background: #f0f4f8 }` (NO `var(--code-bg)` que es oscuro). Los badges de tipo indican la categoría de consumo.

---

## 8. Info / Warning / Success Box

**Cuándo usarlo:** Notas contextuales, advertencias, o confirmaciones dentro de cualquier documento.

```html
<div class="info-box">
    <strong>Nota:</strong> {{TEXTO_INFORMATIVO}}
</div>

<div class="warning-box">
    <strong>Importante:</strong> {{TEXTO_ADVERTENCIA}}
</div>

<div class="success-box">
    <strong>Listo:</strong> {{TEXTO_CONFIRMACION}}
</div>
```

```css
.info-box, .warning-box, .success-box {
    padding: 14px 18px;
    border-radius: 8px;
    margin: 12px 0;
    font-size: 0.88rem;
    border-left: 4px solid;
}
.info-box { background: var(--info-blue); border-left-color: var(--info-border); }
.warning-box { background: var(--warning-orange); border-left-color: var(--warning-border); }
.success-box { background: var(--success-green); border-left-color: var(--success-border); }
.info-box strong, .warning-box strong, .success-box strong {
    display: block;
    margin-bottom: 4px;
}
```

**Guía de uso:**
- `info-box` → Datos adicionales, contexto, tips (azul)
- `warning-box` → Requisitos previos, limitaciones, precauciones (naranja)
- `success-box` → Confirmaciones, pasos completados, resultados positivos (verde)

---

## Resumen de Componentes por Output

| Output | Componentes que usa |
|--------|-------------------|
| Agenda HTML | hero-section, agenda-block, team-card, evaluation-table, prompt-box |
| Tutorial HTML | hero-section, code-block (prompt-box), info-box, warning-box, success-box |
| Resultados HTML | hero-section, kpi-card, credit-table, info-box, success-box |
| Propuesta HTML | hero-section, kpi-card, team-card, evaluation-table |
| Cheat Sheet | code-block (prompt-box), info-box, warning-box |
