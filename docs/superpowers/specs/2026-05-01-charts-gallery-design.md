---
name: Charts Component Gallery
description: Interactive component showcase of 13+ chart types with Ulbra branding, animations, and filters
type: specification
---

# Charts Component Gallery — Design Specification

**Document**: charts.html component showcase  
**Version**: 1.0  
**Date**: 2026-05-01  
**Status**: Design approved, ready for implementation

---

## Executive Summary

Build an interactive **Component Gallery** for data visualizations integrated with Ulbra's design system. This serves as a living reference and copy-paste library for charts, KPIs, and dashboards using modern animation libraries.

**Scope**: Single-page `charts.html` with 13+ chart components, global filters, draggable KPI grid, and export functionality.

---

## Design Principles

1. **Educational**: Each component is self-contained with code examples
2. **Modern**: Latest libraries (ECharts, Framer Motion, GSAP) with smooth animations
3. **Branded**: All colors, typography, spacing use Ulbra design tokens
4. **Responsive**: Works on mobile, tablet, desktop
5. **Interactive**: Filters, tooltips, drag-and-drop, hover effects
6. **Copy-paste Ready**: Code samples for each component are ready to use

---

## Page Architecture

### Layout Structure

```
Header (Topbar)
  ├─ Ulbra logo + branding
  ├─ Navigation pills (Identity, Color, Typography, ... Charts)
  └─ Metadata (version, status)

Hero Section
  ├─ Title: "Componentes de Gráficos"
  ├─ Subtitle: "Biblioteca reutilizável para dashboards educacionais"
  └─ Ruler (HUD-style metadata)

Global Filter Bar
  ├─ Time Period selector (Ano, Semestre)
  ├─ Campus selector (Canoas, Manaus, Palmas, etc.)
  ├─ Program Type selector (Graduação, Pós-Graduação, EAD, Medicina)
  └─ All filters affect every chart below

Chart Sections (4 main groups)
  ├─ Section 1: Common Charts (5 components)
  ├─ Section 2: KPI & Indicators (4 components)
  ├─ Section 3: Advanced Patterns (4 components)
  └─ Section 4: Interactive KPI Grid

Footer
  └─ Ulbra branding + resources
```

---

## Component Categories (13+ Total)

### **Section 1: Common Charts (5)**

#### 1.1 Line Chart
- **Data**: Student enrollments by semester (2020–2026)
- **Library**: ECharts
- **Customization**: Multiple lines per program type
- **Animation**: Smooth line draw on load, hover highlight
- **Tooltip**: Styled with Ulbra gold, shows value + delta
- **Code block**: React/HTML snippet for copy

#### 1.2 Bar Chart
- **Data**: Student count by campus (horizontal bars)
- **Library**: ECharts
- **Customization**: Stacked or grouped bars
- **Animation**: Bar expansion on load, quick transitions on filter
- **Tooltip**: Absolute + percentage
- **Export**: PNG download button

#### 1.3 Area Chart
- **Data**: Annual growth trends (filled area under curve)
- **Library**: ECharts
- **Customization**: Single or stacked areas
- **Animation**: Gradient fill from bottom, fade-in
- **Tooltip**: Point hover with crosshair
- **Interactive**: Click legend to toggle series

#### 1.4 Pie Chart
- **Data**: Program distribution (Grad, Pós, EAD, Medicina)
- **Library**: ECharts
- **Customization**: Donut variant available
- **Animation**: Slice rotation + explode on hover
- **Tooltip**: Percentage + absolute count
- **Label**: Positioned outside, with leader lines

#### 1.5 Donut Chart
- **Data**: Course completion rates by program
- **Library**: ECharts
- **Customization**: Center label shows total
- **Animation**: Subtle pulse at center label
- **Tooltip**: Rate + count
- **Interactive**: Hover slice highlights

---

### **Section 2: KPI & Indicators (4)**

#### 2.1 Gauge Chart
- **Data**: Overall approval rate (0–100%)
- **Library**: Custom SVG + GSAP
- **Style**: Curved gauge with Ulbra colors (green → gold → bronze)
- **Animation**: Needle rotates to value on load (1.2s easing)
- **Label**: Large number in center, percentage below
- **Tooltip**: Benchmark comparison ("vs. 88% last year")

#### 2.2 Progress Bar
- **Data**: Current semester completion (0–100%)
- **Library**: HTML + Framer Motion
- **Style**: Rounded bar, filled with primary gradient
- **Animation**: Width animates from 0 on load (0.6s), smooth
- **Label**: Above (title), right (percentage)
- **Tooltip**: Weeks remaining, estimated completion date

#### 2.3 Stat Card
- **Data**: Total active students
- **Library**: HTML + CSS + Framer Motion
- **Style**: Large number, small label, icon
- **Animation**: Number counts up on load (1s duration)
- **Comparison**: Shows delta vs. last period (↑2.3%)
- **Interactive**: Click to drill down (shows breakdown by program)

#### 2.4 Trend Card
- **Data**: YoY enrollment growth
- **Library**: HTML + GSAP
- **Style**: Large metric, trend arrow (↑/↓), color coded
- **Animation**: Arrow bounces, number has easing
- **Label**: Trend description ("Growing 12% vs. last year")
- **Tooltip**: Breakdown by cohort

---

### **Section 3: Advanced Patterns (4)**

#### 3.1 Heatmap
- **Data**: Enrollments by month × campus (12 months, 7 campuses)
- **Library**: Nivo or ECharts
- **Color scale**: Green (low) → Gold (medium) → Bronze (high)
- **Animation**: Cells fade in staggered (50ms each)
- **Tooltip**: Exact values + % of total
- **Interactive**: Hover highlights row + column

#### 3.2 Scatter Plot
- **Data**: Student cost per capitation vs. graduation rate
- **Library**: ECharts
- **Axes**: X = cost, Y = rate; bubble size = cohort size
- **Animation**: Points animate in from center
- **Color**: By program type (different hues of Ulbra palette)
- **Tooltip**: Name, cost, rate, cohort size
- **Trend line**: Optional linear regression

#### 3.3 Sankey Diagram
- **Data**: Flow: Inquiries → Applications → Approved → Enrolled
- **Library**: ECharts
- **Colors**: Progressive (inquiries=light gold, enrolled=deep green)
- **Animation**: Paths draw in sequence
- **Tooltip**: Shows volume + % of previous stage
- **Interactive**: Hover node highlights connected flows

#### 3.4 Radial/Polar Chart
- **Data**: Distribution by field of study (8 major areas)
- **Library**: ECharts
- **Type**: Polygon or circular bars
- **Animation**: Bars grow outward from center
- **Color**: Each area gets unique Ulbra palette color
- **Tooltip**: Area name, student count, % of total
- **Label**: Areas labeled on perimeter

---

### **Section 4: Interactive KPI Grid**

#### 4.1 Draggable Dashboard
- **Data**: 6 KPI cards (arbitrary selection from sections above)
- **Layout**: CSS Grid, 2×3 responsive
- **Interactive Elements**:
  - Drag cards to reorder (Framer Motion)
  - Drop snaps to grid position
  - localStorage saves order (key: `ulbra-kpi-grid-layout`)
  - Reset button restores default

- **Card Contents**:
  1. Total Students (Stat Card)
  2. Approval Rate (Gauge)
  3. Completion % (Progress)
  4. Enrollment Trend (Trend Card)
  5. Graduation Rate (Stat Card)
  6. Cohort Distribution (Mini Pie)

- **Animation**:
  - Drag: Shadow grows, scale +3%, opacity on siblings
  - Drop: Bounce ease landing
  - Reorder: Cards shift smoothly
  - Hover: Subtle lift + cursor hint

- **Documentation**:
  - Code block showing how to implement draggable grid in own project
  - API: `<DraggableGrid items={[...]} onReorder={...} />`

---

## Global Filters

### Filter Bar (Fixed below hero, above sections)

**Inputs:**
1. **Time Period** (Select)
   - Options: 2020, 2021, 2022, 2023, 2024, 2025, 2026
   - Or: "2026 S1", "2026 S2" (semesters)
   - Default: Current year

2. **Campus** (Multi-select or dropdown)
   - Options: Canoas RS, Manaus AM, Palmas TO, etc.
   - Default: All

3. **Program Type** (Multi-select or dropdown)
   - Options: Graduação, Pós-Graduação, EAD, Medicina
   - Default: All

**Behavior:**
- Filters affect ALL charts below
- On filter change:
  - Charts fadeOut (200ms)
  - Data updates
  - Charts fadeIn (400ms) with staggered enter animations
- Filter state persists in URL query params (optional) or session storage

**Styling:**
- Flex layout, horizontally aligned
- Use Ulbra button styles (primary/ghost for clear/reset)
- Gold accents on active selections

---

## Data & Fixtures

### Data Sources (All Mocked)

**fixtures.js structure:**
```javascript
{
  students: [
    { id, name, campus, program, enrollmentYear, graduationYear, ... },
    ...
  ],
  enrollments: [
    { month, year, campus, program, count },
    ...
  ],
  programs: [
    { id, name, type, campuses, ... },
    ...
  ],
  // etc.
}
```

**Realistic Data:**
- ~50k total students across 7 campuses
- 3 program types (Grad, Pós, EAD) + Medicina (separate)
- Enrollment trends show growth 2020–2026
- Completion rates 85–95% (realistic for university)
- Geographically distributed

**Filter Logic:**
- All data filtered client-side (no backend needed)
- Memoized to avoid recalculations on every filter change

---

## Interactions & Animations

### Chart-Level Animations

| Event | Animation | Duration | Easing |
|-------|-----------|----------|--------|
| Load | Fade-in + draw (lines/bars grow) | 0.8s | cubicBezier |
| Filter change | Fade out → update → fade in | 0.4s+0.4s | standard |
| Hover | Highlight series, show tooltip | instant | - |
| Click legend | Toggle series on/off | 0.3s | smooth |

### Component-Level Animations

- **Stat Cards**: Number counts up (0 to value in 1s)
- **Gauge**: Needle rotates from 0 (1.2s)
- **Progress**: Width animates 0→100% (0.8s, bounce end)
- **Heatmap**: Cells fade in staggered (50ms offset)
- **Sankey**: Paths draw one-by-one (200ms each)
- **Drag**: Shadow expands, scale +3%, smooth return on drop

### Hover States

- Charts: Point highlight, crosshair, tooltip appears
- Bars/segments: Slight scale, outline color change
- KPI Grid cards: Lift (+2px), shadow expands
- Buttons: Color transition (100ms)

---

## Export Functionality

### Export Options (All Charts)

1. **PNG Download**
   - Button: "Export as PNG"
   - Uses html2canvas library
   - Resolution: 1200×800 (configurable)
   - Filename: `chart-name-YYYY-MM-DD.png`

2. **Code Copy**
   - Button: "Copy Code"
   - Copies component code snippet to clipboard
   - Toast notification confirms

3. **Share (Optional)**
   - Button: "Share"
   - Generates short URL with encoded filter state
   - Copy link to clipboard

---

## Technical Implementation

### Libraries & Versions

```json
{
  "echarts": "^5.4.0",
  "nivo": "^0.80.0",
  "framer-motion": "^10.16.0",
  "gsap": "^3.12.0",
  "html2canvas": "^1.4.0"
}
```

### File Structure

```
charts.html (single HTML file with embedded CSS/JS)
│
├── <head>
│   ├── colors_and_type.css (Ulbra tokens)
│   └── <style> charts.css (scoped to this page)
│
├── <body>
│   ├── Topbar (copy from brandbook.html)
│   ├── Hero section
│   ├── Filters bar
│   ├── Chart sections (1–4)
│   └── Footer (copy from brandbook.html)
│
└── <script>
    ├── chart-components.js (ECharts instances)
    ├── kpi-grid.js (Framer Motion draggable)
    ├── filters.js (state management)
    ├── animations.js (GSAP + motion helpers)
    ├── fixtures.js (mock data)
    └── main.js (initialization)
```

### Responsive Breakpoints

- **Mobile** (<640px): 1 column, full-width cards
- **Tablet** (640–1024px): 2 columns
- **Desktop** (>1024px): 2 columns (common charts), 2 columns (KPIs), 2 columns (advanced)

### Performance Considerations

- Lazy load ECharts instances on scroll into view
- Memoize filter results (useMemo or vanilla JS equivalent)
- Debounce window resize (250ms)
- Use will-change CSS for animated elements
- Throttle scroll events for animations

---

## Color & Typography

### Colors (From colors_and_type.css)

- **Primary**: `--ulbra-primary-deep` (#0D3634)
- **Secondary**: `--ulbra-primary` (#045A52)
- **Accent**: `--ulbra-gold-light` (#F5CE99), `--ulbra-gold` (#CCA269)
- **Bronze**: `--ulbra-bronze` (#66563D)
- **Neutral**: `--surface-line`, `--surface`

### Fonts

- **Display**: Playfair Display (600) for section titles
- **Text**: Inter (400–600) for body, labels, tooltips
- **Mono**: JetBrains Mono (500) for code blocks

### Spacing

- Sections: 96px top/bottom padding (from brandbook)
- Cards: 24px gap
- Section meta: Eyebrow + ruler pattern

---

## Accessibility & UX

- Keyboard navigation: Tab through filters, buttons
- ARIA labels on charts for screen readers
- High contrast: All text meets WCAG AA
- Tooltip delays: 200ms (avoid visual noise)
- Focus visible: 2px gold outline
- Color not sole indicator: Icons + text for status

---

## Success Criteria

✅ All 13+ chart types display with Ulbra branding  
✅ Smooth animations (60fps, no jank)  
✅ Filters update all charts in <500ms  
✅ KPI grid drag-and-drop works smoothly  
✅ Export PNG captures chart accurately  
✅ Mobile responsive (<640px works well)  
✅ Code snippets copy without errors  
✅ localStorage persists KPI grid layout  
✅ Navbar link in brandbook.html points to charts  
✅ Page loads <2s (lazy load charts below fold)

---

## Future Enhancements (Out of Scope)

- API integration (replace fixtures with real data)
- Real-time updates via WebSocket
- Custom chart builder UI
- Shared dashboard templates
- Role-based filtering (viewer vs. editor)
- Dark mode variant
- PDF export (not just PNG)

---

## Notes

- All charts use Ulbra color palette; ECharts theme customized with CSS vars
- Animations prioritize 60fps performance; fallback to instant load if performance degrades
- Data is mocked but structured to mirror real educational metrics
- Component gallery serves as both showcase and copy-paste resource for developers
