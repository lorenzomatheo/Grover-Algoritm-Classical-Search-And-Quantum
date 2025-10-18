# Quantum Search Algorithm Comparison

A comprehensive demonstration comparing classical search algorithms with Grover's quantum search algorithm using Qiskit.

## Overview

This project implements and compares two search approaches:

1. **Classical Search**: Traditional linear search through an unsorted list
2. **Grover's Algorithm**: Quantum search algorithm that provides quadratic speedup

The implementation demonstrates the fundamental differences between classical and quantum computing paradigms for search problems.

## Features

- 🔍 **Classical Linear Search**: O(n) time complexity brute force search
- ⚛️ **Grover's Quantum Search**: O(√n) time complexity quantum search
- 📊 **Performance Comparison**: Side-by-side timing and efficiency analysis
- 📈 **Visualization**: Histogram plots of quantum measurement results
- 🧮 **Configurable Parameters**: Adjustable list sizes and search targets

## Prerequisites

- Python 3.8+
- Jupyter Notebook
- Qiskit and Qiskit Aer
- Matplotlib

## Installation

1. Clone or download this repository
2. Install dependencies:

```bash
pip install qiskit qiskit-aer matplotlib
```

Or using uv (as shown in the notebook):

```bash
uv pip install qiskit qiskit-aer matplotlib
```

## Quick Start

1. Open `listadesordenada.ipynb` in Jupyter Notebook
2. Run all cells to see the comparison between classical and quantum search
3. Modify the parameters (N, target values) to experiment with different scenarios

## Project Structure

```
├── listadesordenada.ipynb    # Main Jupyter notebook with implementations
├── README.md                 # This file
├── docs/                     # Additional documentation
│   ├── quantum-algorithms.md
│   ├── api-reference.md
│   ├── performance-analysis.md
│   └── tutorials.md
└── requirements.txt          # Python dependencies
```

## Key Concepts

### Classical Search
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Method**: Linear traversal through all elements
- **Best Case**: O(1) - target found at first position
- **Worst Case**: O(n) - target found at last position or not found

### Grover's Algorithm
- **Time Complexity**: O(√n)
- **Space Complexity**: O(log n) for n qubits
- **Method**: Quantum superposition and interference
- **Key Components**:
  - Oracle: Marks the target state
  - Diffuser: Amplifies the marked state
  - Iterations: ~π/4 × √n rounds

## Usage Examples

### Basic Classical Search
```python
# Create random data
data = [random.randint(0, 9999) for _ in range(1024)]
target = data[random.randint(0, len(data) - 1)]

# Perform search
index = classical_search(target, data)
```

### Grover's Quantum Search
```python
# Search for a 3-qubit state
grover_search(num_qubits=3, target_state="101")
```

## Performance Analysis

The project includes detailed performance metrics:

- **Execution Time**: Precise timing using `time.perf_counter()`
- **Items Checked**: Number of elements examined
- **Success Rate**: Probability of finding the target
- **Quantum Measurements**: Distribution of measurement outcomes

## Results Interpretation

### Classical Search Results
- Shows linear scaling with input size
- Predictable performance characteristics
- Deterministic outcome

### Quantum Search Results
- Shows quadratic speedup for large datasets
- Probabilistic outcomes (measurement results)
- Requires multiple shots for reliable results

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is open source and available under the MIT License.

## References

- [Qiskit Documentation](https://qiskit.org/documentation/)
- [Grover's Algorithm - Wikipedia](https://en.wikipedia.org/wiki/Grover%27s_algorithm)
- [Quantum Computing for Computer Scientists](https://www.cambridge.org/core/books/quantum-computing-for-computer-scientists/)

## Support

For questions or issues, please open an issue in the repository or contact the maintainers.

---

**Note**: This project is for educational purposes and demonstrates quantum computing concepts. For production quantum applications, consider using actual quantum hardware or more sophisticated simulators.