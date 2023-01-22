# Use ILP (MLP) not Compute (Memory) Intensity to Guide ACMP Scheduling
*March 5, 2013*

Before the [PIE paper](http://www.jaleels.org/ajaleel/publications/isca2012-PIE.pdf), ACMP scheduling uses the memory (compute) intensity as the scheduling heuristics. They assume that memory intensity implies high exploitable MLP ratio, and high compute-intensity implies high exploitable ILP ratio. If both ratios are low, scheduling to little core is a good idea.

However, this paper recognizes that those two assumptions are wrong. For example, for high memory intensity application, if the exploitable MLP difference is large on big and little cores (for example, the theoretical MLP is high, but the ILP is low such that the in-order core is not able to exploit the MLP but the out-of-order core is), then scheduling to the little core is a bad idea since the big core will out-perform little core significantly.

What they propose is that, when an application is memory intensive, we need to examine the MLP ratio, and when the application is compute-intensity, we need to look at the ILP ratio. They propose models to estimate/predict the exploitable ILP and MLP on big and little cores to make the scheduling decisions.

One potential optimization that comes into my mind is why not directly estimate/predict the IPC? After all, the exploitable ILP and MLP combined together is equivalent to the IPC. If we predict that the IPC of running on big and little is similar, we know scheduling to the little core is a good idea, and vice versa.

Potential ways of directly estimating IPC include:

* Using regression models such as what [Composite Cores](https://web.eecs.umich.edu/~twenisch/papers/micro12-composite.pdf) does.
* If the blackbox method is not preferred, I think it is still possible to construct a mechanistic model. For example, by examining how many MSHRs are used or the ROB utilization on a big core, it might be possible to predict the IPC on the small core. Maybe when one does this, it is essentially the same thing as estimating exploitable ILP and MLP separately.
