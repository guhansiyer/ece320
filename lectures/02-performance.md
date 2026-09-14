# Performance

## What is it?

> Latency (execution time): time to finish a fixed task.

> Throughput (bandwidth): number of tasks completed in a fixed amount of time.

## CPU Performance Equation

Program Runtime = "second per program" = (instructions/program) * (cycles/instruction) * (seconds/cycle)

* Instructions/Program: "dynamic instruction count"
  * Runtime instruction count
  * Determined by program, compiler, ISA
* Cycles/Instruction: "CPI"
  * On average, how many cycles an instruction takes to execute
  * Determined by compiler (scheduling), micro-architecture
* Seconds/Cycle: cycle time, clock time, 1/clock frequency
  * Determined by micro-architecture, physical layout

For low latency, we want to minimize all three. This is difficult in practice as they often pull against one another.

## Perception on Performance

> When is it okay to ignore dynamic instruction count?

CPU performance equation becomes:

* Latency: seconds/instruction = (cycles/instruction) * (seconds/cycle)
* Throughput: instructons/second = (instructions/cycle) * (cycles/second)


## Benchmarks

* Toy benchmarks: little programs no one really uses
  * ex: Tower of Hanoi, 8-queens, etc.
* Kernel benchmarks: important pieces of real programs
  * ex: Livermore loops
  * Good: focuses on individual features but not big picture
  * Bad: over-emphasizes target feature
* Synthetic benchmarks: programs created for benchmarking
  * ex: Whetstone, Dhrystone
* Real programs
  * Good: only accurate way to characterize performance
  * Bad: requires porting, a lot of work

## Average Performance Numbers

Important to note about averages:

* Ideally proportional to time
  * Arithmetic mean for times (latencies)
  * Harmonic mean for rates (throughput)
  * Geometric mean for ratios (speedup)

Arithmetic:

* Average execution time of $n$ programs
* For units proportional to time

$$
\frac{1}{n}\sum_{i=1}^{n} \text{latency}_i
$$

Harmonic:

* For units inversely proportional to time

$$
\frac{n}{\sum_{i=1}^{n} \text{throughput}_i}
$$

Geometric:

* For unitless quantities (eg: ratios)

$$
\frac{1}{n}\sum_{i=1}^{n} \text{latency}_i
$$

## Amdahl's Law

> The performance improvement to be gained from using some faster mode of execution is limited by the fraction of the time the faster mode can be used.

Let us optimize a fraction $f$ of program A by a factor of $s$:

$$
T_{new} = T_{old} * ((1-f) + \frac{f}{s})
$$

Recall speedup:

$$
\frac{T_{old}}{T_{new}} = \frac{1}{[(1-f) + \frac{f}{s}]}
$$