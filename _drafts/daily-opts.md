---
layout: post
title: "Daily Options"
categories: "power volatility"
---

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
for different moneyness of options and different time to delivery month _T_.
For each case we will present three graphs:

1. First we going to compute at which _n_ (\ref{eq:simplified}) becomes an equality 
(i.e. at what value of _n_ the option's premium equals to average premium of
the strip of daily options.
2. Then we compute for a given _n_ how much approximation is different from
exact average premium of daily options.
3. Finally we compute spot volatility implied by approximation and compare it
to actual spot volatility $$\sigma_S$$.

What if $$\sigma_F = \sigma_S$$ ?

### At The Money Case

![ATM n](/images/daily-options/atm-n.png)

We can see that when we close to the delivery month the optimal value of _n_
decreases. As time to delivery increases _n_ converges to 14.5.

#### _n_ = 14.5

If we use value of _n_ to which it converges at long term the approximation
(\ref{eq:simplified}) produces the following errors in value and implied
volatility:

![ATM v 14.5](/images/daily-options/atm-v-15.png)

Note that if _T_ is less than about half a year the premium error becomes big 
and reaches almost 4% at _T_ = 2 months.

![ATM vol 14.5](/images/daily-options/atm-vol-15.png)

Implied volatility error in magnitude is similar to premium error for short
periods _T_ but does not converge as quickly to zero as _T_ increases.

#### _n_ = 13.7

We can reduce errors observed in previous section if we pick smaller value of
_n_ to 13.7. 

![ATM v 13.7](/images/daily-options/atm-v-14.png)

![ATM vol 13.7](/images/daily-options/atm-vol-14.png)

Now both premium and volatility relative errors are within 2% for the full
range.

### Out Of The Money Case

![OTM n](/images/daily-options/otm-n.png)

It shows behavior similar to ATM (at the money) case.

#### _n_ = 14.5

![OTM v 14.5](/images/daily-options/otm-v-15.png)

The premium error at small _T_ is even bigger than in ATM case and reaches 6% at
_T_ = 2 months.

![OTM vol 14.5](/images/daily-options/otm-vol-15.png)

Implied volatility error in magnitude is similar to premium error for short
periods _T_ but does not converge as quickly to zero as _T_ increases.

#### _n_ = 13.7

We can reduce errors observed in previous section if we pick smaller value of
_n_ to 13.7. 

![ATM v 13.7](/images/daily-options/atm-v-14.png)

![ATM vol 13.7](/images/daily-options/atm-vol-14.png)

Now both premium and volatility relative errors are within 2% for the full
range.




## Conclusion
