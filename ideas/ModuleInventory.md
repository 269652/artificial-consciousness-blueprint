# Module Inventory

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
| Memory Architecture | Multi-dimensional knowledge graph with relational embeddings | Documented |
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

## Summary of Neuromodulator Impact on Algorithms

| Neuromodulator | Algorithmic Effects |
| --- | --- |
| Dopamine (DA) | Increases novelty weight w_DA, exploration budget, consolidation priority, reward signaling; promotes broad associative search ("panning"). |
| Serotonin (5HT) | Opens mind-wandering gate; raises safety penalty w_SAFE; favors positive/safe memory paths; decreases risk appetite. |
| Norepinephrine (NE) | Controls beam search width and depth (focus vs exploration); increases urgency and search depth; biases toward highly relevant/urgent memories and thoughts. |
| Oxytocin (OXT) | Heightens prosocial prior w_SOC, boosts social memory recall and identity coherence weight w_ID. |
| Testosterone (TST) | Increases assertive, goal-seeking weights; raises cost-delay penalties; counterbalanced by serotonin for risk management. |
| Orexin (ORX) | Primary wake-state maintenance; enables sustained goal-directed behavior and locomotor activity; modulates arousal threshold for external stimuli; amplifies histamine release and cognitive wakefulness; gates transition from sleep/consolidation back to active DMN loop. |
| Histamine (HA) | Core wakefulness signal enabling cognitive processing and attention; maintains conscious awareness and prevents sleep transitions; when depleted below threshold, triggers sleep/consolidation mode; supports working memory and executive function during active cognition. |

## Module Details (Condensed)
(See individual specs in this folder for full detail.)
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
