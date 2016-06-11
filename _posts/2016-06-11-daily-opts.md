---
layout: post
title: "Daily Options"
categories: "power volatility"
date: "2016-06-11 00:17"
published: true
---

## Summary

When computing a monthly strip of daily options it is never a good idea to
approximate it with a daily option expiring in in the middle of the month 
(15'th day) as errors in value or implied volatility can be as high as 4%. 
Instead, it is better to take the daily option that expires after 13.9 days 
within a month (this halves the errors of the previous method). An even better 
approach would be to take option that expires depending on how far is the 
delivery month T - with this method the errors are reduced by a factor of 10 
(0.4%). However this approximation does not work well for deep out of the money 
options.

## Introduction

Daily options are popular contracts in power and natural gas markets. 
They are European options where underlyings are daily forward contracts for 
the next day after the exercise. In power markets the forward contract can be
peak, off-peak or base load, however in the US only the peak load options are 
usually liquid. Usually these options are sold in strips (monthly, quarterly, 
annual).When quoting such a strip the price is given as an average option
premium per commodity unit (MWh or MMBtu).

Traders judge if an option is too expensive or too cheap by considering its 
implied volatility, which is computed using the
[Black model](https://en.wikipedia.org/wiki/Black_model) with a given option 
premium and price of the underlying. To find the implied volatility, one has to 
solve the following equation for $$\sigma $$:

$$ v_{opt}=B\left(T,F,\sigma\right) $$

where $$v_{opt}$$ - option premium, 
_B_ - [Black model](https://en.wikipedia.org/wiki/Black_model), 
_F_ - price of underlying forward. Similarly, in the case of a monthly strip of 
daily options one has to solve
 
$$
\begin{equation}
  \bar{v}_{opt}=\frac{1}{N}\sum_{i=0}^{N-1}B\left(T + i\Delta t,F,\sigma_i\right)
  \label{eq:strip}
\end{equation}
$$

where $$\bar{v}_{opt}$$ - average premium of options in the strip, 
_N_ - number of options in the strip, _T_ - time to the start of the strip 
(start of the month), $$\Delta t$$ - length of one day measured in years,
$$\sigma_i$$ - daily volatility for the _i_-th option in the strip.
 
In practice, to speed up calculations, instead of formula (\ref{eq:strip}) one
uses 

$$
\begin{equation}
  \bar{v}_{opt}\thickapprox B\left(T + n\Delta t,F,\sigma_n\right)
  \label{eq:simplified}
\end{equation}  
$$

where _n_ - some (fixed) day within a strip (say 15'th day if monthly strip is 
considered). This approximation speeds up calculations quite significantly 
(factor of 30 for a monthly strip). In this blog post we want to investigate 
what the best value for _n_ is and what the error introduced by this 
approximation is.

## "Blended" Volatility

Before investigating the approximation described in the
Introduction we need to specify how $$\bar{\sigma}_i$$ are computed. Forward 
price dynamics  exhibit a term structure of volatility - the farther we are from 
the delivery period, the lower the volatility ("Samuelson effect"). Therefore 
the volatility of daily prices should be the highest within the delivery month.

Traders in power markets think about price volatility of daily options in terms 
of two periods - before the month starts and inside the month. The volatility 
before the month is described by a monthly option (forward volatility), the 
market of which is usually more liquid than for daily options. The volatility 
inside the month is called spot volatility and describes how daily prices behave 
once we are inside the month. Forward volatilities are usually much lower than 
spot volatilities. The daily volatilities are then computed by a process that is 
called "blending":

$$
\begin{equation}
  \bar{\sigma}_{d}=\sqrt{\frac{\sigma_{F}^{2}T+\sigma_{S}^{2}d\Delta t}{T+d\Delta t}}
  \label{eq:blending}
\end{equation}
$$

where $$\bar{\sigma}_{d}$$ - daily volatility for day _d_, 
$$\sigma_{F}$$ - volatility of forward contract that expires at time _T_,
$$\sigma_{S}$$ - spot volatility, $$\Delta t$$ - time period spanning one day. 
Traders usually assume that the spot volatility is constant throughout the month.

## Examples

For our investigation we will make the following assumptions:

* $$\sigma_F$$ is constant and equals 30%
* $$\sigma_S$$ is constant and equals 80%
* $$\Delta t$$ is one day expressed in years (i.e. 1/365.25)
* a month has 30 days
* we set the interest rate to 0
* we set the forward price of commodity to $50 (per unit)

We are interested in how the approximation (\ref{eq:simplified}) behaves
for strikes of different moneyness and different times to the delivery month 
_T_. For each case we will present three graphs:

1. First we are going to compute at which value of _n_ approximation 
(\ref{eq:simplified}) yields equality (i.e. at what value of _n_ the option's 
premium equals the average premium of the strip of daily options.)
2. Then we compute by how much the approximation is different from the
exact average premium of daily options for a given _n_.
3. Finally we compute the spot volatility implied by the approximation and 
compare it to the actual spot volatility $$\sigma_S$$.

We will consider only call options (put option results should be similar).

### Fixed Moneyness

We consider three cases: at the money $$K = F$$, out of the money
$$K = 1.2\cdot F$$, and in the money $$K = 0.8\cdot F$$.

![n moneyness](/images/daily-options/n-moneyness.png)
{: style="text-align: center"}

We can see that when we are close to the delivery month (_T_ is small) the
optimal value of _n_ decreases. As time to delivery increases _n_ converges to 14.5.

#### _n_ = 14.5

If we use the value of _n_ to which it converges when _T_ is large, the
approximation (\ref{eq:simplified}) produces the following errors for value and 
implied volatility:

![v moneyness 14.5](/images/daily-options/v-moneyness-15.png)\\
![vol moneyness 14.5](/images/daily-options/vol-moneyness-15.png)
{: style="text-align: center"}

Note that if _T_ is less than about half a year the premium and volatility
errors become big and reach almost 4% at _T_ = 2 months.

#### _n_ = 13.9

We can reduce errors observed in the previous section if we pick a smaller value 
of _n_ = 13.9. 

![v moneyness 13.9](/images/daily-options/v-moneyness-14.png)\\
![vol moneyness 13.9](/images/daily-options/vol-moneyness-14.png)
{: style="text-align: center"}

Now both premium and volatility relative errors are within 3% for the full
range.

#### Variable _n_

We can get a better result if we vary _n_ with _T_. For this example we set
_n_ to be equal to the average of _ATM_ and _OTM_ exact values of _n_:

![variable n](/images/daily-options/n-var-moneyness.png)
{: style="text-align: center"}

With this _n_ we get the following results:

![v moneyness var](/images/daily-options/v-moneyness-var.png)\\
![vol moneyness var](/images/daily-options/vol-moneyness-var.png)
{: style="text-align: center"}

Note that at _T_ > 1 the errors are essentially 0 and they do not increase 
beyond 2% for small _T_.

### Fixed Reduced Delta

The results in the previous section show how the error becomes very large
when _T_ is small. One reason this happens is that when moneyness is fixed,
delta becomes very small as _T_ goes to zero. The intuition here is that
the underlying distribution width is described by variance $$\sigma^2 T$$.
When the variance is large even large strikes will fall in a very probable 
region of the distribution. On the other hand when _T_ is small the variance is
small, and even a modest strike can be in a very improbable region of the 
distribution.

One way to describe moneyness while taking into account the above fact is to
use "reduced delta" which is defined as:

$$
  D=N\left(\frac{\log\frac{F}{K}}{\sigma\sqrt{T}}\right)
$$

To ensure that this "reduced delta" is constant for all _T_ we need to adjust
_K_ as follows:

$$
  K=Fe^{-d\sqrt{T}}
$$

where _d_ is some constant. We select this constant to ensure that $$K/F = 1.2$$
at 6 months for the OTM case and $$F/K = 1.2$$ at 6 months for the ITM case.

![n delta](/images/daily-options/n-delta.png)
{: style="text-align: center"}

The general shapes of the _n_ curves are similar to the fixed moneyness case, 
but here ATM and OTM curves are identical.

The error results when _n_ is fixed are similar to the fixed moneyness case
(the errors are somewhat smaller, but not significantly), so we will not show them
here. However when we vary _n_ with _T_ (_n_ = average of _ATM_ and _OTM_ exact
values of _n_) we get a much better result:

![v delta var](/images/daily-options/v-delta-var.png)\\
![vol delta var](/images/daily-options/vol-delta-var.png)
{: style="text-align: center"}

Note how the relative error is now within 0.4% for the full range of _T_.

### Case: Spot and Forward Volatilities are Close

In previous sections we considered the case when the spot volatility is much
larger than the forward volatility (80% vs 30%). But what happens when they
are close to each other? (In reality it almost never happens due to the
Samuelson Effect.) We ran the above analysis for the case when the spot 
volatility is just slightly larger than the forward volatility (31% spot vs 30% 
forward).

![n small spot vol](/images/daily-options/n-spot.png)
{: style="text-align: center"}

Note that the shape of the _n_ curves is similar to the large spot volatility 
case. However all moneyness cases are now collapsed into a single curve.

Using _n_ = 14.5, to which it converges for large _T_, we get the following
errors for premium and implied volatility:

![v small spot](/images/daily-options/v-spot.png)\\
![vol small spot](/images/daily-options/vol-spot.png)
{: style="text-align: center"}

The errors are very small (within 0.2% for the full range, for both premium and
volatility). If we use 13.9 or variable values for _n_ the errors become even 
smaller.

## Conclusion

Approximating a strip of dailies with the value of a single daily option is an
efficient technique for significantly increasing the speed of calculation. 
However, the time of expiration of the single option needs to be selected
carefully. The usual selection with expiration in the middle of the month is 
never optimal and can produce up to 4% errors for the premium or implied 
volatity. It is much better to use an option that expires 13.9 days from the 
beginning of the month, however the errors in this case are still significant - 
2%. The best method is to use an expiration date that changes depending on the 
time to delivery month _T_. With this method the errors fall below 0.4%.

However this approach does not work well for deep out of the money options.

You can download the [Mathematica notebook](/downloads/daily-option.nb) that
was used to obtain the results for this blog post. With this notebook you can
replicate our calculations or play with a different set of parameters.

Feel free to leave questions or comments below.
