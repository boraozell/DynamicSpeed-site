# DynamicSpeed

**Simulation-based decision support for variable speed limits at motorway bottlenecks**

DynamicSpeed is an independent industrial-engineering research and portfolio project exploring a practical operations question: **Can changing speed limits in response to traffic conditions improve the performance of a congested road? And which control policy works better under which conditions?**

The project combines **SUMO**, **Python**, **TraCI**, controlled experiments and a browser-based traffic replay/decision-support interface. It is a **synthetic research prototype**, not a live traffic-control system.

**Interactive project:** https://boraozel.github.io/DynamicSpeed-site/index.html

> **Research status (November 2026):** The initial 3-seed experiment is completed. An expanded 15-seed experiment is currently in progress; no results from it are reported here until the runs and validation are complete.

## The problem

Consider a 6 km simulated motorway where traffic narrows from **three lanes to one lane**, then expands back to three. Vehicles can accumulate upstream of the bottleneck, causing stop-and-go waves and longer journeys. DynamicSpeed tests whether different **variable speed limit (VSL) policies** can manage the approach to this bottleneck more effectively than a fixed limit.

## What I built

- A reproducible **6 km, 3 → 1 → 3 lane** motorway model in SUMO, with Python/TraCI control.
- Four speed-limit policies, evaluated under matched scenario inputs and random seeds.
- A measurement pipeline for system-level objectives and operational KPIs.
- A browser interface with recorded vehicle replays, policy comparisons, charts, downloadable observations and an interactive experiment explorer.

**My contribution:** End-to-end modeling, control-policy implementation, experiment design, analysis and the interactive research presentation. This is an individual portfolio/research project and not a deployed industrial product.

## Four policies, in plain English

| Policy | How it decides |
| --- | --- |
| **Static 100** | Keeps the speed limit at 100 km/h regardless of changing traffic. The baseline. |
| **Rule-Based** | Selects a speed-limit target from a few predefined rules based on the measured speed near the bottleneck. |
| **MPC (simplified)** | Uses a short-horizon, approximate traffic-state prediction and selects a speed-limit action by minimizing its internal predicted cost. This is a simplified MPC prototype, not a calibrated full-scale traffic controller. |
| **Weather-Aware** | Adapts Rule-Based decisions with experimental speed-limit caps for synthetic weather scenarios. It is identical to Rule-Based in the dry scenario. |

The controller updates every **60 seconds**. Changes are limited to a maximum step of **10 km/h per update**. The tested target levels and restrictions are modeling assumptions—not real-world recommended speed limits.

## Experiment design

| Dimension | Initial experiment |
| --- | --- |
| Network | 6 km, 3 → 1 → 3 lane bottleneck |
| Simulated duration | 90 minutes |
| Traffic demand | 2,600 or 3,400 vehicles/hour |
| Weather | Synthetic dry, light rain, heavy rain |
| Random seeds | 314, 909, 2718 |
| Policies | Static, Rule-Based, simplified MPC, Weather-Aware |
| Policy-result records | 3 × 2 × 3 × 4 = **72** |

For matched weather, demand and seed, the policies use the same route input. **The 72 records are not all independent simulations:** in dry conditions Weather-Aware uses the same behavior/results as Rule-Based, and those records are reproduced as policy-specific outputs for consistency checks.

**Expanded study underway:** 15 seeds × 3 weather scenarios × 2 demand levels × 4 policies = **360 planned policy-result records**. The expanded study will be interpreted only after the full outputs are available.

## How performance is measured

The experiment reports an objective:

\[
J = H + 0.25 U + 0.03 C
\]

- **H:** cumulative vehicle-hours, including modeled input-queue waiting.
- **U:** vehicles unfinished at the end of the 90-minute horizon.
- **C:** speed-limit changes expressed in 10 km/h units.

**Lower J is better under this explicitly chosen objective.** The coefficients are *experimental penalty weights*, not universal engineering constants. We also report operational KPIs such as completed/unfinished vehicles, observed speeds, stopped-vehicle sample shares and harsh-braking proxies; these should not be confused with measured crash risk.

## Initial finding (three seeds only)

For the **dry-road, 2,600 vehicles/hour** scenario, the recorded mean objectives were:

| Policy | Mean J | Relative to Static 100 |
| --- | ---: | ---: |
| Static 100 | 603.20 | Baseline |
| Rule-Based | 547.57 | **9.22% lower** |
| Simplified MPC | 562.14 | **6.81% lower** |

*The same dry-weather Rule-Based behavior is reproduced by Weather-Aware.*

**Interpretation:** In this narrow scenario, simple feedback rules outperform the tested simplified MPC under the chosen J objective. This does **not** show that Rule-Based is universally superior, nor that the reported percentage is statistically robust. Other weather/demand conditions can produce different rankings.

### An open research question

**Why does Rule-Based outperform the simplified MPC in this scenario?** Possible explanations include an inaccurate traffic-state approximation, a short prediction horizon, or an internal MPC cost that does not align with the evaluation objective J. These are **hypotheses, not established causes**. We will test them using paired comparisons, decision logs and forecast diagnostics.

## Important limitations

1. **Synthetic network and weather.** The model is not calibrated or validated for a specific public road.
2. **Initial sample size.** Three random seeds per condition are insufficient for strong claims about statistical significance; the larger study addresses this limitation.
3. **Replay coverage.** The interactive 3D vehicle replay currently covers **dry weather, 2,600 vehicles/hour, seed 314**, comparing Rule-Based and MPC. Other experiments are represented by recorded summary outcomes, not invented vehicle traces.
4. **Safety interpretation.** Hard-braking and speed-variation measures are proxies, not collision outcomes or proof of improved road safety.
5. **Scope.** The interface is a decision-support demonstration; it cannot issue speed limits to real roads or run live SUMO simulations directly in the browser.

## Repository contents and reproducibility

The analysis was developed in **Google Colab** using Python and SUMO/TraCI. Key research artifacts include:

- `DynamicSpeed_12_WeatherAware_CLEAN_v2.ipynb` — experimental model, four policies, metrics and recorded scenarios.
- `DynamicSpeed_13_Statistical_Validation.ipynb` — initial statistical analysis.
- `DynamicSpeed_16_Expanded_15Seeds_Colab.ipynb` — planned 15-seed extension (in progress).
- `DynamicSpeed_17_MPC_Model_Audit.ipynb` — model-prediction and decision-audit workflow.
- `index.html` — self-contained portfolio interface (only if the site's HTML file is kept in this repository).

**Note:** The notebook names above describe research artifacts; include them in the repository before representing them as downloadable files. To reproduce the study, open the experiment notebook in Colab, install the required SUMO dependencies as indicated in its setup cells, and run the notebook. The model generates network and scenario files programmatically; execution times depend on the available runtime.

## Next research steps

- Finish the 15-seed expansion and publish paired effect sizes and uncertainty intervals.
- Audit MPC forecasts and compare internal optimization costs with observed J.
- Explain how the preferred policy changes by demand and weather condition.
- Extend operational KPI reporting and document sensitivity to modeling assumptions.

## Professional relevance

Although the example is motorway traffic, the underlying workflow generalizes to **operations research, bottleneck analysis, discrete-event/system simulation, scenario evaluation, KPI design and data-driven decision support** in logistics, manufacturing and business analytics.

---

### About the project

**Bora Özel** · Industrial Engineering background · Independent project

*Portfolio research. All numerical results are simulation outputs, not real-world operational or safety claims.*
