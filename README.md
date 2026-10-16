# DRL Home Energy Management

Teaching an AI agent to run a simulated smart home and cut its power bill. An experiment in reinforcement learning.

A learning project exploring intelligent energy management in a simulated household. It does not control real household equipment.

## Overview

The goal of this project is to develop a simulated home energy system that optimizes electricity usage and costs.

The simulation will initially include a photovoltaic (PV) system, a home battery, household electricity demand, and grid interaction. Later stages may introduce additional components such as an electric vehicle (EV) and heating systems.

The project is intended for learning and experimentation. Visualizations are part of the plan, so that modern energy management is easy to understand, even for non-experts.

## Approach

The environment will be developed in stages, starting with 15-minute timesteps and a household equipped with a PV system, a home battery, and grid access.

The agent optimizes household energy management based on factors such as electricity demand, solar generation, electricity prices, and available storage capacity. Depending on the situation, it may be more beneficial to store surplus energy, use it directly, or export it to the grid.

Later stages extend the system with additional components, including an electric vehicle and heating systems. This introduces further optimization problems, such as scheduling EV charging around electricity prices while respecting defined charging limits.

The objective is to **minimize electricity costs** while respecting the operational constraints of the simulated household.

A rule-based controller serves as a baseline for evaluating different energy management strategies. The choice of optimization methods and learning algorithms remains open.

## Technology Stack

- Python
- Gymnasium
- Stable-Baselines3
- NumPy
- pandas
- Matplotlib

The final selection of tools and methods may change as the project develops.

## Project Status

**Current phase:** Setup and planning

- [x] Initial project specification
- [x] Git repository and project structure
- [x] Python virtual environment
- [x] Initial dependencies installed and verified
- [ ] Simulation environment
- [ ] Energy management strategies
- [ ] Evaluation and visualization

The simulation and energy management components have not yet been implemented.

## Documentation

- [`SPEC.md`](SPEC.md): the project blueprint (goals, stages, environment definition, decisions)
- [`notes/STATUS.md`](notes/STATUS.md): current progress and planned work

## Scope and Limitations

The simulation uses simplified models and representative data rather than a complete physical model of a real household.

Results depend on assumptions about household demand, PV generation, electricity prices, storage efficiency, and grid tariffs.

The project is intended for educational purposes and experimentation. Results from the simulation should not be interpreted as proof that a particular strategy is suitable for a real household energy system.
