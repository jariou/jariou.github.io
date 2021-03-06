[toc]

Fitting a severity model with positive weight at zero
=====================

The problem we are considering here is the question of fitting a severity loss model when there is a non-zero probability of having a loss of 0. This is typically called a zero-inflated model in the frequency world and it is described as a mixture with a degenerate distribution at zero. Focusing on severity modeling, this is a situation that can arise when trying to model the distribution of loss-ratios for a population of insured in a typical non-life insurance portfolio. We propose to describe how to fit such models using maximum likelihood in the simple case where no risk factors or regressors are available as well as in the more interesting case with regressors.

The Problem
-----------
To formalize the simple problem and to fix some notation, we will assume that we have a set of $n$ observations $\{r_1, r_2, \ldots, r_n\}$ representing loss-ratios for the policies in a portfolio of $n$ insured. We assume that these are all independent and identically distributed as $R$[^ratio], a random variable with cumulative distribution function $F_R(r)$.


Since we know that the majority of insured should have a $0$ loss ratio, the simplest way to model $R$ is to use a distribution with a probability mass at 0. Therefore one could write $F_R$ as follows:
$$
F_R(y)=
\begin{cases}
p \, & y=0 \\
(1-p)F_Y(y), & y>0
\end{cases}\label{ref1}
$$
where we define the random variable $Y$ as 
$$
Y(R) =
\begin{cases}
undefined \, & R = 0 \\
R, & R > 0
\end{cases}
$$
The Solution
-----------
Let $n_0$ be the number of zero loss ratios, and let's suppose, for simplicity, that the observed loss ratios are ordered in such a way that those $n_0$ zero loss ratios are at the end of the list so that only the first $n^* = (n-n_0)$ are non-zero. If we write the log-likelihood function $L(.) =L(F_R, \{r_1, r_2, \ldots, r_n\})$ based on the model and the data, we get
$$
\begin{aligned}
L(.) & = \sum_{i=1}^n \log f_R(r_i)\\
     & = \sum_{i=1}^{n*} \log f_R(r_i) + \sum_{i=n^*+1}^n \log f_R(r_i)\\
     & = \sum_{i=1}^{n*} \left[\log f_Y(r_i) + \log(1-p)\right] + n_0 \cdot \log(p)\\
     & = \sum_{i=1}^{n*} \log f_Y(r_i) + (n - n_0) \cdot \log(1-p) + n_0 \cdot \log(p).
\end{aligned}
$$

To find the optimal values of $p$ and of the parameters of $F_Y$, we take the partial derivatives of $L(.)$ and set them equal to zero. These are the resulting equations:

$$
\frac{\partial L}{\partial p}  = \frac{-(n - n_0)}{(1-p)} + \frac{n_0}{p} = 0.
$$
this implies that 
$$
\begin{aligned}
 \frac{(n - n_0)}{(1-p)} & = \frac{n_0}{p}\\ 
 \frac{(n - n_0)}{n_0} & = \frac{(1-p)}{p} \\
 \frac{n}{n_0} - 1 & = \frac{1}{p} -1\\
 \frac{n}{n_0}  & = \frac{1}{p} \\
 p & =\frac{n_0}{n}   
\end{aligned}
$$
which is simply the proportion of zero loss ratios. Similarly, when differentiating with respect of any of the parameters of $F_Y$, the parameter $p$ will not show up and the same equations will be obtained as if one had simply tried to fit the $F_Y$ distribution to the sample of non-zero loss ratios.

The moral of this story, at this point, seems to be "Why bother with the more complicated model and a complex optimization problem? Simply solve two simpler problems: What is the proportion of zero loss ratios? What are the maximum likelihood estimates of the severity model of the non-zero loss ratios". This is not so much of a new discovery but more of a review of a somewhat simpler situation than the one we want to look at next, i.e. solve the same problem but considering covariates.

Time Exposure Consideration
-----------

The loss ratios observed are the ratios of losses to earned premiums. It means that every single observation is in fact a composite of three items; total benefit paid, premium rate, and time exposure. An overly simplified example would be if all policies are in force at the beginning of the insurance period and stayed in force until the end of the period and furthermore that all losses occur at the end of the period. In that case, the loss ratio would simply be the ratio from loss to premium rate and nothing else would need to be said. However, the time exposure component plays a very important role in determining the distributional form of the loss-ratios distribution and as we will see it causes the tail of its distribution to be typically much thicker than that of the base loss model.

A most natural assumption for the distribution of time exposure is that it should be uniform between $0$ and $1$. This is valid if all policies are in force at the beginning of the study period, they stay in force until at least the time of a loss, and that losses occur uniformly in time throughout the observed insurance period. This assumption is perfectly consistent with the usual Poisson process arrival-time model of insured events. We do not include the concept of withdrawal in the present modeling exercise.   

In practice, however, there might be some valid reasons to change that hypothesis to something slightly different because of peaks in policy cancellations or in incoming new policies at a different rate. An example might be a very focused enrollment campaign during a certain period that may increase the enrollment rate immediately and after the campaign. Since we want to describe a run of the mill model and the non-trivial resulting  implications on the loss ratios distribution, we do not include any such complications here. For a specific real life situation, the model could be extended to support different such assumptions.

###Time Exposure Formalized

If we formalize our enhanced assumptions, we obtain that the loss-ratio random variable $R$ can be written as 
$$
R = \frac{L}{\rho\cdot U}
$$
where $\rho$ is the premium rate and $U$ is a $Uniform[0,1]$ random variable representing the fraction of the time interval for which the risk has been insured. Assuming further that we have a model $F_L$ for the distribution of losses, we can obtain the distributions of $R$ are as follows.

$$
\begin{aligned}
F_R(r) & = Pr\left[ R \le r\right]\\
& = Pr\left[ \frac{L}{\rho\cdot U} \le r\right]\\
& = Pr\left[ L \le r\cdot\rho\cdot U\right]\\
& = F_L\left(r\cdot\rho\cdot U\right)\\
& = \int_{0}^{1}F_L\left(r\cdot\rho\cdot U\left|\right.\, U=u\right)\cdot f_U(u)\, du\\
F_R(r) & = \int_{0}^{1}F_L\left(r\cdot\rho\cdot u\right) \, du.
\end{aligned}
$$
For simplicity, if we set the constant premium rate $\rho$ equal to $1$
$$
F_R(r)  = \int_{0}^{1}F_L\left(r\cdot u\right) \, du.
$$

###**Example 1**

Suppose $F_L$ is the cdf of an $Exponential(\theta)$  model
$$
F_L(r) = 1- \frac{\exp(-r/\theta)}{\theta},
$$
we can write an exact expression for the distribution of $F_R$.
$$
\begin{aligned}
F_R(r) & = \int_{0}^{1}F_L\left(r\cdot u\right) \, du\\
 & = \int_{0}^{1} 1- \frac{\exp(-r\cdot u/\theta)}{\theta} \, du\\
 & = \left. u\right|_0^1 -\left. \frac{\theta}{r}\cdot \exp-\left(\frac{r\cdot u}{\theta} \right)\right|_0^1\\
 F_R(r)& = 1 - \frac{\theta}{r}\cdot\left[1- \exp\left(\frac{-r u}{\theta}\right) \right]\\
\end{aligned}
$$
and
$$
 f_R(r) =  \frac{\theta}{r^2} -\left(\frac{\theta+r}{r^2}  \right)\exp\left(\frac{-r u}{\theta}\right)
 $$

###Mean and Variance of Loss-Ratios
Taking expectation on both sides of equation (1), we get
$$
\begin {aligned}
E\[R\] &= E\[\frac{L}{\rho\cdot U}\]\\
&= E[E []]\\
& = 
$$

In [equation](#equation), we find the value of an interesting integral:

######  {#equation}
\begin{equation}
  \int_0^\infty \frac{x^3}{e^x-1}\,dx = \frac{\pi^4}{15}
\end{equation}

$\int_a^b$

Same Problem with Covariates
-----------
 Now, we want to look at

an insurance  to describe a loss ratio severity models, such
Hello, I am your first Markdown document within **StackEdit**[^stackedit]. Don't delete me, I can be helpful. I can be recovered anyway in the `Utils` tab of the <i class="icon-cog"></i> `Settings` dialog.

----------





> - about **Markdown** syntax [here][2],
> - about **Markdown Extra** extension [here][3],
> - about **LaTeX** mathematical expressions [here][4],
> - about **Prettify** syntax highlighting [here][5],
> - about **Highlight.js** syntax highlighting [here][6].

[^ratio]: R as Ratio.

[^stackedit]: [StackEdit](https://stackedit.io/) is a full-featured, open-source Markdown editor based on PageDown, the Markdown library used by Stack Overflow and the other Stack Exchange sites.

  [^gfm]: **GitHub Flavored Markdown** (GFM) is supported by StackEdit.

  
  
  [1]: http://math.stackexchange.com/
  [2]: http://daringfireball.net/projects/markdown/syntax "Markdown"
  [3]: https://github.com/jmcmanus/pagedown-extra "Pagedown Extra"
  [4]: http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference
  [5]: https://code.google.com/p/google-code-prettify/
  [6]: http://highlightjs.org/


