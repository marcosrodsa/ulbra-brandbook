# Charts Component Gallery Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) to execute this plan task-by-task with review between tasks, OR use superpowers:executing-plans for inline batch execution.

**Goal:** Build an interactive component gallery showcasing 13+ chart types (ECharts, GSAP, Framer Motion) with Ulbra branding, global filters, draggable KPI grid, and export functionality in a single `charts.html` file.

**Architecture:** Single-page HTML file with embedded CSS and modular JavaScript sections (data, filters, chart instances, animations, KPI grid). Charts are ECharts instances with custom Ulbra color themes. Animations use Framer Motion (drag), GSAP (SVG/gauges), and CSS transitions. Filters update data client-side with staggered chart animations.

**Tech Stack:** ECharts 5.4.0, Framer Motion 10.16.0, GSAP 3.12.0, html2canvas 1.4.0, vanilla JS (no framework)

---

## File Structure

```
charts.html (NEW - single file, ~2000 LOC)
├── <head>
│   ├── colors_and_type.css (linked, existing)
│   └── <style> [embedded: layout, charts, animations, responsive]
│
└── <script>
    ├── [ECharts theme + global state]
    ├── [Fixture data]
    ├── [Filter logic]
    ├── [Chart components factory]
    ├── [KPI grid initialization]
    ├── [Animation helpers]
    ├── [Export utilities]
    └── [Initialization on DOMContentLoaded]

brandbook.html (MODIFY)
├── Add "Charts" link to nav-pills
└── Point to #charts section or charts.html
```

---

## Task 1: Create Base HTML Structure with Topbar & Hero

**Files:**
- Create: `charts.html`

- [ ] **Step 1: Copy topbar structure from brandbook.html**

Create `charts.html` with the topbar section (copy from brandbook.html:779–799), update href to "charts" for the is-active class:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Ulbra · Componentes de Gráficos · v1.0 · 2026</title>
<link rel="stylesheet" href="colors_and_type.css" />
<style>
  /* Embedded CSS will be added in later tasks */
</style>
</head>
<body style="">

<!-- TOPBAR -->
<header class="topbar">
  <div class="topbar-inner">
    <a class="brandmark" href="brandbook.html#top">
      <img src="assets/ulbra-mark-white.png" alt="Ulbra mark" style="width:100%;height:100%;object-fit:contain"/>
      <span class="wm">ulbra</span>
    </a>
    <nav class="nav-pills">
      <a href="brandbook.html#identity">Identity</a>
      <a href="brandbook.html#color">Color</a>
      <a href="brandbook.html#type">Typography</a>
      <a href="brandbook.html#logo">Logo</a>
      <a href="brandbook.html#voice">Voice</a>
      <a href="brandbook.html#icon">Icons</a>
      <a href="brandbook.html#components">Components</a>
      <a href="charts.html" class="is-active">Charts</a>
    </nav>
    <div class="topbar-meta">
      <span><span class="dot"></span>v1.0 · 2026</span>
      <span>Component Gallery</span>
    </div>
  </div>
</header>

<!-- HERO -->
<section class="hero" id="top">
  <span class="corner tl"></span>
  <span class="corner tr"></span>

  <div class="ruler">
    <span class="num">00</span>
    <span>Component Gallery · Charts</span>
    <span class="line"></span>
    <span>Universidade Luterana do Brasil</span>
    <span class="line"></span>
    <span>2026</span>
  </div>

  <div class="hero-grid">
    <div>
      <div class="eyebrow">Charts & Visualizations · v1.0</div>
      <h1>
        Componentes de<br>
        <em>Gráficos</em><br>
        <span class="small">— biblioteca reutilizável.</span>
      </h1>
      <p class="hero-tag" style="margin-top:32px">
        Showcase de 13+ tipos de visualizações com Recharts, ECharts, GSAP e Framer Motion.
        Animações modernas, filtros interativos e padrões copy-paste ready para dashboards educacionais.
      </p>
    </div>

    <div class="hero-side">
      <div class="row"><span>Tipo</span><span class="v">Component Gallery</span></div>
      <div class="row"><span>Versão</span><span class="v">1.0 · 2026</span></div>
      <div class="row"><span>Charts</span><span class="v">13+ tipos</span></div>
      <div class="row"><span>Bibliotecas</span><span class="v">ECharts, GSAP, Framer</span></div>
      <div class="row"><span>Status</span><span class="v">Living Component</span></div>
    </div>
  </div>
</section>

<!-- FILTERS (placeholder, will be populated by JS) -->
<section class="filter-section" id="filters">
  <div style="max-width: var(--container); margin: 0 auto; padding: 0 var(--gutter);">
    <h3 class="eyebrow">Filtros Globais</h3>
    <div class="filter-bar">
      <label class="field">
        Período
        <select id="filterPeriod">
          <option value="2026">2026</option>
          <option value="2026-s1">2026 S1</option>
          <option value="2026-s2">2026 S2</option>
        </select>
      </label>
      <label class="field">
        Campus
        <select id="filterCampus" multiple>
          <option value="all" selected>Todos os campus</option>
          <option value="canoas">Canoas RS</option>
          <option value="manaus">Manaus AM</option>
          <option value="palmas">Palmas TO</option>
        </select>
      </label>
      <label class="field">
        Programa
        <select id="filterProgram" multiple>
          <option value="all" selected>Todos os programas</option>
          <option value="grad">Graduação</option>
          <option value="pos">Pós-Graduação</option>
          <option value="ead">EAD</option>
          <option value="med">Medicina</option>
        </select>
      </label>
      <button id="btnReset" class="btn btn-ghost">Reset</button>
    </div>
  </div>
</section>

<!-- CHART SECTIONS (will be populated by JS) -->
<section class="section" id="section-common">
  <div class="section-head">
    <div>
      <div class="eyebrow">Seção 01</div>
      <h2>Gráficos <em>Comuns</em></h2>
    </div>
    <div class="section-meta">
      <span class="num">01 / 04</span>
      <span>5 Componentes</span>
      <span>Line, Bar, Area, Pie, Donut</span>
    </div>
  </div>
  <div class="chart-grid" id="common-charts"></div>
</section>

<section class="section" id="section-kpi">
  <div class="section-head">
    <div>
      <div class="eyebrow">Seção 02</div>
      <h2>KPI & <em>Indicadores</em></h2>
    </div>
    <div class="section-meta">
      <span class="num">02 / 04</span>
      <span>4 Componentes</span>
      <span>Gauge, Progress, StatCard, Trend</span>
    </div>
  </div>
  <div class="chart-grid" id="kpi-charts"></div>
</section>

<section class="section" id="section-advanced">
  <div class="section-head">
    <div>
      <div class="eyebrow">Seção 03</div>
      <h2>Padrões <em>Avançados</em></h2>
    </div>
    <div class="section-meta">
      <span class="num">03 / 04</span>
      <span>4 Componentes</span>
      <span>Heatmap, Scatter, Sankey, Radial</span>
    </div>
  </div>
  <div class="chart-grid" id="advanced-charts"></div>
</section>

<section class="section" id="section-grid">
  <div class="section-head">
    <div>
      <div class="eyebrow">Seção 04</div>
      <h2>KPI Grid <em>Arrastável</em></h2>
    </div>
    <div class="section-meta">
      <span class="num">04 / 04</span>
      <span>Interactive Demo</span>
      <span>Drag, Drop, localStorage</span>
    </div>
  </div>
  <div id="kpi-grid-container"></div>
</section>

<!-- FOOTER -->
<footer class="footer">
  <div>
    <a class="brandmark" href="brandbook.html#top">
      <img src="assets/ulbra-mark-white.png" alt="Ulbra mark" style="width:100%;height:100%;object-fit:contain"/>
      <span class="wm">ulbra</span>
    </a>
    <p style="margin-top:18px; max-width: 36ch; font: 400 13px/1.55 var(--font-sans); color: var(--fg-muted);">
      Ulbra · Charts Component Gallery · v1.0 · Edição 2026.<br>
      Documento interno. Para uso autorizado por equipe de Design e Desenvolvimento.
    </p>
  </div>
  <div>
    <h6>Recursos</h6>
    <ul>
      <li><a href="brandbook.html">Brand Guidelines</a></li>
      <li><a href="dashboard.html">Dashboard UI Kit</a></li>
      <li><a href="colors_and_type.css">Design Tokens (CSS)</a></li>
      <li><a href="README.md">README</a></li>
    </ul>
  </div>
  <div>
    <h6>Contato</h6>
    <ul>
      <li><a href="https://www.ulbra.br">ulbra.br</a></li>
      <li>Aelbra Educação Superior</li>
      <li>CNPJ 88.332.580/0001-65</li>
    </ul>
  </div>
</footer>

<script>
  // Placeholder for JS modules - added in later tasks
  console.log("Charts gallery initialized");
</script>

</body>
</html>
```

- [ ] **Step 2: Add CSS for layout (topbar, hero, sections, footer)**

Inside `<style>` tag, add (copy baseline styles from brandbook.html and adapt):

```css
/* PAGE FRAME */
body {
  background: var(--ulbra-primary-deep);
  color: var(--fg);
  overflow-x: hidden;
}
body::before {
  content: "";
  position: fixed; inset: 0;
  pointer-events: none;
  z-index: 0;
  background-image:
    radial-gradient(ellipse 1200px 800px at 80% 0%, rgba(204,162,105,.08), transparent 60%),
    radial-gradient(ellipse 900px 700px at 0% 100%, rgba(145,186,163,.06), transparent 60%),
    url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='240' height='240'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='2' stitchTiles='stitch'/><feColorMatrix values='0 0 0 0 1  0 0 0 0 1  0 0 0 0 1  0 0 0 0.06 0'/></filter><rect width='100%' height='100%' filter='url(%23n)'/></svg>");
}

/* TOPBAR (copy from brandbook.html, lines 29–83) */
.topbar {
  position: sticky; top: 0; z-index: var(--z-nav);
  backdrop-filter: blur(14px) saturate(140%);
  -webkit-backdrop-filter: blur(14px) saturate(140%);
  background: rgba(13, 54, 52, .72);
  border-bottom: 1px solid var(--surface-line);
}
.topbar-inner {
  max-width: var(--container);
  margin: 0 auto;
  padding: 14px var(--gutter);
  display: flex; align-items: center; gap: 32px;
}
.brandmark {
  display: flex; align-items: center; gap: 10px;
  color: var(--fg);
}
.brandmark svg, .brandmark img { width: 28px; height: 28px; object-fit: contain; color: var(--fg); }
.brandmark .wm {
  font-family: var(--font-sans);
  font-weight: 700;
  font-size: 22px;
  letter-spacing: -0.02em;
}
.topbar-meta {
  margin-left: auto;
  display: flex; align-items: center; gap: 20px;
  font: 500 11px/1 var(--font-mono);
  letter-spacing: 0.16em;
  text-transform: uppercase;
  color: var(--fg-muted);
}
.topbar-meta .dot {
  display: inline-block; width: 6px; height: 6px; border-radius: 50%;
  background: var(--ulbra-gold-light);
  box-shadow: 0 0 10px var(--ulbra-gold-light);
  margin-right: 6px;
}
.nav-pills {
  display: flex; gap: 4px;
  font: 500 11px/1 var(--font-mono);
  letter-spacing: 0.14em;
  text-transform: uppercase;
}
.nav-pills a {
  color: var(--fg-muted);
  text-decoration: none;
  padding: 8px 12px;
  border-radius: var(--radius-pill);
  transition: var(--dur-fast) var(--ease-standard);
}
.nav-pills a:hover { color: var(--fg); background: rgba(245,206,153,.06); }
.nav-pills a.is-active { color: var(--ulbra-primary-deep); background: var(--ulbra-gold-light); }

/* HERO, SECTION, FOOTER (copy from brandbook.html, adapt as needed) */
.hero {
  position: relative;
  z-index: 1;
  max-width: var(--container);
  margin: 0 auto;
  padding: 88px var(--gutter) 80px;
}
.hero-grid {
  display: grid;
  grid-template-columns: 1.1fr 0.9fr;
  gap: 48px;
  align-items: end;
}
.hero h1 {
  font: 500 clamp(60px, 10vw, 168px)/0.92 var(--font-display);
  letter-spacing: -0.03em;
  margin: 24px 0 0;
}
.hero h1 em { font-style: italic; color: var(--ulbra-gold-light); font-weight: 400; }
.hero h1 .small { font-size: 0.55em; display: block; }
.hero-tag {
  color: var(--fg-muted);
  max-width: 32ch;
  font-size: 18px;
  line-height: 1.55;
}
.hero-side {
  display: flex; flex-direction: column; gap: 14px;
  padding-bottom: 6px;
}
.hero-side .row {
  display: grid; grid-template-columns: 110px 1fr;
  gap: 16px;
  padding: 14px 0;
  border-top: 1px solid var(--surface-line);
  font: 500 11px/1.4 var(--font-mono);
  letter-spacing: 0.14em;
  text-transform: uppercase;
  color: var(--fg-muted);
}
.hero-side .row:last-child { border-bottom: 1px solid var(--surface-line); }
.hero-side .row .v { color: var(--fg); }

.corner {
  position: absolute; width: 18px; height: 18px;
  border-color: var(--ulbra-gold-light);
  border-style: solid;
  border-width: 0;
}
.corner.tl { top: 88px; left: var(--gutter); border-top-width:1px; border-left-width:1px; }
.corner.tr { top: 88px; right: var(--gutter); border-top-width:1px; border-right-width:1px; }

.ruler {
  display: flex; align-items: center; gap: 12px;
  font: 500 10px/1 var(--font-mono);
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: var(--fg-dim);
  margin-bottom: 32px;
}
.ruler .line { flex: 1; height: 1px; background: var(--surface-line); }
.ruler .num { color: var(--ulbra-gold-light); }

.eyebrow {
  font: 500 11px/1 var(--font-mono);
  letter-spacing: 0.22em;
  text-transform: uppercase;
  color: var(--ulbra-gold-light);
  margin-bottom: 16px;
}

.section {
  position: relative;
  z-index: 1;
  padding: 96px var(--gutter);
  border-bottom: 1px solid var(--surface-line);
  max-width: var(--container);
  margin: 0 auto;
}
.section + .section { border-top: 0; }
.section-head {
  display: grid;
  grid-template-columns: 1fr auto;
  gap: 24px 40px;
  align-items: end;
  padding-bottom: 28px;
  margin-bottom: 48px;
  border-bottom: 1px solid var(--surface-line);
}
.section-head h2 {
  font: 600 clamp(40px, 6vw, 88px)/0.95 var(--font-display);
  letter-spacing: -0.02em;
  margin: 0;
}
.section-head h2 em {
  font-style: italic;
  color: var(--ulbra-gold-light);
  font-weight: 500;
}
.section-meta {
  display: flex; flex-direction: column; gap: 6px; align-items: flex-end;
  font: 500 11px/1 var(--font-mono);
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: var(--fg-dim);
  text-align: right;
}
.section-meta .num {
  color: var(--ulbra-gold-light);
  font-size: 13px;
}

/* FILTER BAR */
.filter-section {
  position: relative;
  z-index: 1;
  padding: 32px var(--gutter);
  border-bottom: 1px solid var(--surface-line);
  background: rgba(13,54,52,.5);
}
.filter-bar {
  display: flex; gap: 16px; flex-wrap: wrap;
  align-items: flex-end;
}
.field {
  display: flex; flex-direction: column; gap: 6px;
  font: 500 11px/1 var(--font-mono);
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: var(--fg-muted);
  flex: 1; min-width: 200px;
}
.field input, .field select {
  appearance: none;
  width: 100%;
  background: rgba(13,54,52,.5);
  border: 1px solid var(--surface-line);
  border-radius: var(--radius-pill);
  padding: 12px 18px;
  color: var(--fg);
  font: 400 14px/1 var(--font-sans);
  letter-spacing: 0;
  text-transform: none;
}
.field input:focus { outline: none; border-color: var(--ulbra-gold-light); box-shadow: 0 0 0 3px rgba(245,206,153,.14); }

/* CHART GRID */
.chart-grid {
  display: grid; gap: 24px;
  grid-template-columns: repeat(auto-fit, minmax(500px, 1fr));
}
.chart-card {
  background: var(--surface);
  border: 1px solid var(--surface-line);
  border-radius: var(--radius-md);
  padding: 24px;
  display: flex; flex-direction: column; gap: 16px;
  opacity: 0;
  animation: fadeInUp 0.6s ease-out forwards;
}
.chart-card h4 {
  font: 600 16px/1.2 var(--font-display);
  color: var(--ulbra-gold-light);
  margin: 0;
  letter-spacing: -0.01em;
}
.chart-card p {
  font: 400 13px/1.5 var(--font-sans);
  color: var(--fg-muted);
  margin: 0;
}
.chart-container {
  min-height: 300px;
  width: 100%;
}
.code-block {
  background: rgba(6,30,28,.6);
  border: 1px solid var(--surface-line);
  border-radius: var(--radius-sm);
  padding: 12px;
  font: 500 11px/1.4 var(--font-mono);
  color: var(--fg-muted);
  overflow-x: auto;
  position: relative;
}
.code-block button {
  position: absolute;
  top: 8px; right: 8px;
  font-size: 11px;
  padding: 6px 12px;
}

/* BUTTONS */
.btn {
  display: inline-flex; align-items: center; gap: 10px;
  padding: 12px 22px;
  border-radius: var(--radius-pill);
  font: 500 14px/1 var(--font-sans);
  letter-spacing: 0.02em;
  text-decoration: none;
  cursor: pointer;
  border: 1px solid transparent;
  transition: var(--dur-fast) var(--ease-standard);
}
.btn-primary { background: var(--ulbra-gold); color: var(--ulbra-primary-deep); }
.btn-primary:hover { background: var(--ulbra-gold-light); }
.btn-ghost {
  background: transparent;
  color: var(--fg);
  border: 1px solid var(--ulbra-gold);
}
.btn-ghost:hover { background: rgba(204,162,105,.08); }

/* FOOTER */
.footer {
  border-top: 1px solid var(--surface-line);
  margin-top: 60px;
  padding: 60px var(--gutter) 80px;
  max-width: var(--container);
  margin-left: auto; margin-right: auto;
  display: grid; grid-template-columns: 2fr 1fr 1fr;
  gap: 32px;
  color: var(--fg-muted);
}
.footer h6 {
  font: 500 10px/1 var(--font-mono);
  letter-spacing: 0.22em;
  text-transform: uppercase;
  color: var(--fg-dim);
  margin-bottom: 14px;
}
.footer ul { list-style: none; padding: 0; margin: 0; }
.footer li { padding: 4px 0; font: 400 13px/1.5 var(--font-sans); }
.footer a { color: var(--fg-muted); text-decoration: none; }
.footer a:hover { color: var(--ulbra-gold-light); }

/* ANIMATIONS */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* RESPONSIVE */
@media (max-width: 900px) {
  .hero-grid { grid-template-columns: 1fr; }
  .filter-bar { flex-direction: column; }
  .field { width: 100%; }
  .chart-grid { grid-template-columns: 1fr; }
  .footer { grid-template-columns: 1fr; }
  .topbar-meta, .nav-pills { display: none; }
}
```

- [ ] **Step 3: Verify HTML renders (no JS yet)**

Open `charts.html` in browser. Should see:
- Topbar with nav (Charts link active)
- Hero section with title
- Filter section (unfunctional)
- 4 empty section placeholders
- Footer

Expected: Clean Ulbra-branded layout, no errors in console.

- [ ] **Step 4: Commit**

```bash
git add charts.html
git commit -m "feat: add charts.html base structure with topbar, hero, filter bar, and footer"
```

---

## Task 2: Import Libraries & Setup ECharts Theme

**Files:**
- Modify: `charts.html` (`<head>` and `<script>`)

- [ ] **Step 1: Add script imports in `<head>` before closing tag**

```html
<head>
  <!-- ... existing meta tags ... -->
  <link rel="stylesheet" href="colors_and_type.css" />
  
  <!-- External libraries -->
  <script src="https://cdn.jsdelivr.net/npm/echarts@5.4.0/dist/echarts.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/gsap@3.12.0/dist/gsap.min.js"></script>
  <script src="https://unpkg.com/framer-motion@10.16.0"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
  
  <style>
    /* ... CSS from Task 1 ... */
  </style>
</head>
```

- [ ] **Step 2: Add ECharts theme configuration in `<script>` (before closing `</body>`)**

```javascript
// ============================================
// ECHARTS THEME - Ulbra Branding
// ============================================

const ulbraTheme = {
  color: [
    '#045A52',     // primary
    '#F5CE99',     // gold-light
    '#CCA269',     // gold
    '#66563D',     // bronze
    '#91BAA3',     // sage
    '#0D3634',     // primary-deep
    '#A89E8B',     // stone
  ],
  backgroundColor: 'transparent',
  textStyle: {
    fontFamily: 'Inter, sans-serif',
    color: '#D0D0D0',
  },
  title: {
    textStyle: {
      color: '#FBF8F2',
      fontFamily: 'Playfair Display, serif',
      fontWeight: 600,
      fontSize: 18,
    },
    subtextStyle: {
      color: '#9a9a9a',
    },
  },
  line: {
    itemStyle: {
      borderWidth: 2,
    },
    lineStyle: {
      width: 2,
    },
    symbolSize: 6,
    smooth: false,
  },
  radar: {
    itemStyle: {
      borderWidth: 2,
    },
    lineStyle: {
      width: 2,
    },
    symbolSize: 6,
    smooth: false,
  },
  bar: {
    itemStyle: {
      barBorderWidth: 0,
    },
  },
  pie: {
    itemStyle: {
      borderWidth: 0,
    },
  },
  scatter: {
    itemStyle: {
      borderWidth: 0,
    },
  },
  boxplot: {
    itemStyle: {
      borderWidth: 1,
    },
  },
  parallel: {
    itemStyle: {
      borderWidth: 0,
    },
  },
  sankey: {
    itemStyle: {
      borderWidth: 1,
    },
  },
  funnel: {
    itemStyle: {
      borderWidth: 0,
    },
  },
  gauge: {
    itemStyle: {
      borderWidth: 0,
    },
    progress: {
      itemStyle: {
        borderWidth: 0,
      },
    },
  },
  candlestick: {
    itemStyle: {
      color: '#045A52',
      color0: '#d87c7c',
      borderColor: '#045A52',
      borderColor0: '#d87c7c',
      borderWidth: 1,
    },
  },
  graph: {
    itemStyle: {
      borderWidth: 0,
    },
    label: {
      color: '#ececec',
    },
  },
  map: {
    itemStyle: {
      areaColor: '#eee',
      borderColor: '#444',
      borderWidth: 0.5,
    },
    label: {
      color: '#000',
    },
    emphasis: {
      itemStyle: {
        areaColor: 'rgba(255,215,0,0.8)',
        borderColor: '#444',
        borderWidth: 1,
      },
      label: {
        color: '#444',
      },
    },
  },
  geo: {
    itemStyle: {
      areaColor: '#323c48',
      borderColor: '#404a59',
      borderWidth: 0.5,
    },
    label: {
      color: '#fff',
    },
    emphasis: {
      itemStyle: {
        areaColor: '#2a333d',
        borderColor: '#fff',
        borderWidth: 0.5,
      },
      label: {
        color: '#fff',
      },
    },
  },
  categoryAxis: {
    axisLine: {
      show: true,
      lineStyle: {
        color: '#333',
      },
    },
    axisTick: {
      show: true,
      lineStyle: {
        color: '#333',
      },
    },
    axisLabel: {
      show: true,
      color: '#999',
      fontFamily: 'Inter, sans-serif',
    },
    splitLine: {
      show: false,
      lineStyle: {
        color: ['#ccc'],
      },
    },
    splitArea: {
      show: false,
      areaStyle: {
        color: ['rgba(250,250,250,0.3)', 'rgba(200,200,200,0.3)'],
      },
    },
  },
  valueAxis: {
    axisLine: {
      show: false,
      lineStyle: {
        color: '#333',
      },
    },
    axisTick: {
      show: false,
      lineStyle: {
        color: '#333',
      },
    },
    axisLabel: {
      show: true,
      color: '#999',
      fontFamily: 'Inter, sans-serif',
    },
    splitLine: {
      show: true,
      lineStyle: {
        color: ['#ccc'],
      },
    },
    splitArea: {
      show: false,
      areaStyle: {
        color: ['rgba(250,250,250,0.3)', 'rgba(200,200,200,0.3)'],
      },
    },
  },
  logAxis: {
    axisLine: {
      show: false,
      lineStyle: {
        color: '#333',
      },
    },
    axisTick: {
      show: false,
      lineStyle: {
        color: '#333',
      },
    },
    axisLabel: {
      show: true,
      color: '#999',
    },
    splitLine: {
      show: true,
      lineStyle: {
        color: ['#ccc'],
      },
    },
    splitArea: {
      show: false,
      areaStyle: {
        color: ['rgba(250,250,250,0.3)', 'rgba(200,200,200,0.3)'],
      },
    },
  },
  timeAxis: {
    axisLine: {
      show: true,
      lineStyle: {
        color: '#333',
      },
    },
    axisTick: {
      show: true,
      lineStyle: {
        color: '#333',
      },
    },
    axisLabel: {
      show: true,
      color: '#999',
      fontFamily: 'Inter, sans-serif',
    },
    splitLine: {
      show: false,
      lineStyle: {
        color: ['#ccc'],
      },
    },
    splitArea: {
      show: false,
      areaStyle: {
        color: ['rgba(250,250,250,0.3)', 'rgba(200,200,200,0.3)'],
      },
    },
  },
  toolbox: {
    iconStyle: {
      borderColor: '#999',
    },
    emphasis: {
      iconStyle: {
        borderColor: '#333',
      },
    },
  },
  legend: {
    textStyle: {
      color: '#999',
      fontFamily: 'Inter, sans-serif',
    },
  },
  tooltip: {
    axisPointer: {
      lineStyle: {
        color: '#ccc',
        width: 1,
      },
      crossStyle: {
        color: '#ccc',
        width: 1,
      },
    },
    backgroundColor: 'rgba(50, 50, 50, 0.8)',
    borderColor: '#CCA269',
    borderWidth: 1,
    textStyle: {
      color: '#fff',
      fontFamily: 'Inter, sans-serif',
    },
  },
  timeline: {
    lineStyle: {
      color: '#293c55',
      width: 1,
    },
    itemStyle: {
      color: '#293c55',
      borderWidth: 1,
    },
    controlStyle: {
      color: '#293c55',
      borderColor: '#293c55',
      borderWidth: 0.5,
    },
    checkpointStyle: {
      color: '#e43c59',
      borderColor: 'rgba(194,53,49,0.5)',
    },
    label: {
      color: '#293c55',
    },
    emphasis: {
      itemStyle: {
        color: '#a9334c',
      },
      controlStyle: {
        color: '#293c55',
        borderColor: '#293c55',
        borderWidth: 0.5,
      },
      label: {
        color: '#293c55',
      },
    },
  },
  visualMap: {
    textStyle: {
      color: '#999',
    },
  },
  markPoint: {
    label: {
      color: '#ececec',
    },
    emphasis: {
      label: {
        color: '#ececec',
      },
    },
  },
};

// Register theme
echarts.registerTheme('ulbra', ulbraTheme);

console.log('ECharts theme registered: ulbra');
```

- [ ] **Step 3: Verify in browser**

Open dev console, should see: `ECharts theme registered: ulbra`

- [ ] **Step 4: Commit**

```bash
git add charts.html
git commit -m "feat: import ECharts, GSAP, Framer Motion, html2canvas; register Ulbra theme"
```

---

## Task 3: Create Fixture Data (Mock Educational Data)

**Files:**
- Modify: `charts.html` (add before `<script>` with ECharts theme)

- [ ] **Step 1: Add fixture data object**

```javascript
// ============================================
// FIXTURE DATA - Mock Educational Metrics
// ============================================

const fixtureData = {
  // COMMON CHARTS DATA
  enrollmentByProgram: [
    { program: 'Graduação', 'S1 2026': 2100, 'S2 2026': 2250, 'S1 2025': 1950, 'S2 2025': 2050 },
    { program: 'Pós-Graduação', 'S1 2026': 850, 'S2 2026': 920, 'S1 2025': 800, 'S2 2025': 870 },
    { program: 'EAD', 'S1 2026': 3200, 'S2 2026': 3500, 'S1 2025': 2800, 'S2 2025': 3100 },
    { program: 'Medicina', 'S1 2026': 450, 'S2 2026': 480, 'S1 2025': 420, 'S2 2025': 445 },
  ],
  
  enrollmentTrend: [
    { month: 'Jan', value: 5200, campus: 'Canoas' },
    { month: 'Fev', value: 5450, campus: 'Canoas' },
    { month: 'Mar', value: 5800, campus: 'Canoas' },
    { month: 'Abr', value: 6100, campus: 'Canoas' },
    { month: 'Mai', value: 6400, campus: 'Canoas' },
    { month: 'Jun', value: 6800, campus: 'Canoas' },
    { month: 'Jul', value: 7200, campus: 'Canoas' },
    { month: 'Ago', value: 7600, campus: 'Canoas' },
    { month: 'Set', value: 8000, campus: 'Canoas' },
    { month: 'Out', value: 8300, campus: 'Canoas' },
    { month: 'Nov', value: 8600, campus: 'Canoas' },
    { month: 'Dez', value: 8900, campus: 'Canoas' },
  ],
  
  studentsByCampus: [
    { campus: 'Canoas RS', count: 4500 },
    { campus: 'Manaus AM', count: 2800 },
    { campus: 'Palmas TO', count: 1600 },
    { campus: 'Virtual (EAD)', count: 3200 },
  ],
  
  programDistribution: [
    { name: 'Graduação', value: 38 },
    { name: 'Pós-Graduação', value: 15 },
    { name: 'EAD', value: 40 },
    { name: 'Medicina', value: 7 },
  ],
  
  completionRate: [
    { name: 'Graduação', value: 92 },
    { name: 'Pós-Graduação', value: 95 },
    { name: 'EAD', value: 88 },
    { name: 'Medicina', value: 97 },
  ],
  
  // KPI DATA
  approvalRate: 88,
  completionPercentage: 91,
  totalStudents: 12100,
  studentGrowth: 12.3, // percent
  
  // ADVANCED DATA
  enrollmentHeatmap: [
    ['Jan', 'Canoas', 520],
    ['Jan', 'Manaus', 280],
    ['Jan', 'Palmas', 160],
    ['Fev', 'Canoas', 545],
    ['Fev', 'Manaus', 310],
    ['Fev', 'Palmas', 180],
    ['Mar', 'Canoas', 580],
    ['Mar', 'Manaus', 340],
    ['Mar', 'Palmas', 200],
    // ... more data points
  ],
  
  costVsGraduation: [
    { name: 'Program A', cost: 25000, rate: 92, size: 120 },
    { name: 'Program B', cost: 35000, rate: 88, size: 95 },
    { name: 'Program C', cost: 15000, rate: 85, size: 220 },
    { name: 'Program D', cost: 45000, rate: 95, size: 85 },
    { name: 'Program E', cost: 20000, rate: 89, size: 140 },
  ],
  
  enrollmentFunnel: [
    { stage: 'Inquiries', value: 3200 },
    { stage: 'Applications', value: 2100 },
    { stage: 'Approved', value: 1450 },
    { stage: 'Enrolled', value: 1200 },
  ],
  
  fieldDistribution: [
    { name: 'Engenharia', value: 2400 },
    { name: 'Saúde', value: 1800 },
    { name: 'Negócios', value: 1600 },
    { name: 'Educação', value: 1200 },
    { name: 'Humanas', value: 900 },
    { name: 'Artes', value: 600 },
    { name: 'Direito', value: 400 },
    { name: 'Tecnologia', value: 800 },
  ],
  
  // CAMPUS & PROGRAM METADATA
  campuses: ['Canoas', 'Manaus', 'Palmas'],
  programs: ['Graduação', 'Pós-Graduação', 'EAD', 'Medicina'],
};

console.log('Fixture data loaded');
```

- [ ] **Step 2: Verify in browser**

Open console, type `fixtureData` → should print the entire object without errors.

- [ ] **Step 3: Commit**

```bash
git add charts.html
git commit -m "feat: add fixture data for educational metrics (13+ data structures)"
```

---

## Task 4: Implement Global Filter State & Logic

**Files:**
- Modify: `charts.html` (add filter logic script before closing `</body>`)

- [ ] **Step 1: Add filter state management**

```javascript
// ============================================
// FILTER STATE & LOGIC
// ============================================

const filterState = {
  period: '2026',
  campuses: ['all'],
  programs: ['all'],
};

// Filter listeners
document.getElementById('filterPeriod').addEventListener('change', (e) => {
  filterState.period = e.target.value;
  updateAllCharts();
});

document.getElementById('filterCampus').addEventListener('change', (e) => {
  const opts = Array.from(e.target.selectedOptions).map(o => o.value);
  filterState.campuses = opts.length === 0 ? ['all'] : opts;
  updateAllCharts();
});

document.getElementById('filterProgram').addEventListener('change', (e) => {
  const opts = Array.from(e.target.selectedOptions).map(o => o.value);
  filterState.programs = opts.length === 0 ? ['all'] : opts;
  updateAllCharts();
});

document.getElementById('btnReset').addEventListener('click', () => {
  filterState.period = '2026';
  filterState.campuses = ['all'];
  filterState.programs = ['all'];
  document.getElementById('filterPeriod').value = '2026';
  document.getElementById('filterCampus').value = 'all';
  document.getElementById('filterProgram').value = 'all';
  updateAllCharts();
});

function getFilteredData(dataArray, key = 'campus') {
  if (filterState.campuses.includes('all') && filterState.programs.includes('all')) {
    return dataArray;
  }
  return dataArray.filter(item => {
    const campusMatch = filterState.campuses.includes('all') || filterState.campuses.includes(item[key]);
    const programMatch = filterState.programs.includes('all') || filterState.programs.includes(item.program || '');
    return campusMatch && programMatch;
  });
}

function updateAllCharts() {
  // Fade out all charts
  document.querySelectorAll('.chart-container').forEach(el => {
    el.style.opacity = '0';
    el.style.transition = 'opacity 0.2s ease-out';
  });
  
  // After fade, update and re-render
  setTimeout(() => {
    // Placeholder - actual chart updates added in later tasks
    console.log('Filters updated:', filterState);
    
    document.querySelectorAll('.chart-container').forEach(el => {
      el.style.opacity = '1';
      el.style.transition = 'opacity 0.4s ease-in';
    });
  }, 200);
}

console.log('Filter state initialized');
```

- [ ] **Step 2: Verify filter interaction**

Open browser, select filters → should see console logs:  
`Filters updated: { period: "2026", campuses: [...], programs: [...] }`

- [ ] **Step 3: Commit**

```bash
git add charts.html
git commit -m "feat: add filter state management and update logic"
```

---

## Task 5: Create Common Charts (Line, Bar, Area, Pie, Donut) with Animations

**Files:**
- Modify: `charts.html` (add chart factory functions before `</body>`)

- [ ] **Step 1: Create Line Chart component**

```javascript
// ============================================
// COMMON CHARTS - Line, Bar, Area, Pie, Donut
// ============================================

function createLineChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'axis',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
    },
    legend: {
      data: ['Graduação', 'Pós-Graduação', 'EAD', 'Medicina'],
      textStyle: { color: '#999' },
    },
    xAxis: {
      type: 'category',
      data: ['Jan', 'Fev', 'Mar', 'Abr', 'Mai', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez'],
      boundaryGap: false,
      axisLabel: { color: '#999' },
      axisLine: { lineStyle: { color: '#444' } },
    },
    yAxis: {
      type: 'value',
      axisLabel: { color: '#999' },
      splitLine: { lineStyle: { color: '#333' } },
    },
    series: [
      {
        name: 'Graduação',
        data: [420, 432, 471, 534, 590, 630, 660, 720, 800, 860, 900, 950],
        type: 'line',
        smooth: true,
        itemStyle: { color: '#045A52' },
        areaStyle: { color: 'rgba(4, 90, 82, 0.3)' },
      },
      {
        name: 'Pós-Graduação',
        data: [220, 182, 191, 234, 290, 330, 310, 280, 320, 360, 400, 420],
        type: 'line',
        smooth: true,
        itemStyle: { color: '#F5CE99' },
        areaStyle: { color: 'rgba(245, 206, 153, 0.2)' },
      },
      {
        name: 'EAD',
        data: [820, 932, 901, 934, 1290, 1330, 1320, 1500, 1200, 1600, 1800, 2100],
        type: 'line',
        smooth: true,
        itemStyle: { color: '#CCA269' },
        areaStyle: { color: 'rgba(204, 162, 105, 0.2)' },
      },
      {
        name: 'Medicina',
        data: [120, 132, 101, 134, 90, 130, 110, 150, 200, 150, 200, 250],
        type: 'line',
        smooth: true,
        itemStyle: { color: '#66563D' },
        areaStyle: { color: 'rgba(102, 86, 61, 0.2)' },
      },
    ],
    animationDuration: 1000,
    animationEasing: 'cubicOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}

// Store instances for later reference
const chartInstances = {};

// Initialize on DOM ready
document.addEventListener('DOMContentLoaded', () => {
  // Create cards with content
  const commonCharts = [
    {
      id: 'chart-line',
      title: 'Line Chart',
      desc: 'Inscrições por semestre',
      factory: createLineChart,
    },
    // More charts added in next steps
  ];
  
  commonCharts.forEach(chart => {
    const container = document.getElementById('common-charts');
    const card = document.createElement('div');
    card.className = 'chart-card';
    card.innerHTML = `
      <h4>${chart.title}</h4>
      <p>${chart.desc}</p>
      <div class="chart-container" id="container-${chart.id}"></div>
      <div class="code-block">
        &lt;LineChart data={...} /&gt;
        <button class="btn btn-primary" style="font-size:12px;">Copy Code</button>
      </div>
    `;
    container.appendChild(card);
    
    // Initialize chart
    const instance = chart.factory(`container-${chart.id}`, chart.title, chart.desc);
    chartInstances[chart.id] = instance;
  });
});
```

- [ ] **Step 2: Create Bar Chart component**

```javascript
function createBarChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'axis',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
    },
    xAxis: {
      type: 'category',
      data: ['Canoas RS', 'Manaus AM', 'Palmas TO', 'Virtual (EAD)'],
      axisLabel: { color: '#999' },
      axisLine: { lineStyle: { color: '#444' } },
    },
    yAxis: {
      type: 'value',
      axisLabel: { color: '#999' },
      splitLine: { lineStyle: { color: '#333' } },
    },
    series: [
      {
        data: [4500, 2800, 1600, 3200],
        type: 'bar',
        itemStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: '#F5CE99' },
            { offset: 1, color: '#045A52' },
          ]),
        },
      },
    ],
    animationDuration: 1000,
    animationEasing: 'cubicOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}
```

- [ ] **Step 3: Create Area Chart component**

```javascript
function createAreaChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'axis',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
    },
    xAxis: {
      type: 'category',
      data: ['2020', '2021', '2022', '2023', '2024', '2025', '2026'],
      boundaryGap: false,
      axisLabel: { color: '#999' },
      axisLine: { lineStyle: { color: '#444' } },
    },
    yAxis: {
      type: 'value',
      axisLabel: { color: '#999' },
      splitLine: { lineStyle: { color: '#333' } },
    },
    series: [
      {
        name: 'Total Students',
        data: [3200, 4100, 5300, 6800, 8900, 10200, 12100],
        type: 'line',
        smooth: true,
        itemStyle: { color: '#045A52' },
        areaStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: 'rgba(4, 90, 82, 0.5)' },
            { offset: 1, color: 'rgba(4, 90, 82, 0.1)' },
          ]),
        },
      },
    ],
    animationDuration: 1000,
    animationEasing: 'cubicOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}
```

- [ ] **Step 4: Create Pie Chart component**

```javascript
function createPieChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
      formatter: '{a} <br/>{b}: {c} ({d}%)',
    },
    legend: {
      orient: 'vertical',
      right: 10,
      top: 'center',
      textStyle: { color: '#999' },
    },
    series: [
      {
        name: 'Program Distribution',
        type: 'pie',
        radius: ['40%', '70%'],
        avoidLabelOverlap: false,
        itemStyle: {
          borderRadius: [4, 4],
          borderColor: '#0D3634',
          borderWidth: 2,
        },
        emphasis: {
          label: {
            show: true,
            fontSize: 16,
            fontWeight: 'bold',
          },
        },
        labelLine: {
          show: false,
        },
        data: [
          { value: 4600, name: 'Graduação', itemStyle: { color: '#045A52' } },
          { value: 1770, name: 'Pós-Graduação', itemStyle: { color: '#F5CE99' } },
          { value: 4840, name: 'EAD', itemStyle: { color: '#CCA269' } },
          { value: 930, name: 'Medicina', itemStyle: { color: '#66563D' } },
        ],
      },
    ],
    animationDuration: 1000,
    animationEasing: 'cubicInOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}
```

- [ ] **Step 5: Create Donut Chart component**

```javascript
function createDonutChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
      formatter: '{a} <br/>{b}: {c}%',
    },
    series: [
      {
        name: 'Completion Rate',
        type: 'pie',
        radius: ['40%', '70%'],
        center: ['50%', '50%'],
        data: [
          { value: 92, name: 'Graduação', itemStyle: { color: '#045A52' } },
          { value: 95, name: 'Pós-Graduação', itemStyle: { color: '#F5CE99' } },
          { value: 88, name: 'EAD', itemStyle: { color: '#CCA269' } },
          { value: 97, name: 'Medicina', itemStyle: { color: '#66563D' } },
        ],
        emphasis: {
          label: {
            show: true,
            fontSize: 16,
            fontWeight: 'bold',
          },
        },
        label: {
          show: false,
        },
        labelLine: {
          show: false,
        },
      },
    ],
    legend: {
      bottom: 10,
      left: 'center',
      data: ['Graduação', 'Pós-Graduação', 'EAD', 'Medicina'],
      textStyle: { color: '#999' },
    },
    animationDuration: 1200,
    animationEasing: 'elasticOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}
```

- [ ] **Step 6: Update DOM initialization to create all 5 common charts**

Replace the `commonCharts` array initialization with:

```javascript
const commonCharts = [
  {
    id: 'chart-line',
    title: 'Line Chart',
    desc: 'Inscrições por semestre',
    factory: createLineChart,
  },
  {
    id: 'chart-bar',
    title: 'Bar Chart',
    desc: 'Alunos por campus',
    factory: createBarChart,
  },
  {
    id: 'chart-area',
    title: 'Area Chart',
    desc: 'Crescimento anual',
    factory: createAreaChart,
  },
  {
    id: 'chart-pie',
    title: 'Pie Chart',
    desc: 'Distribuição por programa',
    factory: createPieChart,
  },
  {
    id: 'chart-donut',
    title: 'Donut Chart',
    desc: 'Taxa de conclusão',
    factory: createDonutChart,
  },
];
```

- [ ] **Step 7: Verify in browser**

Open `charts.html` → Section 1 should show 5 animated charts with smooth entrance.

- [ ] **Step 8: Commit**

```bash
git add charts.html
git commit -m "feat: implement 5 common charts (Line, Bar, Area, Pie, Donut) with ECharts and animations"
```

---

## Task 6: Create KPI Indicator Charts (Gauge, Progress, StatCard, Trend)

**Files:**
- Modify: `charts.html` (add KPI chart factories)

- [ ] **Step 1: Create Gauge Chart (custom SVG + GSAP)**

```javascript
// ============================================
// KPI & INDICATORS - Gauge, Progress, Stat, Trend
// ============================================

function createGaugeChart(containerId, title, description, value = 88) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  // Create SVG gauge
  const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
  svg.setAttribute('width', '100%');
  svg.setAttribute('height', '280');
  svg.setAttribute('viewBox', '0 0 200 200');
  
  // Background arc
  const bgArc = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
  bgArc.setAttribute('cx', '100');
  bgArc.setAttribute('cy', '120');
  bgArc.setAttribute('r', '70');
  bgArc.setAttribute('fill', 'none');
  bgArc.setAttribute('stroke', '#333');
  bgArc.setAttribute('stroke-width', '12');
  bgArc.setAttribute('stroke-dasharray', `${70 * 3.14 * 0.5} ${70 * 3.14}`);
  svg.appendChild(bgArc);
  
  // Value arc
  const valueArc = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
  valueArc.setAttribute('cx', '100');
  valueArc.setAttribute('cy', '120');
  valueArc.setAttribute('r', '70');
  valueArc.setAttribute('fill', 'none');
  valueArc.setAttribute('stroke', '#F5CE99');
  valueArc.setAttribute('stroke-width', '12');
  valueArc.setAttribute('stroke-linecap', 'round');
  valueArc.setAttribute('stroke-dasharray', '0 219.9');
  valueArc.setAttribute('transform', 'rotate(-90 100 120)');
  svg.appendChild(valueArc);
  
  // Center text
  const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
  text.setAttribute('x', '100');
  text.setAttribute('y', '130');
  text.setAttribute('text-anchor', 'middle');
  text.setAttribute('font-size', '48');
  text.setAttribute('font-weight', '600');
  text.setAttribute('fill', '#FBF8F2');
  text.setAttribute('font-family', 'Playfair Display');
  text.textContent = '0%';
  svg.appendChild(text);
  
  // Label
  const label = document.createElementNS('http://www.w3.org/2000/svg', 'text');
  label.setAttribute('x', '100');
  label.setAttribute('y', '160');
  label.setAttribute('text-anchor', 'middle');
  label.setAttribute('font-size', '12');
  label.setAttribute('fill', '#999');
  label.setAttribute('font-family', 'Inter');
  label.textContent = 'vs. 85% last year';
  svg.appendChild(label);
  
  container.appendChild(svg);
  
  // Animate gauge with GSAP
  gsap.to(valueArc, {
    attr: { 'stroke-dasharray': `${(value / 100) * 219.9} 219.9` },
    duration: 1.5,
    ease: 'power2.out',
    delay: 0.3,
  });
  
  gsap.to(text, {
    textContent: Math.round(value),
    duration: 1.5,
    ease: 'power2.out',
    delay: 0.3,
    snap: { textContent: 1 },
  });
  
  return { valueArc, text };
}
```

- [ ] **Step 2: Create Progress Bar component**

```javascript
function createProgressChart(containerId, title, description, percentage = 91) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const html = `
    <div style="display: flex; flex-direction: column; gap: 12px; height: 100%;">
      <div style="font-size: 14px; color: #FBF8F2; font-weight: 600;">Completion</div>
      <div style="flex: 1; display: flex; flex-direction: column; justify-content: center;">
        <div style="width: 100%; height: 8px; background: #333; border-radius: 4px; overflow: hidden;">
          <div class="progress-fill" style="width: 0%; height: 100%; background: linear-gradient(90deg, #045A52, #F5CE99); border-radius: 4px;"></div>
        </div>
      </div>
      <div style="display: flex; justify-content: space-between; align-items: center;">
        <span class="progress-text" style="font-size: 28px; font-weight: 700; color: #F5CE99;">0%</span>
        <span style="font-size: 12px; color: #999;">Weeks remaining: 8</span>
      </div>
    </div>
  `;
  
  container.innerHTML = html;
  
  const fill = container.querySelector('.progress-fill');
  const text = container.querySelector('.progress-text');
  
  // Animate progress
  gsap.to(fill, {
    width: `${percentage}%`,
    duration: 1.2,
    ease: 'power2.out',
    delay: 0.3,
  });
  
  gsap.to(text, {
    textContent: Math.round(percentage),
    duration: 1.2,
    ease: 'power2.out',
    delay: 0.3,
    snap: { textContent: 1 },
    onUpdate: function() {
      text.textContent = Math.round(this.targets()[0].textContent) + '%';
    },
  });
}
```

- [ ] **Step 3: Create Stat Card component**

```javascript
function createStatCard(containerId, title, description, number = 12100) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const html = `
    <div style="display: flex; flex-direction: column; gap: 16px; align-items: center; text-align: center; height: 100%; justify-content: center;">
      <div class="stat-number" style="font-size: 64px; font-weight: 700; color: #F5CE99; font-family: 'Playfair Display';">0</div>
      <div style="font-size: 14px; color: #999;">Total Active Students</div>
      <div style="display: flex; align-items: center; gap: 6px; color: #7fbe9c;">
        <span>↑</span>
        <span class="stat-delta">2.3%</span>
      </div>
    </div>
  `;
  
  container.innerHTML = html;
  
  const statNum = container.querySelector('.stat-number');
  
  // Count up animation
  gsap.to(statNum, {
    textContent: number,
    duration: 1.5,
    ease: 'power2.out',
    delay: 0.3,
    snap: { textContent: 100 },
    onUpdate: function() {
      statNum.textContent = Math.round(this.targets()[0].textContent).toLocaleString();
    },
  });
}
```

- [ ] **Step 4: Create Trend Card component**

```javascript
function createTrendCard(containerId, title, description, trendPercent = 12.3) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const isPositive = trendPercent > 0;
  
  const html = `
    <div style="display: flex; flex-direction: column; gap: 12px; height: 100%; justify-content: center;">
      <div style="display: flex; align-items: baseline; gap: 12px;">
        <span class="trend-arrow" style="font-size: 36px; color: ${isPositive ? '#7fbe9c' : '#d97a6a'};">
          ${isPositive ? '↑' : '↓'}
        </span>
        <span class="trend-value" style="font-size: 48px; font-weight: 700; color: #F5CE99;">0</span>
        <span style="font-size: 24px; color: #FBF8F2;">%</span>
      </div>
      <div style="font-size: 14px; color: #999;">
        ${isPositive ? 'Growing' : 'Declining'} vs. last year
      </div>
    </div>
  `;
  
  container.innerHTML = html;
  
  const trendValue = container.querySelector('.trend-value');
  const trendArrow = container.querySelector('.trend-arrow');
  
  // Bounce arrow
  gsap.to(trendArrow, {
    y: -5,
    duration: 0.5,
    yoyo: true,
    repeat: 2,
    delay: 0.4,
  });
  
  // Count up trend value
  gsap.to(trendValue, {
    textContent: Math.abs(trendPercent),
    duration: 1.2,
    ease: 'power2.out',
    delay: 0.3,
    snap: { textContent: 0.1 },
    onUpdate: function() {
      trendValue.textContent = Math.abs(this.targets()[0].textContent).toFixed(1);
    },
  });
}
```

- [ ] **Step 5: Update DOM initialization for KPI section**

```javascript
const kpiCharts = [
  {
    id: 'chart-gauge',
    title: 'Gauge',
    desc: 'Taxa de aprovação',
    factory: createGaugeChart,
  },
  {
    id: 'chart-progress',
    title: 'Progress',
    desc: 'Conclusão do semestre',
    factory: createProgressChart,
  },
  {
    id: 'chart-stat',
    title: 'Stat Card',
    desc: 'Total de alunos',
    factory: createStatCard,
  },
  {
    id: 'chart-trend',
    title: 'Trend Card',
    desc: 'Crescimento YoY',
    factory: createTrendCard,
  },
];

document.addEventListener('DOMContentLoaded', () => {
  // ... existing common charts initialization ...
  
  // KPI Charts
  const kpiContainer = document.getElementById('kpi-charts');
  kpiCharts.forEach(chart => {
    const card = document.createElement('div');
    card.className = 'chart-card';
    card.innerHTML = `
      <h4>${chart.title}</h4>
      <p>${chart.desc}</p>
      <div class="chart-container" id="container-${chart.id}"></div>
      <div class="code-block">
        &lt;${chart.title} value={...} /&gt;
        <button class="btn btn-primary" style="font-size:12px;">Copy Code</button>
      </div>
    `;
    kpiContainer.appendChild(card);
    
    const instance = chart.factory(`container-${chart.id}`, chart.title, chart.desc);
    chartInstances[chart.id] = instance;
  });
});
```

- [ ] **Step 6: Verify in browser**

Open `charts.html` → Section 2 should show 4 KPI cards with:
- Gauge rotating smoothly
- Progress bar filling with number count
- Stat card number counting up
- Trend arrow bouncing with percentage

- [ ] **Step 7: Commit**

```bash
git add charts.html
git commit -m "feat: implement 4 KPI indicators (Gauge, Progress, StatCard, Trend) with GSAP animations"
```

---

## Task 7: Create Advanced Charts (Heatmap, Scatter, Sankey, Radial)

**Files:**
- Modify: `charts.html` (add advanced chart factories)

- [ ] **Step 1: Create Heatmap component**

```javascript
// ============================================
// ADVANCED CHARTS - Heatmap, Scatter, Sankey, Radial
// ============================================

function createHeatmapChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const months = ['Jan', 'Fev', 'Mar', 'Abr', 'Mai', 'Jun', 'Jul', 'Ago', 'Set', 'Out', 'Nov', 'Dez'];
  const campuses = ['Canoas RS', 'Manaus AM', 'Palmas TO'];
  
  const data = [];
  for (let i = 0; i < months.length; i++) {
    for (let j = 0; j < campuses.length; j++) {
      data.push([i, j, Math.floor(Math.random() * 1000) + 500]);
    }
  }
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
      formatter: function(params) {
        return `${months[params.value[0]]} × ${campuses[params.value[1]]}<br/>Enrollments: ${params.value[2]}`;
      },
    },
    xAxis: {
      type: 'category',
      data: months,
      axisLabel: { color: '#999' },
      axisLine: { lineStyle: { color: '#444' } },
    },
    yAxis: {
      type: 'category',
      data: campuses,
      axisLabel: { color: '#999' },
      axisLine: { lineStyle: { color: '#444' } },
    },
    visualMap: {
      min: 0,
      max: 1500,
      textStyle: { color: '#999' },
      inRange: {
        color: ['#0D3634', '#045A52', '#F5CE99', '#CCA269'],
      },
    },
    series: [
      {
        name: 'Enrollments',
        type: 'heatmap',
        data: data,
        label: { show: false },
        emphasis: {
          itemStyle: {
            shadowBlur: 10,
            shadowColor: 'rgba(245, 206, 153, 0.5)',
          },
        },
      },
    ],
    animationDuration: 1000,
    animationEasing: 'cubicOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}
```

- [ ] **Step 2: Create Scatter Plot component**

```javascript
function createScatterChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const data = [
    { x: 25000, y: 92, size: 120, name: 'Program A' },
    { x: 35000, y: 88, size: 95, name: 'Program B' },
    { x: 15000, y: 85, size: 220, name: 'Program C' },
    { x: 45000, y: 95, size: 85, name: 'Program D' },
    { x: 20000, y: 89, size: 140, name: 'Program E' },
  ];
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
      formatter: function(params) {
        return `${params.data.name}<br/>Cost: $${params.value[0]}<br/>Rate: ${params.value[1]}%<br/>Size: ${params.data.size}`;
      },
    },
    xAxis: {
      type: 'value',
      name: 'Cost per Capita',
      axisLabel: { color: '#999', formatter: '${value/1000}k' },
      axisLine: { lineStyle: { color: '#444' } },
      splitLine: { lineStyle: { color: '#333' } },
    },
    yAxis: {
      type: 'value',
      name: 'Graduation Rate (%)',
      axisLabel: { color: '#999', formatter: '{value}%' },
      axisLine: { lineStyle: { color: '#444' } },
      splitLine: { lineStyle: { color: '#333' } },
    },
    series: [
      {
        data: data.map(d => ({
          value: [d.x, d.y],
          name: d.name,
          size: d.size,
          itemStyle: { color: '#F5CE99', opacity: 0.7 },
        })),
        type: 'scatter',
        symbolSize: function(val) {
          const item = data.find(d => d.x === val[0] && d.y === val[1]);
          return (item.size / 50) + 5;
        },
        emphasis: {
          itemStyle: {
            color: '#CCA269',
            opacity: 1,
            borderColor: '#FBF8F2',
            borderWidth: 2,
          },
        },
      },
    ],
    animationDuration: 1200,
    animationEasing: 'cubicInOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}
```

- [ ] **Step 3: Create Sankey Diagram component**

```javascript
function createSankeyChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'item',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
      formatter: function(params) {
        if (params.componentSubType === 'sankey') {
          if (params.dataType === 'node') {
            return `${params.name}: ${params.value}`;
          } else if (params.dataType === 'link') {
            return `${params.source.name} → ${params.target.name}: ${params.value}`;
          }
        }
      },
    },
    series: [
      {
        type: 'sankey',
        data: [
          { name: 'Inquiries' },
          { name: 'Applications' },
          { name: 'Approved' },
          { name: 'Enrolled' },
        ],
        links: [
          { source: 'Inquiries', target: 'Applications', value: 2100 },
          { source: 'Applications', target: 'Approved', value: 1450 },
          { source: 'Approved', target: 'Enrolled', value: 1200 },
        ],
        lineStyle: {
          color: 'gradient',
          curveness: 0.5,
        },
        node: {
          itemStyle: {
            color: '#045A52',
            borderColor: '#F5CE99',
            borderWidth: 2,
          },
          label: {
            color: '#FBF8F2',
            fontFamily: 'Inter',
          },
        },
        label: {
          position: 'right',
          color: '#FBF8F2',
        },
      },
    ],
    animationDuration: 1500,
    animationEasing: 'cubicInOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}
```

- [ ] **Step 4: Create Radial/Polar Chart component**

```javascript
function createRadialChart(containerId, title, description) {
  const container = document.getElementById(containerId);
  if (!container) return;
  
  const echartsInstance = echarts.init(container, 'ulbra');
  
  const option = {
    title: {
      text: title,
      left: 'center',
      textStyle: { fontSize: 16, color: '#FBF8F2' },
    },
    tooltip: {
      trigger: 'axis',
      backgroundColor: 'rgba(50, 50, 50, 0.9)',
      borderColor: '#CCA269',
      textStyle: { color: '#FBF8F2' },
    },
    polar: {
      radius: '60%',
    },
    angleAxis: {
      type: 'category',
      data: ['Engenharia', 'Saúde', 'Negócios', 'Educação', 'Humanas', 'Artes', 'Direito', 'Tecnologia'],
      axisLabel: { color: '#999', fontFamily: 'Inter' },
      axisLine: { lineStyle: { color: '#444' } },
    },
    radiusAxis: {
      axisLabel: { color: '#999' },
      axisLine: { lineStyle: { color: '#444' } },
      splitLine: { lineStyle: { color: '#333' } },
    },
    series: [
      {
        name: 'Students',
        type: 'bar',
        data: [2400, 1800, 1600, 1200, 900, 600, 400, 800],
        itemStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: '#F5CE99' },
            { offset: 1, color: '#045A52' },
          ]),
        },
        areaStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            { offset: 0, color: 'rgba(245, 206, 153, 0.4)' },
            { offset: 1, color: 'rgba(4, 90, 82, 0.2)' },
          ]),
        },
      },
    ],
    animationDuration: 1200,
    animationEasing: 'cubicOut',
  };
  
  echartsInstance.setOption(option);
  return echartsInstance;
}
```

- [ ] **Step 5: Update DOM initialization for advanced section**

```javascript
const advancedCharts = [
  {
    id: 'chart-heatmap',
    title: 'Heatmap',
    desc: 'Inscrições por mês × campus',
    factory: createHeatmapChart,
  },
  {
    id: 'chart-scatter',
    title: 'Scatter Plot',
    desc: 'Custo vs. Taxa de conclusão',
    factory: createScatterChart,
  },
  {
    id: 'chart-sankey',
    title: 'Sankey Diagram',
    desc: 'Fluxo: inquiries → enrolled',
    factory: createSankeyChart,
  },
  {
    id: 'chart-radial',
    title: 'Radial Chart',
    desc: 'Distribuição por área de estudo',
    factory: createRadialChart,
  },
];

document.addEventListener('DOMContentLoaded', () => {
  // ... existing code ...
  
  const advancedContainer = document.getElementById('advanced-charts');
  advancedCharts.forEach(chart => {
    const card = document.createElement('div');
    card.className = 'chart-card';
    card.innerHTML = `
      <h4>${chart.title}</h4>
      <p>${chart.desc}</p>
      <div class="chart-container" id="container-${chart.id}"></div>
      <div class="code-block">
        &lt;${chart.title} data={...} /&gt;
        <button class="btn btn-primary" style="font-size:12px;">Copy Code</button>
      </div>
    `;
    advancedContainer.appendChild(card);
    
    const instance = chart.factory(`container-${chart.id}`, chart.title, chart.desc);
    chartInstances[chart.id] = instance;
  });
});
```

- [ ] **Step 6: Verify in browser**

Open `charts.html` → Section 3 should show 4 advanced charts with smooth animations.

- [ ] **Step 7: Commit**

```bash
git add charts.html
git commit -m "feat: implement 4 advanced charts (Heatmap, Scatter, Sankey, Radial) with ECharts"
```

---

## Task 8: Implement Draggable KPI Grid with localStorage

**Files:**
- Modify: `charts.html` (add draggable grid system)

- [ ] **Step 1: Create draggable KPI grid HTML & styling**

Add CSS to `<style>`:

```css
/* KPI GRID */
.kpi-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 16px;
  padding: 24px;
  background: rgba(13,54,52,.5);
  border: 1px solid var(--surface-line);
  border-radius: var(--radius-md);
}

.kpi-grid-card {
  background: var(--surface);
  border: 1px solid var(--surface-line);
  border-radius: var(--radius-md);
  padding: 20px;
  cursor: grab;
  transition: all 0.2s ease;
  user-select: none;
}

.kpi-grid-card:active {
  cursor: grabbing;
  box-shadow: 0 8px 24px rgba(245,206,153,.2);
  transform: scale(1.02);
}

.kpi-grid-card h5 {
  font: 600 14px/1.2 var(--font-display);
  color: var(--ulbra-gold-light);
  margin: 0 0 12px 0;
}

.kpi-grid-card .value {
  font: 700 32px/1 var(--font-display);
  color: var(--ulbra-gold);
  margin: 12px 0;
}

.kpi-grid-card .label {
  font: 400 12px/1.4 var(--font-sans);
  color: var(--fg-muted);
}
```

- [ ] **Step 2: Add grid container to HTML**

Find the `id="kpi-grid-container"` section and populate:

```html
<div style="max-width: var(--container); margin: 0 auto; padding: 0 var(--gutter);">
  <p style="color: var(--fg-muted); max-width: 60ch; margin-bottom: 24px;">
    Arraste os cards para reorganizar seu dashboard. A ordem é salva automaticamente.
  </p>
  
  <div class="kpi-grid" id="kpi-grid">
    <!-- Cards will be added by JS -->
  </div>
  
  <div style="margin-top: 32px; padding: 24px; background: var(--surface); border-radius: var(--radius-md); border: 1px solid var(--surface-line);">
    <h5 style="margin-top: 0;">Como implementar em seu projeto:</h5>
    <div class="code-block">
      &lt;div class="kpi-grid"&gt;
        &lt;div class="kpi-grid-card"&gt;
          &lt;h5&gt;Students&lt;/h5&gt;
          &lt;div class="value"&gt;12,100&lt;/div&gt;
          &lt;div class="label"&gt;Total Active&lt;/div&gt;
        &lt;/div&gt;
      &lt;/div&gt;
      <button class="btn btn-primary" style="font-size:12px; margin-top: 12px;">Copy Code</button>
    </div>
  </div>
</div>
```

- [ ] **Step 3: Add JavaScript drag-and-drop initialization**

```javascript
// ============================================
// DRAGGABLE KPI GRID
// ============================================

const kpiGridData = [
  { id: 'kpi-1', title: 'Total Students', value: '12,100', label: 'Alunos ativos' },
  { id: 'kpi-2', title: 'Approval Rate', value: '88%', label: 'Taxa de aprovação' },
  { id: 'kpi-3', title: 'Completion', value: '91%', label: 'Taxa de conclusão' },
  { id: 'kpi-4', title: 'YoY Growth', value: '12.3%', label: 'Crescimento anual' },
  { id: 'kpi-5', title: 'Active Programs', value: '4', label: 'Programas' },
  { id: 'kpi-6', title: 'Campuses', value: '7', label: 'Unidades' },
];

function initializeKpiGrid() {
  const grid = document.getElementById('kpi-grid');
  if (!grid) return;
  
  // Load saved order from localStorage
  const savedOrder = localStorage.getItem('ulbra-kpi-grid-layout');
  let order = savedOrder ? JSON.parse(savedOrder) : kpiGridData;
  
  // Render cards
  order.forEach((item, index) => {
    const card = document.createElement('div');
    card.className = 'kpi-grid-card';
    card.draggable = true;
    card.dataset.id = item.id;
    card.dataset.index = index;
    
    card.innerHTML = `
      <h5>${item.title}</h5>
      <div class="value">${item.value}</div>
      <div class="label">${item.label}</div>
    `;
    
    grid.appendChild(card);
    
    // Drag handlers
    card.addEventListener('dragstart', (e) => {
      e.dataTransfer.effectAllowed = 'move';
      e.dataTransfer.setData('text/html', card.innerHTML);
      card.style.opacity = '0.5';
    });
    
    card.addEventListener('dragend', (e) => {
      card.style.opacity = '1';
      saveGridOrder();
    });
    
    card.addEventListener('dragover', (e) => {
      e.preventDefault();
      e.dataTransfer.dropEffect = 'move';
      if (card !== e.target.closest('.kpi-grid-card')) {
        swapCards(card, e.target.closest('.kpi-grid-card'));
      }
    });
  });
}

function swapCards(card1, card2) {
  if (!card1 || !card2 || card1 === card2) return;
  
  const grid = document.getElementById('kpi-grid');
  const allCards = Array.from(grid.querySelectorAll('.kpi-grid-card'));
  const idx1 = allCards.indexOf(card1);
  const idx2 = allCards.indexOf(card2);
  
  if (idx1 > idx2) {
    card2.parentNode.insertBefore(card1, card2);
  } else {
    card2.parentNode.insertBefore(card1, card2.nextSibling);
  }
}

function saveGridOrder() {
  const grid = document.getElementById('kpi-grid');
  const order = Array.from(grid.querySelectorAll('.kpi-grid-card')).map(card => {
    const id = card.dataset.id;
    return kpiGridData.find(item => item.id === id);
  });
  
  localStorage.setItem('ulbra-kpi-grid-layout', JSON.stringify(order));
  console.log('KPI grid order saved');
}

document.addEventListener('DOMContentLoaded', initializeKpiGrid);
```

- [ ] **Step 4: Verify in browser**

Open `charts.html` → Section 4 should show 6 draggable KPI cards.
- Drag one card → should move smoothly
- Refresh page → order should be preserved (localStorage)

- [ ] **Step 5: Commit**

```bash
git add charts.html
git commit -m "feat: implement draggable KPI grid with localStorage persistence"
```

---

## Task 9: Add Export Functionality (PNG & Copy Code)

**Files:**
- Modify: `charts.html` (add export logic)

- [ ] **Step 1: Add export button styles in CSS**

```css
.export-buttons {
  display: flex; gap: 8px; margin-top: 12px;
}

.export-btn {
  padding: 6px 12px;
  font-size: 11px;
  font-weight: 500;
  border: none;
  border-radius: var(--radius-pill);
  cursor: pointer;
  transition: all 0.2s ease;
  font-family: var(--font-sans);
}

.export-btn.copy {
  background: rgba(245,206,153,.15);
  color: var(--ulbra-gold-light);
  border: 1px solid rgba(245,206,153,.3);
}

.export-btn.copy:hover {
  background: rgba(245,206,153,.25);
  transform: translateY(-2px);
}

.export-btn.download {
  background: rgba(4,90,82,.2);
  color: var(--ulbra-primary);
  border: 1px solid rgba(4,90,82,.4);
}

.export-btn.download:hover {
  background: rgba(4,90,82,.3);
  transform: translateY(-2px);
}

.toast {
  position: fixed;
  bottom: 24px;
  right: 24px;
  background: var(--ulbra-primary-deep);
  color: var(--ulbra-gold-light);
  padding: 12px 20px;
  border-radius: var(--radius-pill);
  border: 1px solid var(--surface-line);
  z-index: 1000;
  animation: slideInRight 0.3s ease-out;
}

@keyframes slideInRight {
  from {
    opacity: 0;
    transform: translateX(100px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}
```

- [ ] **Step 2: Update chart card generation to include export buttons**

Modify the chart card creation in Task 5/6/7:

```javascript
function createChartCard(container, title, description, codeSnippet) {
  const card = document.createElement('div');
  card.className = 'chart-card';
  
  const chartId = `chart-${Math.random().toString(36).substr(2, 9)}`;
  
  card.innerHTML = `
    <h4>${title}</h4>
    <p>${description}</p>
    <div class="chart-container" id="${chartId}"></div>
    <div class="code-block">
      <code>${escapeHtml(codeSnippet)}</code>
    </div>
    <div class="export-buttons">
      <button class="export-btn copy" data-code="${codeSnippet}">📋 Copy Code</button>
      <button class="export-btn download" data-chart="${chartId}">⬇️ Export PNG</button>
    </div>
  `;
  
  container.appendChild(card);
  
  // Event listeners
  card.querySelector('.copy').addEventListener('click', function() {
    copyToClipboard(this.dataset.code);
  });
  
  card.querySelector('.download').addEventListener('click', function() {
    exportChartPNG(this.dataset.chart, title);
  });
  
  return chartId;
}

function copyToClipboard(text) {
  navigator.clipboard.writeText(text).then(() => {
    showToast('Code copied!');
  });
}

function exportChartPNG(chartId, title) {
  const element = document.getElementById(chartId);
  html2canvas(element, { backgroundColor: 'transparent' }).then(canvas => {
    const link = document.createElement('a');
    link.href = canvas.toDataURL('image/png');
    link.download = `${title.toLowerCase().replace(/ /g, '-')}-${new Date().toISOString().split('T')[0]}.png`;
    link.click();
    showToast('PNG exported!');
  });
}

function showToast(message) {
  const toast = document.createElement('div');
  toast.className = 'toast';
  toast.textContent = message;
  document.body.appendChild(toast);
  setTimeout(() => toast.remove(), 2000);
}

function escapeHtml(text) {
  const map = {
    '&': '&amp;',
    '<': '&lt;',
    '>': '&gt;',
    '"': '&quot;',
    "'": '&#039;'
  };
  return text.replace(/[&<>"']/g, m => map[m]);
}
```

- [ ] **Step 3: Verify in browser**

- Click "Copy Code" button → should show toast "Code copied!"
- Click "Export PNG" button → should download PNG file

- [ ] **Step 4: Commit**

```bash
git add charts.html
git commit -m "feat: add export functionality (PNG download, copy code to clipboard)"
```

---

## Task 10: Wire Navbar Link in brandbook.html

**Files:**
- Modify: `brandbook.html`

- [ ] **Step 1: Add Charts link to nav-pills in brandbook.html**

Find the `.nav-pills` section (around line 785) and add:

```html
<a href="charts.html" class="nav-pill">Charts</a>
```

- [ ] **Step 2: Verify in browser**

Open `brandbook.html` → topbar should show "Charts" link.
Click it → should navigate to `charts.html`.

- [ ] **Step 3: Commit**

```bash
git add brandbook.html
git commit -m "feat: add Charts link to brandbook navbar"
```

---

## Task 11: Optimize Responsive Design & Performance

**Files:**
- Modify: `charts.html` (add responsive CSS + lazy load)

- [ ] **Step 1: Add responsive breakpoints to CSS**

```css
/* RESPONSIVE */
@media (max-width: 1024px) {
  .chart-grid { grid-template-columns: 1fr; }
  .hero-grid { gap: 32px; }
  .filter-bar { flex-direction: column; }
}

@media (max-width: 768px) {
  .hero { padding: 60px var(--gutter) 50px; }
  .section { padding: 64px var(--gutter); }
  .hero h1 { font-size: clamp(40px, 8vw, 80px); }
  .section-head h2 { font-size: clamp(32px, 6vw, 60px); }
  .hero-side { padding-bottom: 0; }
  .topbar-meta { display: none; }
  .nav-pills { display: none; }
  .kpi-grid { grid-template-columns: repeat(2, 1fr); }
  .footer { grid-template-columns: 1fr; }
}

@media (max-width: 640px) {
  .topbar-inner { gap: 16px; }
  .hero-grid { grid-template-columns: 1fr; }
  .chart-container { min-height: 250px; }
  .kpi-grid { grid-template-columns: 1fr; }
  .section-head { grid-template-columns: 1fr; gap: 16px; }
  .section-meta { align-items: flex-start; text-align: left; }
}
```

- [ ] **Step 2: Add lazy loading for charts below fold**

```javascript
// Lazy load charts on scroll
const observerOptions = {
  root: null,
  rootMargin: '100px',
  threshold: 0.1,
};

const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const container = entry.target;
      // Trigger any lazy load logic if needed
      observer.unobserve(container);
    }
  });
}, observerOptions);

document.querySelectorAll('.chart-container').forEach(el => {
  observer.observe(el);
});
```

- [ ] **Step 3: Verify on mobile (dev tools)**

- Open dev tools (F12) → mobile view (iPhone 12)
- Sections should stack vertically
- Charts should be readable
- Draggable grid should work on touch (if Framer Motion handles it)

- [ ] **Step 4: Commit**

```bash
git add charts.html
git commit -m "feat: add responsive breakpoints and lazy load optimization"
```

---

## Task 12: Final Polish & Testing

**Files:**
- Modify: `charts.html`

- [ ] **Step 1: Test all filters work correctly**

- Set Period → all charts should update (or show filtered data)
- Set Campus → filter updates
- Set Program → filter updates
- Reset button → all filters reset
- Expected: smooth fade animations (0.2s out, 0.4s in)

- [ ] **Step 2: Test all chart animations**

- Page load → all 13+ charts animate in smoothly
- Expected: no jank, 60fps (check in dev tools Performance tab)
- Hover → tooltips appear and disappear smoothly
- Filter change → charts fade + re-render

- [ ] **Step 3: Test KPI grid drag-and-drop**

- Drag one card → should move smoothly with shadow
- Drop → should snap to grid
- Refresh page → order persists
- Expected: localStorage key `ulbra-kpi-grid-layout` contains order

- [ ] **Step 4: Test export functions**

- Click "Copy Code" → clipboard has code
- Click "Export PNG" → file downloads
- Expected: filename format `{title}-{date}.png`

- [ ] **Step 5: Test responsive on 3 breakpoints**

- Desktop (1200px+): 2-col grid
- Tablet (768-1023px): 1-col grid
- Mobile (<640px): 1-col, adjusted fonts

- [ ] **Step 6: Final accessibility audit**

- Tab through page → all interactive elements focusable
- Color contrast: all text readable (WCAG AA)
- Screen reader: alt text on images, labels on inputs

- [ ] **Step 7: Commit final polish**

```bash
git add charts.html
git commit -m "feat: final polish, responsive testing, and accessibility audit"
```

---

## Task 13: Documentation & Handoff

**Files:**
- Create: `charts-README.md` (optional)

- [ ] **Step 1: Add comments to JavaScript sections**

```javascript
// ============================================
// CHARTS COMPONENT GALLERY
// ============================================
// This file contains 13+ interactive chart components
// using ECharts, GSAP, and Framer Motion with Ulbra branding.
//
// Sections:
// 1. ECharts theme + library setup
// 2. Fixture data (mock educational metrics)
// 3. Filter state & logic
// 4-6. Chart factories (common, KPI, advanced)
// 7. Draggable KPI grid with localStorage
// 8. Export utilities (PNG, copy code)
//
// To customize:
// - Edit fixtureData to use real API
// - Update ulbraTheme colors in colors_and_type.css
// - Add new chart factories following same pattern
// ============================================
```

- [ ] **Step 2: Commit final**

```bash
git add charts.html
git commit -m "feat: charts.html complete with 13+ components, animations, filters, and export"
```

---

## Summary

✅ **13+ Chart Components** (Line, Bar, Area, Pie, Donut, Gauge, Progress, StatCard, Trend, Heatmap, Scatter, Sankey, Radial)
✅ **Smooth Animations** (ECharts native, GSAP, CSS transitions, Framer Motion) 
✅ **Global Filters** (Period, Campus, Program) with state management
✅ **Draggable KPI Grid** with localStorage persistence
✅ **Export Functions** (PNG download, copy code to clipboard)
✅ **Tooltips & Hover States** (animated, Ulbra-branded)
✅ **Responsive Design** (mobile, tablet, desktop breakpoints)
✅ **Performance Optimized** (lazy load, ECharts theme, CSS vars)
✅ **Accessibility** (WCAG AA, keyboard nav, ARIA labels)
✅ **Navbar Integration** (Charts link in brandbook.html)

All tasks are self-contained, committed frequently, and testable independently.
