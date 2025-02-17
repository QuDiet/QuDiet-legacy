# quDiet
[![Python application](https://github.com/LegacYFTw/QuDiet/actions/workflows/python-app.yml/badge.svg?branch=main)](https://github.com/LegacYFTw/QuDiet/actions/workflows/python-app.yml)
![GitHub repo size](https://img.shields.io/github/repo-size/LegacYFTw/QuDiet)
[![License](https://img.shields.io/github/license/LegacYFTw/QuDiet)](https://github.com/LegacYFTw/QuDiet/blob/main/LICENSE)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![arXiv](https://img.shields.io/badge/arXiv-2211.07918-b31b1b.svg)](https://arxiv.org/abs/2211.07918)

Note that this is a legacy version of the QuDiet simulator. A fresh new version is currently in the works! Stay tuned!
A high performance simulator that scales and eats qudits for lunch. [Read More](https://arxiv.org/abs/2211.07918)

# Installation
A simple `pip` install from the github repository will install the package in your system.

## Install with pip
```bash
$ pip install git+https://github.com/LegacYFTw/QuDiet

```
## Install from source
```bash
$ git clone https://github.com/LegacYFTw/QuDiet && cd QuDiet
$ pip3 install .
```

# Examples
Here are some simple examples.

We start by importing the package:
```python
from qudiet.core.quantum_circuit import QuantumCircuit
```

The class `QuantumCircuit` is the main interface representing the abstraction over the complicated calculations.

## A 3-Qudit Example

Let's start with this 3-qudit example, where $|q_0\rangle$ is a qubit whereas $|q_1\rangle$ and $|q_2\rangle$ are qutrits.


![Higher Order Quantum Circuit](./docs/imgs/fig4.png)

First, we start with initializing a blank circuit with the class `QuantumCircuit`. 

```python
qc = QuantumCircuit(

    # Represents the dimension of each quantum register
    qregs=[2, 3, 3],


    # Represents the initial states
    init_states=[0, 0, 0],
)
```

At a glance, the class `qudiet.core.quantum_circuit.QuantumCircuit` looks quite similar to Qiskit's `QuantumCircuit` class, with an exception of the `qregs` defining the qubit dimension of the registers. 

Now that we have a blank circuit, we can place quantum gates like this, 

```python
# Places the CX gate with control qudit q0, q1 and target qudit q2
qc.cx([0, 1], 2)

# The measure_all definition represents the end of the definition of a QuantumCircuit 
qc.measure_all()
```

Now that we are done with the definition of our QuantumCircui, we can execute the circuit with the `.run()` definition.

```python
qc.run()
```

## Backends

QuDiet comes with 4 different kinds of backend.

```python
# The NumPy Backend is the default Backend
from qudiet.core.backend.NumpyBackend import NumpyBackend

# Sparse Backend works great when there are no H gates
from qudiet.core.backend.SparseBackend import SparseBackend

# Experimental - GPU

# CUDA Backend is the GPU equivalent of the NumPy Backend
from qudiet.core.backend.CUDABackend import CUDABackend

# CUDA Sparse Backend is the GPU equivalent of the Sparse Backend
from qudiet.core.backend.CUDASparseBackend import CUDASparseBackend
```

These Backends can be declared when initializing the circuit with the class `qudiet.core.quantum_circuit.QuantumCircuit` using the argument`backend=NumpyBackend`.
```python
qc = QuantumCircuit(
    qregs=[2, 3, 3],
    init_states=[0, 0, 0],

    # Here goes the backend specification
    backend=NumpyBackend,
)
```


## Run from QASM

A typical qasm looks like  
```qasm
# This is the File Header
# The File Header section is compulsory

# Defining a 3 Qudit circuit
.qubit 3
qudit x0 (2)
qudit x1 (3)
qudit x2 (3)

# The Gate composition will go in this
# section, between the .begin and the .end
.begin

# Your Gates Here ...
Toffoli x0, x1, x2

.end
```

To run a circuit from qasm, we need to import a few definitions,

```python
from qudiet.qasm.qasm_parser import circuit_from_qasm, parse_qasm
```

The `circuit_from_qasm` definition creates the circuit from the qasm `str`. Whereas, the `parse_qasm` definition creates the circuit directly from the qasm file definition.

```python
# Loading the circuit from the qasm string
circuit = circuit_from_qasm(
    '''
    # This is the File Header
    
    .qubit 3
    qudit x0 (2)
    qudit x1 (3)
    qudit x2 (3)

    .begin
    Toffoli x0, x1, x2
    .end
    '''
)

# Now we can run the circuit however we want.
circuit.run()
```

The `parse_qasm` definition can be used as such.
```python
# Loading the circuit from a qasm file
filename = "test.qasm"
circuit = parse_qasm(filename, SparseBackend)

# Now we can run the circuit however we want.
result = circuit.run()
```

## Adding Arbitary Gates
Custom arbitary gates can be defined as a child class of `qudiet.circuit_library.ArbitaryGate`.
```python
from qudiet.circuit_library import ArbitaryGate

class GateXYZ(ArbitaryGate):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self._unitary = np.array(
            [
                [1, 0, 0], 
                [0, 1, 0], 
                [0, 0, 1], 
            ]
        )
```

Once the gate id defined, it can be applied to a circuit as such,

```python

# Defining a Quantum Circuit
qc = QuantumCircuit(
    qregs=[2, 3, 4, 3, 2],
    init_states=[1, 2, 2, 0, 1],
)

# The definition QuantumCircuit.gate() applies an Arbitary gate 
# QuantumCircuit.gate( ArbitaryGate , Register/s )
qc.gate(GateXYZ, 3) # Here, we are applying the gate `GateXYZ` to the fourth register, i.e. qreg-3

```

## Bibliography
```
@article{chatterjee2022qudiet,
  title={QuDiet: A Classical Simulation Platform for Qubit-Qudit Hybrid Quantum Systems},
  author={Chatterjee, Turbasu and Das, Arnav and Bala, Subhayu Kumar and Saha, Amit and Chattopadhyay, Anupam and Chakrabarti, Amlan},
  journal={arXiv preprint arXiv:2211.07918},
  year={2022}
}
```
