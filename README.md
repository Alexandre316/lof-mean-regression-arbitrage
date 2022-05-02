# Mean Regression Arbitrage of Listed Open-Ended Funds

## Why I did this?

LOF adopts the trading mechanism of both floor trading and OTC trading, which provides investors with the floor trading price fluctuating around the net value of the fund(NAV). The NAV is calculated according to the net value of the portfolio assets on the day after the closing of the daily exchange, and the on-the-spot transaction is subject to the transaction price, which is based on the supply and demand relationship. The floor trading price is different from the NAV, then investors have the opportunity of arbitrage. Generally speaking: 

- At the end of each cycle, the LOF with the discount ratio of [- 0.075,0] would be purchased at the beginning of next cycle;
- For those that are to be purchased in the next cycle and in the current position, adjust the weights according to the target quantity; 
- On the first trading day at the beginning of each cycle, buy the above LOF at **some price** (equal weighted). If there is no transaction on the day, we will bid continually until the transaction is concluded (but it may not buy enough target quantity);
- At the end of the cycle, those do not need to be bought in the next cycle shall be sold according to some price 
- **Note: for different sell/buy price (open, close, high, low), time and volume, things could be quite different, and I will show them in the next sections**

## Feasible Arbitrage for Small Scale Funds (Individuals)

Due to the small market trading volume, the amount of funds for feasible arbitrage strategy is small. Specifically:

- Discount ratio, premium ratio, all price come from Wind
- Settings: 1) Initial: 10000CNY; 2) buy/sell cost: 0.3%; 3) other cost: 0.1%; 4) profit stop: 15%; 5) loss stop: -5%;
- Open:
  - Buy one board lot of LOF that discounts enough (calculated at the opening price), and there must be a transaction on the day. Otherwise, withdraw the ask;
  - Sell the LOF with the discount convergence calculated at the opening price (of course, a better price could be reached instead of following the opening price and volume)
- Trading:
  - If the intraday lowest price touches the profit stop line or loss stop line, the LOF will be sold at the lowest price (the worst price) in the intraday market;
- Close
  - For those LOF I have, if discount ratio (calculated by close price) converges and PnL is larger than 0.7%, sell them at the next trading day (order:"BID");
  - Summary the account profit and loss result at a daily basis;
  - Get  "to ASK" LOFs on the next trading date: 1) negative premium; 2) discount ratio is smaller than the mean of discount ratio minus one standard deviation;

![small-scale](./Plots/Arbitrage(SmallScale).png)  
- Sharpe:1.22；Annual Return:16.11%；Max Drawdown:17.72%
- Note that we traded in a much worse price and volume. In the real world, if we can bid/ask at a better price (middle, average price, etc.), Sharpe could be over 1.8 even 2

## What About Larger?

- Discount ratio, premium ratio, all price come from Wind. The backtest goes at a weekly basis;
- Settings: 1) Initial: 300000CNY; 2) buy/sell cost: 0.3%; 3) other cost: 0.1%; 4) profit stop: 15%; 5) loss stop: -5%;
- Sell on the last day of a trading week, and buy at the first trading day per week;
- Buy price: average price(better than the lowest price); sell price: close;
- Trade volume is subjected to the real trading volume;
- I have recorded all the results the 'Plots' file and the 'Output' file. For more details, please refer to my project.  

![image-20220430134531114](./Plots/AccountBalance(WeeklyNormal).png)
- Sharpe:1.15；Annual Return:17.84%；Max Drawdown:22.17%
- When the initial comes to 300000CNY, things got much worse. Reasons below are to blame: 1) I could not buy enough volume of LOF due to the small market volume; 2) Ask larger, price worse. I tried to buy all LOFs at the highest price on the day, and it showed a unacceptable result.

## Results of Different Discount Ratio Groups

I grouped all LOF by discount ratio, and the result reveals that (-4%,-2%) group was always the best before 2019, when a reversal effect happened afterward. 

|                   | **Group  0** | **Group  1** | **Group  2** | **Group  3** | **Group  4** | **Group  5** | **Group  6** | **Group  7** | **Group  8** | **Group  9** | **Group  10** | **Group  11** | **Group  12** | **Group  13** |
| ----------------- | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------- | ------------- | ------------- | ------------- |
| **Sharpe**        | 0.1111       | 0.4127       | -0.0155      | 0.6894       | 0.0115       | -0.0276      | -0.2687      | -0.7740      | -0.6508      | -0.8161      | -0.7515       | -0.1367       | -0.7388       | -0.6931       |
| **Annual Return** | 0.0501       | 0.0916       | 0.0358       | 0.1078       | 0.0394       | 0.0328       | 0.0247       | -0.0360      | -0.0330      | -0.0571      | -0.0638       | 0.0133        | -0.0783       | -0.0250       |
| **Max Drawdown**  | 0.2387       | 0.2296       | 0.2235       | 0.1920       | 0.2593       | 0.2910       | 0.1649       | 0.3755       | 0.3863       | 0.4114       | 0.4529        | 0.3252        | 0.4386        | 0.3391        |
| **ub**(%)         | -8.0000      | -6.0000      | -4.0000      | -2.0000      | -1.0000      | -0.5000      | 0.0000       | 0.5000       | 1.0000       | 2.0000       | 4.0000        | 6.0000        | 8.0000        | 10.0000       |
| **lb**(%)         | -10.0000     | -8.0000      | -6.0000      | -4.0000      | -2.0000      | -1.0000      | -0.5000      | 0.0000       | 0.5000       | 1.0000       | 2.0000        | 4.0000        | 6.0000        | 8.0000        |

![image-20220430132635545](./Plots/GroupAccountBalance/AccountBalance(Normal).png)

## Defects and Improvements

- I tried to present the real market transactions and did the stress test strictly, and I also believed that the real market would be likely the same. However, due to the small market trading volume, it is difficult to ensure what could happen in the future.
- I expect that with the increase of market trading volume, arbitrage profit will show a trend of rising first and then falling. For individual investors, the strategy is feasible for some time to come.
