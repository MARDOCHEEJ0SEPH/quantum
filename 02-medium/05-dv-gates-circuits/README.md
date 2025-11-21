# Module 5: DV Gates and Circuits - Deep Dive

## Advanced Circuit Design and Gate Decomposition

This module explores discrete variable quantum circuits in depth, including gate synthesis, circuit optimization, and practical implementations.

---

## Learning Objectives

By the end of this module, you will:
- Master gate decomposition techniques
- Understand circuit identities and optimizations
- Implement controlled operations
- Work with ancilla qubits and measurement-based techniques

---

## 5.1 Gate Decomposition

### Single-Qubit Universality

Any single-qubit unitary U ∈ SU(2) can be decomposed:

**ZYZ Decomposition**:
```
U = e^(iα) Rz(β) Ry(γ) Rz(δ)

Finding parameters:
Given U = [a  b]
          [c  d]

α = arg(det(U))/2
β = 2·atan2(|b|, |a|)
etc.
```

**ZXZ Decomposition**:
```
U = e^(iα) Rz(β) Rx(γ) Rz(δ)
```

### Approximation with Discrete Gates

Using {H, T}:
```
Any Rz(θ) ≈ sequence of H and T gates

Solovay-Kitaev theorem:
ε-approximation needs O(log^c(1/ε)) gates
where c ≈ 3.97
```

**Example**: Approximate Rz(π/16)
```
T = Rz(π/4)
T† = Rz(-π/4)

Rz(π/16) ≈ HT†HT HTH... (specific sequence)
```

### Two-Qubit Gate Decomposition

**KAK Decomposition** (Cartan):
```
Any U ∈ SU(4):
U = (A₁ ⊗ A₂) · exp(i(αXX + βYY + γZZ)) · (B₁ ⊗ B₂)

where A₁, A₂, B₁, B₂ ∈ SU(2)
```

**CNOT Cost**:
```
Generic 2-qubit gate: 3 CNOTs
Special cases:
- SWAP: 3 CNOTs
- iSWAP: 2 CNOTs
- √SWAP: 2 CNOTs
- Controlled-U (diagonal U): 2 CNOTs
```

---

## 5.2 Important Circuit Identities

### Single-Qubit Identities

```
HXH = Z           HZH = X           HYH = -Y
SXS† = Y          SYS† = -X         SZS† = Z
TXT† = (X+Y)/√2   TZT† = Z

X = HZH           Y = SXS†          Z = HXH
```

### CNOT Identities

```
Flipping control/target:
──●──     ──H──●──H──
  │    =      │
──⊕──     ──H──⊕──H──

CNOT with X basis:
──●──     ──⊕──
  │    =    │     (when surrounded by H)
──⊕──     ──●──
```

### CZ Identities

```
CZ is symmetric:
──●──     ──●──
  │    =    │
──●──     ──●──

CZ from CNOT:
──●──     ────●────
  │    =      │
──●──     ──H─⊕─H──
```

### Commutation Rules

```
Gates on different qubits commute:
──A──────     ──────A──
     │     =       │
─────B───     ──B──────

CNOT commutation:
──●──X──  =  ──X──●──     (X commutes through control)
  │            │
──⊕─────     ─────⊕──

──●─────  =  ─────●──     (Z commutes through target)
  │            │
──⊕──Z──     ──Z──⊕──
```

---

## 5.3 Controlled Operations

### General Controlled-U

```
Control ──●──
          │
Target  ──U──

Matrix: |0⟩⟨0| ⊗ I + |1⟩⟨1| ⊗ U
```

### Decomposition of Controlled-U

If U = e^(iα) AXBXC where ABC = I:

```
──────────●────────────●────
          │            │
──A───X───B───X───C────────
      ↑       ↑
    from    from
   control  control
```

**Special case** - Controlled-Rz(θ):
```
──●──────●──
  │      │
──Rz(θ/2)──Rz(-θ/2)──
      ↑
    CNOT here
```

Actually:
```
────────●─────────●────
        │         │
──Rz(θ/2)─X─Rz(-θ/2)─X──
```

### Multi-Controlled Gates

**Toffoli (CCX)** decomposition:
```
──●─────────────●─────────●───●───T───●──
  │             │         │   │       │
──┼──●───────●──┼─────────┼───┼───────┼──
  │  │       │  │         │   │       │
──H──T──X──T†──X──T──X──T†──X──H──────────

6 CNOTs, 7 T gates (can be optimized)
```

**n-controlled NOT** with clean ancillas:
```
Uses O(n) Toffolis → O(n) CNOTs with ancilla
```

---

## 5.4 Circuit Optimization

### Gate Cancellation

```
──X──X──  =  ────────  (X² = I)
──H──H──  =  ────────  (H² = I)
──T──T──T──T──  =  ──S──  (T⁴ = Z, but T² = S)
```

### Gate Merging

```
──Rz(α)──Rz(β)──  =  ──Rz(α+β)──
──Rx(α)──Rx(β)──  =  ──Rx(α+β)──
```

### CNOT Optimization

```
Three CNOT patterns:
──●──●──  =  ────────
  │  │
──⊕──⊕──     ────────

──●─────●──     ─────●─────
  │     │    =      │
──⊕──●──⊕──     ──●──⊕──●──
     │              │     │
─────⊕─────     ────⊕─────⊕──
```

### Template Matching

Find and replace common patterns:
```
Template: ──T──H──T──H──  =  ──H──S──H──Rz(π/4)──

Optimization: Replace expensive sequences with cheaper equivalents
```

---

## 5.5 Measurement-Based Techniques

### Deferred Measurement

```
Principle: Measurements can be moved to the end

──M──●──     ──●──M──
     │    =    │
─────⊕──     ──⊕─────

If classical bit controls quantum gate,
use quantum control instead, measure later
```

### Measurement-Based Uncomputation

```
Compute-copy-uncompute pattern:

|0⟩──────┬──────────     Result
         │
|x⟩──Uf──●──Uf†──────    Restored to |x⟩
```

### Repeat-Until-Success

Some operations probabilistic:
```
while not success:
    prepare ancilla
    apply probabilistic gate
    measure ancilla
    if success: done
    else: reset and retry
```

---

## 5.6 Ancilla Qubits

### Clean vs Dirty Ancillas

**Clean ancilla**: Starts in |0⟩
```
|0⟩──[computation]──|0⟩   (returned to |0⟩)
```

**Dirty ancilla**: Unknown initial state
```
|garbage⟩──[computation]──|garbage⟩   (must restore)
```

### Toffoli with One Clean Ancilla

```
Original: CCNOT on qubits a, b, c

With ancilla |0⟩:
a ──●───────────────●──
    │               │
b ──┼──●─────────●──┼──
    │  │         │  │
|0⟩─⊕──●──●──────●──⊕──  (ancilla)
       │  │
c ─────⊕──X──X───⊕─────

Reduces Toffoli to 4 Toffolis of smaller size
```

### Uncomputing Ancillas

```
|0⟩──[compute f(x)]──|f(x)⟩──[uncompute f(x)]──|0⟩
          │                        │
|x⟩───────●────────────────────────●───────────|x⟩
```

---

## 5.7 Standard Circuit Constructions

### Quantum Fourier Transform (QFT)

```
For n qubits:

|j⟩ → (1/√2ⁿ) Σₖ e^(2πijk/2ⁿ) |k⟩

Circuit (n=3):
q₀ ──H──S──T──────────────╳──
        │  │              │
q₁ ─────●──┼──H──S────────┼──
           │     │        │
q₂ ────────●─────●──H─────╳──

Requires n(n-1)/2 controlled rotations + swaps
```

### Phase Estimation

```
|0⟩⊗ⁿ ──H⊗ⁿ──[controlled-U^(2^k)]──QFT†──M──
              │
|ψ⟩   ────────●─────────────────────────────

Estimates eigenvalue of U: U|ψ⟩ = e^(2πiφ)|ψ⟩
Output: n-bit approximation of φ
```

### Grover Diffusion Operator

```
D = 2|s⟩⟨s| - I   where |s⟩ = H⊗ⁿ|0⟩⊗ⁿ

Circuit:
──H──X──────●──────X──H──
            │
──H──X──────●──────X──H──
            │
──H──X──●───●───●──X──H──
        │       │
──H──X──Z───────Z──X──H──
```

---

## 5.8 Error Analysis

### Gate Errors

```
Ideal: U
Actual: U' = U + ε

Fidelity: F = |Tr(U†U')/d|²
```

### Error Propagation

```
Circuit with n gates, each with error ε:
Total error ≈ nε (first order)

For fault tolerance:
ε < threshold (≈ 1% for surface codes)
```

### Coherent vs Incoherent Errors

**Coherent** (systematic): Always same direction
```
U' = e^(iεH) U   (over/under rotation)
Errors can add constructively!
```

**Incoherent** (random): Different each time
```
ε(ρ) = (1-p)UρU† + p·noise
Errors add in quadrature: √n scaling
```

---

## 5.9 Practical Implementation

### Qiskit: Custom Gates

```python
from qiskit import QuantumCircuit
from qiskit.circuit import Gate
import numpy as np

# Define custom gate matrix
sqrt_x = np.array([[1+1j, 1-1j],
                   [1-1j, 1+1j]]) / 2

# Create custom gate
sqrt_x_gate = Gate('√X', 1, [])
sqrt_x_gate.definition = QuantumCircuit(1)
sqrt_x_gate.definition.unitary(sqrt_x, 0)

# Use in circuit
qc = QuantumCircuit(1)
qc.append(sqrt_x_gate, [0])
```

### Qiskit: Transpilation

```python
from qiskit import transpile
from qiskit_aer import AerSimulator

# Create circuit with high-level gates
qc = QuantumCircuit(3)
qc.ccx(0, 1, 2)  # Toffoli

# Transpile to basis gates
basis_gates = ['cx', 'u3']
transpiled = transpile(qc, basis_gates=basis_gates, optimization_level=3)

print(f"Original: {qc.count_ops()}")
print(f"Transpiled: {transpiled.count_ops()}")
```

### Qiskit: Circuit Analysis

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(3)
qc.h(0)
qc.cx(0, 1)
qc.cx(1, 2)
qc.barrier()
qc.measure_all()

# Analyze
print(f"Depth: {qc.depth()}")
print(f"Width: {qc.width()}")
print(f"Operations: {qc.count_ops()}")
print(f"Size: {qc.size()}")
```

---

## 5.10 Summary

### Gate Costs (in CNOTs)

| Gate | CNOT Cost | Notes |
|------|-----------|-------|
| Single-qubit | 0 | Native |
| CNOT | 1 | Native |
| SWAP | 3 | = 3 CNOTs |
| CZ | 1 | = H·CNOT·H |
| Toffoli | 6 | Optimal without ancilla |
| n-Toffoli | O(n) | With ancillas |
| General 2Q | ≤3 | KAK decomposition |

### Optimization Checklist

1. ✓ Cancel adjacent inverse gates
2. ✓ Merge consecutive rotations
3. ✓ Commute gates to enable cancellation
4. ✓ Use circuit identities
5. ✓ Consider ancilla-based decompositions
6. ✓ Match hardware connectivity

---

## Exercises

### Theory Problems

1. Decompose the SWAP gate into CNOTs. Prove your decomposition is correct.

2. Show that any controlled-U can be implemented with 2 CNOTs if U² = I.

3. Derive the circuit for controlled-Ry(θ).

4. Prove that CNOT count for arbitrary 2-qubit gates is at most 3.

### Programming Exercises

1. Implement the 3-qubit QFT from scratch (no qc.qft()).

2. Write a function to decompose any single-qubit gate into Rz-Ry-Rz.

3. Optimize this circuit to minimum CNOT count:
   ```
   qc.cx(0,1); qc.cx(1,2); qc.cx(0,1); qc.cx(1,2)
   ```

4. Implement Toffoli using only {H, T, CNOT}.

---

## Next Module

[Module 6: CV Gaussian Operations →](../06-cv-gaussian-operations/README.md)
