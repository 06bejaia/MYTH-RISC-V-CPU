
An introduction to TL-Verilog was done and we implemented basic combinational and sequential logic using the same.This day finally ended with an implementation of a sequential cyclic calculator. For this, Makerchip IDE, which is an open source tool developed by Redwood EDA has been utilised.

TL-Verilog is an extension for System Verilog, moreover it acts as an higher level abstraction for System verilog which makes HDL implementation very easy and error free. Here we deal the design at a transaction level assuming the design as a pipeline, where inputs would be provided and output will be generated at the end of the pipeline.

Advantages :

Code reduction , and thus less chances of being bug prone.
In pipelining ,the flip flops,registers and other staged signals are implied from the context.
It is very easy to stage different sections without impacting the behaviour of the logic.
Validity feature which provides easier debugging, cleaner design, automated clock gating and better error checking capabilities.
Lab Exercises:
1. Combinational Logic Example: Full Adder


2. Combinational Calculator
![combinational_logic_calculator](https://user-images.githubusercontent.com/67355283/166132677-3289b04f-2434-4b57-873f-545970c6d03d.PNG)


3. Sequential Logic Example: Fibonacci Series
4. ![fibonacci-sequential](https://user-images.githubusercontent.com/67355283/166132680-8dbbcf04-71b2-40ff-9e07-48261702ee80.PNG)



4. Sequential Calculator
5. ![sequential_calculator](https://user-images.githubusercontent.com/67355283/166132687-b8d3bdd4-ac66-4576-b56b-dd2930e643b7.PNG)

5. 2-Cycle Calculator with validity
![CYCLE-CALCULATOR](https://user-images.githubusercontent.com/67355283/166132718-df368d79-968a-47f2-94cb-89146c62e6c3.PNG)


Note As seen above, apart from the waveforms, The VIZ graphic visualizer was a very helpful tool in Makerchip which helped us analyse and debug our design in case of any functional errors.

10. Calculator with Single Value Memory

![image](https://user-images.githubusercontent.com/67355283/166132732-fd9d8143-d21e-44ee-8713-23c4e403f1c8.png)
