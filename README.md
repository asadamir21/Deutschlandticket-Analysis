# Deutschlandticket Commute Attractiveness Analysis

Estimating how attractive public transport is for employees commuting to **Johnson & Johnson Medical GmbH** (Robert-Koch-Straße 1, 22851 Norderstedt), and how likely they'd be to switch to the **Deutschlandticket**, using a synthetic employee dataset.

## Overview

Real employee address data can't be used for an exercise like this, so the notebook simulates a synthetic workforce spread across Hamburg and the surrounding commuter belt, then works through the full pipeline end to end:

1. Generate a synthetic employee dataset (home locations, demographics)
2. Assign each employee their nearest public-transport station
3. Estimate a door-to-door commute time
4. Group employees into commute-time bands
5. Score each employee's Deutschlandticket adoption likelihood
6. Sanity-check the results and identify the key drivers
7. Summarize findings for the business
8. (Optional) Visualize everything on an interactive map

Every formula and assumption is documented inline in the notebook, so it's straightforward to swap in real data (e.g. an HVV journey-planner API) later.

## Repo contents

| File | Description |
|---|---|
| `Deutschlandticket_Analysis.ipynb` | Main analysis notebook — run top to bottom |
| `synthetic_employee_commute_dataset.csv` | Generated synthetic dataset (output of the notebook) |
| `commute_map.html` | Optional interactive map of employees, stations, and the workplace |
| `README.md` | This file |

## Method summary

- **Synthetic employees:** ~400 employees sampled across 10 representative home areas in Hamburg and nearby towns, with a small random offset per employee so addresses aren't identical. No real employee data is used anywhere.
- **Nearest station:** each employee is matched to their closest of 10 real reference stations along the corridor into Norderstedt, tagged as `Rail` (U-/S-Bahn/AKN) or `Bus`.
- **Commute time:** `walk to station + wait + ride to Norderstedt + fixed last-mile`, using two speed/wait tiers (Rail vs. Bus). Transfers between lines aren't modeled — a deliberate simplification, noted in the notebook.
- **Adoption score (0–100):** a transparent weighted formula — 50% commute time, 30% environmental attitude, 20% cost-saving appeal from a longer commute distance — with a flat penalty for existing car owners. Bucketed into Low / Medium / High potential.
- **Validation:** sanity checks (no negative times, scores in range) plus a correlation check confirming the score responds to commute time, station distance, and environmental attitude as expected.

## Key findings (from the synthetic run)

- ~44% of employees fall outside a 60-minute door-to-door commute.
- Norderstedt and Langenhorn residents are the best connected (~30–40 min); outlying towns like Kaltenkirchen are the least attractive (~85+ min).
- Commute time and distance to the nearest station are the strongest drivers of adoption likelihood; environmental attitude is a smaller but meaningful positive factor.

## Assumptions & limitations

- Workplace and station coordinates are approximate, not survey-grade.
- Only two transit speed/wait tiers are used (Rail vs. Bus); real journeys involve more variation.
- Line transfers aren't modeled.
- The adoption score is a hand-weighted formula, not fitted to real behavioral data — a reasonable first pass, but real survey or pilot data would let the weights be calibrated properly.
- All employee data is entirely synthetic.

## Running the notebook

```bash
pip install numpy pandas matplotlib folium
jupyter notebook Deutschlandticket_Analysis.ipynb
```

Run all cells top to bottom. This regenerates the synthetic dataset, all charts, the summary output, the CSV, and the interactive map.

## Tech stack

Python, pandas, NumPy, Matplotlib, Folium (for the optional map).
