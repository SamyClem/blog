# Density estimation

Let $(X_1,\ldots,X_n)$ be identically distributed real valued random variables whose common distribution has density $p_X$ with respect to the Lebesgue measure, i.e. $\mathbb{P}(X \in A) = \int_{A}p_X(x)dx$ for all $A \subset \mathbb{R}$. Our goal is to estimate $p_X$ from the data $(X_1,\ldots,X_n)$. 

### The parametric way

One classic way to do so is to assume that $p_X$ belongs to a parametric family $$\{f_{\theta} : \theta \in \Theta \}$$ where $\Theta$ is a subset of $\mathbb{R}^k$ with a fixed dimension $k$. For example, one may assume that the data $(X_1,\ldots,X_n)$ was sampled from a normal distribution. In this case, $k=2$, $$\Theta = \{(\mu, \sigma^2) : \mu \in \mathbb{R}, \sigma > 0\}$$ and $f_{\theta}(.) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(. - \mu)^2}{2\sigma^2}}$.