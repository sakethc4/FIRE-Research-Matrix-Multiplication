# Introduction to QFT Arithmetic

Here, we will explain how quantum addition and multiplication work and provide quantum circuits for both.

## Storing Integers

Similar to how classical computers store numbers, quantum computers—at least for the purpose of addition and multiplication—store integers in binary. Classical computers usually store integers using a fixed number of bits, typically either 32 or 64 bits. However, since qubits are a much more limited resource than bits, quantum computers store a reduced range of numbers, generally well below 32 qubits per integer.

Although qubits are probabilistic by nature, the initial and final integer states for quantum arithmetic circuits are designed NOT to be in a superposition; assuming no quantum noise, only one measurement is necessary to get the result of a single arithmetic operation.

## Implementations of Addition and Multiplication

Classical adders use half and full adder circuits. Classical adders require no encoding/preprocessing of the input numbers and use a combination of classical XOR, AND, and OR gates to add numbers together. A similar circuit using half and full adders can be constructed on a quantum computer:

![A quantum circuit for a full adder.](https://www.researchgate.net/publication/346508632/figure/fig1/AS:963729009156096@1606782309965/Circuit-model-of-a-quantum-full-adder.png)

However, the three-qubit gates used in such a quantum adder circuit are very computationally expensive. If the Quantum Fourier Transform (QFT) is used to encode/preprocess inputs first, no three-qubit gates are necessary and the new adder circuit is substantially faster:

(insert circuit)

The fourier transform decomposes data into their constituent frequencies. Since complex numbers represent rotations around the unit circle, these frequencies are stored as complex phases. On classical computers, superposition does not exist and bits are restricted to 0 or 1; for this reason, multiple bits are required to store just a single complex number. However, on quantum computers, qubits can be rotated; only one qubit is needed to store a single complex number. Because of superposition and the compact nature of the QFT, fourier transformed arithmetic circuits on provide speed ups on quantum computers that would not be possible on classical computers.

## How the QFT Works

Formally, the QFT is defined as:

![](/images/QFT_definition.png)

First, we partition the complex plane into multiple phases, one unique phase for each quantum state (note that if there are "n" input qubits, there are "2^n" input states). We define:

![](/images/QFT_phase_partition.png)

Then, we break the input state "a" and the output state "b" into their individual qubits (in order to build a circuit with gates operating on/between qubits, we need to see how the QFT acts on each individual qubit):

![](/images/QFT_expansion.png)
*Notice how we are summing between the 0th to (n-1)th qubits of each state. Each sum contains n terms, one term for each qubit.*

Next, we can actually ignore certain terms in the summation that are integer multiples of 2π, because full rotations in the complex plane get you back where you started. In particular, we only need to include terms such that j+k-n<0, or equivalently j<n-k:

![](/images/QFT_expansion_simplification.png)

We now have an expansion for each qubit. If we look at the last qubit, we notice that the operation is a single Hadamard gate:

![](/images/QFT_last_qubit.png)

If you examine other terms, say the kth qubit, you find that you get a Hadamard transformation accompanied by n-k-1 controlled rotations between other qubits. Translating the Hadamard gates and controlled rotation gates to an actual circuit yields the entire QFT circuit:

(insert circuit)

## How QFT Addition Works

If we try to add numbers in the QFT, we get:

![](/images/QFT_addition.png)

If we decompose "m" and "b" in the second phase into their individual qubits using the same double sum as before, we find that we get similar cancellations, leading to the same exact n-k-1 staircase of controlled rotations as before. As such, the addition circuit looks nearly identical to the QFT circuit, but with the Hadamard's removed:

(insert circuit)

## How QFT Multiplication Works