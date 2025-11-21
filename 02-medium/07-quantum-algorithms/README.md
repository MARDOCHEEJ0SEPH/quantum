# Module 7: Quantum Algorithms

## From Theory to Implementation

This module covers quantum algorithms in both DV and CV paradigms, from foundational algorithms to modern variational methods.

---

## Learning Objectives

By the end of this module, you will:
- Implement foundational quantum algorithms
- Understand algorithmic speedups
- Work with variational quantum algorithms
- Know CV-specific algorithms like Gaussian Boson Sampling

---

## 7.1 Algorithm Classification

### Quantum Speedup Types

```
┌─────────────────────────────────────────────────────────────┐
│                    SPEEDUP CATEGORIES                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  EXPONENTIAL: Quantum solves in poly time,                  │
│               classical requires exp time                   │
│               Examples: Factoring, simulation               │
│                                                             │
│  POLYNOMIAL:  Both poly time, but quantum faster            │
│               Examples: Grover (quadratic)                  │
│                                                             │
│  PROVABLE:    Quantum advantage proven                      │
│               Examples: Oracle problems (Simon's)           │
│                                                             │
│  HEURISTIC:   Believed faster, not proven                   │
│               Examples: QAOA, VQE                           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Algorithm Families

| Family | Examples | Key Technique |
|--------|----------|---------------|
| Oracle-based | Deutsch-Jozsa, Simon's, Grover's | Interference |
| Transform-based | Shor's, Phase estimation | QFT |
| Simulation | Trotter, VQE | Hamiltonian encoding |
| Sampling | Boson sampling, IQP | Hardness of sampling |
| Variational | VQE, QAOA | Classical-quantum hybrid |

---

## 7.2 Deutsch-Jozsa Algorithm

### Problem

Given f: {0,1}ⁿ → {0,1}, promised to be either:
- **Constant**: f(x) = 0 for all x, or f(x) = 1 for all x
- **Balanced**: f(x) = 0 for half of inputs, f(x) = 1 for the other half

Determine which case with certainty.

### Classical Complexity

Worst case: 2^(n-1) + 1 queries

### Quantum Solution

```
|0⟩⊗ⁿ ──H⊗ⁿ──┬────┬──H⊗ⁿ──M── → |0⟩⊗ⁿ iff constant
              │ Uf │
|1⟩   ──H────┴────┴──────────
```

**Analysis**:
```
Initial: |0⟩⊗ⁿ|1⟩

After H⊗(n+1):
(1/√2ⁿ) Σₓ |x⟩ ⊗ (|0⟩-|1⟩)/√2

After Uf (phase kickback):
(1/√2ⁿ) Σₓ (-1)^f(x) |x⟩ ⊗ (|0⟩-|1⟩)/√2

After final H⊗ⁿ on first register:
Amplitude of |0⟩⊗ⁿ = (1/2ⁿ) Σₓ (-1)^f(x)

If constant: amplitude = ±1
If balanced: amplitude = 0
```

### Implementation

```python
from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator

def deutsch_jozsa(oracle, n):
    """Run Deutsch-Jozsa on n-bit oracle"""
    qc = QuantumCircuit(n + 1, n)

    # Initialize
    qc.x(n)  # Set ancilla to |1⟩
    qc.h(range(n + 1))  # Hadamard all

    # Apply oracle
    qc.compose(oracle, inplace=True)

    # Final Hadamards and measure
    qc.h(range(n))
    qc.measure(range(n), range(n))

    # Run
    simulator = AerSimulator()
    result = simulator.run(qc, shots=1).result()
    counts = result.get_counts()

    # If all zeros, constant; otherwise balanced
    return "constant" if "0" * n in counts else "balanced"
```

---

## 7.3 Grover's Search Algorithm

### Problem

Given f: {0,1}ⁿ → {0,1} with f(x*) = 1 for unique x*, find x*.

### Classical vs Quantum

```
Classical: O(2ⁿ) queries (expected 2^(n-1))
Quantum:   O(√2ⁿ) = O(2^(n/2)) queries
```

### Algorithm Structure

```
|0⟩⊗ⁿ ──H⊗ⁿ──[Grover iteration]^k──M──

Grover iteration = Oracle · Diffusion

k ≈ (π/4)√N where N = 2ⁿ
```

### Oracle

```
Uf: |x⟩ → (-1)^f(x)|x⟩

Marks target state with negative phase
```

### Diffusion Operator

```
D = 2|s⟩⟨s| - I

where |s⟩ = H⊗ⁿ|0⟩⊗ⁿ = (1/√N) Σₓ |x⟩

Circuit:
──H──X──●──X──H──
        │
──H──X──●──X──H──
        │
──H──X──Z──X──H──  (multi-controlled Z)
```

### Geometric Interpretation

```
State space spanned by |s'⟩ (non-targets) and |t⟩ (target):

       |t⟩
        ↑
        │   /
        │  / |ψₖ⟩ after k iterations
        │ /
        │/θ
        ●────────→ |s'⟩
       |s⟩

Each iteration rotates by 2θ where sin(θ) = 1/√N
After k iterations: angle = (2k+1)θ
Optimal k: when (2k+1)θ ≈ π/2
```

### Implementation

```python
from qiskit import QuantumCircuit
import numpy as np

def grover(n, target):
    """Grover's algorithm for n qubits, target state"""
    N = 2**n
    iterations = int(np.pi/4 * np.sqrt(N))

    qc = QuantumCircuit(n, n)

    # Initialize superposition
    qc.h(range(n))

    for _ in range(iterations):
        # Oracle: flip target state
        target_bits = format(target, f'0{n}b')
        for i, bit in enumerate(reversed(target_bits)):
            if bit == '0':
                qc.x(i)
        qc.h(n-1)
        qc.mcx(list(range(n-1)), n-1)
        qc.h(n-1)
        for i, bit in enumerate(reversed(target_bits)):
            if bit == '0':
                qc.x(i)

        # Diffusion
        qc.h(range(n))
        qc.x(range(n))
        qc.h(n-1)
        qc.mcx(list(range(n-1)), n-1)
        qc.h(n-1)
        qc.x(range(n))
        qc.h(range(n))

    qc.measure(range(n), range(n))
    return qc
```

---

## 7.4 Quantum Fourier Transform

### Definition

```
QFT: |j⟩ → (1/√N) Σₖ e^(2πijk/N) |k⟩

Inverse of classical DFT matrix
```

### Circuit

For n qubits:
```
q₀ ──H──R₂──R₃──...──Rₙ──────────────────╳──
        │   │       │                    │
q₁ ─────●───┼───...─┼────H──R₂──...────╳─┼──
            │       │       │          │ │
q₂ ─────────●───...─┼───────●──...─────╳─╳──
                    │                  │
...                 │                  ...
                    │
qₙ₋₁ ───────────────●──────────────H───────

Rₖ = [[1, 0], [0, e^(2πi/2^k)]]
```

### Product Formula

```
QFT|j⟩ = (1/√N) ⊗ₗ (|0⟩ + e^(2πij/2ˡ)|1⟩)

Each qubit becomes superposition with phase determined by j
```

### Complexity

```
Gates: O(n²)
With approximate QFT: O(n log n)
```

### Implementation

```python
from qiskit import QuantumCircuit
import numpy as np

def qft(n):
    """Create n-qubit QFT circuit"""
    qc = QuantumCircuit(n)

    for i in range(n):
        qc.h(i)
        for j in range(i+1, n):
            qc.cp(np.pi / 2**(j-i), j, i)

    # Swap qubits for correct ordering
    for i in range(n // 2):
        qc.swap(i, n - i - 1)

    return qc
```

---

## 7.5 Shor's Algorithm

### Problem

Factor integer N into primes.

### Classical Complexity

Best known: exp(O(n^(1/3) log^(2/3) n)) for n-bit N

### Quantum Approach

Reduce to **order finding**: Given a coprime to N, find smallest r such that a^r ≡ 1 (mod N)

### Algorithm Outline

```
1. Choose random a < N coprime to N
2. Use quantum computer to find r = ord(a)
3. If r is even and a^(r/2) ≢ -1 (mod N):
   gcd(a^(r/2) ± 1, N) gives factors
4. Else repeat
```

### Quantum Order Finding

```
|0⟩⊗²ⁿ ──H⊗²ⁿ──┬───────┬──QFT†──M──
               │ U^(2ʲ) │
|1⟩⊗ⁿ  ────────┴───────┴───────────

U: |y⟩ → |ay mod N⟩

Measures approximation to s/r where U|u⟩ = e^(2πis/r)|u⟩
Use continued fractions to extract r
```

### Resource Estimates

```
For factoring n-bit N:
- 2n + 3 qubits (optimized)
- O(n³) gates
- Circuit depth O(n²)
```

---

## 7.6 Variational Quantum Eigensolver (VQE)

### Problem

Find ground state energy of Hamiltonian H.

### Variational Principle

```
E₀ ≤ ⟨ψ(θ)|H|ψ(θ)⟩

Minimize over parameters θ
```

### Algorithm

```
┌─────────────────────────────────────────────────────────────┐
│                    VQE HYBRID LOOP                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Classical:                    Quantum:                     │
│  ┌────────────┐                ┌────────────────┐          │
│  │ Optimizer  │──parameters──→ │ Ansatz U(θ)    │          │
│  │ (COBYLA,   │                │                │          │
│  │  SPSA)     │←───energy─────│ Measure ⟨H⟩    │          │
│  └────────────┘                └────────────────┘          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Ansatz Design

**Hardware-efficient ansatz**:
```
──Ry(θ₁)──Rz(θ₂)──●────────Ry(θ₅)──Rz(θ₆)──
                  │
──Ry(θ₃)──Rz(θ₄)──⊕────────Ry(θ₇)──Rz(θ₈)──

Repeat layers
```

**Problem-inspired ansatz** (e.g., UCCSD for chemistry):
```
|ψ⟩ = e^(T - T†)|HF⟩

T = singles + doubles excitations
```

### Implementation

```python
from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator
import numpy as np
from scipy.optimize import minimize

def create_ansatz(params, n_qubits, depth):
    """Hardware-efficient ansatz"""
    qc = QuantumCircuit(n_qubits)
    idx = 0
    for d in range(depth):
        for q in range(n_qubits):
            qc.ry(params[idx], q)
            qc.rz(params[idx+1], q)
            idx += 2
        for q in range(n_qubits - 1):
            qc.cx(q, q+1)
    return qc

def measure_hamiltonian(qc, hamiltonian_terms):
    """Measure expectation value of Hamiltonian"""
    # Sum contributions from each Pauli term
    # (simplified - actual implementation more complex)
    pass

def vqe(hamiltonian, n_qubits, depth):
    """Run VQE"""
    n_params = 2 * n_qubits * depth

    def cost(params):
        qc = create_ansatz(params, n_qubits, depth)
        return measure_hamiltonian(qc, hamiltonian)

    x0 = np.random.randn(n_params) * 0.1
    result = minimize(cost, x0, method='COBYLA')
    return result.fun  # Ground state energy estimate
```

---

## 7.7 Quantum Approximate Optimization Algorithm (QAOA)

### Problem

Solve combinatorial optimization: max/min C(x) for x ∈ {0,1}ⁿ

### Ansatz

```
|ψ(γ,β)⟩ = U_B(β_p) U_C(γ_p) ... U_B(β₁) U_C(γ₁) |+⟩⊗ⁿ

U_C(γ) = e^(-iγC)    (cost unitary)
U_B(β) = e^(-iβB)    (mixer unitary)

B = Σᵢ Xᵢ (standard mixer)
```

### For MaxCut

Cost function:
```
C = Σ_{(i,j)∈E} (1 - ZᵢZⱼ)/2

U_C(γ) = ∏_{(i,j)∈E} e^(-iγ(1-ZᵢZⱼ)/2)
       = ∏_{(i,j)∈E} CNOT · Rz(γ) · CNOT
```

### Implementation

```python
from qiskit import QuantumCircuit
import numpy as np

def qaoa_maxcut(graph_edges, n_qubits, p, gamma, beta):
    """QAOA circuit for MaxCut"""
    qc = QuantumCircuit(n_qubits)

    # Initial superposition
    qc.h(range(n_qubits))

    for layer in range(p):
        # Cost unitary
        for i, j in graph_edges:
            qc.cx(i, j)
            qc.rz(gamma[layer], j)
            qc.cx(i, j)

        # Mixer unitary
        for q in range(n_qubits):
            qc.rx(2 * beta[layer], q)

    qc.measure_all()
    return qc
```

---

## 7.8 CV Algorithms

### Gaussian Boson Sampling (GBS)

**Setup**:
```
n squeezed states → Linear optics (unitary U) → Photon counting
```

**Output distribution**:
```
P(S) ∝ |Haf(A_S)|²

where A = U diag(tanh r) Uᵀ
Haf = Hafnian (permanent-like function)
```

**Computational hardness**: Computing Hafnians is #P-hard

**Applications**:
- Molecular vibronic spectra
- Graph optimization
- Dense subgraph finding

### Implementation

```python
import strawberryfields as sf
from strawberryfields import ops
import numpy as np

def gbs_circuit(A, n_modes):
    """Gaussian Boson Sampling circuit"""
    prog = sf.Program(n_modes)

    # Decompose A into squeezing and interferometer
    # A = U diag(tanh r) U^T

    with prog.context as q:
        # Apply squeezing
        for i in range(n_modes):
            ops.Sgate(r[i]) | q[i]

        # Apply interferometer (decomposed into beamsplitters)
        ops.Interferometer(U) | q

        # Measure photon numbers
        ops.MeasureFock() | q

    eng = sf.Engine("fock", backend_options={"cutoff_dim": 10})
    result = eng.run(prog, shots=1000)

    return result.samples
```

### CV-VQE

Variational algorithm using CV ansatz:
```python
import strawberryfields as sf
from strawberryfields import ops

def cv_vqe_ansatz(params, n_modes):
    """CV variational ansatz"""
    prog = sf.Program(n_modes)

    with prog.context as q:
        # Displacement layer
        for i in range(n_modes):
            ops.Dgate(params[i]) | q[i]

        # Squeezing layer
        for i in range(n_modes):
            ops.Sgate(params[n_modes + i]) | q[i]

        # Entangling layer (beamsplitters)
        for i in range(n_modes - 1):
            ops.BSgate(params[2*n_modes + i]) | (q[i], q[i+1])

    return prog
```

---

## 7.9 Quantum Simulation

### Hamiltonian Simulation Problem

Given H and time t, implement U = e^(-iHt)

### Trotter-Suzuki Decomposition

For H = Σₖ Hₖ:
```
First order:
e^(-iHt) ≈ (∏ₖ e^(-iHₖt/n))ⁿ

Second order:
e^(-iHt) ≈ (e^(-iH₁t/2n) e^(-iH₂t/n) ... e^(-iH₁t/2n))ⁿ

Error: O(t²/n) for first order, O(t³/n²) for second order
```

### Example: Heisenberg Model

```
H = J Σ_{⟨i,j⟩} (XᵢXⱼ + YᵢYⱼ + ZᵢZⱼ)

Each term simulated by:
e^(-iJtXₓXⱼ) = CNOT · Rz(2Jt) · CNOT
(similar for YY, ZZ)
```

---

## 7.10 Summary

### Algorithm Comparison

| Algorithm | Speedup | Problem Type | Qubits Needed |
|-----------|---------|--------------|---------------|
| Deutsch-Jozsa | Exponential | Oracle | n+1 |
| Grover | Quadratic | Search | n |
| Shor | Exponential | Factoring | ~3n |
| VQE | Heuristic | Ground state | Problem-dependent |
| QAOA | Heuristic | Optimization | n |
| GBS | Exponential | Sampling | n modes |

### Choosing an Algorithm

```
                    Your Problem
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
      Structured    Optimization    Simulation
          │              │              │
    ┌─────┴─────┐   ┌────┴────┐   ┌────┴────┐
    │ Shor,     │   │ QAOA,   │   │ Trotter,│
    │ Grover,   │   │ VQE     │   │ VQE     │
    │ HHL       │   │         │   │         │
    └───────────┘   └─────────┘   └─────────┘
```

---

## Exercises

### Theory Problems

1. Calculate the number of Grover iterations for N = 1024 items.

2. Explain why Shor's algorithm doesn't break quantum-safe cryptography.

3. Derive the circuit for the QAOA mixer e^(-iβΣXᵢ).

### Programming Exercises

1. Implement Grover's algorithm for 4 qubits and search for target = 7.

2. Create a QAOA circuit for MaxCut on a triangle graph.

3. Implement VQE for H = Z₀ + 0.5(Z₀Z₁) and find the ground state energy.

4. (CV) Create a GBS circuit with 4 modes and analyze the output distribution.

---

## Next Module

[Module 8: Photonic Implementations →](../08-photonic-implementations/README.md)
