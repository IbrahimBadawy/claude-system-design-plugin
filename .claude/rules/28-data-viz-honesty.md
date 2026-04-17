---
description: Charts and dashboards tell the truth. Bar charts start at 0. No chartjunk (3D, gradients, decorative patterns). Chart type matches data shape. Max 7 categorical colors. Every chart has a title, caption, and text alternative.
globs: "*.{tsx,jsx,vue,svelte,html}"
---

# Rule 28: Data Visualization Honesty

## The Rule

Every chart, dashboard, and KPI must tell the truth about the data — no visual
deception, no decoration that distorts, no misleading scales. Charts that lie are
worse than no charts.

## The 10 Commandments of Data Viz

1. **Bar charts start at 0.** No axis truncation to exaggerate differences.
2. **No 3D charts.** Perspective distorts, 3D adds ink, zero data value.
3. **No gradients on data elements.** Gradients imply a scale that isn't there.
4. **No decorative backgrounds or patterns** behind data marks.
5. **Chart type matches data shape:**
   - Comparison across categories → horizontal bar
   - Trend over time → line (or column if <5 points)
   - Composition → stacked bar or treemap (not pie for >5 slices)
   - Distribution → histogram or box plot
   - Relationship → scatter
   - Single value + trend → big number + sparkline + delta
6. **Max 7 categorical colors.** Beyond that, use patterns or small multiples.
7. **Color-blind safe palettes.** Never rely on red/green alone — add shape/pattern/label.
8. **Direct labeling over legends** where possible (put label on the line, not in a key).
9. **Gridlines off by default.** Turn them on only when precise reading is required.
10. **Every chart has a title + caption** ("so what?") and a text alternative (data table toggle).

## KPI Card Canonical Pattern

Every KPI widget follows this pattern (Stripe, Linear, Vercel standard):

```
┌──────────────────────────┐
│ Label (muted, small)     │
│ BIG NUMBER   ▲ +12%     │   ← delta in semantic color (green up, red down)
│ ╱╲╱╲╱╱╲╱╲╱╲╱           │   ← sparkline (7/30/90 days)
│ Period (muted, small)    │
└──────────────────────────┘
```

Required elements: label, big number, delta vs previous period, sparkline, period context.

## Dashboard Rules

- **Fit on one screen** — scroll is a dashboard design failure
- **Most important at top-left** (LTR) / top-right (RTL)
- **Supporting context flows down + right**
- **Filter state in URL** — dashboards must be shareable/bookmarkable
- **Per-widget states** — each widget handles its own loading/empty/error
- **Period-over-period comparison built-in** — every metric shows vs previous (WoW/MoM/YoY)
- **No scrolling for primary KPIs**

## What's Banned

- 3D charts in any form
- Pie charts with > 5 slices
- Pie charts for trend data
- Stacked areas for comparison (use grouped bars)
- Line charts between non-time points
- Y-axes starting at anything other than 0 on bar charts
- "Random" color palettes (must be categorical-safe or sequential or diverging)
- Chart titles that describe the chart ("Bar chart of sales") instead of the insight ("Sales up 12% YoY")
- Showing a mean when distribution is skewed — use median + IQR

## Accessibility (non-negotiable)

- Title + caption every chart — should make sense to a screen reader
- Alt text describing trend and key numbers
- Data table alternative ("View as table" toggle)
- Keyboard navigation — arrow keys step through data points
- Color-blind safe — add shape/pattern alongside color
- 4.5:1 contrast between data and background

## Complexity-Driven Scope

- **Simple:** basic charts with title/caption, no chartjunk
- **Medium:** above + KPI pattern + period comparison + filter URL state
- **Complex:** above + color-blind validation + keyboard nav + data-table toggles + anomaly detection + visual regression on charts

## Library Defaults (plugin)

- React: **Recharts** or **Tremor** (dashboard-focused, safe defaults)
- Vue: **vue-chartjs** or **Apache ECharts Vue**
- Angular: **ngx-charts** or **Angular Highcharts**
- Any: **Apache ECharts** (most features, enterprise)

Avoid raw D3 unless custom viz is needed — defaults make it easy to ship chartjunk.

## Enforcement

- `/frontend design` checks for dashboards and applies these rules
- `/ux-kit audit` flags charts without titles, captions, or alt text
- Lint: no `3d: true` in chart configs, no hardcoded hex in chart themes
- Review checklist before dashboard ships

## Why This Rule Exists

- A bad chart is worse than no chart — it misinforms faster than prose
- 3D + gradients + chartjunk = classic "business dashboard" look that users distrust
- Stephen Few, Tufte, and Knaflic all independently arrived at the same principles
  — this isn't taste, it's evidence
- Accessible charts are a legal requirement under ADA/EAA in many jurisdictions
- Shareable dashboards (URL state) = 10× more adoption than one-time views

## Escape Hatch

3D / gradient / decorative elements allowed ONLY in:
- Marketing / landing pages with illustrative (non-data) graphics
- Intentionally stylized data-art visualizations (with documentation noting it's not a business dashboard)
- Third-party dashboards embedded as iframes (out of our control)
