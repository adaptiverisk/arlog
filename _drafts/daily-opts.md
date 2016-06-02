---
layout: post
title: "Daily Options"
categories: "power volatility"
---

## TLDR

When computing monthly strip of daily options it is never good idea to
approximate it with a daily option in the middle (15'th day) of the month as errors
in value or implied volatility can be as high as 4%. It is better instead to
take daily option that expires 13.9 days within a month (this halves the errors
of previous method), but even better is to take option that expires depending
on how far is the delivery month T - with this method the errors are reduced by
a factor of 10 (0.4%).

## Introduction

Daily options are popular contracts in power and natural gas markets. 
These are European options where underlyings are daily forward contracts for 
the next day after the exercise. In power markets the forward contract can be
peak, off-peak or base load, however in US only peak usually liquid. Usually 
these options are sold in strips (monthly, quarterly, annual).
When quoting such a strip the price is given as an average option premium per 
commodity unit (MWh or MMBtu).

Traders judge if an option is too expensive or too cheap by considering its 
implied volatility, which is computed using 
[Black model](https://en.wikipedia.org/wiki/Black_model) with given option 
premium and price of the underlying. To find an implied volatility one has to 
solve the following equation for $$\sigma $$:

$$ v_{opt}=B\left(T,F,\sigma\right) $$

where $$v_{opt}$$ - option premium, 
_B_ - [Black model](https://en.wikipedia.org/wiki/Black_model), 
_F_ - price of underlying forward. Similarly, in the case of (say monthly)
strip of daily options one has to solve
 
$$
\begin{equation}
  \bar{v}_{opt}=\frac{1}{N}\sum_{i=1}^{N}B\left(T_{i},F,\bar{\sigma}_i\right)
  \label{eq:strip}
\end{equation}
$$

where $$\bar{v}_{opt}$$ - average premium of options in the strip, 
_N_ - number of options in the strip, $$T_{i}$$ - expiration of each option 
in the strip, $$\bar{\sigma}$$ - average daily volatility for the options in
the strip.
 
In practice, to speed up calculations, instead of the formula 
(\ref{eq:strip}) one uses 

$$
\begin{equation}
  \bar{v}_{opt}\thickapprox B\left(T_{n},F,\bar{\sigma}_n\right)
  \label{eq:simplified}
\end{equation}  
$$

where _n_ - some (fixed) day within a strip (say 15'th day if monthly strip is 
considered). This approximation speeds up calculations quite significantly 
(factor of 30 for a monthly strip). In this blog post we want to investigate 
what is the best value of _n_ and what is the error introduced by this 
approximation.

## "Blended" Volatility

Before jumping into investigation of the approximation described in the
Introduction we need to specify how $$\bar{\sigma}_i$$ are computed. Forward 
price dynamics  exhibit term structure of volatility - the farther we are from 
delivery period, the lower the volatility ("Samuelson effect"). Therefore the 
volatility of daily prices inside the delivery month should be highest. 

Traders in power markets think about price volatility of daily options in terms 
of two periods - before month starts and inside the month. The volatility before 
the month is described by monthly option, market of which is usually more liquid 
than for daily options. The volatility inside the month is called spot 
volatility and describes how daily prices behave once we are inside the month. 
Forward volatilities are usually much lower than spot volatilities. 
The daily volatilities are then computed by a process that is called "blending":

$$
\begin{equation}
  \bar{\sigma}_{d}=\sqrt{\frac{\sigma_{F}^{2}T+\sigma_{S}^{2}d\Delta t}{T+d\Delta t}}
  \label{eq:blending}
\end{equation}
$$

where $$\bar{\sigma}_{d}$$ - is a daily volatility for day _d_, 
$$\sigma_{F}$$ - volatility of forward contract that expires at time _T_,
$$\sigma_{S}$$ - spot volatility, $$\Delta t$$ - time period spanning one day. 
Traders usually assume that the spot volatility is constant throughout the month.

## Examples

For our investigation we will make the following assumptions:

* $$\sigma_F$$ is constant and equals 30%
* $$\sigma_S$$ is constant and equals 80%
* $$\Delta t$$ is one day expressed in years (i.e. 1/365.25)
* month has 30 days
* we set interest rate to 0
* we set forward price of commodity to $50 (per unit)

We are interested in how the approximation (\ref{eq:simplified}) behaves
for strikes of different moneyness and different time to delivery month _T_.
For each case we will present three graphs:

1. First we going to compute at which _n_ (\ref{eq:simplified}) becomes an 
equality (i.e. at what value of _n_ the option's premium equals to average 
premium of the strip of daily options.
2. Then we compute for a given _n_ how much approximation is different from
exact average premium of daily options.
3. Finally we compute spot volatility implied by approximation and compare it
to actual spot volatility $$\sigma_S$$.

We will consider only call options (put option results should be similar).

### Fixed Moneyness

We consider three cases: at the money $$K = F$$, out of the money
$$K = 1.2\cdot F$$, and in the money $$K = 0.8\cdot F$$.

![n moneyness](/images/daily-options/n-moneyness.png)
{: style="text-align: center"}

We can see that when we close to the delivery month (_T_ is small) the optimal 
value of _n_ decreases. As time to delivery increases _n_ converges to 14.5.

#### _n_ = 14.5

If we use value of _n_ to which it converges at large _T_ the approximation
(\ref{eq:simplified}) produces the following errors in value and implied
volatility:

![v moneyness 14.5](/images/daily-options/v-moneyness-15.png)\\
![vol moneyness 14.5](/images/daily-options/vol-moneyness-15.png)
{: style="text-align: center"}

Note that if _T_ is less than about half a year the premium and volatility
error becomes big and reaches almost 4% at _T_ = 2 months.

#### _n_ = 13.9

We can reduce errors observed in previous section if we pick smaller value of
_n_ = 13.9. 

![v moneyness 13.9](/images/daily-options/v-moneyness-14.png)\\
![vol moneyness 13.9](/images/daily-options/vol-moneyness-14.png)
{: style="text-align: center"}

Now both premium and volatility relative errors are within 3% for the full
range.

#### Variable _n_

We can get better result if we vary _n_ with _T_. For this example we set
_n_ to average of _ATM_ and _OTM_ exact values of _n_:

![variable n](/images/daily-options/n-var-moneyness.png)
{: style="text-align: center"}

With this _n_ we get the following results:

![v moneyness var](/images/daily-options/v-moneyness-var.png)\\
![vol moneyness var](/images/daily-options/vol-moneyness-var.png)
{: style="text-align: center"}

Note that at _T_ > 1 the errors are essentially 0 and they do not get
higher than 2% for small _T_.

### Fixed Reduced Delta

The results in previous section show how the error becomes very large
when _T_ is small. One reason this happens is that when moneyness is fixed
delta becomes extreme as _T_ goes to zero. The intuition here is that
the underlying distribution width is described by variance $$\sigma^2 T$$.
When variance is large even large strikes will fall in very probable region of 
the distribution. On the other hand when _T_ is small the variance is small,
and even modest strike can be in very improbable region of the distribution.

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
at 6 months for OTM case and $$F/K = 1.2$$ at 6 months for ITM case.

![n delta](/images/daily-options/n-delta.png)
{: style="text-align: center"}

The general shapes of _n_ curves is similar to fixed moneyness case, but
here ATM and OTM curves are identical.

The error results when _n_ is fixed is similar to the fixed moneyness case
(the errors somewhat smaller, but not significantly), so we will not show them
here. However when we vary _n_ with _T_ as average of _ATM_ and _OTM_ exact
values of _n_ we get much better result:

![v delta var](/images/daily-options/v-delta-var.png)\\
![vol delta var](/images/daily-options/vol-delta-var.png)
{: style="text-align: center"}

Not how the relative error is now within 0.4% for the full range of _T_.

### Case: Spot and Forward Volatilities are Close

In previous sections we considered case when spot volatility is much
larger than forward volatility (80% vs 30%). But what happens when they
are close to each other (in reality it is almost never happen due to
Samuelson effect). We ran the above analysis for the case when spot volatility
just slightly larger than forward (31%).

![n small spot vol](/images/daily-options/n-spot.png)
{: style="text-align: center"}

Note that function _n_ of _T_ to render the approximation (\ref{eq:simplified})
exact has similar shape as in large spot volatility case. However all moneyness
cases now collapsed into single curve.

Using _n_ = 14.5 to which it converges for large _T_ we get the following
errors for premium and implied volatility:

![v small spot](/images/daily-options/v-spot.png)\\
![vol small spot](/images/daily-options/vol-spot.png)
{: style="text-align: center"}

The errors are very small (within 0.2% for full range, for both premium and
volatility). If we use other values for _n_ the errors become even smaller.

## Conclusion

Approximating strip of dailies by a value of a single daily option is an
efficient technique for significantly increasing speed of calculation. However
the time of expiration of the single option needs to be selected carefully.
The usual selection in the middle of the month is never optimal and can
produce 4% errors in premium or implied volatity. It is much better to
use option that expires 13.9 days from beginning of the month, however the
errors in this case are still significant - 2%. The best method is to use
expiration that changes depending on time to delivery month _T_. With this
method the errors fall down to 0.4%.

You can download the [Mathematica notebook](/downloads/daily-option.nb) that
was used to obtain results for this blog post. With this notebook you can
replicate our calculations or play with different set of parameters.
