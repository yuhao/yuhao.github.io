# How Did Researchers Predict the End of the Road for Conventional Microarchitectures 14 Years Ago

*Dec 8, 2014*

Over the past a few decades, we have been witnessing drastic changes in microprocessors. If I have to summarize, there are two milestone transitions: from single core to multicore, and from multicore toward more specialized accelerators. While it’s clear that the so-called [dark](https://www.cc.gatech.edu/~hadi/doc/paper/2011-isca-dark_silicon.pdf) [silicon](http://cseweb.ucsd.edu/~jsampson/ConservationCores.pdf) is the reason that we are gradually shifting from the multicore era to the specialization regime, it is not as clean and easy to explain why we moved from single core to multicore.

Well I said not easy, but in computer architecture, there are really only two things that matter: performance and power. Yes. These are the two reasons that drove the single core to multicore transition, and I would argue that these two factors somehow intertwined together to push that transition.

* From a performance perspective, single core performance improvement was stagnated, and people needed a way out to sustain the historical performance scaling.
* From a power perspective, the [power consumption/density](https://people.cs.clemson.edu/~mark/330/power_density.gif) of a chip had been so high such that keep the traditional microarchitecture scaling would be effectively impossible.

This seminal paper [Clock Rate versus IPC: The End of the Road for Conventional Microarchitectures](https://www.cs.utexas.edu/users/skeckler/pubs/isca00.pdf) published 14 years ago (ISCA 2000) basically explained the performance aspect of the problem: why conventional microarchitecture techniques could not deliver the promised performance improvement. I guess not many people would disagree that this is one of the must-reads in the computer architecture (even though the paper gets less and less attention recently.).

Conventional microarchitecture scaling techniques have been to increase both the frequency and IPC. Remember performance is proportional to Frequency (i.e., 1/cycle time) * IPC.

* The way to increase the frequency is through 1) process advancement (thanks to Dennard Scaling) , and 2) less logic in each pipeline stage, i.e., deep pipelining.
* The way to increase IPC is mainly to leverage the additional transistors from each generation (thanks to Moore’s Law) to increase the sizes/capacity of many important structures such as instruction window, cache sizes, bypass network, etc.

However, this paper argues that we can no longer get a faster clock rate and a high IPC at the same time. The major reason is that, relative to the faster transistor, the wire delay has grown over the generations. The access delay of important memory structures is directly affected by the wire delay.

* Two types of memory structures that are affected by wire delay: cache (including BTB, BPT, RF, etc.) and CAM (TLP, IW, etc.).
* Bypass network is also strongly effected by the wire delay, but is not discussed in the paper.
* Fewer bits in the memory structures can be reached, and fewer portion of the chip can be reached, in one cycle

Therefore, with a faster clock rate, the IPC will drop because the access delay to those structures will take more cycles (e.g., multiple cycles to select an instruction).

To figure out the solution, let’s first examine what design choices are there:

* Feature size. However, we always want to embrace smaller feature size because process technology has been rewarding us so much (again, thanks to Dennard scaling) such that it’s extremely unlikely that we would give up scaling the feature size.
* Logic in each pipeline stage, i.e., how many FO4 delays in each stage: this will decides the pipeline length.
* The capacity of the memory structures: remember that the access latency to them strongly depends on the wire delay.

We have a few solutions corresponding to the latter two design choices (remember the feature size has to keep scaling):

* Stop deepening the pipeline, i.e., do not decrease the FO4 delay in each stage. That’s why the paper simulates three scenarios: fixed 16 FO4, fixed 8 FO4, and SIA scaling which will eventually get to 5.9 FO4
* Keep increasing the structure capacity, which will need more cycles to access, but hopefully we can buy back the performance with higher IPC.
* Reduce the structure capacity such that they can still be accessed in the original number of cycles. Hopefully we can buy back the performance with fewer numbers of accessing cycles.

If you take a look at Table 7 in the paper, it essentially shows that as we increase the frequency, the IPC already decreases, i.e., we can not get both frequency and IPC improvement at the same time. The end result it that none of the above three techniques will lead to more than 7.4X performance improvement from 250 nm to 35 nm.
