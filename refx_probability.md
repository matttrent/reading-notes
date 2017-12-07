# refx probability



the __complement__ of an event $A$ is denoted as $A^c$ and $A^c$ represnts all outcomes not in $A$ and are related s.t.
$$
p(A) = 1 - p(A^c)
$$
__probability of a union__ of two events
$$
p(A \textrm{ or } B) = p(A \cup B) = p(A) + p(B) - p(A \cap B)
$$
**disjoint / mutually exclusive:** outcomes are said to be disjoint if their intersection is an empty set.  their union simplifies to the sum of the two events
$$
p(A \cap B) = \varnothing \\
p(A \cup B ) = p(A) + p(B)
$$
**joint probability:** intersection of probability of two or more variables
$$
p(A,B)   = p(A\cap B) \\
p(A,B) =  p(A|B) p(B) = p(B|A)p(A)
$$
**marginal probability:** probabilities based on a single variable without condition on any other variable.  You can obtain a marginal probability by summing over the joint probabilities of the other random variable, as
$$
p(A) = \sum_b p(A, B) \\
p(A) = \sum_b p(A|B=b)p(B=b)
$$
__conditional probability:__ (intersection, normalized on $B$) of $A$ given $B$ is computed as
$$
p(A|B) = \frac{p(A, B)}{p(B)}\ \ \textrm{ if}\ \ p(B) > 0
$$
**unconditionally independent**

**conditionally independent**

## bayes theorem

Bayes' theorem is given as
$$
p(A|B) = \frac{ p(B|A)p(A) }{ p(B) }
$$
consider the following conditional probability
$$
p(\textrm{ outcome } A_1 \textrm{ of variable 1 } | \textrm{ outcome } B \textrm{ of variable 2 })
$$
Bayes' Theorem states that the conditional probability can be identified by the following fraction:
$$
p(A_1|B) = 
\frac
{ p(B|A_1)p(A_1) }
{ \sum_{i=1}^k p(B|A_i)p(A_i) }
$$
where $A_2, \dots, A_k$ represent all other possible outcomes of the first variable.  In practice when working out conditional probabilities, consider using a tree diagram.

## random variables

the __expected value__ of a random variable:
$$
E(X) = \sum_{i=1}^k x_i p(X=x_i)
$$
since this is the mean of the expected variable, weighting each outcome by its probability $\mu$ may be used in place of $E(X)$

the __variance__ of a random variable:
$$
\sigma^{2} = \sum_{j=1}^k (x_j - \mu)^{2} p(X = x_j)
$$
where the __standard deviation__ $\sigma$ is the square root of the variance

the __expected value__ and __variance__ of random variables can be preserved under linear transforms.  if $X$ and $Y$ are random variables, then for fixed numbers $a$ and $b$:
$$
E(aX+bY) = aE(X) + bE(Y)
\\
Var(Ax+By) = a^{2} Var(X) + b^{2} Var(Y)
$$

### normal distribution

the **z-score** of an observation is defined by how many standard deviations it falls above or below the mean, given mean $\mu$ and standard devation $\sigma$.  most books give the normal probability in the table as the area to the left.
$$
Z = \frac{x-\mu}{\sigma}
$$

### bernoulli distribution

a **bernoulli random variable** has exactly 2 possible outcomes.  if $X$ is a random variable with probability of success $p$ then
$$
\mu = p
\qquad
\sigma = \sqrt{p(1-p)}
$$

### binomial and hypergeometric distributions

the __binomial distribution__ describes the probability of having exactly $k$ successes in $n$ independent bernoulli trials with probability $p$.  the trials must be fixed and independent, and each trial must have a binary outcome and share the same probability
$$
\binom{n}{k} p^k (1-p)^{n-k}
$$
the mean and standard deviation are
$$
\mu = np 
\qquad
\sigma = \sqrt{np(1-p)}
$$
when the sample size $n$ is sufficiently large that both $np$ and $n(1-p)$ are at least 10, the normal distribution can be used to approximate a binomial distribution with the same mean and standard deviation as the binomial distribution.

the __hypergeometric__ describes the probability of having exactly $k$ successes in $n$ bernoul trials, _without replacment_.  In this case we also consider the total population size $N$ and number of succesful states in the population $K$.
$$
\frac
{\binom{K}{k}\binom{N-K}{n-k}}
{\binom{N}{n}}
$$
the mean and standard deviation are
$$
\mu = \frac{nK}{N} 
\qquad
\sigma = \sqrt{
\frac
{nk(N-K)(N-n)}
{N^2(N-1)}}
$$

### geometric and negative binomial distributions

a __geometric distribution__ arises from the repeated evaluation of a bernoulli variable form, describing the probability of finding the first success on the $n^{th}$ trial
$$

(1-p)^{n-1}p

$$
the mean and standard deviation are
$$
\mu = \frac{1}{p} 
\qquad
\sigma \ = \sqrt{\frac{1-p}{p^2}}
$$
the __negative binomial distribution__ describes the probability of observing the $k^{th}$ success on the $n^{th}$ trial.  all the same conditions apply as above.
$$
p(k^{th} \textrm{ success on } n^{th} \textrm{ trial}) = 
\binom{n-1}{k-1}p^k(1-p)^{n-k}
$$

- in the binomial case we have a fixed number of trials and consider the number of successes.  
- in the negative binomial case, we have a fixed number of successes and consider the number of trials to reach that number

### poisson distribution

the __poisson distribution__ is used for estimating the number of rare events over a unit of time.  for rate $\lambda$
$$
p(\textrm{observe } k \textrm{ rare events}) = 
\frac{\lambda^k e^{-\lambda}}{k!}
$$
the mean and standard deviation are $\lambda$ and $\sqrt{\lambda}$ respectively.