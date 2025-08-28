### 2.3 Identity Memory: Entity / Person Modeling

**Purpose**  
Holds structured, evolving representations of known entities (people, agents, selves, roles). Captures stable and dynamic personality facets, value orientations ("soul" signature), relationship histories, trust/safety/affinity metrics, and narrated relational context. Enables coherent social reasoning, prediction, alignment, and continuity of interpersonal narratives.

**Scope vs Other Stores**  
- Episodic: raw events; IdentityMemory: distilled, person-centric model.  
- Semantic: generalized social concepts; IdentityMemory: specific entity instances.  
- Visionary: prospective goals; IdentityMemory: enduring dispositions & relational evolution.  

**Core Data Model**

- EntityNode (Person / Agent)
  - id
  - canonical_name
  - aliases: [string]
  - categories: {human, system, subself, role, organization, avatar, internal_part}
  - status: {active, dormant, deprecated, merged}
  - provenance: {first_seen_episode_id, created_tick, created_cfg_nodes}
  - trait_vector: DenseVector[d_trait] (Big Five / HEXACO / custom dimensions) normalized [0,1]
  - temperament: {arousal_baseline, volatility, openness_bias, cooperation_bias}
  - value_orientation: {fairness, care, liberty, loyalty, authority, purity, curiosity, pragmatism} ∈ [0,1]
  - motivational_profile: {achievement, affiliation, power, exploration, security} ∈ [0,1]
  - affect_association: rolling EMA of affect valence/arousal when interacting
  - trust_metrics: {competence, integrity, benevolence, reliability, epistemic_calibration} ∈ [0,1]
  - safety_flags: {risk_level, manipulation_risk, boundary_violations_count}
  - reciprocity_score: scalar (symmetric mutual support index)
  - alignment_score: similarity to self identity facets (cosine of value + trait subspace)
  - soul_signature:
    - latent_vector: DenseVector[d_soul] (stable embedding distilled from narratives + consistent trait/value evidence)
    - archetype_tags: [mentor, challenger, nurturer, explorer, analyst, mediator, disruptor]
    - integrity_score: stability of latent across time windows
  - narrative_threads: [NarrativeThreadRef]
  - roles: {contexts → role_weight}
  - internal_models: optional predictive heads (next_action, reliability_hazard)
  - last_update_tick
  - decay_factor: for scheduling refresh

- RelationshipEdge (Entity i ↔ Entity j, directional stored both ways if asymmetric)
  - id
  - entity_src, entity_tgt
  - relation_category: {friend, collaborator, mentor, adversary, family, peer, self_part, neutral, unknown}
  - strength: scalar [0,1]
  - affinity: scalar (emotional closeness / warmth)
  - trust_delta_history: small ring buffer
  - interaction_count
  - last_interaction_tick
  - conflict_index
  - support_index
  - emotional_resonance_vector: DenseVector[d_affect_rel]
  - narrative_refs: [NarrativeEpisodeRef]
  - flags: {stale, volatile, escalating, stabilizing}

- NarrativeEpisode (Relational)
  - id
  - timestamp / tick
  - involved_entities: [ids]
  - summary_text
  - event_type: {conversation, assistance, conflict, promise, breach, joint_success, joint_failure}
  - affect_signature: {valence, arousal, discrete_labels}
  - evidence_deltas: {traits: {dim:Δ}, trust: {facet:Δ}, values: {facet:Δ}}
  - commitments: [{description, confidence, due_tick, fulfilled?}]
  - outcome_tags: {repair, reinforcement, deterioration}
  - provenance: CFG nodes, μ snapshot

- IdentityFacet (Optional explicit tracked facet)
  - facet_name
  - evidence_items: [episode_ids]
  - stability_score (low variance across last k windows)
  - drift_rate

**Indices / Auxiliary Structures**
- entity_index: name & alias → id
- archetype_index: archetype_tag → [entity_ids]
- relationship_adjacency: map entity_id → [RelationshipEdge]
- trust_priority_queue: entities needing reassessment
- stale_entities_heap: by (current_tick - last_interaction_tick)

**Feature / Vector Definitions**
- trait_vector dims (example 12): {agreeableness, conscientiousness, extraversion, openness, stability, empathy, assertiveness, patience, prudence, creativity, resilience, cooperativeness}
- d_trait: ~12–24  
- d_soul: 32 (autoencoder bottleneck over concatenated stable features)  
- d_affect_rel: 8 (relationship affect channels: warmth, tension, respect, dependence, inspiration, admiration, envy, uncertainty)

**Core Operations**

- CreateEntity(name, context_episode):
  1. Initialize trait_vector from priors (population baseline).  
  2. Seed value_orientation from early evidence or neutral 0.5.  
  3. Initialize trust facets mid (0.5) unless evidence suggests otherwise.  
  4. Soul signature latent = encoder(traits + values + motivational_profile).  

- IngestRelationalEpisode(episode):
  1. Parse participants, event_type, affect_signature.  
  2. Update or create RelationshipEdge(s).  
  3. Adjust trust_metrics, affinity, conflict_index via small learning rates.  
  4. Extract evidence_deltas to incrementally refine trait/value facets (Bayesian or EMA).  
  5. Log commitments / breaches.  
  6. Update soul_signature: latent ← latent + η * Encoder(evidence_deltas).  
  7. Recompute stability / drift.  

- UpdateRelationship(edge):
  - strength ← clamp(strength + α_strength * f(interaction_type, affect), 0,1)  
  - affinity, trust facets adjusted with bounded EMA  
  - flags set if volatility > threshold

- RetrieveEntities(query_spec):
  - Filter by archetype, relation_category, trust threshold, role context.  
  - Rank by composite relevance = w1*alignment + w2*trust + w3*affinity + w4*recentness.

- PredictBehavior(entity, context):
  - Use internal_models.next_action (lightweight classifier/regressor) conditioned on trait + value + recent relational episodes subset.

- DriftMonitoring:
  - drift_rate = ||latent_t - latent_{t-W}|| / W  
  - If drift_rate > drift_threshold and integrity_score dropping → escalate review.

- Merging / Dedup:
  - If alias collision or high embedding similarity (> τ) with low conflicting evidence: merge id_b into id_a, unify histories, recompute latent.  

- Decay / Refresh:
  - If (tick - last_interaction_tick) > staleness_window: gradually increase uncertainty in predictive heads and schedule targeted retrieval or questioning.  

- Safety / Boundary Checks:
  - Manipulation risk: rise if high competence + decreasing integrity + rising conflict_index + increasing volatility in trust deltas.  
  - Auto-throttle reliance weight in decision loops if risk_level escalates.  

**Data Flow Integration**

- DMN Loops:
  - Retrieval stages: supply socially relevant entity subsets.  
  - Valuation (VS): incorporate trust/alignment into action / plan scoring.  
  - Self-model updates: compare self identity facet drift vs external entity alignment shifts.  
  - Memory write: new episodes feed IdentityMemory, triggering incremental updates.  

- Reward / Learning Signals:
  - Stability reward: low unnecessary drift in well-evidenced facets.  
  - Calibration reward: predicted trust vs realized reliability error minimized.  
  - Social coherence: consistency of narratives referencing entity roles.  

**Minimal Parameter Update Scheme**
For each updated facet dimension d:  
new_mean_d = old_mean_d + λ_evidence * (evidence_estimate_d - old_mean_d) * confidence  
confidence updated via Bayesian precision or capped EMA.  

**Soul Signature Encoder:**
- Input: concat(trait_vector, value_orientation, motivational_profile, long_horizon averages of trust facets)  
- 2-layer MLP → d_soul; maintain EMA for stability metric.  

**Indices Maintenance (Per Tick)**
- Reheap stale_entities_heap after time advancement.  
- Update archetype_index if archetype_tags change.  
- Recompute alignment_score with current self value/trait vectors (cosine).  

**Audit & Logging**
Log entries:
- (tick, entity_id, facet_changed, Δvalue, evidence_refs)
- (tick, edge_id, trust_change, event_type, outcome_tags)
- Drift alerts and risk escalations

**Pruning & Archival**
- Archive dormant entities after prolonged inactivity & low relevance score.  
- Compress old NarrativeEpisodes into summarized statistical blocks (counts, aggregated affect, commitment fulfillment ratio).  

**Metrics Dashboard (Optional)**
- Top trust growth / decline entities  
- Drift outliers  
- Relationship volatility distribution  
- Alignment vs reliance discrepancy  

**Pseudocode (Sketch)**
```
function ingest_episode(ep):
  ensure entities exist
  for each ordered pair (i,j) in ep.involved_entities:
     edge = get_or_create_edge(i,j)
     update_relationship(edge, ep)
  update_entity_facets(ep)
  update_soul_signatures(changed_entities)

function update_entity_facets(ep):
  for each evidence_delta in ep.evidence_deltas:
     dim ← evidence_delta.dim
     mean, conf = facet_stats[dim]
     mean ← mean + λ * (evidence_delta.value - mean) * evidence_delta.confidence
     conf ← clamp(conf + γ*(evidence_delta.confidence - conf), conf_min, conf_max)

function compute_alignment(entity, self_model):
  return cosine( entity.value_orientation ⊕ entity.trait_vector, self_model.vector )
```

**Simplifications for Initial Implementation**
- Start with trait_vector of 8 dims and omit soul_signature until enough episodes.  
- RelationshipEdge metrics: keep only strength, trust, affinity first.  
- Use simple exponential decay for unused entities.  

**Future Extensions**
- Temporal facet trajectories modeling (RNN per entity).  
- Causal commitment satisfaction graphs.  
- Emotion contagion modeling along relationship network.  
- Cross-identity blending for subself integration tasks.  

