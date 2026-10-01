# Pipelining

## Why Pipelines Exist

A single-cycle processor does all work for one instruction in one clock cycle:

* fetch instruction
* read registers
* perform ALU operation
* access memory
* write result back

This is conceptually simple, but it has a big problem: the clock period must be long enough for the slowest possible instruction, even if many instructions do not need all of that work.

The solution is to split the instruction execution into stages and overlap them.

> A pipeline lets different instructions occupy different stages of the processor at the same time.

This improves throughput, even though a single instruction may still take several cycles to complete.

## Basic Pipeline Stages

A classic RISC pipeline has five stages:

1. Fetch (IF)
   * Read the next instruction from memory.
   * Update the program counter.
2. Decode (ID)
   * Decode the instruction.
   * Read the register operands.
   * Generate control signals.
3. Execute (EX)
   * Perform ALU work.
   * Compute effective addresses and branch targets.
4. Memory (MEM)
   * Read or write data memory for loads and stores.
5. Write-back (WB)
   * Write the ALU or memory result back to the register file.

This is the same basic instruction flow we saw in the datapath, but now each step is separated into a different stage of the pipeline.

## Pipeline Execution

In a pipelined processor, the stages work on different instructions in parallel.

Example:

```text
Cycle 1:  I1  IF
Cycle 2:  I1  ID   I2  IF
Cycle 3:  I1  EX   I2  ID   I3  IF
Cycle 4:  I1  MEM  I2  EX   I3  ID
Cycle 5:  I1  WB   I2  MEM  I3  EX
```

The important point is that the processor is not doing one instruction at a time; it is doing one stage of one instruction and one stage of another instruction at the same time.

This gives much better overall performance when the pipeline is full.

## Ideal Pipeline Behavior

In the ideal case:

* one instruction completes every clock cycle
* the pipeline is fully utilized
* no stalls occur
* no hazards exist

This leads to a throughput improvement of roughly:

* $\text{speedup} \approx \text{number of stages}$

for a perfectly balanced pipeline.

In practice, the speedup is lower because real pipelines have hazards and overhead.

## Hazards

A hazard is a situation where the next instruction cannot execute normally because of a dependency or resource conflict.

### Structural Hazards

These happen when hardware resources are insufficient.

Examples:

* one memory port shared by fetch and data access
* only one write port in the register file

The processor may need to stall an instruction until the resource is available.

### Data Hazards

These happen when one instruction depends on the result of an earlier instruction that has not completed yet.

Example:

```asm
add x10, x11, x12
sub x13, x10, x14
```

The second instruction needs the result of `x10` before the first instruction has reached write-back.

There are two common solutions:

* stall the pipeline until the value is ready
* forward the value directly from a later pipeline stage to the execute stage

Forwarding is preferred when possible because it avoids unnecessary stalls.

### Control Hazards

These happen because the CPU does not know the next instruction until it decides whether a branch or jump is taken.

Example:

```asm
beq x1, x2, target
add x10, x11, x12
```

If the branch is taken, the instruction after the branch is not the next valid instruction. The processor may fetch the wrong instruction.

Solutions include:

* stall until the branch outcome is known
* predict not taken
* use branch prediction hardware

Prediction reduces the penalty, but it can still be wrong.

## Forwarding

Forwarding is a hardware technique that sends the result of one pipeline stage to an earlier stage that needs it.

For example:

* an ALU result from the EX stage can be forwarded to a later EX stage
* a memory result from MEM can be forwarded to the EX stage

This avoids stalling in many common cases, especially when instructions are close together.

It is especially useful for arithmetic dependencies, such as:

```asm
add x1, x2, x3
sub x4, x1, x5
```

The `sub` can use the value of `x1` from the earlier instruction without waiting for the full write-back stage.

## Stalls

Sometimes the pipeline must wait.

This usually happens when:

* a load result is needed too soon
* a branch target is not known yet
* two instructions need the same hardware resource

A stall causes a pipeline bubble, which means a cycle with no useful work for one stage.

The processor effectively inserts a "do nothing" instruction into the pipeline.

## Control Signals in a Pipeline

The control logic is still generated from the decoded instruction, but now the control values travel along with the instruction through the pipeline.

For example:

* the decode stage decides whether the instruction is a branch, load, store, or arithmetic instruction
* the execute stage uses the ALU control bits
* the memory stage uses MemRead and MemWrite
* the write-back stage uses RegWrite and MemToReg

This means control does not need to be recomputed at every stage; it is simply passed forward with the instruction state.

## Pipeline Summary

Pipelining is one of the most important ideas in modern processor design because it greatly increases instruction throughput.

The basic idea is simple:

* keep every stage busy
* overlap different instructions
* use hazards and forwarding to preserve correctness

The cost is complexity:

* data hazards must be detected
* control hazards must be handled
* resources may need arbitration

Still, the throughput gains are so significant that nearly every modern CPU uses pipelining.

## Why This Matters

The processor is no longer just a simple set of combinational logic and memory blocks. It is a coordinated collection of stages that work together to increase performance.

This is the foundation of modern superscalar, out-of-order, and multicore processors.

The key insight is that hardware can do useful work in parallel as long as the instructions are independent enough and the system handles the dependencies correctly.
