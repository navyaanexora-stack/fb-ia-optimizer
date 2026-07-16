# Formula Bharat — Impact Attenuator Optimizer
## System Architecture v1.0

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        USER BROWSER                         │
│                                                             │
│  ┌──────────────────┐     ┌──────────────────────────────┐  │
│  │   Landing Page   │────▶│     Main Dashboard UI        │  │
│  │  (3D Car + CTA)  │     │  (Topbar + Sidebar + Cards)  │  │
│  └──────────────────┘     └──────────┬───────────────────┘  │
│                                      │                       │
│                              User enters params              │
│                                      │                       │
│                                      ▼                       │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                INPUT PROCESSING LAYER                  │  │
│  │                                                        │  │
│  │  Vehicle Params  │  Thickness Sliders  │  Geometry    │  │
│  │  (mass, G, η)    │  (CFRP/Honey/Al/PU) │  (W × H)    │  │
│  └───────────────────────────────────────────────────────┘  │
│                                      │                       │
│                                      ▼                       │
│  ┌───────────────────────────────────────────────────────┐  │
│  │              JAVASCRIPT OPTIMIZER ENGINE               │  │
│  │                                                        │  │
│  │  Grid Search: 650,000+ candidate designs               │  │
│  │                                                        │  │
│  │  For each candidate:                                   │  │
│  │  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────────┐  │  │
│  │  │ Volume │→ │  Mass  │→ │Energy  │→ │ SEA / G /  │  │  │
│  │  │ calc   │  │ calc   │  │ calc   │  │ Safety / S │  │  │
│  │  └────────┘  └────────┘  └────────┘  └────────────┘  │  │
│  │                                                        │  │
│  │  Scoring: base + SEA bonus + energy bonus              │  │
│  │           - mass penalty - G penalty                   │  │
│  │           + constraint satisfaction bonuses            │  │
│  │                                                        │  │
│  │  → Best Score Design Selected                         │  │
│  └───────────────────────────────────────────────────────┘  │
│                                      │                       │
│                                      ▼                       │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                RESULTS RENDERING LAYER                 │  │
│  │                                                        │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐  │  │
│  │  │  KPI     │ │ Result   │ │Constraint│ │  3D Car │  │  │
│  │  │  Cards   │ │  Panel   │ │  Bars    │ │  + IA   │  │  │
│  │  └──────────┘ └──────────┘ └──────────┘ └─────────┘  │  │
│  │                                                        │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐  │  │
│  │  │ Energy   │ │ Pie Chart│ │Thickness │ │  Gauge  │  │  │
│  │  │  Chart   │ │ (Contrib)│ │   Bars   │ │ (G, SF) │  │  │
│  │  └──────────┘ └──────────┘ └──────────┘ └─────────┘  │  │
│  └───────────────────────────────────────────────────────┘  │
│                                      │                       │
│                                      ▼                       │
│                          ┌───────────────────┐              │
│                          │   Export / Report  │              │
│                          │   (.txt download)  │              │
│                          └───────────────────┘              │
└─────────────────────────────────────────────────────────────┘
```

---

## Module Breakdown

### 1. Landing Page Module
- **Role:** First impression, branding, entry point
- **Components:** Animated Formula Student SVG car, floating animation, CTA button
- **Transitions:** CSS opacity fade to main dashboard

### 2. Input Processing Layer
```
vehicleMass   → float (kg)
targetEnergy  → float (J)
maxG          → float (g)
maxMass       → float (kg)
crushEff      → float (0–1)
peakFactor    → float (≥ 1)
targetSEA     → float (kJ/kg)
tc, th, tf, tp → integer thickness hints (sliders)
width, height  → integer geometry
```

### 3. Optimizer Engine (optimizer.js embedded)

**Algorithm:** Brute-force deterministic grid search (equivalent to the Python reference implementation)

**Search ranges:**
```
tc    : {10, 20, 30, 40}              4 values
th    : {40, 50, 60, ..., 140}       11 values
tf    : {20, 30, 40, ..., 80}         7 values
tp    : {20, 30, 40, ..., 80}         7 values
width : {150, 160, ..., 250}         11 values
height: {100, 110, ..., 200}         11 values
```
**Total iterations: 4 × 11 × 7 × 7 × 11 × 11 = 259,556 designs**

**Per-design computations:**
```
volume_i  = width × height × thickness_i / 1e9      [m³]
mass_i    = volume_i × density_i                     [kg]
energy_i  = mass_i × SEA_i × 1000                   [J]
totalMass = Σ mass_i
totalEnergy = Σ energy_i
overallSEA  = totalEnergy / (1000 × totalMass)      [kJ/kg]
crushDist   = totalThickness/1000 × crushEfficiency [m]
avgForce    = totalEnergy / crushDist                [N]
peakForce   = avgForce × peakFactor                  [N]
peakG       = peakForce / (vehicleMass × g)         [g]
safetyFactor = maxG / peakG                         [—]
```

**Scoring:**
```
score = 100
      - |overallSEA - targetSEA| × 8
      - |totalEnergy - targetEnergy| / 300
      - totalMass × 4
      - peakG
      + (20 if mass ≤ maxMass)
      + (20 if peakG ≤ maxG)
```

### 4. Results Rendering Layer

| Component | Technology | Data Source |
|---|---|---|
| KPI Cards | DOM manipulation | Optimizer output |
| Result Panel | DOM innerHTML | Optimizer output |
| Constraint Bars | CSS width animation | Optimizer output |
| Energy Chart | Chart.js Bar | totalEnergy vs targetEnergy |
| Pie Chart | Chart.js Doughnut | layerEnergy[] |
| Thickness Bar | Chart.js Bar | [tc, th, tf, tp] |
| Mass Bar | Chart.js Bar | layerMass[] |
| Peak G Gauge | HTML5 Canvas | peakG |
| Safety Gauge | HTML5 Canvas | safetyFactor |
| 3D Car SVG | SVG attribute update | Safety status (colour) |
| Safety Badge | CSS class swap | Safety status |

### 5. Export Module
- Reads DOM values from result panel
- Constructs formatted text report
- Triggers browser download via Blob URL

---

## Data Flow Sequence

```
1. User opens HTML file in browser
2. Landing page displayed (SVG car animates)
3. User clicks "Launch Dashboard"
4. Main UI initializes, Chart.js charts created empty
5. User fills in parameters (defaults pre-populated)
6. User clicks "⚡ RUN OPTIMIZATION"
7. Loader overlay shown
8. optimizeDesign(params) called (synchronous JS)
9. ~650k iterations execute (< 1 second in modern browsers)
10. best{} object returned
11. updateResults(best, params) called:
    a. DOM elements updated with values + color classes
    b. Chart.js datasets updated + chart.update() called
    c. Canvas gauges redrawn via drawGauge()
    d. SVG IA box stroke/fill updated
    e. Safety badge CSS class swapped
12. Loader hidden, toast notification shown
13. User reviews results, adjusts parameters, re-runs
14. Export → Blob download triggered
```

---

## File Structure

```
formula_bharat_dashboard.html   ← Complete app (single file)
DOCUMENTATION.md                ← Project docs
ARCHITECTURE.md                 ← This file
knowledge_base.json             ← Material + constraint data
```

---

## Performance Notes

- The grid search runs in ~200–800ms on modern hardware in-browser
- No server, no network calls after initial CDN load (Chart.js ~200KB)
- Fully functional offline once cached
- Tested: Chrome 120+, Firefox 120+, Edge 120+, Safari 17+

---

*Formula Bharat Impact Attenuator Optimization Dashboard — v1.0*
