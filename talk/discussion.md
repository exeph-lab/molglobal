## Comments on initial report (23-02-05)

> The algorithm described in the paper might be hard to **reproduce** as there is some missing information and I didn't manage to find any supplementary material. The genetic algorithm (GA henceforth) part was somewhat vague, but I speculate that the authors simply allowed the neural network (NN henceforth) to pick any of the options implemented by the GAUL library. 

Yes, the procedure is rather scarcely described in the paper. That's why I take the idea as an inspiration rather than for replication.
 
> Additionally, I struggle to understand the NN training procedure. The training and testing datasets seem to overlap (maybe they are even the same; I question whether the word "alkanes" was used to keep some ambiguity) and the training dataset seems to consist of very few data points. Moreover, what is the ground truth that the NN should be converging to?

It's based on total (optimized) energy -- the lower the energy, the more likely is the candidate to be correct/"the best".

> there are 6 parameters that the NN takes as an input:
> * min, avg, max distances between chromosomes [bits] (previous generation),
> * max, avg values of fitness (previous generation),
> * fraction that indicates how many generations ago max fitness value was reached,
> 
> and 6 parameters that the NN spews out for the GA:
> * mutation rate,
> * crossover rate,
> * type of crossover,
> * type of mutation,
> * crossover selection,
> * mutation selection.
> 
> All the parameters that the NN works with are mapped to float numbers in [0, 1] range. The NN itself is rather simple: a multilayer perceptron with 6 layers (or 4 hidden layers if you prefer) and with 6 neurons per layer.
> 
> Additionally, a fitness function is provided, which superficially looks ok. The lower the energy and runtime the higher the agents score. Plus the energy part is 1000 times more important. 

OK, but **how do the chromosomes look like? How are they converted from/to initial atomic structure (that is then locally optimized)**?

> The outline of their algorithm is pretty clear (see the image below).

I saw the algorithm. What I'd like to know is how many local optimizations were needed to get the "answer" during test phase. I think convergence graphs have some answer on that.

> The two libraries that were used by the authors of the paper are [GAUL](https://gaul.sourceforge.net/) and [FANN](http://leenissen.dk/fann/wp/). Both libraries were last updated roughly 8 years ago and both seem to be somewhat well documented. They are still available ([here](https://sourceforge.net/projects/gaul/) and [here](http://leenissen.dk/fann/wp/download/)) and, superficially, I see no reason why they should have stopped working. On the other hand, FANN is definitely outdated. From my skimming through their site I gather that only multilayer perceptron type of NNs is implemented. This might or might not be good enough for the task at hand, but nowadays there are more exotic options. Regarding more recent alternatives pick your poison [here](https://github.com/josephmisiti/awesome-machine-learning#c-general-purpose-machine-learning).

Well, if the authors' code (even on the "at least names of library routines used" level) cannot be found, then there's no point in trying to redo this in C.

> Something similar is definitely **implementable**. With Python it would take me significantly less time, but I can look into the APIs of Gaussian or other packets and try to do what it takes and get it working too. 

You won't need to hook *Gaussian* itself into *Python* scripts, just read/write its input/output files.
AX might have helped to set up running *Gaussian* inputs created by GA/NN, but he is unresponsive as of now, so the options are:
- act as a middle-man, running the jobs manually,
- try and automate that yourself,
- look into *VeloxChem* package which has common QC drivers and is designed to be interfaced by *Python*.

I am asking about chromosomes, NN params, overall design etc. so that we have as clear idea as possible on **what data the external optimizer needs to work**; I will then be able to help to actually get it.

> But then there is the question of what specifically do we want implemented, because even though the idea to perform meta-learning using various statistics is cool and interesting, from my readthrough of the paper I gather that their approach was roughly as good as static GA (at least for specific parameter sets that the authors chose to showcase their results). On the other hand, the dynamic GA sometimes converges faster. Finally, they didn't compare their results to what they computed with AMMP package or any other regular approaches, so it's hard to say how much merit is there in their approach.
> ### Finally, I feel like this belongs here

If that's a dig at the authors, you are actually right in this case: they haven't done anything advanced with AI-inspired part, and while "dynamic" GA might be faster than a static version, as long as it needs hundreds of local optimizations without shortcuts, it will remain unfeasible in broad practice. However, I have some things to try on this subject, especially on information sharing / "live" decisions, so I'd like to have a somewhat-working use case with basic structure that can then be tested for improvements.

---
