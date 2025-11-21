# Quantum Computing Resources

## Comprehensive Reference Guide

---

## Textbooks

### Discrete Variable (DV)

| Title | Author(s) | Level | Focus |
|-------|-----------|-------|-------|
| **Quantum Computation and Quantum Information** | Nielsen & Chuang | All | Comprehensive reference |
| **Quantum Computing: An Applied Approach** | Hidary | Beginner-Medium | Practical |
| **An Introduction to Quantum Computing** | Kaye, Laflamme, Mosca | Beginner | Theory |
| **Quantum Computer Science** | Mermin | Beginner | CS perspective |
| **Classical and Quantum Computation** | Kitaev, Shen, Vyalyi | Advanced | Complexity |

### Continuous Variable (CV)

| Title | Author(s) | Level | Focus |
|-------|-----------|-------|-------|
| **Quantum Information with CV** | Braunstein, van Loock | Medium-Advanced | Theory |
| **Quantum Optics** | Gerry, Knight | Medium | Fundamentals |
| **Methods in Theoretical Quantum Optics** | Barnett, Radmore | Advanced | Mathematical |

### Error Correction

| Title | Author(s) | Level | Focus |
|-------|-----------|-------|-------|
| **Quantum Error Correction** | Lidar, Brun (Eds.) | Advanced | Comprehensive |
| **Fault-Tolerant Quantum Computation** | Gottesman | Advanced | Theory |

---

## Online Courses

### Free Courses

1. **IBM Qiskit Textbook**
   - URL: https://qiskit.org/textbook
   - Level: Beginner to Medium
   - Focus: DV, practical

2. **Xanadu Quantum Codebook**
   - URL: https://codebook.xanadu.ai
   - Level: Beginner to Medium
   - Focus: DV and CV

3. **MIT OCW Quantum Computing**
   - URL: https://ocw.mit.edu
   - Level: Medium to Advanced
   - Focus: Theory

4. **Preskill's Lecture Notes**
   - URL: http://theory.caltech.edu/~preskill/ph219/
   - Level: Advanced
   - Focus: Theory, Error Correction

### Paid Courses

1. **edX Quantum Computing Series** (MIT, Berkeley)
2. **Coursera Quantum Computing** (Multiple universities)
3. **Brilliant.org Quantum Computing**

---

## Software Frameworks

### DV Frameworks

```
┌─────────────────────────────────────────────────────────────┐
│ Framework     │ Provider  │ Languages │ Hardware Access    │
├─────────────────────────────────────────────────────────────┤
│ Qiskit        │ IBM       │ Python    │ IBM Quantum        │
│ Cirq          │ Google    │ Python    │ Google hardware    │
│ Q#            │ Microsoft │ Q#        │ Azure Quantum      │
│ Braket        │ Amazon    │ Python    │ Multiple backends  │
│ PennyLane     │ Xanadu    │ Python    │ Multiple backends  │
│ PyQuil        │ Rigetti   │ Python    │ Rigetti hardware   │
│ ProjectQ      │ ETH       │ Python    │ Simulation         │
└─────────────────────────────────────────────────────────────┘
```

### CV Frameworks

```
┌─────────────────────────────────────────────────────────────┐
│ Framework          │ Provider │ Focus                      │
├─────────────────────────────────────────────────────────────┤
│ Strawberry Fields  │ Xanadu   │ Photonic CV computing     │
│ Bosonic Qiskit     │ IBM      │ Bosonic modes (cavity)    │
│ QuTiP              │ Community│ Open quantum systems      │
└─────────────────────────────────────────────────────────────┘
```

### Installation Commands

```bash
# Qiskit
pip install qiskit qiskit-aer qiskit-ibm-runtime

# Strawberry Fields
pip install strawberryfields

# PennyLane
pip install pennylane pennylane-sf

# Cirq
pip install cirq

# QuTiP
pip install qutip
```

---

## Research Resources

### Preprint Servers

- **arXiv quant-ph**: https://arxiv.org/list/quant-ph/recent
- Daily updates on quantum computing research

### Key Journals

| Journal | Publisher | Impact | Focus |
|---------|-----------|--------|-------|
| Physical Review X Quantum | APS | High | All quantum |
| Nature Physics | Springer | Very High | Breakthrough results |
| Quantum | Community | High | Open access |
| npj Quantum Information | Springer | High | Applications |
| PRX Quantum | APS | High | Theory & Experiment |
| Quantum Science & Technology | IOP | Medium | Technology |

### Conference Proceedings

- **QIP** (Quantum Information Processing)
- **TQC** (Theory of Quantum Computation)
- **QCMC** (Quantum Communication, Measurement, Computing)
- **IEEE Quantum Week**

---

## Hardware Access

### Cloud Quantum Computing

| Provider | Platform | Qubits/Modes | Access |
|----------|----------|--------------|--------|
| IBM | IBM Quantum | 100+ qubits | Free tier available |
| Xanadu | Xanadu Cloud | Photonic CV | Free tier available |
| Amazon | Braket | Multiple | Pay per use |
| Google | Cirq + Cloud | Limited | Research access |
| IonQ | Direct / Braket | 20+ qubits | Commercial |
| Quantinuum | H-Series | 32 qubits | Commercial |

### Getting Started

```python
# IBM Quantum
from qiskit_ibm_runtime import QiskitRuntimeService
service = QiskitRuntimeService(channel="ibm_quantum")
backend = service.backend("ibm_brisbane")

# Xanadu Cloud
import strawberryfields as sf
eng = sf.RemoteEngine("borealis")

# Amazon Braket
from braket.aws import AwsDevice
device = AwsDevice("arn:aws:braket:::device/quantum-simulator/amazon/sv1")
```

---

## Mathematical Prerequisites

### Linear Algebra

Essential topics:
- Vector spaces, bases, dimension
- Linear operators, matrices
- Eigenvalues, eigenvectors
- Inner products, norms
- Tensor products
- Trace, partial trace

**Recommended resource**: "Linear Algebra Done Right" by Axler

### Complex Analysis

Essential topics:
- Complex numbers
- Euler's formula
- Unitary matrices

### Probability

Essential topics:
- Probability distributions
- Expectation values
- Variance
- Conditional probability

### Quantum Mechanics Basics

Essential topics:
- Schrödinger equation
- Superposition
- Measurement postulate
- Uncertainty principle

**Recommended resource**: "Quantum Mechanics" by Sakurai

---

## Community Resources

### Online Communities

| Platform | Community | URL |
|----------|-----------|-----|
| Slack | Qiskit | qiskit.slack.com |
| Discord | Unitary Fund | unitaryfund.github.io |
| Stack Exchange | Quantum Computing | quantumcomputing.stackexchange.com |
| Reddit | r/QuantumComputing | reddit.com/r/QuantumComputing |
| Discord | PennyLane | pennylane.ai/community |

### Professional Organizations

- **APS** (American Physical Society)
- **IEEE Quantum**
- **Quantum Industry Coalition**

### Conferences and Events

- QIP (annual, January)
- APS March Meeting
- IEEE Quantum Week
- Qiskit Global Summer School
- Xanadu Quantum Software Workshop

---

## Career Resources

### Job Boards

- Quantum Computing Report job board
- IBM Quantum careers
- Google Quantum AI careers
- Amazon Quantum careers
- LinkedIn quantum computing jobs

### Skills to Develop

```
Technical:
├── Programming (Python, essential)
├── Linear algebra (fundamental)
├── Quantum mechanics basics
├── Quantum algorithms
├── Error correction concepts
└── Specific frameworks (Qiskit, etc.)

Research:
├── Reading papers
├── Reproducing results
├── Writing publications
└── Presenting work

Soft skills:
├── Collaboration
├── Communication
└── Problem-solving
```

---

## Glossary

### DV Terms

| Term | Definition |
|------|------------|
| Qubit | Two-level quantum system |
| Gate | Unitary operation on qubits |
| Circuit | Sequence of gates |
| Entanglement | Non-classical correlations |
| Superposition | Quantum state combination |
| Measurement | State collapse to classical |
| Clifford | Gates preserving Pauli group |
| T gate | Non-Clifford gate for universality |
| Surface code | 2D topological error correcting code |

### CV Terms

| Term | Definition |
|------|------------|
| Mode | Quantum harmonic oscillator degree of freedom |
| Quadrature | Position (x̂) or momentum (p̂) operator |
| Coherent state | Classical-like minimum uncertainty state |
| Squeezed state | Reduced uncertainty in one quadrature |
| Gaussian | State/operation with Gaussian Wigner function |
| Homodyne | Measurement of single quadrature |
| GKP | Grid state encoding for CV error correction |
| Fock state | Definite photon number state |

### General Terms

| Term | Definition |
|------|------------|
| NISQ | Noisy Intermediate-Scale Quantum |
| Fault-tolerant | Error-corrected quantum computing |
| VQE | Variational Quantum Eigensolver |
| QAOA | Quantum Approximate Optimization Algorithm |
| QML | Quantum Machine Learning |
| QPU | Quantum Processing Unit |

---

## Quick Reference Cards

### Pauli Matrices

```
X = [0 1]   Y = [0 -i]   Z = [1  0]   I = [1 0]
    [1 0]       [i  0]       [0 -1]       [0 1]
```

### Common Gates

```
H = 1/√2 [1  1]    S = [1 0]    T = [1    0   ]
         [1 -1]        [0 i]        [0  e^iπ/4]

CNOT = [1 0 0 0]   CZ = [1 0  0  0]
       [0 1 0 0]        [0 1  0  0]
       [0 0 0 1]        [0 0  1  0]
       [0 0 1 0]        [0 0  0 -1]
```

### CV Operations

```
Displacement: D(α) = exp(αâ† - α*â)
Squeezing: S(r) = exp[r(â†² - â²)/2]
Rotation: R(θ) = exp(iθâ†â)
Beamsplitter: BS(θ) = exp[θ(â†b̂ - âb̂†)]
```

### Important States

```
|+⟩ = (|0⟩ + |1⟩)/√2
|-⟩ = (|0⟩ - |1⟩)/√2
|Φ⁺⟩ = (|00⟩ + |11⟩)/√2  (Bell state)
|GHZ⟩ = (|000⟩ + |111⟩)/√2
|W⟩ = (|001⟩ + |010⟩ + |100⟩)/√3
```

---

## Updates

This resource guide is maintained with the course. Check for updates at:
- Course repository
- Associated documentation

Last updated: November 2024
