# Project Status

> This file records the current project state and the next planned steps.
> The project specification is maintained in [`../SPEC.md`](../SPEC.md).
> Update this file at the end of each work session.

**Last updated:** 2026-10-16

## Project Goal

Build a beginner-friendly deep reinforcement learning (DRL) experiment in which an AI agent learns to minimize electricity costs in a simulated smart home.

This is a learning project, not a real-world control system.

The project is developed in two separate tracks that share one simulation core:

- **Track A (first):** rule-based baseline and scenario analysis, for example whether a larger battery pays off.
- **Track B (afterwards):** a DRL agent trained on the fixed system selected in Track A and compared with the baseline.

See Sections 3, 6, and 7 of [`../SPEC.md`](../SPEC.md).

## Current Phase

**Project setup and planning, about to start Track A (baseline and scenario analysis).**

The repository has been created, cloned locally, and opened in PyCharm. The Python environment and initial dependencies have been configured.

No project-specific Python code has been written yet.

## Completed

* [x] Defined the project goal and staged development approach.
* [x] Created the public GitHub repository.
* [x] Added `README.md`, `SPEC.md`, and `notes/STATUS.md`.
* [x] Organized the documentation under the intended repository structure.
* [x] Cloned the repository and opened it in PyCharm.
* [x] Created the initial source directories:

  * `src/env/`
  * `src/baselines/`
* [x] Configured a project-specific virtual environment using Python 3.13.5.
* [x] Created `requirements.txt`.
* [x] Installed the listed Python dependencies.
* [x] Verified that Gymnasium, Stable-Baselines3, pandas, and Matplotlib can be imported.
* [x] Committed and pushed the documentation organization change.
* [x] Decided on the two-track approach (baseline and scenarios first, DRL agent afterwards).
* [x] Extended `SPEC.md` with Track A, Track B, roadmap, decision log, and open questions.

## Current Repository Structure

```text
drl-home-energy/
├── README.md
├── SPEC.md
├── requirements.txt
├── notes/
│   └── STATUS.md
└── src/
    ├── env/
    └── baselines/
```

The source directories are currently empty. The Python environment `.venv/` is local to the development machine and should not be committed to Git.

Planned additions (see Section 13 of `SPEC.md`): `src/scenarios/` and `src/agent/`.

## Next Steps

1. Replace `SPEC.md` and `notes/STATUS.md` with the updated versions, then commit and push.
2. Resolve the open questions needed for Track A: data sources for demand, PV, and prices, plus the model house parameters.
3. Build step A1: the simulation core with data loading for a full year.
4. Build step A2: the rule-based baseline that outputs energy flows.
5. Continue with cost evaluation (A3), scenario runner (A4), and visualizations (A5).

Track B (DRL agent) starts only after Track A is implemented and validated.

These steps are a plan, not a requirement to start coding immediately.

## Open Questions

Refer to Section 16 of [`../SPEC.md`](../SPEC.md).

Important topics include:

* Electricity price, PV, and household load data sources.
* Location and year represented by the data.
* Photovoltaic system and battery parameters.
* Grid feed-in tariff assumptions.
* Investment cost assumptions for the payback calculation.

## Session Log

| Date       | Progress                                                                                               |
| ---------- | ------------------------------------------------------------------------------------------------------ |
| 2026-10-16 | Defined the project goal, staged scope, and initial specification.                                     |
| 2026-10-16 | Created the GitHub repository and organized the project documentation.                                 |
| 2026-10-16 | Cloned the repository in PyCharm and created the initial source directories.                           |
| 2026-10-16 | Configured Python 3.13.5, created a virtual environment, installed dependencies, and verified imports. |
| 2026-10-16 | Planned the two-track approach (baseline and scenario analysis first, DRL agent afterwards) and updated the specification. |
