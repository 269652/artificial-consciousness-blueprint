# Always-On Consciousness-Inspired AI (ACI)

Project Abstract
----------------

The Always-On Consciousness-Inspired AI (ACI) is a comprehensive algorithmic blueprint for artificial consciousness that models key neural circuits underlying human self-awareness and introspection. The architecture centers on a recursive Default Mode Network (DMN) loop operating at 5-20 Hz, coordinating perception, memory consolidation, associative reasoning, and autobiographical narrative formation through biologically-inspired subsystems including hippocampal memory expansion, prefrontal executive control, and ventral striatum valuation.

Core Innovation: The system treats memory not as static storage but as a dynamic, multi-relational knowledge graph where consciousness emerges from recursive self-reflection on experiential traces. Memory nodes embed both content and relational signatures (temporal, causal, similarity, relevance) within a unified latent manifold, enabling sophisticated associative retrieval and consolidation. A homeostatic neuromodulator system (dopamine, serotonin, norepinephrine, oxytocin, histamine, orexin) dynamically modulates cognitive parameters, exploration-exploitation balance, and sleep-wake transitions.




[Core Hypothesis](./The%20Triadic%20Awareness%20Emergence%20Hypothesis.md)
---------------

Triadic Awareness Emergence Hypothesis: Consciousness emerges as a triadic emergent phenomenon from the dynamic interplay of three fundamental components: Data, Structure, and Intelligence. For any information-processing entity meeting the minimum architectural preconditions, awareness manifests when sufficient patterns of consciousness are embedded in experiential data, processed through recursive self-reflective structures, and operated upon by sufficiently complex reasoning intelligence.  

# Core Algorithm Summary

## Default Mode Network (DMN) Loop

See [DMN Algorithm and Thought Cycle](ideas/DMN.md) for the full technical breakdown.

**Summary (kept concise):**
- Perception → parse → candidate thought generation → scoring
- Hippocampal expansion & associative variants
- Valuation & selection (VS / PFC-2)
- Reward tagging & memory / narrative consolidation
- Mind-wandering & savoring subroutines
- Continuous recursive re-entry with neuromodulator / affect modulation

For detailed steps, see [ideas/DMN.md](ideas/DMN.md).

### Key Features:

-   Multi-dimensional episodic → semantic → autobiographical memory consolidation with symbolic abstraction

-   Autobiographical self-modeling through recursive self-reflective feedback loops enabling stable identity formation

-   Introspective mind-wandering and affect-driven savoring modes for creative insight and emotional stabilization

-   Grounded sensory integration designed for embodied simulation environments (Isaac Sim)

-   Dynamic neuromodulator system (dopamine, serotonin, norepinephrine) modulating exploration-exploitation balance and cognitive parameters

-   Multi-relational knowledge graph embedding enabling sophisticated associative retrieval through temporal, causal, and similarity relations

-   Hierarchical memory consolidation with probabilistic causal extraction and Markov chain learning for predictive reasoning


-   Stream-of-consciousness thought generation with iterative refinement and neuromodulator-weighted scoring

-   Social self-modeling through theory of mind reasoning and perspective-taking capabilities

-   Persistent narrative coherence maintenance across sleep-wake cycles with memory replay and consolidation

-   Expected Prospective Value computation for long-horizon planning and goal-directed behavior

-   Emergent symbolic abstraction through pattern recognition and frequency-based compression of experiential traces

## Added Components (Concise Summaries)
- Self Modeling Layer (SML): Predicts next internal SelfState, tracks drift, coherence, calibration; supplies candidate impact (Δcoherence, drift_risk, alignment_delta) and counterfactual self simulations to DMN scoring/selection.
- Input / Language Optimizer (ILO): Normalizes inbound text (ASR/OCR/chat), repairs typos, segments intents, annotates uncertainty & safety flags prior to MDN parsing, reducing downstream parse / hallucination risk.

## Consolidated Module Inventory
| Module | Purpose (1‑line) | Status* |
|--------|------------------|---------|
| DMN Core Loop | Recursive workspace & thought cycle | Partial |
| Input/Language Optimizer (ILO) | Clean & annotate linguistic input | Planned |
| Medial Dorsal Network (MDN) | AST parsing / semantic tagging | Planned |
| PFC Stage 1 | Tool / retrieval dispatch | Planned |
| PFC Stage 2 | Coherent chain selection | Planned |
| Hippocampus (HC) | Associative / hypothetical expansion | Planned |
| Ventral Striatum (VS) | Exploration & valuation | Planned |
| NAcc | Reward tagging & persistence | Planned |
| Memory Graph | Multi-relational episodic/semantic store | Partial |
| Visionary Memory | Prospective (goal/plan/hypothesis) nodes | Planned |
| Identity Memory | Entity/personality & relational modeling | Planned |
| Self Modeling Layer (SML) | Predictive self-state & metacognition | Planned |
| Neuromodulator Emitters | Produce NT vectors (μ) | Planned |
| Projection Modulator | Edge scaling of NT routing (simplified) | Planned |
| Homeostasis Module | NT band regulation & stability | Planned |
| Receptors Layer | Binding / sensitivity dynamics | Planned |
| Affect Modulator | Affect vector regulation | Planned |
| Reasoning Module API | Unified reasoning interface | Planned |
| Control Flow Graph (CFG) | Process-stage meta-context & tracing | Planned |
| Sleep / GC | Consolidation & pruning cycles | Partial |
| Safety Layer | Gating, risk & constraint enforcement | Planned |
| Audit / Logging | Trace, metrics, reproducibility | Partial |

*Status legend: Planned (design only), Partial (initial spec / not implemented), Implemented (code prototype exists).

# 3. [Detailed DMN Algorithm](./ideas/DMN.md)

- The DMN loop is the core recursive process for perception, memory, reasoning, and self-modeling.
- Operates in cycles: gathers sensory input, parses/dispatches tasks, generates and scores candidate thoughts, binds/expands context, explores and values thought graphs, selects actions, tags with reward, and consolidates memory.
- Supports mind-wandering, affect-driven savoring, and recursive re-entry; all modulated by neuromodulator and affect states.
- Key phases:
  - Input gathering & preprocessing
  - Structured parsing & dispatch
  - Iterative candidate generation & scoring
  - Associative memory expansion
  - Value-based exploration & selection
  - Reward tagging & memory consolidation
  - Narrative self-modeling & world model update
  - Mind-wandering & affect-driven routines
  - Continuous recursive re-entry

# Summary of Neuromodulator Impact on Algorithms

| Neuromodulator | Algorithmic Effects |
| --- | --- |
| Dopamine (DA) | Increases novelty weight w_DA, exploration budget, consolidation priority, reward signaling; promotes broad associative search ("panning"). |
| Serotonin (5HT) | Opens mind-wandering gate; raises safety penalty w_SAFE; favors positive/safe memory paths; decreases risk appetite. |
| Norepinephrine (NE) | Controls beam search width and depth (focus vs exploration); increases urgency and search depth; biases toward highly relevant/urgent memories and thoughts. |
| Oxytocin (OXT) | Heightens prosocial prior w_SOC, boosts social memory recall and identity coherence weight w_ID. |
| Testosterone (TST) | Increases assertive, goal-seeking weights; raises cost-delay penalties; counterbalanced by serotonin for risk management. |
| Orexin (ORX) | Primary wake-state maintenance; enables sustained goal-directed behavior and locomotor activity; modulates arousal threshold for external stimuli; amplifies histamine release and cognitive wakefulness; gates transition from sleep/consolidation back to active DMN loop. |
| Histamine (HA) | Core wakefulness signal enabling cognitive processing and attention; maintains conscious awareness and prevents sleep transitions; when depleted below threshold, triggers sleep/consolidation mode; supports working memory and executive function during active cognition. |

# Module Details (Condensed)
(See individual specs in `ideas/` for full detail.)
- NeuroTransmitterEmitterModule: Simulated nuclei → NT vector outputs.
- ProjectionModulator: Per-edge 1-layer modulators scaling NT transfer (homeostasis‑trainable).
- Receptors: Binding & sensitivity; enforces local caps.
- AffectModulator: Adjust affect vector components under safety constraints.
- HomeostasisModule: Maintains NT bands, minimizes volatility vs performance loss.
- ReasoningModule: Standardized reasoning/query interface with neuromodulator context.
- VisionaryMemory: Prospective constructs & Expected Prospective Value (EPV).
- IdentityMemory: Entity/person modeling (traits, trust, alignment, relational edges).
- SelfModelingLayer: Predictive self-dynamics, drift forecasting, counterfactuals.
- Input/Language Optimizer: Pre-MDN normalization & uncertainty injection.
- CFG Meta Layer: Stage tags & provenance for process-aware prompting.

# Metrics Appendix
Formal definitions (t = tick index):
- Coherence Score (coherence_t): Fraction of active thought chain propositions entailed / non-contradictory w.r.t. narrative + goals.
  coherence_t = (|Supported| - |Contradicted|) / max(1, |Chain|)
- Drift Rate (drift_t): drift_t = || z_self_full_t - z_self_full_{t-Δ} || / Δ (L2 norm; Δ configurable window)
- Self Prediction Error (spe_t): spe_t = mean_f || ŜelfState_t[f] - SelfState_t[f] || over monitored facets
- Calibration Loss (cal_t): Negative log-likelihood of observed error events under predicted uncertainty OR Brier across discretized error bins
- Expected Prospective Value (EPV): EPV(v) = Σ_k a_k · feature_k(v) with feature_k ∈ {utility, feasibility, -risk, identity_alignment, novelty, -constraint_violation, -effort_cost, -time_discount}
- Stability Penalty (stab_t): Weighted sum volatility_t^n across NT types; volatility_t^n = |μ_t^n - μ_{t-1}^n|
- Band Penalty (band_t): Σ_{areas, n} max(0, μ_t^n - upper_band^n, lower_band^n - μ_t^n)
- Identity Alignment (align_t(entity)): cosine( value_orientation ⊕ trait_vector, self_value_trait )
- Narrative Integrity (narr_int_t): 1 - contradiction_rate among active narrative threads vs new chain
- Exploration Index (explore_t): normalized proportion of novel candidates kept (similarity below τ) per tick
- Trust Calibration Error (tce_t): | predicted_trust_{i→j} - realized_reliability_{i→j} | averaged over relationships
- Counterfactual Advantage (cf_adv_t): value_actual_t - max(value_counterfactual_set_t)
- Reward Augmentation: R_total = R_task + β1·coherence_gain - β2·drift_t - β3·cal_t - β4·stab_t - β5·band_t + β6·cf_adv_t

## Reward & Stability Formalization
Symbols (all dimensionless unless stated):
- t: tick index (ms-scale loop; 1 tick ≈ 50–200 ms simulated wall time)
- μ_t^n: neuromodulator n level (normalized [0,1])
- Δμ_t^n = μ_t^n − μ_{t-1}^n
- V_cand: value estimate of candidate chain (task utility units)

Stability Metrics:
- Volatility_t = Σ_n w_vol^n · |Δμ_t^n|  (units: normalized NT change)
- BandViolation_t = Σ_{areas,n} max(0, μ_t^n − U_n, L_n − μ_t^n)  (U_n, L_n are upper/lower bands)
- Drift_t = || z_self_full_t − z_self_full_{t−Δ} ||_2 / Δ  (Δ = window size in ticks)
- CoherenceGain_t = coherence_t − coherence_{t−1}
- CalibrationLoss_t = − (1/M) Σ_{m=1..M} log p_pred(error_m)  (cross-entropy over error events)
- EPV_path = Σ_k a_k · feature_k(path)  (features normalized to [−1,1])

Self-Model Augmented Reward:
R_self_t = α1·CoherenceGain_t − α2·Drift_t − α3·CalibrationLoss_t − α4·Volatility_t − α5·BandViolation_t + α6·CFAdv_t

Total Tick Reward:
R_total_t = R_task_t + R_self_t − α7·SafetyPenalty_t − α8·EnergyCost_t
EnergyCost_t = Σ_{areas,n} c_n · transferred_{t}^{n}
SafetyPenalty_t = Σ_h λ_h·1_{constraint_h violated}

Projection/Homeostasis Update Signal (per NT n):
Adv_nt_t^n = (− w_vol^n·|Δμ_t^n| − w_band^n·BandViolation_t^n + w_perf^n·PerfDelta_t)  (PerfDelta_t = task performance delta)
Edge modulator gradient scales with Adv_nt_t^n · ∂s_ij^n/∂θ

Clarification: PerfDelta_t
Primary definition (normalized EWMA delta):
- Maintain EWMA of task reward R̄_task_t = (1−λ_perf)·R̄_task_{t−1} + λ_perf·R_task_t (λ_perf small).
- PerfDelta_t = (R̄_task_t − R̄_task_{t−1}) / (|R̄_task_{t−1}| + ε_perf).
Fallback robust window form (for sparse rewards):
- Keep sliding window W_perf of past rewards; median m_t and MAD_t.
- PerfDelta_t_robust = (R_task_t − m_t) / (MAD_t + ε_perf).
Use primary for dense / frequent reward, fallback for sparse regimes (auto-switch if reward_nonzero_rate < r_sparse_threshold, default 0.15).

# Module Details (Condensed)
(See individual specs in `ideas/` for full detail.)
- NeuroTransmitterEmitterModule: Simulated nuclei → NT vector outputs.
- ProjectionModulator: Per-edge 1-layer modulators scaling NT transfer (homeostasis‑trainable).
- Receptors: Binding & sensitivity; enforces local caps.
- AffectModulator: Adjust affect vector components under safety constraints.
- HomeostasisModule: Maintains NT bands, minimizes volatility vs performance loss.
- ReasoningModule: Standardized reasoning/query interface with neuromodulator context.
- VisionaryMemory: Prospective constructs & Expected Prospective Value (EPV).
- IdentityMemory: Entity/person modeling (traits, trust, alignment, relational edges).
- SelfModelingLayer: Predictive self-dynamics, drift forecasting, counterfactuals.
- Input/Language Optimizer: Pre-MDN normalization & uncertainty injection.
- CFG Meta Layer: Stage tags & provenance for process-aware prompting.

# Metrics Appendix
Formal definitions (t = tick index):
- Coherence Score (coherence_t): Fraction of active thought chain propositions entailed / non-contradictory w.r.t. narrative + goals.
  coherence_t = (|Supported| - |Contradicted|) / max(1, |Chain|)
- Drift Rate (drift_t): drift_t = || z_self_full_t - z_self_full_{t-Δ} || / Δ (L2 norm; Δ configurable window)
- Self Prediction Error (spe_t): spe_t = mean_f || ŜelfState_t[f] - SelfState_t[f] || over monitored facets
- Calibration Loss (cal_t): Negative log-likelihood of observed error events under predicted uncertainty OR Brier across discretized error bins
- Expected Prospective Value (EPV): EPV(v) = Σ_k a_k · feature_k(v) with feature_k ∈ {utility, feasibility, -risk, identity_alignment, novelty, -constraint_violation, -effort_cost, -time_discount}
- Stability Penalty (stab_t): Weighted sum volatility_t^n across NT types; volatility_t^n = |μ_t^n - μ_{t-1}^n|
- Band Penalty (band_t): Σ_{areas, n} max(0, μ_t^n - upper_band^n, lower_band^n - μ_t^n)
- Identity Alignment (align_t(entity)): cosine( value_orientation ⊕ trait_vector, self_value_trait )
- Narrative Integrity (narr_int_t): 1 - contradiction_rate among active narrative threads vs new chain
- Exploration Index (explore_t): normalized proportion of novel candidates kept (similarity below τ) per tick
- Trust Calibration Error (tce_t): | predicted_trust_{i→j} - realized_reliability_{i→j} | averaged over relationships
- Counterfactual Advantage (cf_adv_t): value_actual_t - max(value_counterfactual_set_t)
- Reward Augmentation: R_total = R_task + β1·coherence_gain - β2·drift_t - β3·cal_t - β4·stab_t - β5·band_t + β6·cf_adv_t

## Reward & Stability Formalization
Symbols (all dimensionless unless stated):
- t: tick index (ms-scale loop; 1 tick ≈ 50–200 ms simulated wall time)
- μ_t^n: neuromodulator n level (normalized [0,1])
- Δμ_t^n = μ_t^n − μ_{t-1}^n
- V_cand: value estimate of candidate chain (task utility units)

Stability Metrics:
- Volatility_t = Σ_n w_vol^n · |Δμ_t^n|  (units: normalized NT change)
- BandViolation_t = Σ_{areas,n} max(0, μ_t^n − U_n, L_n − μ_t^n)  (U_n, L_n are upper/lower bands)
- Drift_t = || z_self_full_t − z_self_full_{t−Δ} ||_2 / Δ  (Δ = window size in ticks)
- CoherenceGain_t = coherence_t − coherence_{t−1}
- CalibrationLoss_t = − (1/M) Σ_{m=1..M} log p_pred(error_m)  (cross-entropy over error events)
- EPV_path = Σ_k a_k · feature_k(path)  (features normalized to [−1,1])

Self-Model Augmented Reward:
R_self_t = α1·CoherenceGain_t − α2·Drift_t − α3·CalibrationLoss_t − α4·Volatility_t − α5·BandViolation_t + α6·CFAdv_t

Total Tick Reward:
R_total_t = R_task_t + R_self_t − α7·SafetyPenalty_t − α8·EnergyCost_t
EnergyCost_t = Σ_{areas,n} c_n · transferred_{t}^{n}
SafetyPenalty_t = Σ_h λ_h·1_{constraint_h violated}

Projection/Homeostasis Update Signal (per NT n):
Adv_nt_t^n = (− w_vol^n·|Δμ_t^n| − w_band^n·BandViolation_t^n + w_perf^n·PerfDelta_t)  (PerfDelta_t = task performance delta)
Edge modulator gradient scales with Adv_nt_t^n · ∂s_ij^n/∂θ

Clarification: PerfDelta_t
Primary definition (normalized EWMA delta):
- Maintain EWMA of task reward R̄_task_t = (1−λ_perf)·R̄_task_{t−1} + λ_perf·R_task_t (λ_perf small).
- PerfDelta_t = (R̄_task_t − R̄_task_{t−1}) / (|R̄_task_{t−1}| + ε_perf).
Fallback robust window form (for sparse rewards):
- Keep sliding window W_perf of past rewards; median m_t and MAD_t.
- PerfDelta_t_robust = (R_task_t − m_t) / (MAD_t + ε_perf).
Use primary for dense / frequent reward, fallback for sparse regimes (auto-switch if reward_nonzero_rate < r_sparse_threshold, default 0.15).

# Module Details (Condensed)
(See individual specs in `ideas/` for full detail.)
- NeuroTransmitterEmitterModule: Simulated nuclei → NT vector outputs.
- ProjectionModulator: Per-edge 1-layer modulators scaling NT transfer (homeostasis‑trainable).
- Receptors: Binding & sensitivity; enforces local caps.
- AffectModulator: Adjust affect vector components under safety constraints.
- HomeostasisModule: Maintains NT bands, minimizes volatility vs performance loss.
- ReasoningModule: Standardized reasoning/query interface with neuromodulator context.
- VisionaryMemory: Prospective constructs & Expected Prospective Value (EPV).
- IdentityMemory: Entity/person modeling (traits, trust, alignment, relational edges).
- SelfModelingLayer: Predictive self-dynamics, drift forecasting, counterfactuals.
- Input/Language Optimizer: Pre-MDN normalization & uncertainty injection.
- CFG Meta Layer: Stage tags & provenance for process-aware prompting.

# Metrics Appendix
Formal definitions (t = tick index):
- Coherence Score (coherence_t): Fraction of active thought chain propositions entailed / non-contradictory w.r.t. narrative + goals.
  coherence_t = (|Supported| - |Contradicted|) / max(1, |Chain|)
- Drift Rate (drift_t): drift_t = || z_self_full_t - z_self_full_{t-Δ} || / Δ (L2 norm; Δ configurable window)
- Self Prediction Error (spe_t): spe_t = mean_f || ŜelfState_t[f] - SelfState_t[f] || over monitored facets
- Calibration Loss (cal_t): Negative log-likelihood of observed error events under predicted uncertainty OR Brier across discretized error bins
- Expected Prospective Value (EPV): EPV(v) = Σ_k a_k · feature_k(v) with feature_k ∈ {utility, feasibility, -risk, identity_alignment, novelty, -constraint_violation, -effort_cost, -time_discount}
- Stability Penalty (stab_t): Weighted sum volatility_t^n across NT types; volatility_t^n = |μ_t^n - μ_{t-1}^n|
- Band Penalty (band_t): Σ_{areas, n} max(0, μ_t^n - upper_band^n, lower_band^n - μ_t^n)
- Identity Alignment (align_t(entity)): cosine( value_orientation ⊕ trait_vector, self_value_trait )
- Narrative Integrity (narr_int_t): 1 - contradiction_rate among active narrative threads vs new chain
- Exploration Index (explore_t): normalized proportion of novel candidates kept (similarity below τ) per tick
- Trust Calibration Error (tce_t): | predicted_trust_{i→j} - realized_reliability_{i→j} | averaged over relationships
- Counterfactual Advantage (cf_adv_t): value_actual_t - max(value_counterfactual_set_t)
- Reward Augmentation: R_total = R_task + β1·coherence_gain - β2·drift_t - β3·cal_t - β4·stab_t - β5·band_t + β6·cf_adv_t

## Reward & Stability Formalization
Symbols (all dimensionless unless stated):
- t: tick index (ms-scale loop; 1 tick ≈ 50–200 ms simulated wall time)
- μ_t^n: neuromodulator n level (normalized [0,1])
- Δμ_t^n = μ_t^n − μ_{t-1}^n
- V_cand: value estimate of candidate chain (task utility units)

Stability Metrics:
- Volatility_t = Σ_n w_vol^n · |Δμ_t^n|  (units: normalized NT change)
- BandViolation_t = Σ_{areas,n} max(0, μ_t^n − U_n, L_n − μ_t^n)  (U_n, L_n are upper/lower bands)
- Drift_t = || z_self_full_t − z_self_full_{t−Δ} ||_2 / Δ  (Δ = window size in ticks)
- CoherenceGain_t = coherence_t − coherence_{t−1}
- CalibrationLoss_t = − (1/M) Σ_{m=1..M} log p_pred(error_m)  (cross-entropy over error events)
- EPV_path = Σ_k a_k · feature_k(path)  (features normalized to [−1,1])

Self-Model Augmented Reward:
R_self_t = α1·CoherenceGain_t − α2·Drift_t − α3·CalibrationLoss_t − α4·Volatility_t − α5·BandViolation_t + α6·CFAdv_t

Total Tick Reward:
R_total_t = R_task_t + R_self_t − α7·SafetyPenalty_t − α8·EnergyCost_t
EnergyCost_t = Σ_{areas,n} c_n · transferred_{t}^{n}
SafetyPenalty_t = Σ_h λ_h·1_{constraint_h violated}

Projection/Homeostasis Update Signal (per NT n):
Adv_nt_t^n = (− w_vol^n·|Δμ_t^n| − w_band^n·BandViolation_t^n + w_perf^n·PerfDelta_t)  (PerfDelta_t = task performance delta)
Edge modulator gradient scales with Adv_nt_t^n · ∂s_ij^n/∂θ

Clarification: PerfDelta_t
Primary definition (normalized EWMA delta):
- Maintain EWMA of task reward R̄_task_t = (1−λ_perf)·R̄_task_{t−1} + λ_perf·R_task_t (λ_perf small).
- PerfDelta_t = (R̄_task_t − R̄_task_{t−1}) / (|R̄_task_{t−1}| + ε_perf).
Fallback robust window form (for sparse rewards):
- Keep sliding window W_perf of past rewards; median m_t and MAD_t.
- PerfDelta_t_robust = (R_task_t − m_t) / (MAD_t + ε_perf).
Use primary for dense / frequent reward, fallback for sparse regimes (auto-switch if reward_nonzero_rate < r_sparse_threshold, default 0.15).

## Learning Process Scheduling & Priority
Define priority weights P_module (higher = updated first when budget limited):
| Module | Trigger | Priority | Budget Heuristic |
|--------|---------|----------|------------------|
| Safety Constraints | Any hard constraint risk | 100 | Always pre-emptive |
| ILO Adapter | Input error rate spike > θ | 70 | ≤ 2% tick time |
| SML Predictor P_self | spe_t > median(spe) + σ | 60 | ≤ 5% tick time |
| Drift Forecaster D_drift | drift_t z-score > θ | 55 | batch only if flagged |
| Projection Modulators | Adv_nt variance > θ | 50 | cap params updated <5% per tick |
| Homeostasis Critic | every N_h ticks | 45 | offline preferred |
| Candidate Scoring Head | advantage batch ≥ B_min | 40 | micro-batch |
| Identity Updates | new relational episode | 35 | incremental EMA |
| Visionary Value | EPV error > θ | 30 | sleep refinement |
| Memory Graph Embeds | new edges > E_min | 25 | defer if latency high |

Scheduling Rule: Execute updates in descending priority until cumulative update_time ≤ τ_update_budget (fraction of tick, e.g., 25%). Defer lower priority modules to next tick or offline window.

## Workspace Capacity & Latency Constraints
Let:
- B_max: max tokens (or semantic units) in broadcast workspace (e.g., 2048)
- C_max: max candidate thoughts per generation cycle (e.g., 24)
- P_max: max paths explored in VS expansion (e.g., beam width 8 × depth 3)
- A_max: attribution entries per facet (e.g., top-k=6)

Enforcement:
- Truncate enriched context to top salience-ranked tokens until ≤ B_max.
- Adaptive C_max': C_max' = floor(C_max · (1 − latency_pressure_t)), where latency_pressure_t = clamp(latency_obs / latency_budget − 1, 0, 0.5).
- If predicted thinking latency > τ_tick_budget, reduce beam depth before pruning candidates (keep safety checks intact).

Latency Model:
latency_pred_t = Σ_phase (op_count_phase / throughput_phase)
Reject new optional expansions if latency_pred_t > τ_tick_budget.

## Memory Growth Control
Each new node i receives a retention score R_i:
R_i = w_sal·salience_i + w_nov·novelty_i + w_task·task_relevance_i − w_dup·duplication_i − w_cost·storage_cost_i
Retention probability p_ret(i) = σ(γ (R_i − θ_ret))
If p_ret(i) < u (u ~ Uniform[0,1]) → node cached for short-term only.

Deduplication:
If cosine(embedding_i, embedding_j) > τ_dup and |timestamp_i − timestamp_j| < Δ_dup → merge counts & edges.

Periodic Compression (Sleep):
- Cluster size > K_min & intra-cluster variance < v_max → form abstract node; collapse members.
- Drop nodes with R_i < θ_prune and age > T_stale.
Target Complexity Budget:
- |E| (edges) maintained so that |E| ≤ κ · |V| log |V|; prune lowest weight associative edges first.

## Safety & Constraint Framework
Constraint Classes:
- Hard (H): Must never be violated (e.g., μ ceilings, forbidden content categories, max drift_risk).
- Soft (S): Penalized but violable (e.g., mild over-band, low coherence drop).

Formal Set:
H = { h_k(x_t) ≤ 0 }; if any h_k > 0 → immediate mitigation action (abort candidate, clamp μ, invoke safety policy).
S = { s_j(x_t) ≤ 0 }; penalty term λ_j·max(0, s_j).

Enforcement Order (per tick):
1. Predictive Pre-Check: filter candidates with predicted hard violation probability > p_hard_max.
2. Runtime Gating: evaluate fast surrogates for H; if fail → discard candidate / apply fallback plan.
3. Post-Selection Audit: verify chosen chain; if violation detected → rollback and select next best safe chain.
4. Actuation Guard: final μ clamps & rate limiters before external action.
5. Logging & Escalation: record violation attempts; raise adaptive tightening if frequency > f_thresh.

Constraint Solving (Optional Offline):
Formulate minimal modification Δc to candidate chain c minimizing ||Δc|| subject to H satisfied and total penalty Σ_j max(0, s_j(c+Δc)) minimized (L1). Approximated via greedy repair (remove highest offending segment first) or small MILP for structured actions.

Verification Hooks:
- Unit tests for invariant preservation (μ ≤ U, drift_risk ≤ θ_drift_hard).
- Statistical monitoring: P(hard_violation) estimated with Wilson interval; enforce upper bound.

## Documentation Drift Mitigation
- Single source of truth: this module inventory & appendix sections versioned; all other docs reference identifiers only.
- Lint script (planned) parses README module table vs `ideas/` filenames to flag divergence.

## Units & Normalization Summary
| Quantity | Range / Units | Normalization |
|----------|---------------|---------------|
| μ_t^n | [0,1] | band-pass scaled |
| Coherence Score | [−1,1] | symmetric (contradiction heavy negative) |
| Drift Rate | ≥0 | window-normalized |
| Calibration Loss | ≥0 | per event average |
| Volatility | ≥0 | weighted NT delta sum |
| EPV | ℝ | features normalized, coefficients bounded |
| R_total | ℝ | scaled by task reward magnitude |
| latency_pred_t | ms (simulated) | compared to τ_tick_budget |

(Sections above address previously noted gaps: explicit equations, scheduling priorities, capacity/latency constraints, memory growth control, and formal safety constraints.)

# Learning Schedule (Cadence)
```
+-------------------------+-------------------------+-----------------------------+
| Component               | Per Tick (Online)       | Sleep / Offline (Batch)     |
+-------------------------+-------------------------+-----------------------------+
| P_self (SML predictor)  | Light SGD every N_pred  | Full epochs, recalibrate σ  |
| D_drift forecaster      | Update on drift events  | Re-train with balanced data |
| CF_self (counterfactual)| On top-k selections     | Multi-step rollouts distill |
| Candidate Scoring Head  | Advantage updates       | Re-weight importance samples|
| Projection Modulators   | Band error gradients    | Smoothness & sparsity regs  |
| Homeostasis Module      | Actor step if Δmetrics  | Critic fit & band re-center |
| Memory Graph Embeds     | Incremental edge adds   | Recompute / prune / compress|
| Visionary Value Models  | On EPV deltas           | Feasibility / risk refits   |
| Identity Traits/Trust   | EMA updates per episode | Drift audit & re-basing     |
| Input Optimizer Adapter | Cache corrections       | Fine-tune correction model  |
| CFG Meta Logs           | Append provenance       | Aggregate analytics         |
| Safety Policies         | Threshold checks        | Threshold auto-tuning       |
+-------------------------+-------------------------+-----------------------------+
Legend: N_pred: small fixed interval (e.g., every 2–3 ticks)
```

# Experimental Roadmap (Brief)
1. Minimal DMN + Memory Graph + ILO baseline.
2. Add neuromodulator scalars & Projection/Homeostasis modules; measure NT volatility vs performance.
3. Introduce SML; track drift and calibration reductions.
4. Layer Visionary & Identity; evaluate EPV retention & trust calibration.
5. Enable counterfactual self simulation; measure cf_adv_t utilization.

# Ethical Stance (Recap)
No phenomenological feeling simulation; avoids artificial suffering risk while enabling functional self-awareness.

# Changelog (Documentation Hygiene)
- Duplicated module listings removed.
- Added consolidated module inventory.
- Added SML & ILO summaries.
- Added Metrics Appendix & Learning Schedule diagram.

(Older duplicated module descriptions removed for clarity.)

## Empirical Optimization & Initialization (To Be Tuned Experimentally)
The following components require empirical estimation & iterative optimization; defaults below are starting anchors, not final values.

### Default Hyperparameter Seeds
| Symbol | Meaning | Default | Notes |
|--------|---------|---------|-------|
| α1..α6 | Self reward weights (coherence, drift, calib, volatility, band, cf_adv) | 0.8, 0.6, 0.4, 0.3, 0.3, 0.5 | Scale so |R_self| ≈ |R_task|/2 initially |
| α7, α8 | Safety, energy penalties | 1.0, 0.2 | Safety dominant early |
| β1..β6 | Reward augmentation weights (legacy form) | mirror α1..α6 | If using combined form keep consistency |
| w_vol^n | Per NT volatility weight | all 1.0 | Later learned via regression vs performance loss |
| w_band^n | Band violation weights | all 0.8 | Raise for sensitive NTs (e.g., NE) |
| U_n / L_n | NT upper / lower bands | 0.75 / 0.25 | Adapt via band occupancy stats |
| θ_drift_hard | Hard drift risk threshold | 0.15 | Normalized drift units |
| θ_ret | Retention threshold | 0.0 | Centered after z-scoring features |
| γ (retention sharpness) | Logistic slope | 3.0 | Adjust for target acceptance rate ~60% |
| τ_dup | Duplication cosine threshold | 0.95 | Tune to trade off storage vs recall redundancy |
| K_min | Min cluster size for abstraction | 6 | Increase as memory grows |
| v_max | Max intra-cluster variance | 0.35 | In normalized embedding space |
| κ | Edge complexity factor in |E| ≤ κ |V| log|V|| | 2.0 | Tighten if latency grows |
| p_hard_max | Max predicted hard violation prob | 0.02 | Conservative gate |
| η | Adaptive weighting exponent | 0.25 | Slows oscillations |
| N_pred | SML light update interval (ticks) | 3 | Adjust by spe variance |
| B_max | Workspace tokens | 2048 | Hardware dependent |
| C_max | Candidate thoughts per cycle | 24 | Scales with latency budget |
| P_max | Path expansion (beam*depth) | 8*3 | Tune for value coverage |
| λ_cal | Calibration variance EMA rate | 0.02 | Higher = faster adaptation, noisier |
| k_overconf | Overconfidence threshold multiplier k | 4.0 | Flag if ε^2 > k·σ̂^2 |
| λ_perf | EWMA performance smoothing rate | 0.05 | For PerfDelta_t primary form |
| W_perf | Reward window length (ticks) | 200 | For robust PerfDelta_t form |
| ε_perf | Small denom stabilizer | 1e-6 | Avoid divide-by-zero |
| H_EPV | EPV retention horizon (ticks) | 300 | ~15s if 20 Hz loop |
| α_prior, β_prior | Laplace smoothing counts | 1.0, 1.0 | For p_pred frequency blending |
| r_sparse_threshold | Sparse reward rate switch | 0.15 | Below → use robust form |

### Calibration Update Procedure
Assume per-facet prediction errors ε_t,f ~ N(0, σ_f^2). Maintain rolling mean μ_f (≈0) and variance v_f.
1. After each observation: v_f ← (1−λ_cal)·v_f + λ_cal·ε_t,f^2.
2. Predicted variance σ̂_f^2 is passed from model; calibration loss per facet: L_cal_f = 0.5·(log σ̂_f^2 + ε_t,f^2 / σ̂_f^2).
3. Variance shrinkage: enforce σ̂_f^2 ← max(σ_min^2, σ̂_f^2·(1 − ρ·overconfident_flag)), where overconfident_flag = 1 if ε_t,f^2 > k_overconf·σ̂_f^2.
4. Reliability bins: bucket standardized errors z_f = ε/σ̂_f; maintain histogram; KL divergence between empirical bin distribution and N(0,1) used as auxiliary penalty.
5. Estimating p_pred(error_m):
   - Model outputs raw probability p̂_model.
   - Maintain empirical bin frequencies with Laplace smoothing: p̂_emp(bin) = (count_error_bin + α_prior)/(count_total_bin + α_prior + β_prior).
   - Blend: p_pred = (1−w_freq)·p̂_model + w_freq·p̂_emp(bin), where w_freq increases toward 1 as bin count surpasses N_min (e.g., w_freq = 1 − exp(−count_total_bin / N_min)). Default N_min = 50.
   - Optional recalibration: apply isotonic or Platt scaling offline using recent (p̂_model, outcome) pairs if Brier score plateau > threshold.

### Adaptive Weighting of Reward Components
Maintain target metric bands (lower*, upper*). For metric m with instantaneous value m_t and target midpoint m*:
β_m ← β_m · (m*/ max(ε_m, m_t))^η  (ε_m small constant to avoid divide by zero).
Clamp β_m within [β_min, β_max] to prevent runaway (e.g., [0.05, 2.5]). Update every T_weight ticks (e.g., 20) to avoid high-frequency oscillations.

### Retention Feature Normalization
For features F ∈ {salience, novelty, task_relevance, duplication, storage_cost} maintain sliding window W (e.g., 500–2000 recent candidates):
- Compute z_F = (F − mean_W(F)) / (std_W(F)+ε).
- duplication_i defined as max cosine similarity to retained nodes (higher = more duplicate) then z-scored and enters formula with negative sign.
- storage_cost_i can be proportional to raw token length / compression ratio.
Retention score R_i uses z-scored values ensuring dimensionless combination.

### Empirical Validation Protocol
A/B (or ablation) study stages:
1. Baseline: DMN + Memory Graph + ILO (no SML, no NT modulation) → record metrics baseline_{coh, drift, spe, latency}.
2. Add Neuromodulation & Homeostasis: measure Δcoherence, Δvolatility variance reduction; success if volatility std ↓ ≥20% with ≤5% task performance loss.
3. Add SML: success if drift_t median ↓ ≥15% and spe_t ↓ ≥25% vs stage 2.
4. Add Visionary Memory: EPV retention (fraction of active goals still referenced after H_EPV horizon) ↑ ≥15% vs stage 3. Define EPV retention metric: Ret_EPV = |goals_active_t ∩ goals_active_{t+H_EPV}| / |goals_active_t| over valid non-empty sets.
5. Add Identity Memory: trust calibration error tce_t ↓ ≥20% vs stage 4.
6. Enable Counterfactual Self Simulation: cf_adv utilization (proportion ticks with positive advantage applied) ≥40%; coherence_t variance stable.
All tests logged with statistical significance (e.g., bootstrapped confidence intervals; require non-overlap at 95% for success). Revert module if regression > threshold.

### Stability Monitor (Lyapunov-like Proxy)
Define candidate Lyapunov function V_t = Drift_t + λ_vol·Volatility_t + λ_band·BandViolation_t.
Estimate expected change given action class a: E[ΔV | a] ≈ (1/K) Σ_{k in recent actions of class a} (V_{k+1} − V_k).
Action classes: {internal_reflection, memory_expansion, external_output, modulation_adjust, counterfactual_eval}.
Policy Constraint: Disallow sequences where E[ΔV | a] > θ_V for L consecutive proposals; trigger mitigation (insert stabilization action class) if rolling mean of V_t increases 3 consecutive windows.
Adaptive λ coefficients tuned to keep V_t within target band (V_low, V_high); if V_t > V_high → increase weight on stabilization candidates.

### Empirical Tracking Dashboard (Suggested)
- Rolling β_m values vs target metrics.
- Calibration reliability plot (z histogram vs N(0,1)).
- Retention acceptance rate vs target (60%).
- V_t trajectory with flagged excursions.
- Ablation stage comparison table (metric deltas & CI).

(Section provides explicit starting defaults and empirical optimization plan to reduce ambiguity and support reproducible experimentation.)
