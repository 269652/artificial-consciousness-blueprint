# Metrics and Formalization

## Metrics Appendix
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
