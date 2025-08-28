# Self Modeling Layer (SML)

Provides an explicit, continuously learned model of the system-as-agent: current internal state, persistent dispositions, roles/commitments, predictive dynamics, and calibration of its own uncertainty & drift. Supplies metacognitive signals (coherence, stability, misprediction, calibration loss) to DMN valuation, modulation, and safety subsystems.

High-Level Principles  
1. Predictive: Always predicts its next internal state before observing it.  
2. Attributional: Knows which recent experiences most influenced current self-state.  
3. Metacognitive: Quantifies uncertainty, drift, coherence, calibration.  
4. Counterfactual: Simulates alternative internal futures under different candidate thoughts/actions.  
5. Integrative: Feeds summary + diagnostics into DMN loop phases (generation, valuation, selection, consolidation, safety).  

Core State Structures
---------------------
SelfState_t (vector / structured record)
- control_context: {active_goals_ids, active_roles, focus_topic_embedding}
- capability_estimates: {reasoning_depth, retrieval_quality, planning_span}
- cognitive_load: scalar (normalized utilization estimate)
- modulation_snapshot: μ (neuromodulator), A (affect) summary (means & recent deltas)
- performance_metrics: {recent_reward_rate, coherence_score, memory_hit_rate}
- integrity_metrics: {narrative_integrity, identity_alignment, drift_rate}
- uncertainty: per facet (epistemic_u, aleatoric_u)
- commitments_active: [{id, due_tick, confidence}]
- energy / arousal proxies (if modeled)
- timestamp / tick

Derived Latents
- z_self_core: encoder(SelfState_t stable subfields) → d_core
- z_self_narr: running EMA over narrative embeddings (autobiographical thread)
- z_self_full = concat(z_self_core, z_self_narr)

Predictive Models
-----------------
1. Self Dynamics Predictor P_self:  
   Input: (SelfState_t, ActionCandidate or ThoughtSummary_t) → ŜelfState_{t+1}  
2. Sensory / Outcome Predictor (optional tie-in): predicts key external outcome proxies (reward, retrieval success).  
3. Counterfactual Simulator CF_self: batch-evaluates alternative ActionCandidates for projected SelfState_{t+H}.  
4. Drift Forecaster D_drift: predicts probability of undesirable drift (identity fragmentation / instability) in horizon H.

Attribution Mechanism
---------------------
Maintain sliding window W of (experience_id, influence_vector). When updating SelfState_t+1:
- Compute gradient or attention attribution from prior nodes (episodes, visionary, identity nodes) to changed facets.  
- Store top-k (experience_id, facet, weight).  
- Provide to Narrative layer for explanatory queries ("Why is focus low?").  

Key Metrics
-----------
- self_pred_error_t = ||mask(ŜelfState_{t}, observed SelfState_t)|| (facet-wise)
- calibration_loss = Brier / log-loss between predicted uncertainty and realized error events
- coherence_score: overlap consistency between current narrative thread set & active goal / identity constraints
- drift_rate = ||z_self_full_t − z_self_full_{t−Δ}|| / Δ
- counterfactual_advantage = (actual projected long-horizon value − best counterfactual) (used to refine selection policy)

Learning Signals
----------------
- Reward shaping addenda:  
  R_self = + α1 * coherence_gain − α2 * drift_rate − α3 * calibration_loss − α4 * unpredicted_large_error_flag.  
- Train P_self to minimize predictive MSE + uncertainty calibration loss.  
- Train D_drift (binary) on future drift events (label if drift_rate > τ while task context stable).  
- Train CF_self with distillation from multi-step rollouts or model-based approximations.  

Operational Cycle Integration
-----------------------------
Mapping onto DMN phases (see DMN loop numbering if applicable):

1. Perception / Input Gathering:
   - Extract current observable internal features → assemble raw SelfState_t.  
   - Before updates, P_self produces ŜelfState_{t+1|prior}. Store for later error computation.  

2. Candidate Thought Generation (HC expansion, reasoning):
   - Each candidate thought/action receives predicted ΔSelfState estimate via fast linear head over z_self_full.  
   - Filter out candidates that raise predicted drift probability > threshold (early safety gating).  

3. Valuation (VS / PFC-2):
   - Augment candidate score with self-alignment_score, predicted coherence_gain, predicted calibration_impact.  
   - Counterfactual set: sample k high-variance candidates; run CF_self to estimate long-horizon (H) value & self stability.  

4. Selection:
   - Include negative penalty if candidate reduces integrity or raises drift risk.  
   - Log selection rationale referencing top contributing self facets (for meta-explainability).  

5. Execution / Binding:
   - Apply chosen action; update memory + internal modulators.  
   - Observe new SelfState_t+1; compute prediction error & update P_self, calibration stats.  

6. Reward Tagging & Consolidation:
   - Store (SelfState_t, action, ŜelfState_{t+1}, SelfState_{t+1}, metrics) into self-transition buffer.  
   - Update attribution table linking episodes → self facet changes.  
   - Recompute z_self_core & z_self_narr.  

7. Mind-Wandering / Introspection Windows:
   - Schedule introspection tasks: audit highest uncertainty facets, recompute narrative_integrity, run drift forecasts.  
   - Possibly generate adjustment actions (rest, refocus, memory review) inserted as low-cost candidate thoughts next loop.  

8. Sleep / Offline Period:
   - Batch train P_self, D_drift, CF_self on accumulated buffers.  
   - Prune stale attribution entries; compress multi-episode influences into summarized factors.  

Data Structures
---------------
Buffers:
- SelfTransitionBuffer: (SelfState_t, action_repr, ŜelfState_{t+1}, SelfState_{t+1}, reward_slice, coherence_score, drift_rate)
- CounterfactualBuffer: (SelfState_t, candidate_set_repr, projected_values, chosen_value)
- AttributionStore: map facet → [(experience_id, weight, tick)] top-k

Parameter Sets:
- θ_pred (P_self), θ_cf (CF_self), θ_drift, θ_align (alignment scoring head), θ_unc (uncertainty head)

Minimal Initial Implementation
------------------------------
Phase 1 (Core):
- Define SelfState schema (subset: goals_active_count, μ summary, reward_rate, coherence_score, drift_rate_estimate).  
- Implement simple linear P_self + per-facet variance tracker.  
- Insert predicted self change features into candidate scoring (weight small).  

Phase 2 (Calibration & Drift):
- Add uncertainty estimation (predict log-variance).  
- Add drift forecaster logistic head; gate high-risk candidates.  

Phase 3 (Counterfactual):
- Sample candidate subsets; run 1–2 step simulated self transitions.  
- Use advantage vs actual to update scoring head.  

Phase 4 (Attribution & Narrative Integrity):
- Maintain top-k episode influences per key facet (focus, alignment, reward_rate).  
- Expose explanation API: explain(facet) → ranked supporting episodes.  

Safety & Integrity Hooks
------------------------
- Hard block if predicted drift probability > p_max.  
- Rate limit large Δ in identity_alignment across consecutive ticks.  
- Trigger diagnostic introspection if unpredicted_large_error (z-score > z_thresh).  

Interfaces
----------
DMN Consumption API:
- get_self_summary() → {alignment_score, coherence_score, drift_rate, uncertainty_vector}
- predict_candidate_impact(candidate_repr) → {Δcoherence_est, drift_risk, alignment_delta}
- explain_self_change(facet) → attribution list

Update API (called once per tick end):
- update_after_observation(observed_State)  
- log_candidate_evaluations(candidate_batch, impacts)  

Pseudocode (Tick Skeleton)
-------------------------
```
function dmn_tick(input):
  self_prior = assemble_self_state(observe_internal())
  self_pred = P_self(self_prior)
  candidates = generate_candidates(self_prior, input)
  enriched = []
  for c in candidates:
     impact = fast_impact_head(self_prior, c)
     if impact.drift_risk < threshold:
        enriched.append((c, impact))
  scores = value_candidates(enriched, self_prior)
  chosen = select(scores)
  apply(chosen)
  self_obs = assemble_self_state(observe_internal())
  log_transition(self_prior, chosen, self_pred, self_obs)
  update_prediction_models_if_batch_ready()
  run_introspection_if_scheduled()
```

Metrics Dashboard (Optional)
---------------------------
- Prediction error per facet (sparkline + rolling mean)
- Drift risk forecast vs realized drift events
- Calibration reliability diagram
- Attribution coverage (% facets with ≥1 supporting episodes)
- Counterfactual advantage utilization trend

Research Hypotheses Supported
-----------------------------
- Reduced prediction error + maintained low unintended drift correlates with elevated narrative coherence → surrogate of emergent self-awareness.  
- Counterfactual simulation improves alignment stability under perturbations.  
- Attribution transparency increases meta-control efficacy (faster recovery from instability).  

Future Extensions
-----------------
- Hierarchical SelfState (short-term vs long-term strata).  
- Probabilistic graphical model over facets for structured uncertainty propagation.  
- Active self-query policy (choose which facet to introspect to maximize expected calibration gain).  
- Social self projection (simulate how others model self; compare divergence).  

Minimal Dataset Requirements (for training P_self):
- ~10^4 self transitions for stable variance estimates; bootstrap with synthetic perturbation episodes.  

Versioning & Audit
------------------
- Version θ_pred, θ_drift; store checksum each sleep cycle.  
- Keep change log: (tick, model, metric_before, metric_after).  

This layer can now be referenced in other module specs; integrate via the DMN consumption & update APIs above.
