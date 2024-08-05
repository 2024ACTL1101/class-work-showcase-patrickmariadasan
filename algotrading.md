
## Algorithmic Trading Strategy

## Introduction

In this assignment, you will develop an algorithmic trading strategy by incorporating financial metrics to evaluate its profitability. This exercise simulates a real-world scenario where you, as part of a financial technology team, need to present an improved version of a trading algorithm that not only executes trades but also calculates and reports on the financial performance of those trades.

## Background

Following a successful presentation to the Board of Directors, you have been tasked by the Trading Strategies Team to modify your trading algorithm. This modification should include tracking the costs and proceeds of trades to facilitate a deeper evaluation of the algorithm’s profitability, including calculating the Return on Investment (ROI).

After meeting with the Trading Strategies Team, you were asked to include costs, proceeds, and return on investments metrics to assess the profitability of your trading algorithm.

## Objectives

1. **Load and Prepare Data:** Open and run the starter code to create a DataFrame with stock closing data.

2. **Implement Trading Algorithm:** Create a simple trading algorithm based on daily price changes.

3. **Customize Trading Period:** Choose your entry and exit dates.

4. **Report Financial Performance:** Analyze and report the total profit or loss (P/L) and the ROI of the trading strategy.

5. **Implement a Trading Strategy:** Implement a trading strategy and analyze the total updated P/L and ROI. 

6. **Discussion:** Summarise your finding.


## Instructions

### Step 1: Data Loading

Start by running the provided code cells in the "Data Loading" section to generate a DataFrame containing AMD stock closing data. This will serve as the basis for your trading decisions. First, create a data frame named `amd_df` with the given closing prices and corresponding dates. 

```r
# Load data from CSV file
amd_df <- read.csv("AMD.csv")
# Convert the date column to Date type and Adjusted Close as numeric
amd_df$date <- as.Date(amd_df$Date)
amd_df$close <- as.numeric(amd_df$Adj.Close)
amd_df <- amd_df[, c("date", "close")]
```

#### Plotting the Data
Plot the closing prices over time to visualize the price movement.
```r
plot(amd_df$date, amd_df$close,'l')
```

### Step 2: Trading Algorithm
Implement the trading algorithm as per the instructions. You should initialize necessary variables, and loop through the dataframe to execute trades based on the set conditions.

- Initialize Columns: Start by ensuring dataframe has columns 'trade_type', 'costs_proceeds' and 'accumulated_shares'.
- Change the algorithm by modifying the loop to include the cost and proceeds metrics for buys of 100 shares. Make sure that the algorithm checks the following conditions and executes the strategy for each one:
  - If the previous price = 0, set 'trade_type' to 'buy', and set the 'costs_proceeds' column to the current share price multiplied by a `share_size` value of 100. Make sure to take the negative value of the expression so that the cost reflects money leaving an account. Finally, make sure to add the bought shares to an `accumulated_shares` variable.
  - Otherwise, if the price of the current day is less than that of the previous day, set the 'trade_type' to 'buy'. Set the 'costs_proceeds' to the current share price multiplied by a `share_size` value of 100.
  - You will not modify the algorithm for instances where the current day’s price is greater than the previous day’s price or when it is equal to the previous day’s price.
  - If this is the last day of trading, set the 'trade_type' to 'sell'. In this case, also set the 'costs_proceeds' column to the total number in the `accumulated_shares` variable multiplied by the price of the last day.



```r
# Initialize columns for trade type, cost/proceeds, and accumulated shares in amd_df
amd_df$trade_type <- NA
amd_df$costs_proceeds <- NA  # Corrected column name
amd_df$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

for (i in 1:nrow(amd_df)) {
  if (previous_price == 0 || amd_df$close[i] < previous_price) {
    # Buy if first day or if current price is less than  previous day's price
    amd_df$trade_type[i] <- "buy"
    amd_df$costs_proceeds[i] <- -amd_df$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
  } else {
    # Do not buy if price greater or equal to previous day's price
    amd_df$trade_type[i] <- NA
    amd_df$costs_proceeds[i] <- 0
  }
  
  # Accumulate shares in the data frame
  amd_df$accumulated_shares[i] <- accumulated_shares
  
  # Sell all shares on the last day
  if (i == nrow(amd_df)) {
    amd_df$trade_type[i] <- "sell"
    amd_df$costs_proceeds[i] <- amd_df$close[i] * accumulated_shares
    accumulated_shares <- 0
    amd_df$accumulated_shares[i] <- accumulated_shares
  }
  
  # Store previous price for loop
  previous_price <- amd_df$close[i]
}
```


### Step 3: Customize Trading Period
- Define a trading period you wanted in the past five years 
```r
start_date <- as.Date("2021-07-27")
end_date <- as.Date("2023-07-27")

desired_trading_period <- amd_df[amd_df$date >= start_date & amd_df$date <= end_date, ]
```


### Step 4: Run Your Algorithm and Analyze Results
After running your algorithm, check if the trades were executed as expected. Calculate the total profit or loss and ROI from the trades.

- Total Profit/Loss Calculation: Calculate the total profit or loss from your trades. This should be the sum of all entries in the 'costs_proceeds' column of your dataframe. This column records the financial impact of each trade, reflecting money spent on buys as negative values and money gained from sells as positive values.
- Invested Capital: Calculate the total capital invested. This is equal to the sum of the 'costs_proceeds' values for all 'buy' transactions. Since these entries are negative (representing money spent), you should take the negative sum of these values to reflect the total amount invested.
- ROI Formula: $$\text{ROI} = \left( \frac{\text{Total Profit or Loss}}{\text{Total Capital Invested}} \right) \times 100$$

```r
# Updated code for desired trading period dataframe

# Initialize columns for trade type, cost/proceeds, and accumulated shares in desired_trading_period
desired_trading_period$trade_type <- NA
desired_trading_period$costs_proceeds <- NA  # Corrected column name
desired_trading_period$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
share_size <- 100
accumulated_shares <- 0

for (i in 1:nrow(desired_trading_period)) {
  if (previous_price == 0 || desired_trading_period$close[i] < previous_price) {
    # Buy if first day or if current price is less than  previous day's price
    desired_trading_period$trade_type[i] <- "buy"
    desired_trading_period$costs_proceeds[i] <- -desired_trading_period$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
  } else {
    # Do not buy if price greater or equal to previous day's price
    desired_trading_period$trade_type[i] <- NA
    desired_trading_period$costs_proceeds[i] <- 0
  }
  
  # Accumulate shares in the data frame
  desired_trading_period$accumulated_shares[i] <- accumulated_shares
  
  # Sell all shares on the last day
  if (i == nrow(desired_trading_period)) {
    desired_trading_period$trade_type[i] <- "sell"
    desired_trading_period$costs_proceeds[i] <- desired_trading_period$close[i] * accumulated_shares
    accumulated_shares <- 0
    desired_trading_period$accumulated_shares[i] <- accumulated_shares
  }
  
  # Store previous price for loop
  previous_price <- desired_trading_period$close[i]
}


# calculating total profit/loss in the desired trading period
total_profit_or_loss <- sum(desired_trading_period$costs_proceeds, na.rm = TRUE)
cat("Total Profit/Loss:", total_profit_or_loss, "$\n")

# calculating invested capital in the desired trading period
invested_capital <- -sum(desired_trading_period$costs_proceeds[desired_trading_period$costs_proceeds < 0], na.rm = TRUE)
cat("Invested Capital:", invested_capital, "$\n")

# calculating ROI in the desired trading period
return_on_investment <- (total_profit_or_loss / invested_capital) * 100
cat("Return on Investment:", round(return_on_investment, 2), "%\n")
```

### Step 5: Profit-Taking Strategy or Stop-Loss Mechanisum (Choose 1)
- Option 1: Implement a profit-taking strategy that you sell half of your holdings if the price has increased by a certain percentage (e.g., 20%) from the average purchase price.
- Option 2: Implement a stop-loss mechanism in the trading strategy that you sell half of your holdings if the stock falls by a certain percentage (e.g., 20%) from the average purchase price. You don't need to buy 100 stocks on the days that the stop-loss mechanism is triggered.


```r
# Completing Option 1:

# Creating a new dataframe for the profit-taking strategy
profit_taking_strategy <- desired_trading_period

# Initialize columns for trade type, cost/proceeds, and accumulated shares in profit_taking_strategy
profit_taking_strategy$trade_type <- NA
profit_taking_strategy$costs_proceeds <- NA  # Corrected column name
profit_taking_strategy$accumulated_shares <- 0  # Initialize if needed for tracking

# Initialize variables for trading logic
previous_price <- 0
accumulated_shares <- 0
total_cost_of_purchases <- 0
average_share_price <- 0
share_size <- 100

# Initialize share price threshold limit for half of total shares to be sold
price_threshold <- 1.3

for (i in 1:nrow(profit_taking_strategy)) {
   if (profit_taking_strategy$close[i] >= price_threshold * average_share_price && accumulated_shares > 0) {
    # Sell half of total shares accumulated if price is above price threshold
    shares_to_sell <- accumulated_shares / 2
    profit_taking_strategy$trade_type[i] <- "sell"
    profit_taking_strategy$costs_proceeds[i] <- profit_taking_strategy$close[i] * shares_to_sell
    accumulated_shares <- accumulated_shares - shares_to_sell
    total_cost_of_purchases <- total_cost_of_purchases - (average_share_price * shares_to_sell)
    average_share_price <- total_cost_of_purchases / accumulated_shares
  } else if (previous_price == 0 || profit_taking_strategy$close[i] < previous_price) {
    # Buy if first day or if current price is less than previous day's price
    profit_taking_strategy$trade_type[i] <- "buy"
    profit_taking_strategy$costs_proceeds[i] <- -profit_taking_strategy$close[i] * share_size
    accumulated_shares <- accumulated_shares + share_size
    total_cost_of_purchases <- total_cost_of_purchases + (profit_taking_strategy$close[i] * share_size)
    average_share_price <- total_cost_of_purchases / accumulated_shares
  } else {
    # Do not trade the conditions are not met
    profit_taking_strategy$trade_type[i] <- NA
    profit_taking_strategy$costs_proceeds[i] <- 0
  }
  
  # Accumulate shares in the data frame
  profit_taking_strategy$accumulated_shares[i] <- accumulated_shares
  
  # Sell all shares on the last day
  if (i == nrow(profit_taking_strategy)) {
    profit_taking_strategy$trade_type[i] <- "sell"
    profit_taking_strategy$costs_proceeds[i] <- profit_taking_strategy$close[i] * accumulated_shares
    accumulated_shares <- 0
    profit_taking_strategy$accumulated_shares[i] <- accumulated_shares
  }
  
  # Store previous price for loop
  previous_price <- profit_taking_strategy$close[i]
}
```


### Step 6: Summarize Your Findings
- Did your P/L and ROI improve over your chosen period?
- Relate your results to a relevant market event and explain why these outcomes may have occurred.


```r
# Fill your code here and Discuss

# calculating total profit/loss using profit-taking strategy
total_profit_or_loss <- sum(profit_taking_strategy$costs_proceeds, na.rm = TRUE)
cat("Total Profit/Loss:", total_profit_or_loss, "$\n")

# calculating invested capital using profit-taking strategy
invested_capital <- -sum(profit_taking_strategy$costs_proceeds[profit_taking_strategy$costs_proceeds < 0], na.rm = TRUE)
cat("Invested Capital:", invested_capital, "$\n")

# calculating ROI using profit-taking strategy
return_on_investment <- (total_profit_or_loss / invested_capital) * 100
cat("Return on Investment:", round(return_on_investment, 2), "%\n")
```

Discussion: After implementing the profit-taking strategy, P/L improves from $339790 to $744316.80, and ROI increases from 13.51% to 31.58% over the 2-year trading period. The initial rise in share price seen in the second half of 2021 marks increasing investor confidence stemming from expanding economic activity as the global economy recovered from COVID-19. The profit-taking method allowed for shares to be sold during this time as share prices peaked, from 8th November 2021 to 2nd December 2021. 

However, as 2022 started, AMD shares can be seen to fall in price. This can be attributed to a lack of investor confidence stemming from the ongoing Russia/Ukraine War, as well as fears of an economic recession in the United States of America. Specifically regarding AMD, this period also sees a 'cyclical decline in PC sales', where the processing chips they manufactured are demanded to a lower extent. As a result, share prices continue to fall in 2022 and only start to improve in 2023. By only selling shares at the end of the period like seen in Step 4, ROI will not be maximised.





