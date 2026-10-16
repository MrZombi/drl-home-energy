# SPEC: DRL Home Energy Management

> This file is the single source of truth for the project scope, requirements, and design decisions.
> Update this file before implementing changes that affect the project design.
> Current progress is tracked in [`notes/STATUS.md`](notes/STATUS.md).
> Items marked **(proposed)** are suggestions that have not yet been confirmed.

## 1. Goal

Explore how intelligent energy management can reduce electricity costs in a simulated household.

The project will model a smart home with photovoltaic generation, battery storage, household electricity demand, and a grid connection. Additional components, such as an electric vehicle and heating systems, will be introduced in later stages.

The project is a learning experiment, not a real-world control system.

The main goals are:

- Understand how household energy flows and electricity prices interact.
- Investigate how energy storage and flexible consumption can reduce electricity costs.
- Answer what-if questions, for example whether a larger battery pays off.
- Compare different energy management strategies.
- Make the results understandable through visualizations and simple explanations.
- Learn about simulation, optimization, and deep reinforcement learning.

## 2. Principles

- **Staged development:** Start with a simple model and add complexity gradually.
- **Two separate tracks:** The rule-based baseline and scenario analysis (Track A) are developed and validated first. The DRL agent (Track B) follows afterwards. See Section 3.
- **One shared simulation core:** Both tracks use the same simulation, so comparisons are fair.
- **Understandable results:** Prefer transparent models and explanations over unnecessary complexity.
- **Comparison:** Use simple reference strategies to evaluate more advanced approaches.
- **Time resolution:** The simulation uses 15-minute timesteps, corresponding to 96 steps per day.
- **Data:** Start with representative public data rather than real household measurements.
- **Learning project:** Understanding the implementation and its behavior is more important than maximizing technical complexity.
- **Language:** All repository content, including code, comments, documentation, and commit messages, is written in English.
- **Beginner-friendly workflow:** Work in small steps and keep implementation and Git instructions easy to follow.

## 3. Two Tracks

### Track A: Baseline and Scenario Analysis (first)

A rule-based controller on the shared simulation. It serves as the reference point and as a what-if tool for different household configurations (see Section 6).

### Track B: DRL Agent (afterwards)

A deep reinforcement learning agent trained on **one fixed system**, namely the configuration that Track A identified as the most cost-effective. The question is whether and how well the agent manages this system compared with the baseline (see Section 7).

### Relationship

- Track A is completed and validated before Track B starts.
- The tracks are evaluated separately but share the simulation core, data, and cost calculation.
- If the results of Track A change the picture, the goals of Track B may be adjusted.

## 4. Components and Development Stages

| Component | Role | Planned stage |
|---|---|---|
| Household electricity demand | Represents the home's electricity consumption | 1 |
| Photovoltaic system | Generates electricity from solar energy | 1 |
| Home battery | Stores surplus energy and supplies energy when needed | 1 |
| Grid connection | Allows electricity imports and exports | 1 |
| Electric vehicle | Adds flexible electricity demand and charging requirements | 2 |
| Heat pump | Represents heating demand and electricity consumption | 3 |
| Heating rod | Uses electricity for additional heating, for example hot water | 3 |
| Variable conditions and random events | Represent uncertainty, such as changing weather or unexpected EV usage | 4 |

The exact capabilities of each component will be defined as the corresponding stage is developed. Each stage extends both tracks.

## 5. Stage 1: Basic Household Energy System

### 5.1 System Model

The initial model contains:

- Household electricity demand.
- Photovoltaic generation.
- A home battery.
- A grid connection.
- Time-dependent electricity prices (applied to the simulation results, see Section 6.4).

The simulation advances in 15-minute timesteps.

The model represents the relationship between electricity generation, household consumption, battery storage, and grid interaction.

### 5.2 Energy Management

The system should account for the following possibilities:

- Use photovoltaic electricity directly to meet household demand.
- Store surplus electricity in the battery.
- Export surplus electricity to the grid when exporting is more beneficial than storing it.
- Discharge the battery when stored energy is useful for meeting demand.
- Import electricity from the grid when local generation and available storage are insufficient or when importing is economically preferable.

The economic value of storing energy depends on factors such as:

- The current electricity purchase price.
- The revenue from exporting electricity.
- Expected future electricity prices.
- Expected household demand.
- Battery capacity, power limits, and efficiency losses.

The simulation makes these energy flows and their costs observable.

### 5.3 Cost Objective

The main objective is to reduce the household's net electricity costs.

The calculation should account for:

- Costs of electricity imported from the grid.
- Revenue from electricity exported to the grid.
- Battery losses and other relevant operating costs, where modeled.

The exact cost calculation and treatment of additional costs must be defined before implementation.

### 5.4 Initial Model Parameters

The following values are proposed starting points, not confirmed requirements. In Track A they are **inputs** that can be changed per scenario.

| Parameter | Proposed value |
|---|---|
| Simulation timestep | 15 minutes |
| Steps per day | 96 |
| Battery capacity | 10 kWh |
| Maximum battery charging/discharging power | 5 kW |
| Battery charging/discharging efficiency | 95% each way |
| Photovoltaic system size | 6 kWp |

These values may be adjusted after reviewing suitable data sources and model assumptions.

## 6. Track A: Baseline and Scenario Analysis

### 6.1 Purpose

- Provide the reference point for all later strategies.
- Make it possible to explore **what-if scenarios**, for example:
  - Does a larger battery pay off, for example to build reserves for winter?
  - Which combination of battery and PV capacity is the most cost-effective?
- Identify the system configuration that Track B will be trained on.

### 6.2 Inputs

- **House configuration:** battery capacity, battery power limit, efficiency, PV size, and later further components.
- **Time series:** household demand and PV generation for a full year (the full year makes seasonal effects visible, which is needed for the winter question).

### 6.3 Baseline Controller

A simple, transparent, rule-based controller that does **not depend on prices (proposed)**:

1. Cover household demand with PV generation first.
2. Charge the battery with surplus PV energy.
3. Export surplus energy when the battery is full or charging power is exhausted.
4. Discharge the battery when demand exceeds PV generation.
5. Import from the grid when the battery cannot cover the remaining demand.

### 6.4 Outputs and Cost Evaluation

- The simulation outputs **energy flows** per time step (import, export, battery charge and discharge, battery state of charge, PV use). These do not depend on prices.
- **Prices are applied after the run.** The user provides a purchase price and a feed-in tariff, either fixed or as a time series, and the costs are calculated from the stored energy flows.
- Changing the tariff therefore does not require a new simulation.

### 6.5 Scenario Comparison

- A scenario runner executes several house configurations on the same data.
- The results are compared by annual net electricity cost.
- With investment costs provided by the user (for example price per kWh of storage), the runner estimates the **payback time** of an extra investment.
- A sweep over two parameters (for example battery size and PV size) can be shown as a heatmap.

### 6.6 Known Limitations

- A price-independent baseline cannot exploit price fluctuations. It may therefore underestimate the value of a larger battery under dynamic tariffs.
- An optional **price-aware baseline** (for example "charge when the price is below a threshold") may be added later as a second reference.
- Results depend entirely on the assumed data, prices, and investment costs.

## 7. Track B: DRL Agent

### 7.1 Setup

- Starts after Track A has been implemented and validated.
- The agent is trained on one fixed system configuration, selected from the Track A results.
- It uses the same simulation core, data, and cost calculation as Track A.
- The simulation is wrapped as a Gymnasium environment and trained with Stable-Baselines3 (PPO first, SAC optional).

### 7.2 Proposed Definition (proposed)

**Observation:**

- Time of day (encoded as sin/cos)
- Current PV generation
- Current household demand
- Current electricity price
- Battery state of charge

**Action:** one continuous value in [-1, 1], scaled to the battery power limit. Negative values discharge, positive values charge.

**Reward:** negative net electricity cost per time step, so maximizing the total reward minimizes the total cost.

**Episodes:** one day (96 steps), with training days sampled randomly from the data. Whether longer episodes are needed for seasonal planning is open.

### 7.3 Evaluation

- Compare the agent with the baseline on the same house, the same data, and the same cost calculation.
- Evaluate on days that were not used for training.
- Unlike the baseline, the agent needs prices **during** the run, because reacting to prices is the main source of savings.
- If the agent does not beat the baseline, the goals and the setup may be adjusted.

## 8. Stage 2: Electric Vehicle

The electric vehicle will introduce additional flexibility and constraints into household energy management.

### 8.1 Charging Requirements

The model should eventually account for:

- EV battery capacity and state of charge.
- Charging power limits.
- Vehicle availability and departure times.
- A required state of charge at departure.
- Electricity prices and available household energy.
- The interaction between EV charging, household demand, battery storage, and grid imports.

### 8.2 Charging Strategy

A planned behavior is to schedule most EV charging intelligently within a state-of-charge range of approximately 20% to 80%.

Charging should take electricity prices, available energy, and the vehicle's expected departure into account.

The model should also allow charging beyond 80%, including charging the final 20% up to 100%, when required by the configured departure target or another explicit charging requirement.

The exact rules, charging limits, and priorities remain to be defined.

## 9. Stage 3: Heating Systems

Later development may introduce heating-related electricity consumption.

Planned components include:

- A heat pump.
- A simplified representation of thermal storage or a thermal buffer.
- A heating rod, for example for hot water.

The model should investigate how heating demand and thermal storage affect electricity costs and the use of locally generated electricity.

Comfort requirements, temperature limits, and thermal dynamics must be defined before this stage is implemented.

## 10. Stage 4: Uncertainty and Additional Features

Later experiments may introduce:

- Variable household demand.
- Changing photovoltaic generation and weather conditions.
- Unexpected changes in EV availability, for example the vehicle being unplugged.
- Additional scenarios and operating constraints.
- Visualizations that explain energy flows and decisions in a way that non-experts can follow.

The purpose is to investigate how different strategies perform when conditions vary.

## 11. Data Sources

The simulation should use representative public data wherever practical.

Potential data categories include:

- **Electricity prices:** Public day-ahead or spot-price data, supplemented by relevant grid charges and taxes where appropriate.
- **Photovoltaic generation:** Representative generation profiles for a defined system size and location.
- **Household demand:** A standard load profile or another suitable representative dataset.
- **Electric vehicle:** Charging characteristics, availability patterns, and departure requirements.
- **Heating:** Representative heating demand and outdoor temperature data.

Concrete data sources have not yet been selected.

The model should document the assumptions and limitations associated with each dataset.

## 12. Technical Approach

The project uses Python.

- **Python:** Main programming language.
- **Gymnasium:** Framework for the simulation environment (needed for Track B, the core is designed so Track A can use it too).
- **Stable-Baselines3:** Library for the DRL experiments in Track B.
- **NumPy and pandas:** Numerical calculations and data processing.
- **Matplotlib:** Visualization and analysis.
- **PyCharm:** Development environment.
- **Git and GitHub:** Version control and project hosting.

The rule-based baseline is established first. Further approaches, such as mathematical optimization as an additional reference, remain optional.

## 13. Planned Repository Structure

The structure below is partly **(proposed)** and may change during implementation.

```text
drl-home-energy/
├── README.md
├── SPEC.md
├── requirements.txt
├── notes/
│   └── STATUS.md
└── src/
    ├── env/           # shared simulation core (and later the Gymnasium wrapper)
    ├── baselines/     # rule-based controllers (Track A)
    ├── scenarios/     # scenario runner, cost evaluation, payback (Track A) (proposed)
    └── agent/         # training and evaluation of the DRL agent (Track B) (proposed)
```

## 14. Roadmap

### Track A: Baseline and Scenario Analysis

1. **A1:** Simulation core with data loading (demand, PV) for a full year
2. **A2:** Rule-based baseline that outputs energy flows
3. **A3:** Cost evaluation with user-provided prices
4. **A4:** Scenario runner and payback calculation
5. **A5:** Visualizations (energy flows, scenario comparison, heatmap)

### Track B: DRL Agent

1. **B1:** Gymnasium wrapper around the simulation core
2. **B2:** Training on the system selected in Track A
3. **B3:** Comparison with the baseline
4. **B4:** Extension to further components

### Later Stages

Stages 2 to 4 (EV, heating, uncertainty) extend both tracks step by step.

## 15. Decision Log

| Date | Decision |
|---|---|
| 2026-10-16 | The agent acts every 15 minutes |
| 2026-10-16 | Start with standard public data from the internet |
| 2026-10-16 | In the end, the system should control all devices, built up in stages |
| 2026-10-16 | Objective: minimal electricity costs |
| 2026-10-16 | Public GitHub repository, all repository content in English |
| 2026-10-16 | Repository description: "Teaching an AI agent to run a simulated smart home and cut its power bill. An experiment in reinforcement learning." |
| 2026-10-16 | Two separate tracks: baseline and scenario analysis first, DRL agent afterwards |
| 2026-10-16 | Track A takes capacities as inputs; prices are entered after the simulation run |
| 2026-10-16 | The DRL agent is trained on one fixed system, selected from the Track A results |
| 2026-10-16 | Goals may be adjusted if the results require it |

## 16. Open Questions

- Which concrete data sources for household demand, PV generation, and electricity prices?
- Which location and year should the data represent?
- Which battery and PV parameters define the model house?
- Fixed or time-dependent feed-in tariff?
- Which investment cost assumptions are used for the payback calculation (storage price per kWh, PV price per kWp, lifetime, battery degradation)?
- Should a price-aware baseline be added as a second reference?
- Does the agent need longer episodes to plan across seasons?
- How should comfort (house temperature, EV ready on time) be rewarded or penalized in later stages?
