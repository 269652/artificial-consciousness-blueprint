# 3. Detailed DMN Algorithm and Thought Cycle

The Default Mode Network (DMN) loop is the central recursive process coordinating perception, memory, reasoning, and self-modeling in the ACI architecture. It operates in continuous cycles, integrating sensory input, parsing and dispatching tasks, generating and scoring candidate thoughts, binding and expanding context via hippocampal memory, exploring and valuing thought graphs, selecting coherent action chains, tagging with reward, and consolidating memory. The loop also supports mind-wandering, affect-driven savoring, and recursive re-entry, all modulated by neuromodulator and affect states.

**Key phases include:**
- Input gathering and preprocessing
- Structured parsing and dispatch
- Iterative candidate generation and scoring
- Associative memory expansion
- Value-based exploration and selection
- Reward tagging and memory consolidation
- Narrative self-modeling and world model update
- Mind-wandering and affect-driven routines
- Continuous recursive re-entry

For full technical details, see the canonical DMN algorithm documentation.

## 3.1. [Input Gathering and Preprocessing](steps/3.1.md)

- Sensory inputs (vision, audio, proprioception) are encoded into latent embeddings: zv, za (text, prosody), zp.
- Associative cortices bind cross-modal observations into concise descriptive thought snippets.
- Combine sensory embeddings and inner speech text into a composite input.
- SML Hook (Pre-Prediction): Assemble provisional SelfState_t (μ / affect summary, active goals, recent reward rate, prior coherence, drift estimate). Call P_self(SelfState_t) to produce ŜelfState_{t+1|prior} retained for later error + calibration metrics.

### 3.1.1 Input / Language Optimizer (ILO)
Purpose: Clean, normalize, segment, and uncertainty‑annotate inbound linguistic / symbolic streams (ASR transcripts, OCR text, user chat, internal captions) to reduce downstream cognitive load and parsing errors.

Core Functions:
1. Normalization: spelling correction, punctuation restoration, casing, number expansion ("twenty-one" ↔ 21).
2. Disfluency & Noise Removal: eliminate filler tokens ("uh", repeated fragments), mark uncertain spans.
3. Semantic Stabilization: unify synonyms to canonical forms when confidence high (configurable), preserve ambiguity with tags when low.
4. Segmentation: split into intent units / clauses with boundaries and confidence scores.
5. Typo & Corruption Repair: detect OOV / high perplexity tokens, propose replacements; retain original in metadata for audit.
6. Alignment & Fusion: merge multi-modal captions (e.g., visual OCR + speech) into a single timeline, resolve conflicts with confidence weighting.
7. Safety & Policy Pre-Filter: early removal / flagging of unsafe or irrelevant content before MDN parsing.
8. Uncertainty Propagation: attach per-token uncertainty u_t and span_confidence; pass to MDN so low-confidence regions can trigger clarification or reduced weight in reasoning.

Architecture:
- Lightweight heuristic pre-pass (regex, dictionary) → produces draft_clean.
- LLM Refinement Pass (Estimator): prompt includes raw_input, draft_clean, per-token ASR confidence, visual tags. Returns structured JSON:
  { normalized_text, segments:[{text, intent_hint, confidence, span_uncertainty, original_span_indices}], token_map, corrections:[...], unresolved:[...], safety_flags:[...] }.
- Optional fast mode: skip LLM if input length < L_min or latency budget tight; fallback to heuristics only.

Data Structures:
- RawInputPacket: { text_raw, modality_meta, token_conf, timestamp }
- OptimizedInputPacket: { text_norm, segments[], corrections[], uncertainty_stats, safety_flags, original_ref }
- Segment: { text, intent_hint, confidence, u_span, orig_idx_range }

Neuromodulator Modulation:
- NE (focus): higher NE → stricter pruning (remove low-confidence fragments).
- DA (novelty): higher DA → retain unconventional tokens (lower canonicalization aggressiveness) to preserve creative inputs.
- 5HT (safety): higher 5HT → strengthen safety filter thresholds.
- OXT (social): bias intent_hint labeling toward social / relational interpretations when ambiguous.

Interaction with SML:
- Provide input_uncertainty_score to SelfState_t (affects cognitive_load estimate).
- If input_uncertainty high, SML may elevate introspection / clarification candidates in subsequent generation.

Learning / Adaptation:
- Self-supervised loop: compare parser (MDN) failure rates & downstream coherence when using ILO vs not; reinforce correction strategies that reduce later repairs.
- Maintain CorrectionHistory: (raw_phrase, correction, acceptance_outcome) → train a small adapter model for local rapid corrections before LLM call.
- Calibration: track true_error_rate vs predicted uncertainty to refine u_span scaling.

Pseudocode:
```
function optimize_input(raw_packet):
  draft = heuristic_clean(raw_packet)
  if use_llm(draft):
     refined = llm_refine(draft, raw_packet.conf)
  else:
     refined = fallback_format(draft)
  refined = apply_neuromodulator_policies(refined, μ)
  return refined
```

Outputs to MDN Parsing:
- normalized_text (primary string)
- segments with intent_hints (plan?, question?, social?)
- token_map for traceability (audit)
- uncertainty vector per segment
- safety flags (may short-circuit or mask segments)

Failure Handling:
- If LLM refinement times out, proceed with draft & mark degraded_mode.
- If safety_flags critical, skip semantic expansion and raise safety event.

Metrics:
- Correction precision / recall (manual or delayed validation).
- Downstream parse_error_rate reduction.
- Coherence_gain_delta (difference in candidate coherence with vs without ILO).
- Latency budget adherence.

Integration Points:
- Positioned after 3.1 sensory fusion, before 3.2 MDN Parsing.
- MDN sees OptimizedInputPacket instead of raw composite text.
- Logging: store (raw, normalized, corrections, uncertainties) for reproducibility & ablation.

## 3.2. MDN Parsing

- Parse combined input into an Abstract Syntax Tree (AST), segmenting content into semantically tagged nodes:

  - Math, factual, social, recall, plan, explanation, self-reference.
- SML Hook: Tag parsed AST with self-context header (alignment_score, drift_risk_forecast) so downstream generation conditions on current stability.

## 3.3. PFC Stage 1 Dispatch

- For each AST node:
  - Math nodes: Regex extraction and execution of symbolic evaluation (SymPy) to generate definite results.
  - Factual/Recall nodes: Query memory graph with hybrid text and embedding search to synthesize answers.
  - Social/Explain nodes: Mini LLM chains generate empathetic or abductive explanatory content.
- Merge enriched nodes back into a comprehensive context pack, combining AST plus sensory and self-model information.
- Additionally, detect intent/plan content and create or extend VisionNodes so future-oriented constructs (visions, goals, hypotheses, plans) are captured in Visionary Memory with “not-yet-happened” semantics.
- SML Hook: Update quick capability_estimates (retrieval_quality, reasoning_depth proxy) from dispatch outcomes; write interim deltas into transient SelfState cache (not yet committed).

## 3.4. Iterative Thought Layer Generation & Scoring


1. Generate a diverse set of candidate thoughts c_i from the enriched context via an LLM with varied decoding styles: {literal, formal, terse, abductive, empathetic}.

2. Extract features per candidate:

- Coherence via entailment & self-assessment.
- Identity coherence estimated by cosine similarity with current self-model z_self.
- Task utility aligned with goals.
- Novelty (distance from recent thoughts).
- Epistemic gain (expected information gain/uncertainty reduction).
- Safety metrics (toxicity, hallucination flags, constitutional compliance).
- Calibration gap (discrepancy between likelihood and confidence).

3. Score candidates with neuromodulator-weighted linear combination (cleaned to a single expression):
   score(c) = w_DA·nov + w_EPI·epi + w_TASK·util + w_SOC·prosocial + w_ID·idcoh − w_SAFE·penalty

4. Refine context iteratively by augmenting it with the top candidate thought, repeat generation and scoring until these termination criteria are met:

- Top candidate remains stable for k cycles.
- Marginal improvement below threshold ε.
- Safety or computational budget exceeded.

5. Output the best thought chain (pre-HC expansion) — an ordered, scored sequence of internal thoughts.

- At this stage, also generate refinements of VisionNodes, alternative strategies, and plan decompositions; attach them to the evolving thought chain to keep prospective content co-evolving with deliberation.

SML Augmentations:
- For each candidate c_i obtain predict_candidate_impact(c_i) → {Δcoh_est, drift_risk, alignment_delta}.
- Early prune candidates with drift_risk > θ_drift or large negative alignment_delta.
- Extend scoring formula:
  score(c) = base_score(c) + w_coh·Δcoh_est + w_align·alignment_delta − w_drift·drift_risk
- Log (candidate_id, drift_risk, Δcoh_est) to SelfTransitionBuffer precursor for later learning.

## 3.5. DMN Binding and Hippocampal Expansion

- Bind sensory embeddings zv, zp, thought chain, self-model z_self, and small memory snippets in global workspace b_t.
- Use HC to expand b_t into an enriched thought graph containing associative and hypothetical variants plus partial replays.
- When querying memory, include Visionary Memory using prospective relation operators (e.g., T_goal, T_feasibility, T_risk, T_dependency, T_temporal_forecast, T_alignment_identity, T_value) to retrieve visionary neighbors and generate counterfactual variants aligned with current goals and constraints.
- SML Hook: Include z_self_full (z_self_core ⊕ z_self_narr) explicitly in workspace b_t; store snapshot for counterfactual simulation.

## 3.6. Ventral Striatum Exploration and Valuation

- Explore the HC-expanded graph using beam search or graph walks.
- For each candidate path, compute salience and value based on weighted features (novelty, emotional affect, relevance, uncertainty reduction) minus safety penalties (cleaned to a single expression):
  val(path) = Σ_k w_k(μ)·feature_k − safety_penalty
- For paths involving VisionNodes, compute Expected Prospective Value (EPV) and rank vision paths by expected utility, feasibility, risk, identity alignment, safety margin, effort cost, and time discount.
- SML Hook: For each path compute projected self impact by aggregating candidate ΔSelfState estimates; penalize cumulative drift_risk; boost paths increasing predicted narrative_integrity.

## 3.7. PFC Stage 2 Selection

- Filter paths for coherence and safety.
- Collapse the candidate graph to a single coherent chosen chain with attached confidence.
- Choose actions among internal (self-query, simulate) or external (speech, behavior) modes.
- Update VisionNode statuses (e.g., draft→candidate→active), select plan steps to stage, and schedule reviews while enforcing safety and coherence constraints.
- SML Hook: Counterfactual batch: run CF_self over top-k finalist chains to refine drift_risk and long-horizon coherence_gain; adjust final selection using counterfactual_advantage.

## 3.8. Nucleus Accumbens Reward Tagging and Persistence


- Apply reinforcement tags based on neuromodulator states.
- Update memory nodes with persistence decisions.
- Trigger symbolic abstraction if repetition thresholds are exceeded.
- Persistence tagging also biases recall/scheduling of active and high-EPV VisionNodes to ensure prospective content remains salient.
- SML Hook: Compute R_self components (coherence_gain, drift_penalty, calibration_delta); append to reward tag so PHM / modulators receive integrated cognitive + self-model signal.

## 3.9. Memory Write & Autobiographical Narrative

1. Episodic Memory Storage  
   Write chosen chain to MemoryTape.  
   Link edges through MemoryGraph (temporal, similarity, causal, goal relevance).

2. Multi-Relational Embedding Space  
   Every time we add edges, we encode the updated working memory graph into a latent vector space:

Each MemoryRecord has:  
content_embedding: semantic representation (text, sensor fusion, context).  
Each edge type is assigned a transformation vector or operator:

```
T_temporal = unit displacement on time axis.
T_similarity = close embedding alignment.
T_causal = directional translation (TransE-style: cause + relation ≈ effect).
T_relevance = weighted axis conditioned on current goal embedding.
```

Combined embedding:  
`z_node* = content_embedding ⊕ Σ (relation_weight × T_relation)`

> This means nodes aren’t just content, they are content + relational signature.  
> This gives us a multi-relational latent manifold where:  
> Radius search retrieves nodes connected by one type of relation (similarity sphere).  
> Higher-dimensional sphere query retrieves clusters that satisfy all relations simultaneously (similar, temporally close, causally relevant, and salient).

👁 Analogy: This is like mixing word embeddings with knowledge graph embeddings (TransE/RotatE/ComplEx) and then projecting them into a single working latent space for the HC to search.

## 3.10 Autobiographical Narrative Storage (expanded)  
   Narrative nodes also get multi-relational embeddings.

Use a set transformer or GRU over the embeddings of all linked episodic memories.  

Store narrative as:  
```
summary_text (LLM-generated).  
narrative_embedding = pooled latent vector 
``` 
representing both memories + relation types.

This allows queries like:

> “Find all narratives in which I was under social stress and learned something new.”

By executing a high-dimensional sphere query in relation space combining e.g. 
`tag=stress, relation=causality, and goal=learning.`

During Memory Write, also link episodes to any referenced VisionNodes (tracked_by → MemoryRecord) and update realization progress, including realized_by/failed_by when success criteria are met or infeasible; adjust feasibility predictors accordingly.

## 3.11. World Model and Self-Model Update

- Update recurrent world state via RSSM with latest encoded inputs and executed actions.
- Update self-model z_self via EMA and learned GRUs from b_t and autobiographical narrative, modulated by μ.
- SML Commit Phase:
  - Observe final SelfState_{t+1}, compute self_pred_error and per-facet z-scores.
  - Update P_self (online or buffer) and uncertainty calibration (compare predicted variance vs realized error).
  - Update drift_rate and narrative_integrity; raise alerts if thresholds exceeded.

## 3.12. Mind-Wandering Micro-Loop Activation

- Triggered when serotonin 5HT is high and external input demand low, or uncertainty is elevated.
- Executes sequences of internal introspection without external actions:
  - Repeated self-queries, hypothesis generation, memory expansions, salience evaluation, filtered selection, and reward tagging.
- Supports creativity, insight, and reflection.
- Incubate VisionNodes via divergent exploration (novel alternatives under constraints) and convergent refinement (mitigate risks/constraints), including counterfactual stress-testing of dependencies and timelines.
- SML Hook: Schedule introspective tasks (re-estimate uncertain facets, recompute drift_forecast) and inject adjustment candidates (rest, refocus, review) into generator.

## 3.13 [Revel subroutine (affect-driven savoring, 5HT-led stabilization)](ideas/DMN/routines/RevelRoutine.md)

  -   Gate on low external demand or explicit intent; require safe state (low hazards), NE low→moderate, stable self-model; clamp max μ levels and dμ/dt.

  -   Curate positively tagged autobiographical memories (love/beauty/gratitude) with high identity alignment and safety; play calm→peak→fade with grounding pauses.

  -   Upregulate Raphe 5HT via osteosteric priming and PHM routing; run short closed-loop epochs (200--500 ms) projecting along top‑k edges, enforcing per‑area ceilings/ramps; taper back to baseline and write a "savoring summary."

- 3.5 DMN binding integrates curated memories and self-anchors into b_t, priming affect targets while suppressing DA spikes and unnecessary NE; PHM/edge routing preferences are set for safe, prosocial stabilization.

- 3.6--3.7 Closed-loop control ties μ adjustments to online metrics (ΔC, ΔH, safety, identity drift), dynamically scaling emitter outflux and edge weights; immediate taper/abort on coherence drop or safety rise, then reintegrate and persist narrative links.
  
- SML Hook: During savoring, clamp drift-sensitive updates; log stabilization effect on coherence & drift variance for later comparison.

## 3.13. Recursive Re-entry into DMN

- Feed the chosen thought chain as inner speech into the next cycle's DMN input combined with fresh sensory text.
- Loop continues endlessly, enabling ongoing conscious experience.
- SML Hook: Pass self_summary (alignment_score, drift_rate, uncertainty_vector) into next cycle's MDN Parsing meta-context.

## 3.14. Self Modeling Layer Summary API (New)

Provides standardized interface for all above hooks:
- get_self_summary() → minimal metrics used in phases 3.2/3.4/next-tick initialization.
- predict_candidate_impact(batch) → vectorized impacts for scoring & pruning.
- commit_self_transition(prior, pred, observed, annotations) → updates buffers & metrics.
- run_counterfactuals(candidate_set, horizon H) → returns projected {value, drift_risk, coherence_gain}.

## 4. Cleanup / Consolidation Mechanisms
### 4.1 Sleep / Garbage Collection (Meta-Gate)

- Enter sleep-like state when histamine (HA) drops below a threshold (and/or orexin low).
- Processes:
  - Garbage Collection (GC): purge low-value/redundant traces, decay low-salience nodes.
  - Memory Consolidation: episodic → semantic; narrative updates; symbolic abstraction of repeated event-sequences; update long-term predictive/Markov models.
  - Replay & Reweighting: HC replay strengthens salient edges; downscale irrelevant activations.
- Wake Transition: when HA surpasses the wake-threshold, return to wake DMN loop.
- Consolidate visionary clusters into higher-level objectives, prune stale/superseded items, reconcile contradictions, and learn feasibility/realization predictors from accumulated evidence.

> ✅ Perplexity: One unified latent space means the hippocampus doesn’t need to separately search similarity, causality, time, goals — it just queries a manifold ball.

> Sphere queries → HC.expand(b_t, radius=r, relation_weights=W) can dynamically tune which relations matter (like “bias toward causality vs similarity”), modulated by neuromodulators.
>
> Narrative embeddings make autobiographical reasoning as searchable as episodic memories.
>
> Autobiographical records can be clustered → the agent can ask “show me all phases of life where I pursued exploration goals.”

### 4.1. Memory Consolidation: Probabilistic Knowledge Formation

---

Memory consolidation transforms raw episodic experience graphs into structured symbolic knowledge, enabling abstract cognition:

- Duplicate Removal: Merge nodes representing nearly identical experiences, preserving count data to inform frequency estimates.

- Causal Edge Extraction: Detect action → reaction pairings, explicitly linking cause and consequence nodes.

- Markov Chain Construction: Build probabilistic transition models capturing likely sequences of events or thoughts (cleaned to a single expression):

  P(next_state = s_j | current_state = s_i) = count(i → j) / Σ_k count(i → k)

- Symbolic Abstraction: Detect high-frequency patterns and replace them with abstract symbolic nodes (e.g., "Insult Action").

- Probability Maps: Collapse Markov chains into probabilistic summaries assigning likelihoods to reaction categories (e.g., Negative Reaction: 97%, Positive Reaction: 3%).

- Hierarchical Transfer: Gradually move from episodic experiences to semantic knowledge and finally into an autobiographical narrative self-model, forming the backbone of introspective identity.

---

## Additional Introspective Context: DMN Control Flow Graph (CFG)

One extension to the ACI design is to explicitly represent the **DMN loop as a Control Flow Graph (CFG)**.  
This CFG encodes each step of the algorithm as a _node with identifiers, inputs, outputs, and function role_.

By maintaining a structured CFG definition (e.g., in YAML), the system can:

- **Feed active CFG node tags into LLM invocations** as _meta-context_, so the model always knows:

  - _Which stage of the DMN loop it is operating in._
  - _What its current purpose is (e.g., hypothesis generation vs salience valuation)._
  - _What constraints or output type is expected._

- **Enhance process self-awareness**:

  - The system carries "awareness" of being in _Candidate Generation_, _HC Expansion_, or _VS Valuation_ phase.
  - Prevents role confusion and encourages consistent LLM behavior.

- **Enable explicit traceability**:

  - Each memory node or thought can be tagged with the CFG node ID that produced it.
  - Later, autobiographical narrative can reflect not just _what was thought_, but _how it was produced_.

- **Stabilize cognitive dynamics**:

  - Feeding CFG meta information constrains the LLM to follow algorithmic intent.
  - Acts as a role-encoding signal, similar to "system prompts" that anchor behavior.

- **Provide a basis for metacognitive reasoning**:
  - By referring to its own CFG, the system can simulate/debug itself:
    > "This candidate emerged during PFC-1 hypothesis expansion while DA was high, but it was pruned at VS valuation."

---

When invoking an LLM sub-process (e.g., to generate a thought, evaluate coherence, or narrativize memory),  
prepend a meta-context header:

```yaml
DMN_State:
  Node: "3.4_Candidate_Generation"
  Purpose: "Generate abductive hypotheses"
  Neuromodulators: { DA: 1, NE: 0.7, 5HT: 0.8 }
```

This makes the LLM _process-aware_ of **where it is in the loop** and **what its functional role is**.