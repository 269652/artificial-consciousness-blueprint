# Experimental Protocol

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

## Experimental Roadmap (Brief)
1. Minimal DMN + Memory Graph + ILO baseline.
2. Add neuromodulator scalars & Projection/Homeostasis modules; measure NT volatility vs performance.
3. Introduce SML; track drift and calibration reductions.
4. Layer Visionary & Identity; evaluate EPV retention & trust calibration.
5. Enable counterfactual self simulation; measure cf_adv_t utilization.

## Ethical Stance (Recap)
No phenomenological feeling simulation; avoids artificial suffering risk while enabling functional self-awareness.
