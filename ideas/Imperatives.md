# Imperatives Module

## Purpose

The Imperatives module encodes core survival and self-preservation drives (e.g., safety, energy, social belonging, exploration, homeostasis) and provides top-down modulation to:

- Directly influence AffectModulator outputs (e.g., upregulate fear/urgency, downregulate social drive under threat).
- Modulate associative cortex prompts (e.g., bias perception, memory retrieval, and candidate generation toward imperative-relevant content).
- Modulate neurotransmitter (NT) release rates (e.g., trigger NE/DA bursts under threat or opportunity).

## Core Components

- **Imperative Vector (`I_t`)**
  - Encodes current imperative drive levels (e.g., safety, hunger, fatigue, social, exploration, homeostasis).
  - Updated each tick based on internal state, sensory input, and DMN context.

- **AffectModulator Coupling**
  - `I_t` provides direct gain or gating signals to AffectModulator neurons (e.g., increase w_SAFE, clamp w_SOC, boost NE_gain).
  - Can override or scale affective outputs in urgent or survival-critical contexts.

- **Associative Cortex Prompt Modulation**
  - `I_t` injects imperative-weighted priors into associative cortex and candidate generation prompts.
  - Example: High safety imperative → bias retrieval and generation toward threat detection, avoidance, and risk assessment.

- **NT Release Modulation**
  - `I_t` provides direct input to NT emitter modules (e.g., NeuroTransmitterEmitterModule), scaling or gating DA, NE, 5HT, OXT, etc.
  - Example: High exploration imperative → increase DA release; high threat → NE burst.

## API Sketch

```python
class ImperativesModule:
    def __init__(self):
        # ...initialize imperative vector, coupling weights...
        pass

    def update(self, internal_state, sensory_input, dmn_context):
        # Update imperative vector I_t based on current state
        pass

    def modulate_affect(self, affect_state):
        # Apply imperative-driven gains/gates to AffectModulator outputs
        pass

    def modulate_associative_prompt(self, prompt_ctx):
        # Inject imperative priors into associative cortex prompt context
        pass

    def modulate_nt_release(self, nt_release_rates):
        # Scale/gate NT release rates based on imperative vector
        pass
```

## Integration

- **AffectModulator:** Receives imperative-driven modulation signals each tick; can be overridden or scaled by survival imperatives.
- **Associative Cortex:** Prompts and candidate generation are biased by current imperative priorities.
- **NT Emitter Modules:** NT release rates are dynamically adjusted in response to imperative state.

## Example Imperatives

- **Safety:** Upregulate NE, w_SAFE, bias perception toward threat cues.
- **Energy/Fatigue:** Downregulate exploration, increase rest/maintenance drives.
- **Social Belonging:** Upregulate OXT, w_SOC, bias toward prosocial retrieval/actions.
- **Exploration:** Upregulate DA, novelty_gain, widen candidate generation.
- **Homeostasis:** Clamp NT/affect outputs to maintain physiological balance.

---

This scaffold ensures the Imperatives module acts as a top-level controller, dynamically shaping affect, cognition, and neuromodulation in alignment with core survival and identity drives.
