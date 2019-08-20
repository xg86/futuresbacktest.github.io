---
title: Weighting
permalink: /drafts/weighting/
sitemap: false
layout: default
---

# Weighting

In this section we will cover in more detail the methods you can use to set the weights of assets and strategies in your backtests.

As explained earlier on the page presenting the portfolio construction [methodology](/docs/methodology/), the general idea is first to compute an aggregated estimate of expected returns for each asset (which we call "indicator"), and then a weight for each asset based on this indicator and volatility and correlation measures, if applicable.

The aggregated indicator of expected returns is computed for each asset as the sum of the product of each individual indicator value and some weights. The individual indicators are computed based on the strategies you select among "long", "short", "trend following", "carry" and "value".

For now, we let you set the weights by hand for each individual strategy. You can set different weights for each strategy and for each asset class or even each asset. However, we recommend that you stick with the same weights at least inside an asset class, in order to limit the risk of overfitting (improved backtest results based on arbitrary choices that often end up in poor out-of-sample performance).

The weights you set for each indicator should sum up to 1 (however it is not mandatory and we will not normalize them to force the sum to be equal to 1). Please refer to the page describing the [strategies](/docs/strategies/) for more details on how individual indicators are calculated. Usually indicators are scaled between -1 and 1, but this is not mandatory.

Given an aggregated indicator of expected returns for each asset, we compute the weight of each asset relative to total portfolio value for each rebalancing date, using one of the methodologies presented below. The final position for each asset is equal to the product of this asset weight and the aggregated indicator value (usually between -1 and 1).

# Static Weights

Static weights is the most straightforward option,  where you set the weights by hand. There are two possibilities however.

By default, you need to set weights for each asset and each asset class. We compute final asset weights as the product of the asset weight and the asset class weight you set, divided by the number of assets in each asset class. The reason is that depending on data availability, the number of assets in each asset class varies over time, so doing this allows us to maintain a roughly constant leverage for each asset class over time in our simulation.

Example: You select US and Germany 10yr bonds, with a weight of 1 for each. You select a weight of 3 (300%) for the "Bonds" assets class. When data is available only for US bonds futures, computed weight for these is 3. When data is available for both US and Germany bonds futures, computed weight are 1.5 for each.

Alternatively, you can choose to set weights directly for each asset. In this case, you do not need to set weights for assets classes. However, with that option, leverage increases over time when more historical data is available. In any case, simulation starts when data is available for at least one contract in each asset class; as a consequence, this drawback is not present if you select only one asset per asset class.

# Inverse Volatility

Inverse volatility is the simplest, most naïve risk parity approach. This term is often used to describe methodologies that can differ in their specifics but are similar in the general approach: the idea is that all assets and/or strategies in a diversified portfolio should bring comparable contributions to the total risk of the portfolio. As a consequence, diversification should be the most effective: if correlations are not too high, the risk of the whole portfolio should be lower than the sum of the risk of all components, while expected returns add up, thus achieving better risk adjusted expected returns than the portfolio component considered separately. On the contrary, if portfolio components brought very different amounts of risk to the portfolio, the total risk of the portfolio would be most likely driven by the riskiest components and diversification would not be as effective.

In the "inverse volatility" approach, assets weights are computed as the inverse of each asset volatility. To keep it simple, we use recent realized volatility as a proxy for expected volatility, using a rolling window with exponentially decreasing weights.

This basic approach does not account for correlations. As an option, you can also choose to equalize the volatility of each asset class (considering the correlations between assets in the same asset class and the sign of the aggregated indicators).

Volatility and correlations

For all the weighting methods except static weights, we need to compute estimates of volatility and correlations at every rebalancing date. To do so:
* we compute volatility as the standard deviation of the daily log returns multiplied by the square root of 252 (average number of trading days in a year), using a rolling exponential window with a span of your choice (default to 150 trading days);
* using this daily volatility measure, we scale all returns to 10% volatility;
* we cumulate these scaled returns to obtain "virtual assets" time series with 10% volatility each;
* based on these scaled assets, we compute correlations of weekly returns (we use only weekly data because time differences between market close at different exchanges can introduce false correlations), using a rolling exponential window with a span of your choice (default to 150 weeks).

We advise that you keep long enough windows for volatility and correlations in order to improve stability of the weights computation.

More sophisticated models exist to estimate volatility ([GARCH](), to name one), but for now we decided to keep it simple and use recent realized volatility as a proxy. 

Correlations estimation, or the equivalent covariance matrix estimation, is also a heavily researched field. As most portfolio optimization approaches rely on the inverse or the eigenvalues of the covariance matrix, the sample correlation estimator is known to behave poorly in this context when the number of variables is not small relative to the sample size. Several regularization techniques (such as "shrinkage") have been introduced to tackle this issue; we may include some of them at a later point in time but for now we stick to the standard sample correlation estimator (Pearson correlation coefficient) and advise that you keep a long enough rolling window.

# Mean-Variance

The mean-variance weighting approach is inspired by Markowitz optimal portfolio theory (if need be, you can find a lot of information on this general framework on the internet, for instance on Wikipedia).

The idea is to find the assets weights that minimize expected volatility for a given level of expected returns, using some measures of expected returns, assets volatility and correlations. Here, we will use our aggregated indicator value as a measure of expected returns, and recent realized volatilities (based on daily returns) and correlations (based on weekly returns) as a measure of expected ones, to keep it simple.

The mean-variance optimization can be solved numerically; the formula for the weights is the following:

Alpha * Sigma-1 * mu 

Though theoretically appealing, the mean-variance portfolio construction is known to generate highly concentrated portfolios and seems to be not widely used by practitioners without some tweaks (for instance by forcing a certain amount of diversification). It is however still a reference which is worth including in your options.

If no assumption is made on expected returns (if you choose a 100% long strategy for each asset), mean-variance optimization is equivalent to minimum-variance optimization.

# Risk Budgeting or Equal Risk Contribution

Risk Budgeting is a generalization of the Equal Risk Contribution portfolio introduced by … in 2008. In this framework, the aim is to set the contribution of each asset to the total variance of the portfolio equal to some "risk budget".

a popular risk-parity approach;
* agnostic risk parity, a newer approach to risk parity.