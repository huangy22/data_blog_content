title: Understanding the price elasticity of demand from an item response model point of view
tags:
	- price elasticity
	- item response model
categories: economics
date: 2018-08-12
---

> Price elasticity of demand (PED or Ed) is a measure used in economics to show the responsiveness, or elasticity, of the quantity demanded of a good or service to a change in its price when nothing but the price changes. More precisely, it gives the percentage change in quantity demanded in response to a one percent change in price.
> 
> --[wikipedia](https://en.wikipedia.org/wiki/Price_elasticity_of_demand)

<!-- more -->

Suppose we have $N$ products, each with price $P_n$, and send them out to $M$ customers. At each event $u$($u$ means box unit), when customer $m$ receives product $n$, the probability of purchase is captured by the following item-response-type model with price as an additional feature:
$$\text{prob}_u =  \text{prob} _{m,n} =\sigma (c(v _n + \delta v _m - P _n))$$

Here, $\sigma$ is the sigmoid function, $P_n$ is the price of the product, $v_n$ represents the "value" of the product, $\delta v _m$ represents the customer's budget preference, meaning how much more or less she's willing to pay than average. $v_n$ and $\delta v _m$ both have the same unit with price. The coefficient $c$, with the unit of inverse price, characterizes how strongly the probability depends on the price. Note that $c P$ is a dimensionless number.

(In the real calculation, in order to have dimensionless parameters, we can reformulate the model as $\sigma (b + \alpha _m + \beta _n - c \tilde{P} _n)$, where $\tilde{P} _n = P _n / \langle P_n \rangle$.)

In this document, we are going to derive the price elasticity from this model and try to understand the relationship between the elasticity and other properties of the product such as the price and average conversion rate.

The price elasticity of demand for a product is defined as the percentage change in quantity demanded in response to one percent change in its price.
$$ E _n = \frac{dQ _n/Q _n} {d P _n / P _n}$$

**Let us make an assumption that we are always going to send the product to the same group of customers when changing its price**, then the relative change in demand can be calculated as the relative change in the average purchase probability:
$$ \frac{d Q _n }{ Q_n} = \frac{d\sum_{\{m_n\}} \text{prob}_{m,n}}{\sum_{\{m_n\}} \text{prob}_{m,n}}$$
where $\{m _n\}$ represents the customers that product $n$ has been sent to.

Therefore we have 
$$ E_n = \frac{d \sum_{\{m_n\}} \sigma(c(v_n + \delta v_m - P_n))}{dP_n} \cdot \frac{P_n}{\sum_{\{m_n\}} \sigma(c(v_n + \delta v_m - P_n))}$$

Define $f(n, m) = c ( v _n + \delta v _m - P _n)$, then we have
$$ \frac{d \sum_{\{m_n\}} \sigma(f_{m,n})} {d P_n} = \sum_{\{m_n\}} \sigma(f_{m,n}) (1-\sigma(f_{m,n})) \cdot \frac{df_{m,n}}{dP_n} = -c \sum_{\{m_n\}} \sigma(f_{m,n}) (1-\sigma(f_{m,n}))$$

$$\Rightarrow E_n =  -P_n \cdot \frac{c \cdot \sum_{\{m_n\}} \sigma(f_{m,n}) (1-\sigma(f_{m,n}))}{\sum_{\{m_n\}} \sigma(f_{m,n})} = -P_n \cdot \frac{ c \cdot \langle \sigma(f_{m,n}) (1-\sigma(f_{m,n}))\rangle_{m_n}}{\langle \sigma(f_{m,n})\rangle_{m_n}}$$

In physics, the sigmoid function (Fermi-Dirac distribution) $\sigma(x) = \frac{1}{1+ e^{-cx}}$ defines the occupation of fermions over possible energy states and the derivative of the Fermi distribution gives the Fermi surface. In the limit of $c \to \infty$ (equivalent to zero-temperature limit), the Fermi distribution becomes a step function $sgn(x)$, and its derivative becomes a delta function $\delta(x)$. As $c$ decreases (finite temperature), the step function softens and the derivative becomes a finite range distribution around $x=0$. [see this figure](https://www.researchgate.net/figure/Graph-of-the-Logistic-function-and-its-derivative-function_fig1_268874045)

Now let us try to get some intuition about the asymptotic behavior of $E_n$ in the zero-temperature limit ($c \to \infty$). Here we need to make another assumption: **the customer budget offset term $\delta v_m$ has a normal distribution with zero-mean and standard deviation $\sigma_C$: $\delta v_m \sim \mathcal{N}(0, \sigma_C^2)$.** 

In the limit of infinite $c$, the numerator of the equation now becomes:
$$
\begin{aligned}
& \lim _{c\to\infty}   c \cdot \langle \sigma(f _{m,n}) (1-\sigma(f _{m,n}))\rangle _{m _n} \\
&= \langle \delta (f _{m,n} ) \rangle _{m _n}  \\
&= \langle \delta (v _n + \delta v _m - P _n) \rangle _{m _n} \\
&\approx \int_{-\infty}^{+\infty} dx \frac{1}{\sqrt{2\pi} \sigma_C }e^{-x^2/2\sigma_C^2} \delta(x+v_n -P_n) \\
&= \frac{1}{\sqrt{2\pi} \sigma_C }e^{-(P_n-v_n)^2/2\sigma_C^2}
\end{aligned}
$$
which is a normal function with mean $P_n-v_n$ and standard deviation $\sigma_C$. When $c$ is a finite number, the infinitely sharp delta function becomes flatter, my intuition would be that this average will also have a wider tail than normal function. **If we assume that our current pricing strategy roughly captures the average value for the product, in other words that the difference between $P_n$ and $v_n$ does not deviate too far from zero and from each other**, the dependence of this function on $P_n$ should not be too strong.

The denominator of the equation represents the average conversion probability of the product. If we also use the assumption that $\delta v_m \sim \mathcal{N}(0, \sigma_C^2)$, then
$$
\begin{aligned}
& \lim_{c\to\infty} \langle \sigma(f_{m,n}) \rangle_{m_n}\\
&= \langle \text{sgn}(v_n + \delta v_m -P_n) \rangle_{m_n} \\
&\approx \int_{-\infty}^{+\infty} dx \frac{1}{\sqrt{2\pi} \sigma_C }e^{-x^2/2\sigma_C^2} \text{sgn}(x + v_n -P_n)\\
&= \int_{P_n-v_n}^{+\infty} dx \frac{1}{\sqrt{2\pi} \sigma_C }e^{-x^2/2\sigma_C^2} \\
&= \Phi(\frac{v_n-P_n}{\sigma_C})
\end{aligned}  
$$
where $\Phi$ is the cumulative distribution function for standard normal distribution. The [shape of this function](https://en.wikipedia.org/wiki/Normal_distribution#/media/File:Normal_Distribution_CDF.svg) is very similar to a sigmoid function. When $c$ is finite, this function is also likely to become even wider. **Again, suppose we are setting the price of our products to reflect the conversion rate, in other words, the price is set high for higher value products and vise versa**, then the average conversion probability should also not strongly correlate with price.

Therefore, based on all the assumptions, we shall see a roughly linear relationship between $E_n$ and $P_n$. If we see some products strongly deviate from this linear behavior, that might indicate that there's a large difference between the current price and its value learned from the model. This might indicates that our price strategy about that product should be better fine-tuned.

*Note: The dimension of elasticity*

$$E_n = -P_n \cdot \frac{ c \cdot \langle \sigma(f_{m,n}) (1-\sigma(f_{m,n}))\rangle_{m_n}}{\langle \sigma(f_{m,n})\rangle_{m_n}}$$

In the above equation, the sigmoid function and the derivative $\sigma (1-\sigma)$ are dimensionless, and $c \cdot P_n$ is dimensionless, therefore the elasticity is also dimensionless.

In the $c\to\infty$ limit, it's a little less obvious. Recall that the value $v_n$ and offset $\delta v_m$ both have the same dimension with the price $P_n$, therefore the standard deviation of $\delta v_m$, which is $\sigma_C$, also has the dimension of price. In the $c\to\infty$ limit, 
$$E_n = -P_n \cdot \frac{1}{\sqrt{2\pi} \sigma_C} e^{-(P_n-v_n)^2/2\sigma_C^2} \frac{1}{\Phi(\frac{v_n - P_n}{\sigma_C})}$$
the unit in $P_n$ and $\sigma_C$ cancel with each other, leaving the elasticity dimensionless.
