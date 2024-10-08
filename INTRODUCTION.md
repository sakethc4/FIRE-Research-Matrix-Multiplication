# Introduction to QFT Arithmetic

Here, we will explain how quantum addition and multiplication work and provide quantum circuits for both.

## Storing Integers

Similar to how classical computers store numbers, quantum computers—at least for the purpose of addition and multiplication—store integers in binary. Classical computers usually store integers using a fixed number of bits, typically either 32 or 64 bits. However, since qubits are a much more limited resource than bits, quantum computers store a reduced range of numbers, generally well below 32 qubits per integer.

Although qubits are probabilistic by nature, the initial and final integer states for quantum arithmetic circuits are designed NOT to be in a superposition; assuming no quantum noise, only one measurement is necessary to get the result of a single arithmetic operation. Superposition is only used internally within the arithmetic circuits.

## Implementations of Addition and Multiplication

Classical adders use half and full adder circuits. Classical adders require no encoding/preprocessing of the input numbers and use a combination of classical XOR, AND, and OR gates to add numbers together. A similar circuit using half and full adders can be constructed on a quantum computer:

![A quantum circuit for a full adder.](https://www.researchgate.net/publication/346508632/figure/fig1/AS:963729009156096@1606782309965/Circuit-model-of-a-quantum-full-adder.png)

However, the three-qubit gates used in such a quantum adder circuit are very computationally expensive. If the Quantum Fourier Transform (QFT) is used to encode/preprocess inputs first, no three-qubit gates are necessary and the new adder circuit is substantially faster:

(insert circuit)

The fourier transform decomposes data into their constituent frequencies. Since complex numbers represent rotations around the unit circle, these frequencies are stored as complex phases. On classical computers, superposition does not exist and bits are restricted to 0 or 1; for this reason, multiple bits are required to store just a single complex number. However, on quantum computers, qubits can be rotated; only one qubit is needed to store a single complex number. Because of superposition and the compact nature of the QFT, fourier transformed arithmetic circuits on provide speed ups on quantum computers that would not be possible on classical computers.

## How the QFT Works

For the QFT, we partition the complex plane into multiple phases, one unique phase for each quantum state (note that if there are "n" input qubits, there are "2^n" input states). We define:

![](/images/QFT_phase_partition.png)

Where omega is both the smallest possible phase in the QFT and the phase between any phase and the next largest phase. Formally, the QFT is defined as:

![](/images/QFT_definition.png)

To understand the QFT, we break the input state "a" and the output state "b" into their individual qubits (in order to build a circuit with gates operating on/between qubits, we need to see how the QFT acts on each individual qubit):

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

If we decompose "m" and "b" in the second phase into their individual qubits using the same double sum as before, we find that we get similar cancellations, leading to the same exact n-k-1 staircase of controlled rotations as before. As such, the addition circuit looks nearly identical to the QFT circuit, but with the Hadamard gates removed:

(insert circuit)

## How QFT Multiplication Works

Since classical computers don’t have built in multiplication circuits (like they do adders). They need to do repeated addition in order to efficiently perform multiplication operations. 

![](/images/QFT_MULT_EXAMPLE.png)

To emulate this repeated addition action we can create a quantum multiplier that follows the following steps: 
Get the multiplicand and the multiplier (this will just be user input)
Create a quantum register to use as the accumulator 
Add the multiplicand to the accumulator using quantum fourier based transforms 
Decrement the multiplier using quantum fourier based subtraction
Repeat this until the multiplier reaches 0. 
Now if we just output the value of the accumulator we get the product of the two input numbers. 

Since there is no “control” that enables us to add a gate on a specific value held in a register we can implement a c_if line telling the quantum computer to apply an “X” gate to the first qubit of the register only if the value of the bit string stored in the classical register is equal to |00…01>. This notation is used in quantum computing to represent a state where 00…01 represents the state of a number of qubits. An example to better understand this is |00001> where the first 4 qubits are in the 0 state and the last qubit is in the state 1. Once we find this meaningful state |00…01> we apply the “X” gate in order to flip the qubits (it’s pretty much a classical NOT gate). This flip (based on the necessary state we need the qubits to be in) forms the foundation for constructing the more complex operations (controlled gates and addition circuits) in the rest of the matrix multiplication process. 

The Universal equation for matrix multiplication is as follows: 

![](/images/UNIVERSAL_MATRIX_MULT_EQ.png)
 
This approach is a naive approach to matrix multiplication where we are essentially going through and multiplying every row in the first matrix with every column in the second. Essentially Product = A dot B. This approach is implemented using the adders and multipliers as previously mentioned. 


## Resource Consumption

Suppose we are operating on an n-digit number a and an m-digit number b. For QFT addition, we need m+n+1 qubits total: m qubits to store a, n qubits to store b, and an extra qubit to prevent overflow/ensure the result a+b has enough qubits to be stored. However, only one of the numbers being added—which we'll assume is number a—needs to be put through the QFT/inverse QFT; the other number b remains constant and only serves as controls for rotations on a. As the original paper describes, if b is directly encoded into the rotation gates themselves instead of used as controls, the m qubits of b become redundant and the entire adder only needs n+1 qubits total.
