# A brief introduction to density estimation

Let $(X_1,\ldots,X_n)$ be identically distributed real valued random variables whose common distribution has density $p_X$ with respect to the Lebesgue measure, i.e. $\mathbb{P}(X \in A) = \int_{A}p_X(x)dx$ for all $A \subset \mathbb{R}$. Our goal is to estimate $p_X$ from the data $(X_1,\ldots,X_n)$. 

### The parametric way

One classic way to do so is to assume that $p_X$ belongs to a parametric family $$\{f_{\theta} : \theta \in \Theta \}$$ where $\Theta$ is a subset of $\mathbb{R}^k$ with a fixed dimension $k$. For example, one may assume that the data $(X_1,\ldots,X_n)$ was sampled from a normal distribution. In this case, $k=2$, $$\Theta = \{(\mu, \sigma^2) : \mu \in \mathbb{R}, \sigma > 0\}$$ and $f_{\theta}(.) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(. - \mu)^2}{2\sigma^2}}$.

How do we estimate the parameter $\theta$ ? A theoretically-justified method of estimation is to choose $\hat{\theta}$ that maximizes the log-likelihood $l_\theta$ of the data :

$$l(\theta) = \sum_{i=1}^n \log f_{\theta}(X_i)$$

Now, we hope that computing the solutions of $\nabla l(\theta) = 0$ gives one and only one maximum likelihood estimate (MLE). This is the case for the above example : the MLE is $\hat{\theta} = (\hat{\mu}, \hat{\sigma}^2)$ where $\hat{\mu} = \frac{1}{n}\sum_{i=1}^nX_i$ and $\hat{\sigma}^2 = \frac{1}{n}\sum_{i=1}^n(X_i - \hat{\mu})^2$, as expected.

### The nonparametric way

Unfortunately, the data we gather can be too complex and prior information about $p_X$ may be not available. Murray Rosenblatt, in an article published in 1956, suggested a nonparametric method for estimating $p_X$ using the weak assumption that $p_X$ is continuous function. Here's his reasoning :

If $p_X$ is continuous, then the cumulative distribution function of $X$ defined by $F_X(x) = \mathbb{P}(X\leq x)$ for all $x \in \mathbb{R}$ is differentiable and its derivative is continuous. Indeed, $F_X(x) = \int_{-\infty}^{x}p_X(u)du$, so $F_X'(x) = p_X(x)$ for all $x \in \mathbb{R}$, which means that for $h > 0$ small :

$$p_X(x) \approx \frac{F_X(x+h) - F_X(x-h)}{2h}.$$

As an estimate of $F_n$, let's use the usual empirical cumulative distribution function defined as $$\hat{F}_n(x) = \frac{1}{n}\sum_{i=1}^n 1_{X_i \leq x}$$. Then, our estimate of $p_X$ is :

$$\hat{p}_h(x) = \frac{\hat{F}_n(x+h) - \hat{F}_n(x-h)}{2h},$$

Where $h>0$ is the bandwidth hyperparameter.

Let's write it differently so we can better understand what's going on.

$$\hat{p}_h(x) = \frac{1}{2nh}\sum_{i=1}^n 1_{x-h<X_i\leq x+h}.$$

Everything is clear now. What we do to estimate $p_X(x)$ is just counting the training examples $X_i$ which are lying within distance $h$ from $x$ and finally dividing this count by the good constant so that $\hat{p}_h$ is a probability density function.

[insert graph bandwidth]

Three problems :
<ol>
<li>We get a discontinuous density estimate.</li>
<li>The estimator gives the same attention to the data very close to $x$ than those at distance a bit less than h. We also discard all the data further.</li>
<li>How do we choose the hyperparameter $h$ ?</li>
</ol>

### Kernels

I will now define what a kernel is, and then I will explain why kernels solve both of these problems.

A kernel is a positive function $K : \mathbb{R} \rightarrow \mathbb{R}$ such that :

$$\int_{-\infty}^{\infty}K(u)du = 1$$

The kernels we will be using will play a role similar as those we encounter in real analysis. Because of this, we don't call them probability density functions, even though they share essentially the same definition.

We can rewrite our estimator in the form :

$$\hat{p}_h(x) = \frac{1}{nh}\sum_{i=1}^n K_{unit}(\frac{X_i-x}{h}),$$

where $K_{unit}(u) = \frac{1}{2}1_{1<u\leq 1}$ is the unit kernel.

We can now replace $K_{unit}$ by a smooth kernel that gives less and less importance to the training samples furthest from $x$. 

### Performance of the kernel density estimator

Now fix $x \in \mathbb{R}$. We want to assess the performance of $\hat{p}_h(x)$ as an estimate of $p(x)$. This is a problem of point estimation. A common way to measure the accuracy of an point estimator is to look at its mean squared risk :

$$\text{MSE}_p(x) = \mathbb{E}_{p}[(\hat{p}_h(x) - p(x))^2].$$

Here the expectation is taken with respect to all the possible traning examples $(X_1,\ldots,X_n) \sim p$. As usual, the MSE can be decomposed as the sum of the squared bias and the variance of $\hat{p}_h(x)$ :

$$MSE_p(x) = b_p(x)^2 + \text{Var}_p(\hat{p}_h(x)),$$

where $b_p(x) = \mathbb{E}_p(\hat{p}_h(x)) - p(x)$ and $\text{Var}_p(\hat{p}_h(x)) = \mathbb{E}_p[\hat{p}_h(x)^2] - \mathbb{E}_p[\hat{p}_h(x)]^2$.

We will now give upper bounds on these two terms.

**Upper bound on the variance**: $\hat{p}_h(x)$ is a sum of functions of i.i.d. random variables, so:

$$\text{Var}_p(\hat{p}_h(x)) = \frac{1}{nh^2}\text{Var}_p[K(\frac{X_1 - x}{h})].$$

König-Huygens formula gives us an upper bound for this variance :

$$\text{Var}_p(\hat{p}_h(x)) \leq \frac{1}{nh^2}\mathbb{E}_p[K^2(\frac{X_1 - x}{h})].$$

But :

$$\mathbb{E}_p[K^2(\frac{X_1 - x}{h})] = \int_{-\infty}^{+\infty}K^2(\frac{u - x}{h})p(u)du = h\int_{-\infty}^{+\infty}K^2(u)p(x+uh)du.$$

Finally, we see that :

$$\text{Var}_p(\hat{p}_h(x)) \leq \frac{C_1}{nh},$$

where $$C_1 = \|p\|_\infty\int_{-\infty}^{+\infty}K^2(u)du$$.

We see that when $h$ is large, then we have small variance. This is no surprise because then a lot of training examples will be taken into account when making inference about $p(x)$, so having a large bandwidth $h$ adds stability.

**Upper bound on the bias:** Using the property $\int_{-\infty}^{+\infty}K(u)du = 1$ we get:

$$b_p(x) = \int_{-\infty}^{+\infty}K(u)[p(x + uh) - p(x)]du.$$

This expression of $b_p(x)$ allows us to see the role of the regularity of $p$. It is natural to think that the most regular $p$ is, the better our estimate will be,  because when making inference about $p(x)$ we only have access to the nearest training example $(X_1,\ldots,X_n)$ which were generated using the amplitude of $p$ near them, so we do not want $p$ to be too wiggly around $x$.

Let's assume that $p$ is $L$-Lipschitz, where $L$ is a positive real number. This means that $\|p(u) - p(v)\| \leq L\|u-v\|$ for all $(u,v) \in \mathbb{R}^2$. Then:

$$|b_p(x)| \leq Lh\int_{-\infty}^{+\infty} K(u)|u|du,$$

so:

$$b_p(x)^2 \leq C_2^2h^2,$$

where $C_2 = L\int_{-\infty}^{+\infty}K(u)\|u\|du$.

We see that we are guaranteed to have a small bias if $h$ is small. This is natural because the training samples $X_i$ which give the most information about $p(x)$ are those close to $x$.

Finally we get that the mean squared error is upper bounded by the sum of two terms :

$$\text{MSE}_p(x) \leq \frac{C_1}{nh} + C_2^2h^2.$$

### The role of $h$ and the bias-variance tradeoff

We can plot the different terms of the $\text{MSE}$ as a function of $h$.

![MSE]({{ site.baseurl }}/images/MSE.png)

We see that the choice of $h$ is crucial. You do not want $h$ to be too big, otherwise you would give too much attention to training examples that are far from $x$. You do not want $h$ to be too small neither, otherwise the estimator would not be stable under small changes in the training data.

By taking the derivative of the upper bound with respect to $h$, we see that the optimal $h$ is:

$$h^{*} = (\frac{C_1}{2C_2^2})^{\frac{1}{3}}\frac{1}{n^3}.$$

Of course, $h^*$ depends directly on $p$ which is unknown.
