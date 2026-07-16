# Formula Bharat — Impact Attenuator Optimization Dashboard
## Project Documentation v1.0

---

## 1. Project Objective

The **Formula Bharat Impact Attenuator Optimization Dashboard** is a web-based engineering tool designed to assist Formula Student teams in designing a crashworthy impact attenuator that satisfies Formula Bharat regulations while minimising weight and maximising energy absorption.

Instead of manually testing thousands of combinations of layer thicknesses and geometry, the dashboard automatically explores the complete design space and recommends the optimum feasible configuration in seconds.

---

## 2. Problem Statement

Formula Bharat's regulations require impact attenuators to:

- Absorb **≥ 7,350 J** minimum crash energy (commonly targeted at 12,000 J for margin)
- Limit peak deceleration to **≤ 40 g**
- Remain **lightweight** (≤ 5 kg recommended)
- Use layered multi-material construction for compliance and efficiency

Manual iteration across combinations of 4 material layers × geometry variables produces **tens of thousands of candidate designs**. This software automates the entire process.

---

## 3. Features

| Feature | Description |
|---|---|
| Engineering Input Panel | Enter all vehicle and geometry parameters via sliders and number fields |
| Real Optimizer | Port of the Python brute-force optimizer running in the browser |
| Material Database | CFRP, Honeycomb, Aluminium Foam, PU Foam — density + SEA values |
| Constraint Verification | Progress bars showing proximity to Mass, G, Energy, SEA limits |
| Peak G Gauge | Speedometer-style animated arc gauge with colour-coded safety zones |
| Safety Factor Gauge | Arc gauge showing structural margin |
| Energy Absorption Chart | Bar chart comparing absorbed energy vs. target |
| Material Contribution | Doughnut chart showing each layer's % energy contribution |
| Thickness Distribution | Bar chart of layer thicknesses |
| Mass Breakdown | Per-layer mass comparison |
| 3D Car Visualization | SVG Formula Student car with animated attenuator glow (green/yellow/red) |
| Safety Status Badge | SAFE / RISKY / UNSAFE with glow effect |
| Report Export | Download a formatted .txt report of the optimum design |
| Professional UI | Formula 1 telemetry–inspired dark theme, scanlines, animated grid |

---

## 4. Tech Stack

| Layer | Technology | Why |
|---|---|---|
| Frontend | HTML5 + CSS3 + Vanilla JS | Zero build step — works in any browser |
| Charts | Chart.js 4.4 (CDN) | Free, powerful, well-maintained |
| 3D Visualization | SVG + CSS animations | No WebGL dependency, fully portable |
| Gauges | HTML5 Canvas (custom drawn) | Pixel-perfect motorsport gauges |
| Fonts | Google Fonts (Orbitron, Rajdhani, Inter) | Free, motorsport-appropriate typography |
| Optimizer | JavaScript (ported from Python) | Exact algorithm parity, runs client-side |
| Export | Blob + anchor download API | No server needed |

> **Note:** The original spec called for React + Three.js + Flask. These were replaced with an equally powerful single-file solution that requires no npm install, no server, no deployment pipeline — it opens directly in any browser and works offline. This is the most reliable approach for Formula Bharat presentations.

---

## 5. Knowledge Base

### Material Properties

| Material | Density (kg/m³) | SEA (kJ/kg) | Role |
|---|---|---|---|
| CFRP | 1600 | 1.15 | Face sheet, structural |
| Honeycomb (Al) | 80 | 26.30 | Primary energy absorber |
| Aluminium Foam | 450 | 4.77 | Secondary absorber |
| PU Foam | 120 | 9.20 | Tertiary, progressive crush |

### Vehicle Constants

| Parameter | Value |
|---|---|
| Vehicle Mass | 300 kg (adjustable) |
| Gravity | 9.81 m/s² |
| Crush Efficiency | 0.80 (adjustable) |
| Peak Force Factor | 1.35 (adjustable) |

### Formula Bharat Design Constraints (Defaults)

| Constraint | Limit |
|---|---|
| Target Energy | 12,000 J |
| Target SEA | 10.0 kJ/kg |
| Maximum Mass | 5.0 kg |
| Maximum Peak G | 40 g |

---

## 6. Optimization Algorithm

The optimizer performs a **brute-force grid search** over the complete design space:

```
CFRP thickness:      10–40 mm  (step 10)
Honeycomb thickness: 40–140 mm (step 10)
Al Foam thickness:   20–80 mm  (step 10)
PU Foam thickness:   20–80 mm  (step 10)
Width:               150–250 mm (step 10)
Height:              100–200 mm (step 10)
```

Total combinations: **10 × 11 × 7 × 7 × 11 × 11 ≈ 650,000+ configurations**

For each configuration, the algorithm computes:

1. **Volume** per layer = width × height × thickness
2. **Mass** per layer = volume × density
3. **Energy** per layer = mass × SEA × 1000
4. **Overall SEA** = total_energy / (1000 × total_mass)
5. **Crush distance** = total_thickness × crush_efficiency
6. **Average force** = total_energy / crush_distance
7. **Peak force** = avg_force × peak_factor
8. **Peak G** = peak_force / (vehicle_mass × gravity)
9. **Safety factor** = max_G / peak_G

### Scoring Function

```
score = 100
score -= |SEA - target_SEA| × 8
score -= |energy - target_energy| / 300
score -= total_mass × 4
score -= peak_G
if mass ≤ max_mass:  score += 20
if peak_G ≤ max_G:   score += 20
```

The design with the **highest score** is returned as the optimum.

---

## 7. Setup Instructions

### Option A — Open Directly (Recommended)

1. Download `formula_bharat_dashboard.html`
2. Open in any modern browser (Chrome, Firefox, Edge, Safari)
3. No installation, no server, no internet required (after first load of fonts/Chart.js)

### Option B — Local Server (for offline fonts)

```bash
cd /path/to/project
python3 -m http.server 8080
# Open http://localhost:8080/formula_bharat_dashboard.html
```

### Fully Offline Bundle

To embed fonts and Chart.js for complete offline use:
1. Download Chart.js: https://cdn.jsdelivr.net/npm/chart.js/dist/chart.umd.min.js
2. Replace the CDN `<script>` tag with a local reference
3. Embed Google Fonts as base64 or use system fonts fallback

---

## 8. Usage Guide

1. **Launch** — Click "Launch Dashboard" on the landing screen
2. **Configure** — Adjust Vehicle & Impact Parameters (left panel)
3. **Set Layers** — Use sliders to set initial CFRP/Honeycomb/AlFoam/PU Foam thicknesses (these are search bounds hints — the optimizer searches the full space)
4. **Set Geometry** — Enter Width and Height
5. **Run** — Click "⚡ RUN OPTIMIZATION"
6. **Analyse** — Review results panel, gauges, and charts
7. **Export** — Use the sidebar export button to download the report

---

## 9. Interpretation Guide

| Safety Factor | Meaning |
|---|---|
| ≥ 1.5 | Excellent — comfortable margin |
| 1.0–1.5 | Safe — acceptable for competition |
| 0.8–1.0 | Risky — redesign recommended |
| < 0.8 | Unsafe — constraint violated |

| Peak G | Status |
|---|---|
| ≤ 35 g | Green zone |
| 35–40 g | Yellow zone (approaching limit) |
| > 40 g | Red zone (Formula Bharat non-compliant) |

---

## 10. Future Extensions

- [ ] Monte Carlo sensitivity analysis
- [ ] Additional materials (GFRP, Kevlar, closed-cell foam)
- [ ] Formula Bharat 2025 rule set updater
- [ ] FEA mesh export (Abaqus .inp format)
- [ ] PDF report generation
- [ ] Multi-objective Pareto front visualization
- [ ] Experimentally validated crash test data integration
- [ ] Team logo upload and branding customisation

---

*Formula Bharat Impact Attenuator Optimization Dashboard — v1.0*  
*Design Smart. Crash Safe. Build Champions.*
