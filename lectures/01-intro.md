# Introduction

## Application Specific Design

We are concerned with general-purpose CPUs:

* These are processors that can do anything (ex: run a full OS)

This is in contrast to application-specific chips (ASICs):

* Domain specific functionality in hardware (ex: TPU, AI accelerator)

In reality, CPUs are a mix between general and domain-specific purpose.

## Technology

Basic element: solid-state transistor (switch). These are the building block of integrated circuits (ICs).

> What's so great about ICs?

* High performance, high reliability, low cost, low power
* Easily mass producible

Several families of ICs:

* SRAM/logic: optimized for speed, used for processors
* DRAM: optimized for density, cost, power, used for memory
* Flash: non-volatile memory

## Trends in Technology

> Moore's Law: the density of transistors on an IC roughly doubles every year.

Some ramifications of this in technology include:

* Absolute improvements in density and speed
  * SRAM/logic: density: ~30% (annual), speed: ~20%
  * DRAM: density: ~60%, speed ~4%
  * Disk: density: ~60%, speed: ~10%

## Revolution 1: The Microprocessor

One significant technology threshold was crossed in the 1970s; enough transistors (~25K) to put a 16-bit processor on one chip.

* Huge performance advantages: fewer slow chip-crossings
* Even bigger cost advantages: one **stamped-out** component

Microprocessors have created new market segments:

* Desktops, CD/DVD players, laptops, game consoles, cable boxes, mobile phones, etc.

They have also replaced incumbents in existing segments:

* Microprocessor-based systmes replaced supercomputers, mainframes, etc.

> First microprocessor: Intel 4004 (1971)

Specifications:

* Application: calculators
* Technology: 10 micrometer PMOS
* 2300 transistors
* 13 $\text{mm}^2$
* 108 KHz
* 12 volts
* 4-bit data
* Single-cycle datapath

> Pinnacle of single-core microprocessors: Intel Pentium4 (2003)

Specifications:

* Application: desktop/server
* Technology: 90 nanometer (1/100x)
* 55 million transistors (20,000x)
* 101 $\text{mm}^2$ (10x)
* 3.4 GHz (10,000x)
* 1.2 volts (1/10x)
* 32/64-bit data (16x)
* 22-stage pipelined datapath
* 3 instructions per cycle (superscalar)
* 2 levels of on-chip cache
* data-parallel vector (SIMD) instructions, hyperthreading

> Early modern processor: Intel Core i7-3770K (2011)

Specifications:

* Application: desktop/server
* Technology: 22 nanometer CMOS
* 774 million transistors
* 37.5 $\text{mm}^2$
* 3.5-3.9 GHz
* 4 cores
* Each core is hyperthreaded (2 threads)
  * 8 threads total
* Integrated graphics

> Today: Intel Meteor Lake Technology (2023)

Specifications:

* Multi-tile chiplet design
* CPU 7 nm, GPU 5 nm, SOC 6nm
* 6 P(performace)-cores (12 threads)
* 8 E(efficiency)-cores (8 threads)
* L1 = 112 KB per P-core, 96 KB per E-core
* L2 = 2 MB per P-core and E-core cluster
* L3 = up to 24 MB

## Tracing the Microprocessor Revolution

Growing transistor count used in several ways:

* Widen datapath:
  * 4004: 4 bits $\rightarrow$ Pentium4: 64 bits
* Add more powerful instructions:
  * Amortize fetching and decoding of instructions.
  * Simplify programming.

## Revolution 2: Implicit Parallelism

Then, to extract implicit instruction-level parallelism:

* hardware provides parallel resources, figures out how to use them (software is obvious).

This was initially done with pipelining, increasing clock frequency.

Caches became necessary as processor clock frequency increased.

After this, integrated floating-point was added directly onto the chip.

Then we moved to deeper pipelines and branch speculation, and moved into superscalar (fetch/decode multiple instructions at once) and dynamic scheduling (out-of-order execution)

## Revolution 3: Explicit Parallelism

Then to support explicit data and thread-level parallelism:

* Hardware provides parallel resources, software specifices usage.
* Why? Diminishing returns on instruction-level-parallelism.

First using vector instructions (Intel's Streaming SIMD Extensions):

* One instruction does 4 parallel multiplies.

General support for multi-threaded programs:

* Hardware synchronization primitives.

Then using support for multiple concurrent threads on chip

* First with single-core multi-threading, now with multi-core.

Graphics processing units (GPUs) are highly parallel:

* Converging with general-purpose processors (CPUs).

## Revolution 4: Heterogeneous Processing

The idea of combining multiple kinds of compute engines in one die:

* Not just homogenous collections of cores
* System-on-chip (SoC) is a common example in the mobile space

Lots of stuff on chips beyond just CPUs:

* GPUs
  * Throughput-oriented specialized multi-core processors
* Special-purpose logic
  * Media codecs, encryption, machine learning, compression

Excellent energy efficiency and performance, downside is complicated programming.
