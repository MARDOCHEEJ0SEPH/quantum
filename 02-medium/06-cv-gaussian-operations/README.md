# Module 6: CV Gaussian Operations

## Symplectic Transformations and Gaussian Quantum Information

This module provides a deep dive into Gaussian operations in continuous variable quantum computing.

---

## Learning Objectives

By the end of this module, you will:
- Understand the symplectic formalism
- Master single-mode and multi-mode Gaussian operations
- Work with covariance matrices
- Analyze entanglement in Gaussian states

---

## 6.1 The Symplectic Framework

### Phase Space Formalism

For n modes, define the vector of quadrature operators:
```
ξ̂ = (x̂₁, p̂₁, x̂₂, p̂₂, ..., x̂ₙ, p̂ₙ)ᵀ
```

**Canonical Commutation Relations**:
```
[ξ̂ᵢ, ξ̂ⱼ] = iΩᵢⱼ

where Ω = ⊕ⁿ ω,  ω = [0  1]
                      [-1 0]

Full form:
Ω = [0  1  0  0  ...]
    [-1 0  0  0  ...]
    [0  0  0  1  ...]
    [0  0 -1  0  ...]
    [... ... ... ...]
```

### Gaussian States

A Gaussian state ρ is fully characterized by:
1. **Mean vector**: μ = ⟨ξ̂⟩
2. **Covariance matrix**: V_ij = ⟨{Δξ̂ᵢ, Δξ̂ⱼ}⟩/2

**Wigner Function**:
```
W(ξ) = exp(-½(ξ-μ)ᵀ V⁻¹ (ξ-μ)) / (πⁿ √det V)
```

### Uncertainty Principle

For valid quantum states:
```
V + iΩ/2 ≥ 0

Single mode: V₁₁V₂₂ - V₁₂² ≥ 1/4
```

---

## 6.2 Symplectic Transformations

### Definition

A **symplectic matrix** S preserves the symplectic form:
```
SΩSᵀ = Ω
```

### Properties

```
det(S) = 1
S⁻¹ = Ω Sᵀ Ω⁻¹
Sp(2n, ℝ) forms a group
```

### Gaussian Unitaries

Gaussian unitary U transforms:
```
U ξ̂ U† = S ξ̂ + d

where:
- S is symplectic (linear transformation)
- d is displacement vector
```

**Covariance matrix transforms**:
```
V → SVSᵀ
```

**Mean transforms**:
```
μ → Sμ + d
```

---

## 6.3 Single-Mode Operations

### Displacement D(α)

```
D(α) = exp(αâ† - α*â)

Symplectic matrix: S = I (identity)
Displacement: d = √2 (Re(α), Im(α))ᵀ

Effect:
μ → μ + d
V → V (unchanged)
```

### Rotation R(θ)

```
R(θ) = exp(iθ â†â)

Symplectic matrix:
S = [cos θ   sin θ ]
    [-sin θ  cos θ ]

Effect:
- Rotates phase space by angle θ
- V → SVSᵀ (rotates ellipse)
```

### Squeezing S(r, φ)

```
S(r, φ) = exp[r/2 (e^(-2iφ) â² - e^(2iφ) â†²)]

For φ = 0 (x-squeezing):
S = [e^(-r)   0   ]
    [  0    e^(r) ]

Effect on vacuum:
V = ½I → V = ½[e^(-2r)    0   ]
              [  0     e^(2r)]
```

### Single-Mode Decomposition

Any single-mode Gaussian unitary:
```
U = D(α) R(θ) S(r) R(φ)

Bloch-Messiah: Any Gaussian = Passive · Squeezing · Passive
```

---

## 6.4 Two-Mode Operations

### Beamsplitter BS(θ, φ)

```
BS(θ, φ) = exp[θ(e^(iφ) â†b̂ - e^(-iφ) âb̂†)]

Symplectic matrix (φ = 0):
S = [cos θ I    sin θ I ]
    [-sin θ I   cos θ I ]

where I is 2×2 identity

Mode transformation:
â → â cos θ + b̂ sin θ
b̂ → -â sin θ + b̂ cos θ
```

**50:50 Beamsplitter** (θ = π/4):
```
â → (â + b̂)/√2
b̂ → (-â + b̂)/√2
```

### Two-Mode Squeezing S₂(r)

```
S₂(r) = exp[r(â†b̂† - âb̂)]

Symplectic matrix:
S = [cosh(r)I    sinh(r)Z]
    [sinh(r)Z    cosh(r)I]

where Z = diag(1, -1)
```

**Effect on two vacuums**:
```
S₂(r)|0,0⟩ = (1/cosh r) Σₙ tanh^n(r) |n,n⟩

This creates the two-mode squeezed vacuum (TMSV)!
```

### Controlled Operations

**CX gate** (controlled displacement):
```
CX = exp(-i x̂₁ p̂₂)

S = [I  0  0  0]
    [0  I  I  0]
    [0  0  I  0]
    [0  0  0  I]

Effect: x₂ → x₂ + x₁ (adds position of mode 1 to mode 2)
```

**CZ gate** (controlled phase):
```
CZ = exp(i x̂₁ x̂₂)

Creates correlations between x quadratures
```

---

## 6.5 Covariance Matrix Calculus

### Standard Forms

**Vacuum (n modes)**:
```
V = ½ I₂ₙ
```

**Thermal state** (n̄ mean photons):
```
V = (n̄ + ½) I₂
```

**Coherent state** |α⟩:
```
V = ½ I₂
μ = √2 (Re α, Im α)ᵀ
```

**Squeezed vacuum** (r, φ=0):
```
V = ½ [e^(-2r)    0   ]
      [  0     e^(2r) ]
```

**Two-mode squeezed state**:
```
V = ½ [cosh(2r)I    sinh(2r)Z]
      [sinh(2r)Z    cosh(2r)I]
```

### Partial Trace

For bipartite state with covariance matrix:
```
V = [A  C]
    [Cᵀ B]

Reduced state of first system: V_A = A
```

### Purity

```
μ = 1/√det(V) for n modes

Pure state: det(V) = 1/4ⁿ
```

---

## 6.6 Gaussian Entanglement

### Separability Criterion (Simon)

For two modes with covariance matrix V, define partial transpose:
```
Ṽ = (σ_z ⊕ I) V (σ_z ⊕ I)

where σ_z = diag(1, -1)
```

**PPT criterion**: State is separable if Ṽ + iΩ/2 ≥ 0

For symmetric states: Equivalent to checking smallest symplectic eigenvalue ν̃₋ ≥ 1/2

### Logarithmic Negativity

```
E_N = max(0, -log₂(2ν̃₋))

where ν̃₋ = smallest symplectic eigenvalue of Ṽ
```

### Two-Mode Squeezed State Entanglement

```
For TMSV with squeezing r:
ν̃₋ = e^(-2r)/2

E_N = 2r / ln(2) ≈ 2.88r

More squeezing → more entanglement!
```

---

## 6.7 Gaussian Measurements

### Homodyne Detection

Measures quadrature x̂_θ = x̂ cos θ + p̂ sin θ

**Update rules** (for outcome x):
```
μ' = μ + V Πᵀ (ΠVΠᵀ)⁻¹ (x - Πμ)
V' = V - V Πᵀ (ΠVΠᵀ)⁻¹ Π V

where Π = (cos θ, sin θ) for single mode
```

### Heterodyne Detection

Measures both quadratures with added noise:
```
Equivalent to: Beamsplitter with vacuum → Homodyne on both outputs

POVM element: |α⟩⟨α|/π

Outcome: Complex number α
```

### Gaussian Measurement Preserves Gaussianity

If input is Gaussian, output (conditional) state is also Gaussian!

---

## 6.8 Williamson Normal Form

### Theorem

Any covariance matrix V can be written:
```
V = S D Sᵀ

where:
- S is symplectic
- D = diag(ν₁, ν₁, ν₂, ν₂, ..., νₙ, νₙ)
- νᵢ are symplectic eigenvalues
```

### Computing Symplectic Eigenvalues

```
Eigenvalues of |iΩV| come in pairs ±νₖ

Uncertainty principle: νₖ ≥ 1/2 for all k
```

### Purity and Entropy

```
Purity: μ = ∏ₖ (1/2νₖ)

von Neumann entropy:
S = Σₖ g(νₖ)

where g(x) = (x + ½)log(x + ½) - (x - ½)log(x - ½)
```

---

## 6.9 CV Clifford Gates

### Gaussian Operations as Cliffords

In CV, Gaussian operations play role analogous to Clifford gates in DV:
- Map Gaussian states to Gaussian states
- Map quadrature operators to quadrature operators
- Efficiently classically simulable!

### Universal CV Gate Set

**Gaussian gates** (CV Clifford):
- Displacement D(α)
- Rotation R(θ)
- Squeezing S(r)
- Beamsplitter BS(θ)

**Non-Gaussian gate** (for universality):
- Cubic phase: V(γ) = exp(iγ x̂³/3)

```
{D(α), S(r), R(θ), BS(θ), V(γ)} is universal for CV!
```

---

## 6.10 Programming Gaussian Operations

### Strawberry Fields: Gaussian Backend

```python
import strawberryfields as sf
from strawberryfields import ops

# Two-mode entangled state
prog = sf.Program(2)

with prog.context as q:
    ops.Sgate(1.0) | q[0]       # Squeeze mode 0
    ops.BSgate(np.pi/4) | (q[0], q[1])  # 50:50 BS

eng = sf.Engine("gaussian")
result = eng.run(prog)

# Access covariance matrix
cov = result.state.cov()
print("Covariance matrix:\n", cov)

# Access means
means = result.state.means()
print("Means:", means)
```

### Symplectic Operations Directly

```python
import numpy as np

def rotation_symplectic(theta):
    """Single-mode rotation symplectic matrix"""
    c, s = np.cos(theta), np.sin(theta)
    return np.array([[c, s], [-s, c]])

def squeezing_symplectic(r):
    """Single-mode squeezing symplectic matrix"""
    return np.diag([np.exp(-r), np.exp(r)])

def beamsplitter_symplectic(theta):
    """Two-mode beamsplitter symplectic matrix"""
    c, s = np.cos(theta), np.sin(theta)
    return np.block([
        [c * np.eye(2), s * np.eye(2)],
        [-s * np.eye(2), c * np.eye(2)]
    ])

# Apply to covariance matrix
V_vacuum = 0.5 * np.eye(2)
S_squeeze = squeezing_symplectic(1.0)
V_squeezed = S_squeeze @ V_vacuum @ S_squeeze.T
print("Squeezed variance:", V_squeezed)
```

### Computing Entanglement

```python
import numpy as np
from scipy.linalg import sqrtm

def symplectic_eigenvalues(V):
    """Compute symplectic eigenvalues of 2-mode covariance matrix"""
    n = len(V) // 2
    Omega = np.block([
        [np.zeros((n,n)), np.eye(n)],
        [-np.eye(n), np.zeros((n,n))]
    ])
    M = 1j * Omega @ V
    eigenvalues = np.linalg.eigvals(M)
    # Take positive imaginary parts
    return np.sort(np.abs(eigenvalues.real))[:n]

def logarithmic_negativity(V):
    """Compute log negativity for 2-mode state"""
    # Partial transpose
    V_pt = V.copy()
    V_pt[1, 1] *= -1  # Flip p1 sign
    V_pt[0, 1] *= -1
    V_pt[1, 0] *= -1

    nu = symplectic_eigenvalues(V_pt)
    nu_min = np.min(nu)

    if nu_min < 0.5:
        return -np.log2(2 * nu_min)
    return 0
```

---

## 6.11 Summary Tables

### Single-Mode Gaussian Operations

| Operation | Unitary | Symplectic S | Effect |
|-----------|---------|--------------|--------|
| Displacement | D(α) | I | Shifts mean |
| Rotation | R(θ) | Rot(θ) | Rotates phase space |
| Squeezing | S(r) | diag(e^(-r), e^r) | Squeezes x, stretches p |

### Two-Mode Gaussian Operations

| Operation | Effect | Creates Entanglement? |
|-----------|--------|----------------------|
| Beamsplitter | Mixes modes | No (on vacuum) |
| Two-mode squeeze | Correlates x₁-x₂, p₁+p₂ | Yes |
| CX | x₂ → x₂ + x₁ | Yes |
| CZ | Phase correlations | Yes |

### Important States

| State | Covariance Matrix | Properties |
|-------|-------------------|------------|
| Vacuum | ½I | Minimum uncertainty |
| Coherent | ½I | Displaced vacuum |
| Squeezed | ½diag(e^(-2r), e^(2r)) | Reduced x variance |
| TMSV | (cosh,sinh,sinh,cosh) | Entangled |

---

## Exercises

### Theory Problems

1. Verify that the beamsplitter matrix is symplectic.

2. Show that squeezing followed by rotation gives a general squeezed state.

3. Compute the covariance matrix of a thermal state with n̄ mean photons.

4. Prove that Gaussian operations preserve Gaussianity.

### Programming Exercises

1. Implement symplectic eigenvalue computation for arbitrary covariance matrices.

2. Create a two-mode squeezed state and verify its entanglement increases with r.

3. Simulate homodyne detection on a squeezed state and plot the measurement distribution.

4. Implement the CV CNOT gate and verify its action on coherent states.

---

## Next Module

[Module 7: Quantum Algorithms →](../07-quantum-algorithms/README.md)
