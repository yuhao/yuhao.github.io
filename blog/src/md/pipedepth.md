# What is the Optimal Logic Depth Per Pipeline Stage?
*July 31, 2014*

There are two things that affect the performance: cycles per second (clock rate, frequency), and instruction per cycle (IPC).

The way to increase the frequency is: 1) faster transistor through process generations, and 2) less logic in each pipeline stage (evaluated by #FO4 delays).

[This seminal paper](https://dl.acm.org/doi/10.5555/545215.545218) assumes that the context is within a particular process generation, that is the FO4 delay stays the same.

The question they want to address is, how does the overall performance change with the number of pipeline stage?

Changing the number of pipeline stages will lead to high frequency (since less logic in each stage), but will also affect IPC. There is a tradeoff.

Theoretically, deeper pipelining always creases IPC, but can be compensated by the improved clock rate—if the pipeline has little stall. This paper makes a conclusion that the pipeline stall in deep pipelined processor is too much to be compensated by the improved by the clock rate.

The reasons is because deeper pipeline leads to longer critical path between two consecutive, and dependent instructions, assuming the structures that get pipelined is an atomic unit.

For example, the instruction selection logic is an atomic unit. If it’s pipelined, then we are not able to execute dependent instructions back to back.
Also the branch predictor, we can’t fetch the next one before the current one is resolved.
Because IPC decreases with deeper pipeline, and frequency increases with deeper pipeline, it is not immediately clear how pipeline stages affects the performance.

This paper experimentally determines the optimal pipeline stages (or effectively the #FO4 delays in each stage).

It also discusses taking and not taking into account the overhead in each pipeline stage, including latch, clock skew, and jitter.

Note that this paper is talking about the pipeline depth purely from a performance perspective. There is no power discussion. This paper was published at 2002, before Intel’s first multicore.
