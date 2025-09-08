# CoherenceClassifier Hook (Pre-Memory-Write)

Purpose: Lightweight, deterministic hook run AFTER candidate thought selection but BEFORE committing any new memory nodes. It attaches coherence / contradiction tags, optionally blocks low-importance contradictions, and emits a chain-level coherence metric + reward hint for downstream valuation (e.g. NAcc). Keep it fast; degrade gracefully if model components unavailable.

---
## 0. Position in Loop
```
Candidate Thought Chain (selected)
   ↓
CoherenceClassifier Hook  <— (this file)
   ↓ (tags, metrics, maybe block/quarantine)
Retention / Compression → Memory Commit
```

---
## 1. Inputs (Minimal Required)
| Name | Type | Notes |
|------|------|-------|
| chain.id | str | Unique thought chain identifier |
| chain.text / chain.propositions | str / list | Raw text OR pre-extracted propositions |
| narrative_ctx | list[proposition_id] | Top-k active narrative proposition refs |
| goals_ctx | list[goal_proposition_id] | Active goal propositions |
| self_facets | dict[facet_id → vector] | For self / goal contradiction detection |
| mem_api.retrieve_semantic(p,k) | fn | Embedding similarity top-k nodes |
| mem_api.retrieve_temporal(p,k) | fn | Temporal neighbors (optional) |
| mem_api.retrieve_self(p,k) | fn | Self-referential nodes (optional) |
| calib_state | object | Temperature T, optional isotonic map, reliability bins |
| cfg.thresholds | dict | τ_c, τ_block, θ_importance, etc. |
| cfg.weights | dict | w_h, w_m, α1, α2 (reward hint) |

---
## 2. Outputs
Per proposition p → Tag:
```
{
  triplet: (subj, pred, obj, modifiers?),
  entail: float ∈ [0,1],
  contradict: float ∈ [0,1],
  neutral: float ∈ [0,1],
  uncertainty: sigma_or_entropy,
  support_evidence: [(mem_id, weight)...],
  contradiction_evidence: [(mem_id, weight, type)...],
  contradiction_types: [type...],
  drift_facets: [facet_id...],
  confidence: float (post calibration)
}
```
Chain-level metrics:
```
{
  coherence_chain: (Σ entail − Σ contradict)/N_props,
  contradiction_density: (# props with contradict>τ_c)/N_props,
  drift_risk: g(drift_facets),
  reward_hint: α1*Δcoherence_est − α2*drift_risk,
  blocked: bool
}
```
If blocked: return early with quarantine directive.

---
## 3. Minimal Data Structures
```
Proposition = {subj, pred, obj, polarity, modifiers}
EvidenceNode = {id, text, embedding, recency, trust}
PropTag = {...as above...}
HookResult = {blocked, prop_tags, chain_metrics}
```

---
## 4. Processing Stages (Compact)
| Stage | Name | Goal | Hard Latency Cut? |
|-------|------|------|------------------|
| 0 | Normalize | Extract propositions if absent; canonicalize negations/numbers | ✔ if > budget skip advanced parse |
| 1 | Retrieve | Collect semantic / temporal / self evidence | Reduce k if latency high |
| 2 | Heuristics | Fast entail/contradict seed (overlap, negation, antonym, numeric) | Always |
| 3 | Model Pass | Optional mini-NLI over top-m evidence | Skip if model_ready=False or budget hit |
| 4 | Fusion | Combine heuristic + model scores | Always |
| 5 | Conflict Typing | Label contradiction categories | Skip if contradict<τ_c |
| 6 | Calibration | Apply temperature / isotonic; compute uncertainty | Use previous T if update deferred |
| 7 | Decision | Block / tag / drift facet flagging | Always |
| 8 | Aggregate | Chain metrics + reward hint | Always |
| 9 | Log | Emit JSONL record | Compress fields if large |

---
## 5. Heuristic Rules (Phase A)
- Polarity flip words: {"not","never","no"}
- Antonym lexicon (small curated; expand later)
- Numeric contradiction: |num_new − num_old| / max(1,num_old) > δ_num (e.g. 0.2)
- Temporal order clash: pattern ("after" vs "before") swap on same pair
Scoring increments:
```
entail_h += overlap_tokens / total_tokens
contr_h += polarity_flip? + antonym_hits + numeric_flag + temporal_flag
```
Clamp to [0,1].

---
## 6. Score Fusion
```
entail_raw = max( w_h*entail_h, w_m*entail_m )   # or smooth_max
contr_raw = max( w_h*contr_h, w_m*contr_m )
neutral_raw = 1 - max(entail_raw, contr_raw)
```
If model missing → entail_m=contr_m=0.

---
## 7. Thresholds (Defaults)
| Param | Default | Rationale |
|-------|---------|-----------|
| τ_c | 0.70 | Initial contradiction detection (precision first) |
| τ_block | 0.85 | Hard reject noisy contradictions |
| θ_importance | 0.60 | Importance gate (importance<θ + high contradiction → block) |
| τ_conf_low | 0.55 | Below → treat as neutral for reward hint weight |
| δ_num | 0.20 | Numeric relative diff contradiction |
| k_sem | 8 | Semantic evidence cap |
| k_temp | 4 | Temporal neighbors |
| k_self | 4 | Self evidence |
| m (NLI) | 4 | Evidence pairs to score |
| w_h | 0.4 | Heuristic weight |
| w_m | 0.6 | Model weight |
| α1 | 0.8 | Reward coherence weight (align global) |
| α2 | 0.6 | Reward drift risk weight |

Adaptation: lower τ_c only after precision_contradiction > target (e.g. 0.9 over last 500).

---
## 8. Drift Facet Detection
Add facet_id to drift_facets if contradiction_types includes any of:
- self_trait
- goal_violation
Aggregate drift_risk = mean(contradict scores for those facets) * log(1+|unique facets|).

---
## 9. Reward Hint
```
Δcoherence_est = coherence_chain - coherence_prev_est   # pass previous chain estimate via ctx
reward_hint = α1*Δcoherence_est - α2*drift_risk
# Apply confidence scaling
confidence = mean(max(entail,contradict) for props)
reward_hint *= confidence if confidence > τ_conf_low else 0
```

---
## 10. Blocking Logic
```
if any(p.contradict > τ_block for low-importance props) and chain.importance < θ_importance:
    blocked = True (quarantine)
```
Quarantine re-check schedule: every Q ticks or upon new supporting evidence referencing same proposition subjects.

---
## 11. Latency Budget Strategy
| Budget Event | Action |
|--------------|--------|
| hook_time > 1.2*budget | Drop model pass; heuristics only |
| hook_time > 1.5*budget | Reduce retrieval k (halve) |
| backlog_flag True | Skip calibration update (use cached T) |

---
## 13. Metrics to Log
| Metric | Definition | Goal |
|--------|------------|------|
| precision_contradiction | TP / (TP+FP) on labeled set | ≥0.9 before lowering τ_c |
| recall_contradiction | TP / (TP+FN) | Gradual ↑ after precision stable |
| chain_coherence_stability | Rolling std of coherence_chain | ↓ over phases |
| drift_leading_indicator | % drift facet contradictions preceding drift spike | Establish predictive value |
| ECE_margin | Calibration error on (entail-contradict) margin bins | ↓ with calibration |
| false_block_rate | (Later accepted) / blocked | Keep < 5% |

---
## 14. Phased Enablement (Checklist)
| Phase | Additions | Exit Criterion |
|-------|-----------|---------------|
| A | Heuristics only | precision_contradiction heuristic baseline logged |
| B | NLI model | +10% precision over heuristic baseline |
| C | Calibration (temperature) | ECE_margin ↓ ≥20% |
| D | Conflict typing + drift facets | drift_leading_indicator measurable |
| E | Adaptive τ_c lowering | FPR stable <5% over 1k samples |

---
## 15. Implementation File Layout (Suggestion)
```
/coherence_classifier
  heuristics.py
  retrieval.py
  nli.py
  calibration.py
  hook.py
  schemas.py
  tests/
```

---
## 16. Quick Start Implementation Order
1. extract_props (regex + basic parse)  
2. retrieval (semantic only)  
3. heuristics scoring  
4. hook aggregation + logging  
5. add mini NLI  
6. temperature calibration  
7. conflict typing & drift facets  
8. adaptive thresholds + reliability bins  

---
## 17. Logging Schema (JSONL)
```
{
  tick: int,
  chain_id: str,
  p_id: str,
  entail: float,
  contradict: float,
  uncertainty: float,
  types: [str],
  drift_facets: [str],
  coherence_chain: float,
  contradiction_density: float,
  blocked: bool
}
```

---
## 18. Upgrade Path (Post MVP)
- Temporal relation graph (Allen interval reasoning)
- Causal inversion detection ("causes" vs "prevents")
- Paraphrase embedding alignment for oblique self contradictions
- Active learning: re-score top uncertainty propositions with higher-capacity model offline

---
## 19. Safety / Fail-Safes
| Condition | Fallback |
|-----------|----------|
| Hook crash | Treat chain as neutral (no block) + log error event |
| Model timeout | Skip model scores; heuristics only |
| Calibration drift detected | Reset temperature to 1.0; re-fit when sample buffer refills |

---
## 20. Rationale Summary
Separating semantic consistency assessment (mPFC/ACC analog) from valuation (NAcc analog) improves credit assignment, keeps reward gradients cleaner, and allows independent tuning of contradiction precision before amplifying its influence on learning.

(End)
