# Module 10: Hybrid DV-CV Systems

## Combining the Best of Both Worlds

This module explores hybrid approaches that leverage both discrete and continuous variable quantum systems.

---

## Learning Objectives

By the end of this module, you will:
- Understand motivations for hybrid DV-CV systems
- Know qubit-oscillator coupling schemes
- Master hybrid quantum algorithms
- Appreciate applications in quantum networking

---

## 10.1 Why Hybrid Systems?

### Complementary Strengths

```
┌─────────────────────────────────────────────────────────────┐
│              DV vs CV: COMPLEMENTARY FEATURES                │
├──────────────────────────┬──────────────────────────────────┤
│    DV STRENGTHS          │         CV STRENGTHS             │
├──────────────────────────┼──────────────────────────────────┤
│ Digital error correction │ Hardware-efficient encoding      │
│ Well-developed algorithms│ Deterministic operations         │
│ Universal gates clear    │ Efficient communication          │
│ Projective measurement   │ Room temperature (optical)       │
├──────────────────────────┼──────────────────────────────────┤
│    DV CHALLENGES         │         CV CHALLENGES            │
├──────────────────────────┼──────────────────────────────────┤
│ Probabilistic gates (LO) │ Non-Gaussian resources hard      │
│ High overhead for QEC    │ Infinite-dimensional space       │
│ Two-qubit gates costly   │ Measurement back-action          │
└──────────────────────────┴──────────────────────────────────┘

Hybrid approach: Use each where it excels!
```

### Natural Hybrid Platforms

```
1. Superconducting circuits
   - Transmon qubit (DV) + microwave cavity (CV)

2. Trapped ions
   - Electronic state (DV) + motional mode (CV)

3. Photonic systems
   - Single photon (DV) + optical mode (CV)

4. Spin-mechanical
   - Spin qubit (DV) + mechanical oscillator (CV)
```

---

## 10.2 Qubit-Oscillator Coupling

### Jaynes-Cummings Model

```
H = ωc â†â + ωq σz/2 + g(â†σ⁻ + âσ⁺)

ωc = cavity frequency
ωq = qubit frequency
g = coupling strength
σ± = qubit raising/lowering operators
```

**Regimes**:
```
Resonant (ωc = ωq):
- Energy exchange between qubit and cavity
- Vacuum Rabi oscillations

Dispersive (|ωc - ωq| >> g):
- Qubit-state-dependent cavity frequency
- QND measurement of qubit
- Controlled displacement
```

### Dispersive Regime

```
H_eff ≈ (ωc + χσz)â†â + (ωq/2)σz

χ = g²/(ωq - ωc)  (dispersive shift)

Cavity frequency shifts by ±χ depending on qubit state!
```

**Applications**:
- Qubit readout via cavity
- Photon number-dependent qubit phase
- Entangling gates

### Controlled Operations

**Conditional displacement**:
```
|0⟩|α⟩ + |1⟩|α⟩ → |0⟩|α + β⟩ + |1⟩|α - β⟩

Creates entanglement between qubit and oscillator
```

**SNAP gate** (Selective Number-dependent Arbitrary Phase):
```
SNAP(φ₀, φ₁, ...) = Σₙ e^(iφₙ)|n⟩⟨n| ⊗ I_qubit

Applies different phases to different Fock states
Universal for oscillator state preparation!
```

---

## 10.3 Bosonic Qubits with DV Ancillas

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                  HYBRID BOSONIC QUBIT                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│    ┌─────────┐         ┌─────────────┐                     │
│    │ Transmon│ ──────  │ Microwave   │                     │
│    │ (ancilla)│ g      │ Cavity      │                     │
│    └─────────┘         │ (bosonic    │                     │
│                        │  qubit)     │                     │
│                        └─────────────┘                     │
│                                                             │
│    Ancilla: Controls cavity operations                      │
│    Cavity: Stores quantum information                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Operations via Ancilla

**State preparation**:
```
Use ancilla + SNAP + displacement to create arbitrary cavity states

Example - Create |1⟩ (single photon):
1. Start: |g⟩|0⟩
2. π-pulse on qubit: |e⟩|0⟩
3. SWAP to cavity: |g⟩|1⟩
```

**Measurement**:
```
Photon number parity measurement:
1. Apply e^(iπâ†â) conditioned on qubit state
2. Measure qubit
3. Outcome reveals parity of photon number
```

### Error Correction Protocol

For cat qubit:
```
1. Encode: |ψ⟩ → α|+α⟩ + β|-α⟩

2. Error (photon loss): State jumps between even/odd sectors

3. Detect: Measure parity using ancilla qubit

4. Correct: Apply correction based on parity history
```

---

## 10.4 Hybrid Entanglement

### DV-CV Entangled States

**Schrödinger cat state**:
```
|ψ⟩ = (|0⟩|α⟩ + |1⟩|-α⟩)/√2

Qubit entangled with coherent states
Bell-like but with continuous part
```

**Hybrid cluster states**:
```
Mix of qubit and mode vertices
Enables hybrid MBQC
```

### Entanglement Measures

**Hybrid entropy**:
```
Trace out CV system → DV density matrix → von Neumann entropy
Or trace out DV system → CV density matrix
```

**Logarithmic negativity**:
```
Still works for hybrid systems
Compute partial transpose over one subsystem
```

### Creation Methods

```
1. Conditional displacement:
   |+⟩|0⟩ → CDISP → (|0⟩|α⟩ + |1⟩|-α⟩)/√2

2. Beam splitter + single photon:
   |1⟩|0⟩ → BS → (|1,0⟩ + |0,1⟩)/√2

3. Two-mode squeezing + qubit:
   Complex entangled states
```

---

## 10.5 Hybrid Quantum Algorithms

### Variational Quantum Eigensolver with Bosonic Modes

```python
# Pseudo-code for hybrid VQE
def hybrid_vqe(hamiltonian, n_qubits, n_modes):
    # Ansatz includes both qubit and oscillator parameters
    params_qubit = initialize_qubit_params()
    params_mode = initialize_mode_params()

    def ansatz(params_q, params_m):
        # Qubit rotations
        for p in params_q:
            apply_qubit_rotation(p)

        # Qubit-mode entangling
        for q, m in qubit_mode_pairs:
            apply_conditional_displacement(q, m)

        # Mode operations (squeezing, displacement)
        for p in params_m:
            apply_mode_operation(p)

    def cost(params):
        state = ansatz(params[:n_q], params[n_q:])
        return measure_hamiltonian(state, hamiltonian)

    # Optimize
    result = minimize(cost, initial_params)
    return result
```

### Quantum Simulation with Hybrid Systems

**Simulating spin-boson models**:
```
H = ωâ†â + (ε/2)σz + g·σx(â + â†)

Natural for hybrid hardware!
No encoding overhead needed
```

**Molecular simulation**:
```
Electrons → Qubits
Vibrations → Oscillator modes

More efficient than pure qubit encoding
```

### Hybrid Machine Learning

```
Quantum neural network with hybrid layers:

Input → [CV layer] → [DV layer] → [Entangling] → Output
        squeez/disp   rotations    qb-mode

Potential for richer function spaces
```

---

## 10.6 Quantum Networking

### CV-DV Transduction

Converting between encodings for networking:

```
┌─────────────────────────────────────────────────────────────┐
│                  QUANTUM TRANSDUCTION                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Superconducting        Optical                             │
│  (microwave)     →      (telecom)                          │
│                                                             │
│  Methods:                                                   │
│  - Electro-optic coupling                                   │
│  - Optomechanical intermediary                              │
│  - Direct frequency conversion                              │
│                                                             │
│  Challenge: Added noise, limited efficiency                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Hybrid Repeaters

```
For long-distance quantum communication:

Node 1         Fiber         Node 2
[DV memory] ─────────────── [DV memory]
     │                           │
     └── CV link (telecom) ─────┘

DV: Good for storage, error correction
CV: Good for transmission, detection
```

### Entanglement Distribution

```
Protocol:
1. Generate CV entanglement (two-mode squeezed state)
2. Transmit one mode over fiber
3. Convert to DV at receiver (via photon subtraction)
4. Heralded DV entanglement

Advantages:
- Deterministic CV generation
- Efficient CV transmission
- DV storage and processing
```

---

## 10.7 Experimental Platforms

### Circuit QED

```
State of the art (2024):
- Cavity lifetimes: ~1 ms
- Qubit T1: ~100 μs
- Dispersive shift χ: ~1 MHz
- Cat qubit lifetimes: approaching 1 ms

Companies: IBM, Google, Amazon, Yale-based startups
```

### Trapped Ions

```
Hybrid system:
- Electronic state: Qubit (|↑⟩, |↓⟩)
- Motional state: Oscillator (phonons)

Advantages:
- Excellent coherence
- All-to-all connectivity
- High-fidelity gates

Companies: IonQ, Quantinuum
```

### Photonics + Matter

```
Atom-light interfaces:
- Atomic ensemble + optical mode
- NV center + photonic crystal cavity
- Quantum dot + waveguide

Challenges:
- Interface efficiency
- Bandwidth matching
- Decoherence
```

---

## 10.8 Hybrid Error Correction

### GKP + Transmon

```
Physical setup:
┌──────────┐     ┌──────────────┐
│ Transmon │ ─── │ Cavity (GKP) │
└──────────┘     └──────────────┘

Error correction cycle:
1. Ancilla senses cavity error (dispersive coupling)
2. Apply correction displacement based on syndrome
3. Reset ancilla

Recent results: GKP qubits with ~300 μs lifetime
```

### Concatenated Hybrid Codes

```
Level 1: Bosonic code (GKP, cat) in cavity
Level 2: Surface code across multiple cavities

Benefits:
- Inner code handles analog errors
- Outer code handles residual digital errors
- Reduced overhead vs. pure qubit surface code
```

### Autonomous Error Correction

```
Engineered dissipation for passive protection:

Design Lindbladian L such that:
- Code space is steady state
- Errors are autonomously corrected
- No active measurement needed

Example: Two-photon driven cat qubit
```

---

## 10.9 Programming Hybrid Systems

### QuTiP Simulation

```python
import numpy as np
from qutip import *

# System parameters
N = 20  # Cavity Hilbert space truncation
wc = 1.0  # Cavity frequency
wq = 1.0  # Qubit frequency
g = 0.1   # Coupling strength

# Operators
a = tensor(destroy(N), qeye(2))       # Cavity annihilation
sm = tensor(qeye(N), destroy(2))      # Qubit lowering
sz = tensor(qeye(N), sigmaz())        # Qubit Z

# Jaynes-Cummings Hamiltonian
H = wc * a.dag() * a + wq/2 * sz + g * (a.dag() * sm + a * sm.dag())

# Initial state: qubit excited, cavity vacuum
psi0 = tensor(basis(N, 0), basis(2, 1))

# Time evolution
times = np.linspace(0, 25, 100)
result = mesolve(H, psi0, times, [], [a.dag()*a, sz])

# Plot vacuum Rabi oscillations
import matplotlib.pyplot as plt
plt.plot(times, result.expect[0], label='Cavity photons')
plt.plot(times, (result.expect[1]+1)/2, label='Qubit excited')
plt.legend()
plt.xlabel('Time')
plt.show()
```

### Conditional Displacement

```python
def conditional_displacement(N, alpha):
    """
    Create conditional displacement operator:
    |0><0| ⊗ D(alpha) + |1><1| ⊗ D(-alpha)
    """
    a = destroy(N)
    D_plus = displace(N, alpha)
    D_minus = displace(N, -alpha)

    proj_0 = tensor(qeye(N), basis(2,0) * basis(2,0).dag())
    proj_1 = tensor(qeye(N), basis(2,1) * basis(2,1).dag())

    return proj_0 * tensor(D_plus, qeye(2)) + proj_1 * tensor(D_minus, qeye(2))

# Create cat state
psi_plus = (basis(2,0) + basis(2,1)).unit()
psi_vac = tensor(basis(N, 0), psi_plus)

alpha = 2.0
CD = conditional_displacement(N, alpha)
psi_cat = CD * psi_vac

# This creates (|0⟩|α⟩ + |1⟩|-α⟩)/√2
```

### SNAP Gate

```python
def snap_gate(N, phases):
    """
    Selective Number-dependent Arbitrary Phase gate
    phases[n] = phase applied to |n⟩
    """
    op = sum([np.exp(1j * phases[n]) * basis(N, n) * basis(N, n).dag()
              for n in range(len(phases))])
    return op

# Example: Apply π phase to |1⟩ (creates sign flip for single photon)
phases = [0, np.pi, 0, 0, 0]  # phases for |0⟩, |1⟩, |2⟩, ...
S = snap_gate(N, phases)
```

---

## 10.10 Summary

### Hybrid Architecture Comparison

| Platform | Qubit | Oscillator | Coupling | Coherence |
|----------|-------|------------|----------|-----------|
| Circuit QED | Transmon | MW cavity | Dispersive | ~100 μs |
| Trapped ion | Electronic | Motional | Direct | ~1 s |
| Photonic | Polarization | Optical mode | Beamsplitter | μs-ms |
| Spin-mech | NV center | Mechanical | Strain | ~ms |

### Key Benefits of Hybrid

1. **Hardware efficiency**: Use oscillators for storage, qubits for control
2. **Error correction**: Bosonic codes with DV syndrome extraction
3. **Algorithm design**: Natural for certain Hamiltonians
4. **Networking**: CV for transmission, DV for processing

### Future Directions

- Scaling hybrid systems
- Improved qubit-oscillator interfaces
- Hybrid fault-tolerant protocols
- Modular quantum computing with hybrid nodes

---

## Exercises

### Theory Problems

1. Derive the dispersive Hamiltonian from Jaynes-Cummings in the limit |Δ| >> g.

2. Show that conditional displacement creates DV-CV entanglement from a product state.

3. Calculate the infidelity of a SNAP gate with finite dispersive shift.

4. Design a protocol to convert a GKP qubit to a transmon qubit state.

### Programming Exercises

1. Simulate vacuum Rabi oscillations and extract the coupling strength.

2. Create a cat state and compute its Wigner function.

3. Implement photon parity measurement using an ancilla qubit.

4. Simulate error correction for a cat qubit with photon loss.

---

## Next Module

[Module 11: Quantum Machine Learning →](../11-quantum-machine-learning/README.md)
