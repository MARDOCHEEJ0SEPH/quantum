# Module 8: Photonic Implementations

## Light-Based Quantum Computing

This module explores photonic quantum computing, covering both discrete (single-photon) and continuous variable (optical mode) approaches.

---

## Learning Objectives

By the end of this module, you will:
- Understand photonic qubit encodings
- Know linear optical quantum computing (LOQC)
- Master measurement-based quantum computing
- Appreciate CV photonic systems

---

## 8.1 Why Photonics?

### Advantages

```
┌─────────────────────────────────────────────────────────────┐
│                  PHOTONIC ADVANTAGES                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ✓ Room temperature operation                               │
│  ✓ Low decoherence (photons don't interact much)           │
│  ✓ High-speed operations (THz clock rates possible)        │
│  ✓ Natural for quantum communication                        │
│  ✓ Frequency multiplexing for scalability                  │
│  ✓ Mature fabrication technology                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Challenges

```
┌─────────────────────────────────────────────────────────────┐
│                  PHOTONIC CHALLENGES                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ✗ Photon-photon interactions are weak                     │
│  ✗ Single photon sources are probabilistic                 │
│  ✗ Photon loss is significant                              │
│  ✗ Deterministic gates require nonlinearity or measurement │
│  ✗ Photon detection is destructive                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 8.2 Photonic Qubit Encodings

### Polarization Encoding

```
|0⟩ = |H⟩  (horizontal polarization)
|1⟩ = |V⟩  (vertical polarization)

Single-qubit gates: Wave plates (half-wave, quarter-wave)
Two-qubit gates: Require nonlinearity or measurement
```

**Operations**:
```
Half-wave plate at angle θ:
HWP(θ) ∝ [cos(2θ)   sin(2θ)]
         [sin(2θ)  -cos(2θ)]

X gate: HWP at 45°
H gate: HWP at 22.5°
```

### Dual-Rail Encoding

```
|0⟩ = |1,0⟩  (photon in mode a)
|1⟩ = |0,1⟩  (photon in mode b)

      mode a ─────────

      mode b ─────────

|0⟩: One photon in top rail
|1⟩: One photon in bottom rail
```

**Operations**:
```
Beamsplitter = Hadamard (up to phase)
Phase shifter = Rz gate
```

### Time-Bin Encoding

```
|0⟩ = |early⟩   (photon in early time bin)
|1⟩ = |late⟩    (photon in late time bin)

Useful for fiber-based communication
```

### Path Encoding (Multi-Rail)

```
|j⟩ = photon in path j

d-dimensional qudit using d paths
Naturally implements Hadamard via multiport
```

---

## 8.3 Linear Optical Elements

### Beamsplitter

```
        a_in                 a_out
          ↘   ┌─────┐   ↗
             │ BS  │
          ↗  └─────┘   ↘
        b_in                 b_out

Transformation:
[a_out]   [cos θ   i sin θ] [a_in]
[b_out] = [i sin θ  cos θ ] [b_in]

50:50 BS (θ = π/4):
[a_out]   1  [ 1  i] [a_in]
[b_out] = ── [ i  1] [b_in]
          √2
```

### Phase Shifter

```
──[φ]──

a_out = e^(iφ) a_in

Implemented with glass plates, electro-optic modulators
```

### Polarizing Beamsplitter (PBS)

```
Transmits H, reflects V

|H⟩ → transmitted
|V⟩ → reflected

Enables polarization-path conversion
```

### Interferometer

**Mach-Zehnder**:
```
    ┌────────────────────────────────┐
in →│ BS ─── [φ] ─── BS → out      │
    │    \         /                │
    │     \───────/                 │
    └────────────────────────────────┘

Output depends on relative phase φ
```

---

## 8.4 Linear Optical Quantum Computing (LOQC)

### KLM Protocol

Knill, Laflamme, Milburn (2001): Universal QC with linear optics + single photons + measurement

**Key insight**: Measurement-induced nonlinearity

### Nondeterministic CNOT

```
Success probability: 1/16 (basic), 1/4 (improved)

Requires:
- Two ancilla photons
- Beamsplitters
- Photon detection
- Classical feedforward
```

**Circuit concept**:
```
Control ──●──┬──────────●──
          │  │          │
Ancilla ──⊕──┼──D──D────⊕──
             │
Target  ─────⊕──────────────

D = Detection
Success heralded by specific detection pattern
```

### Boosting Success Probability

Using **quantum teleportation**:
```
Prepare entangled ancillas offline
Teleport through gate
Probability improved with more ancillas
```

### Resource Overhead

```
Original KLM: ~10⁴ photons per gate
Improved schemes: ~10² photons per gate
Photonic MBQC: More efficient
```

---

## 8.5 Measurement-Based Quantum Computing (MBQC)

### Cluster States

**Definition**: Highly entangled multi-qubit state
```
|cluster⟩ = ∏_{edges} CZ |+⟩⊗ⁿ
```

**1D cluster** (wire):
```
●─●─●─●─●   Each ● is |+⟩, each ─ is CZ

|cluster_1D⟩ = CZ₁₂ CZ₂₃ CZ₃₄ ... |+⟩⊗ⁿ
```

**2D cluster** (universal):
```
●─●─●─●
│ │ │ │
●─●─●─●
│ │ │ │
●─●─●─●
```

### Computation by Measurement

**Single-qubit rotation** on 1D cluster:
```
|ψ⟩ ─●─ M(θ)
     │
     ●  → Rz(θ)Hx · |ψ⟩  (up to Pauli correction)

M(θ) = measure in basis {|0⟩ ± e^(iθ)|1⟩}
```

**General rotation**:
```
Measure qubits 1, 2, 3 at angles θ₁, θ₂, θ₃
Implements: Rz(θ₃) Rx(θ₂) Rz(θ₁) |ψ_in⟩
```

**Two-qubit gates**: Naturally from 2D cluster structure

### MBQC vs Circuit Model

```
Circuit Model          MBQC
─────────────          ────
Gates applied    →     Measurements determine computation
sequentially
                       Cluster prepared offline
Need gate             Only single-qubit measurements
decomposition         needed online
```

### Photonic MBQC

Advantages for photonics:
- Entanglement generation can be probabilistic (offline)
- Computation only needs single-photon measurements
- Loss can be detected and handled

**Fusion operations**: Probabilistically connect small clusters
```
●─●  +  ●─●  → ●─●─●─●  (with probability p)
```

---

## 8.6 CV Photonic Quantum Computing

### Optical Modes

```
Each frequency/spatial mode is a harmonic oscillator

â, â† operators
Quadratures: x̂ = (â + â†)/√2, p̂ = i(â† - â)/√2
```

### Gaussian Operations (Easy)

```
┌─────────────────────────────────────────────────────────────┐
│              GAUSSIAN OPTICAL OPERATIONS                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Displacement    : Electro-optic modulator                  │
│  Phase rotation  : Delay line, phase plate                  │
│  Squeezing       : Optical parametric amplifier (OPA)       │
│  Beamsplitter    : Partially reflective mirror              │
│  Two-mode squeeze: Nonlinear crystal (SPDC)                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Non-Gaussian Operations (Hard)

```
Required for universality:
- Cubic phase gate: Very challenging
- Photon subtraction: Probabilistic
- GKP state preparation: Resource-intensive
```

### Squeezed Light Generation

**Optical Parametric Amplification (OPA)**:
```
Pump photon (2ω) → Two signal photons (ω each)

In degenerate case: Produces squeezed vacuum
χ⁽²⁾ nonlinearity in crystal (PPKTP, PPLN)
```

**Squeezing levels**:
```
3 dB:  Standard benchmark
10 dB: State of the art continuous wave
15 dB: Achieved in pulsed systems
```

---

## 8.7 Photonic Hardware Platforms

### Integrated Photonics

```
┌─────────────────────────────────────────────────────────────┐
│               PHOTONIC INTEGRATED CIRCUITS                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Silicon photonics:     CMOS compatible, telecom wavelength │
│  Silicon nitride:       Low loss, broad transparency        │
│  Lithium niobate:       High χ⁽²⁾ for sources/modulators   │
│  InP / GaAs:            Direct bandgap for sources          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Single Photon Sources

**Spontaneous Parametric Down-Conversion (SPDC)**:
```
Pump → Signal + Idler
Heralded: Detect idler, know signal exists
Probabilistic: Must multiplex for determinism
```

**Quantum Dots**:
```
On-demand single photon emission
High brightness, purity approaching 99%
Indistinguishability challenges
```

**Multiplexing**:
```
N probabilistic sources → Near-deterministic output
Spatial or temporal multiplexing
Switch photon to output when generated
```

### Photon Detectors

**Single Photon Avalanche Diodes (SPADs)**:
```
Efficiency: 60-70% at visible
Timing jitter: ~50 ps
Cannot resolve photon number
```

**Superconducting Nanowire (SNSPDs)**:
```
Efficiency: >95%
Timing jitter: <20 ps
Requires cryogenics (2-4 K)
```

**Transition Edge Sensors (TES)**:
```
Photon number resolving
Efficiency: >98%
Very slow (~μs)
Requires mK temperatures
```

---

## 8.8 Xanadu's Photonic Approach

### Borealis Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    BOREALIS STRUCTURE                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Squeezed light sources → Time-domain multiplexing          │
│                         → Programmable interferometer       │
│                         → Photon number detection           │
│                                                             │
│  216 squeezed modes                                         │
│  3 delay loops for connectivity                             │
│  Photon-number-resolving detectors                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Time-Domain Multiplexing

```
Single spatial mode, many temporal modes

... ─[pulse]─[pulse]─[pulse]─ ...
         t₁      t₂      t₃

Delay loops create entanglement between pulses:
Loop 1: Connects adjacent pulses
Loop 2: Connects pulses separated by N
Loop 3: Connects pulses separated by N²
```

### Programming with Strawberry Fields

```python
import strawberryfields as sf
from strawberryfields import ops

# Define a 4-mode GBS circuit
prog = sf.Program(4)

with prog.context as q:
    # Squeezed states
    for i in range(4):
        ops.Sgate(0.5) | q[i]

    # Interferometer (programmable)
    ops.Interferometer(unitary_matrix) | q

    # Photon number measurement
    ops.MeasureFock() | q

# Run on simulator or hardware
eng = sf.RemoteEngine("borealis")  # Xanadu hardware
results = eng.run(prog, shots=10000)
```

---

## 8.9 PsiQuantum's Approach

### Fusion-Based Quantum Computing (FBQC)

```
Resource states + Fusion operations → Computation

Key components:
1. Small entangled states (e.g., 4-photon GHZ)
2. Fusion measurements to connect them
3. Error correction integrated
```

### Resource State Generation

```
Generate small clusters offline
Multiplexing overcomes probabilistic generation
Manufacturing approach to scale
```

### Fusion Operations

```
Type-I Fusion: Probabilistic (50%)
Type-II Fusion: Boosted success rate

Failure → Erasure error (can be corrected)
```

### Silicon Photonics Manufacturing

```
CMOS foundry compatibility
GlobalFoundries partnership
Goal: 1M+ qubit system
```

---

## 8.10 Comparison: DV vs CV Photonic

| Aspect | DV Photonic | CV Photonic |
|--------|-------------|-------------|
| Information carrier | Single photons | Optical modes |
| Encoding | Polarization, path, time-bin | Quadratures |
| Entanglement | Probabilistic | Deterministic (Gaussian) |
| Gates | Measurement-based | Linear optics |
| Non-Gaussian | Natural (photon counting) | Challenging |
| Detection | SPDs, SNSPDs | Homodyne (efficient) |
| Error correction | Qubit codes | Bosonic codes (GKP) |
| Scalability | Fusion-based | Time-frequency multiplexing |

---

## 8.11 Programming Examples

### Dual-Rail Encoding

```python
from qiskit import QuantumCircuit
import numpy as np

def dual_rail_circuit():
    """Simulate dual-rail photonic qubit"""
    # Two modes: rail_a and rail_b
    qc = QuantumCircuit(2)

    # Start with photon in rail_a (|0⟩ = |1,0⟩)
    qc.x(0)  # Put "photon" in first rail

    # Beamsplitter = Hadamard-like
    # |1,0⟩ → (|1,0⟩ + |0,1⟩)/√2
    qc.h(0)
    qc.cx(0, 1)
    qc.h(0)

    return qc
```

### CV Teleportation

```python
import strawberryfields as sf
from strawberryfields import ops

def cv_teleportation():
    """CV quantum teleportation"""
    prog = sf.Program(3)  # modes: input, Alice, Bob

    with prog.context as q:
        # Input state (coherent state to teleport)
        ops.Coherent(1.0 + 0.5j) | q[0]

        # Create EPR pair (two-mode squeezed state)
        ops.S2gate(2.0) | (q[1], q[2])

        # Bell measurement: mix input with Alice's mode
        ops.BSgate() | (q[0], q[1])

        # Homodyne measurements
        ops.MeasureX | q[0]
        ops.MeasureP | q[1]

        # Displacement correction on Bob (classical communication)
        # In practice, apply D(x_meas + i*p_meas) to q[2]

    eng = sf.Engine("gaussian")
    result = eng.run(prog)
    return result
```

### Hong-Ou-Mandel Effect

```python
import strawberryfields as sf
from strawberryfields import ops

def hong_ou_mandel():
    """Hong-Ou-Mandel interference"""
    prog = sf.Program(2)

    with prog.context as q:
        # Two single photons
        ops.Fock(1) | q[0]
        ops.Fock(1) | q[1]

        # 50:50 beamsplitter
        ops.BSgate(np.pi/4) | (q[0], q[1])

        # Measure photon numbers
        ops.MeasureFock() | q

    eng = sf.Engine("fock", backend_options={"cutoff_dim": 4})
    result = eng.run(prog, shots=1000)

    # Expect |2,0⟩ and |0,2⟩, never |1,1⟩!
    return result.samples
```

---

## 8.12 Summary

### Photonic QC Roadmap

```
Current state (2024):
- GBS advantage demonstrated (Xanadu, China)
- Fusion-based prototypes (PsiQuantum)
- Integrated photonics maturing

Near term:
- Error-corrected logical qubits
- Useful GBS applications
- Memory integration

Long term:
- Fault-tolerant photonic QC
- Networking with atom-photon interfaces
- Distributed quantum computing
```

### Key Takeaways

1. **Photons excel at communication** but need tricks for computation
2. **Linear optics alone isn't enough** - need measurement or nonlinearity
3. **MBQC natural for photonics** - preparation offline, measurement online
4. **CV photonics offers determinism** but needs non-Gaussian resources
5. **Manufacturing approach** (PsiQuantum) vs **modular approach** (Xanadu)

---

## Exercises

### Theory Problems

1. Show that a beamsplitter implements a Hadamard-like transformation on dual-rail qubits.

2. Explain why the Hong-Ou-Mandel effect produces bunching (|2,0⟩ or |0,2⟩).

3. Calculate the squeezing needed for 10 dB noise reduction.

4. Why is the cubic phase gate difficult to implement optically?

### Programming Exercises

1. Simulate a Mach-Zehnder interferometer with variable phase.

2. Create a 4-photon GHZ state using fusion operations (simulated).

3. Implement CV teleportation and verify fidelity.

4. Simulate Gaussian boson sampling with 8 modes and analyze the output.

---

## Next Module

[Module 9: Error Correction →](../../03-advanced/09-error-correction/README.md)
