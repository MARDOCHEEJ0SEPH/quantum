# Module 1: Quantum Basics

## Introduction to Quantum Computing

Welcome to your quantum journey! This module introduces the fundamental concepts that underpin all quantum computing—both DV and CV.

---

## Learning Objectives

By the end of this module, you will:
- Understand classical vs quantum information
- Grasp superposition, interference, and entanglement
- Know why quantum computers are powerful
- Distinguish between DV and CV at a conceptual level

---

## 1.1 Classical vs Quantum Information

### Classical Bits

In classical computing, information is stored in **bits**:
- A bit is either `0` or `1`
- N bits can represent one of 2^N states
- Operations are deterministic

```
Classical bit: 0 ──OR── 1
               │       │
            definite state
```

### Quantum Information

Quantum systems can exist in **superposition**:
- A quantum bit (qubit) can be both 0 and 1 simultaneously
- A quantum mode can have continuous values
- Measurement collapses to a definite outcome

```
Quantum state:  |0⟩ ←──superposition──→ |1⟩
                        │
                   |ψ⟩ = α|0⟩ + β|1⟩
                        │
                measurement gives 0 or 1
                with probabilities |α|² and |β|²
```

---

## 1.2 The Three Pillars of Quantum Computing

### Pillar 1: Superposition

**Definition**: A quantum system can exist in multiple states simultaneously until measured.

**Classical Analogy** (imperfect): Imagine a coin spinning in the air—it's neither heads nor tails until it lands.

**Mathematical Expression**:
```
|ψ⟩ = α|0⟩ + β|1⟩

where:
- |α|² = probability of measuring 0
- |β|² = probability of measuring 1
- |α|² + |β|² = 1 (normalization)
```

**Why It Matters**: Superposition allows quantum computers to process multiple possibilities simultaneously.

### Pillar 2: Interference

**Definition**: Quantum amplitudes can add constructively or destructively, affecting measurement probabilities.

**Example**: The Hadamard operation on |0⟩ and |1⟩
```
H|0⟩ = (|0⟩ + |1⟩)/√2    (equal superposition)
H|1⟩ = (|0⟩ - |1⟩)/√2    (note the minus sign)

Apply H twice:
H·H|0⟩ = H·(|0⟩ + |1⟩)/√2
       = (H|0⟩ + H|1⟩)/√2
       = ((|0⟩+|1⟩) + (|0⟩-|1⟩))/2
       = |0⟩              (interference!)
```

**Why It Matters**: Algorithms use interference to amplify correct answers and cancel wrong ones.

### Pillar 3: Entanglement

**Definition**: Quantum systems can be correlated in ways impossible classically. Measuring one system instantly affects the other.

**Bell State Example**:
```
|Φ⁺⟩ = (|00⟩ + |11⟩)/√2

Properties:
- Measuring first qubit as 0 → second is definitely 0
- Measuring first qubit as 1 → second is definitely 1
- Individual qubits have no definite state before measurement
- Correlations cannot be explained by pre-shared randomness
```

**Why It Matters**: Entanglement enables exponential speedup and secure communication.

---

## 1.3 Why Quantum Computers?

### Computational Complexity

```
┌────────────────────────────────────────────────────────────┐
│                    COMPLEXITY CLASSES                       │
├────────────────────────────────────────────────────────────┤
│                                                            │
│   P: Problems solvable efficiently classically             │
│      (sorting, searching sorted lists)                     │
│                                                            │
│   NP: Problems verifiable efficiently                      │
│      (factoring, traveling salesman)                       │
│                                                            │
│   BQP: Problems solvable efficiently on quantum computer   │
│      (factoring, certain simulations)                      │
│                                                            │
│              P ⊆ BQP ⊆ PSPACE                              │
│                                                            │
│   Key insight: BQP contains problems not in P!             │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### Quantum Speedups

| Problem | Classical | Quantum | Speedup |
|---------|-----------|---------|---------|
| Factoring (N digits) | exp(N^(1/3)) | poly(N) | **Exponential** |
| Unstructured search | O(N) | O(√N) | **Quadratic** |
| Simulation of quantum systems | Exponential | Polynomial | **Exponential** |

### The Catch: Quantum Limitations

Not everything is faster quantumly:
- Reading data still takes time
- Extracting information requires measurement
- Quantum states are fragile (decoherence)
- Error correction has overhead

---

## 1.4 Two Paradigms: DV and CV

### Discrete Variable (DV)

**Physical Systems**:
- Superconducting circuits (IBM, Google)
- Trapped ions (IonQ, Quantinuum)
- Photonic qubits (PsiQuantum)
- Neutral atoms (QuEra)
- Spin qubits (Intel)

**Key Characteristics**:
```
• Finite-dimensional Hilbert space
• Computational basis: |0⟩, |1⟩
• Gates: discrete unitary operations
• Measurement: projective (definite outcomes)
```

**Advantages**:
- Well-developed theory
- Clear algorithmic framework
- Established error correction

### Continuous Variable (CV)

**Physical Systems**:
- Optical modes (Xanadu)
- Microwave cavities
- Mechanical oscillators
- Atomic ensembles

**Key Characteristics**:
```
• Infinite-dimensional Hilbert space
• Basis: position |x⟩ or Fock |n⟩
• Gates: often Gaussian operations
• Measurement: homodyne/heterodyne
```

**Advantages**:
- Deterministic entanglement generation
- Room-temperature operation (optical)
- Natural for communication

### Comparison at a Glance

```
           DV                          CV
    ┌─────────────┐            ┌─────────────┐
    │   Qubit     │            │   Mode      │
    │  |0⟩, |1⟩   │            │  |x⟩, |p⟩   │
    │             │            │             │
    │   ●         │            │  ∿∿∿∿∿∿∿    │
    │  /│\        │            │   wave      │
    │   │ Bloch   │            │   packet    │
    │   ● sphere  │            │             │
    └─────────────┘            └─────────────┘
      2D Hilbert                ∞D Hilbert
       space                     space
```

---

## 1.5 Quantum States in Dirac Notation

### Bra-Ket Notation

**Ket** |ψ⟩: Column vector (state)
```
|0⟩ = [1]    |1⟩ = [0]
      [0]          [1]
```

**Bra** ⟨ψ|: Row vector (conjugate transpose)
```
⟨0| = [1, 0]    ⟨1| = [0, 1]
```

**Inner Product** ⟨φ|ψ⟩: Scalar (overlap)
```
⟨0|0⟩ = 1    ⟨0|1⟩ = 0    ⟨1|1⟩ = 1
```

**Outer Product** |ψ⟩⟨φ|: Matrix (operator)
```
|0⟩⟨0| = [1 0]    |0⟩⟨1| = [0 1]
         [0 0]             [0 0]
```

### Multi-Qubit States

**Tensor Product**:
```
|00⟩ = |0⟩ ⊗ |0⟩ = [1]    |01⟩ = [0]    |10⟩ = [0]    |11⟩ = [0]
                   [0]            [1]            [0]            [0]
                   [0]            [0]            [1]            [0]
                   [0]            [0]            [0]            [1]
```

**Entangled State** (cannot be written as tensor product):
```
|Φ⁺⟩ = (|00⟩ + |11⟩)/√2 ≠ |a⟩ ⊗ |b⟩ for any |a⟩, |b⟩
```

---

## 1.6 Your First Quantum Program

### Using Qiskit (DV)

```python
# Install: pip install qiskit qiskit-aer

from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator

# Create a 2-qubit circuit
qc = QuantumCircuit(2, 2)

# Apply Hadamard to qubit 0
qc.h(0)

# Apply CNOT (controlled-X) from qubit 0 to qubit 1
qc.cx(0, 1)

# Measure both qubits
qc.measure([0, 1], [0, 1])

# Simulate
simulator = AerSimulator()
result = simulator.run(qc, shots=1000).result()
counts = result.get_counts()

print("Results:", counts)
# Expected: {'00': ~500, '11': ~500} (Bell state!)
```

### Using Strawberry Fields (CV)

```python
# Install: pip install strawberryfields

import strawberryfields as sf
from strawberryfields import ops

# Create a 2-mode program
prog = sf.Program(2)

with prog.context as q:
    # Squeeze mode 0
    ops.Sgate(1.0) | q[0]

    # Apply 50:50 beamsplitter
    ops.BSgate() | (q[0], q[1])

    # Measure x-quadrature of both modes
    ops.MeasureX | q[0]
    ops.MeasureX | q[1]

# Run simulation
eng = sf.Engine("gaussian")
result = eng.run(prog)

print("Mode 0 x-measurement:", result.samples[0])
print("Mode 1 x-measurement:", result.samples[1])
```

---

## 1.7 Key Takeaways

1. **Quantum information** differs fundamentally from classical information
2. **Superposition** allows parallel processing of possibilities
3. **Interference** enables computation by amplitude manipulation
4. **Entanglement** provides correlations without classical analog
5. **DV systems** use discrete qubits; **CV systems** use continuous modes
6. Both paradigms have unique strengths for different applications

---

## Exercises

### Conceptual Questions

1. Explain why quantum computers cannot solve all NP problems efficiently.

2. A qubit is in state |ψ⟩ = (3|0⟩ + 4i|1⟩)/5. What is the probability of measuring 0? What about 1?

3. Why can't we clone a quantum state? (Hint: consider linearity)

4. Describe one advantage of CV over DV quantum computing.

### Programming Exercises

1. Create a circuit that prepares the state |+⟩ = (|0⟩ + |1⟩)/√2 and measure it 1000 times.

2. Create a 3-qubit GHZ state: (|000⟩ + |111⟩)/√2

3. (CV) Create a coherent state |α⟩ with α = 2 and measure its photon statistics.

---

## Next Module

[Module 2: Mathematical Foundations →](../02-mathematical-foundations/README.md)
