# How to Explain a Scientific Figure
*Dec 3, 2014*

A figure sometimes is more powerful than a hundred words in explaining scientific findings. However, putting figures in a paper without proper explanation might lead to confusion, or at the very least waste precious space real estate. Note that this article is not about how to display data in a figure, but about how to explain a figure, assuming it is elegantly crafted. There are many articles about the former, on which Edward Tufte has many great [articles](https://www.edwardtufte.com/bboard/q-and-a?topic_id=1) and [books](https://www.edwardtufte.com/tufte/courses) that I highly recommend.

The goal of explaining a figure is to guide the readers in the way that you want the information to be conveyed, rather than letting them fumble the balls. Just as many great computer engineering techniques, the methodology to explain a scientific figure ishierarchical—in fact a three-level process:

1. to describe the observation/trend in the figure;
2. to justify the observation/trend in the figure;
3. to identify the implications of the observation/trend in the figure.

Let me use an example, Fig. 15 from Prof. Benjamin Lee‘s [TACO 2010 paper](https://www.seas.upenn.edu/~leebcc/documents/lee2010-taco-inference.pdf), to elaborate this.

<p align="center">
    <img src="imgs/clusterfigure.png">
</p>

**Describe the observation/trend in the figure.** The first step is to describe what the figure is about. What is the x-axis, y-axis; what is the most important observation/trend that you want the readers to focus on. The goal is to provide a concise, but semantically equivalent, representation of the figure that quickly puts readers into the context. In his paper, he wrote:

>Figure 15 plots the delay and power characteristics of the nine benchmark architectures executing their corresponding benchmarks (radial points). Aggressive architectures with deep, wide pipelines are located in the upper left quadrant and the less aggressive cores with shallow, narrow pipelines are located in the lower right quadrant. Deep, narrow and shallow, wide architectures both occupy the moderate center.

The first sentence precisely describes what this figure is plotting through explaining what the x-axis, y-axis, and all the markers are in the figure. It is immediately clear that this figure is showing the delay vs. power relationship of nine benchmark architectures. The next sentence further makes an important observation, the one that the author wants readers to focus on, that there are four clusters scattered in different regions in the figure, with each cluster differing in their pipeline depth and width.

**Justify the observation/trend in the figure.** After describing the observation in the figure, typically you want to justify the observation or trend, essentially to explain why certain behaviors occurred. For instance in our example, the reason that shallow, narrow pipelines (cluster 4) are located in the lower right quadrant is because shallow and narrow pipelines provide limited computation capability, and therefore lead to long delay (toward right size of the x-axis) and low power (toward the bottom of the y-axis). In his paper, this step is omitted, perhaps because readers can naturally draw the link between pipeline depth and width with the corresponding delay and power characteristics. In other cases where the rationale is not so obvious, it is necessary to provide justifications to the trend in the figure.

**Identify the implication of the observation/trend in the figure.** This step is not required, and sometimes omitted for a better flow of the text; but whenever possible good writers always tend to identify insightful implications that the data in the figure teaches us beyond the figure itself. For example in the paper, he wrote:

>Figure 15 also reveals new opportunities for workload similarity analysis based on resource requirements at the microarchitectural level. For example, ammp, applu, equake, and twolf may be similar workloads … similarity exposed by microarchitectural clustering may be most useful for hardware accelerator design. In the ideal case, accelerators would be designed for every kernel of interest. However, resource constraints necessitate compromises and the penalties from such compromises may be minimized by designing an accelerator to meet the needs of multiple similar kernels.

From the observation that architectures optimized for individual applications tend to cluster depending on their microarchitecture parameters, the author pointed out the implications for future resource-constrained accelerator designs, that is to design a “compromise” accelerator that fits a range of applications, instead of designing separate accelerators for each application.

In summary, the principle of explaining scientific figures is to guide the readers, rather than throwing them into the dark end and letting them fumble the balls, by carefully describing the figure content, providing rationales of the data in the figure, and recognizing insights from the data that can pave the way for a potential future direction.
