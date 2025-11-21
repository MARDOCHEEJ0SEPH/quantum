# Quantum Computing Exercises

## Hands-On Practice for DV and CV

This directory contains practical exercises and projects to reinforce your learning.

---

## Exercise Structure

Each exercise includes:
1. **Problem statement**
2. **Hints** (if needed)
3. **Solution template**
4. **Full solution** (separate file)

---

## Beginner Exercises

### E1: Quantum State Manipulation
**File**: `beginner/e1_state_manipulation.py`

```python
"""
Exercise 1: Quantum State Manipulation

Tasks:
1. Create the state |+⟩ = (|0⟩ + |1⟩)/√2
2. Create the state |-⟩ = (|0⟩ - |1⟩)/√2
3. Create the state |+i⟩ = (|0⟩ + i|1⟩)/√2
4. Verify your states by measuring in appropriate bases
"""

from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator

def create_plus_state():
    """Create |+⟩ state"""
    qc = QuantumCircuit(1, 1)
    # TODO: Add gates to create |+⟩
    return qc

def create_minus_state():
    """Create |-⟩ state"""
    qc = QuantumCircuit(1, 1)
    # TODO: Add gates to create |-⟩
    return qc

def create_plus_i_state():
    """Create |+i⟩ state"""
    qc = QuantumCircuit(1, 1)
    # TODO: Add gates to create |+i⟩
    return qc

# Test your implementations
if __name__ == "__main__":
    sim = AerSimulator()

    # Test |+⟩
    qc = create_plus_state()
    qc.measure(0, 0)
    result = sim.run(qc, shots=1000).result()
    print("|+⟩ state measurement:", result.get_counts())
    # Expected: ~50% |0⟩, ~50% |1⟩
```

### E2: Bell State Creation
**File**: `beginner/e2_bell_states.py`

```python
"""
Exercise 2: Create All Four Bell States

Bell states:
|Φ⁺⟩ = (|00⟩ + |11⟩)/√2
|Φ⁻⟩ = (|00⟩ - |11⟩)/√2
|Ψ⁺⟩ = (|01⟩ + |10⟩)/√2
|Ψ⁻⟩ = (|01⟩ - |10⟩)/√2

Tasks:
1. Create circuits for each Bell state
2. Verify by measuring correlations
"""

from qiskit import QuantumCircuit

def create_bell_phi_plus():
    """Create |Φ⁺⟩ = (|00⟩ + |11⟩)/√2"""
    qc = QuantumCircuit(2, 2)
    # TODO: Implement
    return qc

def create_bell_phi_minus():
    """Create |Φ⁻⟩ = (|00⟩ - |11⟩)/√2"""
    qc = QuantumCircuit(2, 2)
    # TODO: Implement
    return qc

def create_bell_psi_plus():
    """Create |Ψ⁺⟩ = (|01⟩ + |10⟩)/√2"""
    qc = QuantumCircuit(2, 2)
    # TODO: Implement
    return qc

def create_bell_psi_minus():
    """Create |Ψ⁻⟩ = (|01⟩ - |10⟩)/√2"""
    qc = QuantumCircuit(2, 2)
    # TODO: Implement
    return qc
```

### E3: CV Coherent States
**File**: `beginner/e3_coherent_states.py`

```python
"""
Exercise 3: Coherent States in CV

Tasks:
1. Create coherent states with different amplitudes
2. Measure x-quadrature and verify mean
3. Measure photon statistics and verify Poissonian distribution
"""

import strawberryfields as sf
from strawberryfields import ops
import numpy as np

def create_coherent_state(alpha):
    """Create coherent state |α⟩"""
    prog = sf.Program(1)

    with prog.context as q:
        # TODO: Create coherent state with amplitude alpha
        pass

    return prog

def measure_x_quadrature(alpha, shots=1000):
    """Measure x-quadrature of coherent state"""
    prog = sf.Program(1)

    with prog.context as q:
        ops.Coherent(alpha) | q[0]
        ops.MeasureX | q[0]

    eng = sf.Engine("gaussian")
    results = []
    for _ in range(shots):
        result = eng.run(prog)
        results.append(result.samples[0])

    return np.array(results)

# Test
if __name__ == "__main__":
    alpha = 2.0
    x_measurements = measure_x_quadrature(alpha)

    print(f"Coherent state |{alpha}⟩:")
    print(f"  Mean x: {np.mean(x_measurements):.3f} (expected: {np.sqrt(2)*alpha:.3f})")
    print(f"  Std x: {np.std(x_measurements):.3f} (expected: {1/np.sqrt(2):.3f})")
```

---

## Medium Exercises

### E4: Quantum Fourier Transform
**File**: `medium/e4_qft.py`

```python
"""
Exercise 4: Implement Quantum Fourier Transform

Tasks:
1. Implement QFT from scratch (no built-in qft)
2. Test on various input states
3. Compare with inverse QFT
"""

from qiskit import QuantumCircuit
import numpy as np

def qft_rotations(circuit, n):
    """Apply QFT rotations to first n qubits"""
    if n == 0:
        return circuit

    n -= 1
    circuit.h(n)

    for qubit in range(n):
        # TODO: Add controlled rotation gates
        pass

    # Recursive call
    qft_rotations(circuit, n)

def swap_registers(circuit, n):
    """Swap qubits for correct QFT output ordering"""
    for qubit in range(n // 2):
        circuit.swap(qubit, n - qubit - 1)
    return circuit

def qft(n):
    """Create n-qubit QFT circuit"""
    qc = QuantumCircuit(n)
    qft_rotations(qc, n)
    swap_registers(qc, n)
    return qc

# Test
if __name__ == "__main__":
    n = 3
    qft_circuit = qft(n)
    print(qft_circuit.draw())
```

### E5: Grover's Algorithm
**File**: `medium/e5_grover.py`

```python
"""
Exercise 5: Implement Grover's Search Algorithm

Tasks:
1. Implement oracle for a specific target
2. Implement diffusion operator
3. Run complete Grover's algorithm
4. Verify quadratic speedup by counting iterations
"""

from qiskit import QuantumCircuit
import numpy as np

def create_oracle(n, target):
    """Create oracle that marks target state"""
    oracle = QuantumCircuit(n, name='Oracle')
    # TODO: Implement oracle
    return oracle

def create_diffusion(n):
    """Create Grover diffusion operator"""
    diffusion = QuantumCircuit(n, name='Diffusion')
    # TODO: Implement diffusion
    return diffusion

def grover_algorithm(n, target):
    """Complete Grover's algorithm"""
    qc = QuantumCircuit(n, n)

    # Initial superposition
    qc.h(range(n))

    # Number of iterations
    iterations = int(np.pi / 4 * np.sqrt(2**n))

    for _ in range(iterations):
        # TODO: Apply oracle and diffusion
        pass

    qc.measure(range(n), range(n))
    return qc

# Test
if __name__ == "__main__":
    n = 4
    target = 7  # Search for |0111⟩

    qc = grover_algorithm(n, target)
    print(f"Searching for target {target} in {2**n} items")
    print(f"Number of iterations: {int(np.pi/4 * np.sqrt(2**n))}")
```

### E6: CV Squeezing and Entanglement
**File**: `medium/e6_cv_entanglement.py`

```python
"""
Exercise 6: CV Entanglement with Two-Mode Squeezing

Tasks:
1. Create two-mode squeezed state
2. Verify EPR-like correlations
3. Calculate logarithmic negativity
"""

import strawberryfields as sf
from strawberryfields import ops
import numpy as np

def create_tms_state(r):
    """Create two-mode squeezed state"""
    prog = sf.Program(2)

    with prog.context as q:
        # TODO: Apply two-mode squeezing
        pass

    return prog

def measure_correlations(r, shots=1000):
    """Measure x1-x2 and p1+p2 correlations"""
    # TODO: Implement correlation measurement
    pass

def calculate_log_negativity(cov_matrix):
    """Calculate logarithmic negativity from covariance matrix"""
    # TODO: Implement log negativity calculation
    pass

# Test
if __name__ == "__main__":
    r = 1.0  # Squeezing parameter

    # Create and measure
    prog = create_tms_state(r)
    eng = sf.Engine("gaussian")
    result = eng.run(prog)

    # Get covariance matrix
    cov = result.state.cov()
    print("Covariance matrix:")
    print(cov)
```

---

## Advanced Exercises

### E7: Surface Code Simulation
**File**: `advanced/e7_surface_code.py`

```python
"""
Exercise 7: Surface Code Error Correction

Tasks:
1. Implement stabilizer measurements
2. Add noise and measure syndromes
3. Implement simple decoder
4. Calculate logical error rate vs physical error rate
"""

import numpy as np
from collections import defaultdict

class SurfaceCode:
    def __init__(self, d):
        """Initialize distance-d surface code"""
        self.d = d
        self.data_qubits = np.zeros((d, d), dtype=int)  # 0=I, 1=X, 2=Y, 3=Z

    def apply_noise(self, p):
        """Apply depolarizing noise with probability p"""
        for i in range(self.d):
            for j in range(self.d):
                if np.random.random() < p:
                    self.data_qubits[i, j] = np.random.choice([1, 2, 3])

    def measure_syndromes(self):
        """Measure X and Z stabilizer syndromes"""
        # TODO: Implement syndrome measurement
        x_syndromes = np.zeros((self.d-1, self.d), dtype=int)
        z_syndromes = np.zeros((self.d, self.d-1), dtype=int)
        return x_syndromes, z_syndromes

    def decode_mwpm(self, syndromes):
        """Minimum weight perfect matching decoder"""
        # TODO: Implement MWPM decoding
        pass

    def check_logical_error(self):
        """Check if logical error occurred"""
        # TODO: Check if error chain spans code
        pass

# Test
if __name__ == "__main__":
    d = 5  # Code distance
    p = 0.01  # Physical error rate
    trials = 1000

    logical_errors = 0
    for _ in range(trials):
        code = SurfaceCode(d)
        code.apply_noise(p)
        syndromes = code.measure_syndromes()
        code.decode_mwpm(syndromes)
        if code.check_logical_error():
            logical_errors += 1

    print(f"Logical error rate: {logical_errors/trials:.4f}")
```

### E8: VQE for H2 Molecule
**File**: `advanced/e8_vqe_h2.py`

```python
"""
Exercise 8: VQE for Hydrogen Molecule

Tasks:
1. Set up H2 Hamiltonian
2. Create hardware-efficient ansatz
3. Implement VQE optimization
4. Compare with exact diagonalization
"""

from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator
import numpy as np
from scipy.optimize import minimize

# H2 Hamiltonian coefficients (simplified)
H2_COEFFS = {
    'II': -1.0523,
    'ZI': 0.3979,
    'IZ': -0.3979,
    'ZZ': -0.0112,
    'XX': 0.1809
}

def create_ansatz(params):
    """Hardware-efficient ansatz for 2 qubits"""
    qc = QuantumCircuit(2)
    # TODO: Implement ansatz
    return qc

def measure_term(qc, term):
    """Measure a Pauli term"""
    # TODO: Add basis rotation and measurement
    pass

def compute_energy(params):
    """Compute ⟨H⟩ for given parameters"""
    energy = 0
    for term, coeff in H2_COEFFS.items():
        # TODO: Measure each term and sum
        pass
    return energy

def run_vqe():
    """Run VQE optimization"""
    initial_params = np.random.randn(4) * 0.1
    result = minimize(compute_energy, initial_params, method='COBYLA')
    return result.fun

# Test
if __name__ == "__main__":
    ground_energy = run_vqe()
    print(f"VQE ground state energy: {ground_energy:.4f}")
    print(f"Exact ground state energy: -1.1373")  # For comparison
```

### E9: Hybrid DV-CV Simulation
**File**: `advanced/e9_hybrid_simulation.py`

```python
"""
Exercise 9: Hybrid Qubit-Oscillator System

Tasks:
1. Simulate Jaynes-Cummings dynamics
2. Create cat state via conditional displacement
3. Implement photon parity measurement
"""

import numpy as np
from qutip import *

def jaynes_cummings_dynamics(g, delta, t_max, n_steps):
    """
    Simulate Jaynes-Cummings model

    Parameters:
    - g: coupling strength
    - delta: detuning (ω_c - ω_q)
    - t_max: maximum time
    - n_steps: number of time steps
    """
    N = 20  # Fock space truncation

    # Operators
    a = tensor(destroy(N), qeye(2))
    sm = tensor(qeye(N), sigmam())
    sz = tensor(qeye(N), sigmaz())

    # Hamiltonian
    wc = 1.0
    wq = wc - delta
    H = wc * a.dag() * a + wq/2 * sz + g * (a.dag() * sm + a * sm.dag())

    # Initial state: qubit excited, cavity vacuum
    psi0 = tensor(basis(N, 0), basis(2, 1))

    # Time evolution
    times = np.linspace(0, t_max, n_steps)
    result = mesolve(H, psi0, times, [], [a.dag()*a, sz])

    return times, result.expect

def create_cat_state(alpha):
    """Create cat state via conditional displacement"""
    N = 30

    # Initial state: |+⟩|0⟩
    psi_plus = (basis(2, 0) + basis(2, 1)).unit()
    psi0 = tensor(basis(N, 0), psi_plus)

    # Conditional displacement
    D_plus = displace(N, alpha)
    D_minus = displace(N, -alpha)

    proj_0 = tensor(qeye(N), basis(2, 0) * basis(2, 0).dag())
    proj_1 = tensor(qeye(N), basis(2, 1) * basis(2, 1).dag())

    CD = proj_0 * tensor(D_plus, qeye(2)) + proj_1 * tensor(D_minus, qeye(2))

    psi_cat = CD * psi0

    return psi_cat

# Test
if __name__ == "__main__":
    # Test Jaynes-Cummings
    times, expect = jaynes_cummings_dynamics(g=0.1, delta=0, t_max=50, n_steps=100)
    print("Jaynes-Cummings simulation complete")
    print(f"Max cavity photons: {max(expect[0]):.3f}")

    # Test cat state
    cat = create_cat_state(2.0)
    print(f"\nCat state created with {cat.norm():.3f} norm")
```

---

## Projects

### P1: Quantum Random Number Generator
Build a certified quantum random number generator using Bell test.

### P2: Quantum Key Distribution (BB84)
Implement the BB84 protocol with error estimation.

### P3: Variational Quantum Classifier
Build a complete ML pipeline with quantum circuits.

### P4: Gaussian Boson Sampling Simulator
Implement GBS and analyze output distributions.

### P5: Error-Corrected Logical Qubit
Simulate a full error correction cycle.

---

## Solutions

Solutions are provided in the `solutions/` directory. Try the exercises first!
