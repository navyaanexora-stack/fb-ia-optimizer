# Formula Bharat — Impact Attenuator Optimization Dashboard

> **Design Smart. Crash Safe. Build Champions.**

---

## Quick Start (30 seconds)

```
1. Download formula_bharat_dashboard.html
2. Double-click to open in Chrome / Firefox / Edge
3. Click "Launch Dashboard"
4. Set your parameters → Click ⚡ RUN OPTIMIZATION
```

**That's it. No npm. No Python. No server. No installation.**

---

## What's Included

| File | Description |
|---|---|
| `formula_bharat_dashboard.html` | **Complete application** — all UI, optimizer, charts |
| `DOCUMENTATION.md` | Full project documentation |
| `ARCHITECTURE.md` | System architecture and data flow |
| `knowledge_base.json` | Engineering data used by the optimizer |
| `README.md` | This file |

---

## Features at a Glance

- ✅ **Real optimizer** — exact port of your Python algorithm (250k+ iterations)
- ✅ **Professional UI** — Formula 1 telemetry dark theme
- ✅ **Interactive inputs** — sliders + number fields with live feedback  
- ✅ **5 charts** — energy bar, material contribution pie, thickness bar, mass bar, gauges
- ✅ **Animated arc gauges** — Peak G + Safety Factor with colour zones
- ✅ **3D animated car** — SVG Formula Student car with attenuator glow (green/yellow/red)
- ✅ **Safety badge** — SAFE / RISKY / UNSAFE with visual feedback
- ✅ **Constraint verification** — progress bars for all 4 design constraints
- ✅ **Report export** — download .txt design report
- ✅ **Zero dependencies** — works completely offline

---

## Browser Requirements

| Browser | Minimum Version |
|---|---|
| Chrome | 90+ |
| Firefox | 90+ |
| Edge | 90+ |
| Safari | 15+ |

---

## How the Optimizer Works

The optimizer searches 250,000+ candidate designs using the exact brute-force grid search algorithm from your Python code. For each combination of material layer thicknesses and geometry, it computes:

```
Volume → Mass → Energy → SEA → Crush Distance → 
Peak Force → Peak G → Safety Factor → Score
```

The highest-scoring design that best satisfies all Formula Bharat constraints is returned.

---

## Optimization Score Interpretation

| Score | Quality |
|---|---|
| > 120 | Excellent — highly optimised |
| 100–120 | Good — meets objectives |
| 80–100 | Acceptable — some trade-offs |
| < 80 | Poor — constraints may be violated |

---

## Default Design Parameters

| Parameter | Default |
|---|---|
| Vehicle Mass | 300 kg |
| Target Energy | 12,000 J |
| Max Peak G | 40 g |
| Max Mass | 5.0 kg |
| Crush Efficiency | 0.80 |
| Peak Factor | 1.35 |
| Target SEA | 10.0 kJ/kg |

---

## For Formula Bharat Evaluators

This dashboard demonstrates:

1. **Engineering rigour** — real physics-based optimization with correct Formula Bharat constraints
2. **Software capability** — complete web application built from engineering fundamentals  
3. **Visualization** — professional motorsport telemetry UI for result communication
4. **Documentation** — full project architecture, knowledge base, and documentation

---

*Formula Bharat Impact Attenuator Optimization Dashboard v1.0*  
*All calculations based on Formula Bharat Technical Regulations.*
