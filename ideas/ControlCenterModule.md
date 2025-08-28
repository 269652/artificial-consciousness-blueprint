# Control Center Module

## Purpose

The Control Center Module acts as a central interface for:
- Debugging and monitoring protein/neurotransmitter production and release.
- Manually overriding production/release rates for any protein.
- Using a neural network (NN) to optimize production and release rates based on system state, feedback, or experimental objectives.
- Logging, visualization, and audit of all interventions and NN outputs.

## Core Features

- **Manual Control Panel**
  - Set or override production/release rates for each protein/emitter.
  - Pause/resume/step simulation ticks.
  - View current rates, recent history, and system state.

- **NN Optimization Interface**
  - Plug in a trainable NN that receives system state (e.g., μ, receptor states, DMN metrics) and outputs recommended production/release rates for each protein.
  - Switch between manual and NN-driven modes per protein/emitter.
  - Optionally blend manual and NN outputs (e.g., weighted sum or gating).

- **Logging & Visualization**
  - Log all changes to production/release rates, including source (manual/NN), time, and context.
  - Provide hooks for visualization dashboards (e.g., time series plots, heatmaps).
  - Export logs for offline analysis.

- **Integration**
  - Connects to NeuroTransmitterEmitterModule and ProjectionModulator.
  - Can be called each tick to update emitter rates before projection/routing.
  - Exposes a UI or CLI for interactive debugging and testing.

## Usage Scenarios

- Debugging: Manually set DA/5HT/NE production to test downstream effects.
- Training: Use NN to optimize rates for stability, coherence, or experimental reward.
- Safety: Instantly clamp or pause all production in case of runaway feedback.
- Audit: Review all interventions and NN decisions for reproducibility.

---

This module provides a safe, auditable, and flexible interface for both manual and automated control of neuromodulator/protein production and release in the ACI system.
