# Implementation Phases Guide (Concise Build & Test Path)
Goal: Incrementally construct the ACI system with measurable checkpoints; avoid premature complexity. Each phase lists: Objectives, Build Tasks, Instrumentation, Exit Criteria (must pass before advancing).

Legend: M = metric already defined in README (coherence_t, drift_t, spe_t, volatility_t, etc.)

---
## Phase 0: Repo & Metrics Scaffold
Objectives: Deterministic env, logging, metric registry.
Build: 
- Basic Python project (or preferred language) structure `/core`, `/memory`, `/metrics`, `/loops`.
- Config loader (YAML) + seeded RNG wrapper.
- MetricRegistry: register/update/export JSONL per tick.
Instrumentation: Tick logger stub.
Exit: Can run 100 dummy ticks producing structured log with timestamps, seed, empty metric fields.

## Phase 1: Minimal Memory Graph
Objectives: Store & retrieve nodes/edges.
Build:
- Node class (id, embedding placeholder vec, tags, timestamp).
- Edge store (adjacency list; relation type).
- Salience scoring stub (random or frequency-based).
Instrumentation: Memory size, insertion latency.
Exit: Insert 10k nodes < X ms avg (set baseline), retrieval by tag returns expected set.

## Phase 2: Core DMN Loop (Skeleton)
Objectives: Single-cycle perception→candidate→selection→log.
Build:
- `dmn.tick()` with phases enumerated but most as pass.
- Candidate generator: trivial echo + one transformed variant.
- Scoring: heuristic (length penalty + random tie-break).
Instrumentation: latency per phase, C_max enforcement check.
Exit: Stable 5–20 Hz simulated loop for 5k ticks; no drift metrics yet.

## Phase 3: Input / Language Optimizer (ILO) Stub
Objectives: Normalize raw text.
Build:
- Basic pipeline: lowercase, whitespace collapse, sentence split.
- Error counter (typos simulated via random noise injection) + correction rate (dummy pass-through now).
Instrumentation: pre vs post length, detected anomaly count.
Exit: ILO adds <5% latency; produces deterministic normalized output.

## Phase 4: Metrics & Reward Wiring
Objectives: Compute baseline M subset.
Build:
- Implement coherence_t placeholder (supported = chain length, contradicted=0 for now).
- Compute Reward R_task (dummy constant) + log R_total components (zeros except R_task).
Instrumentation: Per-tick JSON with all reward fields present.
Exit: 0 missing fields across 10k ticks; serialization size bounded.

## Phase 5: Neuromodulator Scalars (Static Model)
Objectives: Introduce μ vector & projection shell.
Build:
- μ_t dict (DA, 5HT, NE) updated by simple deterministic oscillation or noise.
- ProjectionModulator stub returns pass-through scaling=1.
Instrumentation: Log μ, Δμ, volatility_t.
Exit: Volatility_t non-zero; band limits not enforced yet; overhead <3% tick time.

## Phase 6: Homeostasis Controller v1
Objectives: Regulate μ within bands.
Build:
- Add band params L_n/U_n.
- Simple proportional correction toward mid-band if outside.
- Compute Volatility_t, BandViolation_t.
Instrumentation: Plot band occupancy distribution.
Exit: After warmup, P(μ outside band) <5%; volatility_t reduced ≥10% vs Phase 5 baseline.

## Phase 7: Self Modeling Layer (SML) Minimal
Objectives: Predict next compact SelfState.
Build:
- Define SelfState vector z_self (concatenate mean(μ), recent reward EMA, memory load).
- Predictor: linear or small MLP trained online minimizing L2.
- spe_t metric implemented.
Instrumentation: spe_t moving average.
Exit: spe_t decreases ≥15% over first 2k training ticks; no latency budget breach.

## Phase 8: Drift Monitoring + Stability Proxy
Objectives: Track drift_t & provisional V_t.
Build:
- Maintain z_self_full history (reuse z_self for now).
- Compute drift_t over window Δ.
- Implement V_t = drift_t + λ_vol*volatility_t.
- Abort speculative expansions if predicted V increase (simple threshold rule).
Exit: Mean V_t stable (no positive linear trend) across 5k ticks; aborted expansions logged.

## Phase 9: Visionary Memory + EPV Skeleton
Objectives: Add goal/plan nodes & EPV scoring.
Build:
- Visionary node type with feature vector (utility, feasibility, risk, novelty placeholders).
- EPV = linear weighted sum (static weights a_k).
- Candidate scoring adds EPV contribution.
Instrumentation: EPV distribution, Ret_EPV placeholder (store active goals set).
Exit: Ret_EPV computable; EPV influences selection frequency (correlation >0.3 between EPV and selection).

## Phase 10: Identity Memory Basic
Objectives: Track entities & trust.
Build:
- Entity struct (id, trait_vec stub, trust score EMA).
- Relationship update on observed interaction events (simulate events).
- tce_t = |predicted - realized| (realized = noisy ground truth).
Instrumentation: tce_t trend.
Exit: tce_t decreasing after initial calibration (≥10% vs start); no major latency spike.

## Phase 11: Counterfactual Self Simulation (CF_self)
Objectives: Evaluate alternative candidate impact on SelfState.
Build:
- Lightweight model: apply delta templates (e.g., candidate type -> expected Δμ).
- Compute cf_adv_t.
- Integrate selection: if advantage positive beyond threshold include.
Instrumentation: Utilization rate (% ticks with positive cf_adv applied).
Exit: cf_adv utilization ≥20% initial (target will rise later), improvement in coherence_gain median vs Phase 10.

## Phase 12: Safety Layer (Hard Constraints Minimal)
Objectives: Enforce μ ceilings & drift threshold.
Build:
- Implement hard checks pre-selection.
- SafetyPenalty_t logging.
Instrumentation: Violation attempts count, rollback count.
Exit: Hard violations leading to external action = 0; added latency <5%.

## Phase 13: Adaptive Reward Weighting
Objectives: Implement dynamic β_m adjustments.
Build:
- Track target bands for drift_t, spe_t, volatility_t.
- Update β per schedule (every T_weight).
- Clamp & log β evolution.
Instrumentation: β trajectories, correlation with metric improvements.
Exit: Metrics return to bands after perturbation (inject synthetic noise) within T_recover < defined threshold.

## Phase 14: Memory Retention & Compression
Objectives: Implement retention score & pruning.
Build:
- Retention scoring (z-scored features) & stochastic keep decision.
- Sleep routine: cluster (simple k-means) + prune low R_i.
Instrumentation: Acceptance rate, |V| growth curve, |E| vs κ|V|log|V|.
Exit: Acceptance ~60%±5; complexity budget constraint satisfied over 20k ticks.

## Phase 15: Calibration & Probability Blending
Objectives: Full p_pred(error) pipeline.
Build:
- Reliability bins, Laplace smoothing, blending weight w_freq.
- Overconfidence correction (k_overconf, variance shrinkage).
- Brier & KL reliability metrics.
Instrumentation: Reliability plot generation script.
Exit: Brier score improvement ≥10% vs uncalibrated output; KL divergence to N(0,1) decreased.

## Phase 16: Full Stability Monitor & Mitigation Actions
Objectives: Complete V_t with BandViolation component + action class gating.
Build:
- Track recent actions per class, estimate E[ΔV|a].
- Mitigation insertion policy (stabilization candidate).
Instrumentation: V_t excursions log, mitigation efficacy (ΔV post-mitigation <0 baseline).
Exit: >70% of excursions (V_t > V_high) corrected within L ticks.

## Phase 17: Expanded EPV & Goal Retention Metric
Objectives: Finalize Ret_EPV measurement.
Build:
- Maintain sliding window of goal active sets; compute Ret_EPV using H_EPV horizon.
- Add feature uncertainty (optional variance per feature).
Instrumentation: Ret_EPV time-series, feature uncertainty coverage.
Exit: Ret_EPV stable or increasing after Visionary tuning; variance well-calibrated (empirical coverage within ±5%).

## Phase 18: Advanced Counterfactual Rollouts
Objectives: Multi-step hypothetical evaluation.
Build:
- Simulate N-step future via applying predictive deltas iteratively.
- Distill results into policy adjustments.
Instrumentation: Multi-step cf_adv_t vs single-step; computation cost.
Exit: Multi-step adds ≥5% improvement in coherence_gain or EPV without violating latency budget.

## Phase 19: Offline Ablation & Statistical Testing Harness
Objectives: Reproducible evaluation pipeline.
Build:
- Script to run scenario seeds (≥30) per configuration.
- Bootstrap CIs for key metrics.
- Automatic success/fail gating vs predefined thresholds.
Exit: One-click (single command) report generation with tables & plots.

## Phase 20: Documentation Drift Linter
Objectives: Keep specs consistent.
Build:
- Script: parse README module table + directory `ideas/`; report missing/mismatched modules.
Exit: Linter passes (0 errors) and integrated into CI.

---
# Cross-Cutting Practices
- Determinism: Capture RNG seeds per run & per tick where stochastic decisions occur.
- Telemetry Budget: Keep logging payload < X KB/tick (define X early; e.g., 4 KB).
- Latency Budget: Track real (wall) vs simulated tick; assert real ≤ 2× simulated target median.
- Fallback Strategy: If a phase fails Exit criteria after max_attempts (e.g., 3), revert to previous stable tag.

# Recommended Order Recap
0 Scaffold → 1 Memory → 2 DMN skeleton → 3 ILO → 4 Metrics/Reward → 5 μ Scalars → 6 Homeostasis → 7 SML → 8 Drift/V → 9 Visionary/EPV → 10 Identity → 11 Counterfactual → 12 Safety → 13 Adaptive Weights → 14 Retention/Compression → 15 Calibration → 16 Stability Monitor Full → 17 EPV Retention Final → 18 Multi-step Counterfactual → 19 Ablation Harness → 20 Linter.

# Minimal Test Suite (Add incrementally)
- Unit: memory insert/retrieve, retention decision distribution, SML prediction error.
- Property: invariants (μ within [0,1], |E| budget, non-negative metrics).
- Regression: stored baseline metrics vs new run deltas.
- Load: sustained 50k tick run memory growth & latency drift.

# Advancement Checklist Template
Before advancing: All Exit Criteria green, latency within budget, no increase in hard violations, metrics improvement or neutrality relative to prior phase.

(End of guide)
