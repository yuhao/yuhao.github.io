# Token Coherence: Decoupling Performance and Correctness
*Aug 13, 2012*

I really liked the idea of [Token Coherence](https://www.cis.upenn.edu/~milom/papers/isca03_token_coherence.pdf). It makes so much sense to me, especially when I think about it using [my framework of understanding cache coherence](cache-coherence.html).

Workload trend suggests that we want to enable cache to cache transfer to minimize latency. So we need to use direct communication scheme. However, due to the distributed nature of the direct communication, it is hard to enforce order, and traditionally we can only enforce a total order (the strongest assumption) using an ordered interconnect (e.g., bus). For example, destination-set predictor uses the direct communication scheme but has to use an ordered network.That limits the performance. The technology trend suggests that we want to use unordered interconnects.

The most enlightening thing of this paper is the idea of decoupling performance and correctness of a cache coherence implementation to enable enforcing the strong assumption in an unordered interconnect.

Another way of looking at the token coherence is that it solves another problem in the direct communication: performance. DSP solves the bandwidth issue using predictive multicasting, but still has to enforce total order that limits performance.

The correctness of shared-memory processor means each processor, or a process/thread from a programmer’s perspective, has a coherent/consistent view of the memory, i.e., they agree upon the value of a particular memory location, i.e., it is not possible that there are two “correct” value of an address at any given point. Whatever the cache coherence protocol does, it has to guarantee a consistent memory view across all the processors and memory.

All cache coherence protocols are able to guarantee such correctness by assuming that each state transitioning happens atomically. It only cares about a particular node. However, during implementation, we can get to race conditions when multiple processors have request at the same time. Then it’s possible that multiple caches (of the same address) are transitioning states simultaneously.

Section 2 of this paper gives an example where when P0 have a read and P1 have a write request, without care we may end up with a final state of the machine where P0 is in modified state and P1 is in shared state. This violates the write propagation since the write of P0 is not properly propagated to P1. After a sufficiently long time, P1 is still gonna read the old value rather than what P1 has updated.

The easiest way to resolve this race is to enforce the order of all the requests to the same location such that all the processors and memory see the same global request order. We always first finish the first request before we deal with the next. This is what a snoopy protocol based on bus does. It serializes all the requests to all the locations. Everytime a processor has a read or write miss or even a write hit to the shared state, bus has to broadcast (BI, BRI, or BR), and nature of the bus guarantees that all the broadcasts are seen in the same global order by all the participants.

Apparently the bus (or any other total ordered interconnect) that performs global serialization is the performance bottleneck, because there is no point of serialize requests to different memory locations. In fact, even for a particular memory address, if there are only reads, they do not have to be serialized. These optimizations are very difficult to implement in broadcast-based systems.

However, the directory based implementation uses the directory as a centralizes middle man to intelligent manage and oversee the dynamic of system and provide per-block ordering. In this case, the processors or the memory do not have any sense of “global ordering”, it is the directory that has the view of the ordering (at a block basis), and tries to enforces per-block serialization. Such centralized “arbiter” allows directory implementation to perform finer-grained serialization such as not serializing different reads. The cost is then the added latency to/from the directory.

The token coherence wants to achieve 1) the flexibility of a directory based protocol such that each processor can freely send out request in an unordered interconnect without being totally serialized by a bus 2) the direct cache to cache transfer that is enabled by the bus without the added path to/from the directory. Essentially, it wants to enable directory-like management for per-block ordering without having to have a directory but direct cache to cache transfer.

So because there is no directory for central management, token coherence has to rely on broadcast — even in a torus network. Also, because it doesn’t have directory, the correctness is now then ensured by something called token.

How does the token system ensure correctness? It’s the same as the traditional directory implementation where only two scenarios are permitted: 1) one single writer; 2) one or multiple readers. The token coherence ensures that only those two can happen, i.e., there can not be more than one writer by enforcing the rule that a write needs to have all the tokens.
