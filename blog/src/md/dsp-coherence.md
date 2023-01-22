# Predicting the Communication Scheme to Improve the Bandwidth/Latency Trade-off in Cache Coherency
*Aug 11, 2012*

The [DSP paper](https://www.cis.upenn.edu/~milom/papers/isca03_destination_set_prediction.pdf) focuses on how to choose the communication scheme in a cache coherence implementation. The communication scheme is the middle layer in [my framework of understanding cache coherence](cache-coherence.html).

Direct communication, i.e., broadcast, achieves low latency while requiring high bandwidth due to broadcast. This is what snoopy implementation does.

Indirect communication, i.e., centralized, requires low bandwidth while incurring high latency. This is what directory based implementation does.

They want to improve the tradeoff between bandwidth and latency. The end result is really more design points on the Pareto optimal frontier (Fig. 7 and 8), which originally only have two points, snoopy and directory. It doesn’t improve both latency and bandwidth at the same, but provides more choices such that designers can make better decisions given certain system constraints.

They start with a direct implementation that requires broadcast but no middle man. They reduce bandwidth requirement by replacing broadcast with multicast through predicting which are the mostly likely sharers. To guarantee correctness, they also need a directory, but doesn’t route the request as in the original indirect implementation. Rather, it just checks if the multicast is sufficient, i.e., whether the prediction is correct.

In this way, if the prediction is correct, then the bandwidth is greatly reduced as compared to broadcast, and achieves similar latency (although still not cache to cache transfer).

If the prediction fails, the directory corrects the misprediction, and the request is resent. In this case, the bandwidth is probably still lower than broadcast (because the prediction could be very conservative and only give out one possible destination in order to not waste bandwidth), and the latency is probably slightly higher than the indirect one.
