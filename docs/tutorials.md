# Tutorials and Usage Examples

## Table of Contents
1. [Getting Started](#getting-started)
2. [Basic Examples](#basic-examples)
3. [Advanced Examples](#advanced-examples)
4. [Performance Tuning](#performance-tuning)
5. [Troubleshooting](#troubleshooting)
6. [Best Practices](#best-practices)

## Getting Started

### Prerequisites

Before running the examples, ensure you have the required dependencies installed:

```bash
pip install qiskit qiskit-aer matplotlib
```

### Running Your First Example

1. **Open the Jupyter Notebook:**
   ```bash
   jupyter notebook listadesordenada.ipynb
   ```

2. **Execute the Classical Search:**
   - Run the first few cells to see classical search in action
   - Observe the linear time complexity

3. **Execute the Quantum Search:**
   - Run the Grover's algorithm cell
   - Compare the results with classical search

## Basic Examples

### Example 1: Simple Classical Search

```python
# Create a small dataset
data = [42, 17, 89, 23, 56, 91, 3, 78]
target = 23

# Perform search
index = classical_search(target, data)
print(f"Target {target} found at index {index}")
```

**Expected Output:**
```
Target 23 found at index 3
```

### Example 2: Basic Quantum Search

```python
# Search for a 2-qubit state
grover_search(num_qubits=2, target_state="10")
```

**Expected Output:**
```
Resultados da medição: {'10': 512, '00': 256, '01': 128, '11': 128}

 Valor alvo: 10
 Estado encontrado: 10
 Tempo de execução: 0.000045 segundos
 Itens verificados (equivalentes): 4
```

### Example 3: Performance Comparison

```python
import time
import random

# Test with different dataset sizes
sizes = [64, 256, 1024, 4096]
results = []

for N in sizes:
    # Classical search
    data = [random.randint(0, 9999) for _ in range(N)]
    target = data[random.randint(0, N - 1)]
    
    start = time.perf_counter()
    classical_search(target, data)
    classical_time = time.perf_counter() - start
    
    # Quantum search (equivalent qubits)
    num_qubits = int(N.bit_length() - 1)
    target_state = format(random.randint(0, N-1), f'0{num_qubits}b')
    
    start = time.perf_counter()
    grover_search(num_qubits, target_state)
    quantum_time = time.perf_counter() - start
    
    results.append({
        'size': N,
        'classical_time': classical_time,
        'quantum_time': quantum_time
    })

# Display results
for result in results:
    print(f"Size: {result['size']}, "
          f"Classical: {result['classical_time']:.6f}s, "
          f"Quantum: {result['quantum_time']:.6f}s")
```

## Advanced Examples

### Example 4: Custom Oracle Implementation

```python
def custom_grover_search(num_qubits, target_states):
    """
    Grover's search for multiple target states
    """
    qreg = QuantumRegister(num_qubits, "q")
    creg = ClassicalRegister(num_qubits, "c")
    qc = QuantumCircuit(qreg, creg)
    
    # Initialize superposition
    qc.h(qreg)
    
    # Custom oracle for multiple targets
    for target in target_states:
        # Mark each target state
        for i, bit in enumerate(target):
            if bit == '0':
                qc.x(qreg[i])
        
        # Apply phase flip
        qc.h(qreg[-1])
        qc.mcx(qreg[:-1], qreg[-1])
        qc.h(qreg[-1])
        
        # Unmark
        for i, bit in enumerate(target):
            if bit == '0':
                qc.x(qreg[i])
    
    # Diffusion operator
    qc.h(qreg)
    qc.x(qreg)
    qc.h(qreg[-1])
    qc.cx(qreg[:-1], qreg[-1])
    qc.h(qreg[-1])
    qc.x(qreg)
    qc.h(qreg)
    
    # Measure
    qc.measure(qreg, creg)
    
    # Run simulation
    sim = AerSimulator()
    job = sim.run(qc, shots=1024)
    result = job.result()
    counts = result.get_counts()
    
    return counts

# Test with multiple targets
targets = ["101", "110"]
results = custom_grover_search(3, targets)
print("Multi-target results:", results)
```

### Example 5: Iterative Grover Search

```python
def iterative_grover_search(num_qubits, target_state, max_iterations=10):
    """
    Grover's search with configurable iterations
    """
    qreg = QuantumRegister(num_qubits, "q")
    creg = ClassicalRegister(num_qubits, "c")
    qc = QuantumCircuit(qreg, creg)
    
    # Initialize superposition
    qc.h(qreg)
    
    # Optimal number of iterations
    optimal_iterations = int(3.14159 / 4 * (2 ** num_qubits) ** 0.5)
    iterations = min(optimal_iterations, max_iterations)
    
    for _ in range(iterations):
        # Oracle
        for i, bit in enumerate(target_state):
            if bit == '0':
                qc.x(qreg[i])
        
        qc.h(qreg[-1])
        qc.mcx(qreg[:-1], qreg[-1])
        qc.h(qreg[-1])
        
        for i, bit in enumerate(target_state):
            if bit == '0':
                qc.x(qreg[i])
        
        # Diffusion
        qc.h(qreg)
        qc.x(qreg)
        qc.h(qreg[-1])
        qc.cx(qreg[:-1], qreg[-1])
        qc.h(qreg[-1])
        qc.x(qreg)
        qc.h(qreg)
    
    # Measure
    qc.measure(qreg, creg)
    
    # Run simulation
    sim = AerSimulator()
    job = sim.run(qc, shots=1024)
    result = job.result()
    counts = result.get_counts()
    
    return counts, iterations

# Test with different iteration counts
for max_iter in [1, 3, 5, 10]:
    counts, actual_iter = iterative_grover_search(3, "101", max_iter)
    success_rate = counts.get("101", 0) / 1024
    print(f"Max iterations: {max_iter}, "
          f"Actual iterations: {actual_iter}, "
          f"Success rate: {success_rate:.2%}")
```

### Example 6: Benchmarking Suite

```python
def benchmark_search_algorithms():
    """
    Comprehensive benchmarking of search algorithms
    """
    import matplotlib.pyplot as plt
    import numpy as np
    
    # Test parameters
    sizes = [2**i for i in range(3, 12)]  # 8 to 2048
    classical_times = []
    quantum_times = []
    
    for size in sizes:
        print(f"Testing size: {size}")
        
        # Classical benchmark
        data = [random.randint(0, 9999) for _ in range(size)]
        target = data[random.randint(0, size - 1)]
        
        start = time.perf_counter()
        classical_search(target, data)
        classical_times.append(time.perf_counter() - start)
        
        # Quantum benchmark (if size is power of 2)
        if size & (size - 1) == 0:  # Check if power of 2
            num_qubits = int(np.log2(size))
            target_state = format(target % size, f'0{num_qubits}b')
            
            start = time.perf_counter()
            grover_search(num_qubits, target_state)
            quantum_times.append(time.perf_counter() - start)
        else:
            quantum_times.append(None)
    
    # Plot results
    plt.figure(figsize=(10, 6))
    plt.loglog(sizes, classical_times, 'b-o', label='Classical Search')
    
    quantum_sizes = [s for s, t in zip(sizes, quantum_times) if t is not None]
    quantum_times_clean = [t for t in quantum_times if t is not None]
    
    if quantum_times_clean:
        plt.loglog(quantum_sizes, quantum_times_clean, 'r-s', label='Grover\'s Algorithm')
    
    plt.xlabel('Dataset Size')
    plt.ylabel('Execution Time (seconds)')
    plt.title('Search Algorithm Performance Comparison')
    plt.legend()
    plt.grid(True)
    plt.show()
    
    return sizes, classical_times, quantum_times

# Run benchmark
benchmark_search_algorithms()
```

## Performance Tuning

### Optimizing Classical Search

```python
def optimized_classical_search(target, data):
    """
    Optimized classical search with early termination
    """
    # Use enumerate for better performance
    for i, value in enumerate(data):
        if value == target:
            return i
    return -1

# Test optimization
data = list(range(10000))
target = 5000

# Time both versions
start = time.perf_counter()
result1 = classical_search(target, data)
time1 = time.perf_counter() - start

start = time.perf_counter()
result2 = optimized_classical_search(target, data)
time2 = time.perf_counter() - start

print(f"Original: {time1:.6f}s, Optimized: {time2:.6f}s")
```

### Optimizing Quantum Search

```python
def optimized_grover_search(num_qubits, target_state, shots=2048):
    """
    Optimized Grover's search with more shots for better accuracy
    """
    # ... (same implementation as before)
    
    # Use more shots for better statistics
    job = sim.run(qc, shots=shots)
    result = job.result()
    counts = result.get_counts()
    
    return counts

# Compare accuracy
results_1024 = grover_search(3, "101")  # 1024 shots
results_2048 = optimized_grover_search(3, "101", 2048)  # 2048 shots
```

## Troubleshooting

### Common Issues

#### 1. "ValueError: target_state length mismatch"

**Problem:** Target state length doesn't match number of qubits.

**Solution:**
```python
# Ensure target state matches qubit count
num_qubits = 3
target_state = "101"  # Must be 3 characters for 3 qubits
```

#### 2. "Simulation timeout or memory error"

**Problem:** Circuit too large for simulation.

**Solution:**
```python
# Reduce qubit count or use smaller datasets
if num_qubits > 20:
    print("Warning: Large circuit may cause memory issues")
    num_qubits = min(num_qubits, 15)
```

#### 3. "No measurement results"

**Problem:** Circuit execution failed.

**Solution:**
```python
# Add error handling
try:
    job = sim.run(qc, shots=1024)
    result = job.result()
    counts = result.get_counts()
    
    if not counts:
        print("Warning: No measurement results")
        return {}
        
except Exception as e:
    print(f"Simulation error: {e}")
    return {}
```

### Debugging Tips

#### 1. Circuit Visualization

```python
# Draw the quantum circuit
qc.draw(output='mpl')
plt.show()
```

#### 2. State Vector Analysis

```python
# Get state vector before measurement
from qiskit.quantum_info import Statevector

state = Statevector.from_instruction(qc)
print("State vector:", state.data)
```

#### 3. Gate-by-Gate Debugging

```python
# Add intermediate measurements
qc.measure(qreg, creg)
sim = AerSimulator()
job = sim.run(qc, shots=1)
result = job.result()
counts = result.get_counts()
print("Intermediate result:", counts)
```

## Best Practices

### 1. Code Organization

```python
# Separate functions for different components
def create_oracle(qc, qreg, target_state):
    """Create oracle for marking target state"""
    # Implementation here
    pass

def create_diffuser(qc, qreg):
    """Create diffusion operator"""
    # Implementation here
    pass

def run_grover_search(num_qubits, target_state):
    """Main Grover search function"""
    qreg = QuantumRegister(num_qubits, "q")
    creg = ClassicalRegister(num_qubits, "c")
    qc = QuantumCircuit(qreg, creg)
    
    # Initialize
    qc.h(qreg)
    
    # Apply oracle and diffuser
    create_oracle(qc, qreg, target_state)
    create_diffuser(qc, qreg)
    
    # Measure and return results
    qc.measure(qreg, creg)
    # ... rest of implementation
```

### 2. Error Handling

```python
def safe_grover_search(num_qubits, target_state):
    """Grover search with comprehensive error handling"""
    try:
        # Validate inputs
        if not isinstance(num_qubits, int) or num_qubits <= 0:
            raise ValueError("num_qubits must be a positive integer")
        
        if not isinstance(target_state, str):
            raise ValueError("target_state must be a string")
        
        if len(target_state) != num_qubits:
            raise ValueError("target_state length must match num_qubits")
        
        # Check for valid binary string
        if not all(c in '01' for c in target_state):
            raise ValueError("target_state must contain only 0s and 1s")
        
        # Proceed with search
        return grover_search(num_qubits, target_state)
        
    except Exception as e:
        print(f"Error in Grover search: {e}")
        return None
```

### 3. Performance Monitoring

```python
def timed_grover_search(num_qubits, target_state):
    """Grover search with detailed timing"""
    start_time = time.perf_counter()
    
    # Circuit creation time
    circuit_start = time.perf_counter()
    # ... circuit creation ...
    circuit_time = time.perf_counter() - circuit_start
    
    # Simulation time
    sim_start = time.perf_counter()
    # ... simulation ...
    sim_time = time.perf_counter() - sim_start
    
    total_time = time.perf_counter() - start_time
    
    print(f"Circuit creation: {circuit_time:.6f}s")
    print(f"Simulation: {sim_time:.6f}s")
    print(f"Total time: {total_time:.6f}s")
    
    return results
```

### 4. Documentation and Comments

```python
def grover_search(num_qubits, target_state):
    """
    Implements Grover's quantum search algorithm.
    
    This function searches for a target state in a quantum database
    of size 2^num_qubits using Grover's algorithm, which provides
    a quadratic speedup over classical search.
    
    Args:
        num_qubits (int): Number of qubits (database size = 2^num_qubits)
        target_state (str): Binary string representing target state
        
    Returns:
        None: Results are printed to console
        
    Raises:
        ValueError: If target_state length doesn't match num_qubits
        
    Example:
        >>> grover_search(3, "101")
        # Searches for state "101" in 8-element database
    """
    # Implementation with detailed comments
    pass
```

This comprehensive tutorial provides everything needed to understand, use, and extend the quantum search algorithms in this project.