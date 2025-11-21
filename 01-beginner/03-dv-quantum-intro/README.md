# Module 3: Introduction to Discrete Variable Quantum Computing

## Qubits, Gates, and Circuits

This module provides a comprehensive introduction to discrete variable (DV) quantum computing using qubits.

---

## Learning Objectives

By the end of this module, you will:
- Understand qubit representations
- Know the universal gate set
- Build basic quantum circuits
- Understand measurement and quantum parallelism

---

## 3.1 The Qubit

### Physical Realizations

A qubit is a two-level quantum system. Physical implementations include:

```
┌─────────────────────────────────────────────────────────────┐
│                    QUBIT IMPLEMENTATIONS                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Superconducting    Trapped Ion    Photonic    Spin        │
│  ┌───┐              ⊕              ↗↘          ↑↓          │
│  │   │ Josephson    Ground/        Polarization Electron   │
│  └───┘ junction     excited        or path      spin       │
│                                                             │
│  IBM, Google        IonQ           PsiQuantum   Intel      │
│  Rigetti            Quantinuum     Xanadu       QuTech     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Mathematical Description

**State Space**: 2-dimensional complex Hilbert space ℂ²

**Computational Basis**:
```
|0⟩ = [1]    |1⟩ = [0]
      [0]          [1]
```

**General Qubit State**:
```
|ψ⟩ = α|0⟩ + β|1⟩ = [α]
                     [β]

Constraints:
|α|² + |β|² = 1 (normalization)
α, β ∈ ℂ
```

### Bloch Sphere

Any qubit state can be written:
```
|ψ⟩ = cos(θ/2)|0⟩ + e^(iφ)sin(θ/2)|1⟩
```

**Bloch Sphere Visualization**:
```
                    |0⟩ (θ=0)
                     ↑
                     │  z
                     │
          ←─────────●─────────→ |+⟩ (θ=π/2, φ=0)
         |-⟩       /│         x
                  / │
                 /  │
                ↙   ↓
               y   |1⟩ (θ=π)

|+⟩ = (|0⟩ + |1⟩)/√2    (positive x)
|-⟩ = (|0⟩ - |1⟩)/√2    (negative x)
|+i⟩ = (|0⟩ + i|1⟩)/√2  (positive y)
|-i⟩ = (|0⟩ - i|1⟩)/√2  (negative y)
```

### Global Phase

States differing only by global phase are physically identical:
```
|ψ⟩ ≡ e^(iγ)|ψ⟩

Example: |0⟩ = e^(i0)|0⟩ = e^(iπ)|0⟩ = -|0⟩
```

This is why qubits have 2 real degrees of freedom (θ, φ), not 4.

---

## 3.2 Single-Qubit Gates

### Pauli Gates

**X Gate** (NOT gate, bit flip):
```
X = [0 1]    X|0⟩ = |1⟩
    [1 0]    X|1⟩ = |0⟩

Rotation: π around x-axis
```

**Y Gate**:
```
Y = [0 -i]   Y|0⟩ = i|1⟩
    [i  0]   Y|1⟩ = -i|0⟩

Rotation: π around y-axis
```

**Z Gate** (phase flip):
```
Z = [1  0]   Z|0⟩ = |0⟩
    [0 -1]   Z|1⟩ = -|1⟩

Rotation: π around z-axis
```

### Hadamard Gate

```
H = (1/√2)[1  1]
          [1 -1]

H|0⟩ = |+⟩ = (|0⟩ + |1⟩)/√2
H|1⟩ = |-⟩ = (|0⟩ - |1⟩)/√2

Properties:
- Creates superposition from basis states
- H² = I (self-inverse)
- HXH = Z, HZH = X (basis change)
```

### Phase Gates

**S Gate** (√Z):
```
S = [1 0]    S|0⟩ = |0⟩
    [0 i]    S|1⟩ = i|1⟩

S² = Z
```

**T Gate** (√S):
```
T = [1    0   ]    T|0⟩ = |0⟩
    [0  e^(iπ/4)]   T|1⟩ = e^(iπ/4)|1⟩

T² = S, T⁴ = Z
Critical for universal computation!
```

### Rotation Gates

**Rx(θ)**: Rotation around x-axis
```
Rx(θ) = cos(θ/2)I - i·sin(θ/2)X
      = [cos(θ/2)    -i·sin(θ/2)]
        [-i·sin(θ/2)  cos(θ/2)  ]
```

**Ry(θ)**: Rotation around y-axis
```
Ry(θ) = cos(θ/2)I - i·sin(θ/2)Y
      = [cos(θ/2)   -sin(θ/2)]
        [sin(θ/2)    cos(θ/2)]
```

**Rz(θ)**: Rotation around z-axis
```
Rz(θ) = cos(θ/2)I - i·sin(θ/2)Z
      = [e^(-iθ/2)    0     ]
        [   0      e^(iθ/2) ]
```

---

## 3.3 Multi-Qubit Gates

### CNOT (Controlled-NOT)

```
Control ──●──     CNOT|00⟩ = |00⟩
          │       CNOT|01⟩ = |01⟩
Target  ──⊕──     CNOT|10⟩ = |11⟩
                  CNOT|11⟩ = |10⟩

Matrix:
CNOT = [1 0 0 0]
       [0 1 0 0]
       [0 0 0 1]
       [0 0 1 0]

Action: Flips target if control is |1⟩
```

### CZ (Controlled-Z)

```
──●──     CZ|00⟩ = |00⟩
  │       CZ|01⟩ = |01⟩
──●──     CZ|10⟩ = |10⟩
          CZ|11⟩ = -|11⟩

Matrix:
CZ = [1 0 0  0]
     [0 1 0  0]
     [0 0 1  0]
     [0 0 0 -1]

Symmetric: control and target are interchangeable
```

### SWAP Gate

```
──✕──     SWAP|00⟩ = |00⟩
  │       SWAP|01⟩ = |10⟩
──✕──     SWAP|10⟩ = |01⟩
          SWAP|11⟩ = |11⟩

SWAP = [1 0 0 0]
       [0 0 1 0]
       [0 1 0 0]
       [0 0 0 1]

SWAP = CNOT₁₂ · CNOT₂₁ · CNOT₁₂
```

### Toffoli (CCNOT)

```
──●──
  │
──●──     If both controls are |1⟩,
  │       flip the target
──⊕──

CCNOT|110⟩ = |111⟩
CCNOT|111⟩ = |110⟩

Used for classical reversible computing
```

---

## 3.4 Universal Gate Sets

### What is Universality?

A gate set is **universal** if it can approximate any unitary operation to arbitrary precision.

### Common Universal Sets

```
1. {H, T, CNOT}  - Standard universal set
2. {H, S, CNOT, T}  - With S for Clifford completeness
3. {Rx, Ry, CNOT}  - Rotation-based
4. {Any entangling 2-qubit gate} + {all 1-qubit gates}
```

### Clifford vs Non-Clifford

**Clifford Gates**: {H, S, CNOT, X, Y, Z}
```
Properties:
- Map Pauli operators to Pauli operators
- Efficiently simulable classically (Gottesman-Knill)
- NOT universal alone!
```

**Non-Clifford Gates**: T, Toffoli, arbitrary rotations
```
Adding T to Clifford gates → Universal!
This is why T gates are expensive in fault-tolerant QC
```

---

## 3.5 Quantum Circuits

### Circuit Model

```
|0⟩ ──H──●──────M──
         │
|0⟩ ─────⊕──H───M──

Time flows left to right
```

### Reading a Circuit

1. Initial state (usually |0⟩⊗n)
2. Gates applied sequentially
3. Measurements at the end

### Bell State Preparation

```
|0⟩ ──H──●──     |Φ⁺⟩ = (|00⟩ + |11⟩)/√2
         │
|0⟩ ─────⊕──

Step by step:
|00⟩ → H⊗I → (|0⟩+|1⟩)/√2 ⊗ |0⟩ = (|00⟩+|10⟩)/√2
     → CNOT → (|00⟩+|11⟩)/√2 = |Φ⁺⟩
```

### GHZ State Preparation

```
|0⟩ ──H──●──●──     |GHZ⟩ = (|000⟩ + |111⟩)/√2
         │  │
|0⟩ ─────⊕──┼──
            │
|0⟩ ────────⊕──
```

---

## 3.6 Measurement

### Computational Basis Measurement

```
|ψ⟩ = α|0⟩ + β|1⟩ ──M──→ { |0⟩ with prob |α|²
                          { |1⟩ with prob |β|²
```

**Post-measurement state**: Collapses to the measured outcome

### Measurement in Other Bases

**X-basis measurement**:
```
|ψ⟩ ──H──M──

Measures in {|+⟩, |-⟩} basis
```

**General basis {|a⟩, |b⟩}**:
Apply unitary U where U|a⟩ = |0⟩, U|b⟩ = |1⟩, then measure in Z-basis

### Partial Measurement

Measuring one qubit of an entangled system:

```
|Φ⁺⟩ = (|00⟩ + |11⟩)/√2

Measure first qubit:
- Result 0 (prob 1/2): system collapses to |00⟩
- Result 1 (prob 1/2): system collapses to |11⟩

The second qubit is now determined!
```

---

## 3.7 Quantum Parallelism

### The Power of Superposition

A quantum computer can evaluate f(x) for all x simultaneously:

```
|0⟩⊗ⁿ ──H⊗ⁿ──  → (1/√2ⁿ) Σₓ |x⟩
                   │
              ──[Uf]──→ (1/√2ⁿ) Σₓ |x⟩|f(x)⟩
```

### The Catch

We cannot read all answers!
- Measurement collapses to a single random outcome
- Must use interference to extract useful information

### Deutsch-Jozsa Algorithm

**Problem**: Is f:{0,1}ⁿ → {0,1} constant or balanced?

```
|0⟩⊗ⁿ ──H⊗ⁿ──┌────┐──H⊗ⁿ──M──
              │ Uf │
|1⟩   ──H────└────┘────────

Classical: Requires 2^(n-1)+1 queries (worst case)
Quantum: Requires 1 query!
```

---

## 3.8 Programming DV Circuits

### Qiskit Example: Bell State

```python
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator
from qiskit.visualization import plot_histogram

# Create Bell state circuit
qc = QuantumCircuit(2, 2)
qc.h(0)           # Hadamard on qubit 0
qc.cx(0, 1)       # CNOT: control=0, target=1
qc.measure([0,1], [0,1])

# Draw the circuit
print(qc.draw())

# Simulate
simulator = AerSimulator()
compiled = transpile(qc, simulator)
result = simulator.run(compiled, shots=1000).result()
counts = result.get_counts()

print(counts)  # {'00': ~500, '11': ~500}
```

### Qiskit Example: Superposition

```python
from qiskit import QuantumCircuit

# Create equal superposition over 3 qubits
qc = QuantumCircuit(3, 3)
qc.h([0, 1, 2])  # Hadamard on all qubits
qc.measure_all()

print(qc.draw())
# All 8 outcomes (000 through 111) equally likely
```

### Qiskit Example: Custom Rotation

```python
from qiskit import QuantumCircuit
import numpy as np

qc = QuantumCircuit(1, 1)

# Rotate to prepare specific state
theta = np.pi / 3
qc.ry(theta, 0)  # |ψ⟩ = cos(θ/2)|0⟩ + sin(θ/2)|1⟩

qc.measure(0, 0)
```

---

## 3.9 Key Concepts Summary

### Gate Reference Table

| Gate | Matrix | Action | Circuit Symbol |
|------|--------|--------|----------------|
| X | σₓ | Bit flip | ─[X]─ |
| Y | σᵧ | Bit+phase flip | ─[Y]─ |
| Z | σᵤ | Phase flip | ─[Z]─ |
| H | (X+Z)/√2 | Create superposition | ─[H]─ |
| S | diag(1,i) | π/2 phase | ─[S]─ |
| T | diag(1,e^(iπ/4)) | π/4 phase | ─[T]─ |
| CNOT | Controlled-X | Entangle | ─●─⊕─ |
| CZ | Controlled-Z | Phase entangle | ─●─●─ |

### Important States

| State | Expression | Properties |
|-------|------------|------------|
| \|+⟩ | (|0⟩+|1⟩)/√2 | X eigenstate (+1) |
| \|-⟩ | (|0⟩-|1⟩)/√2 | X eigenstate (-1) |
| \|Φ⁺⟩ | (|00⟩+|11⟩)/√2 | Bell state |
| \|GHZ⟩ | (|0⟩⊗ⁿ+|1⟩⊗ⁿ)/√2 | Maximally entangled |

---

## Exercises

### Theory Questions

1. What state does the circuit H-S-H produce when starting from |0⟩?

2. Show that CNOT can be written as: CNOT = |0⟩⟨0| ⊗ I + |1⟩⟨1| ⊗ X

3. Prove that HZH = X using matrix multiplication.

4. How many real parameters are needed to describe an n-qubit pure state?

### Programming Exercises

1. Create a circuit that prepares the state (|0⟩ + i|1⟩)/√2

2. Implement the SWAP gate using only CNOT gates

3. Create all four Bell states and verify by measurement

4. Implement a circuit that prepares |ψ⟩ = cos(π/8)|0⟩ + sin(π/8)|1⟩

---

## Next Module

[Module 4: CV Quantum Introduction →](../04-cv-quantum-intro/README.md)
