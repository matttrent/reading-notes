# statistics

## general

**population:** collection of all possible occurances of a measurement

**sample:** subset of the population for which data has been collected, which should always been randomly selected from the population

**bias:** patterns or non-randomness in sampling the population

## random sampling

multple strategies exist for sampling a subset of the data (either during or after collection):

- **simple random sampling:** randomly selecting a subset of the observation
- **stratified sampling:** grouping similar cases to cases together based on similar traits and randomly selecting from each strata
- **cluster sampling:** grouping the cases together based on similar traits and selecting certain clusters
- **multistage sampling:** performing cluster sampling, collecting a random sample from those clusters

stratified sampling tends to be used when the variability within groups is small and the variability between groups is large (when the property used to choose strata is thought to be descriptive of outcome).  cluster sampling tends to be used when in-group variability is large and between-group variabliy is small (not predictive of outcome).

## experiment design

randomized experiments are built on 4 properties:

- **controlled:** study designers attempt to control any other differences between groups besides the ones they are studying.
- **randomized:** participants are randomly assigned to the different groups
- **replicatable:** collecting enough data that the results they observe are generalizable to the greater population.  also documenting their methods clearly enough to be performed again
- **blocked:** if studying the effect of the experiment on different types of subjects (e.g. low/high risk) they divide subjects and assign equal numbers of each to the control groups

## probability

**probability:** likelihood that an outcome would occurr if we observed a process infinitely many times

**law of large numbers:** the proportion $\hat{p_n}$ of occurances with a particular outcome converges to the probability $p$

**disjoint / mutually exclusive:** outcomes are said to be disjoint if they cannot both happen

addition rule of disjoint outcomes

$$
P(A_1 \textrm{ or } A_2 \textrm{ or } \dots \textrm{ or } A_n) = 
P(A_1) + P(A_2) + \dots + P(A_n)
$$

general addition rule, for both disjoint and non-disjoint outcomes.  simplifies to the disjoint addition rule when outcomes are disjoint.

$$
P(A \textrm{ or } B) = P(A) + P(B) - P(AB)
$$

the __complement__ of an event $A$ is denoted as $A^c$ and $A^c$ represnts all outcomes not in $A$ and are related s.t.

$$
P(A) = 1 - P(A^c)
$$

**marginal probability:** probabilities based on a single variable without condition on any other variable

**join probability:** probability of two or more variables

the __conditional probability__ of $A$ given $B$ is computed as

$$
P(A|B) = \frac{P(A \textrm{ and } B)}{P(B)}
$$

__general multiplication rule__, if $A$ and $B$ represent 2 outcomes

$$
P(A \textrm{ and } B) = P(A|B) \times P(B)
$$

if $A_1, \ldots, A_k$ represent all disjoint outcomes for a variable, then if $B$ is an event for another process, the sum of conditional probabilities equals 1

$$
P(A_1|B) + \cdots + P(A_k|B) = 1
$$

two processes are __independent__ if knowing the outcomes of one provides no information about the other

## bayes theorem

Bayes' theorem is given as

$$
P(A|B) = \frac{ P(B|A)P(A) }{ P(B) }
$$

consider the following conditional probability

$$
P(\textrm{ outcome } A_1 \textrm{ of variable 1 } | \textrm{ outcome } B \textrm{ of variable 2 })
$$

Bayes' Theorem states that the conditional probability can be identified by the following fraction:

$$
P(A_1|B) = 
\frac
{ P(B|A_1)P(A_1) }
{ \sum_{i=1}^k P(B|A_i)P(A_i) }
$$

where $A_2, \dots, A_k$ represent all other possible outcomes of the first variable.

In practice when working out conditional probabilities, consider using a **tree diagram**.

## random variables

the __expected value__ of a random variable:

$$
E(X) = \sum_{i=1}^k x_iP(X=x_i)
$$

since this is the mean of the expected variable, weighting each outcome by its probability $\mu$ may be used in place of $E(X)$

the __variance__ of a random variable:

$$
\sigma^{2} = \sum_{j=1}^k (x_j - \mu)^{2} P(X = x_j)
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

### geometric distribution

a **bernoulli random variable** has exactly 2 possible outcomes.  if $X$ is a random variable with probability of success $p$ then

$$
\mu = p
\qquad
\sigma = \sqrt{p(1-p)}
$$

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

### binomial distribution

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

the __negative binomial distribution__ describes the probability of observing the $k^{th}$ success on the $n^{th}$ trial.  all the same conditions apply as above.

$$
P(k^{th} \textrm{ success on } n^{th} \textrm{ trial}) = 
\binom{n-1}{k-1}p^k(1-p)^{n-k}
$$

- in the binomial case we have a fixed number of trials and consider the number of successes.  
- in the negative binomial case, we have a fixed number of successes and consider the number of trials to reach that number

### poisson distribution

the __poisson distribution__ is used for estimating the number of rare events over a unit of time.  for rate $\lambda$

$$
P(\textrm{observe } k \textrm{ rare events}) = 
\frac{\lambda^k e^{-\lambda}}{k!}
$$

the mean and standard deviation are $\lambda$ and $\sqrt{\lambda}$ respectively.

## foundations for inference

**Statistical inference** is concerned primarily with understanding the quality of parameter estimates.  For example, a classical inference question is, "How sure are we that the estimated mean, $\bar{x}$, is near the true population mean $\mu$?"

### variability of estimates

We want to estimate the population mean $\mu$ based on a sample.  The mean of that sample is known as a **point sample**.  If we collect multiple samples from the population the mean of each sample will vary slightly, since none represents the true population mean.   Repeating this process we can accumulate a **sampling distribution** of variation in our sample means.  The properties of the sampling distribution relate to the population as such:

$$
{\rm mean}(\bar{x}) \approx \mu 
\qquad 
SD(\bar{x}) < \sigma
$$

Given a sample of $n$ observations, the **standard error** of the sample mean 
$\bar{x}$ is
$$
SE = \frac{\sigma}{\sqrt{n}} 
$$

### central limit theorem

The distribution of sample statistics is nearly normal, centered at the population mean, with a standard deviation equal to the population standard deviation divided by the sample size

$$
\bar{x} 
\sim N \left(
mean=\mu, 
SE = \frac{\sigma}{\sqrt{n}} 
\right)
$$

Important conditions to help ensure the sampling distribution of $\bar{x}$ is nearly normal and the estimate of SE is sufficiently accurate:

- Sample observations are independent (<10% of population)
- Sample size is large: $n \geq 30$ is a good rule of thumb
- The population distribution is not strongly skewed.  The larger the sample size, the more we can relax this criteria.

If the conditions fail to meet these critera, we don't have sufficient tools to assess it yet.

###  confidence intervals

If the point estimate follows a normal model with standard error $SE$, then the confidence interval for the population parameter is:

$$
CI = {\rm point\ estimate} \pm z^*SE
$$

where the critical value, $z^*$, corresponds to the confidence level selected.  $z^*SE$ is known as the **margin of error**.  In the case of the population mean, the confidence interval corresponds to:

$$
\bar{x} \pm z^* \frac{s}{\sqrt{n}}
$$

To compute the critical value, $z^*$, one figures out the z-score that spans the desired confidence level centered in the distribution.  For example, if a 95% confidence level is desired, the range must span from 0.025 to 0.975.  Using a z-table or equivalent, these probabilities correspond to z-scores of $\pm1.96$, which is the value of $z^*$.  If the symbol $\alpha$ is used, the quantity of $z^*$ is relate by $z^* = 1-\alpha$.

*Example interpretation:* Based on a survey of 1,154 responses, a 95% confidence interval for the average number of hours Americans have to relax or pursue activities that they enjoy after an average day of work was found to be between 3.53 and 5.83 hours. Which of the following are true?

1. 95% of Americans spend between 3.53 and 3.83 hours relaxing after a work day.
   **FALSE.** The confidence interval is not about individuals in the population but instead about the true population parameter.
2. 95% of random samples of 1,154 Americans will yield confidence intervals that contain the true average number of hours relaxing after a work day.
   **TRUE.** This is the definition of the confidence level.
3. 95% of the time the true average number of hours Americans spend relaxing after a work day is between 3.53 and 3.83.
   **FALSE.** The population parameter is not a moving target that is sometimes within an interval and sometimes outside of it. This is frequentist statistics
4. We are 95% confident that Americans in this sample spend on average 3.53 to 3.83 hours relaxing after a work day.
   **FALSE.** The confidence interval is not about the sample mean, but the population.