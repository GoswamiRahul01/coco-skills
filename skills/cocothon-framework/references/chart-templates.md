# Chart Templates para Resultados (Paso 8)

3 visualizaciones matplotlib para el reporte de resultados post-evento. Cada chart se genera como imagen PNG codificada en base64 para embeber directamente en el HTML.

---

## Setup Común

```python
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
from io import BytesIO
import base64
from datetime import datetime

# Snowflake brand colors
SF_COLORS = {
    'primary': '#11567F',
    'accent': '#29B5E8',
    'dark': '#0D2B3E',
    'light': '#E8F4F8',
    'gradient': ['#11567F', '#29B5E8', '#4DC9F6', '#7DD3FC', '#A5E1FA', '#C7EDFC']
}

# Palette extendida para múltiples categorías
CATEGORY_COLORS = ['#11567F', '#29B5E8', '#F59E0B', '#10B981', '#8B5CF6', '#EF4444']

def fig_to_base64(fig):
    """Convierte figura matplotlib a string base64 para embeber en HTML."""
    buf = BytesIO()
    fig.savefig(buf, format='png', dpi=150, bbox_inches='tight', 
                facecolor='white', edgecolor='none')
    buf.seek(0)
    img_base64 = base64.b64encode(buf.read()).decode('utf-8')
    plt.close(fig)
    return f"data:image/png;base64,{img_base64}"

# Font config
plt.rcParams.update({
    'font.family': 'sans-serif',
    'font.sans-serif': ['Helvetica', 'Arial', 'DejaVu Sans'],
    'font.size': 10,
    'axes.titlesize': 13,
    'axes.labelsize': 11,
})
```

---

## Chart 1: Pie Chart — Distribución de Créditos por Categoría

Muestra cómo se distribuyeron los créditos totales del evento entre tipos de servicio (Cortex Code, Warehouse, AI Functions, SPCS, etc.).

**Datos de entrada:** Agregado de `SNOWFLAKE.ACCOUNT_USAGE.METERING_HISTORY` de todas las cuentas.

```python
def chart_credit_distribution(credits_by_service: dict) -> str:
    """
    Genera pie chart de distribución de créditos por categoría de servicio.
    
    Args:
        credits_by_service: Dict con SERVICE_TYPE -> total_credits
            Ejemplo: {
                'CORTEX_CODE': 45.2,
                'WAREHOUSE_METERING': 32.1,
                'AI_SERVICES': 28.7,
                'SNOWPIPE_STREAMING': 5.3,
                'SERVERLESS_TASK': 12.0
            }
    
    Returns:
        String base64 de la imagen PNG para embeber en <img src="...">.
    """
    # Agrupar categorías menores en "Otros" si hay muchas
    labels = list(credits_by_service.keys())
    values = list(credits_by_service.values())
    total = sum(values)
    
    # Nombres más amigables para el reporte
    FRIENDLY_NAMES = {
        'CORTEX_CODE': 'Cortex Code',
        'WAREHOUSE_METERING': 'Warehouse',
        'AI_SERVICES': 'AI Functions',
        'SNOWPIPE_STREAMING': 'Snowpipe',
        'SERVERLESS_TASK': 'Tasks',
        'QUERY_ACCELERATION': 'Query Acceleration',
        'AUTO_CLUSTERING': 'Clustering',
    }
    labels = [FRIENDLY_NAMES.get(l, l.replace('_', ' ').title()) for l in labels]
    
    fig, ax = plt.subplots(1, 1, figsize=(7, 5))
    
    wedges, texts, autotexts = ax.pie(
        values,
        labels=labels,
        autopct=lambda pct: f'{pct:.1f}%\n({pct/100*total:.1f} cr)',
        colors=CATEGORY_COLORS[:len(values)],
        startangle=90,
        pctdistance=0.75,
        wedgeprops={'linewidth': 2, 'edgecolor': 'white'}
    )
    
    for autotext in autotexts:
        autotext.set_fontsize(9)
        autotext.set_fontweight('bold')
    
    ax.set_title(f'Distribución de Créditos — Total: {total:.1f} credits', 
                 fontweight='bold', pad=20)
    
    return fig_to_base64(fig)
```

---

## Chart 2: Bar Chart Horizontal — Créditos por Cuenta (Ordenado)

Muestra el consumo de cada equipo/cuenta ordenado de mayor a menor, para identificar los equipos más activos.

**Datos de entrada:** Total de créditos por account_id (de `METERING_HISTORY` por cuenta).

```python
def chart_credits_per_account(accounts_credits: dict) -> str:
    """
    Genera bar chart horizontal de créditos por cuenta (ordenado mayor a menor).
    
    Args:
        accounts_credits: Dict con team_name/account_id -> total_credits
            Ejemplo: {
                'Equipo Finanzas': 25.3,
                'Equipo Logística': 18.7,
                'Equipo RRHH': 15.2,
                'Equipo Comercial': 22.1,
                'Equipo Calidad': 12.8
            }
    
    Returns:
        String base64 de la imagen PNG.
    """
    # Ordenar de mayor a menor
    sorted_accounts = dict(sorted(accounts_credits.items(), key=lambda x: x[1]))
    
    labels = list(sorted_accounts.keys())
    values = list(sorted_accounts.values())
    total = sum(values)
    
    fig, ax = plt.subplots(1, 1, figsize=(8, max(4, len(labels) * 0.7)))
    
    bars = ax.barh(labels, values, color=SF_COLORS['accent'], 
                   edgecolor=SF_COLORS['primary'], linewidth=0.5, height=0.6)
    
    # Etiquetas de valor en cada barra
    for bar, val in zip(bars, values):
        pct = (val / total) * 100
        ax.text(bar.get_width() + 0.3, bar.get_y() + bar.get_height()/2,
                f'{val:.1f} cr ({pct:.0f}%)', va='center', fontsize=9)
    
    ax.set_xlabel('Créditos Consumidos')
    ax.set_title('Consumo por Equipo', fontweight='bold', pad=15)
    ax.set_xlim(0, max(values) * 1.3)
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)
    ax.axvline(x=total/len(labels), color=SF_COLORS['primary'], 
               linestyle='--', alpha=0.5, label=f'Promedio: {total/len(labels):.1f} cr')
    ax.legend(loc='lower right', fontsize=9)
    
    return fig_to_base64(fig)
```

---

## Chart 3: Timeline — Actividad por Hora del Evento

Muestra la distribución temporal de queries durante el evento, útil para ver cuándo hubo más actividad (típicamente picos al inicio del hackathon y antes de las presentaciones).

**Datos de entrada:** Timestamps de queries de `QUERY_HISTORY` de todas las cuentas.

```python
def chart_activity_timeline(query_timestamps: list, event_start: str, event_end: str) -> str:
    """
    Genera timeline de actividad (queries) por hora del evento.
    
    Args:
        query_timestamps: Lista de datetime objetos (START_TIME de cada query)
            Ejemplo: [datetime(2026, 7, 2, 9, 15), datetime(2026, 7, 2, 9, 16), ...]
        event_start: Hora inicio del evento "HH:MM" (para anotar)
        event_end: Hora fin del evento "HH:MM" (para anotar)
    
    Returns:
        String base64 de la imagen PNG.
    """
    if not query_timestamps:
        return ""
    
    # Agrupar por intervalos de 15 minutos
    min_time = min(query_timestamps)
    max_time = max(query_timestamps)
    
    # Crear bins de 15 min
    bins = []
    counts = []
    current = min_time.replace(minute=(min_time.minute // 15) * 15, second=0, microsecond=0)
    
    while current <= max_time:
        next_bin = current.replace(minute=current.minute + 15) if current.minute < 45 \
                   else current.replace(hour=current.hour + 1, minute=0)
        count = sum(1 for t in query_timestamps if current <= t < next_bin)
        bins.append(current)
        counts.append(count)
        current = next_bin
    
    fig, ax = plt.subplots(1, 1, figsize=(10, 4))
    
    ax.fill_between(bins, counts, alpha=0.3, color=SF_COLORS['accent'])
    ax.plot(bins, counts, color=SF_COLORS['primary'], linewidth=2, marker='o', markersize=4)
    
    # Formatear eje X
    ax.xaxis.set_major_formatter(mdates.DateFormatter('%H:%M'))
    ax.xaxis.set_major_locator(mdates.MinuteLocator(interval=30))
    plt.xticks(rotation=45)
    
    # Anotaciones de fases del evento
    ax.set_xlabel('Hora')
    ax.set_ylabel('Queries ejecutados (por 15 min)')
    ax.set_title('Actividad Durante el Evento', fontweight='bold', pad=15)
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)
    ax.grid(axis='y', alpha=0.3)
    
    # Anotar inicio y fin
    peak_idx = counts.index(max(counts))
    ax.annotate(f'Pico: {max(counts)} queries', 
                xy=(bins[peak_idx], max(counts)),
                xytext=(10, 10), textcoords='offset points',
                fontsize=9, fontweight='bold', color=SF_COLORS['primary'],
                arrowprops=dict(arrowstyle='->', color=SF_COLORS['primary']))
    
    return fig_to_base64(fig)
```

---

## Integración en HTML

Para embeber los charts en el reporte de resultados:

```html
<div class="chart-section">
    <h3>Distribución de Consumo</h3>
    <img src="{{ PIE_CHART_BASE64 }}" alt="Distribución de créditos por categoría" 
         style="max-width: 100%; height: auto;">
</div>

<div class="chart-section">
    <h3>Consumo por Equipo</h3>
    <img src="{{ BAR_CHART_BASE64 }}" alt="Créditos por equipo" 
         style="max-width: 100%; height: auto;">
</div>

<div class="chart-section">
    <h3>Actividad del Evento</h3>
    <img src="{{ TIMELINE_CHART_BASE64 }}" alt="Timeline de actividad" 
         style="max-width: 100%; height: auto;">
</div>
```

---

## Notas

- Todos los charts usan `facecolor='white'` para mantener fondo limpio en HTML
- DPI 150 da buena calidad sin archivos enormes (~50-100KB por chart)
- Los colores siguen la paleta Snowflake (`#11567F` primary, `#29B5E8` accent)
- Si un chart no aplica (ej: solo 1 cuenta), omitir en vez de generar con datos insuficientes
- Mínimo 2 cuentas para bar chart, mínimo 10 queries para timeline
