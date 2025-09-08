# Memory Architecture: Multidimensional Graph of Experience

## Overview

The heartbeat of consciousness in this model is the memory graph, which acts both as a **database of experience** and a **dynamic knowledge architecture** driving cognition and self-modeling.

Effective memory is not passive storage but an evolving, prioritized, multi-dimensional knowledge graph that supports scalable abstraction, associative search, and semantic generalization. Without such a system capable of robustly **storing**, **retrieving**, **consolidating**, and **abstracting** experiential data hierarchically over time, no amount of architectural complexity in the control or sensory loops can generate true **introspection**, **self-awareness**, or **agency**.

Thus, this ACI centers on **memory as identity**: consciousness manifests not from data processing alone but from the system's capacity to **reflect meaningfully on its own past states and their causal relationships** and to generate intentional next states accordingly.

## Memory Node Structure

### Core Components

- **Content**: Textual representation of events/thoughts/actions
- **Embeddings**: Semantic vector representations enabling similarity-based retrieval
- **Contextual Meta**: Planner graphs (external/internal subgoals), sensory summaries, and submodule results
- **Attributes**: 
  - Emotional valence and arousal
  - Arbitrary tags (danger, joy, productive)
  - Timestamp, duration
  - Neurochemical state snapshot at encoding

### Edge Types

- **Temporal**: Sequential order
- **Similarity**: Semantic embeddings overlap
- **Relevance**: Task/goal salience weighted by PFC
- **Associative**: HC-generated cross-links
- **Causal**: Explicit action→reaction links identified by PFC and consolidation

## Memory Operations

### Encoding
- Incoming enriched thoughts/actions become graph nodes
- Tagged with neuromodulator state and salience
- Connected temporally and contextually
- Integrated with planner state

### Hippocampal Enrichment
- Cross-links to semantically and temporally related nodes
- Creation of hypothetical variants

### Consolidation

#### Pattern Extraction
- Merge duplicate/similar nodes, preserving counts to estimate probabilities
- Extract causal edges, forming **action → reaction** pairs (e.g., Insult → Leave)
- Build **Markov chains**, representing probabilistic transitions between memory states
- Compress frequent patterns into **symbolic abstract nodes** tied to probability maps (e.g., Insult leads to Negative Reaction 97%)

#### Hierarchical Memory Transfer
**Episodic memories** → **Semantic knowledge** → **Autobiographical narrative**

## Multi-Relational Embedding Space

### Architecture

Every time we add edges, we encode the updated working memory graph into a latent vector space:

```
Each MemoryRecord has:
- content_embedding: semantic representation (text, sensor fusion, context)

Each edge type is assigned a transformation vector or operator:
- T_temporal = unit displacement on time axis
- T_similarity = close embedding alignment  
- T_causal = directional translation (TransE-style: cause + relation ≈ effect)
- T_relevance = weighted axis conditioned on current goal embedding
```

### Combined Embedding

```
z_node* = content_embedding ⊕ Σ (relation_weight × T_relation)
```

> **Key Insight**: Nodes aren't just content, they are **content + relational signature**.

This gives us a **multi-relational latent manifold** where:
- **Radius search** retrieves nodes connected by one type of relation (similarity sphere)
- **Higher-dimensional sphere query** retrieves clusters that satisfy all relations simultaneously (similar, temporally close, causally relevant, and salient)

### Query Examples

By executing a high-dimensional sphere query in relation space combining:
`tag=stress, relation=causality, and goal=learning`

This enables queries like:
> "Find all narratives in which I was under social stress and learned something new."

## Autobiographical Narrative Storage

### Structure
- **Narrative nodes** also get multi-relational embeddings
- Use a set transformer or GRU over the embeddings of all linked episodic memories

### Storage Format
```
- summary_text (LLM-generated)
- narrative_embedding = pooled latent vector representing both memories + relation types
```

### Capabilities
- Complex autobiographical queries and self-model formation
- Identity clustering: "show me all phases of life where I pursued exploration goals"

## Memory Consolidation: Probabilistic Knowledge Formation

Memory consolidation transforms raw episodic experience graphs into **structured symbolic knowledge**, enabling abstract cognition:

### Duplicate Removal
- Merge nodes representing nearly identical experiences
- Preserve count data to inform frequency estimates

### Symbolic Abstraction
- Extract causal patterns from repeated sequences
- Create probabilistic models of state transitions
- Enable predictive reasoning about future scenarios

### Hierarchical Compression
- **Episodic**: Raw experiential records
- **Semantic**: Abstracted patterns and rules  
- **Autobiographical**: Identity-forming narrative threads

## Implementation Notes

### Knowledge Graph Integration
👁 **Analogy**: This is like mixing word embeddings with knowledge graph embeddings (TransE/RotatE/ComplEx) and then projecting them into a single working latent space for the HC to search.

### Multi-View Learning
The multi-relational memory embedding approach draws from proven techniques in:
- Knowledge graph representation learning (TransE/RotatE)
- Multi-view learning
- Predictive processing frameworks

### Symbol Grounding
The integration of symbolic abstraction with neural embeddings addresses the symbol grounding problem through experiential learning.

## Memory as the Foundation of Consciousness

**Framing**: Implementing artificial consciousness is a monumental challenge, where the most intricate and foundational problem is an effective memory system. Consciousness, as conceived in this blueprint, does not simply arise from raw computation, intelligence, or isolated algorithms. Instead, it emerges through the **recursive transformation and continual interplay of memories and thought streams** within a structured loop of cortical analogues that interact dynamically over time. This loop binds perception, memory, goals, and self-modeling into a coherent, ongoing narrative of experience.

Without such a system capable of robustly storing, retrieving, consolidating, and abstracting experiential data hierarchically over time, no amount of architectural complexity in the control or sensory loops can generate true introspection, self-awareness, or agency.

Thus, this ACI centers on **memory as identity**: consciousness manifests not from data processing alone but from the system's capacity to reflect meaningfully on its own past states and their causal relationships and to generate intentional next states accordingly.
