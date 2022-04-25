# MYTH-RISC-V-CPU

Intoduction to RISC-V CPU arcchitecture:

RISC-V is an ISA, so it doesn't talk about how to implement the actual design. CPU designers take the ISA as their input and decide how to implement hardware (almost always) in RTL. In this day a single cycle design of the ISA will be implemented named MYTH. MYTH will become pipeline in the next day. Here is a schematic of the design


Fetch

The basic operation of a computer is called the ‘fetch-execute’ cycle. The CPU is designed to understand a set of instructions - the instruction set. It fetches the instructions from the main memory and executes them. This is done repeatedly from when the computer is booted up to when it is shut down.

![1](https://user-images.githubusercontent.com/67355283/165024096-cfbce379-92bc-453e-a46b-cb758725610f.PNG)


Decode

Decode stage The decoding process allows the CPU to determine what instruction is to be performed so that the CPU can tell how many operands it needs to fetch in order to perform the instruction. The opcode fetched from the memory is decoded for the next steps and moved to the appropriate registers.

![2](https://user-images.githubusercontent.com/67355283/165024199-9611fcfd-9855-4723-96d3-9f6a1304e9cc.PNG)

Register File

A register file is a means of memory storage within a computer's central processing unit (CPU). The computer's register files contain bits of data and mapping locations. These locations specify certain addresses that are input components of a register file. Other inputs include data, a read and write function and execute function.

Excute

The control unit of the CPU passes the decoded information as a sequence of control signals to the relevant functional units of the CPU to perform the actions required by the instruction, such as reading values from registers, passing them to the ALU to perform mathematical or logic functions on them, ...

Write register File
A register file is an array of processor registers in a central processing unit (CPU). Register banking is the method of using a single name to access multiple different physical registers depending on the operating mode. Modern integrated circuit-based register files are usually implemented by way of fast static RAMs with multiple ports. Such RAMs are distinguished by having dedicated read and write ports, whereas ordinary multiported SRAMs will usually read and write through the same ports.
