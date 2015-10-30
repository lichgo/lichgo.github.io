---
layout: post
title: 40 line Python code for P&L calculation
---
Here is my implementation of P&L calculation for real-time marketdata and trade log streaming, as you can see from the two member functions: `def update_by_tradefeed()` and `def update_by_marketdata()`.

* The traded quantity is a signed number: positive for buy and negative for sell. That means the net position is negative if the transactioin short sells some products.
* If the trade feed takes close action, need to be careful of whether the close quantity is larger than the net position. In this case, the average open price should also be updated to the close price.


{% highlight python %}
class PnlSnapshot:
    def __init__(self, ticker, buy_or_sell, traded_price, traded_quantity):
        self.m_ticker = ticker
        self.m_net_position = 0
        self.m_avg_open_price = 0
        self.m_net_investment = 0
        self.m_realized_pnl = 0
        self.m_unrealized_pnl = 0
        self.m_total_pnl = 0
        self.update_by_tradefeed(buy_or_sell, traded_price, traded_quantity)

    # buy_or_sell: 1 is buy, 2 is sell
    def update_by_tradefeed(self, buy_or_sell, traded_price, traded_quantity):
        # buy: positive position, sell: negative position
        quantity_with_direction = traded_quantity if buy_or_sell == 1 else (-1) * traded_quantity
        is_still_open = (self.m_net_position * quantity_with_direction) >= 0
        # net investment
        self.m_net_investment = max( self.m_net_investment, abs( self.m_net_position * self.m_avg_open_price  ) )
        # realized pnl
        if not is_still_open:
            # Remember to keep the sign as the net position
            self.m_realized_pnl += ( traded_price - self.m_avg_open_price ) * min( abs(quantity_with_direction), abs(self.m_net_position) ) * ( abs(self.m_net_position) / self.m_net_position )
        # total pnl
        self.m_total_pnl = self.m_realized_pnl + self.m_unrealized_pnl
        # avg open price
        if is_still_open:
            self.m_avg_open_price = ( ( self.m_avg_open_price * self.m_net_position ) + ( traded_price * quantity_with_direction ) ) / ( self.m_net_position + quantity_with_direction )
        else:
            # Check if it is close-and-open
            if traded_quantity > abs(self.m_net_position):
                self.m_avg_open_price = traded_price
        # net position
        self.m_net_position += quantity_with_direction

    def update_by_marketdata(self, last_price):
        self.m_unrealized_pnl = ( last_price - self.m_avg_open_price ) * self.m_net_position
        self.m_total_pnl = self.m_realized_pnl + self.m_unrealized_pnl
{% endhighlight %}