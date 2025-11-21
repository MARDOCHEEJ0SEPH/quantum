# Module 11: Quantum Machine Learning

## Bridging Quantum Computing and AI

This module explores quantum machine learning in both DV and CV paradigms, covering variational circuits, quantum kernels, and quantum neural networks.

---

## Learning Objectives

By the end of this module, you will:
- Understand quantum-classical hybrid learning
- Implement variational quantum circuits
- Work with quantum kernel methods
- Explore CV-specific QML approaches

---

## 11.1 QML Landscape

### Categories of QML

```
┌─────────────────────────────────────────────────────────────┐
│                  QML TAXONOMY                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Classical data + Quantum processor:                        │
│    • Variational classifiers                               │
│    • Quantum kernel methods                                │
│    • Quantum neural networks                               │
│                                                             │
│  Quantum data + Quantum processor:                          │
│    • Quantum state tomography                              │
│    • Quantum error mitigation                              │
│    • Quantum simulation analysis                           │
│                                                             │
│  Classical data + Classical processor (quantum-inspired):   │
│    • Tensor networks                                       │
│    • Quantum-inspired sampling                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### When Might Quantum Help?

```
Potential advantages:
• High-dimensional feature spaces
• Quantum correlations in data
• Optimization landscapes
• Certain kernel computations

NOT guaranteed advantages:
• Arbitrary classical data
• Simple classification tasks
• Small datasets
```

---

## 11.2 Variational Quantum Circuits

### Basic Architecture

```
Classical Data → Encoding → Variational Circuit → Measurement → Classical Output
       x            Ux           U(θ)              ⟨M⟩           f(x; θ)

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  |0⟩ ──[Encode x]──[Ry(θ₁)]──●────[Ry(θ₃)]──M──           │
│                              │                              │
│  |0⟩ ──[Encode x]──[Ry(θ₂)]──⊕────[Ry(θ₄)]──M──           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Data Encoding Strategies

**Basis encoding**:
```
x = (x₁, x₂, ..., xₙ) → |x₁x₂...xₙ⟩

Binary: Efficient for discrete data
Requires log(N) qubits for N data points
```

**Amplitude encoding**:
```
x = (x₁, ..., x_N) → Σᵢ xᵢ|i⟩ / ||x||

Exponential compression: log(N) qubits for N features
Challenge: State preparation depth
```

**Angle encoding**:
```
x = (x₁, ..., xₙ) → ⊗ᵢ Ry(xᵢ)|0⟩

One qubit per feature
Most common for VQC
```

**IQP encoding**:
```
Apply H, then ZZ(xᵢxⱼ) rotations
Creates entanglement dependent on data
```

### Ansatz Design

**Hardware-efficient ansatz**:
```python
def hardware_efficient(n_qubits, depth, params):
    qc = QuantumCircuit(n_qubits)
    idx = 0
    for d in range(depth):
        # Single-qubit rotations
        for q in range(n_qubits):
            qc.ry(params[idx], q)
            qc.rz(params[idx+1], q)
            idx += 2
        # Entangling layer
        for q in range(n_qubits - 1):
            qc.cx(q, q+1)
    return qc
```

**Problem-inspired ansatz** (e.g., chemistry):
```
Use physically motivated structure
UCCSD for molecular simulations
```

**Data re-uploading**:
```
Encode data multiple times within circuit:
[Encode(x)] → [Layer 1] → [Encode(x)] → [Layer 2] → ...

Increases expressibility
```

---

## 11.3 Quantum Kernel Methods

### Kernel Trick

Classical SVM:
```
K(x, x') = φ(x)ᵀφ(x')

φ: Feature map to high-dimensional space
```

Quantum version:
```
K(x, x') = |⟨0|U†(x')U(x)|0⟩|²

U(x): Quantum feature map
Kernel = state overlap
```

### Quantum Feature Maps

```python
def quantum_feature_map(x, n_qubits, reps):
    """Create quantum feature map circuit"""
    qc = QuantumCircuit(n_qubits)

    for r in range(reps):
        # First-order terms
        for i in range(n_qubits):
            qc.h(i)
            qc.rz(2 * x[i % len(x)], i)

        # Second-order terms (entangling)
        for i in range(n_qubits - 1):
            qc.cx(i, i+1)
            qc.rz(2 * (np.pi - x[i]) * (np.pi - x[i+1]), i+1)
            qc.cx(i, i+1)

    return qc
```

### Computing the Kernel

```python
def compute_kernel(x1, x2, feature_map_func, n_qubits):
    """Compute quantum kernel K(x1, x2)"""
    # Create feature map circuits
    qc1 = feature_map_func(x1, n_qubits)
    qc2 = feature_map_func(x2, n_qubits)

    # Combine: U†(x2) U(x1)
    qc = qc1.compose(qc2.inverse())
    qc.measure_all()

    # Run and get probability of |0...0⟩
    simulator = AerSimulator()
    result = simulator.run(qc, shots=1000).result()
    counts = result.get_counts()

    # Kernel = probability of measuring all zeros
    kernel = counts.get('0' * n_qubits, 0) / 1000
    return kernel
```

### Kernel Matrix

```python
def kernel_matrix(X_train, feature_map_func, n_qubits):
    """Compute full kernel matrix"""
    n = len(X_train)
    K = np.zeros((n, n))

    for i in range(n):
        for j in range(i, n):
            K[i,j] = compute_kernel(X_train[i], X_train[j],
                                    feature_map_func, n_qubits)
            K[j,i] = K[i,j]

    return K
```

---

## 11.4 Training Variational Circuits

### Cost Functions

**Classification**:
```
L = -Σᵢ yᵢ log(p(y=1|xᵢ; θ)) + (1-yᵢ) log(1-p(y=1|xᵢ; θ))

p(y=1|x; θ) from qubit measurement
```

**Regression**:
```
L = Σᵢ (yᵢ - f(xᵢ; θ))²

f(x; θ) = ⟨ψ(x; θ)|M|ψ(x; θ)⟩
```

### Gradient Computation

**Parameter shift rule**:
```
∂L/∂θ = [L(θ + π/2) - L(θ - π/2)] / 2

Exact gradient using two circuit evaluations!
Works for gates of form e^(-iθG/2) where G² = I
```

**Implementation**:
```python
def parameter_shift_gradient(circuit, params, cost_func, param_idx):
    """Compute gradient using parameter shift rule"""
    shift = np.pi / 2

    params_plus = params.copy()
    params_plus[param_idx] += shift

    params_minus = params.copy()
    params_minus[param_idx] -= shift

    gradient = (cost_func(params_plus) - cost_func(params_minus)) / 2
    return gradient
```

### Barren Plateaus

**Problem**: Gradients vanish exponentially with circuit depth

```
⟨∂L/∂θ⟩ = 0
Var(∂L/∂θ) ~ exp(-n)  for random circuits

Training becomes exponentially hard!
```

**Mitigation strategies**:
- Structured ansätze (not random)
- Layer-wise training
- Correlated parameter initialization
- Local cost functions

---

## 11.5 CV Quantum Machine Learning

### CV Feature Encoding

**Displacement encoding**:
```
x = (x₁, ..., xₙ) → ⊗ᵢ D(xᵢ)|0⟩

Each feature displaces one mode
```

**Squeezing encoding**:
```
x → ⊗ᵢ S(xᵢ)|0⟩

Maps features to squeezing parameters
```

**Combined encoding**:
```
|ψ(x)⟩ = ⊗ᵢ D(xᵢ)S(rᵢ)|0⟩

More expressive
```

### CV Variational Layers

```python
import strawberryfields as sf
from strawberryfields import ops

def cv_layer(params, n_modes):
    """Single CV variational layer"""
    # Squeezing
    for i in range(n_modes):
        ops.Sgate(params[i]) | q[i]

    # Interferometer (parameterized beamsplitters)
    for i in range(n_modes - 1):
        ops.BSgate(params[n_modes + i]) | (q[i], q[i+1])

    # Displacement
    for i in range(n_modes):
        ops.Dgate(params[2*n_modes - 1 + i]) | q[i]

    # Kerr nonlinearity (non-Gaussian)
    for i in range(n_modes):
        ops.Kgate(params[3*n_modes - 1 + i]) | q[i]
```

### CV Kernel Methods

**Fidelity kernel**:
```
K(x, x') = |⟨ψ(x)|ψ(x')⟩|²
```

For Gaussian states with same squeezing:
```
K(x, x') = exp(-|αₓ - α_{x'}|² / 2)

Gaussian kernel naturally!
```

### Gaussian Process with CV

```python
def cv_kernel(x1, x2, squeezing=0.5):
    """CV quantum kernel using Gaussian states"""
    # Displacement difference
    diff = np.array(x1) - np.array(x2)

    # Gaussian state overlap
    # For coherent states: K = exp(-|α₁-α₂|²/2)
    kernel = np.exp(-np.sum(diff**2) / 2)

    # Squeezing modification
    kernel *= np.exp(-squeezing * np.sum(diff**2))

    return kernel
```

---

## 11.6 Quantum Neural Networks

### Quantum Perceptron

```
|x⟩ ──[U(θ)]── ⟨M⟩ ──[activation]── output

U(θ): Parameterized unitary
⟨M⟩: Expectation value
Activation: Classical function (sigmoid, ReLU)
```

### Quantum Convolutional Neural Network

```
┌─────────────────────────────────────────────────────────────┐
│                    QCNN STRUCTURE                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Layer 1 (Conv):  ──[U]──[U]──[U]──[U]──                   │
│                       \ /    \ /                            │
│  Layer 2 (Pool):  ────[M]────[M]────                       │
│                         \   /                               │
│  Layer 3 (Conv):  ──────[U]──────                          │
│                           │                                 │
│  Output:          ────────M────────                        │
│                                                             │
│  [U] = Parameterized 2-qubit gate                          │
│  [M] = Measurement (pooling by discarding)                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Implementation**:
```python
def qcnn(n_qubits, params):
    """Quantum CNN for classification"""
    qc = QuantumCircuit(n_qubits)
    idx = 0

    # Convolutional layers
    for layer in range(int(np.log2(n_qubits))):
        stride = 2 ** layer

        # Convolution
        for i in range(0, n_qubits - stride, 2 * stride):
            # Two-qubit parameterized gate
            qc.rxx(params[idx], i, i + stride)
            qc.ryy(params[idx + 1], i, i + stride)
            qc.rzz(params[idx + 2], i, i + stride)
            idx += 3

        # Pooling (measure and discard some qubits)
        # In simulation, trace out

    return qc
```

### Quantum Reservoir Computing

```
Fixed random quantum circuit as reservoir:
|0⟩ → [Random U] → [Feature extraction] → Classical readout

Benefits:
- No quantum gradient computation
- Only classical training
- Exploits quantum complexity
```

---

## 11.7 Expressibility and Trainability

### Expressibility

**Definition**: How well can the ansatz explore Hilbert space?

```
Expressibility = KL divergence from Haar-random distribution

More expressible ≠ better for ML
Need to balance with trainability
```

### Entangling Capability

```
Meyer-Wallach entanglement measure:
Q(|ψ⟩) = (2/n) Σᵢ S(ρᵢ)

Higher Q → more entanglement in state
Correlates with circuit power
```

### Trainability vs Expressibility Tradeoff

```
            Trainability
                ↑
                │  ○ Ideal region
        Low     │    ●
   expressibility│      ○ Hardware-efficient
                │         ○ Random circuits
                │             (barren plateaus)
                └─────────────────────────→
                        Expressibility

Goal: Enough expressibility, good trainability
```

---

## 11.8 Applications

### Drug Discovery

```
Molecular fingerprint → Quantum encoding → Property prediction

Potential advantage:
- Quantum representation of molecular orbitals
- Correlation capture in feature space
```

### Financial Modeling

```
Portfolio optimization:
- QAOA for combinatorial optimization
- Quantum Monte Carlo for risk analysis
- Quantum kernels for time series

Credit scoring:
- Variational classifiers
- Fairness constraints in quantum optimization
```

### Image Classification

```
Quantum-enhanced CNNs:
- Encode patches quantum mechanically
- Quantum convolution operations
- Pooling via measurement

Current status: Proof of concept, not yet advantageous
```

### Anomaly Detection

```
Train on normal data → Quantum model → Detect anomalies

Approach:
- Quantum autoencoders
- One-class quantum SVM
- Density estimation
```

---

## 11.9 Implementations

### PennyLane Example: Variational Classifier

```python
import pennylane as qml
from pennylane import numpy as np

# Device
dev = qml.device("default.qubit", wires=4)

@qml.qnode(dev)
def circuit(params, x):
    # Encode data
    for i in range(4):
        qml.RX(x[i], wires=i)

    # Variational layers
    for layer in range(2):
        for i in range(4):
            qml.RY(params[layer, i, 0], wires=i)
            qml.RZ(params[layer, i, 1], wires=i)
        for i in range(3):
            qml.CNOT(wires=[i, i+1])

    return qml.expval(qml.PauliZ(0))

def cost(params, X, Y):
    predictions = [circuit(params, x) for x in X]
    return np.mean((np.array(predictions) - Y)**2)

# Training
params = np.random.randn(2, 4, 2) * 0.1
opt = qml.GradientDescentOptimizer(stepsize=0.1)

for step in range(100):
    params = opt.step(lambda p: cost(p, X_train, Y_train), params)
```

### Strawberry Fields: CV Classifier

```python
import strawberryfields as sf
from strawberryfields import ops
import numpy as np

def cv_classifier(x, params, n_modes=4):
    """CV variational classifier"""
    prog = sf.Program(n_modes)

    with prog.context as q:
        # Encode data
        for i in range(n_modes):
            ops.Dgate(x[i % len(x)]) | q[i]

        # Variational layer
        for i in range(n_modes):
            ops.Sgate(params[i]) | q[i]

        for i in range(n_modes - 1):
            ops.BSgate(params[n_modes + i]) | (q[i], q[i+1])

        # Non-Gaussian (for expressibility)
        for i in range(n_modes):
            ops.Kgate(params[2*n_modes - 1 + i]) | q[i]

    eng = sf.Engine("fock", backend_options={"cutoff_dim": 10})
    result = eng.run(prog)

    # Output: photon number in mode 0
    return result.state.mean_photon(0)[0]
```

### Qiskit Machine Learning

```python
from qiskit_machine_learning.algorithms import VQC
from qiskit_machine_learning.circuit.library import ZZFeatureMap, RealAmplitudes
from qiskit_algorithms.optimizers import COBYLA

# Feature map
feature_map = ZZFeatureMap(feature_dimension=4, reps=2)

# Ansatz
ansatz = RealAmplitudes(num_qubits=4, reps=3)

# Classifier
vqc = VQC(
    feature_map=feature_map,
    ansatz=ansatz,
    optimizer=COBYLA(maxiter=100),
    quantum_instance=backend
)

# Train
vqc.fit(X_train, y_train)

# Predict
predictions = vqc.predict(X_test)
```

---

## 11.10 Summary

### QML Method Comparison

| Method | Type | Data Size | Training | Potential Advantage |
|--------|------|-----------|----------|---------------------|
| VQC | Hybrid | Small-Medium | On device | Feature space |
| Quantum Kernel | Kernel | Medium | Classical | Kernel computation |
| QCNN | Deep | Medium | On device | Symmetry exploitation |
| QRC | Reservoir | Large | Classical | Complexity |
| CV QML | Hybrid | Medium | Mixed | Natural encoding |

### Current Limitations

1. **NISQ constraints**: Noise limits circuit depth
2. **Data loading**: Amplitude encoding is expensive
3. **Barren plateaus**: Training challenges for deep circuits
4. **Classical simulation**: Small systems classically simulable

### Future Directions

- Provable quantum advantages for specific ML tasks
- Quantum data settings (learning quantum processes)
- Quantum-classical co-design
- Error-mitigated QML

---

## Exercises

### Theory Problems

1. Derive the parameter shift rule for Ry(θ) gates.

2. Calculate the expressibility of a single Ry rotation vs. the full Bloch sphere.

3. Show that quantum kernels satisfy Mercer's condition.

4. Why do barren plateaus occur for random circuits?

### Programming Exercises

1. Implement a variational classifier for the Iris dataset.

2. Create a quantum kernel SVM and compare to classical RBF kernel.

3. Build a QCNN for a simple image classification task.

4. Implement CV variational circuit with Strawberry Fields.

---

## Next Module

[Module 12: Research Frontiers →](../12-research-frontiers/README.md)
