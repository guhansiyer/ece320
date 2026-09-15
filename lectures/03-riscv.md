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

To transfer from memory to register, use load word (`lw`):

```c
// C
int A[100];
g = h + A[3];
```

```asm
# RISC-V
lw x10, 12(x13) # reg x10 gets A[3]
add x11, x12, x10 # g = h + A[3]
```

In this instance `x13` is the base register and points to `A[0]`. `12` is the offset in bytes necessarily to obtain `A[3]`. The offset must be a constant known at assembly time.

To transfer from register to memory, use store word (`sw`):

```c
// C
int A[100];
A[10] = h + A[3];
```

```asm
# RISC-V
lw x10, 12(x13) # temp reg x10 gets A[3]
add x11, x12,x10 # temp reg x10 h + A[3]
sw x10, 40(x13) # A[10] = h + A[3]
```

In addition to word transfers, RISC-V has byte transfers with the same format:

* load byte: `lb`
* store byte: `sb`

## Shifting

We can perform bit shifting on values held in registers and store them in registers:

* Shift Left Logical (`slli`): `slli x11, x12, 2 # x11 = x12 << 2`
* Shift Right Logical (`slri`): same thing but right shifted.
  * Zeroes inserted at left end of word, right bits are shifted off.
* Shift Right Arithmetic (`srai`): moves `n` bits to the right (high order sign bit moves into empty bits)

## Types of Branches

A branch is a change in control flow. There are condiitonal and unconditional branches.

Conditional branches change the control flow depending on the outcome of comparison:

* Branch if equal: `beq`
* Branch if not equal: `bne`
* Branch if less than: `blt`
  * Branch if less than unsigned: `bltu`
* Branch if greater than: `bge`

Unconditional branches always jump (`j`).

## Helpful Features

### Symbolic Register Names

| Register | ABI Name | Description | Saver |
| :--- | :--- | :--- | :--- |
| **x0** | zero | Hard-wired zero | — |
| **x1** | ra | Return address | Caller |
| **x2** | sp | Stack pointer | Callee |
| **x3** | gp | Global pointer | — |
| **x4** | tp | Thread pointer | — |
| **x5** | t0 | Temporary/alternate link register | Caller |
| **x6–7** | t1–2 | Temporaries | Caller |
| **x8** | s0/fp | Saved register/frame pointer | Callee |
| **x9** | s1 | Saved register | Callee |
| **x10–11** | a0–1 | Function arguments/return values | Caller |
| **x12–17** | a2–7 | Function arguments | Caller |
| **x18–27** | s2–11 | Saved registers | Callee |
| **x28–31** | t3–6 | Temporaries | Caller |

### Pseudo-instructions

* `mv rd, rs` = `addi rd, rs, 0`
* `li rd, 13` = `addi rd, x0, 13`

## Six Steps in Function Calling

1. Put parameters in a place where the function can access them.
2. Transfer control to function.
3. Acquire (local) storage resources needed to function.  
4. Perform desired task of the function.
5. Put result value in a place where calling code can access it and restore any registers you used.
6. Return control to point of origin, since a function can be called from several points in a program.