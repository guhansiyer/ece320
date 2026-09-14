# RISC-V Overview

## Registers

32 Registers in the RISC-V ISA

* Referred to by `x0-x31`
* `x0` is special, always holds a value of zero
* Why 32?
  * Smaller is faster, but too small is too bad

Each register is 32 bits wide (RV32 variant of ISA).

## Assembly Syntax

Instruction have operands and an opcode:

* `add x1, x2, x3`
  * `add`: opcode
  * `x1`: destination register
  * `x2`: first source operand register
  * `x3`: second source operand register

Immediates are numerical constants. The syntax is similar to `add` instructions, except the last operand is a number:

* `addi x3, x4, -10`

## Memory Structure

An 8 bit chunk is called a byte (1 word = 4 bytes = 32 bits). Memory addresses are in bytes, word addresses are 4 bytes apart (little endian convention).

To transfer from memory to register, use load word (`lw`).