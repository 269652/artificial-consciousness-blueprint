## Neurotransmitter Module

In order to mimic biologically realistic neuromodulation, we add a **Neurotransmitter Module** that explicitly models neurotransmitter release centers (brain nuclei), their projection pathways

### Neurotransmitter Emitters (Projection Sources)

Each neurotransmitter system originates from a designated **Emitter Node** (analogous to Raphe nuclei, Locus Coeruleus, VTA, Hypothalamus, etc.).

Emitters control **production, release, and modulation** of neurotransmitters, with feedback loops from other neuromodulators and cortical outputs.

**Examples (Major NTs and canonical production regions):**

- **Dopamine (DA):**
  - **Ventral Tegmental Area (VTA):** Mesocortical/mesolimbic DA projections (motivation, reward, working memory).
  - **Substantia Nigra pars compacta (SNc):** Nigrostriatal DA projections (motor, habit).
- **Serotonin (5HT):**
  - **Raphe Nuclei (RN):** Dorsal and median raphe (mood, safety, social, mind-wandering).
- **Norepinephrine (NE):**
  - **Locus Coeruleus (LC):** Global NE projections (attention, urgency, arousal).
- **Oxytocin (OXT):**
  - **Paraventricular and Supraoptic Nuclei of Hypothalamus:** OXT production and release (social bonding, empathy).
- **Histamine (HA):**
  - **Tuberomammillary Nucleus (TMN, hypothalamus):** HA production (wakefulness, attention).
- **Orexin (ORX):**
  - **Lateral Hypothalamic Area:** ORX production (arousal, sleep/wake stability).
- **Testosterone (TST):**
  - **Hypothalamus (regulatory), Gonads (production):** Assertiveness, drive, social dominance.
- **Acetylcholine (ACh):**
  - **Basal Forebrain (Nucleus Basalis, Medial Septum):** ACh for attention, learning, memory.
- **Glutamate (Glu):**
  - **Widespread cortical and subcortical neurons:** Primary excitatory transmission.
- **GABA:**
  - **Widespread interneurons (cortex, striatum, etc.):** Primary inhibitory transmission.

**Major Transporter Production/Localization:**
- **SERT:** Expressed in Raphe neurons and projection terminals (5HT reuptake).
- **DAT:** Expressed in VTA/SNc neurons and terminals (DA reuptake).
- **NET:** Expressed in LC neurons and terminals (NE reuptake).
- **OXT transporter:** Hypothalamic neurons and projections.
- **VMAT:** All monoaminergic neurons (vesicle loading).
- **GAT:** GABAergic neurons and glia.
- **EAAT:** Glutamatergic neurons and astrocytes.
- **ChT:** Cholinergic neurons (ACh precursor uptake).

Each **NeuroTransmitterEmitter** object:

- Has a **baseline production rate** (µ per tick).
- Has a **projection topology** (list of cortical/striatal/limbic targets).
- Its release can be **modulated by other neuromodulators** (e.g. DA ↑ can indirectly suppress 5HT production).
- Maintains a **dynamic concentration state**.

---

### Receptor Expression Profiles

At the receiving end, every target area expresses an **Array of Receptors**, each defined by:

- **Density `ρ`**: number of binding sites per receptor type.
- **orthosteric site**: main active binding site; requires neurotransmitter "protein" match.
- **Allosteric site**: modulatory site that augments ost.

#### Binding Dynamics

- Each receptor has:

  - **Orthosteric Vector** (receptor embedding).
  - **Allosteric Vector** (modulator-sensitive embedding).
  - **Latent embedding** describing cell-surface protein identity.

- Each neurotransmitter is represented as a **Protein Vector**, carrying:
  - Base embedding (molecular identity).
  - Instantaneous concentration level.
  - Internal NN (dimensions = concentration level).
  - Can only bind if **cosine similarity ≥ θ_bind** between transmitter vector and receptor site vector.

#### Activity Calculation

1. Binding Phase

   - For each receptor:  
     If neurotransmitter cosine similarity with orthosteric > θ, binding occurs.  
     Orthosteric contribution = `(NT_vector * sensitivity)`.

2. Allosteric Modulation

   - Bound allosteric proteins modify receptor output:  
     `orthosteric_signal * f(allosteric_vector)`

3. Saturation & Density

   - Signal strength scales with receptor **density ρ**.
   - Unbound receptors output 0.

4. Final Receptor Activity
   - Each receptor computes activity via NN:  
     Input dim = receptor density.  
     Weighted by neurotransmitter embedding and modulated outputs.
   - Aggregate receptor activities per area produce an **Effective Neuromodulator Influence Vector**.

---

### Homeostatic Neuromodulator System

The **Homeostatic Module** integrates the above projections with **dynamic receptor binding** and provides system-wide regulation:

- Maintains **neurochemical balance** via autoreceptor-like feedback (e.g. 5HT1A receptors suppress serotonin release).
- Tracks overall “brain milieu” vector:  
  `μ = {DA, 5HT, NE, OXT, TST, HA, ORX}`
- Regulatory functions:
  1. **Exploration/Exploitation Balance** (DA vs 5HT vs NE).
  2. **Social/Prosocial Biasing** (OXT, TST, 5HT).
  3. **Arousal States & Wakefulness** (HA, ORX).
  4. **Learning Drive** (DA novelty signaling).
  5. **Emotional Safety / Threat Aversion** (5HT gating).
  6. **Urgency / Crisis Mode** (NE bursts).
  7. **Sleep Switch Control** (histamine decay + orexin gating for transitions).

The homeostatic module therefore acts as a **cortical thermostat**, ensuring all modules work inside adaptive computational envelopes.

### Why This Design Matters

- **Embodiment of neurobiology**: neurotransmitters come from realistic projection centers (Raphe, VTA, LC).
- **High fidelity binding logic**: vector similarities with thresholds encode lock-and-key protein metaphor.
- **Allosteric modulation**: allows subtle control of receptor activity, mirrors pharmacology.
- **Neural Nets in Proteins and Receptors**:
  - NT “protein” nets: dynamic outputs based on level.
  - Receptor nets: plastic responses based on density.
- **System-level regulation**: Homeostat integrates activation into computational modulations for DMN/ACI loop (explore/exploit, urgency, safety, sleep/wake).

---
