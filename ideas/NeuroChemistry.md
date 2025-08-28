# Neurochemistry Module: SERT, NET, NE, and Receptor Dynamics

## 1. Entities and Embeddings

**Major Neurotransmitters (NTs):**
- **Dopamine (DA):** Motivation, reward, exploration, working memory.
- **Serotonin (5HT):** Mood, safety, social behavior, mind-wandering.
- **Norepinephrine (NE):** Attention, urgency, arousal, focus.
- **Oxytocin (OXT):** Social bonding, empathy, prosocial modulation.
- **Histamine (HA):** Wakefulness, attention, sleep-wake transitions.
- **Orexin (ORX):** Arousal, wake/sleep stability, drive.
- **Testosterone (TST):** Assertiveness, drive, social dominance.
- **Acetylcholine (ACh):** Attention, learning, memory encoding.
- **Glutamate (Glu):** Primary excitatory transmission, cognition.
- **GABA:** Primary inhibitory transmission, stability, gating.

For each NT:
  - `density_embedding`: Encodes the amount/concentration at each location.
  - `location_embedding`: Encodes spatial distribution (e.g., a vector or map over the simulated brain region).

**Major Transporters:**
- **SERT:** Serotonin transporter (5HT reuptake).
- **DAT:** Dopamine transporter (DA reuptake).
- **NET:** Norepinephrine transporter (NE reuptake).
- **OXT transporter:** Oxytocin reuptake (less well-characterized, but include for completeness).
- **VMAT:** Vesicular monoamine transporter (vesicle loading, not direct reuptake but essential for NT cycling).
- **GAT:** GABA transporter.
- **EAAT:** Excitatory amino acid transporter (glutamate reuptake).
- **ChT:** Choline transporter (ACh precursor uptake).

For each transporter:
  - `density_embedding`: Number of transporter molecules per location.
  - `location_embedding`: Spatial distribution (often more uniform than receptors).
  - `instruction_vector`: Encodes reuptake/recycling behavior.

**Receptor:**
  - `density_embedding`: Number of receptors at each location.
  - `location_embedding`: Spatial map (often clustered at synaptic edges).

---

## 1a. Integration with ProjectionModulator

- The NeuroChemistry module receives, at each tick, a set of **incoming NT projections** from the [ProjectionModulator](./ProjectionModulator.md).
- Each incoming projection specifies:
    - Source area (or emitter node)
    - NT type
    - Amount of NT influx
    - **Location embedding**: spatial pattern of NT arrival in the target area (e.g., a vector or map over the region)
- The NeuroChemistry module maintains a **map of incoming projections**:
    - `incoming_projections: Dict[(source_area, nt_type), location_embedding]`
- On each tick:
    1. For each incoming projection, update the NT's `location_embedding` in the target area by adding the influx at the specified locations.
    2. Proceed with local diffusion, binding, and reuptake as before.
- This allows the NeuroChemistry module to model realistic spatial NT gradients based on projection topology and dynamic routing.

**Design Note:**  
- The ProjectionModulator handles global routing, homeostatic scaling, and edge gating.
- The NeuroChemistry module focuses on local NT dynamics, using the influx patterns provided by the ProjectionModulator.

---

## 2. Diffusion and Brownian Motion

- On each tick, update the `location_embedding` of each NT using a Brownian motion model:
  - For each NT molecule, update its position:  
    `x_new = x_old + N(0, σ^2)`  
    where `σ` is the diffusion coefficient.
  - Aggregate all positions to update the NT’s `location_embedding` (e.g., as a heatmap or vector).

---

## 3. Binding Mechanism

- For each receptor:
  - Compute local NT concentration by evaluating the overlap between NT’s `location_embedding` and the receptor’s `location_embedding`.
  - If local NT concentration > threshold, binding occurs with probability proportional to concentration and receptor density.
  - SERT/NET compete for free NT in the same way, but their `location_embedding` is typically more diffuse.

- **Binding Priority:**
  - If NT concentration is highest near a receptor, binding to the receptor is favored.
  - If NT diffuses away (unbound), SERT/NET can reuptake NT where their density is high.

---

## 4. Reuptake and Recycling

- SERT/NET’s `instruction_vector` encodes the reuptake process:
  - When NT is unbound and within a SERT/NET-rich region, execute reuptake:
    - Remove NT from extracellular space.
    - Optionally, increment a recycled NT pool.

---

## 5. Update Loop (per tick)

1. **Release:**  
   - NT is released at emitter location, updating its `location_embedding` (high local concentration).

2. **Diffusion:**  
   - Update NT’s `location_embedding` using Brownian motion.

3. **Binding:**  
   - For each receptor and transporter, compute local NT concentration.
   - Bind NT to receptor if local concentration is high and receptor is available.
   - If not bound, check for SERT/NET reuptake.

4. **Unbinding:**  
   - After a dwell time, NT unbinds and diffuses again.

5. **Reuptake:**  
   - If NT is in a SERT/NET-rich region, execute reuptake via `instruction_vector`.

6. **Update Embeddings:**  
   - All entities update their `location_embedding` and `density_embedding` as appropriate.

---

## 1b. Simplified Scalar Dynamics Mode (Optional Minimal Path)

If you prefer a lean, testable first implementation, replace / augment embeddings with scalar (or small-vector) concentrations and densities per (area, NT, receptor_type):

State per area a and neurotransmitter n:
- NT_level[a,n]          (extracellular concentration)
- Transporter_density[a,n] (e.g., SERT, DAT, NET, GAT, EAAT, etc.)
- Receptor_density[a,r]  (total receptors of type r)
- Bound[a,r]             (currently occupied receptors)
- Optional: Allosteric_factor[a,r] (scalar modulation 0.5–2.0)
- Release_pool[e,n] at emitter e (for resource-constrained release)

Core parameters (global or learned per type):
- k_on[r,n], k_off[r,n]             (association / dissociation)
- k_reuptake[n]                     (baseline transporter rate)
- α_transporter[n]                  (scaling by transporter density)
- D_n                               (diffusion/decay coefficient)
- k_release[e→a,n]                  (per edge release allocation)
- Kd[r,n] = k_off / k_on            (affinity)
- Hill coefficient h_r (optional; default 1)

Derived:
- Free_receptors[a,r] = Receptor_density[a,r] - Bound[a,r]

Update equations per tick Δt (explicit Euler or semi-implicit):

1) Release (from emitters to areas):
   Release_in[a,n] = Σ_e k_release[e→a,n] * Release_pool[e,n]
   NT_level[a,n] += Release_in[a,n]

2) Binding / Unbinding (mass-action):
   dBound = k_on[r,n] * Free_receptors[a,r] * NT_level[a,n] * Δt
            - k_off[r,n] * Bound[a,r] * Δt
   Bound[a,r] = clamp(Bound[a,r] + dBound, 0, Receptor_density[a,r])

3) Receptor occupancy shortcut (Hill form alternative):
   Occupancy[a,r] = (NT_level[a,n]^h_r) / (NT_level[a,n]^h_r + Kd[r,n]^h_r)
   Bound[a,r] = Occupancy[a,r] * Receptor_density[a,r]   (use either mass-action integration OR Hill equilibrium, not both in same step)

4) Reuptake / Clearance:
   Reuptake[a,n] = k_reuptake[n] * α_transporter[n] * Transporter_density[a,n] * NT_level[a,n] * Δt
   NT_level[a,n] = max(0, NT_level[a,n] - Reuptake[a,n])

5) Unbinding contribution back to pool (only if using mass-action dynamic form):
   NT_level[a,n] += k_off[r,n] * Bound[a,r] * Δt   (if not already implicit in eq. 2; avoid double counting)

6) Diffusion / Decay (lumped):
   NT_level[a,n] *= (1 - D_n * Δt)

7) Optional Allosteric Modulation (simple multiplicative gain on effect, not binding):
   Effective_signal[a,r] = (Bound[a,r] / Receptor_density[a,r]) * Allosteric_factor[a,r]

8) Postsynaptic effect (normalized activation):
   Activation[a,r] = g_r( Effective_signal[a,r] )   (g_r could be identity, sigmoid, or small 1-layer MLP)

9) Spillover metrics:
   Spillover[a,n] = max(0, NT_level[a,n] - Σ_r (Bound[a,r] * k_off[r,n]) )  (diagnostic)

10) Homeostatic adjustments (optional simple heuristics):
   If average Occupancy outside target band:
     Receptor_density[a,r] ← Receptor_density[a,r] + η * (target - Occupancy)
     Transporter_density[a,n] ← Transporter_density[a,n] + η_t * (target_level - NT_level[a,n])

Advantages of scalar mode:
- Fewer parameters → faster to calibrate.
- Direct interpretability of k_on/k_off/k_reuptake.
- Easy numerical stability checks (monitor NT_level and Bound).
- Clear mapping to pharmacology (Kd, occupancy curves).
- Smooth upgrade path: later replace scalar NT_level with spatial map or embedding; reuse same equations per voxel.

Minimal data schema (pseudo):

```
NTState {
  level: float
  k_release: float
  diffusion: float
}

ReceptorState {
  density: float
  bound: float
  k_on: float
  k_off: float
  allosteric: float
}

TransporterState {
  density: float
  k_reuptake: float
  scaling: float
}
```

Validation checklist:
- Plot occupancy vs NT_level to verify Hill / mass-action alignment.
- Ensure Bound never exceeds density.
- Track energy proxy: Σ release - Σ reuptake over window (should not diverge).
- Monitor variability (std) of NT_level; tune D_n and k_reuptake for stability.
- Add assertions on negative concentrations (clip & log).

Upgrade path:
1. Start scalar (per area).
2. Add per-subfield partition (vector).
3. Introduce location_embedding only when spatial gradients become functionally necessary.
4. Replace static k_on/k_off with small learned functions conditioned on μ or affect if justified.

Recommendation:
Adopt this scalar mode first; keep the original embedding section as a “Phase 2” enhancement. This reduces complexity and accelerates empirical iteration on cognitive impacts before adding representational richness.