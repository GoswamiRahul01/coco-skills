# Chart Generation Reference

Generate visual charts for the POC readout using matplotlib. Charts are saved as PNG images and embedded in the HTML as `<img>` tags.

## When to Use

- Config menu: `chart_generation: true`
- Requires: Python with matplotlib installed
- Alternative: If matplotlib unavailable, fall back to CSS-only charts in the component-library

## Chart 1: Daily Credit Trend

Line chart showing daily credit consumption over the POC period.

```python
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
from datetime import datetime

# Data from Query 2.3 (credits_by_day)
dates = [datetime.strptime(d, '%Y-%m-%d') for d in date_strings]
credits = credit_values  # list of floats

fig, ax = plt.subplots(figsize=(10, 4))
ax.fill_between(dates, credits, alpha=0.15, color='#29B5E8')
ax.plot(dates, credits, color='#29B5E8', linewidth=2, marker='o', markersize=4)

ax.set_xlabel('Date', fontsize=11, color='#333333')
ax.set_ylabel('Credits', fontsize=11, color='#333333')
ax.set_title('Daily Credit Consumption', fontsize=14, fontweight='bold', color='#11567F', pad=15)

ax.xaxis.set_major_formatter(mdates.DateFormatter('%b %d'))
ax.xaxis.set_major_locator(mdates.WeekdayLocator(interval=1))
plt.xticks(rotation=45, ha='right')

ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['left'].set_color('#E0E7EF')
ax.spines['bottom'].set_color('#E0E7EF')
ax.tick_params(colors='#8A999E')
ax.grid(axis='y', alpha=0.3, color='#E0E7EF')

plt.tight_layout()
fig.savefig('chart_daily_credits.png', dpi=150, bbox_inches='tight', transparent=False, facecolor='white')
plt.close()
```

## Chart 2: Credit Distribution by Warehouse (Donut)

Donut chart showing credit allocation across warehouses.

```python
fig, ax = plt.subplots(figsize=(6, 6))

colors = ['#29B5E8', '#11567F', '#00C9A7', '#FF9F36', '#7D44CF', '#E74C3C', '#8A999E']
warehouses = warehouse_names      # list of strings
credits = warehouse_credits       # list of floats

wedges, texts, autotexts = ax.pie(
    credits,
    labels=warehouses,
    autopct='%1.1f%%',
    colors=colors[:len(warehouses)],
    startangle=90,
    pctdistance=0.75,
    wedgeprops=dict(width=0.4, edgecolor='white', linewidth=2)
)

for text in texts:
    text.set_fontsize(10)
    text.set_color('#333333')
for autotext in autotexts:
    autotext.set_fontsize(9)
    autotext.set_fontweight('bold')
    autotext.set_color('#333333')

ax.set_title('Credits by Warehouse', fontsize=14, fontweight='bold', color='#11567F', pad=20)

plt.tight_layout()
fig.savefig('chart_warehouse_credits.png', dpi=150, bbox_inches='tight', transparent=False, facecolor='white')
plt.close()
```

## Chart 3: POC Health Score Gauge

Semi-circle gauge for the POC health score.

```python
import numpy as np

fig, ax = plt.subplots(figsize=(5, 3.5))

score = health_score  # 0-100
max_score = 100

# Background arc
theta = np.linspace(np.pi, 0, 100)
ax.plot(np.cos(theta), np.sin(theta), color='#E0E7EF', linewidth=20, solid_capstyle='round')

# Score arc
score_pct = score / max_score
theta_score = np.linspace(np.pi, np.pi - (np.pi * score_pct), 100)
color = '#00C9A7' if score >= 75 else '#FF9F36' if score >= 50 else '#E74C3C'
ax.plot(np.cos(theta_score), np.sin(theta_score), color=color, linewidth=20, solid_capstyle='round')

# Score text
ax.text(0, 0.15, str(score), fontsize=48, fontweight='bold', ha='center', va='center', color='#11567F')
ax.text(0, -0.15, 'POC Health Score', fontsize=12, ha='center', va='center', color='#8A999E')

ax.set_xlim(-1.3, 1.3)
ax.set_ylim(-0.4, 1.3)
ax.set_aspect('equal')
ax.axis('off')

plt.tight_layout()
fig.savefig('chart_health_score.png', dpi=150, bbox_inches='tight', transparent=False, facecolor='white')
plt.close()
```

## Chart 4: Feature Adoption Heatmap (Peer Comparison)

Horizontal bar chart comparing feature adoption.

```python
fig, ax = plt.subplots(figsize=(8, 4))

features = feature_names           # list of strings
adoption_pct = adoption_values     # list of floats (0-100)
customer_has = has_feature_flags   # list of bools

bars = ax.barh(features, adoption_pct, color='#E0E7EF', height=0.6, edgecolor='none')

for i, (bar, has) in enumerate(zip(bars, customer_has)):
    if has:
        bar.set_color('#29B5E8')
        ax.text(adoption_pct[i] + 2, i, '  You', va='center', fontsize=9, color='#11567F', fontweight='bold')

ax.set_xlabel('% of Peers Using', fontsize=11, color='#333333')
ax.set_title('Feature Adoption vs. Industry Peers', fontsize=14, fontweight='bold', color='#11567F', pad=15)

ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines['left'].set_color('#E0E7EF')
ax.spines['bottom'].set_color('#E0E7EF')
ax.tick_params(colors='#8A999E')
ax.set_xlim(0, 110)
ax.invert_yaxis()

plt.tight_layout()
fig.savefig('chart_feature_adoption.png', dpi=150, bbox_inches='tight', transparent=False, facecolor='white')
plt.close()
```

## Embedding in HTML

```html
<figure class="screenshot">
  <img src="chart_daily_credits.png" alt="Daily credit consumption trend" class="screenshot-img">
  <figcaption class="screenshot-caption">Figure: Daily credit consumption during POC period</figcaption>
</figure>
```

## Styling Rules

All charts must follow the Snowflake brand palette:
- Primary: `#29B5E8` (sf-blue)
- Dark accent: `#11567F` (sf-mid-blue)
- Success: `#00C9A7`
- Warning: `#FF9F36`
- Error: `#E74C3C`
- Gray: `#8A999E`
- Light: `#F4F7FA`
- Border: `#E0E7EF`
- Body text: `#333333`
- Background: white

## Fallback (No matplotlib)

If matplotlib is not available, use CSS-only bar charts from the component library. The `credit-table` component with inline `width` bars provides a reasonable alternative:

```html
<td style="width:200px;">
  <div style="background:#29B5E8; height:20px; width:{{PCT}}%; border-radius:4px;"></div>
</td>
```
