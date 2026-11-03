# Dynamic Scheduling

Our basic pipeline started with single, in-order issue, single-cycle operations. We extended this basic pipeline with multi-cycle operations and multiple issue (superscalar).

Now we have dynamic-scheduling (out-of-order execution), where we want to execute instructions in non-sequential order. This will reduce stalls, improve functional unit utilization, and enable parallel execution.

We want to make this all appear like sequential execution, which is extremely important but hard to get correct.

## Scheduling

Scheduling is the process of re-arranging instructions to maximize performance. This requires knowledge of the processor structure, latencies and dependencies.

Two options for who should schedule instructions: dynamic (hardware) or static (compiler).

## The Problem with In-Order Pipelines

In a simple in-order pipeline we may have four stages (where EX is multi-cycle and includes M). There is a structural hazard in that we have 1 pipeline register per stage, and thus 1 instruction per stage per cycle. Younger instructions can't pass older ones without 'killing' them.

In an out-of-order pipeline, we can implement passing functionality.
