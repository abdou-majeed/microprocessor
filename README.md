# microprocessor

Design of a 4-bit microprocessor from first principles, down to logic gates.

This is a personal project I started during my BSc in Electrical/Electronic Engineering
at KNUST (Ghana). The goal is to design a complete 4-bit computer: ALU, registers,
buses, control unit, memory interface, instruction set, and fetch-execute cycle,
all built from basic logic gates.

The project has two parts:

- **Part A** (documentation): a written walkthrough of every component, from transistors
  and tri-state buffers up to the full fetch-execute cycle, with block diagrams,
  truth tables, pseudo-assembly examples, and a custom instruction set.
- **Part B** (circuits): logic gate implementations of each component using
  [Logicly](https://logic.ly/), a digital circuit simulator.


## Architecture

The microprocessor has a 4-bit data bus, a 6-bit address bus, and an 8-bit wide memory.
The CPU contains:

| Component | Description |
|---|---|
| **ALU** | 9 operations: ADD, SUB, AND, OR, NOT, SLT, INA (increment), DEA (decrement), CMA (complement). |
| **Registers** | Accumulator (reg A), buffer register (reg B), instruction register, memory address register, program counter, flags (zero, carry). |
| **Control Unit** | Decodes instructions into micro-operations and sequences them across clock cycles. |
| **Internal bus** | 4-bit bus with tri-state buffers for component isolation. |


## Instruction set

16 instructions encoded as 4-bit opcodes:

| Opcode | Mnemonic | Description |
|--------|----------|-------------|
| 0000 | LDA addr | Load register A from memory |
| 0001 | STA addr | Store register A to memory |
| 0010 | ADD addr | Add memory contents to reg A |
| 0011 | SUB addr | Subtract memory contents from reg A |
| 0100 | AND addr | Bitwise AND with memory contents |
| 0101 | OR addr  | Bitwise OR with memory contents |
| 0110 | NOT      | Bitwise NOT of reg A |
| 0111 | SLT addr | Set reg A = 1 if reg A < memory contents |
| 1000 | MOV val  | Load immediate value into reg A |
| 1001 | JZ addr  | Jump if zero flag is set |
| 1010 | JC addr  | Jump if carry flag is set |
| 1011 | INA      | Increment reg A |
| 1100 | DEA      | Decrement reg A |
| 1101 | CMA      | Complement reg A |
| 1110 | JMP addr | Unconditional jump |
| 1111 | HLT      | Halt processor |


## Repository structure

```
microprocessor/
|-- README.md
|-- docs/
|   |-- MicroProcessor_2021.pdf          # Revised documentation (February 2021)
|   |-- MicroP_Part_A_2019.pdf           # Original documentation (January 2019)
|   |-- MicroP_Part_B_2019.pdf           # Circuit descriptions
|   +-- MicroP_Presentation_2019.pptx    # Presentation slides
+-- circuits/
    |-- ALU_1_AND.logicly
    |-- ALU_2_OR.logicly
    |-- ALU_3_NOT.logicly
    |-- ALU_4_SLT.logicly
    |-- ALU_5_INA.logicly
    |-- ALU_6_DCA.logicly
    |-- ALU_7_ADD.logicly
    |-- ALU_8_SUB.logicly
    |-- ALU_SUB.logicly
    |-- Register.logicly
    |-- CU.logicly
    |-- Complete_Decoder.logicly
    |-- Decrement_Decoder.logicly
    |-- Set_Less_Than_Decoder.logicly
    |-- Complement_A_Decoder.logicly
    |-- AND.logicly
    |-- AND_with_Tri-State_Buffer.logicly
    |-- AND_OR_with_Tri-State_Buffer.logicly
    |-- Decoding_with_AND.logicly
    |-- Decoding_with_AND_OR.logicly
    |-- Synchronization_1.logicly
    |-- Synchronization_2.logicly
    +-- Zero_Flag.logicly
```


## Circuit files

The circuits are built in [Logicly](https://logic.ly/), a digital logic simulator.
A free trial is available. The files are organized by component:

| Group | Files | Description |
|-------|-------|-------------|
| **ALU operations** | ALU_1 through ALU_8, ALU_SUB | Individual arithmetic and logic operations at the gate level. |
| **Decoders** | Decrement, SLT, Complement_A, Complete | Instruction decoders that route control signals to the correct ALU operation. |
| **Tri-state buffers** | AND, AND_with_Tri-State_Buffer, AND_OR variants | Bus isolation circuits that prevent components from interfering with each other. |
| **Control and state** | CU, Register, Synchronization_1, Synchronization_2, Zero_Flag | Control unit, register design, clock synchronization, and status flags. |

The circuits build on each other: decoders use basic gates, the ALU uses decoders
and tri-state buffers, and the control unit orchestrates everything.


## Example program

A program to compute 4 × 3 = 12 using repeated addition:

```asm
MOV 4       ; Load 4 into reg A
DEA         ; Decrement: reg A = 3 (loop counter)
LDA 0       ; Store counter at memory 0x0
MOV 3       ; Load 3 into reg A
LDA 1       ; Store 3 at memory 0x1
LDA 2       ; Store 3 at memory 0x2 (accumulator)
ADD 2       ; reg A = reg A + [0x2]
LDA 2       ; Store partial result
STA 0       ; Load counter from memory
DEA         ; Decrement counter
JZ done     ; If zero, jump to halt
LDA 0       ; Store updated counter
STA 1       ; Load the addend
JMP ADD     ; Jump back to addition
HLT         ; Result (12) is at memory 0x2
```


## Status

This is a work in progress. The documentation and ALU circuits are complete.
The control unit, register, and memory circuits are partially implemented.
The full integration into a working 4-bit computer is not yet done.


## Versions

- **January 2019**: first version of the documentation and circuit designs.
- **February 2021**: revised version correcting mistakes found in the original.
