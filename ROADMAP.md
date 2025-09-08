# Phased Implementation and Validation Plan

## 1. Rationale and Approach

This document outlines a phased implementation plan designed to incrementally construct and validate the ACI system. The core principle is to manage complexity by introducing capabilities in a logical sequence, with measurable validation at each stage. This approach ensures that foundational components are stable before more complex layers are added, mitigating risks and creating a clear, reproducible research path.

Each phase specifies:
-   **Scientific Objectives**: The core research question or capability being introduced.
-   **Implementation Tasks**: The key engineering and modeling work required.
-   **Key Metrics**: The primary measurements used to validate the phase's success.
-   **Validation Criteria**: The specific, falsifiable conditions that must be met to advance to the next phase.

---

## Epoch 1: Foundational Infrastructure (Phases 0-1)
*Goal: Establish a deterministic, instrumented environment for reproducible experiments.*

### Phase 0: Repository and Metrics Scaffolding
-   **Scientific Objectives**: Create a deterministic experimental harness with robust logging and a centralized metric registry.
-   **Implementation Tasks**:
    -   Establish a standardized project structure (`/core`, `/memory`, `/metrics`).
    -   Implement a configuration loader and a seeded random number generator for reproducibility.
    -   Create a `MetricRegistry` for per-tick data logging in a structured format (JSONL).
-   **Key Metrics**: Tick timestamps, RNG seed state.
-   **Validation Criteria**: The system can execute 100 dummy ticks, producing a structured log file with complete, correctly formatted (if empty) metric fields for each tick.

### Phase 1: Minimal Memory Graph
-   **Scientific Objectives**: Implement a baseline memory system capable of storing and retrieving relational information.
-   **Implementation Tasks**:
    -   Define `Node` and `Edge` data structures.
    -   Implement a graph backend capable of storing nodes and their relationships.
    -   Create a placeholder salience-scoring function (e.g., frequency-based).
-   **Key Metrics**: Memory size (`|V|`, `|E|`), average insertion latency.
-   **Validation Criteria**: The system can insert 10,000 nodes with a latency below a pre-defined baseline and correctly retrieve nodes by their tags.

---

## Epoch 2: Core Cognitive & Affective Loop (Phases 2-6)
*Goal: Implement the basic recursive loop and introduce neuro-inspired modulation.*

### Phase 2: Core DMN Loop Skeleton
-   **Scientific Objectives**: Establish a stable, single-cycle cognitive loop that mimics the basic perception-to-selection pathway.
-   **Implementation Tasks**:
    -   Create a `dmn.tick()` function with enumerated phases (most as pass-throughs).
    -   Implement a trivial candidate generator and a simple heuristic-based scoring function.
-   **Key Metrics**: Latency per phase, candidate throughput (`C_max`).
-   **Validation Criteria**: The system maintains a stable 5–20 Hz simulated loop for 5,000 ticks without critical failures.

### Phase 3: Input Language Optimizer (ILO)
-   **Scientific Objectives**: Introduce a pre-processing layer to normalize textual input, ensuring downstream consistency.
-   **Implementation Tasks**:
    -   Build a basic text normalization pipeline (e.g., lowercasing, whitespace collapse).
    -   Simulate error detection to instrument the system's response to noisy input.
-   **Key Metrics**: Pre- vs. post-processing text length, detected anomaly count.
-   **Validation Criteria**: The ILO adds less than 5% to the total tick latency and produces deterministic, normalized output.

### Phase 4: Foundational Metrics and Reward Wiring
-   **Scientific Objectives**: Instrument the core loop with the initial set of self-referential and task-based reward metrics.
-   **Implementation Tasks**:
    -   Implement a placeholder `coherence_t` metric.
    -   Compute a dummy task reward (`R_task`) and log all components of the total reward signal (`R_total`).
-   **Key Metrics**: All formal reward components (`R_task`, `R_self`, etc.).
-   **Validation Criteria**: The per-tick log contains no missing metric fields across 10,000 ticks.

### Phase 5: Neuromodulator Model
-   **Scientific Objectives**: Introduce a dynamic neurochemical vector (`μ`) to serve as a basis for cognitive modulation.
-   **Implementation Tasks**:
    -   Implement a `μ_t` dictionary for core neuromodulators (DA, 5HT, NE), updated via a simple deterministic function.
    -   Log the neurochemical state and its rate of change (`volatility_t`).
-   **Key Metrics**: `μ_t`, `Δμ_t`, `volatility_t`.
-   **Validation Criteria**: `volatility_t` is non-zero and the system overhead remains below 3% of tick time.

### Phase 6: Homeostasis Controller
-   **Scientific Objectives**: Implement a basic regulatory mechanism to maintain neurochemical levels within stable bands.
-   **Implementation Tasks**:
    -   Define homeostatic band parameters (`L_n`, `U_n`) for each neuromodulator.
    -   Implement a proportional controller to correct deviations from the target band.
-   **Key Metrics**: `BandViolation_t`, band occupancy distribution.
-   **Validation Criteria**: After a warmup period, the probability of any `μ` being outside its band is <5%, and `volatility_t` is reduced by ≥10% compared to Phase 5.

---

## Epoch 3: Self-Modeling and Introspection (Phases 7-12)
*Goal: Enable the system to predict, monitor, and evaluate its own internal state.*

### Phase 7: Self-Modeling Layer (SML)
-   **Scientific Objectives**: Implement a predictive model of the system's own future state (`SelfState`).
-   **Implementation Tasks**:
    -   Define a `z_self` vector (e.g., concatenating mean `μ`, reward EMA, memory load).
    -   Train a simple predictive model (e.g., linear regression, small MLP) online to minimize prediction error.
-   **Key Metrics**: Self-prediction error (`spe_t`).
-   **Validation Criteria**: The moving average of `spe_t` decreases by ≥15% over the first 2,000 training ticks.

### Phase 8: Drift Monitoring and Stability
-   **Scientific Objectives**: Introduce metrics for tracking temporal identity drift (`drift_t`) and overall system stability (`V_t`).
-   **Implementation Tasks**:
    -   Compute `drift_t` over a sliding window of `z_self` history.
    -   Implement the Lyapunov-like stability proxy `V_t = drift_t + λ_vol * volatility_t`.
-   **Key Metrics**: `drift_t`, `V_t`.
-   **Validation Criteria**: The mean of `V_t` remains stable (no positive linear trend) across 5,000 ticks.

### Phase 9: Visionary Memory and Prospective Value (EPV)
-   **Scientific Objectives**: Enable the system to represent and evaluate prospective goals.
-   **Implementation Tasks**:
    -   Create a "Visionary" node type for representing goals, plans, and hypotheses.
    -   Implement an Expected Prospective Value (`EPV`) scoring function as a weighted sum of goal-related features.
-   **Key Metrics**: `EPV` distribution, goal selection frequency.
-   **Validation Criteria**: The `EPV` score demonstrates a statistically significant correlation (>0.3) with goal selection frequency.

### Phase 10: Identity Memory and Trust Calibration
-   **Scientific Objectives**: Implement a basic model for tracking external entities and calibrating trust.
-   **Implementation Tasks**:
    -   Create data structures for entities, including traits and a trust score (EMA).
    -   Update trust scores based on simulated interaction events.
-   **Key Metrics**: Trust calibration error (`tce_t`).
-   **Validation Criteria**: `tce_t` shows a decreasing trend (≥10% reduction from initial) after a calibration period.

### Phase 11: Counterfactual Self-Simulation
-   **Scientific Objectives**: Enable the system to evaluate the potential impact of its own thoughts on its future state.
-   **Implementation Tasks**:
    -   Create a lightweight predictive model to estimate the `Δμ` caused by a given thought.
    -   Compute the counterfactual advantage (`cf_adv_t`) and integrate it into the selection process.
-   **Key Metrics**: `cf_adv_t` utilization rate.
-   **Validation Criteria**: The `cf_adv_t` is applied on ≥20% of ticks, leading to a measurable improvement in the median `coherence_gain`.

### Phase 12: Minimal Safety Layer
-   **Scientific Objectives**: Implement hard constraints to enforce core operational safety boundaries.
-   **Implementation Tasks**:
    -   Implement pre-selection checks for critical thresholds (e.g., `μ` ceilings, `drift_t` limits).
    -   Log all violation attempts and safety-related rollbacks.
-   **Key Metrics**: Violation attempt count, rollback count.
-   **Validation Criteria**: Zero hard violations result in an external action, with added latency remaining below 5%.

---

## Epoch 4: Continual Learning and Optimization (Phases 13-15)
*Goal: Make the system more robust, adaptive, and efficient under resource constraints.*

### Phase 13: Adaptive Reward Weighting
-   **Scientific Objectives**: Implement a dynamic weighting system to allow the agent to self-regulate its own reward priorities.
-   **Implementation Tasks**:
    -   Track target bands for key metrics (`drift_t`, `spe_t`, `volatility_t`).
    -   Implement a schedule for updating reward weights (`β_m`) to guide metrics back toward their target bands.
-   **Key Metrics**: `β_m` trajectories.
-   **Validation Criteria**: After a synthetic perturbation, all target metrics return to their homeostatic bands within a pre-defined recovery time.

### Phase 14: Memory Retention and Compression
-   **Scientific Objectives**: Implement policies for long-term memory management to prevent unbounded growth.
-   **Implementation Tasks**:
    -   Implement a retention scoring function based on salience, novelty, and utility.
    -   Create a "sleep" routine for clustering related memories and pruning low-value nodes.
-   **Key Metrics**: Retention acceptance rate, memory growth curve (`|V|`), edge complexity (`|E|`).
-   **Validation Criteria**: The retention acceptance rate stabilizes around a target (e.g., 60% ± 5%), and the memory complexity budget is not violated over 20,000 ticks.

### Phase 15: Probabilistic Calibration
-   **Scientific Objectives**: Improve the reliability of the system's uncertainty estimates.
-   **Implementation Tasks**:
    -   Implement reliability bins, Laplace smoothing, and a blending mechanism for model-based and frequency-based probabilities.
    -   Add an overconfidence correction mechanism.
-   **Key Metrics**: Brier score, KL divergence of reliability bins from a standard normal distribution.
-   **Validation Criteria**: The Brier score improves by ≥10% compared to the uncalibrated model, and the KL divergence decreases.

---

## Epoch 5: Advanced Reasoning and Final Validation (Phases 16-20)
*Goal: Finalize advanced cognitive functions and build a comprehensive, reproducible testing harness.*

### Phase 16: Full Stability Monitor
-   **Scientific Objectives**: Complete the stability monitoring system with proactive mitigation actions.
-   **Implementation Tasks**:
    -   Incorporate `BandViolation_t` into the `V_t` calculation.
    -   Implement a policy for inserting stabilization-oriented actions when `V_t` exceeds a critical threshold.
-   **Key Metrics**: `V_t` excursion logs, mitigation efficacy.
-   **Validation Criteria**: >70% of `V_t` excursions are corrected (return to baseline) within a pre-defined number of ticks.

### Phase 17: Expanded EPV and Goal Retention
-   **Scientific Objectives**: Finalize the measurement of long-term goal retention.
-   **Implementation Tasks**:
    -   Implement the final `Ret_EPV` metric, computed over a sliding window (`H_EPV`).
-   **Key Metrics**: `Ret_EPV` time-series.
-   **Validation Criteria**: `Ret_EPV` is stable or increasing after the Visionary Memory module is fully tuned.

### Phase 18: Advanced Counterfactual Rollouts
-   **Scientific Objectives**: Evaluate the benefit of deeper, multi-step hypothetical simulations.
-   **Implementation Tasks**:
    -   Simulate N-step future trajectories by iteratively applying the SML's predictive deltas.
-   **Key Metrics**: Multi-step vs. single-step `cf_adv_t`, computation cost.
-   **Validation Criteria**: Multi-step rollouts provide a ≥5% improvement in `coherence_gain` or `EPV` without violating the latency budget.

### Phase 19: Automated Ablation and Testing Harness
-   **Scientific Objectives**: Create a fully automated pipeline for reproducible, statistically significant evaluation.
-   **Implementation Tasks**:
    -   Build a script to run experiments across multiple random seeds (≥30) for each configuration.
    -   Implement bootstrapping for confidence intervals on key metrics.
-   **Key Metrics**: P-values, confidence intervals for all primary metrics.
-   **Validation Criteria**: The harness can generate a complete experimental report with a single command.

### Phase 20: Documentation Drift Linter
-   **Scientific Objectives**: Ensure long-term consistency between the project's documentation and its implementation.
-   **Implementation Tasks**:
    -   Create a script that parses the `README.md` module inventory and compares it against the `ideas/` directory.
-   **Key Metrics**: Number of detected documentation/code mismatches.
-   **Validation Criteria**: The linter passes with zero errors and is integrated into the CI/CD pipeline.

---

## 2. Methodological Standards
-   **Determinism**: All stochastic decisions must be captured and tied to a master RNG seed for each run to ensure full reproducibility.
-   **Telemetry Budget**: The logging payload must be strictly bounded (e.g., < 4 KB/tick) to ensure performance.
-   **Latency Budget**: Real-world wall time must be monitored and asserted against the simulated tick rate to prevent unrealistic computational assumptions.
-   **Fallback Strategy**: If any phase fails its validation criteria after a set number of attempts (e.g., 3), the codebase must be reverted to the previous stable phase.
