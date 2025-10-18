# API Reference

## Table of Contents
1. [Classical Search Functions](#classical-search-functions)
2. [Quantum Search Functions](#quantum-search-functions)
3. [Utility Functions](#utility-functions)
4. [Data Structures](#data-structures)
5. [Error Handling](#error-handling)

## Classical Search Functions

### `classical_search(target, data)`

Performs a linear search through an unsorted list to find the target value.

**Parameters:**
- `target` (int): The value to search for
- `data` (list): The list of integers to search through

**Returns:**
- `int`: The index of the target value if found, -1 otherwise

**Time Complexity:** O(n)
**Space Complexity:** O(1)

**Example:**
```python
data = [5, 2, 8, 1, 9, 3]
index = classical_search(8, data)  # Returns 2
```

**Implementation Details:**
- Uses linear traversal through the list
- Returns immediately upon finding the target
- Returns -1 if target is not found

## Quantum Search Functions

### `grover_search(num_qubits, target_state)`

Implements Grover's quantum search algorithm to find a target state in a quantum database.

**Parameters:**
- `num_qubits` (int): Number of qubits (determines database size = 2^num_qubits)
- `target_state` (str): Binary string representing the target state (e.g., "101")

**Returns:**
- `None`: Results are printed to console and visualized

**Raises:**
- `ValueError`: If target_state length doesn't match num_qubits

**Time Complexity:** O(√N) where N = 2^num_qubits
**Space Complexity:** O(log N)

**Example:**
```python
grover_search(num_qubits=3, target_state="101")
```

**Output:**
```
Resultados da medição: {'001': 495, '011': 126, '000': 144, '100': 134, '111': 125}

 Valor alvo: 101
 Estado encontrado: 001
 Tempo de execução: 0.000123 segundos
 Itens verificados (equivalentes): 8
```

**Implementation Details:**

1. **Quantum Circuit Setup:**
   - Creates quantum and classical registers
   - Initializes all qubits in superposition

2. **Oracle Implementation:**
   - Marks the target state with a phase flip
   - Uses multi-controlled gates for efficiency

3. **Diffusion Operator:**
   - Amplifies the marked state
   - Suppresses non-target states

4. **Measurement:**
   - Performs 1024 shots for statistical accuracy
   - Returns measurement counts and timing

## Utility Functions

### Data Generation

#### Random Data Creation
```python
# Create random data for testing
N = 1024
data = [random.randint(0, 9999) for _ in range(N)]
```

**Parameters:**
- `N` (int): Size of the dataset
- `range` (tuple): Range of random values (0, 9999)

**Returns:**
- `list`: List of N random integers

#### Target Selection
```python
# Select random target from data
target = data[random.randint(0, N - 1)]
```

**Parameters:**
- `data` (list): The dataset to select from

**Returns:**
- `int`: Random element from the dataset

### Timing Functions

#### Performance Measurement
```python
start = time.perf_counter()
# ... algorithm execution ...
elapsed_time = time.perf_counter() - start
```

**Purpose:**
- Measures execution time with high precision
- Uses `time.perf_counter()` for best accuracy

**Returns:**
- `float`: Elapsed time in seconds

### Visualization Functions

#### Histogram Plotting
```python
plot_histogram(counts)
plt.show()
```

**Parameters:**
- `counts` (dict): Dictionary of measurement results

**Purpose:**
- Visualizes quantum measurement outcomes
- Shows probability distribution of results

## Data Structures

### Quantum Registers

#### QuantumRegister
```python
qreg = QuantumRegister(num_qubits, "q")
```

**Attributes:**
- `size`: Number of qubits
- `name`: Register identifier

#### ClassicalRegister
```python
creg = ClassicalRegister(num_qubits, "c")
```

**Attributes:**
- `size`: Number of classical bits
- `name`: Register identifier

### Quantum Circuit

#### QuantumCircuit
```python
qc = QuantumCircuit(qreg, creg)
```

**Methods:**
- `h(qubits)`: Apply Hadamard gate
- `x(qubits)`: Apply X (NOT) gate
- `z(qubits)`: Apply Z gate
- `cx(control, target)`: Apply CNOT gate
- `mcx(controls, target)`: Apply multi-controlled X gate
- `measure(qubits, cbits)`: Measure qubits

### Measurement Results

#### Counts Dictionary
```python
counts = {'001': 495, '011': 126, '000': 144, '100': 134, '111': 125}
```

**Structure:**
- Keys: Binary strings representing measured states
- Values: Number of times each state was measured

## Error Handling

### Input Validation

#### Target State Validation
```python
if len(target_state) != num_qubits:
    raise ValueError("O comprimento de 'target_state' deve ser igual a 'num_qubits'.")
```

**Checks:**
- Target state length matches number of qubits
- Target state contains only valid binary characters

#### Data Type Validation
```python
if not isinstance(data, list):
    raise TypeError("Data must be a list")
```

**Checks:**
- Input parameters are correct types
- Data structures are properly initialized

### Quantum Simulation Errors

#### Simulator Errors
```python
try:
    job = sim.run(qc, shots=1024)
    result = job.result()
except Exception as e:
    print(f"Simulation error: {e}")
```

**Common Issues:**
- Circuit depth too large
- Insufficient memory
- Invalid gate operations

#### Measurement Errors
```python
if not counts:
    raise RuntimeError("No measurement results obtained")
```

**Checks:**
- Measurement results are not empty
- Valid probability distribution

## Performance Metrics

### Classical Search Metrics

```python
print(f" Valor alvo: {target}")
print(f" Posição encontrada: {index_found}")
print(f" Tempo de execução: {elapsed_time:.6f} segundos")
print(f" Itens verificados: {N}")
```

**Metrics:**
- Target value
- Found index
- Execution time
- Items checked

### Quantum Search Metrics

```python
print("Resultados da medição:", counts)
print(f" Valor alvo: {target_state}")
print(f" Estado encontrado: {found_state}")
print(f" Tempo de execução: {elapsed_time:.6f} segundos")
print(f" Itens verificados (equivalentes): {2 ** num_qubits}")
```

**Metrics:**
- Measurement distribution
- Target state
- Most probable result
- Execution time
- Equivalent items checked

## Configuration Options

### Simulation Parameters

#### Number of Shots
```python
job = sim.run(qc, shots=1024)
```

**Default:** 1024 shots
**Purpose:** Statistical accuracy of measurements

#### Number of Qubits
```python
grover_search(num_qubits=3, target_state="101")
```

**Range:** 1 to ~30 (simulation limit)
**Impact:** Database size = 2^num_qubits

### Data Parameters

#### Dataset Size
```python
N = 1024  # Adjustable size
```

**Default:** 1024 elements
**Purpose:** Comparison with quantum search

#### Value Range
```python
data = [random.randint(0, 9999) for _ in range(N)]
```

**Default:** 0 to 9999
**Purpose:** Realistic test data

## Dependencies

### Required Packages
- `qiskit`: Quantum computing framework
- `qiskit_aer`: Quantum simulator
- `matplotlib`: Visualization
- `time`: Timing functions
- `random`: Data generation

### Installation
```bash
pip install qiskit qiskit-aer matplotlib
```

### Version Compatibility
- Python 3.8+
- Qiskit 0.45+
- Matplotlib 3.0+