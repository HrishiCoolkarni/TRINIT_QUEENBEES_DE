# TRINIT_QUEENBEES_DE

We chose to go with DE02 track as our problem statement for TRINIT Hackathon<br>

The problem statement is to design a digital communication system based on
convolutional codes. The problem statement is 3-fold:
1. You have to design a convolutional code encoder circuit.
2. You have to design a circuit that simulates channel noise.
3. You have to design a convolutional code decoder circuit.<br>

**Convolutional Code Encoder**: The circuit must parallelly input a 4-bit message
sequence. The parallel message sequence is fed serially (LSB first) to the input of
the Convolutional Code Encoder. The encoder has a constraint length of 3, and
parity equations are as follows:<br>
p0[n] = x[n] + x[n-1] + x[n-2]<br>
p1[n] = x[n] + x[n-2]<br>
The parity bits generated above are transmitted serially, such that the p0 bit is
transmitted first. For example p0(0) is transmitted, then p1(0), then p0(1), then
p1(1), and so on.<br><hr/>

 
 We implemented a 4 bit PISO(Parallel In Serial Out) Shift Register to feed in the data(LSB first) to the input Covolutional Code Encoder.<br>
 ![piso.png](https://github.com/HrishiCoolkarni/TRINIT_QUEENBEES_DE/blob/main/piso.png)<br>
 
 ## Convolutional code overview:<br>
 Here, the first d-flipflop is storing the x[n-1] state and the next d-flipflop is storing the x[n-2] state
 To further concatenate the two parity bits encoded for each incoming input bit, we have a 2 bit PISO shift register, which will feed in p0[n]p1[n] to the noisy_channel<br>
 ![encoding.png](https://github.com/HrishiCoolkarni/TRINIT_QUEENBEES_DE/blob/main/encoding_ch.png)<br><hr/>
 
**Channel Noise Simulator**: The circuit must accept the output of the
Convolutional Code Encoder, and for each code word received, it should
introduce one bit of error. It must comprise a pseudo-noise sequence counter
that counts in the following order and then repeats:
8, 11, 5, 1, 2, 7, 0, 4, 3, 9, 10, 6 <br>
 
Truth Table for Pseudo-Noise Sequence:<br>
![truthTable](https://github.com/HrishiCoolkarni/TRINIT_QUEENBEES_DE/blob/main/PN_seq_truthTable.png)<br>

The logisim circuit:<br>
![PN_seq.png](https://github.com/HrishiCoolkarni/TRINIT_QUEENBEES_DE/blob/main/PN_sequence.png)<br>

The logic for the noisy channel implementation was to produce a <b>4-bit UpCounter</b> and these 4 bits are compared with the Pseduo-Noise Sequence Counter, once all 
the bits matches <b> we increment the Pseudo-Noise Sequence counter to next value and reset the UpCounter to 0 </b>(added an extra D-flipflop to align the data at the PSeudo-
Noise sequence and the upcounter).<br>
Once the comparator signal goes high, this signal is connected to the select line of 2to1 Multiplexer which will act as an XOR gate, which will flip the incoming bit inturn
introducing a noise to our encoded signal.</b><hr/>

## submodules<br>
4-bit Comparator<br>
![comparator.png](https://github.com/HrishiCoolkarni/TRINIT_QUEENBEES_DE/blob/main/comparator.png)<br>

2to1 Multiplexer<br>
![mux.png](https://github.com/HrishiCoolkarni/TRINIT_QUEENBEES_DE/blob/main/2to1mux.png)<br>

4-bit UpCounter<br>
![Upcounter.png](https://github.com/HrishiCoolkarni/TRINIT_QUEENBEES_DE/blob/main/upcounter.png)<br>

### noisy_channel<br>
![noisy_Channel.png](https://github.com/HrishiCoolkarni/TRINIT_QUEENBEES_DE/blob/main/nosiy_channel.png)<br>

Noisy Channel ( the above circuit ) makes the necessary alteration to the encoded input that comes in. It works as follows
- The Pseudo-Noise Sequence (PSN) , produces its output, after which it momentarily frezzes before moving to its next state
- The sub-counter ( an up counter ) begins counting from its 0000 state till the number that the PSN produces
- When the comparator turns 1, ie, when the up counter and the PSN value match, the signal bit is flipped, hence the noise is introduced
- Basically the sub-counter counts till the bit that needs to be changed arrives, it shifts until then, once that particular bit arrives, the circuit makes the change it is supposed to the encoded signal.
- Once the bit is changed, the PSN moves onto its next state while the sub-counter resets back into 0000 state ( ie, the initial state for an upcounter )

