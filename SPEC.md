# SPEC: DRL Home Energy Management

> Single source of truth for *what* we are building. Change this file first, then the code.
> Current progress lives in [`notes/STATUS.md`](notes/STATUS.md).
> Items marked **(proposed)** are suggestions that are not yet confirmed by the owner.

## 1. Goal

Teach a deep reinforcement learning (DRL) agent to run a **simulated** smart home so that
**electricity costs are minimized**.

- Fun side project and learning experiment, **not** a real-world control system.
- Should be easy to understand for non-experts (visualizations, "explain it to grandma" mode).
- Deep math/theory is explicitly **not** a goal. Understanding what the code does is.

## 2. Principles

- **Staged approach:** start small, grow the simulation step by step.
- **Baseline first:** every stage gets a simple rule-based controller to compare against.
- **Time step:** the agent acts every **15 minutes** (96 steps per day).
- **Data:** typical/averaged public data from the internet, no real measurements.
- **Language:** everything in this repo (code, comments, docs, commits) is in **English**.
- **Beginner-friendly workflow:** small steps, copy-paste friendly code, Git via simple
  `add` / `commit` / `push`.

## 3. Components (full vision)

| Component | Role | Stage |
|---|---|---|
| Household load | Base consumption of the house | 1 |
| Photovoltaic (PV) | Generation | 1 |
| Battery storage | Store and release energy | 1 |
| Grid connection | Buy at spot price, sell at feed-in tariff | 1 |
| Electric vehicle (EV) | Flexible load, can also act as storage; has departure time and target charge | 2 |
| Heat pump | Heating demand, coupled to a thermal buffer | 3 |
| Heating rod | Surplus-energy heater (e.g. for hot water) | 3 |
| Random events | e.g. EV unplugged unexpectedly, cloudy day | 4 |

The agent should eventually control **all** controllable devices.

## 4. Stage 1 definition (PV + battery + grid)

### Observation (what the agent sees)
- Time of day (encoded as sin/cos) **(proposed)**
- Current PV generation (kW)
- Current household load (kW)
- Current electricity price (EUR/kWh)
- Battery state of charge (SoC, 0 to 1)

### Action (what the agent controls)
- One continuous value in [-1, 1]: battery power, scaled to max charge/discharge power.
  Negative = discharge, positive = charge. **(proposed)**

### Dynamics
- Battery respects capacity limits, power limits and (later) efficiency losses.
- Grid power = load - PV + battery charge power. Positive = import, negative = export.

### Reward
- `reward = -(cost of grid import) + (revenue from grid export)` per time step.
- Maximizing total reward = minimizing electricity cost.

### Episode
- One day = 96 steps. Training uses randomly sampled days. **(proposed)**

### Proposed default parameters (to be confirmed)
- Battery: 10 kWh, max 5 kW, efficiency 95 % each way
- PV: 6 kWp
- Feed-in tariff: fixed value below the average purchase price

## 5. Data (candidates, to be decided)

- Electricity price: public day-ahead / spot price data, plus fixed grid fees.
- PV generation: typical yearly profile for a given location and size.
- Household load: standard load profile of a typical household.
- Later: EV usage pattern, heating demand and outdoor temperature.

## 6. Tech stack

- Python, PyCharm
- Gymnasium (custom environment)
- Stable-Baselines3 (start with PPO, try SAC later)
- pandas, numpy, matplotlib (visualization)

## 7. Planned repo structure

```
drl-home-energy/
├── README.md
├── SPEC.md
├── requirements.txt
├── notes/
│   └── STATUS.md
└── src/
    ├── env/          # Gymnasium environment
    ├── baselines/    # rule-based controllers
    ├── train.py
    └── evaluate.py
```

## 8. Roadmap

1. **Stage 1:** load + PV + battery + grid, baseline vs. trained agent
2. **Stage 2:** add EV
3. **Stage 3:** add heat pump and heating rod
4. **Stage 4:** random events, dashboard, "grandma mode" visualizations

## 9. Decision log

| Date | Decision |
|---|---|
| 2026-10-16 | Agent acts every 15 minutes |
| 2026-10-16 | Use standard internet data at the start |
| 2026-10-16 | Agent should control all devices in the end, built up in stages |
| 2026-10-16 | Objective: minimal electricity costs |
| 2026-10-16 | Public GitHub repo, everything in English |
| 2026-10-16 | Repo description: "Teaching an AI agent to run a simulated smart home and cut its power bill. An experiment in reinforcement learning." |

## 10. Open questions

- Which concrete data sources for price, PV and load?
- Which battery/PV parameters should the "model house" have?
- Fixed or dynamic feed-in tariff?
- How should comfort (house temperature, EV ready on time) be rewarded or penalized in later stages?
