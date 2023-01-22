# A Framework For Understanding ACMP Scheduling
*Aug 23, 2014*

## The Framework

Three critical issues in the ACMP scheduling: **scheduling unit**, **scheduling objective**, and **scheduling heuristics**.

The entire scheduling problem is essentially to use certain scheduling heuristics to decide the best ACMP execution configuration for each scheduling unit such that the scheduling objective is achieved.

### Scheduling unit

There are two orthogonal ways to classify scheduling unit.

1. Fine-grained vs. Coarse-grained

Fine-grained typically is at the granularity of a few hundred to one thousand instructions.

Coarse-grained is typically at millions instructions granularity. The OS DVFS governor can even work at billions of instructions granularity.

Fine-grained typically has large energy gain than coarse-grained, but also requires very fast transition, such as in Composite core and Thread Motion.

2. Code activity aware vs. Agnostic

Scheduling unit could be code segment or execution activity agnostic. In this case, the scheduling unit is typically expressed as a fixed instruction length (e.g., Time-Driven TM, Composite Cores) or a fixed cycle counts (PIE).

Scheduling unit could also be code or execution activity aware. In this case, each scheduling unit bear meaningful semantics.

Examples of code segment aware unit include a critical section, the lagging thread, program traces (or a group of traces), a distinct program phase (such as ACS, BIS, UBA).

Examples of execution activity aware unit include code between cache miss and refill (such as in Miss-Driven TM).

### Scheduling objective

There are two types:

1. Constrained

Constrained scheduling objective include 1) Tolerating x% performance while minimizing energy; 2) Tolerating x% power increase while maximizing performance; 3) Maximizing performance under a fixed power budget.

This requires the scheduling heuristics to able to predict/estimate the performance/energy of different scheduling targets.

The nice thing about optimizing for a constrained scheduling objective is that it provides some guarantee of the end result, such as certain performance loss/energy savings because these can be expressed in the scheduling objective.

2. Unconstrained

Unconstrained scheduling simply optimizes for performance (throughput) or energy whenever possible — based on some heuristics. There is no guarantee that the performance will be maximized, for example.

For example, ACS schedules selected the critical sections on to a big core. The hope is that the performance gain is large enough to offset the power increase, eventually saving energy, or at least reducing EDP.

### Scheduling heuristics

There are two orthogonal problems: the prediction scheme within each scheduling unit, and the assumption across consecutive scheduling units:

1. For the prediction scheme within each scheduling unit:

Some schedulers do not have any prediction. For example, it simply assumes that running on a big core is faster than small core. Therefore, use the big core whenever the performance is needed. Examples include ACS. This is mostly seen and works best with an unconstrained scheduling objective.

Other schedules construct performance/utility models such that the scheduling decision can be made more under control. Performance models include linear regression model (as in Composite Cores, [3]), mechanistic analytical model (as in PIE, [5], TM). These quantitative models are needed when optimizing for a constrained scheduling objective. Even when optimizing for an unconstrained scheduling objective, sometimes performance/utility estimation is still needed to decide how exactly to map different applications to different cores (such as in PIE).

2. The Assumption across consecutive scheduling unit, there are two types:

Last-value prediction: it assumes that the next scheduling unit has the same behavior as the previous one. For next scheduling unit, it simply assumes the same scheduling decision as the previous one. Examples include TM, Composite Cores.

Per-unit prediction: it works at a per-scheduling unit basis. It recognizes that consecutive scheduling units might have completely behaviors. Instead, it re-predicts before each scheduling unit, and re-schedules if needed. Examples include [3], PIE, etc. Of course there are many ways to implement a per-unit predictor. Sampling is the commonly seen one, where at the very beginning of each unit, multiple potential configurations are sampled and then the best is chosen to run for the rest of the unit. The original single-ISA heterogeneous paper does this.

# Case-studies

### [1] Scheduling Heterogeneous Multi-Cores through Performance Impact Estimation (PIE)

Scheduling unit: 2.5 ms
Scheduling objective: unconstrained, simply optimizing for system throughput
Scheduling heuristics: analytical performance model + per-unit prediction
Scheduling problem description: at every 2.5ms, decides the best application to core mapping such that the overall system throughput is maximized.

### [2] Thread Motion: Fine-Grained Power Management for Multi-Core Systems

Scheduling unit: 1K instructions
Scheduling objective: Constrained, maximizing performance under certain power budget
Scheduling heuristics: simple analytical performance model + last-value prediction
Scheduling problem description: for each 1K instructions, decides whether to schedule it to a high VF core or low VF core with certain power budget (e.g., 80%).

### [3] Trace Based Phase Prediction For Tightly-Coupled Heterogeneous Cores

Scheduling unit: ~300 instructions (super-trace that consists of multiple traces)
Scheduling objective: Constrained, minimizing energy with 5% performance loss (configurable)
Scheduling heuristics: linear regression-based performance model + per-unit prediction
Scheduling problem description: for each super-trace, decides whether to schedule it to the OoO backend or in-order backend to minimize energy with 5% performance loss.

### [4] Composite Cores: Pushing Heterogeneity into a Core

Scheduling unit: 1K instructions
Scheduling objective: Constrained, minimizing energy with 5% performance loss (configurable)
Scheduling heuristics: linear regression-based performance model + last-value prediction
Scheduling problem description: for each 1K instructions, decides whether to schedule it to the OoO backend or in-order backend to minimize energy with 5% performance loss.

### [5] Predicting Performance Impact of DVFS for Realistic Memory Systems

Scheduling unit: 100K instructions
Scheduling objective: Unconstrained, minimizing energy consumption
Scheduling heuristics: Analytical performance model (considering realistic memory system) + per-unit prediction
Scheduling problem description: for each 100K instructions, decides the best frequency such that the entire program’s energy consumption is minimized.

### [6] Accelerating Critical Section Execution with Asymmetric Multi-Core Architectures

Scheduling unit: a critical section, size varying from 29.5 instructions to 620K
Scheduling objective: Unconstrained, maximizing performance
Scheduling heuristics: Simple threshold-based heuristics (SEL) + per-unit prediction
Scheduling problem description: for each critical section, decides whether to schedule it to a big core for maximizing performance.

### [7] Bottleneck Identification and Scheduling in Multithreaded Applications

Scheduling unit: multithreaded application bottleneck: a critical section, or the slowest thread that reaches the barrier, Amdahl’s sequential code, pipeline stage. Sizes are undocumented, but I guess they are similar to those in ACS
Scheduling objective: Unconstrained, maximizing performance
Scheduling heuristics: Threshold-based heuristics (TWC based) to decide which scheduling unit to accelerate + per-unit prediction
Scheduling problem description: for each application bottleneck, decides whether to schedule it to a big core for maximizing performance.

### [8] Utility-Based Acceleration of Multithreaded Applications on Asymmetric CMPs

Anyone wants to try this and tell me your answer?
