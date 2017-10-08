# bookx the master algorithm

## prologue

The scariest form of complexity is human, when algorithms become too intricate for our poor brains to understand.

We can think of machine learning as the inverse of programming.  What algorithm gives this output?

Computer science has traditionally been about thinking deterministically, but machine learning requires thinking statistically.

Whoever learns the fastest wins.

Learning is a program that, when given a set of examples, produces a new program that attempts to generalize those examples to all cases.

## the master algorithm

Most of all, why is it that the simple, abstract language of mathematics can accurately capture so much of our infinitely complex world?

Science goes through three phases, which we can call the Brahe, Kepler and Newton phases.  In the Brahe phase, we gather lots of data, like Tycho Brahe patiently recording the positions of the planets night after night, year after year.  In the Kepler phase, we fit empirical laws to the data, like Kepler did to the planets' motion.  In the Newton phase, we discover the deeper truths.

Machine intelligence has 5 major camps:

- For __Symbologists__, all intelligence can be reduced to manipulating symbols, in the same way that a mathematician solves equations by replacing expressions with other expressions.
- For __Connectionists__, learning is what the brain does, and so what we need to do is reverse engineer it, inspired by neuroscience and physics.  The crucial problem is figuring out which connections are to blame for which errors and changing the accordingly.
- __Evolutionaries__ believe the mother of all learning is natural selection.  They simulate evolution to solve the key problem of learning structure, not just adjusting parameters.
- __Bayesians__ are concerned above all with uncertainty and believe learning is a form of probabilistic inference.  The problem then becomes how to deal with noisy, incomplete and even contradictory information without falling part.
- For __Analogizers__, the key to learning is recognizing similarities between situations and thereby inferring other similarities. The key problem is judging how similar two things are, then learn by extrapolating.

## hume's problem of induction / symbologists

Rationalists believe that the senses decieve and that logical reasoning is the only sure path to knowledge.  Empiricists believe that all reasoning is fallible and knowledge must come from observation and experimentation.

How can we ever be justified in generalizing from what we've seen to what we haven't?

_No free lunch:_ on average no learner can do better than random guessing.  In order to perform in one scenario, you must incorporate assumptions that one can exploit to make it underperform in another.  The practical consequence of the theorem is that there's no such thing as learning without knowledge.  __Data alone is not enough.__  However, since we only care about this world opposed to all the other imagined worlds, we can still make progress.

Machine learning is ill-posed, it doesn't have a unique solution.

### Overfitting

Whenever a learner finds a pattern in the data that is not actually true in the real world.  Overfitting is when you have too many hypotheses and not enough data to tell them apart.

You can figure out in advance how many examples you'll need to be pretty sure that the chosen hypothesis is very close to the true one, provided it fits all the data.  

Read _Probably approximately correct_.

Avoid overfitting

- Stop short of perfectly fitting the data even if we're able to
- Use statistical tests to make sure the observed patterns are really there
- Realize that some false hypotheses will get through, but keep their number 
  under control by rejecting enough low-significance ones
- Prefer simpler hypotheses

### Bias / Variance

Estimate bias & variance of a learner by comparing its predictions after learning on a random variations of the training set.  If it keeps making the same mistakes, the problem is bias and you need a more flexible learner.  If there's no pattern to the mistakes, the problem is variance and your either need a less flexible learner or more data.  Most learners have knobs to make them more or less flexible via different means.  This should be your first resort.

### Inverse deduction and decision trees

To learn a decision tree, pick at each node the attribute that on average yields the lowest class entropy across all its branches, weighted by how many examples go into each branch.

Inverse deduction is easily confused by noise.  Real concepts can seldom be concisely defined by rules.  They're not black and white, there's a lot of gray.

David Marr argued that every information processing system should be studied at 3 distinct levels: the fundamental properties of the problem it's solving; the algorithms and representations used to solve it; and how they are physically implemented.

## how does your brain learn / connectionists

Symbologist learning maintains a one-to-one correspondence between symbols and the concepts they represent, learning sequentially.  Connectionist representations are distributed across many neurons and each neuron participates in many concepts, learning in parallel.

_Perceptrons_ book by Minsky and Papert.

Who do you blame?  Who do you give credit to for correct outputs.  This credit-assignment problem is one of the central problems in machine learning.

A Boltzmann machine has a mix of sensory and hidden neurons.

One of futurist Paul Saffo's rules for forecasting is: look for the S curves.

### Gradient descent

For optimizing weights in a network.  Back propogation (backprop) is an efficient way to do it in a multilayer perceptron: keep tweaking the weights so as to lower the error and stop when all tweaks fail.  Do so layer by layer, tweaking each neuron based on how your tweaked the neurons it connects to.

There's no proof backprop can optimize to a global minimum, but we've come to realize tha tmost of the time a local minimum is fine.

Beware of attaching too much meaning to the weights backprop finds.  There are probably many different ones that are just as good.

Nonlinearity matters.  A network of linear neurons is no better than a single linear neuron.  Backprop is well suited because it can efficiently learn nonlinear functions.

We need high-level concepts to make sense of the morass of low-level details.

Neural networks are not compositional (composable?) and this is very import to human reasoning.  (but to computer reasoning as well?)

## evolution: nature's learning algorithm

TO READ: _The Genetic Theory of Natural Selection_

Genetic algorithms consist of several parts:

- A coded representation of each program's behavior
- A fitness function assigns the program a numeric score reflecting how well it fits its purpose.  
- mating the fittest individuals, producing 2 offspring from each pair of parents by crossing over their bit strings at random points.

Like multilayer perceptrons, classifier systems face the credit-assignment problem--what is the fitness of rules for intermediate concepts?

Genetic algorithms make no a priori assumptions about the structures they will learn from, other than their general form.  Because of this, genetic algorithms are much less likely than backprop to get stuck in a local optimum and in principle better able to come up with something truly new.  But they are also much more difficult to analyze.

One of the most important problems in machine learning--and life-- is the exploration-exploitation dilemma.

Why not evolve full-blow computer programs?

Evolutionaries focus on learning structure; to them, fine-tuning an evolved structure by optimizing parameters is of secondary importance.  In contrast, connectionists prefer to take a simple-hand-coded structure with lots of connections and let weight learning do all the work.

## In the Church of Reverend Bayes

The crucial question is exactly how the posterior probablity should evolve as you see more evidence.  The answer is Bayes' theorem.

Bayes' theorem is useful because what we usually know is the probability of the effects given the causes, but what we want to know is the probability of the causes given the effects.

Bayesians' answer is that a probability is not a frequency but a subjective degree of belief.

All models are wrong but some are useful.

Naive Bayes multiplies probabilities while the perception adds.  Taking the logarithm of naive Bayes reduces to the perceptron.

Markov chains represent the probability of the next instance given the prior instances.  While widely studied, they're fundamentally limited.  Extend the idea where the states still form a Markov chain but we don't get to see them; we have to infer them from observations.  This is called a hidden Markov model (slightly misleadingly, since the states are hidden, not the model.)

Naive Bayes, Markov chains and hidden Markov models are all special cases of Bayesian networks.

Bayesian networks in effect have "invisible" arrows to go along with the visible ones.  In a Bayesian network, all parents of the same variable are interdependent.

Approximate inference vial Markov chain Monte Carlo (MCMC).

Inference in Bayesian networks is not limited to computing probabilities.  It also includes finding the most probable explanation for the evidence.

We never know for sure which hypothesis is the true one, and so we shouldn't just pick one hypothesis; rather we should compute the posterior probability of every hypothesis and entertain them all when making predictions.

Bayesians can use the prior distribution to encode expert knowledge of the problem.  We can design and initial Bayesian network for medical diagnosis by interviewing doctors, asking them which symptoms they think depend on which diseases.

## You are what you resemble

>   "This sense of sameness is the very keel and backbone of our thinking".
>   -- William James

The reason lazy learning wins is that forming a global model, such as a decision tree, is much harder than just figuring out where specific query points lie one at a time.  Instead of trying to fit a straight line to all the data, you just fit it to the points near the query points, and you now have a very powerful linear regression algorithm.

Given enough data, nearest-neighbor is at worst only twice as error-prone as the best imaginable classifier.  _(but how much data is that?)_

In high dimensions, the notion of similarity itself breaks down.  No learner is immune to the curse of dimensionality.  It's the second worst problem in machine learning, after overfitting.

Examples may have a thousand attributes, but in reality they all "live" in a much lower-dimensional space.

Superficially, an SVM looks a lot like weighted k-nearest-neighbor: the frontier between the positive and negative classes is defined by a set of examples and their weights, together with a similarity measure.

The fewer support vectors an SVM selects, the better it generalizes.  An SVM can have an infinite number of parameters and still not overfit, provided it has a large enough margin.  We can view what SVMs do with kernels, support vectors and weights as mapping the data to a higher-dimensional space and finding a maximum-margin hyperplane in that space.  (The same has been said about deep neural networks)

## Learning without a learner

The notion that not all states have rewards (positive or negative) but every state has a value is central to reinforcement learning.

The real problem with reinforcement learning is you don't have a map of the territory.

Chunking concept from psychology of perception and memory.  Getting better at chess, and all skills, mainly involves acquiring more and larger chunks.  A chunk is a just a symbol that stands for a pattern of symbols.

A bunch of other comments about reinforcement learning and networks, but nothing that could easily be extracted.

## This is the world on machine learning

With the total value of labor greatly reduce, the wealthiest nations will be those with the highest ratio of natural resources to population.  (Move to Canada now)

Teaching ethics to robots, with their logical minds and lack of baggage, will force us to examine our assumptions and sort out our contradictions.