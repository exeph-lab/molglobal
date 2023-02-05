## Topic

There's an intriguing [paper](https://github.com/exeph-lab/exeph-library/blob/main/local/GA-NN-global-opt-amc21314.pdf) on global molecular geometry optimization using a combination of genetic algorithm and neural network. While dated, it's a good starting point to try out connecting machine learning to quantum chemistry. Instead of direct replication of the results, I'm more interested in studying the paper and getting answers to the following questions:
- [x] What is the GA setup? What parameters are used to construct candidate structures for local optimization?
- [x] What is the order of the number of local optimizations needed to converge to a global minimum?
- [x] Are the tools / libraries used here still available, what are their up-to-date equivalents, is it possible to track down the driver code beyond "we have written the program in C language"?
- [x] Regardless of the previous point (although that would help), can we mock up an analogue using Python and *Gaussian* (or *VeloxChem*, or whatever)?

The topic is most relevant to RG (implementation part) and AX (instrumentation part, also ties in with the upcoming *Gaussian* computations), but anyone is welcome to pitch in if interested. If the answer to one of the marked questions above is fairly straightforward, it may be written directly in a comment, although link to a collaborative report (e. g. Markdown file) would probably be more orderly.

## Report (23-01-28)

[Here](https://github.com/exeph-lab/exeph-library/blob/main/local/GA-NN-global-opt-amc21314-annotated.pdf) I included my version of this paper with annotations. Note that the annotations seem not to show up on Github.

The algorithm described in the paper might be hard to **reproduce** as there is some missing information and I didn't manage to find any supplementary material. The genetic algorithm (GA henceforth) part was somewhat vague, but I speculate that the authors simply allowed the neural network (NN henceforth) to pick any of the options implemented by the GAUL library. Additionally, I struggle to understand the NN training procedure. The training and testing datasets seem to overlap (maybe they are even the same; I question whether the word "alkanes" was used to keep some ambiguity) and the training dataset seems to consist of very few data points. Moreover, what is the ground truth that the NN should be converging to?

### Having said that, regarding your first question:

there are 6 parameters that the NN takes as an input:
* min,
* avg,
* max
distances between chromosomes [bits] (previous generation),
* max,
* avg
values of fitness (previous generation),
* fraction that indicates how many generations ago max fitness value was reached,

and 6 parameters that the NN spews out for the GA:
* mutation rate,
* crossover rate,
* type of crossover,
* type of mutation,
* crossover selection,
* mutation selection.

All the parameters that the NN works with are mapped to float numbers in [0, 1] range. The NN itself is rather simple: a multilayer perceptron with 6 layers (or 4 hidden layers if you prefer) and with 6 neurons per layer.

Additionally, a fitness function is provided, which superficially looks ok. The lower the energy and runtime the higher the agents score. Plus the energy part is 1000 times more important. 

### Regarding your second question:

The outline of their algorithm is pretty clear (see the image below). The thing that interests me is that their while cycle runs for a fixed amount of generations rather than achieving a certain amount of convergence. On the other hand, this might be non trivial to implement with GAs.
<details>
<summary><b>Algorithm</b></summary>

![image](https://user-images.githubusercontent.com/59236770/215255538-76d30622-f7b4-4db7-9390-579eb72c0363.png)

</details>

### Regarding your third question:

The two libraries that were used by the authors of the paper are [GAUL](https://gaul.sourceforge.net/) and [FANN](http://leenissen.dk/fann/wp/). Both libraries were last updated roughly 8 years ago and both seem to be somewhat well documented. They are still available ([here](https://sourceforge.net/projects/gaul/) and [here](http://leenissen.dk/fann/wp/download/)) and, superficially, I see no reason why they should have stopped working. On the other hand, FANN is definitely outdated. From my skimming through their site I gather that only multilayer perceptron type of NNs is implemented. This might or might not be good enough for the task at hand, but nowadays there are more exotic options. Regarding more recent alternatives pick your poison [here](https://github.com/josephmisiti/awesome-machine-learning#c-general-purpose-machine-learning).

### Regarding your fourth question:

Something similar is definitely **implementable**. With Python it would take me significantly less time, but I can look into the APIs of Gaussian or other packets and try to do what it takes and get it working too. But then there is the question of what specifically do we want implemented, because even though the idea to perform meta-learning using various statistics is cool and interesting, from my readthrough of the paper I gather that their approach was roughly as good as static GA (at least for specific parameter sets that the authors chose to showcase their results). On the other hand, the dynamic GA sometimes converges faster. Finally, they didn't compare their results to what they computed with AMMP package or any other regular approaches, so it's hard to say how much merit is there in their approach.

### Finally, I feel like this belongs here:
<details>
<summary><b>You merely adopted the pink</b></summary>

![Image](https://user-images.githubusercontent.com/59236770/216775069-54245ae6-5edf-4e68-905e-cc2e4e14eff6.jpg)

</details>

