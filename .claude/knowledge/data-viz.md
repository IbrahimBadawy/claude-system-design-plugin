# Dashboards & Data Visualization

> Canonical principles from Stephen Few (*Information Dashboard Design*),
> Cole Nussbaumer Knaflic (*Storytelling with Data*), Edward Tufte
> (*The Visual Display of Quantitative Information*), plus modern dashboard
> patterns from Stripe, Linear, Vercel, Observable Plot.

Use when: designing a dashboard, picking a chart type, auditing data-viz quality,
or enforcing "no chartjunk" on generated output.

---

## Stephen Few — Information Dashboard Design

A dashboard is "a visual display of the most important information needed to achieve
one or more objectives that fits entirely on a single screen." The 10 canonical rules:

1. **Fit on one screen** — no scrolling for primary KPIs
2. **Exceed the bounds of a single screen** — #1 mistake
3. **No excessive detail or precision** — `$4.2M` beats `$4,237,582.34`
4. **Misleading measures** — avoid 3D, truncated axes, inconsistent scales
5. **Chartjunk** — remove gridlines, background fills, 3D effects, gradients on data
6. **Overuse of color** — color should encode meaning, not decorate
7. **Ineffective highlighting** — use color/weight sparingly so it actually draws attention
8. **Cluttering with useless decoration** — every pixel must earn its place
9. **Meaningless variety** — don't mix 8 chart types on one screen
10. **Misplaced/poorly arranged data** — most important = top-left (LTR); supporting context flows down/right

Source: https://www.perceptualedge.com/library.php

---

## Cole Nussbaumer Knaflic — Storytelling with Data

Six practical lessons:

1. **Understand the context** — who is the audience, what decision must they make?
2. **Choose appropriate visual** — table for lookup, chart for pattern. Default: horizontal bar chart
3. **Eliminate clutter** — every non-data element is suspect. Remove borders, gridlines, tick marks, legends when possible
4. **Focus attention with pre-attentive attributes** — color, size, position process in <500ms. Use ONE per chart for emphasis
5. **Think like a designer** — alignment, white space, consistent typography, restraint with color
6. **Tell a story** — annotate the "so what." Charts without captions force readers to guess

### Pre-attentive Attributes (use 1-2 max per chart)

Color (hue/intensity), size, length, position, orientation, enclosure.

Source: https://www.storytellingwithdata.com/

---

## Edward Tufte — The Visual Display of Quantitative Information

1. **Data-ink ratio** — maximize ink devoted to data, minimize everything else. Erase any ink that, if removed, wouldn't lose information
2. **Chartjunk** — decorative graphics that don't encode data are noise (drop shadows, 3D, patterns, clip art)
3. **Small multiples** — many small versions of the same chart beat one complex chart. Enables direct visual comparison
4. **Sparklines** — word-sized graphics embedded inline with text/KPI cards. Show trend at a glance
5. **Lie factor** — `(size of effect shown in graphic) / (size of effect in data) ≈ 1.0`. Bar charts start at 0, no axis truncation
6. **High data density** — aim for many data points per square inch when the info is meaningful

Source: https://www.edwardtufte.com/tufte/books_vdqi

---

## Chart Selection Matrix

Canonical reference (Abela's Chart Chooser + modern practice):

| Goal | Data Shape | Best Chart | Avoid |
|------|-----------|-----------|-------|
| **Comparison** (few categories) | Discrete values across categories | Horizontal bar | Pie for >3 slices |
| **Comparison** (over time) | Values across time periods | Line (5+ points), column (<5) | Stacked areas for comparison |
| **Composition** (static) | Parts of whole | Stacked bar, treemap, waffle | Pie with >5 slices |
| **Composition** (over time) | How parts change | Stacked area, 100% stacked bar | Overlapping areas |
| **Distribution** (1 var) | Spread of values | Histogram, box plot | Bar without binning |
| **Distribution** (2 vars) | Correlation density | Scatter, hexbin, heatmap | 3D plots |
| **Relationship** | Correlation | Scatter (2 vars), bubble (3 vars) | Lines between non-time points |
| **Trend** | Time series | Line, area (cumulative) | Bar for dense time series |
| **Geographic** | Regions + metric | Choropleth, symbol map | 3D globes for analytics |
| **Flow** | Transitions between states | Sankey, chord | Multiple pies |
| **Hierarchy** | Nested composition | Treemap, sunburst | Nested pies |
| **KPI (single value)** | Current state + trend | Big number + sparkline + delta | Gauge (except fuel-style UI) |

---

## Modern Dashboard Patterns (2024-2026)

### 1. KPI Card — the canonical unit

```
┌──────────────────────────┐
│ Revenue                  │  ← Label (muted)
│ $4.2M            ▲ +12%  │  ← Big number + delta (colored)
│ ╱╲╱╲╱╱╲╱╲╱╲╱╲╱╲╱╲╱      │  ← Sparkline (last 30 days)
│ Last 30 days             │  ← Period context (muted)
└──────────────────────────┘
```

Standardized across Stripe, Linear, Vercel. Rules:
- Big number uses a display font, weight 600-700
- Delta uses semantic color (green up, red down)
- Sparkline spans 7/30/90 days
- Period context always shown
- Click-through opens the detail view

### 2. Sparklines inline

Always pair with numeric values in tables; show 7/30/90 day trend. Width: 60-120px.

### 3. Anomaly highlighting

Automatic outlier detection with colored markers/bands:
- Red dot for spikes
- Amber band for degraded period
- Gray band for data gaps

### 4. Drill-down progression

Overview → segment → detail. Never start at detail.

### 5. Period-over-period comparison built-in

Every metric shows current vs previous (WoW, MoM, YoY) by default.

### 6. Filter persistence

Filter state lives in URL params → shareable, bookmarkable dashboards.

### 7. Per-widget states

Each widget handles its own loading/empty/error independently. One widget's failure
doesn't break the dashboard.

---

## Color for Data Viz

### Categorical palettes (for discrete groups)

- Max **7 colors** — beyond that, use patterns or small multiples
- Use color-blind-safe palettes (e.g., Tableau's 10-color palette, Viridis for sequential)
- Test with color-blind simulators (Stark, Colorblindly)

### Sequential palettes (ordered data)

- Single hue, varying lightness (e.g., light-to-dark blue)
- Viridis, Magma, Cividis for perceptual uniformity

### Diverging palettes (data with a meaningful midpoint)

- Two hues on either side of a neutral (e.g., red-white-blue)
- Use when midpoint matters (e.g., temperature vs average, profit vs loss)

### Semantic colors in charts

- Success/growth: green
- Warning/caution: amber
- Error/decline: red
- Info/neutral: blue

Never use red/green as the ONLY distinguisher (color-blind accessibility) — always add
a shape, label, or pattern too.

---

## Accessibility for Data Viz

1. **Title + caption every chart** — the chart should still make sense to a screen reader
2. **Alt text** describing trend and key numbers: `"Revenue chart showing 12% growth from March to April, peaking at $4.5M on April 12"`
3. **Data table alternative** — offer "view as table" toggle; screen readers can't interpret SVG paths
4. **Keyboard navigation** — arrow keys to step through data points
5. **Color-blind safe** — add shape/pattern alongside color
6. **4.5:1 contrast** between data and background

---

## 15 Auto-Enforce Dashboard Rules

When `/implement` or `/frontend design` generates a dashboard:

1. No 3D effects anywhere
2. No gradients on data elements (backgrounds OK)
3. Bar charts start at 0 (no truncated axes)
4. Gridlines off by default; turn on only if precise reading required
5. No more than 7 categorical colors per chart
6. Color-blind-safe palette selected
7. Sparklines inline in tables
8. KPI cards follow the canonical pattern (number + delta + spark + period)
9. Period-over-period comparison on every metric
10. Filter state in URL params (shareable)
11. Per-widget loading/empty/error states
12. Alt text + data table toggle on every chart
13. Max content width so layouts don't stretch unreadably on ultra-wide screens
14. Keyboard-navigable charts
15. Respect `prefers-reduced-motion` for animated entries

---

## Chart Libraries (2026)

| Library | Best For | Framework |
|---------|---------|-----------|
| **Recharts** | React dashboards, declarative composable | React |
| **Tremor** | Dashboard-focused (KPI cards, charts built in) | React |
| **Chart.js** | Simple charts, stable, framework-agnostic | Any |
| **ECharts (Apache)** | Large data, rich features, enterprise | Any |
| **D3.js** | Custom visualizations, full control | Any |
| **Observable Plot** | Quick exploratory, grammar-of-graphics | Any |
| **Nivo** | Beautiful defaults, React | React |
| **Visx (Airbnb)** | D3 + React, low-level | React |
| **Victory** | React charting, good docs | React |
| **Chart.js-Vue** / **Vue-ChartJS** | Vue charts | Vue |
| **ngx-charts** / **ng2-charts** | Angular charts | Angular |

**Default for SaaS dashboards:** Recharts or Tremor (React), Nivo (if beauty matters more than flexibility).

Sources:
- Stripe Dashboard patterns: https://stripe.com/docs/dashboard
- Observable Plot best practices: https://observablehq.com/plot/
- Material Design data viz: https://m3.material.io/foundations/data-visualization/overview
