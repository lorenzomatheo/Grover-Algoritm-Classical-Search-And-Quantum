# Quantum Algorithms Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Grover's Algorithm](#grovers-algorithm)
3. [Classical Search Comparison](#classical-search-comparison)
4. [Mathematical Foundations](#mathematical-foundations)
5. [Implementation Details](#implementation-details)
6. [Quantum Circuit Analysis](#quantum-circuit-analysis)

## Introduction

This document provides a comprehensive technical overview of the quantum search algorithms implemented in this project. The focus is on understanding both the theoretical foundations and practical implementation of Grover's algorithm compared to classical search methods.

## Grover's Algorithm

### Overview

Grover's algorithm is a quantum search algorithm that can find a unique item in an unsorted database of N items using O(√N) queries, compared to O(N) queries required by classical algorithms. This represents a quadratic speedup, which is significant for large databases.

### Key Principles

1. **Superposition**: All possible states are explored simultaneously
2. **Interference**: Quantum interference amplifies the correct answer
3. **Iteration**: The algorithm requires approximately π/4 × √N iterations

### Algorithm Steps

1. **Initialization**: Create uniform superposition of all possible states
2. **Oracle Application**: Mark the target state (phase flip)
3. **Diffusion Operator**: Amplify the amplitude of the marked state
4. **Measurement**: Extract the result

### Mathematical Formulation

For a database of N = 2^n items:

- **Initial State**: |ψ₀⟩ = (1/√N) Σᵢ |i⟩
- **Target State**: |t⟩ (the item we're searching for)
- **Oracle**: O|i⟩ = -|i⟩ if i = t, otherwise |i⟩
- **Diffusion Operator**: D = 2|ψ₀⟩⟨ψ₀| - I

The algorithm applies the operator (DO)^k where k ≈ π/4 × √N.

## Classical Search Comparison

### Linear Search Algorithm

The classical approach uses a simple linear traversal:

```python
def classical_search(target, data):
    for i, value in enumerate(data):
        if value == target:
            return i
    return -1
```

### Complexity Analysis

| Algorithm | Time Complexity | Space Complexity | Queries |
|-----------|----------------|------------------|---------|
| Classical Linear | O(N) | O(1) | N/2 (average) |
| Grover's | O(√N) | O(log N) | π/4 × √N |

### Performance Characteristics

#### Classical Search
- **Best Case**: O(1) - target at first position
- **Average Case**: O(N/2) - target in middle
- **Worst Case**: O(N) - target at last position or not found
- **Deterministic**: Always finds the correct answer

#### Grover's Algorithm
- **Consistent**: O(√N) regardless of target position
- **Probabilistic**: Success probability increases with iterations
- **Optimal Iterations**: ~π/4 × √N for maximum success probability

## Mathematical Foundations

### Quantum State Representation

For n qubits, we can represent 2^n states:
- |0⟩, |1⟩, |2⟩, ..., |2^n - 1⟩
- Each state represents a possible database item

### Superposition Principle

The initial state is:
|ψ₀⟩ = (1/√2^n) Σᵢ₌₀^(2^n-1) |i⟩

This creates equal probability amplitudes for all possible states.

### Oracle Function

The oracle marks the target state by flipping its phase:
- O|t⟩ = -|t⟩ (target state)
- O|i⟩ = |i⟩ (non-target states)

### Diffusion Operator

The diffusion operator reflects the state about the average amplitude:
D = 2|ψ₀⟩⟨ψ₀| - I

This amplifies the marked state while suppressing others.

### Success Probability

After k iterations, the success probability is:
P(k) = sin²((2k + 1)θ)

Where θ = arcsin(1/√N) and optimal k ≈ π/4 × √N.

## Implementation Details

### Quantum Circuit Construction

The implementation follows these steps:

1. **Register Setup**:
   ```python
   qreg = QuantumRegister(num_qubits, "q")
   creg = ClassicalRegister(num_qubits, "c")
   qc = QuantumCircuit(qreg, creg)
   ```

2. **Superposition Creation**:
   ```python
   qc.h(qreg)  # Apply Hadamard to all qubits
   ```

3. **Oracle Implementation**:
   ```python
   # Mark target state with phase flip
   for i, bit in enumerate(target_state):
       if bit == '0':
           qc.x(qreg[i])  # Flip qubits for 0 bits
   
   # Apply multi-controlled Z gate
   qc.h(qreg[-1])
   qc.mcx(qreg[:-1], qreg[-1])
   qc.h(qreg[-1])
   ```

4. **Diffusion Operator**:
   ```python
   qc.h(qreg)
   qc.x(qreg)
   qc.h(qreg[-1])
   qc.cx(qreg[:-1], qreg[-1])
   qc.h(qreg[-1])
   qc.x(qreg)
   qc.h(qreg)
   ```

### Measurement and Results

The quantum circuit is measured multiple times (shots) to obtain statistical results:

```python
qc.measure(qreg, creg)
sim = AerSimulator()
job = sim.run(qc, shots=1024)
result = job.result()
counts = result.get_counts()
```

## Quantum Circuit Analysis

### Circuit Depth

The circuit depth grows as:
- **Oracle**: O(n) - linear in number of qubits
- **Diffusion**: O(n) - linear in number of qubits
- **Total per iteration**: O(n)
- **Total algorithm**: O(n × √N)

### Gate Count

For n qubits:
- **Hadamard gates**: 2n per iteration
- **X gates**: 2n per iteration
- **CNOT gates**: n per iteration
- **Multi-controlled gates**: 1 per iteration

### Error Considerations

1. **Gate Errors**: Each quantum gate has a small error probability
2. **Decoherence**: Quantum states lose coherence over time
3. **Measurement Errors**: Readout errors affect final results
4. **Simulation Limitations**: Classical simulation is limited by memory

### Scalability

- **Classical Simulation**: Limited to ~30 qubits on typical hardware
- **Quantum Hardware**: Current devices support 50-100+ qubits
- **Practical Applications**: Grover's algorithm is most useful for large databases

## Advanced Topics

### Multiple Target States

Grover's algorithm can be modified to search for multiple target states:
- Success probability decreases with number of targets
- Optimal number of iterations changes
- Oracle becomes more complex

### Partial Search

For cases where we only need to find if a target exists (not its exact location):
- Can stop early with high confidence
- Reduces number of required iterations
- Useful for decision problems

### Quantum Error Correction

In real quantum hardware:
- Error correction codes are necessary
- Overhead increases circuit complexity
- Fault-tolerant implementation required

## References
1. Nielsen, M. A., & Chuang, I. L. (2010). "Quantum computation and quantum information"
