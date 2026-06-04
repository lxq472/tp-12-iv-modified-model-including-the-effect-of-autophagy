# Week 2 – Autophagy Extension of the Sneppen Parkinson Model

**Course:** Physics of Molecular Diseases  
**Topic:** Protein Folding Diseases II — Protein Quality Control (PQC)  
**Subunit:** Deterministic model of protease dynamics in Parkinson's disease  
**Question:** *"How would you modify the model to include the effect of autophagy? How does this change the results?"*

---

## Biological motivation

The original Sneppen model (2009) captures only the **ubiquitin-proteasome system (UPS)** as the cell's PQC machinery. But the Week 2 lecture slides show that the cell uses two complementary degradation pathways:

| Pathway | Target | Mechanism |
|---------|--------|-----------|
| UPS (proteasome P) | Small aggregates: protofilaments, monomers | Ubiquitin tagging → proteasomal degradation |
| **Autophagy** | **Large aggregates: mature fibrils F, Lewy bodies** | Double membrane → autophagosome → lysosome → degradation |

Autophagy fills the gap that the proteasome cannot: mature fibrils are too large to thread through the proteasomal barrel. Two papers cited in Sneppen (2009) motivate the extension directly:

- **Webb et al. (2003)**: α-synuclein is degraded by *both* autophagy and the proteasome.
- **Cuervo et al. (2004)**: mutant αSN (A53T, A30P) **inhibits** chaperone-mediated autophagy (CMA) — creating an additional positive feedback loop where high fibril load progressively disables its own clearance.

This gives two distinct biological scenarios worth modelling separately.

---

## Model modification

The original 4-parameter Sneppen model (eqs. 1–3):

$$\frac{dF}{dt} = \frac{m}{1+P} - \gamma FP$$

$$\frac{dC}{dt} = \gamma FP - \nu C$$

$$\frac{dP}{dt} = \sigma - P - \gamma FP + \nu C$$

We add one new variable **A** (autophagy activity, i.e. autophagic flux / autophagosome formation rate) and modify three equations:

$$\frac{dF}{dt} = \frac{m}{1+P} - \gamma FP \;-\; \delta_A \cdot A \cdot F$$

$$\frac{dC}{dt} = \gamma FP - \nu C \;-\; \delta_{AC} \cdot A \cdot C$$

$$\frac{dP}{dt} = \sigma - P - \gamma FP + \nu C \;+\; \delta_{AC} \cdot A \cdot C$$

$$\frac{dA}{dt} = \sigma_A \cdot f(F) \;-\; A/\tau_A$$

where $f(F)$ depends on the biological scenario:

**Wild-type** (normal αSN — autophagy induced by fibril stress):
$$f(F) = 1 + \frac{\alpha_A \cdot F}{K_A + F}$$

**Mutant αSN** (Cuervo 2004 — autophagy inhibited by fibrils):
$$f(F) = \frac{1}{1 + (F/K_{inh})^h}$$

### The critical insight: dual rescue mechanism

The most important term in the modification is **+δ_AC · A · C** in the dP/dt equation. Autophagy does not only degrade free fibrils F — it also degrades the fibril-proteasome **complex C = [P:F]**, releasing the sequestered proteasome back into the free pool.

```
Autophagy degrades C = [P:F]
        ↓
P released back to free pool
        ↓
Free P available to degrade protofilaments and oligomers
        ↓
Positive feedback loop broken
```

This dual mechanism — direct fibril clearance AND proteasome rescue from sequestration — is why wild-type autophagy is more effective at raising the disease threshold than any of the proteasome-induction strategies (s, ααα, or F induction).

---

## New parameters

| Parameter | Value | Meaning |
|-----------|-------|---------|
| `sigma_A` | 0.5 | Baseline autophagy production rate |
| `alpha_A` | 3.0 | Induction strength (WT: how much F upregulates A) |
| `K_A` | 20.0 | Half-saturation for F-induction of autophagy |
| `delta_A` | 0.02 | Rate of fibril F degradation by autophagy |
| `tau_A` | 5.0 | Autophagy lifetime (autophagic flux timescale) |
| `delta_AC` | 0.5 | Fraction of complex C also cleared by autophagy |
| `K_inh` | 30.0 | Half-inhibition for mutant αSN scenario |
| `h_inh` | 2 | Hill coefficient for inhibition |

Baseline autophagy level (no fibrils): $A_0 = \sigma_A \cdot \tau_A = 2.5$  
At disease steady state (m=25, WT): $\langle A \rangle \approx 5.2$ — approximately **2.1× upregulated** by fibril accumulation.

---

## Key results

### Threshold shift

| Scenario | m_T (onset of oscillations) | Shift from base |
|----------|----------------------------|----------------|
| Base (no autophagy) | ≈ 9 | — |
| Basal autophagy (no induction, α_A=0) | ≈ 15 | +6 |
| **WT autophagy (induced by F)** | **> 50** | **+41 ← dominant** |
| Inhibited autophagy (mutant αSN) | ≈ 19 | +10 |

WT autophagy raises m_T by more than 5× compared to the base model, pushing the threshold well beyond any realistic αSN overexpression scenario.

### Comparison at m = 25 (disease state for base model)

| Scenario | ⟨1/P⟩ | F_mean | Oscillates? |
|----------|--------|--------|------------|
| No autophagy (base) | 4.52 | 83.6 | YES ★ |
| Basal only (no induction) | 1.08 | 13.4 | YES ★ |
| **WT autophagy (induced)** | **1.00** | **11.3** | **NO — steady state ✓** |
| Inhibited (mutant αSN) | 2.43 | 40.1 | YES ★ |

WT autophagy is the **only strategy that completely rescues the system to a non-oscillatory steady state** at m=25, outperforming all proteasome-induction strategies (which still oscillate, even if above the P=0.5 danger threshold).

### Comparison with all rescue strategies

| Strategy | ⟨1/P⟩ at m=25 | Oscillates? |
|----------|---------------|------------|
| Base model | 4.52 | YES |
| s-induction (α=2) | 0.45 | YES (above threshold) |
| ααα-induction (α=2) | 0.51 | YES (above threshold) |
| F-induction (α=2) | 1.47 | YES |
| **WT autophagy** | **1.00** | **NO — fixed point** |
| Inhibited autophagy | 2.43 | YES |

---

## Why WT autophagy is the most effective rescue

The key is the **dual mechanism**. When comparing to proteasome induction strategies:

**Induction strategies** (s, ααα, F): increase σ_eff → more P produced → P competes with F for binding. But the fibrils are still there accumulating, and P can still be sequestered.

**Autophagy**: physically removes F from the system AND breaks up existing C complexes, freeing P. It attacks the positive feedback from both ends simultaneously:

```
INDUCTION STRATEGIES:             AUTOPHAGY:
                                  
σ_eff ↑ → more P produced         A ↑ → F removed directly
      ↓                                 ↓
P available for fibril             C = [P:F] also degraded
degradation                              ↓
                                   P freed from sequestration
                                   BOTH mechanisms act together
```

---

## The mutant αSN scenario (Cuervo 2004)

For mutant αSN, autophagy is inhibited by fibril accumulation:

$$f(F) = \frac{1}{1 + (F/K_{inh})^h} \quad \xrightarrow{F \to \infty} \quad 0$$

This creates an **additional positive feedback loop**:

```
Fibrils F ↑  →  autophagy A ↓  →  less F clearance  →  F ↑↑
```

Combined with the existing Sneppen positive feedback (F sequesters P), the mutant cell now has **two interlocking positive feedback loops**, making disease much more likely. The threshold m_T ≈ 19 is worse than WT (>50) but slightly better than the base model (9) because baseline autophagy (A_0 = 2.5) still provides some protection at low fibril loads.

---

## Figures produced

| Figure | Description |
|--------|-------------|
| **Fig 1** | Trajectories of F(t), P(t), and A(t) for all four scenarios at m=25. WT autophagy shows convergence to steady state; inhibited shows sustained oscillations worse than base. |
| **Fig 2** | Threshold scan: ⟨1/P⟩ (log) and Lewy body burden vs m for all scenarios. WT autophagy curve is flat and low for m < 50; inhibited shifts onset to m≈19. |
| **Fig 3** | Autophagy dynamics: A(t) trajectories (WT vs inhibited), fibril comparison, and the induction curves f(F) for both scenarios. |
| **Fig 4** | δ_A sensitivity scan: ⟨1/P⟩ vs autophagy clearance rate, with and without complex clearance (δ_AC). Shows that complex clearance (releasing P from C) is essential — without it, autophagy is much less effective. |
| **Fig 5** | Phase portraits F vs P for all four scenarios: fixed point (healthy) vs limit cycle (disease). WT autophagy converts the limit cycle into a fixed point. |

---

## How to run

**Requirements:** Python 3, NumPy, SciPy (solve_ivp, find_peaks), Matplotlib.  
**NumPy note:** uses `np.trapz` directly — compatible with NumPy < 2.0 (standard Anaconda). If you use NumPy ≥ 2.0, replace with `np.trapezoid`.  
**Matplotlib note:** Fig 1 uses `layout="constrained"` instead of `tight_layout()` to avoid a UserWarning with GridSpec + add_subplot combinations. This is purely cosmetic — if you see the warning it does not affect the output.  

---

## Connection to lecture material

| Concept | Implementation |
|---------|---------------|
| Autophagy removes large aggregates (slides) | `delta_A · A · F` term in dF/dt |
| Autophagosome → lysosome → degradation | Modelled as first-order clearance with rate δ_A |
| PQC = chaperones + proteases + autophagy | Model now includes all three (P = UPS, A = autophagy) |
| Webb et al. 2003: αSN via both pathways | Both P and A clear F in the model |
| Cuervo et al. 2004: mutant αSN inhibits CMA | `f(F) = 1/(1+(F/K_inh)^h)` replaces induction |
| Positive feedback: Ai --|PQC (Krohn model) | Mutant scenario adds F --|A as second positive loop |
| UPR = PQC in ER (slides) | Analogous: autophagy is PQC for large cytoplasmic aggregates |

---

## References
- Ala Trusina. Lecture slides: *Physics of Molecular Diseases – Week 2, Protein Quality Control* (Niels Bohr Institute, 2020).
- Kim Sneppen et al. (2009). *Modeling proteasome dynamics in Parkinson's disease.* **Physical Biology**, 6, 036005.
- Webb, J.L. et al. (2003). Alpha-synuclein is degraded by both autophagy and the proteasome. *Journal of Biological Chemistry*, 278, 25009–25013.
- Cuervo, A.M. et al. (2004). Impaired degradation of mutant alpha-synuclein by chaperone-mediated autophagy. *Science*, 305, 1292–1295.
- Rubinsztein, D.C. et al. (2011). Autophagy and its possible roles in nervous system diseases, damage and repair. *Cell*, 146 — source of the autophagy diagram in the Week 2 biology slides.
