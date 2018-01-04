# HW07_Quantopian
Quantopian策略

績效:
-Total Returns:141.81%
-Benchmark Returns:59.33%
-Alpha:0.09
-Beta:1.44
-Sharpe:0.86
-Sortino:1.26
-Volatility:0.31
-Max Drawdown:43.91%

程式碼:
def initialize(context):
    # Stock = AAPL、MICROSOFT CORP
    context.security = [sid(24),sid(5061)]

def rebalance(context, data):
    for stock in context.security:

        price_history = data.history(
            stock,
            fields='price',
            bar_count=5,
            frequency='1d'
        )
     # get the volume history's information to calculating 
     # the stock's moving average for the last 10 mins.
        volume_history = data.history(
            stock,
            fields='volume',
            bar_count=10,
            frequency='1m'
        )
     # take an average 
        average_price = price_history.mean()
        average_volume = volume_history.mean()
    
     # get the stock's current price and current volume. 
        current_price = data.current(stock, 'price') 
        current_volume = data.current(stock, 'volume')
        
     # If  stock is currently listed on a major exchange
        if data.can_trade(stock):
        # If the current price is 1% above the 5-day average price, 
        # we open a long position. If the current price is below the 
        # average price, then we want to close our position to 0 shares.
           if current_price > (1.01 * average_price):
            # Place the buy order (positive means buy, negative means sell)
               order_target_percent(stock, 1)
               log.info("Buying %s" % (stock.symbol))
           elif current_price < average_price:
            # Sell all of our shares by setting the target position to zero
               order_target_percent(stock, 0)
               log.info("Selling %s" % (stock.symbol))
               
        # If the current volume is -1% above the 10-min average volume, 
        # we open a long position. If the current volume is below the 
        # average volume, then we want to close our position to 0 shares.
           if current_volume > (0.99 * average_volume):
               order_target_percent(stock, 1)
               log.info("Buying %s" % (stock.symbol))
           elif current_volume < average_volume:
               order_target_percent(stock, 0)
               log.info("Selling %s" % (stock.symbol))
                
    record(current_price=current_price, average_price=average_price)
    record(current_volume=current_volume, average_volume=average_volume)
