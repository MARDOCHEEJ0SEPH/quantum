# Module 9: Quantum Error Correction

## Protecting Quantum Information

This module covers quantum error correction for both discrete and continuous variable systems, including modern bosonic codes.

---

## Learning Objectives

By the end of this module, you will:
- Understand the need for quantum error correction
- Master stabilizer formalism and surface codes
- Learn CV-specific bosonic codes (GKP, cat codes)
- Appreciate fault-tolerant quantum computing

---

## 9.1 Why Error Correction is Different

### Classical vs Quantum Errors

```
┌─────────────────────────────────────────────────────────────┐
│                    ERROR COMPARISON                          │
├──────────────────────────┬──────────────────────────────────┤
│    CLASSICAL             │         QUANTUM                  │
├──────────────────────────┼──────────────────────────────────┤
│ Bit flip: 0 → 1          │ Bit flip: |0⟩ → |1⟩             │
│                          │ Phase flip: |0⟩+|1⟩ → |0⟩-|1⟩   │
│                          │ Continuous errors                │
├──────────────────────────┼──────────────────────────────────┤
│ Copy and compare         │ No cloning theorem!              │
│                          │ Measurement destroys state       │
├──────────────────────────┼──────────────────────────────────┤
│ Simple majority vote     │ Indirect syndrome measurement    │
└──────────────────────────┴──────────────────────────────────┘
```

### The No-Cloning Obstacle

Cannot copy quantum states:
```
|ψ⟩ → |ψ⟩|ψ⟩  is impossible for arbitrary |ψ⟩
```

Solution: Encode into **entangled states** of multiple qubits

### Continuous Errors

Quantum errors are continuous (rotations by any angle):
```
|ψ⟩ → e^(iθσ)|ψ⟩ for any θ

But! Syndrome measurement discretizes errors:
Small rotation → projects to "no error" or "full error"
```

---

## 9.2 Basic DV Error Correction

### Three-Qubit Bit-Flip Code

**Encoding**:
```
|0⟩ → |0_L⟩ = |000⟩
|1⟩ → |1_L⟩ = |111⟩

General: α|0⟩ + β|1⟩ → α|000⟩ + β|111⟩
```

**Error detection**:
```
Syndrome measurement via ancillas:
- Measure Z₁Z₂ (parity of qubits 1,2)
- Measure Z₂Z₃ (parity of qubits 2,3)

Syndrome table:
Z₁Z₂  Z₂Z₃  Error
 +1    +1   None
 -1    +1   Qubit 1
 -1    -1   Qubit 2
 +1    -1   Qubit 3
```

**Circuit**:
```
|ψ⟩ ──●──●──────────────────────
      │  │
|0⟩ ──⊕──┼──────────────────────
         │
|0⟩ ─────⊕──────────────────────
         Encoding
```

### Three-Qubit Phase-Flip Code

**Encoding**:
```
|0⟩ → |+⟩|+⟩|+⟩ = |+++⟩
|1⟩ → |-⟩|-⟩|-⟩ = |---⟩
```

**Syndrome**: Measure X₁X₂ and X₂X₃

### Nine-Qubit Shor Code

Concatenate bit-flip and phase-flip codes:
```
|0⟩ → (|000⟩ + |111⟩)(|000⟩ + |111⟩)(|000⟩ + |111⟩)/2√2
|1⟩ → (|000⟩ - |111⟩)(|000⟩ - |111⟩)(|000⟩ - |111⟩)/2√2

Corrects any single-qubit error (X, Y, or Z)
```

---

## 9.3 Stabilizer Formalism

### Stabilizer Group

A **stabilizer code** is defined by an abelian group S of Pauli operators:
```
For all g ∈ S and |ψ⟩ in code space:
g|ψ⟩ = |ψ⟩

Code space = +1 eigenspace of all stabilizers
```

### Generators

For n qubits encoding k logical qubits:
```
n - k independent stabilizer generators

[[n, k, d]] code:
- n physical qubits
- k logical qubits
- d minimum distance (corrects ⌊(d-1)/2⌋ errors)
```

### Example: [[5,1,3]] Code

Smallest code correcting any single-qubit error:
```
Generators:
g₁ = XZZXI
g₂ = IXZZX
g₃ = XIXZZ
g₄ = ZXIXZ

Logical operators:
X_L = XXXXX
Z_L = ZZZZZ
```

### Syndrome Measurement

```
For each generator gᵢ:
1. Prepare ancilla |0⟩
2. Apply controlled-gᵢ from ancilla
3. Measure ancilla in X basis

Outcome = eigenvalue of gᵢ (±1 → 0 or 1)
```

---

## 9.4 Surface Codes

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    SURFACE CODE LAYOUT                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│    ○───●───○───●───○                                       │
│    │   │   │   │   │     ○ = Data qubit                    │
│    ●───○───●───○───●     ● = Measure qubit                 │
│    │   │   │   │   │                                       │
│    ○───●───○───●───○     □ = Z stabilizer (plaquette)     │
│    │   │   │   │   │     ◇ = X stabilizer (vertex)        │
│    ●───○───●───○───●                                       │
│    │   │   │   │   │                                       │
│    ○───●───○───●───○                                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Stabilizers

**Z-type (plaquette)**:
```
    │
  ──●──
    │
Z₁Z₂Z₃Z₄ on four data qubits around plaquette
```

**X-type (vertex)**:
```
    │
  ──●──
    │
X₁X₂X₃X₄ on four data qubits around vertex
```

### Error Detection

```
X error on data qubit:
- Anticommutes with adjacent Z stabilizers
- Creates pair of -1 syndromes (defects)

Z error on data qubit:
- Anticommutes with adjacent X stabilizers
- Creates pair of -1 syndromes

String of errors → defects at endpoints only
```

### Decoding

**Minimum Weight Perfect Matching (MWPM)**:
```
1. Observe syndrome (defect locations)
2. Pair defects with minimum total distance
3. Apply corrections along pairing paths
```

**Threshold**: ~1% physical error rate

### Logical Operations

```
X_L: String of X operators from left to right boundary
Z_L: String of Z operators from top to bottom boundary

Distance d = code width
```

### Surface Code Cycle

```python
def surface_code_cycle(data_qubits, measure_qubits):
    """One round of surface code error correction"""
    # Reset measure qubits
    for m in measure_qubits:
        reset(m)

    # X stabilizers
    for x_stab in x_stabilizers:
        hadamard(x_stab.measure_qubit)
        for d in x_stab.data_qubits:
            cnot(x_stab.measure_qubit, d)
        hadamard(x_stab.measure_qubit)
        measure(x_stab.measure_qubit)

    # Z stabilizers
    for z_stab in z_stabilizers:
        for d in z_stab.data_qubits:
            cnot(d, z_stab.measure_qubit)
        measure(z_stab.measure_qubit)

    # Decode and correct
    syndrome = collect_measurements()
    correction = mwpm_decode(syndrome)
    apply_correction(correction)
```

---

## 9.5 CV Error Correction: Bosonic Codes

### Why Bosonic Codes?

```
Qubit encoded in oscillator (infinite-dimensional)
Errors in oscillator: photon loss, gain, dephasing

Advantages:
- Single physical system encodes qubit
- Hardware-efficient
- Natural for CV platforms
```

### GKP (Gottesman-Kitaev-Preskill) Code

**Ideal code states**:
```
|0_L⟩ = Σₙ |x = 2n√π⟩     (position at even multiples of √π)
|1_L⟩ = Σₙ |x = (2n+1)√π⟩ (position at odd multiples of √π)
```

**Wigner function**: Grid of peaks in phase space
```
      p
      ↑
    + ○ - ○ + ○ -
      ○ + ○ - ○ +      + = positive
    - ○ + ○ - ○ +      - = negative
      ○ - ○ + ○ -      ○ = peak location
    + ○ - ○ + ○ -
      └────────────→ x
        spacing = √π
```

**Logical operators**:
```
X_L = D(√π)      (displacement by √π in x)
Z_L = D(i√π)     (displacement by √π in p)
```

**Error correction**:
```
Small displacement δ in x or p:
1. Measure x mod √π and p mod √π
2. Correct by displacing back

Corrects displacements up to √π/2
```

**Finite-energy approximation**:
```
Physical GKP states have Gaussian envelope:
|GKP(Δ)⟩ ∝ Σₙ e^(-Δ²n²) D(n√π)|sq⟩

Δ = squeezing parameter (smaller = better code)
```

### Cat Codes

**Two-component cat**:
```
|0_L⟩ ∝ |α⟩ + |-α⟩  (even cat)
|1_L⟩ ∝ |α⟩ - |-α⟩  (odd cat)

α = coherent state amplitude (larger = more distinguishable)
```

**Phase space picture**:
```
      p
      ↑
      │     /\
      │    /  \
    ──●────────●──→ x
     -α        α

Cat states: superposition of coherent states
```

**Error correction**:
```
Dominant error: Single photon loss â

â|even cat⟩ ∝ |odd cat⟩
â|odd cat⟩ ∝ |even cat⟩

Detect photon loss by monitoring parity!
Parity jumps = error detected
```

**Four-component cat**:
```
|0_L⟩ ∝ |α⟩ + |-α⟩ + |iα⟩ + |-iα⟩
|1_L⟩ ∝ |α⟩ + |-α⟩ - |iα⟩ - |-iα⟩

Corrects both photon loss and dephasing
```

### Binomial Codes

```
|0_L⟩ = Σₖ cₖ|4k⟩
|1_L⟩ = Σₖ dₖ|4k+2⟩

Coefficients chosen for error correction
Corrects up to L photon losses
```

---

## 9.6 Fault-Tolerant Quantum Computing

### Fault-Tolerance Threshold

```
If physical error rate p < p_threshold:
Can compute arbitrarily accurately!

Surface code threshold: p_th ≈ 1%
Concatenated codes: p_th ≈ 10⁻⁴
```

### Logical Gate Implementations

**Transversal gates** (naturally fault-tolerant):
```
Apply same gate to each physical qubit
Error on one qubit can't spread to others

X_L = X⊗ⁿ
Z_L = Z⊗ⁿ
CNOT_L = CNOT⊗ⁿ (between two code blocks)
```

**Non-transversal gates** (require more care):
```
T gate, Toffoli gate
Methods:
- Magic state distillation
- Code switching
- Measurement-based
```

### Magic State Distillation

```
Noisy |T⟩ states → Distillation circuit → Cleaner |T⟩ states

|T⟩ = (|0⟩ + e^(iπ/4)|1⟩)/√2

Uses many noisy copies to produce fewer clean copies
Overhead: ~1000 physical qubits per logical T gate
```

### Resource Estimates

```
For practical quantum advantage (e.g., breaking RSA-2048):

Physical qubits: ~20 million
Physical error rate: ~10⁻³
Time: ~8 hours

Most resources go to T gates!
```

---

## 9.7 CV Fault Tolerance

### GKP + Surface Code

```
GKP qubits as building blocks for surface code:
- GKP corrects small (analog) errors
- Surface code corrects large (digital) errors
- Significantly reduces overhead
```

**Concatenation benefit**:
```
Without GKP: Need p < 1% physical error rate
With GKP: Can tolerate higher photon loss rates
         (~10% loss potentially correctable)
```

### CV Stabilizer Operations

```
CV Clifford gates (Gaussian operations):
- Preserve stabilizer structure
- Include: Displacement, Squeezing, Beamsplitter

For universality: Add GKP magic states
```

### Error Syndrome in GKP

```
Measure Sₓ = e^(i2√π p̂) and Sₚ = e^(-i2√π x̂)

Eigenvalue +1: No error (or error multiple of √π)
Eigenvalue -1: Error of √π/2 in that quadrature

Continuous syndromes give analog error information!
```

---

## 9.8 Implementation Examples

### Qiskit: Stabilizer Measurement

```python
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister

def bit_flip_code():
    """Three-qubit bit-flip code with syndrome measurement"""
    data = QuantumRegister(3, 'data')
    ancilla = QuantumRegister(2, 'ancilla')
    syndrome = ClassicalRegister(2, 'syndrome')

    qc = QuantumCircuit(data, ancilla, syndrome)

    # Encode |ψ⟩ (data[0] holds input)
    qc.cx(data[0], data[1])
    qc.cx(data[0], data[2])

    qc.barrier()

    # Introduce error (for testing)
    # qc.x(data[1])  # Bit flip on qubit 1

    qc.barrier()

    # Syndrome measurement
    # Measure Z₁Z₂
    qc.cx(data[0], ancilla[0])
    qc.cx(data[1], ancilla[0])

    # Measure Z₂Z₃
    qc.cx(data[1], ancilla[1])
    qc.cx(data[2], ancilla[1])

    qc.measure(ancilla, syndrome)

    return qc
```

### Strawberry Fields: GKP State

```python
import strawberryfields as sf
from strawberryfields import ops
import numpy as np

def approximate_gkp(delta=0.3, cutoff=50):
    """Create approximate GKP |0_L⟩ state"""
    prog = sf.Program(1)

    with prog.context as q:
        # Start with squeezed state
        ops.Squeezed(np.arccosh(1/delta)) | q[0]

        # Apply GKP preparation (simplified)
        # In practice, more complex preparation needed
        # This creates approximate grid state

    eng = sf.Engine("fock", backend_options={"cutoff_dim": cutoff})
    result = eng.run(prog)

    return result.state
```

### Surface Code Simulation

```python
import numpy as np

class SurfaceCode:
    def __init__(self, d):
        """Initialize distance-d surface code"""
        self.d = d
        self.data = np.zeros((d, d), dtype=int)
        self.x_syndrome = np.zeros((d-1, d), dtype=int)
        self.z_syndrome = np.zeros((d, d-1), dtype=int)

    def apply_error(self, error_rate):
        """Apply random Pauli errors"""
        for i in range(self.d):
            for j in range(self.d):
                if np.random.random() < error_rate:
                    # Random X, Y, or Z error
                    self.data[i, j] = np.random.choice([1, 2, 3])

    def measure_syndrome(self):
        """Measure stabilizer syndromes"""
        # Simplified syndrome calculation
        # Real implementation more complex
        pass

    def decode_mwpm(self):
        """Decode using minimum weight perfect matching"""
        # Use library like PyMatching
        pass
```

---

## 9.9 Summary

### Code Comparison

| Code | Type | Distance | Threshold | Overhead |
|------|------|----------|-----------|----------|
| [[7,1,3]] Steane | DV | 3 | ~10⁻⁴ | Low |
| Surface code | DV | d | ~1% | High |
| GKP | CV | continuous | ~10% loss | Medium |
| Cat code | CV | 2α | ~1% loss | Low |

### Choosing a Code

```
High error rate, DV hardware → Surface code
Low error rate, need efficiency → Steane/color codes
CV hardware → GKP or cat codes
Photon loss dominant → Cat codes
Dephasing dominant → GKP codes
```

### Key Concepts

1. **Stabilizer codes** define code space as simultaneous +1 eigenspace
2. **Surface codes** have high threshold but high overhead
3. **Bosonic codes** encode in oscillator modes
4. **GKP codes** use grid states, correct displacement errors
5. **Cat codes** use coherent state superpositions, correct photon loss
6. **Fault tolerance** requires error rate below threshold

---

## Exercises

### Theory Problems

1. Show that X and Z errors anticommute with different stabilizers in the surface code.

2. Calculate the logical error rate for concatenated codes.

3. Prove that the GKP stabilizers Sₓ and Sₚ commute.

4. Why can't cat codes correct dephasing with only two components?

### Programming Exercises

1. Implement syndrome measurement for the [[5,1,3]] code.

2. Simulate a surface code with noise and decode using MWPM.

3. Create an approximate GKP state and verify its Wigner function.

4. Implement parity monitoring for a cat qubit.

---

## Next Module

[Module 10: Hybrid DV-CV Systems →](../10-hybrid-dv-cv/README.md)
