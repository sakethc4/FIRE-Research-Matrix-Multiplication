# A Quantum Approach to Optimize Classical Machine Learning Operations

## Introduction
In school, addition, multiplication, and other arithmetic operations are among the first mathematical concepts we learn—and for good reason. Countless fields like machine learning, scientific computing, and computer graphics rely on repeated addition, multiplication, and matrix multiplication—everything from neural networks to projecting three-dimensional objects onto your two-dimensional screen. However, when machine learning models rely on millions or billions of additions, multiplications, and matrix multiplications, the time complexity of each operation quickly adds up; this led our team to search for quantum ways to bring down the cost of matrix multiplication and other important basic arithmetic operations. Unlike classical computers, which can only store two states in each bit of data, quantum computers can store a whole continuum of values in each qubit; superposition allows for entirely new addition and multiplication algorithms otherwise impossible on a classical computer.

Our research builds on an innovative approach leveraging Quantum Fourier Transform based optimized adders and multipliers. We successfully replicated the reference paper’s original results, confirming the promising efficiency of quantum matrix multiplication. Building on this foundation, we used a similar approach  to optimize machine learning activation functions; we created a quantum circuit that approximates the sigmoid function, one of the most commonly used activation functions. By applying quantum addition and multiplication to the sigmoid function, our research highlights the advantages of quantum-optimized computation to significantly accelerate costly machine learning tasks. 

## Methods 
### Dependencies
Like the reference paper, we used Pennylane (version **0.33.1**) and Qiskit (version **0.33.1**) to create our quantum circuit. All code has been tested on Google Colab.

### QFT

### QFT Addition 
Classical computers have built in adders; classical addition is fairly straightforward. Classical addition involves converting inputs into binary and using a full adder circuit (using AND, OR, & XOR gates). 
QFT addition differs from classical in that it leverages a QFT to encode numbers in phases of quantum states rather than just binary bits. The equations below demonstrate QFT addition for two values (a, m). The first equation QFT | a creates a superposition of states | b with each stage having a weight of w^ab. Essentially this is just transforming the input to a Fourier basis where the w value is just the amplitude of the sinusoidal function we generate after transforming the input. The next step is to actually add the two values essentially by taking that w value and setting it w ^ (a+m) b where instead of just classically computing the sum of the two values we are manipulating the phases. The last equation combines both our new and old w values to mimic classical addition essentially leveraging controlled phase shifts based on m to a. The last step is to use an inverse QFT to get the sum of a + m in the computational basis. 

Essentially we use QFT to transform the input then apply a sequence of controlled phase 
rotations. Then for each bit in our m variable we apply a controlled rotation to each bit producing a phase shift w^mb which is dependent on the value of m. After we sum up all of these controlled rotations we get a + b on the Fourier basis. Now we just need to do an inverse QFT and we convert the result back to the computational basis and get the same exact solution as just adding a + b. Leveraging quantum superposition enables us to compute addition for large inputs more efficiently as opposed to classical addition. 

![](/images/classicalsum.png)

![](/images/quantumsum.png)
 
### QFT Multiplication
In order to complete the QFT Multiplication, we used PennyLane.
Classical computers do not have built-in multiplication circuits and instead use repeated addition to perform multiplication. In order to multiply via repeated addition, we:

Load the number 0 into an accumulator register and apply the QFT to it
Add a multiplicand to the accumulator (perform a rotation on each accumulator qubit)
Repeat the previous step for all pairs of bits of the inputs
Apply the inverse QFT to the accumulator
Extract the product of the two numbers, now stored in the accumulator



## Dataset and Preprocessing
For our results we did not leverage a traditional dataset instead we manipulated our code to create outputs for different qubit inputs. This allowed us to benchmark the effectiveness of matrix multiplication for differing qubit sizes. When calculating results for our quantum sigmoid computations we leveraged varying x values to determine quantum sigmoid values. After running our code with varying x values we then compared the output we got to actual sigmoid values to determine how effective our computations were. Both “datasets” we leveraged had a total of 5 features. 

All the data mentioned is visualized in depth as shown in our results section containing graphs and tables for both matrix multiplication and quantum sigmoid computation. 

Different qubit inputs for matrix multiplication
Different quantum sigmoid x values

## Results 
Graphs for matrix multiplication

![](/images/5qbtotal.png)
![](/images/5qbmul.png)

Graphs for sigmoid x values

![](/images/sigpos.png)
![](/images/signegative.png)

Actual QPU or simulations

![](/images/5qbtotalionq.png)
![](/images/5qbmulionq.png)


Key Findings: Present your main results using clear and concise visuals (e.g., graphs, charts, tables).
Performance Metrics: Highlight the key performance metrics (accuracy, precision etc).
As the amount of qubits we tested our circuit on increased, the more effective our optimized multiplication algorithm became, leading to results showing very significant improvement in computation time. However, on the IonQ simulator, we saw results that were random and unexpected. This disparity between our original results likely occurred due to the quick processes being run on a server.

## Conclusion (Justin)
As demonstrated, our quantum sigmoid function accurately approximates sigmoid values. When paired with quantum matrix multiplication algorithms, quantum activation functions may allow classical neural networks to be trained significantly faster using quantum hardware rather than classical hardware. However, as mentioned earlier, arithmetic operations and matrix multiplication are used in countless fields other than machine learning; the same techniques used for the quantum sigmoid (e.g. QFT addition, multiplication, and comparison) can be applied to speed up computation of a broad spectrum of other functions. Further research is needed to implement other functions—and possibly their derivatives, as required by gradient descent algorithms—on quantum circuits. Additionally, our function’s accuracy could be improved, either by using more/finer intervals or some other method.

## References
Matrix Multiplication on Quantum Computer (Jiaqi Yao and Ding Liu) - https://arxiv.org/pdf/2408.03085
