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

## Instruction Formats

The RISC-V ISA uses fixed-length 32-bit instructions. The bit patterns are interpreted differently depending on the instruction type.

### R-Type Instructions

Used for register-register arithmetic and logical operations:

* `add`
* `sub`
* `and`
* `or`
* `xor`
* `sll`
* `srl`
* `sra`

General form:

```asm
op rd, rs1, rs2
```

Example:

```asm
add x10, x11, x12
```

This performs: `x10 = x11 + x12`.

### I-Type Instructions

Used for immediate arithmetic, loads, and some control-flow operations:

* `addi`
* `slti`
* `xori`
* `ori`
* `andi`
* `lw`
* `lb`
* `jalr`

General form:

```asm
op rd, rs1, immediate
```

Example:

```asm
addi x5, x6, 8
lw x7, 20(x8)
```

The immediate is encoded in the instruction bits and sign-extended before use.

### S-Type Instructions

Used for stores:

* `sw`
* `sb`

General form:

```asm
op rs2, immediate(rs1)
```

Example:

```asm
sw x9, 16(x10)
```

This stores the value from `x9` into memory at address `x10 + 16`.

### B-Type Instructions

Used for conditional branches:

* `beq`
* `bne`
* `blt`
* `bge`
* `bltu`
* `bgeu`

General form:

```asm
beq rs1, rs2, label
```

The branch offset is relative to the current program counter.

### U-Type Instructions

Used for building 32-bit immediates:

* `lui`
* `auipc`

General form:

```asm
lui rd, upper_imm
```

This loads a 20-bit immediate into the upper bits of the register and clears the lower bits.

### J-Type Instructions

Used for unconditional jumps and procedure calls:

* `jal`
* `jalr`

Example:

```asm
jal x1, label
```

This writes the return address into `x1` and jumps to the target label.

## Immediate Generation

Immediate values are not stored as normal integer operands; they are encoded in the instruction word itself and then sign-extended when needed.

Examples:

* `addi x5, x6, -4`
* `sw x7, 8(x8)`
* `beq x1, x2, target`

Important idea:

* For arithmetic and loads, immediates are generally sign-extended.
* For branches, the immediate is shifted and interpreted as a PC-relative offset.
* For `lui` and `auipc`, the immediate is used to construct larger constants.

## Control Flow

Branches and jumps are how the CPU changes instruction order.

### Conditional Branches

```asm
beq x1, x2, L1
bne x1, x2, L2
blt x1, x2, L3
bge x1, x2, L4
```

These compare registers and either continue or jump to a target label.

### Unconditional Jump

```asm
jal x0, label
```

or

```asm
j label
```

A jump does not depend on a condition; it simply changes the PC.

## Logical and Bitwise Operations

RISC-V includes the standard logical operations:

* `and`
* `or`
* `xor`
* `not` is implemented as `xori rd, rs, -1`

Example:

```asm
and x10, x11, x12
xor x13, x14, x15
```

These work at the bit level and are useful for masks, flags, and bit manipulations.

## Comparisons

RISC-V uses arithmetic and branch instructions together:

```asm
slt x10, x11, x12
beq x10, x0, equal_case
```

`slt` sets `x10` to 1 if `x11 < x12`, otherwise 0.

There are also unsigned comparisons:

* `sltu`
* `bltu`
* `bgeu`

Unsigned comparisons interpret the register values as unsigned integers rather than signed values.

## Procedure Calls and Returns

To call a function, we traditionally use `jal`:

```asm
jal ra, func
```

This stores the return address in `ra` and jumps to `func`.

To return:

```asm
jalr x0, 0(ra)
```

This jumps back to the address saved in `ra`.

The caller-saved and callee-saved register conventions are important for correctness in high-level languages and software conventions.

## Summary of the RISC-V ISA

The RISC-V ISA emphasizes:

* A small, regular instruction set
* Fixed instruction width
* Simple addressing modes
* Few instruction formats
* Easy hardware implementation

This regularity is one reason it is attractive for both education and practical processor design.

The basic execution model is simple:

1. Fetch an instruction from memory.
2. Decode the instruction.
3. Read operands from registers or immediates.
4. Execute using the ALU.
5. Access memory if needed.
6. Write the result back to a register.
7. Update the program counter.

This model is the foundation of the datapath and control hardware we study next.
