# CPU Design Lab

## Overview

This project implements a simple educational CPU in **SystemVerilog** by integrating a set of provided hardware modules into a complete processor.

The goal of the lab is not to invent a new CPU from scratch, but to:

- assemble the CPU from existing RTL components,
- connect the datapath and control signals correctly,
- import the required opcode type definitions,
- verify the CPU using the provided testbench,
- and make sure all diagnostic programs execute correctly.

This CPU is built around a **shared memory**, a **Program Counter (PC)**, an **Instruction Register (IR)**, an **Accumulator (AC)**, an **ALU**, a **Controller**, and an **address-select multiplexer**.

---

## Lab Objective

The lab focuses on **CPU integration and verification**.

You are expected to:

1. Complete the top-level CPU model in `cpu.sv`
2. Instantiate and connect the provided components correctly
3. Import the package that defines `opcode_t`
4. Run the testbench `cpu_test.sv`
5. Debug the design until all required CPU tests pass

---

## What Is CPU Architecture?

CPU architecture describes how a processor is organized internally and how its hardware blocks cooperate to execute instructions.

In this lab, the CPU architecture is intentionally simple so that the main ideas are easy to understand.

### Main architectural concepts

- **Program Counter (PC)**  
  Holds the address of the next instruction to fetch

- **Memory**  
  Stores both instructions and data

- **Instruction Register (IR)**  
  Holds the current instruction

- **Accumulator (AC)**  
  Main working register used by the ALU

- **ALU**  
  Performs arithmetic and logic operations

- **Controller**  
  Decodes the opcode and generates control signals

- **Address MUX**  
  Selects which address is sent to memory:
  - the program address from the PC, or
  - the address field from the instruction

This means the processor follows an **accumulator-based architecture**, where most operations are centered around the accumulator instead of a large general-purpose register file.

---

## CPU Organization

The CPU in this lab is composed of the following functional blocks:

### 1. Program Counter
Implemented using the `counter` module.

Responsibilities:
- holds the current instruction address
- increments to the next instruction
- loads a new address when required by control logic

### 2. Address Select MUX
Implemented using `scale_mux`.

Responsibilities:
- selects between:
  - PC address for instruction fetch
  - instruction address field for data access

### 3. Memory
Implemented using `mem`.

Responsibilities:
- stores program instructions
- stores data values
- provides instruction/data output
- accepts data writes when enabled

### 4. Instruction Register
Implemented using `register`.

Responsibilities:
- stores the current instruction fetched from memory
- provides opcode and address field to the rest of the CPU

### 5. Accumulator
Also implemented using `register`.

Responsibilities:
- stores the main operand/result value
- receives ALU output
- supplies one ALU input

### 6. ALU
Implemented using `alu`.

Responsibilities:
- receives:
  - data from memory
  - current accumulator value
  - opcode
- computes arithmetic/logic result
- provides result back to accumulator or memory depending on the instruction

### 7. Controller
Implemented using `control`.

Responsibilities:
- decodes the opcode
- generates control signals such as:
  - `mem_rd`
  - `mem_wr`
  - `load_ir`
  - `load_ac`
  - `load_pc`
  - `inc_pc`
  - `halt`

---

## Execution Flow

The CPU operates in a simple instruction cycle:

### 1. Fetch
The PC provides the instruction address.  
The address MUX selects the program address.  
Memory outputs the instruction.  
The instruction is loaded into the IR.

### 2. Decode
The controller decodes the opcode in the IR and determines what operation must be performed.

### 3. Operand Access
If the instruction needs data from memory, the address MUX selects the instruction address field and memory is accessed again.

### 4. Execute
The ALU performs the required operation using:
- the accumulator value,
- memory data,
- and the opcode.

### 5. Write Back
The result is written back to:
- the accumulator, or
- memory, depending on the instruction type.

### 6. Next Instruction
The PC is incremented or loaded with a new value.  
Execution continues until the CPU reaches a `HLT` instruction.

---

## Project Structure

```text
rtl/
├── alu.sv
├── control.sv
├── counter.sv
├── cpu.sv
├── mem.sv
├── register.sv
├── scale_mux.sv
└── typedefs.sv

tb/
└── cpu_test.sv

CPUtest1.dat
CPUtest2.dat
CPUtest3.dat
compile.f
Makefile
design_of_cpu.png
lab_requirement.pdf
