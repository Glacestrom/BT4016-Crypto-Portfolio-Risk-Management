# BT4016 - Crypto Portfolio Risk Management
This GitHub repository represents a collaborative group project in which I contributed to various components.  
Specifically, my contributions encompass:
1. **Section 1:** Basic analysis and visualizations of the portfolio.
2. **Section 3:** Calculating VaR(5%) for component assets.
3. **Section 5:** Strategy 3 - Moneyness Ranking and Strategy Evaluation.

While I have included the entirety of the codebase for completeness and coherence, it is important to note that Section 5 is dependent on the preceding sections. Moreover, I wish to highlight that Section 5 involves the design of new hedging strategies that my teammates and I devised entirely on our own, without any hints or suggestions from our professor. 

The following contributions were undertaken by my teammates:
- (Zhuo Yunhui) **Section 2:** Markowitz MVO and simulated portfolio value 
- (Lo Yin-Fan) **Section 4:** Options Hedging
- (Lo Yin-Fan) **Section 5:** Strategy 1 - Extended Straddle
- (Zhuo Yunhui) **Section 5:** Strategy 2 - Options Pricing Model

This collaborative effort allows for a comprehensive examination of the project's scope and outcomes. If you have inquiries or require further details, please feel free to reach out. Thank you for your interest.

## Section 1 – Basic Analysis and Visualizations of the Portfolio
The daily prices of the 4 cryptos are retrieved from Yahoo Finance for the period of 2020-02-01 to 2022-02-01, and the daily log returns are calculated using ln(St+1/St) from Adjusted Close. The asset return correlations are plotted using a matrix heatmap, and the annualized Sharpe ratios of the four crypto assets are plotted using a bar chart. The Sharpe ratios are calculated using the assumption that the risk-free rate is zero and annualized by multiplying sqrt(365) because crypto markets, unlike stocks, are always open 365 days a year.

The matrix heatmap shows a positive correlation between all 4 crypto asset returns, with a weak correlation (ρ=0.28) between MATIC-USD and DOGE-USD and a strong correlation (ρ=0.82) between BTC-USD and ETH-USD. Notably, DOGE-USD returns exhibit the lowest correlation with the other 3 cryptos, with coefficients less than 0.5. From the bar chart, investing in MATIC-USD, which has the highest Sharpe ratio of 1.38, maximizes risk-adjusted returns for a single-crypto portfolio. Additionally, BTC-USD is a suboptimal asset as its Sharpe ratio is the lowest at 0.9 < 1, suggesting that its returns do not effectively offset its risk.

## Section 3 – Calculating the VaR(5%) for the Component Assets
We used the historical simulation approach to estimate the daily VaR and ES of the percentage return of all the individual component crypto assets and the portfolio (no limit to holding weights setting) at the α = 5% level. The daily VaR and ES were calculated using a rolling basis, with a sample period of 365 days before the focal trading day of VaR, inclusive of the day itself. Subsequently, the VaR and ES curves were plotted for one year (2021-2-1 to 2022-2-1).

From the graphs, the most risky component asset is undeniably DOGE-USD. First, the Value at Risk for DOGE-USD is consistently among the two highest assets for the entire
period. This indicates that the likelihood of experiencing losses beyond a certain level is greater compared to the other assets. Second, the Expected Shortfall for DOGE-USD is also the highest for most of the period, indicating that not only is the probability of losses beyond the Value at Risk higher, but the magnitude of the average loss is also higher. Hence, there is a higher likelihood of experiencing larger losses for DOGE-USD.

## Section 5 – Designing Our Own Hedging Strategies
### Strategy 3: Moneyness Ranking
We considered a strategy similar to Strategy 1, where we plan to buy one straddle (buy one put + buy one call) for each one unit of Bitcoin. Since our portfolio is
mostly constructed from Bitcoin, we want to buy straddles for Bitcoin every month. However, we ranked the put and call options using the option's “moneyness”, which is
calculated from the option's strike price, the current price of the underlying asset and the option premium using Equation 1 for call options and Equation 2 for put options below.
```
Moneyness Ratio = (Spot Price - Strike Price) / Call Option Premium (1)
Moneyness Ratio = (Strike Price - Spot Price) / Put Option Premium (2)
```

The moneyness ratio provides a measure of how much the option is in or out of the money, taking into account the option premium. Options that are “in the money” (i.e., the strike price is lower/higher than the current price for calls/puts) would have a moneyness of greater than 0, while options that are “out of the money” (i.e., the strike price is higher/lower than the current price for calls/puts) would have a moneyness less than 0. We can then rank the options based on their moneyness, with a higher ratio indicating that the option is more in the money and a lower ratio indicating that the option is more out of the money.

This ranking approach assumes that options with strike prices closer to the current price of the underlying asset are more likely to be profitable, as the underlying asset only needs to move a small amount in order for the option to be in-the-money. Additionally, a higher moneyness ratio indicates that the potential profit relative to the cost of the option is higher, which can be seen as a measure of the option's leverage.

Similar to Strategy 1, we want to buy straddles for Bitcoin every month, again to minimize the frequency of options trading, which can be practical in the real world due to high commission fees. Our goal is to hedge our investments, so we believe it is more appropriate to purchase new options after the previously bought options have expired.

In the code, we iterated through the first day of each month and determined which call option and put option to purchase. Since the options have varied expiration lengths but straddles must have the same expiration date, we merged the put and call data frames on the expiration date and obtained the straddle with the highest total moneyness ratio (i.e. the sum of the moneyness ratios of the put and call options). We then calculated the expected return on that day by subtracting the cost of the options and adding the return (payoff of the put and call options) to our portfolio on the expiration date.

However, it is important to consider that when buying options, we are paying out of our portfolio. Therefore, it may be prudent to hedge a percentage of our total portfolio value in order to limit our risk exposure while still providing some level of protection against market volatility. This would also allow for potentially higher gains if the market moves in our favour. To determine the percentage of assets to be hedged, we can use the VaR(5%) percentage from Section 3, which would allow us to protect our portfolio from the maximum expected loss over the last 365 days with a 95% confidence level.

By applying the same strategy of buying Bitcoin options, we can purchase Ether straddle options for each unit of Ether in our portfolio, as well as for the VaR(5%) percentage of Ether, on the first day of each month. These combined strategies are expected to generate slightly higher performance results than simply buying Bitcoin straddle options.

Comparing the two-year performance of our portfolio with and without the straddle hedging using moneyness ranking, taking into account the annualized return and Sharpe ratio, it can be seen that our portfolio performance improves with all the straddle hedging strategies using moneyness ranking. Furthermore, the combined strategies of purchasing both Bitcoin and Ether straddle options outperform the strategies of buying only Bitcoin options. However, the strategy of buying one straddle for the VaR(5%) percentage of Bitcoin and Ether seems to generate slightly worse performance results than simply buying one straddle for each unit of Bitcoin and Ether in the portfolio. Hence, the optimal hedging strategy is to buy one straddle for each unit of Bitcoin and Ether in the portfolio, which would generate annualized returns of 2.90 and an annualized Sharpe ratio of 1.70 for the period of 2020-02-01 to 2022-02-01. One possible explanation could be that the VaR only measures the percentage of losses, but not the size of potential extreme losses in the worst 5% of scenarios. By only hedging a portion of the portfolio with the VaR(5%) percentage, there may still be
significant risk exposure left unaccounted for, leading to worse performance results.

Alternatively, it could be that the VaR(5%) percentage is based on historical data and assumes that future market conditions will be similar to the past. However, if the market experiences unforeseen events or conditions, the VaR calculation may not accurately reflect the level of risk in the portfolio. Therefore, hedging the VaR(5%) percentage may not fully protect the portfolio against unexpected market movements. On the other hand, buying one straddle for each unit of Bitcoin and Ether in the portfolio provides a more direct hedge against market volatility, as the options are specifically tailored to the amount of assets in the portfolio. This may provide better protection against unexpected market movements, as the options are based on the current market value of the assets rather than historical data.

### Strategy Evaluation and Final Recommendation
Finally, the following are the results comparing the performance of our unlimited portfolio in Section 2, with hedging using the extended straddle in Section 5.1, and with hedging using moneyness ranking in Section 5.3, taking into account the annualized return and Sharpe ratio.

While both hedging strategies outperform the unlimited portfolio in Section 2, it can be seen that the optimal hedging strategy is to buy one straddle for each one unit of Bitcoin and Ether in the portfolio, which would generate the highest annualized returns of 2.90 and the highest annualized Sharpe ratio of 1.70 for the period of 2020-02-01 to 2022-02-01. The portfolio returns are also plotted for the unlimited portfolio in Section 2 and with hedging using the extended straddle and the moneyness ranking respectively.

The results from the annualized return and Sharpe ratio analysis are supported by the graph, which shows that both hedging strategies consistently outperform the unhedged portfolio in terms of returns, of which the strategy of hedging using the moneyness ranking produces the highest portfolio returns. Moreover, the curves for the extended straddle are so close together that they are indistinguishable, and the same is true for the four curves for the moneyness ranking, indicating little variation between the modifications to each hedging strategy.
