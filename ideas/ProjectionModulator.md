Simplified Neurotransmitter Projection Modulator (Direct Wiring Variant)
=====================================================================

Goal
----
Provide a minimal, implementable mechanism to route neurotransmitter (NT) amounts from emitting brain areas to receiving areas without latent link / destination embeddings. Each projection (i→j) and NT type n has a tiny trainable 1‑layer modulator that scales release. A Homeostasis Module (HM) trains these modulators to keep NT levels within adaptive target bands while supporting coherence / stability.

Key Differences vs Original Design
----------------------------------
- No 128‑d link channels or destination subfield vectors.
- No matching step (compatibility matrices removed).
- Each anatomical / functional edge is explicit: a fixed adjacency list.
- Per edge & NT: a single scalar base gain plus a 1‑layer (affine + nonlinearity) modulator.
- Homeostasis Module supplies training signals (reward + band errors) to adjust modulators.
- Strongly reduced compute: O(E * |NT|) per tick.

1) Core Objects
---------------
BrainArea i:
- μ_i: vector of current neuromodulator levels (|NT| = 7 default: DA, 5HT, NE, OXT, TST, HA, ORX)
- reservoirs_i (optional): emission pools per NT (else use μ_i directly)
- targets_i: per NT (lower_band_i^n, upper_band_i^n)
- ctx_i (minimal): optional small feature vector (e.g., recent volatility, safety flag, coherence contribution)

Edge / Projection (i → j):
For each NT type n:
- base_gain_ij^n: scalar (initialized ~ 1.0)
- Modulator f_ij^n: 1‑layer NN producing scaling s_ij^n ∈ [0, s_max]
  s_ij^n = σ( w_ij^n · concat(μ_i^n, μ_j^n, band_err_i^n, band_err_j^n, global_metrics) + b_ij^n ) * s_max

Global:
- Homeostasis Module (HM): adjusts (w, b, base_gain) to minimize band errors, volatility, instability penalties.
- Safety limits: hard ceilings and max Δ per tick per NT per area.

2) Transfer Computation (Per Tick)
----------------------------------
For each edge (i→j) and NT n:
1. Source availability: avail_i^n = clamp(reservoirs_i^n or μ_i^n, 0, cap_out_i^n)
2. Modulator scale: s_ij^n = f_ij^n(...)
3. Raw release: release_raw_ij^n = base_gain_ij^n * s_ij^n * avail_i^n
4. Edge cap: release_capped_ij^n = min(release_raw_ij^n, cap_edge_ij^n)
5. Deduct from source: reservoirs_i^n -= release_capped_ij^n (or μ_i^n if using direct)
6. Add to receiver influx: influx_j^n += release_capped_ij^n

Reception & Update:
- Effective uptake: bound_j^n = min(influx_j^n, cap_recv_j^n)
- μ_j^n ← clamp( μ_j^n + k_bind_j^n * bound_j^n, 0, hard_ceiling_j^n )
- Spillover (if any): influx_j^n - bound_j^n can be logged / decayed.

3) Band Error & Signals
-----------------------
For each area i and NT n after updates:
- band_err_i^n = 0 if lower_band_i^n ≤ μ_i^n ≤ upper_band_i^n else signed distance to nearest boundary.
- volatility_i^n = |μ_i^n - μ_i^n(prev)|
Aggregate metrics each tick:
- stability_penalty = Σ_i Σ_n volatility_i^n
- band_penalty = Σ_i Σ_n |band_err_i^n|
- safety_penalty (boolean events → large constant)
- coherence_gain (input from DMN loop if available)

4) Homeostasis Module (HM)
--------------------------
Inputs (per edge & NT during learning step):
- (μ_i^n, μ_j^n, band_err_i^n, band_err_j^n, stability_penalty_local, global_metrics)
Outputs / Parameters:
- Adjust w_ij^n, b_ij^n (gradient step)
- Optionally adjust base_gain_ij^n (slow timescale)
Objective:
Maximize reward R_t:
R_t = α1 * coherence_gain - α2 * band_penalty - α3 * stability_penalty - α4 * safety_penalty
Add regularizers:
- L_smooth: (s_ij^n - s_ij^n(prev))^2
- L_sparsity: encourage lower average s_ij^n (optional L1 on s)
- L_energy: Σ release_capped_ij^n (penalize excessive total movement)
Total loss per batch: L = -R_t + β1 L_smooth + β2 L_sparsity + β3 L_energy

5) Training Procedure
----------------------
Online (each tick or mini-batch of ticks):
- Collect tuples: (edge, n, context_features, s_ij^n, band_errs, R_t components)
- Compute R_t
- Backprop through s_ij^n to w_ij^n, b_ij^n (detach μ dynamics if simplifying) using loss L
Periodic (sleep / consolidation):
- Refit base_gain_ij^n via simple regression to average beneficial release fraction
- Prune edges where mean s_ij^n < ε and contribution negligible
- Re‑center target bands if chronically saturated / underfilled

6) Pseudocode (Simplified)
--------------------------
Initialize all base_gain_ij^n = 1.0, w_ij^n ~ N(0, 0.1), b_ij^n = 0

function tick_projection():
  for each area: compute band_err_i^n
  R_components reset accumulators
  for each edge (i,j):
    for each NT n:
      avail = clamp(source(i,n), 0, cap_out_i^n)
      x = [μ_i^n, μ_j^n, band_err_i^n, band_err_j^n, global_metrics]
      s = sigmoid(dot(w_ij^n, x) + b_ij^n) * s_max
      raw = base_gain_ij^n * s * avail
      sent = min(raw, cap_edge_ij^n)
      source(i,n) -= sent
      influx(j,n) += sent
  for each area j, NT n:
      bound = min(influx(j,n), cap_recv_j^n)
      μ_j^n = clamp( μ_j^n + k_bind_j^n * bound, 0, hard_ceiling_j^n )
  compute updated band_err, volatility, coherence_gain
  R_t = α1*coherence_gain - α2*band_penalty - α3*stability_penalty - α4*safety_penalty
  accumulate training batch
  if ready_to_update(): optimize_modulators(batch)

function optimize_modulators(batch):
  for each sample: forward s, compute loss L, backprop to w,b
  optional: slow update to base_gain (e.g., EMA toward (desired_release / avail))

7) Data Structures (Shapes)
---------------------------
- μ: [num_areas, |NT|]
- base_gain: [num_edges, |NT|] scalars
- w: [num_edges, |NT|, feature_dim]; b: [num_edges, |NT|]
- feature_dim (minimal) ≈ 5–10
- influx: [num_areas, |NT|] zeroed each tick

8) Safety & Audit
-----------------
- Hard ceilings per area & NT (unalterable)
- Max per-tick Δμ limit
- Log per transfer: {edge_id, n, s, sent, μ_i_before, μ_j_after}
- Flag if saturation (> upper_band + margin) persists > T ticks → auto reduce base_gain_ij^n

9) Initialization Heuristics
----------------------------
- Target bands: pick mid-range goals (e.g., 0.4–0.6 of normalized scale) per NT
- s_max: 1.0 (or 2.0 if allowing amplification)
- k_bind_j^n: small (0.1–0.3) to avoid sudden jumps
- Learning rates: modulators fast (e.g., 1e-3), base_gain slow (1e-4)

10) Extension Hooks (Optional)
------------------------------
- Add small global context vector (coherence score, uncertainty) to features
- Introduce per-area bias modulators (area-level scaling before per-edge)
- Add simple decay: μ_i^n ← μ_i^n * (1 - decay_rate_n)

11) Minimal Implementation Steps
--------------------------------
1. Define adjacency list with edges (i,j)
2. Allocate parameter tensors (base_gain, w, b)
3. Implement tick_projection() loop
4. Collect metrics, compute R_t
5. Backprop to modulators
6. Periodically adjust target bands & prune

This simplified design removes the complexity of latent matching while retaining adaptive, learnable control of neurotransmitter flow. It should be straightforward to implement incrementally and later expandable if richer routing becomes necessary.

(Original detailed architecture replaced by this simplified variant per request.)