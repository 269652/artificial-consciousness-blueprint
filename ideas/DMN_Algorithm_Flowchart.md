# DMN Algorithm Control Flow Graph

This diagram visualizes the core cognitive loop of the ACI, as detailed in `Algorithm.md`. It illustrates the flow of information from sensory input through various processing stages, culminating in action, memory updates, and recursive re-entry.

```mermaid
graph TD
    subgraph "DMN Tick Cycle (5-20 Hz)"
        A["0. Sensor Ingress & Preprocessing"] --> B["1. MDN: Parse Text to AST"];
        B --> C["2. PFC-1: Dispatch & Enrich Context"];
        C --> D["3. Iterative Thought Layer<br/>(Generate & Score Candidates)"];
        D --> E["4. Bind Workspace Context<br/>(Create b_t vector)"];
        E --> F["5. HC: Expand Context<br/>(Multi-relational Latent Space Query)"];
        F --> G["6. VS: Explore Expanded Graph<br/>(Value Estimation)"];
        G --> H["7. PFC-2: Final Selection<br/>(Select Coherent Chain)"];
        H --> I["8. NAcc: Reward Tagging"];
        I --> J["9. Update Memory, World & Self-Model"];
    end

    subgraph "Internal Loops & Recursive Entry"
        J --> K{"10. Mind-Wandering Gate<br/>(Low external demand, High 5HT)"};
        K -- Yes --> L["Internal Simulation Loop<br/>(Self-Query, Replay)"];
        L --> J;
        K -- No --> M["11. Recursive Re-Entry<br/>(Feed output as next input)"];
    end

    subgraph "Global State Control (Meta-Cycle)"
        M --> Z{"Histamine Level Check"};
        Z -- Normal --> A;
        Z -- Below Threshold --> P["Enter Sleep Phase<br/>(Gate external senses)"];
        P --> Q["Memory Consolidation<br/>(GC, Replay, Abstraction)"];
        Q --> R["Wake Up<br/>(Resecrete Histamine)"];
        R --> A;
    end

    classDef core fill:#85C1E9,stroke:#3498DB,stroke-width:2px,color:#000;
    classDef memory fill:#BB8FCE,stroke:#8E44AD,stroke-width:2px,color:#000;
    classDef control fill:#F7DC6F,stroke:#F1C40F,stroke-width:2px,color:#000;

    class A,B,C,D,E,G,H,I,J,M core;
    class F,Q memory;
    class K,Z,P,R control;
```

### How to Read the Diagram:

1.  **DMN Tick Cycle**: This is the main, high-frequency loop (5-20 Hz) that represents one "thought." It processes sensory input, generates and evaluates thoughts, and updates the system's state.
2.  **Internal Loops**: After a thought cycle completes, the system can either recursively feed its own output back into the loop (simulating a stream of consciousness) or enter a "mind-wandering" state for internal simulation and reflection.
3.  **Global State Control**: This is a slower meta-cycle that governs the sleep/wake state, controlled by a simulated neurochemical (Histamine). The sleep phase is critical for memory consolidation, garbage collection (GC), and preventing memory overload.

This visualization provides a clear, high-level overview of the algorithm's architecture and control flow.
