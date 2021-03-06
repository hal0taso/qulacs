 
# Qulacs
[![Build Status](https://travis-ci.org/qulacs/qulacs.svg?branch=master)](https://travis-ci.org/qulacs/qulacs)

Qulacs is a python/C++ library for fast simulation of large, noisy, or parametric quantum circuits.

Qulacs is licensed under the [MIT license](https://github.com/qulacs/qulacs/blob/master/LICENSE).

## Quick Install

```
pip install qulacs
```

If you have NVIDIA GPU with CUDA installed try:
```
pip install qulacs-gpu
```

## Feature
- Fast quantum circuit simulation with parallelized C/C++ backend
- Noisy quantum gate for simulation of NISQ devices
- Parametric quantum gates for variational methods
- Circuit compression for fast simulation
- GPU support for fast simulation
- Many utility functions for research

## Performance
- Compared processing time with following libraries on October 1st, 2018
    - Qulacs CPU & GPU (ours)
    - [Cirq](https://github.com/quantumlib/Cirq)
    - [ProjectQ](https://github.com/ProjectQ-Framework/ProjectQ)
    - [pyQuil](https://github.com/rigetticomputing/pyquil)
    - [Q#](https://github.com/Microsoft/Quantum)
    - [Qiskit Terra QASM Simulator](https://github.com/Qiskit/qiskit-terra/tree/master/src/qasm-simulator-cpp)
    - [Qiskit Aer](https://github.com/Qiskit/qiskit-aer)
    - [QuPy CPU & GPU](https://github.com/ken-nakanishi/qupy)

- Test environment:
    - 100 shot sampling of 10 layers of all random rotation X gate and 9 layers of all neighboring CNOT
    - Intel Core i7-8700 CPU
    - NVIDIA GTX 1050 Ti GPU
    - OpenMP enabled
    - MKL enabled (numpy runs in multi thread)
    - Circuit compression disabled
    
![benchmark](https://storage.googleapis.com/qunasys/Qulacs_bench.png)

[QuEST](https://github.com/quest-kit/QuEST) and [qHiPSTER](https://github.com/intel/Intel-QS) is also fast circuit
simulator but we excluded since it doesn't have python interface.

## Supported environment
Qulacs is tested on the following systems.

- OS
  - Ubuntu 16.04
  - MacOS X Sierra
  - Windows 10

The following languages are supported.

- C++
  - gcc/g++ >= 7.0.0
  - Microsoft VisualStudio C++ 2015, 2017
- python
  - python 2.7
  - python 3.x

If you want to use GPU, install CUDA >= 8.0.

## Install from Source
If you encounter some troubles, see [troubleshooting (Japanese)](http://qulacs.org/md_4__trouble_shooting.html).
Currently, if you want to use GPU, qulacs must be installed from source.

### Requirements

- C++
    - gcc/g++ >= 7.0.0  (for python on Unix platforms in Linux, MacOS, or Windows)
    - Microsoft VisualStudio C++ 2015 or 2017   (for other python on Windows)
- python 2.7 or 3.x
- cmake >= 3.0
- (optional) CUDA >= 8.0

### Install qulacs from source

Install
```
git clone https://github.com/qulacs/qulacs.git
cd qulacs
python setup.py install
```

Uninstall
```
pip uninstall qulacs
```

### Build C++ and python library

#### GCC
```
git clone https://github.com/qulacs/qulacs.git
cd qulacs
./script/build_gcc.sh
```

When you want to build with GPU, use <code>build_gcc_with_gpu.sh</code>.

#### MSVC
```
git clone https://github.com/qulacs/qulacs.git
cd qulacs
script/build_msvc.bat
```

When you want to build with GPU, use <code>build_msvc_with_gpu.bat</code>.

## Tutorial and API document

See the following documents for more detail.

- [Python Tutorial](http://qulacs.org/md_3__tutorial_python.html)
- [C++ Tutorial](http://qulacs.org/md_2__tutorial__c_p_p.html)  
- [Examples](https://github.com/qulacs/quantum-circuits)  
- [API document](http://qulacs.org/annotated.html)   

## Sample code
### Python
```python
from qulacs import Observable, QuantumCircuit, QuantumState
from qulacs.gate import Y,CNOT,merge

state = QuantumState(3)
state.set_Haar_random_state()

circuit = QuantumCircuit(3)
circuit.add_X_gate(0)
merged_gate = merge(CNOT(0,1),Y(1))
circuit.add_gate(merged_gate)
circuit.add_RX_gate(1,0.5)
circuit.update_quantum_state(state)

observable = Observable(3)
observable.add_operator(2.0, "X 2 Y 1 Z 0")
observable.add_operator(-3.0, "Z 2")
value = observable.get_expectation_value(state)
print(value)
```

If you want to run it on GPU, install qulacs from source and replace <code>QuantumState</code> with <code>QuantumStateGpu</code>.

### C++

```cpp
#include <iostream>
#include <cppsim/state.hpp>
#include <cppsim/circuit.hpp>
#include <cppsim/observable.hpp>

int main(){
    QuantumState state(3);
    state.set_Haar_random_state();

    QuantumCircuit circuit(3);
    circuit.add_X_gate(0);
    auto merged_gate = gate::merge(gate::CNOT(0,1),gate::Y(1));
    circuit.add_gate(merged_gate);
    circuit.add_RX_gate(1,0.5);
    circuit.update_quantum_state(&state);

    Observable observable(3);
    observable.add_operator(2.0, "X 2 Y 1 Z 0");
    observable.add_operator(-3.0, "Z 2");
    auto value = observable.get_expectation_value(&state);
    std::cout << value << std::endl;
    return 0;
}
```

Build command for g++:
```sh
g++ -O2 -I ./<qulacs_path>/include -L ./<qulacs_path>/lib <your_code>.cpp -fopenmp -lcppsim_static.so
```

If you want to run it on GPU, include <code>cppsim/state_gpu.hpp</code> and replace <code>QuantumState</code> with <code>QuantumStateGpu</code>.

