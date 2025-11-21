# Module 4: Introduction to Continuous Variable Quantum Computing

## Bosonic Modes, Quadratures, and Gaussian States

This module introduces continuous variable (CV) quantum computing using bosonic modes, with emphasis on photonic implementations.

---

## Learning Objectives

By the end of this module, you will:
- Understand the CV Hilbert space and quadrature operators
- Know creation/annihilation operators and Fock states
- Work with coherent and squeezed states
- Understand Gaussian operations and measurements

---

## 4.1 What is CV Quantum Computing?

### Paradigm Shift

```
┌─────────────────────────────────────────────────────────────┐
│         DISCRETE VARIABLE         CONTINUOUS VARIABLE       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   Information in:              Information in:              │
│   • Finite levels             • Infinite-dimensional modes  │
│   • |0⟩, |1⟩                  • Quadratures x̂, p̂           │
│                                                             │
│   Carriers:                    Carriers:                    │
│   • Superconducting qubits    • Optical modes (photons)     │
│   • Trapped ions              • Microwave cavities          │
│   • Spin systems              • Mechanical oscillators      │
│                                                             │
│   Native gates:               Native gates:                 │
│   • Pauli, Hadamard           • Displacement, Squeezing     │
│   • CNOT, Toffoli             • Beamsplitter, Rotation      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Why CV?

1. **Deterministic entanglement**: Gaussian states prepared without probabilistic heralding
2. **Room temperature**: Optical systems operate at ambient temperature
3. **High bandwidth**: Frequency multiplexing for scalability
4. **Native operations**: Some transformations are natural in CV
5. **Networking**: Light is ideal for quantum communication

---

## 4.2 The Quantum Harmonic Oscillator

### Classical Harmonic Oscillator

```
Position: x(t) = A cos(ωt + φ)
Momentum: p(t) = -mωA sin(ωt + φ)

Energy: E = (1/2)mω²x² + p²/(2m)
```

### Quantization

Promote x, p to operators x̂, p̂ with:
```
[x̂, p̂] = iℏ    (we set ℏ = 1)
```

**Hamiltonian**:
```
Ĥ = (1/2)(x̂² + p̂²) = ℏω(â†â + 1/2)
```

### Quadrature Operators

```
x̂ = (â + â†)/√2     (position quadrature)
p̂ = (â - â†)/(i√2)  (momentum quadrature)

More general quadrature at angle θ:
x̂_θ = x̂ cos(θ) + p̂ sin(θ)
```

---

## 4.3 Creation and Annihilation Operators

### Definition

```
â = (x̂ + ip̂)/√2      (annihilation operator)
â† = (x̂ - ip̂)/√2     (creation operator)

Inverse:
x̂ = (â + â†)/√2
p̂ = (â - â†)/(i√2)
```

### Commutation Relations

```
[â, â†] = 1
[â, â] = [â†, â†] = 0
```

### Number Operator

```
n̂ = â†â

n̂|n⟩ = n|n⟩   (eigenvalue equation)
```

---

## 4.4 Fock States

### Definition

**Fock states** |n⟩ are eigenstates of the number operator:
```
n̂|n⟩ = n|n⟩,  n = 0, 1, 2, ...
```

### Ladder Operations

```
â|n⟩ = √n |n-1⟩      (removes one quantum)
â†|n⟩ = √(n+1) |n+1⟩  (adds one quantum)

Special cases:
â|0⟩ = 0             (vacuum cannot be lowered)
â†|0⟩ = |1⟩          (create single photon from vacuum)
```

### Building Fock States

```
|n⟩ = (â†)ⁿ/√(n!) |0⟩

|1⟩ = â†|0⟩
|2⟩ = (â†)²/√2 |0⟩ = â†|1⟩/√2
|3⟩ = (â†)³/√6 |0⟩
```

### Properties

```
Orthonormality: ⟨m|n⟩ = δₘₙ
Completeness:   Σₙ |n⟩⟨n| = I
Energy:         ⟨n|Ĥ|n⟩ = ℏω(n + 1/2)
```

### Position Representation

```
ψₙ(x) = ⟨x|n⟩ = (mω/πℏ)^(1/4) · (1/√(2ⁿn!)) · Hₙ(√(mω/ℏ)x) · e^(-mωx²/2ℏ)

where Hₙ are Hermite polynomials
```

---

## 4.5 Coherent States

### Definition

**Coherent states** |α⟩ are eigenstates of the annihilation operator:
```
â|α⟩ = α|α⟩,  α ∈ ℂ
```

### Fock Expansion

```
|α⟩ = e^(-|α|²/2) Σₙ (αⁿ/√(n!)) |n⟩
```

### Properties

**Displacement from vacuum**:
```
|α⟩ = D(α)|0⟩

D(α) = exp(αâ† - α*â)  (displacement operator)
```

**Mean photon number**:
```
⟨n̂⟩ = |α|²
```

**Photon statistics** (Poissonian):
```
P(n) = |⟨n|α⟩|² = e^(-|α|²) |α|^(2n)/n!
```

**Quadrature means**:
```
⟨x̂⟩ = √2 Re(α)
⟨p̂⟩ = √2 Im(α)
```

**Minimum uncertainty**:
```
Δx · Δp = 1/2  (saturates Heisenberg bound)
Δx = Δp = 1/√2  (equal uncertainties)
```

### Phase Space Picture

```
      p
      ↑
      │    ●──────→ α = x₀ + ip₀
      │   /│
      │  / │ uncertainty
      │ ●  │ disk (radius 1/√2)
      │    │
      └────┼───────→ x
           │
     ⟨x⟩=√2 Re(α)
     ⟨p⟩=√2 Im(α)
```

---

## 4.6 Squeezed States

### Why Squeezing?

Coherent states have equal uncertainty in both quadratures. **Squeezed states** trade uncertainty between quadratures:

```
Coherent:  Δx = Δp = 1/√2

Squeezed (x-squeezed):
           Δx = e^(-r)/√2   (reduced)
           Δp = e^(r)/√2    (increased)

Still satisfies: Δx · Δp = 1/2
```

### Squeezing Operator

```
S(ζ) = exp[(ζ*â² - ζâ†²)/2]

where ζ = r·e^(iθ)
- r: squeezing parameter (magnitude)
- θ: squeezing angle
```

### Squeezed Vacuum

```
|r, θ⟩ = S(re^(iθ))|0⟩

Fock expansion (θ = 0):
|r, 0⟩ = (1/√cosh(r)) Σₙ (√((2n)!/n!) · (-tanh(r)/2)ⁿ |2n⟩

Only even photon numbers! (squeezed vacuum has 0, 2, 4, ... photons)
```

### Phase Space Picture

```
Coherent state:        Squeezed state (r>0, θ=0):
      p                      p
      ↑                      ↑
      │  ●──●                │  ───●───
      │ ●  ● ← circle       │  ●     ● ← ellipse
      │  ●──●                │  ───●───
      └────────→ x           └────────────→ x
      Δx = Δp               Δx < 1/√2 < Δp
```

### Applications

1. **Enhanced sensing**: Beat standard quantum limit
2. **CV quantum computing**: Non-Gaussian resource
3. **Gravitational wave detection**: LIGO uses squeezing
4. **Quantum communication**: Improved signal-to-noise

---

## 4.7 Gaussian States and Operations

### Gaussian States

A state is **Gaussian** if its Wigner function is Gaussian:
```
W(x, p) = (1/π√det(V)) exp[-½(ξ-μ)ᵀ V⁻¹ (ξ-μ)]

where:
- μ = (⟨x̂⟩, ⟨p̂⟩) is the mean vector
- V is the covariance matrix
- ξ = (x, p)
```

**Examples of Gaussian states**:
- Vacuum |0⟩
- Coherent states |α⟩
- Squeezed states |r, θ⟩
- Thermal states

### Covariance Matrix

```
V_ij = ⟨{Δξᵢ, Δξⱼ}⟩/2

where Δξᵢ = ξᵢ - ⟨ξᵢ⟩

For single mode:
V = [⟨Δx²⟩        ⟨{Δx,Δp}⟩/2]
    [⟨{Δx,Δp}⟩/2  ⟨Δp²⟩      ]
```

**Examples**:

Vacuum/Coherent:
```
V = (1/2)[1 0]
        [0 1]
```

Squeezed (x-squeezed, r):
```
V = (1/2)[e^(-2r)  0    ]
        [0        e^(2r)]
```

### Gaussian Operations

Operations that map Gaussian states to Gaussian states:

| Operation | Unitary | Effect |
|-----------|---------|--------|
| Displacement | D(α) | Shifts mean, preserves V |
| Rotation | R(θ) | Rotates phase space |
| Squeezing | S(ζ) | Squeezes/stretches ellipse |
| Beamsplitter | BS(θ,φ) | Mixes two modes |
| Two-mode squeeze | S₂(r) | Creates entanglement |

### Displacement

```
D(α) = exp(αâ† - α*â)

D(α)|β⟩ = |α + β⟩

Phase space: shifts by (√2 Re(α), √2 Im(α))
```

### Rotation (Phase Shift)

```
R(θ) = exp(iθ n̂) = exp(iθ â†â)

R(θ)|α⟩ = |αe^(iθ)⟩

Phase space: rotates by angle θ
```

### Beamsplitter

```
BS(θ) = exp[θ(â†b̂ - âb̂†)]

Transforms modes:
â → â cos(θ) + b̂ sin(θ)
b̂ → -â sin(θ) + b̂ cos(θ)

50:50 beamsplitter: θ = π/4
```

---

## 4.8 Measurements in CV

### Homodyne Detection

Measures a single quadrature x̂_θ:
```
x̂_θ = x̂ cos(θ) + p̂ sin(θ)

θ = 0: measure x̂ (position)
θ = π/2: measure p̂ (momentum)
```

**Outcome**: Continuous value x ∈ ℝ

**Implementation**: Mix signal with strong local oscillator (LO) on beamsplitter, detect intensity difference

```
Signal ──────┐
             ├── BS ──┬── Detector 1
LO (strong) ─┘        └── Detector 2
                           │
                     Subtract signals
                           ↓
                    x_θ measurement
```

### Heterodyne Detection

Measures both quadratures simultaneously:
```
Outcome: complex value α = (x + ip)/√2
```

**Trade-off**: Added noise (vacuum from unused port)
```
ΔxΔp = 1  (not 1/2, due to simultaneous measurement)
```

### Photon Number Measurement

Measures n̂ = â†â:
```
Outcome: n = 0, 1, 2, 3, ...
Destroys quantum state
```

**Implementation**: Photon-number-resolving (PNR) detectors

---

## 4.9 Non-Gaussian Operations

### Why Non-Gaussian?

Gaussian operations alone are efficiently classically simulable!

**For universal CV quantum computing**, need non-Gaussian elements:
- Cubic phase gate
- Photon subtraction
- Photon addition
- GKP state preparation

### Cubic Phase Gate

```
V(γ) = exp(iγ x̂³/3)

Non-Gaussian! Essential for universality.
Difficult to implement directly.
```

### Photon Subtraction

```
â|ψ⟩ → |ψ'⟩ (after renormalization)

Implementation: Low-reflectivity beamsplitter + single photon detection
```

Creates non-Gaussian states from Gaussian inputs:
```
Photon subtraction on squeezed vacuum → non-Gaussian "cat-like" state
```

---

## 4.10 Programming CV Circuits

### Strawberry Fields Example: Coherent State

```python
import strawberryfields as sf
from strawberryfields import ops
import numpy as np

# Create a coherent state |α=2⟩
prog = sf.Program(1)

with prog.context as q:
    ops.Coherent(2.0) | q[0]  # |α=2⟩
    ops.MeasureX | q[0]       # Homodyne measurement

eng = sf.Engine("gaussian")
result = eng.run(prog, shots=1000)

# x measurement should be centered around √2 * Re(α) = 2√2 ≈ 2.83
print("Mean x:", np.mean(result.samples))
print("Std x:", np.std(result.samples))  # Should be ~0.71 (= 1/√2)
```

### Strawberry Fields Example: Squeezing

```python
import strawberryfields as sf
from strawberryfields import ops
import numpy as np

prog = sf.Program(1)

with prog.context as q:
    ops.Squeezed(r=1.0) | q[0]  # Squeezed vacuum
    ops.MeasureX | q[0]

eng = sf.Engine("gaussian")
result = eng.run(prog, shots=1000)

# x-squeezed: Δx should be e^(-r)/√2 ≈ 0.26
print("Std x:", np.std(result.samples))
```

### Strawberry Fields Example: Two-Mode Entanglement

```python
import strawberryfields as sf
from strawberryfields import ops

prog = sf.Program(2)

with prog.context as q:
    # Two-mode squeezed vacuum (creates entanglement)
    ops.S2gate(1.0) | (q[0], q[1])

    ops.MeasureX | q[0]
    ops.MeasureX | q[1]

eng = sf.Engine("gaussian")
result = eng.run(prog, shots=1000)

# Measurements should be correlated!
import numpy as np
x0 = result.samples[:, 0]
x1 = result.samples[:, 1]
print("Correlation:", np.corrcoef(x0, x1)[0,1])  # Should be high!
```

---

## 4.11 Comparison: DV vs CV Operations

| DV Concept | CV Analog |
|------------|-----------|
| Qubit |0⟩, |1⟩ | Mode quadratures x̂, p̂ |
| Pauli X | Displacement D(α) |
| Pauli Z | Rotation R(θ) |
| Hadamard | Fourier transform F |
| CNOT | Controlled displacement (CX) |
| Bell state | Two-mode squeezed state |
| Clifford gates | Gaussian operations |
| T gate | Cubic phase gate |

---

## 4.12 Key Takeaways

1. **CV uses infinite-dimensional Hilbert spaces** based on harmonic oscillators
2. **Coherent states** are classical-like minimum uncertainty states
3. **Squeezed states** trade uncertainty between quadratures
4. **Gaussian operations** (displacement, squeezing, beamsplitter) preserve Gaussian states
5. **Non-Gaussian resources** needed for universal quantum computing
6. **Homodyne detection** measures single quadrature; heterodyne measures both

---

## Exercises

### Theory Questions

1. Calculate ⟨α|n̂|α⟩ and ⟨α|n̂²|α⟩. What is the variance (Δn)²?

2. Show that [D(α), D(β)] ≠ 0 in general. When do they commute?

3. Prove that the vacuum state is Gaussian with V = (1/2)I.

4. What is the covariance matrix after applying a beamsplitter to two vacuum modes?

### Programming Exercises

1. Create a coherent state and verify it has Poissonian photon statistics.

2. Create a squeezed state and measure both quadratures. Verify the uncertainty relation.

3. Implement Hong-Ou-Mandel interference: two photons on a 50:50 beamsplitter.

4. Create a two-mode squeezed state and verify the EPR-like correlations.

---

## Next Module

[Module 5: DV Gates and Circuits →](../../02-medium/05-dv-gates-circuits/README.md)
