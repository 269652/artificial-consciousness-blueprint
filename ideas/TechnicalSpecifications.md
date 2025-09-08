# Technical Specifications

## Project Abstract

The Always-On Consciousness-Inspired AI (ACI) is a comprehensive algorithmic blueprint for artificial consciousness that models key neural circuits underlying human self-awareness and introspection. The architecture centers on a recursive Default Mode Network (DMN) loop operating at 5-20 Hz, coordinating perception, memory consolidation, associative reasoning, and autobiographical narrative formation through biologically-inspired subsystems including hippocampal memory expansion, prefrontal executive control, and ventral striatum valuation.

Core Innovation: The system treats memory not as static storage but as a dynamic, multi-relational knowledge graph where consciousness emerges from recursive self-reflection on experiential traces. Memory nodes embed both content and relational signatures (temporal, causal, similarity, relevance) within a unified latent manifold, enabling sophisticated associative retrieval and consolidation. A homeostatic neuromodulator system (dopamine, serotonin, norepinephrine, oxytocin, histamine, orexin) dynamically modulates cognitive parameters, exploration-exploitation balance, and sleep-wake transitions.

## Core Algorithm Summary

### Default Mode Network (DMN) Loop

See [DMN Algorithm and Thought Cycle](DMN.md) for the full technical breakdown.

**Summary (kept concise):**
- Perception → parse → candidate thought generation → scoring
- Hippocampal expansion & associative variants
- Valuation & selection (VS / PFC-2)
- Reward tagging & memory / narrative consolidation
- Mind-wandering & savoring subroutines
- Continuous recursive re-entry with neuromodulator / affect modulation

For detailed steps, see [DMN.md](DMN.md).

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
