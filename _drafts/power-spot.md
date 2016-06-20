---
layout: post
title: "Spot Price Dynamics in Power Markets"
categories: "power spot"
---

## Summary
The modelling of spot (daily) prices in commodities usually starts with 
modelling monthly average (forward) prices and then selecting some simple
model to describe daily prices within the month relative to the monthly average.
The most common model is a simple geometric brownian motion with constant (spot)
volatility. In this blog post we show that this approach results in wrong
interdependencies (autocorrelation) in spot prices. 

We tested two other popular approaches - treating daily prices as independent 
random variables (no autocorrelation) and assuming that the spot prices can 
be described as a mean-reverting process. We show that we should use the
mean reverting process to realistically describe interdependencies of daily
prices.  

## Introduction

The standard approach to modeling daily (spot) prices in commodity markets is to 
first model dynamics of monthly average (forward) and then generate daily prices 
as offset from this average. Usually monthly forward contracts and their options 
have highest market liquidity so their dynamics can be deduced quite reliably. 
Since forward price equals to expected average daily price the expectation of 
daily offset should be zero. There are two main approaches to model daily 
prices:

1. GBM (Geometric Brownian Motion) starting at the level of forward prices.

2. Independent random log-normal variables with mean equal to forward prices.

These approaches ignore spikes, which are usually accounted by skew in volatility 
constructions. The main difference between these two approaches is the fact that 
in the first case the auto-correlation of daily prices is very high (since next 
step completely defined by previous). In the second approach the 
auto-correlation is very low, since every day price is completely independent of 
previous day prices. This leads to the question we addressing with this post: 
Which model better describes the dynamics of daily prices? We will consider case 
of power prices and will look into data from ERCOT.

## Daily Dynamics in ERCOT

For the analysis we used an hourly day-ahead LMP prices from a node in the North 
Zone of ERCOT. We computed average of hourly prices over the peak hours (the 
hour ending 7am through the hour ending 10pm) and skipped weekends and NERC 
holidays. We also computed monthly average of these prices:

![power prices](/images/power-spot/prices.png)
{: style="text-align: center"}

To find which model best describes the behavior of daily prices we ran the 
auto-correlation test (
[`AutocorrelationTest`](https://reference.wolfram.com/language/ref/AutocorrelationTest.html)
in Mathematica) on daily prices within each month. Then we found frequency with 
which this test declared prices to be autocorrelated. For the peak ERCOT prices 
it was 33%. It is much bigger number than 5% we would get if prices were 
independent (we ran autocorrelation test with 5% significance level) and it is 
much smaller than 95% we would get if prices evolve as GBM.

With this analysis we conclude that neither GBM neither independent variables can 
realistically describe inderdependence of daily prices. In the next section we
will discuss a model that produces more realistic results.

## Mean Reverting Daily Prices

Many studies used mean reverting process to describe dynamics of spot 
electricity prices. With discrete time steps the mean reverting process becomes 
AR(1) process with auto-regression coefficient related to the mean reversion:

$$
\begin{align*}
d\log p_{t} & =\theta\left(\mu-\log p_{t}\right)dt+\sigma dW_{t}\\
\log p_{d+1}-\log p_{d} & =\theta\mu \Delta t-\theta \Delta t\log p_{d}+\sigma\sqrt{\Delta t}\epsilon_{d}\\
\log p_{d+1} & =\left(1-\theta \Delta t\right)\log p_{d}+\theta\mu \Delta t+\sigma\sqrt{\Delta t}\epsilon_{d}
\end{align*}
$$

We ran the numerical experiment that showed that if we set auto-regression 
coefficient $$\theta \Delta t$$ to 0.33 the frequency with which the 
autocorrelation test declared prices autocorrelated was about 33%, i.e. matched 
the result for ERCOT daily prices. This suggests that we should use AR(1) 
process to model daily prices.

## Conclusion

The analysis in this blog post shows that two basic models for describing
daily prices relative to average monthly have very different autocorrelation
properties - geometric brownian motion produces price sequences that are
perfectly autocorrelated, while treating prices as independent random variables
make price sequences that are not autocorrelated. Usually the actual market
prices will fall somewere in between of theses two extremes. The mean reverting
model is the simplest model to accomodate this effect.

Please download the [Mathematica notebook and data](/downloads/power-spot.zip) 
that were used to obtain the results for this blog post. With this notebook you 
can replicate our calculations or play with a different set of parameters.

Feel free to leave questions or comments below.
