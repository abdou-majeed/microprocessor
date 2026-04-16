# microprocessor (in progress)

Design of a 4-bit microprocessor from first principles, down to logic gates.

This is a personal project I started during my Electrical Engineering studies.
The goal is to design a complete 4-bit computer: ALU, registers,
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


## Circuit files

The `circuits/` directory contains gate-level implementations of every component,
organized into subdirectories for the ALU, decoders, tri-state buffers, and control logic.
PDF screenshots of each circuit are available in `circuits/screenshots/`.

The `.logicly` files can be opened and simulated interactively in
[Logicly's free online simulator](https://logic.ly/demo/) (File → Open).
Running the simulations is much better than looking at the static PDFs —
you can toggle inputs, watch signals propagate, and step through the clock.



## Example program

Multiplication of 4 and 3 by repeated addition. The program decrements
one factor as a loop counter while accumulating the other. The result
ends up at memory location 0x2. Starting at 0x0:

```asm
0x0	MOV 4		; Move 4 to reg A
0x2	DEA		; Decrement reg A. reg A = 3
0x3	LDA 0		; Store the content of reg A in memory location 0x0
0x5	MOV 3		; Move 3 to reg A
0x7	LDA 1		; Store the content of reg A in memory location 0x1
; The content of reg A is still 3
0x9	LDA 2		; Store the content of reg A in memory location 0x2
			; [0x0]=3 [0x1]=3 [0x2]=3
0xB	ADD 2		; Add reg A and the content of memory location 0x2 (the number 3)
0xD	LDA 2		; Store the content of reg A in memory location 0x2
			; [0x0]=3 [0x1]=3 [0x2]=6
0xF	STA 0		; Set A with the content of memory location 0x0 (the number 3)
0x11	DEA		; Decrement reg A
			; reg A = 2
0x12	JZ 26		; If Zero flag is True, then jump to memory location 0x1A
0x14	LDA 0		; Store the content of reg A in memory location 0x0
			; [0x0]=2 [0x1]=3 [0x2]=6
0X16	STA 1		; Set A with the content of memory location 0x1 (the number 3)
0x18	JMP 11	; Jump to memory location 0xB
0x1A	HLT		; HALT
			; the result is in memory location 0x2
```



## Roadmap

- Complete the circuit for the full fetch-execute cycle.
- Write a simple assembler that translates mnemonics to binary.
