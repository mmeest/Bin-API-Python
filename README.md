# Bin-API-Python
Binance API in Python

<hr>

### Contents
- [Setup](#setup)
- [Api keys](#api-keys)
- [Retriev your balance](#retrieve-your-balance)
- [Buy order](#buy-order)

<hr>

### Setup

Setup Python-Binance libraries

```
pip install python-binance
```

### Api keys

You can store your API keys(api and secret key) in your 'config.py' file 
but better and secure option is to sthore them as environment variables.

```
set binance_api=your_api_key_here
set binance_secret=your_api_secret_here
```

To access your api_key and api_secret

```
C:\>python
>>> import os
>>> api_key = os.environ.get('binance_api')
>>> api_secret = os.environ.get('binance_secret')
>>> api_key, api_secret
('your_api_key_here', 'your_api_secret_here')
>>> _
```

### Retrieve your balance

```
import os

from binance.client import Client
```

Retrieve API keys

```
# init
api_key = os.environ.get('binance_api')
api_secret = os.environ.get('binance_secret')
```

Store keys to local variables
```
client = Client(api_key, api_secret)
```

Get balances and assets. This will print out all balances. Even zero balances.
```
# get balances for all assets & some account information
print(client.get_account())
```

To get balances bigger than zero
```
# get balances
bal = info['balances']

for b in bal:
    if float(b['free']) > 0:
        print(b)
```

To get BTC balance
```
# get balance for a specific asset only (BTC)
print(client.get_asset_balance(asset='BTC'))
```

To get latest Bitcoin price
```
# get latest price from Binance API
btc_price = client.get_symbol_ticker(symbol="BTCUSDT")
# print full output (dictionary)
print(btc_price)
```

To get exchange endpoint for example for 'BTCUSDT'
'get_exchange_info' will return symbol pair result
'get_symbol_info' will return single symbol result
```
import asyncio
import json

from binance import AsyncClient

    async def main():
    
        client = await AsyncClient.create()
        symbol_info = await client.get_symbol_info('BTCUSDT')
    
        print(json.dumps(symbol_info, indent=2))

        await client.close_connection()
    
    if __name__ == "__main__":
    
        loop = asyncio.get_event_loop()
        loop.run_until_complete(main())
```

This returns
```
    {
      "symbol": "BTCUSDT",
      "status": "TRADING",
      "baseAsset": "BTC",
      "baseAssetPrecision": 8,
      "quoteAsset": "USDT",
      "quotePrecision": 8,
      "quoteAssetPrecision": 8,
      "baseCommissionPrecision": 8,
      "quoteCommissionPrecision": 8,
      "orderTypes": [
        "LIMIT",
        "LIMIT_MAKER",
        "MARKET",
        "STOP_LOSS_LIMIT",
        "TAKE_PROFIT_LIMIT"
      ],
      "icebergAllowed": true,
      "ocoAllowed": true,
      "quoteOrderQtyMarketAllowed": true,
      "isSpotTradingAllowed": true,
      "isMarginTradingAllowed": true,
      "filters": [
        {
          "filterType": "PRICE_FILTER",
          "minPrice": "0.01000000",
          "maxPrice": "1000000.00000000",
          "tickSize": "0.01000000"
        },
        {
          "filterType": "PERCENT_PRICE",
          "multiplierUp": "5",
          "multiplierDown": "0.2",
          "avgPriceMins": 5
        },
        {
          "filterType": "LOT_SIZE",
          "minQty": "0.00000100",
          "maxQty": "9000.00000000",
          "stepSize": "0.00000100"
        },
        {
          "filterType": "MIN_NOTIONAL",
          "minNotional": "10.00000000",
          "applyToMarket": true,
          "avgPriceMins": 5
        },
        {
          "filterType": "ICEBERG_PARTS",
          "limit": 10
        },
        {
          "filterType": "MARKET_LOT_SIZE",
          "minQty": "0.00000000",
          "maxQty": "104.64146873",
          "stepSize": "0.00000000"
        },
        {
          "filterType": "MAX_NUM_ORDERS",
          "maxNumOrders": 200
        },
        {
          "filterType": "MAX_NUM_ALGO_ORDERS",
          "maxNumAlgoOrders": 5
        }
      ],
      "permissions": [
        "SPOT",
        "MARGIN"
      ]
    }
```

### Buy order

Example of test order. For live order use 'create_order' function instead of 'create_test_order'
```
buy_order_limit = client.create_test_order(
    symbol='ETHUSDT',
    side='BUY',
    type='LIMIT',
    timeInForce='GTC',
    quantity=100,
    price=200)
```

Market order would look like this
```
buy_order = client.create_test_order(
    symbol='ETHUSDT', 
    side='BUY', 
    type='MARKET', 
    quantity=100)
```

In case there could be Exeption we need to use try/exept block and impoet exceptions from library.
```
import os

from binance.client import Client
from binance.enums import *
from binance.exceptions import BinanceAPIException, BinanceOrderException

# init
api_key = os.environ.get('binance_api')
api_secret = os.environ.get('binance_secret')

client = Client(api_key, api_secret)
```

And here is the code block
```
# create a real order if the test orders did not raise an exception

try:
    buy_limit = client.create_order(
        symbol='ETHUSDT',
        side='BUY',
        type='LIMIT',
        timeInForce='GTC',
        quantity=100,
        price=200)

except BinanceAPIException as e:
    # error handling goes here
    print(e)
except BinanceOrderException as e:
    # error handling goes here
    print(e)
```

An order confirmation will be sent back from the exchange
and stored in our buy_limit variable. This is what it looks like:
```
{'symbol': 'ETHUSDT',
 'orderId': 87899939,
 'orderListId': -1,
 'clientOrderId': 'UNHu1iLx0VFWzICafm0VHF',
 'transactTime': 1592018645594,
 'price': '200.00000000',
 'origQty': '100.00000000',
 'executedQty': '0.00000000',
 'cummulativeQuoteQty': '0.00000000',
 'status': 'NEW',
 'timeInForce': 'GTC',
 'type': 'LIMIT',
 'side': 'BUY',
 'fills': []}
```

With this order Id we can cancel order:
```
    # cancel previous orders
    cancel = client.cancel_order(symbol='ETHUSDT', orderId=buy_limit['orderId'])
```

Two short example orders
```
    # same order but with helper function
    buy_limit = client.order_limit_buy(symbol='ETHUSDT', quantity=100, price=200)

    # market order using a helper function
    market_order = client.order_market_sell(symbol='ETHUSDT', quantity=100)
```

Helper functions for orders:
* order_limit_buy()
* order_limit_sell()
* order_market_buy()
* order_market_sell()
* order_oco_buy()
* order_ocosell()

The last two are considered advanced order types. OCO stands for One Cancels the Other. 

Here is an example of an order without using the built-in variables:
```
buy_order = client.create_test_order(
  symbol='ETHUSDT', 
  side='BUY', 
  type='MARKET', 
  quantity=100)
```

And here is the same thing using the built-in variables.
```
buy_order = client.create_test_order(
  symbol='ETHUSDT', 
  side=SIDE_BUY, 
  type=ORDER_TYPE_MARKET, 
  quantity=100)
```

Stop loss or take profit using the Binance API
```
try:
    order = client.create_oco_order(
        symbol='ETHUSDT',
        side='SELL',
        quantity=100,
        price=250,
        stopPrice=150,
        stopLimitPrice=150,
        stopLimitTimeInForce='GTC')

except BinanceAPIException as e:
    # error handling goes here
    print(e)
except BinanceOrderException as e:
    # error handling goes here
    print(e)
```

### Live orders

To buy or sell all assets (BTC USD Example)
```
usdtBalance = client.get_asset_balance(asset='USDT').get('free')
btcBalance = client.get_asset_balance(asset='BTC').get('free')

order_buy = Client.order_market_buy(symbol='BTCUSDT', quantity=usdtBalance)

order_sell = Client.order_market_sell(symbol='BTCUSDT', quantity=btcBalance)
```

This will throw Error because Binance takes 0.1 percent on every order
