# Implementation Details

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

## Learning Schedule (Cadence)

| Component | Per Tick (Online) | Sleep / Offline (Batch) |
|-----------|-------------------|-------------------------|
| P_self (SML predictor) | Light SGD every N_pred | Full epochs, recalibrate σ |
| D_drift forecaster | Update on drift events | Re-train with balanced data |
| CF_self (counterfactual) | On top-k selections | Multi-step rollouts distill |
| Candidate Scoring Head | Advantage updates | Re-weight importance samples |
| Projection Modulators | Band error gradients | Smoothness & sparsity regs |
| Homeostasis Module | Actor step if Δmetrics | Critic fit & band re-center |
| Memory Graph Embeds | Incremental edge adds | Recompute / prune / compress |
| Visionary Value Models | On EPV deltas | Feasibility / risk refits |
| Identity Traits/Trust | EMA updates per episode | Drift audit & re-basing |
| Input Optimizer Adapter | Cache corrections | Fine-tune correction model |
| CFG Meta Logs | Append provenance | Aggregate analytics |
| Safety Policies | Threshold checks | Threshold auto-tuning |

Legend: N_pred: small fixed interval (e.g., every 2–3 ticks)
