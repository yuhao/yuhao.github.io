# A Framework For Understanding Cache Coherence
*Aug 12, 2014*

## The Premise

The correctness of shared-memory processor means each processor, or a process/thread from a programmer’s perspective, has a coherent view of the memory, i.e., they agree upon the value of a particular memory location, i.e., it is not possible that there are two “correct” values of an address at any given point. Whatever the cache coherence protocol does, it has to guarantee a consistent memory view across all the processors and memory.

To ensure correctness, cache coherence has to ensure the following two things:

* Write propagation mandates what happens after one write is done: the store has to be propagated somehow at some point to other processors, i.e., after a sufficiently long time after the store, the correct value of that location will be that stored value. There are two requirements for write propagation. First, if a read happens before the write, then the writer has to make sure that the reader knows that it no longer has the most up to date copy, the value has been changed. Second, if a read happens after the write, then the writer has to guarantee that the read will get the new value. In which order the read and write happens doesn’t matter, either is legal. What matters is whether the system is in a correct state after the read and write.
* Write serialization mandates what happens after multiple writes are done: regardless when a store is made visible to others, after two stores (from the same processor or different processors), there should only be one correct value in the system. That is to say, it appears that two stores to the same location are interleaved/serialized in certain order.

## The Framework

Any cache coherence implementation has three levels: **protocol**, **communication**, **interconnection**. The three levels are orthogonal to each other. Any cache coherence implementation is a certain permutation of the three.

**Protocol**: MSI => MESI/MOSI => MOESI

* Strongest assumption: atomicity. All memory operations to a location are serialized, which is equivalent to every state transitioning in the state machine is atomic.
* Strong assumption: only allowing at most one write at a time. Multiple reads are fine.
* Weak assumption: everything can happen. Multiple writes or write with reads can happen. This is not guaranteed to be correct.

**Communication**: two flavors

* Indirect, i.e., centralized, or directory based => needs middle man
* Direct, i.e., distributed, broadcast, or snoopy => requires cache-to-cache transfer

**Interconnection**: two flavors

* Ordered: bus, virtual bus, switch, etc.
* Unordered: mesh, torus, etc.

At the **protocol** layer: the protocol is to mainly meant to guarantee correctness (one can certainly optimizes it for better performance, but it depends on the specific choice of the other two layers). Essentially it has to ensure the two write properties (write propagation and write serialization).

* The protocol assumes that each memory operation (read/write hit/miss) is atomic. That is to say all the memory operations appear to happen in certain order, which is the strongest assumption. It guarantees absolute correctness if atomicity is met. But in reality it might be too strong to serializing all the memory operations. For example, if there are multiple reads that happen at the same time, the state machine can still be transitioning fine, and the end state of the system is still correct. We call it a strong assumption. Implementing the strong assumption still guarantees correctness (i.e., the two write properties are met). A weak assumption would allow everything to happen, such as multiple writes or writes with multiple reads at the same time. Implementing weak assumption may lead to incorrect system state.
* To see why implementing a weak assumption and thus violating write serialization might still be OK, consider the directory based implementation, where the directory receives the following requests: P0 ST value X, P1 ST value Y, P2 LD, P3 ST value Z. Even if the directory allows P0 and P1 to go out of order such that it is possible that after the LD, P1 and P2 think they have the current value of Y, but P0 thinks it has the correct value of X. But then if P3 comes in later to invalidate all other values, then the system can resume correct execution afterwards.
* The protocol describes conceptually how the system has to work in order to maintain a consistent view of the memory across processors and the memory. It says nothing about how to implement it. As long as you are able to correctly implement it, the system is guaranteed to be correct.

At the **communication** layer: The goal of the communication layer is to provide the communication substrate to correctly implement the protocol since processors have to somehow talk to each other to achieve a consistent view of the memory. The protocol only cares about each individual processor. It is the communication layer that enables realistic implementation. Note that the communication scheme is independent of the underlying interconnection. It is a conceptual layer. But of course some interconnects with best with some communication scheme.

* A indirect communication scheme has a middle man that oversees and manages the system. All the memory operations are sort of “delegated” by the processors to a centralized location, which makes sure that the correctness criterion is met, and tries to improve performance whenever possible. Because of the middle man who has a global view of the system, it’s easy to implement various assumptions from strongest to strong or even weak. Also since the middle man knows which processor has which data, it can direct requests to only those relevant processors (instead of broadcast) to save bandwidth. The bad thing about it is that it adds additional indirection latency (both the directory lookup and additional interconnect traversal). Even when a processor can directly get the data from another, this has to go through the middle man, which will examine its internal “log”, and route this request to another processor, incurring two-fold performance overhead.
* A direct communication scheme has no centralized management location. Therefore, when a processor needs an exclusive copy for example, it can’t not delegate it to anyone else. It can only broadcast this request to all others to figure out what the system status is. Such broadcast might overwhelm the bandwidth, and can’t scale to large systems. The absent of centralized management also means that no one can control others. Therefore, it’s difficult to enforce any atomicity and implement any assumption. For example, if two processors want to request exclusive write, without global view, it may be the case that each processor ends up believing they have an exclusive copy. The good thing without the middle man enables direct cache to cache transfer because if one needs data, it is able to directly get it from another processor without the mediation of the centralized unit.

In summary, the choice between the two communication schemes are really a tradeoff between bandwidth, latency, and easy of ordering control.

* Indirect:
  + Bandwidth efficient via precise multicasting instead of broadcasting
  + Easy to enforce ordering and implement correctness substrate
  - Long latency via indirection
* Direct:
  + Enable low latency communication via direct cache-to-cache transfer without a middleman
  - Need broadcast and therefore bandwidth inefficient
  - Difficult to enforce order (unless with an ordered interconnect like a bus and enforce the strongest assumption)

At the **interconnection** layer: the particular communication scheme of the protocol is strongly tied to the underlying interconnection of the system. It is the interconnection that finally decides how the coherency is implemented.

* Ordered interconnect such as bus. The nice thing about bus is that 1) it supports broadcast cheaply because there is a P2P link between one to all the others. Therefore, the order interconnect goes best with the distributed communication scheme which requires broadcast. 2) inherently serializes everything. Therefore, bus makes it easy to implement the strongest assumption. To realize this, a request can hold the bus until its request is fulfilled, at which point it then releases the bus. The bad thing about the bus is that it over-serializes requests to all the memory locations, and such serialization becomes a bottleneck in large scale systems.
* Unordered interconnect such as a mesh. The nice thing about it is that it has no order. Every processor can freely send out request without being blocked. However, this makes it difficult to serialize things to support the strongest/strong assumption unless there is a centralized unit. Therefore, the unordered interconnect goes best with the centralized implementation.

## Case-studies of Some Cache Coherence Implementations

The traditional snoopy based cache coherence uses:

* whatever protocol
* broadcast communication implementing the strongest assumption
* bus interconnect

The traditional directory based cache coherence (e.g., [MIPS R10k](https://ieeexplore.ieee.org/document/491460/) and [Origin 2000](http://www.sgidepot.co.uk/origin/isca.pdf)) uses:

* whatever protocol
* centralized communication implementing the strong assumption
* unordered interconnect

The [destination-set prediction](https://www.cis.upenn.edu/~milom/papers/isca03_destination_set_prediction.pdf) (DSP) based implementation uses

* MOESI/MOSI protocol
* multicast communication (broadcast with prediction) implementing the strongest assumption
* ordered interconnect

The [token coherence](https://www.cis.upenn.edu/~milom/papers/isca03_token_coherence.pdf) uses:

* MOSI protocol
* broadcast communication implementing the strong assumption
* unordered interconnect (torus)
