# Module 2: Mathematical Foundations

## The Mathematics of Quantum Computing

This module builds the mathematical toolkit needed for both DV and CV quantum computing.

---

## Learning Objectives

By the end of this module, you will:
- Master linear algebra for quantum mechanics
- Understand Hilbert spaces and operators
- Work with density matrices
- Know phase space representations for CV

---

## 2.1 Linear Algebra Essentials

### Vector Spaces

A **vector space** V over complex numbers ℂ has:
- Addition: |u⟩ + |v⟩ ∈ V
- Scalar multiplication: α|v⟩ ∈ V for α ∈ ℂ

**Properties**:
```
Commutativity:     |u⟩ + |v⟩ = |v⟩ + |u⟩
Associativity:     (|u⟩ + |v⟩) + |w⟩ = |u⟩ + (|v⟩ + |w⟩)
Zero vector:       |u⟩ + |0⟩ = |u⟩
Inverse:           |u⟩ + (-|u⟩) = |0⟩
Distributivity:    α(|u⟩ + |v⟩) = α|u⟩ + α|v⟩
```

### Inner Product

The **inner product** ⟨·|·⟩: V × V → ℂ satisfies:
```
⟨u|v⟩ = ⟨v|u⟩*                    (conjugate symmetry)
⟨u|(α|v⟩ + β|w⟩) = α⟨u|v⟩ + β⟨u|w⟩   (linearity in second argument)
⟨u|u⟩ ≥ 0, with equality iff |u⟩ = 0  (positive definiteness)
```

**Norm**: ||ψ|| = √⟨ψ|ψ⟩

**Orthogonality**: |u⟩ ⊥ |v⟩ iff ⟨u|v⟩ = 0

### Orthonormal Bases

A set {|e₁⟩, |e₂⟩, ..., |eₙ⟩} is an **orthonormal basis** if:
```
⟨eᵢ|eⱼ⟩ = δᵢⱼ  (Kronecker delta)

Any state: |ψ⟩ = Σᵢ cᵢ|eᵢ⟩  where cᵢ = ⟨eᵢ|ψ⟩
```

**Completeness Relation**:
```
Σᵢ |eᵢ⟩⟨eᵢ| = I  (identity operator)
```

---

## 2.2 Hilbert Spaces

### Definition

A **Hilbert space** ℋ is a complete inner product space:
- Complete: every Cauchy sequence converges
- May be finite or infinite dimensional

### Finite-Dimensional (DV)

For n qubits: ℋ = ℂ^(2ⁿ)

```
Single qubit (n=1):  dim(ℋ) = 2
    Basis: {|0⟩, |1⟩}

Two qubits (n=2):    dim(ℋ) = 4
    Basis: {|00⟩, |01⟩, |10⟩, |11⟩}

Three qubits (n=3):  dim(ℋ) = 8
    Basis: {|000⟩, |001⟩, |010⟩, |011⟩, |100⟩, |101⟩, |110⟩, |111⟩}
```

### Infinite-Dimensional (CV)

For one mode: ℋ = L²(ℝ) (square-integrable functions)

**Position basis**: {|x⟩ : x ∈ ℝ}
```
⟨x|x'⟩ = δ(x - x')
∫ |x⟩⟨x| dx = I
```

**Momentum basis**: {|p⟩ : p ∈ ℝ}
```
⟨x|p⟩ = e^(ixp)/√(2π)  (Fourier transform relation)
```

**Fock basis**: {|n⟩ : n = 0, 1, 2, ...}
```
⟨n|m⟩ = δₙₘ
Σₙ |n⟩⟨n| = I
```

---

## 2.3 Operators and Matrices

### Linear Operators

An operator A: ℋ → ℋ is **linear** if:
```
A(α|u⟩ + β|v⟩) = αA|u⟩ + βA|v⟩
```

**Matrix representation**: In basis {|eᵢ⟩}:
```
Aᵢⱼ = ⟨eᵢ|A|eⱼ⟩
```

### Important Operators

**Hermitian (Self-adjoint)**: A = A†
```
Properties:
- Real eigenvalues
- Orthogonal eigenvectors
- Represent observables

Example: Pauli Z
Z = |0⟩⟨0| - |1⟩⟨1| = [1  0]
                       [0 -1]
Eigenvalues: +1, -1
Eigenvectors: |0⟩, |1⟩
```

**Unitary**: U†U = UU† = I
```
Properties:
- Preserve inner products: ⟨Uψ|Uφ⟩ = ⟨ψ|φ⟩
- Represent quantum gates

Example: Hadamard
H = (1/√2)[1  1]
          [1 -1]
H† = H, H² = I
```

**Projection**: P² = P = P†
```
Example: P₀ = |0⟩⟨0| = [1 0]
                       [0 0]
Represents measurement outcome
```

### Pauli Matrices

The Pauli matrices form a basis for 2×2 Hermitian matrices:

```
I = [1 0]    X = [0 1]    Y = [0 -i]    Z = [1  0]
    [0 1]        [1 0]        [i  0]        [0 -1]
```

**Properties**:
```
X² = Y² = Z² = I
XY = iZ,  YZ = iX,  ZX = iY
{σᵢ, σⱼ} = 2δᵢⱼI  (anticommutation)
[σᵢ, σⱼ] = 2iεᵢⱼₖσₖ  (commutation)
```

**Any 2×2 operator**: A = (1/2)Σᵤ Tr(σᵤA)σᵤ where σ₀ = I

---

## 2.4 Tensor Products

### Definition

For spaces ℋ₁ and ℋ₂, the tensor product ℋ₁ ⊗ ℋ₂ has:
```
dim(ℋ₁ ⊗ ℋ₂) = dim(ℋ₁) × dim(ℋ₂)
```

### States

```
|ψ⟩ ⊗ |φ⟩ = |ψ⟩|φ⟩ = |ψφ⟩

Example:
|0⟩ ⊗ |1⟩ = |01⟩ = [0]
                    [1]
                    [0]
                    [0]
```

### Operators

```
(A ⊗ B)(|ψ⟩ ⊗ |φ⟩) = (A|ψ⟩) ⊗ (B|φ⟩)

Matrix form (Kronecker product):
A ⊗ B = [a₁₁B  a₁₂B  ...]
        [a₂₁B  a₂₂B  ...]
        [...   ...   ...]
```

### Entanglement

A state |Ψ⟩ ∈ ℋ₁ ⊗ ℋ₂ is **separable** if:
```
|Ψ⟩ = |ψ⟩ ⊗ |φ⟩ for some |ψ⟩ ∈ ℋ₁, |φ⟩ ∈ ℋ₂
```

Otherwise, it is **entangled**.

**Schmidt Decomposition**: Any pure bipartite state:
```
|Ψ⟩ = Σᵢ λᵢ |uᵢ⟩ ⊗ |vᵢ⟩

where λᵢ ≥ 0 (Schmidt coefficients)
{|uᵢ⟩} orthonormal in ℋ₁
{|vᵢ⟩} orthonormal in ℋ₂
```

The number of non-zero λᵢ is the **Schmidt rank**. If rank = 1, the state is separable.

---

## 2.5 Density Matrices

### Pure vs Mixed States

**Pure state**: Complete quantum description
```
|ψ⟩ with density matrix ρ = |ψ⟩⟨ψ|
```

**Mixed state**: Statistical ensemble of pure states
```
ρ = Σᵢ pᵢ|ψᵢ⟩⟨ψᵢ| where Σᵢ pᵢ = 1
```

### Properties of Density Matrices

```
1. Hermitian:     ρ = ρ†
2. Positive:      ρ ≥ 0 (all eigenvalues ≥ 0)
3. Unit trace:    Tr(ρ) = 1
4. Purity:        Tr(ρ²) ≤ 1, with equality iff pure
```

### Examples

**Pure state** |+⟩ = (|0⟩ + |1⟩)/√2:
```
ρ = |+⟩⟨+| = (1/2)[1 1]
                  [1 1]
Tr(ρ²) = 1 (pure)
```

**Maximally mixed state**:
```
ρ = (1/2)I = (1/2)[1 0]
                  [0 1]
Tr(ρ²) = 1/2 (mixed)
```

### Partial Trace

For bipartite system ℋ_A ⊗ ℋ_B, the **reduced density matrix**:
```
ρ_A = Tr_B(ρ_AB) = Σⱼ (I_A ⊗ ⟨j|_B) ρ_AB (I_A ⊗ |j⟩_B)
```

**Example**: Bell state |Φ⁺⟩ = (|00⟩ + |11⟩)/√2
```
ρ_AB = |Φ⁺⟩⟨Φ⁺| = (1/2)(|00⟩⟨00| + |00⟩⟨11| + |11⟩⟨00| + |11⟩⟨11|)

ρ_A = Tr_B(ρ_AB) = (1/2)(|0⟩⟨0| + |1⟩⟨1|) = (1/2)I

The subsystem is maximally mixed despite the global state being pure!
This is a signature of entanglement.
```

---

## 2.6 Quantum Channels

### Kraus Representation

A quantum channel ε: ρ → ρ' can be written:
```
ε(ρ) = Σₖ Kₖ ρ Kₖ†

where Σₖ Kₖ†Kₖ = I (completeness)
```

**Examples**:

**Bit flip channel** (probability p):
```
K₀ = √(1-p) I
K₁ = √p X

ε(ρ) = (1-p)ρ + p XρX
```

**Depolarizing channel**:
```
ε(ρ) = (1-p)ρ + (p/3)(XρX + YρY + ZρZ)
```

**Amplitude damping** (decay to |0⟩):
```
K₀ = [1    0  ]    K₁ = [0  √γ]
     [0  √(1-γ)]         [0   0]
```

---

## 2.7 Phase Space (CV)

### Quadrature Operators

Position and momentum operators for CV:
```
x̂ = (â + â†)/√2
p̂ = (â - â†)/(i√2)

Commutator: [x̂, p̂] = i (ℏ = 1)
```

### Creation and Annihilation

```
â|n⟩ = √n |n-1⟩    (annihilation)
â†|n⟩ = √(n+1) |n+1⟩  (creation)

[â, â†] = 1
```

### Phase Space Representations

**Wigner Function**:
```
W(x, p) = (1/π) ∫ ⟨x+y|ρ|x-y⟩ e^(2ipy) dy

Properties:
- Real-valued
- Marginals give position/momentum distributions
- Can be negative (non-classical!)
```

**Coherent States**:
```
|α⟩ = e^(-|α|²/2) Σₙ (αⁿ/√n!) |n⟩

Properties:
- â|α⟩ = α|α⟩ (eigenstates of annihilation)
- Minimum uncertainty
- Wigner function: Gaussian centered at (Re(α)√2, Im(α)√2)
```

**Squeezed States**:
```
|r, φ⟩ = S(ζ)|0⟩ where ζ = re^(iφ)

S(ζ) = exp[(ζ*â² - ζâ†²)/2]

Properties:
- Reduced uncertainty in one quadrature
- Increased uncertainty in conjugate quadrature
- Essential for CV quantum computing
```

---

## 2.8 Important Mathematical Tools

### Spectral Decomposition

Any Hermitian operator H:
```
H = Σᵢ λᵢ|eᵢ⟩⟨eᵢ|

where λᵢ are eigenvalues, |eᵢ⟩ eigenvectors
```

### Operator Functions

For f(H) where H is Hermitian:
```
f(H) = Σᵢ f(λᵢ)|eᵢ⟩⟨eᵢ|
```

**Example**: e^(iHt) (time evolution)
```
e^(iHt) = Σᵢ e^(iλᵢt)|eᵢ⟩⟨eᵢ|
```

### Commutator Algebra

```
[A, B] = AB - BA

Baker-Campbell-Hausdorff formula:
e^A e^B = e^(A+B+[A,B]/2+...) if [A,[A,B]] = [B,[A,B]] = 0

Useful identity:
e^A B e^(-A) = B + [A,B] + [A,[A,B]]/2! + ...
```

---

## 2.9 Summary Tables

### Key Operators

| Operator | Symbol | Property | Physical Meaning |
|----------|--------|----------|------------------|
| Hermitian | H = H† | Real eigenvalues | Observable |
| Unitary | U†U = I | Preserves norm | Gate/evolution |
| Projection | P² = P | Idempotent | Measurement |
| Positive | ⟨ψ|A|ψ⟩ ≥ 0 | Non-negative expectation | Density matrix |

### CV Operators

| Operator | Action | Physical Meaning |
|----------|--------|------------------|
| â | â\|n⟩ = √n\|n-1⟩ | Remove photon |
| â† | â†\|n⟩ = √(n+1)\|n+1⟩ | Add photon |
| n̂ = â†â | n̂\|n⟩ = n\|n⟩ | Photon number |
| x̂ | Position | Quadrature |
| p̂ | Momentum | Conjugate quadrature |

---

## Exercises

### Mathematical Problems

1. Prove that eigenvalues of Hermitian operators are real.

2. Show that the tensor product of two unitary operators is unitary.

3. Calculate the partial trace of:
   ```
   ρ = (1/4)(|00⟩ + |01⟩ + |10⟩ + |11⟩)(⟨00| + ⟨01| + ⟨10| + ⟨11|)
   ```

4. Verify that |α⟩ = e^(-|α|²/2) Σₙ (αⁿ/√n!) |n⟩ is normalized.

5. Compute [x̂, p̂] using â and â†.

### Computational Exercises

1. Write code to compute the tensor product of two matrices.

2. Implement the partial trace operation.

3. Calculate the purity Tr(ρ²) for a random density matrix.

4. Plot the Wigner function of a coherent state |α = 2⟩.

---

## Next Module

[Module 3: DV Quantum Introduction →](../03-dv-quantum-intro/README.md)
