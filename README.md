# OTCBTC Official API Documentation

## Beta Warning
Please be kindly noticed that currently this version is in BETA and we may keep developing. Some changes would be expected in later versions.

## Web Socket API document
The web socket API document can be found here: https://github.com/otcbtc/otcbtc-exchange-api-doc/blob/master/WEB_SOCKET_API.md

## Index
- [API End Point](#end-point)
    - [endpoint](#end-point)
- [Error Message](#error-message)
    - [error message](#error-message)
- [Public API](#public-api)
    - [markets](#markets)
    - [tickers](#tickers)
    - [tickers{market}](#tickersmarket)
    - [order_book](#order_book)
    - [trades](#trades)
    - [server time](#timestamp)
    - [klines](#klines)
    - [klines_with_pending_trades](#klines_with_pending_trades)
- [Auth API](#auth-api)
    - [users](#users)
    - [orders](#list_orders)
        - [list orders](#list_orders)
        - [list order](#list_order)
        - [create order](#create_order)
        - [cancel order](#cancel_order)
        - [cancel orders](#cancel_orders)
    - [my trades](#my-trades)

**End Point**
----
The base API endpoint is `https://bb.otcbtc.com`

**Error Message**
----

If API request failed, the response will return HTTP status code, e.g. 400, 401, 404 etc., and detailed error message in JSON format.

```
  {
    "error": {
      "code": custom_error_message_code, // Not HTTP status code
      "message": detailed_error_message  // Detailed error message
    }
  }
```

**Public API**
----

### markets

* **URL**
  /api/v2/markets

* **Description**
  Get all available markets.

* **Method:**
  `GET`
  
* **Success Response:**  
    * **Code:** 200
    * **Response Body:** 

    ```
    [
      {
        "id": "otbeth",         // Unique marked id.
        "ticker_id": "btc_eth", // Unique ticker id.
        "name": "BTC/ETH"       // market name
      },
      {
        "id": "otbeth",
        "ticker_id": "otb_eth",
        "name": "OTB/ETH"
      },
      {
        "id": "eoseth",
        "ticker_id": "eos_eth",
        "name": "EOS/ETH"
      },
      {
        "id": "bcheth",
        "ticker_id": "bch_eth",
        "name": "BCH/ETH"
      },
      ...
    ]
    ```

### tickers

* **URL**
  /api/v2/tickers

* **Description**
  Get ticker of all markets.

* **Method:**
  `GET`
  
* **Success Response:**  
    * **Code:** 200
    * **Response Body:** 

    ```
    {
      "btc_eth": {
        "at": 1517833531,        // An integer represents the seconds elapsed since Unix epoch.
        "ticker": {
          "buy": "9.0000008",    // Latest bid price
          "sell": "9.78949997",  // Latest ask price
          "low": "8.65100033",   // Lowest price within last 24 hours
          "high": "9.79999439",  // Highest Price within last 24 hours
          "last": "9.0000008",   // Last trade's price
          "vol": "13.37148291".  // Trade volume within last 24 hours
        }
      },
      "otb_eth": {
        "at": 1517833531,
        "ticker": {
          "buy": "0.0011599",
          "sell": "0.00116",
          "low": "0.00109301",
          "high": "0.00116",
          "last": "0.00116",
          "vol": "71236.11349855"
        }
      },
      ...
    }
    ```
 
### tickers{market}

* **URL**
  /api/v2/tickers/{market_id}

* **Description**
  Get ticker of specific market.

* **Method:**
  `GET`

* **Example Request:**
    * **Request:**
    `GET /api/v2/tickers/otbeth`
  
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
        {
          "at": 1517833531,           // An integer represents the seconds elapsed since Unix epoch.
          "ticker": {
            "buy": "0.0011599",       // Latest bid price
            "sell": "0.00116",        // Latest ask price
            "low": "0.00109301",      // Lowest price within last 24 hours
            "high": "0.00116",        // Highest Price within last 24 hours
            "last": "0.00116",        // Last trade's price
            "vol": "71236.11349855"   // Trade volume within last 24 hours
          }
        }
        ```
 
### order_book

* **URL**
  /api/v2/order_book

* **Description**
  Get the order book of specified market.

* **Method:**
  `GET`

* **Parameters**
    * **market`(required)`**: _Unique market id. It’s always in the form of xxxyyy, where xxx is the base currency code, yyy is the quote currency code, e.g. 'otbeth’. All available markets can be found at /api/v2/markets._

    * asks_limit: _Limit the number of returned sell orders. Default to 20._

    * bids_limit: _Limit the number of returned buy orders. Default to 20._

* **Example Request:**
    * **Request:**
    `GET /api/v2/order_book?market=otbeth&asks_limit=1&bids_limit=1`
  
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
        {
          "asks": [
            {
              "id": 71468,                               // Unique order id
              "side": "sell",                            // Either 'sell' or 'buy'.
              "ord_type": "limit",                       // Type of order, now only 'limit'.
              "price": "0.00116",                        // Price for each unit. e.g. If you sell/buy 1 OTB at 0.00116 ETH, the price is '0.00116'
              "avg_price": "0.00116",                    // Average execution price, average of price in trades.
              "state": "wait",                           // One of 'wait', 'done', or 'cancel'. An order in 'wait' is an active order, waiting fullfillment; a 'done' order is an order fullfilled; 'cancel' means the order has been cancelled.
              "market": "otbeth",                        // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
              "created_at": "2018-02-04T20:13:01+08:00", // Order create time in iso8601 format.
              "volume": "5000.0",                        // The amount user want to sell/buy. An order could be partially executed, e.g. an order sell 5000 otb can be matched with a buy 2146.6 otb order, left 2000 otb to be sold; in this case the order's volume would be '5000.0', its remaining_volume would be '2853.4', its executed volume is '2146.6'.
              "remaining_volume": "2853.4",              // The remaining volume
              "executed_volume": "2146.6",               // The executed volume
              "trades_count": 3                          // Counts of trades under this order
            }
          ],
          "bids": [
            {
              "id": 74866,
              "side": "buy",
              "ord_type": "limit",
              "price": "0.00113212",
              "avg_price": "0.00113212",
              "state": "wait",
              "market": "otbeth",
              "created_at": "2018-02-05T20:32:35+08:00",
              "volume": "849.15791612",
              "remaining_volume": "845.15791612",
              "executed_volume": "4.0",
              "trades_count": 1
            }
          ]
        }
        ```

### trades

* **URL**
  /api/v2/trades

* **Description**
  Get recent trades on market, each trade is included only once. Trades are sorted in reverse creation order.

* **Method:**
  `GET`

* **Parameters**
    * **market`(required)`**: _Unique market id. It’s always in the form of xxxyyy, where xxx is the base currency code, yyy is the quote currency code, e.g. 'otbeth’. All available markets can be found at /api/v2/markets._

    * limit: _Limit the number of returned price levels. Default to 50._

    * timestamp: _An integer represents the seconds elapsed since Unix epoch. If set, only trades executed before the time will be returned._

    * from: _Trade id. If set, only trades created after the trade will be returned._

    * to: _Trade id. If set, only trades created before the trade will be returned._

    * order_by: _If set, returned trades will be sorted in specific order, default to 'desc’._

* **Example Request:**
    * **Request:**
    `GET /api/v2/trades?market=otbeth&limit=1&order_by=desc`
  
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
        [
          {
            "id": 25073,                               // Unique trade id
            "price": "0.00116",                        // Trade's price
            "volume": "473.3586",                      // Trade's volume
            "funds": "0.549095976",                    // Trade's funds, calculated by price * volume
            "market": "otbeth",                        // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
            "created_at": "2018-02-05T20:45:22+08:00", // Order create time in iso8601 format.
            "at": 1517834722,                          // An integer represents the seconds elapsed since Unix epoch.
            "side": "up"                               // Trade's side, 'up' means the price is higher than the previous one, 'down' is lower than the previous one
          }
        ]
        ```

### timestamp

* **URL**
  /api/v2/timestamp

* **Description**
  Get server current time, in seconds since Unix epoch.

* **Method:**
  `GET`

* **Example Request:**
    * **Request:**
    `GET /api/v2/timestamp`
  
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
        1517740381
        ```

### klines

* **URL**
  /api/v2/klines

* **Description**
  Get OHLC(k line) of specific market.

* **Method:**
  `GET`

* **Parameters**
    * **market`(required)`**: _Unique market id. It’s always in the form of xxxyyy, where xxx is the base currency code, yyy is the quote currency code, e.g. 'otbeth’. All available markets can be found at /api/v2/markets._

    * limit: _Limit the number of returned price levels. Default to 30._

    * period: _Time period of K line, default to 1. You can choose between 1, 5, 15, 30, 60, 120, 240, 360, 720, 1440, 4320, 10080. Default value : 1_

    * timestamp: _An integer represents the seconds elapsed since Unix epoch. If set, only k-line data after that time will be returned._

* **Example Request:**
    * **Request:**
    `GET /api/v2/klines?market=otbeth&limit=2&period=1`
  
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
        [
          [
            1517833860, // An integer represents the seconds elapsed since Unix epoch.
            0.001159,   // K line open price
            0.001162,   // K line highest price
            0.001157,   // K line lowest price
            0.001158,   // K line close price
            1000        // K line volume
          ],
          [
            1517833920,
            0.001142,
            0.001160,
            0.001142,
            0.001159,
            500
          ]
        ]
        ```

### klines_with_pending_trades

* **URL**
  /api/v2/klines_with_pending_trades

* **Description**
Get K data with pending trades, which are the trades not included in K data yet, because there’s delay between trade generated and processed by K data generator.

* **Method:**
  `GET`

* **Parameters**
    * **market`(required)`**: _Unique market id. It’s always in the form of xxxyyy, where xxx is the base currency code, yyy is the quote currency code, e.g. 'otbeth’. All available markets can be found at /api/v2/markets._

    * **trade_id`(required)`**: _The trade id of the first trade you received._

    * limit: _Limit the number of returned price levels. Default to 30._

    * period: _Time period of K line, default to 1. You can choose between 1, 5, 15, 30, 60, 120, 240, 360, 720, 1440, 4320, 10080. Default value : 1_

    * timestamp: _An integer represents the seconds elapsed since Unix epoch. If set, only k-line data after that time will be returned._

* **Example Request:**
    * **Request:**
    `GET /api/v2/klines_with_pending_trades?market=otbeth&trade_id=1&period=1`
  
    * **Success Response:**  
        * **Code:** 200
        * **Content:** 

        ```
        {
          "k": [
            [
              1517833860,
              0.001159,   // K line open price
              0.001162,   // K line highest price
              0.001157,   // K line lowest price
              0.001158,   // K line close price
              1000        // K line volume
            ]
          ],
          "trades": [
            {
              "id": 25073,                               // Unique trade id
              "price": "0.00116",                        // Trade's price
              "volume": "473.3586",                      // Trade's volume
              "funds": "0.549095976",                    // Trade's funds, calculated by price * volume
              "market": "otbeth",                        // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
              "created_at": "2018-02-05T20:45:22+08:00", // Order create time in iso8601 format.
              "at": 1517834722,                          // An integer represents the seconds elapsed since Unix epoch till the trade create time.
              "side": "up"                               // Trade's side, 'up' means the price is higher than the previous one, 'down' is lower than the previous one
            }
          ]
        }
        ```

**Auth API**
----

`auth` endpoints requires 2 extra authencation paramsters:

* `access_key`: your api key
* `signature`: can be generated by `HMAC-SHA256(payload, your_api_secret).to_hex`
    * `payload` is a string represents this request, combiled with HTTP method, request URI and request parameters:
        - HTTP method: e.g. "GET", "POST", etc
        - request URI: such as "/users/me", or "trades/my"
        - request parameters: parameters concated with "&", MUST BE IN ALPHABETICAL ORDER by parameters' name. 

Then concat the above 3 strings with `|`, you will have the `payload`.

For example:

if your `api key` is `xxx`, and your `api secret` is `yyy`, then the `payload` is `GET|/api/v2/users/me|access_key=xxx`.

The `signature` is then calculated: `a71c1c5ee28fbd2196ee0bca9e334a18e6053526bd979020ef3839245136c763`

And you can make the request:

`GET /api/v2/users/me?access_key=xxx&signature=a71c1c5ee28fbd2196ee0bca9e334a18e6053526bd979020ef3839245136c763`

_The following endpoints requires these 2 authencation parameters._

### users

* **URL**
  /api/v2/users/me

* **Description**
  Get your profile and accounts info.

* **Method:**
  `GET`

* **Parameters**
    * **access_key`(required)`**: _Access key._

    * **signature`(required)`**: _The signature of your request payload, generated using your secret key._
  
* **Example Request:**
    * **Request:**
    Please refer to the example above

    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

        ```
        {
          "user_name": "u1513250056",        // your user name
          "email": "u1513250056@gmail.com",  // your email
          "accounts": [                      // your account information
            {
              "currency": "btc",             // your BTC account
              "balance": "0.01",             // your current BTC balance amount
              "locked": "0.001",             // your current BTC locked amount
              "saving": "0.0"                // your current BTC saving amount
            },
            {
              "currency": "otb",
              "balance": "1000.0",
              "locked": "200.0",
              "saving": "0.0"
            },
            ...
          ]
        }
        ```

### list_orders

* **URL**
  /api/v2/orders

* **Description**
  Get your orders, results is paginated.

* **Method:**
  `GET`
  
* **Parameters**
    * **access_key`(required)`**: _Access key._

    * **signature`(required)`**: _The signature of your request payload, generated using your secret key._

    * market: _Unique market id. It’s always in the form of xxxyyy, where xxx is the base currency code, yyy is the quote currency code, e.g. 'otbeth’. All available markets can be found at /api/v2/markets. If left blank, the api will return your orders of all markets._

    * state: _Filter order by state, default to ‘wait’ (active orders). Other options:‘cancel’, ‘done’_

    * limit: _Limit the number of returned price levels. Default to 100._

    * page: _Specify the page of paginated results. Default value: 1_

    * order_by: _If set, returned trades will be sorted in specific order, default to 'asc’._

* **Example Request:**
    * Access Key: `xxx`
    * Secret Key: `yyy`
    * payload: `GET|/api/v2/orders|access_key=xxx&market=otbeth`
    * Calculated signature: `be0694b7c33e92da3ec6ee534f7391fb7d0332fc1d867681c5085c5194ed69c8`
    * Example Request: `GET https://bb.otcbtc.com/api/v2/orders?access_key=xxx&market=otbeth&signature=be0694b7c33e92da3ec6ee534f7391fb7d0332fc1d867681c5085c5194ed69c8`

    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

        ```
        [
          {
            "id": 1,                                   // Unique order id.
            "side": "buy",                             // Either 'sell' or 'buy'.
            "ord_type": "limit",                       // Type of order, now only 'limit'.
            "price": "0.002",                          // Price for each unit. e.g. If you sell/buy 1 OTB at 0.002 ETH, the price is '0.002'
            "avg_price": "0.0",                        // Average execution price, average of price in trades.
            "state": "wait",                           // One of 'wait', 'done', or 'cancel'. An order in 'wait' is an active order, waiting fullfillment; a 'done' order is an order fullfilled; 'cancel' means the order has been cancelled.
            "market": "otbeth",                        // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
            "created_at": "2017-02-01T00:00:00+08:00", // Order create time in iso8601 format.
            "volume": "100.0",                         // The amount user want to sell/buy. An order could be partially executed, e.g. an order sell 100 otb can be matched with a buy 60 otb order, left 40 otb to be sold; in this case the order's volume would be '100.0', its remaining_volume would be '40.0', its executed volume is '60.0'.
            "remaining_volume": "100.0",               // The remaining volume
            "executed_volume": "0.0",                  // The executed volume
            "trades_count": 1                          // Counts of trades under this order
          },
          {
            "id": 3,
            "side": "sell",
            "ord_type": "limit",
            "price": "0.003",
            "avg_price": "0.0",
            "state": "wait",
            "market": "otbeth",
            "created_at": "2017-02-01T00:00:00+08:00",
            "volume": "100.0",
            "remaining_volume": "100.0",
            "executed_volume": "0.0",
            "trades_count": 0
          }
        ]
        ```

### list_order

* **URL**
  /api/v2/order

* **Description**
  Get information of specified order.

* **Method:**
  `GET`
  
* **Parameters**
    * **access_key`(required)`**: _Access key._

    * **signature`(required)`**: _The signature of your request payload, generated using your secret key._

    * **id`(required)`**: _Unique order id._

* **Example Request:**
    * Access Key: `xxx`
    * Secret Key: `yyy`
    * payload: `GET|/api/v2/order|access_key=xxx&id=1`
    * Calculated signature: `c242b60f1830337f7618afab08d378b7cb2e9501fe226e76e0cab0ee93ac1933`
    * Example Request: `GET https://bb.otcbtc.com/api/v2/order?access_key=xxx&id=1&signature=c242b60f1830337f7618afab08d378b7cb2e9501fe226e76e0cab0ee93ac1933`

    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

        ```
        {
          "id": 1,                                   // Unique order id.
          "side": "buy",                             // Either 'sell' or 'buy'.
          "ord_type": "limit",                       // Type of order, now only 'limit'.
          "price": "0.002",                          // Price for each unit. e.g. If you sell/buy 1 OTB at 0.002 ETH, the price is '0.002'
          "avg_price": "0.0",                        // Average execution price, average of price in trades.
          "state": "wait",                           // One of 'wait', 'done', or 'cancel'. An order in 'wait' is an active order, waiting fullfillment; a 'done' order is an order fullfilled; 'cancel' means the order has been cancelled.
          "market": "otbeth",                        // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
          "created_at": "2017-02-01T00:00:00+08:00", // Order create time in iso8601 format.
          "volume": "100.0",                         // The amount user want to sell/buy. An order could be partially executed, e.g. an order sell 100 otb can be matched with a buy 60 otb order, left 40 otb to be sold; in this case the order's volume would be '100.0', its remaining_volume would be '40.0', its executed volume is '60.0'.
          "remaining_volume": "100.0",               // The remaining volume
          "executed_volume": "0.0",                  // The executed volume
          "trades_count": 1                          // Number of trades under this order
        }
        ```

### create_order

* **URL**
  /api/v2/orders

* **Description**
  Create a Sell/Buy order.

* **Method:**
  `POST`
  
* **Parameters**
    * **access_key`(required)`:** _Access key._

    * **signature`(required)`**: _The signature of your request payload, generated using your secret key._

    * **market`(required)`**: _Unique market id. It’s always in the form of xxxyyy, where xxx is the base currency code, yyy is the quote currency code, e.g. 'otbeth’. All available markets can be found at /api/v2/markets._

    * **side`(required)`**: _Either ‘sell’ or 'buy’._

    * **volume`(required)`**: _The amount user want to sell/buy. An order could be partially executed, e.g. an order sell 100 otb can be matched with a buy 60 otb order, left 40 otb to be sold; in this case the order’s volume would be '100.0’, its remaining_volume would be '40.0’, its executed volume is '60.0’._

    * price: _Price for each unit. e.g. If you want to sell/buy 1 otb at 0.002 ETH, the price is ‘0.002’._

    * ord_type: _Type of order, now only 'limit'._

* **Example Request:**
    * Access Key: `xxx`
    * Secret Key: `yyy`
    * payload: `POST|/api/v2/orders|access_key=xxx&market=otbeth&price=0.002&side=sell&volume=100`
    * Calculated signature: `efcc83119fe25b18f0a02302aaee7765b62d7bf64dc6c5d4f2266f5a5fda4327`
    * Example Request: `POST https://bb.otcbtc.com/api/v2/orders`
    * Example Request Body (form-data):

        ```
          "market": "otbeth",
          "side": "sell",
          "volume": "100",
          "price": "0.002",
          "access_key": "xxx",
          "signature": "efcc83119fe25b18f0a02302aaee7765b62d7bf64dc6c5d4f2266f5a5fda4327"
        ```

    * **Success Response:**
        * **Code:** 200
        * **Response Body:** 

        ```
        {  
           "id": 1,                                   // Unique order id. 
           "side": "sell",                            // Either 'sell' or 'buy'.
           "ord_type": "limit",                       // Type of order, now only 'limit'.
           "price": "0.002",                          // Price for each unit. e.g. If you sell/buy 100 OTB at 0.002 ETH, the price is '0.002'.
           "avg_price": "0.0",                        // Average execution price, average of price in trades.
           "state": "wait",                           // One of 'wait', 'done', or 'cancel'. An order in 'wait' is an active order, waiting fullfillment; a 'done' order is an order fullfilled; 'cancel' means the order has been cancelled.
           "market": "otbeth",                        // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
           "created_at": "2017-02-01T00:00:00+08:00", // Trade create time in iso8601 format.
           "volume": "100.0",                         // The amount user want to sell/buy. An order could be partially executed, e.g. an order sell 100 otb can be matched with a buy 60 otb order, left 40 otb to be sold; in this case the order's volume would be '100.0', its remaining_volume would be '40.0', its executed volume is '60.0'.
           "remaining_volume": "100.0",               // The remaining volume
           "executed_volume": "0.0",                  // The executed volume
           "trades_count": 0                          // Number of trades under this order
        }
        ```

### cancel_order

* **URL**
  /api/v2/order/delete

* **Description**
  Cancel an order.

* **Method:**
  `POST`
  
* **Parameters**
    * **access_key`(required)`:** _Access key._

    * **signature`(required)`**: _The signature of your request payload, generated using your secret key._

    * **id`(required)`**: _Unique order id._

* **Example Request:**
    * Access Key: `xxx`
    * Secret Key: `yyy`
    * payload: `POST|/api/v2/order/delete|access_key=xxx&id=1`
    * Calculated signature: `47ba4a04e8f5471a05078f8dd13976b7caa80665c5f8152d654486de327c395c`
    * Example Request: `POST https://bb.otcbtc.com/api/v2/order/delete`
    * Example Request Body (form-data):

      ```
        "id": "1",
        "access_key": "xxx",
        "signature": "47ba4a04e8f5471a05078f8dd13976b7caa80665c5f8152d654486de327c395c"
      ```

    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

        ```
        {  
           "id": 1,                                   // Unique order id. 
           "side": "buy",                             // Either 'sell' or 'buy'.
           "ord_type": "limit",                       // Type of order, now only 'limit'.
           "price": "0.002",                          // Price for each unit. e.g. If you sell/buy 100 OTB at 0.002 ETH, the price is '0.002'.
           "avg_price": "0.0",                        // Average execution price, average of price in trades.
           "state": "wait",                           // One of 'wait', 'done', or 'cancel'. An order in 'wait' is an active order, waiting fullfillment; a 'done' order is an order fullfilled; 'cancel' means the order has been cancelled.
           "market": "otbeth",                        // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
           "created_at": "2017-02-01T00:00:00+08:00", // Trade create time in iso8601 format.
           "volume": "100.0",                         // The amount user want to sell/buy. An order could be partially executed, e.g. an order sell 100 otb can be matched with a buy 60 otb order, left 40 otb to be sold; in this case the order's volume would be '100.0', its remaining_volume would be '40.0', its executed volume is '60.0'.
           "remaining_volume": "100.0",               // The remaining volume
           "executed_volume": "0.0",                  // The executed volume
           "trades_count": 0                          // Number of trades under this order
        }
        ```

### cancel_orders

* **URL**
  /api/v2/orders/clear

* **Description**
  Cancel all your orders.

* **Method:**
  `POST`
  
* **Parameters**
    * **access_key`(required)`:** _Access key._

    * **signature`(required)`**: _The signature of your request payload, generated using your secret key._

    * side: _If present, only sell orders (asks) or buy orders (bids) will be canncelled. Vaules: 'sell', 'buy'_

* **Example Request:**
    * Access Key: `xxx`
    * Secret Key: `yyy`
    * payload: `POST|/api/v2/orders/clear|access_key=xxx`
    * Calculated signature: `f2ab1d061ad07a2de9fe7658b7203ce28ed6b6511287502b2a7a869172039bcf`
    * Example Request: `POST https://bb.otcbtc.com/api/v2/orders/clear`
    * Example Request Body (form-data):

      ```
        "access_key": "xxx",
        "signature": "f2ab1d061ad07a2de9fe7658b7203ce28ed6b6511287502b2a7a869172039bcf"
      ```

    * **Success Response:**  
        * **Code:** 200
        * **Response Body:** 

        ```
        [  
           {  
              "id": 2,                                       // Unique order id. 
              "side": "buy",                                 // Either 'sell' or 'buy'.
              "ord_type": "limit",                           // Type of order, now only 'limit'.
              "price": "0.0015",                             // Price for each unit. e.g. If you sell/buy 100 OTB at 0.0015 ETH, the price is '0.0015'.         
              "avg_price": "0.0",                            // Average execution price, average of price in trades.
              "state": "wait",                               // One of 'wait', 'done', or 'cancel'. An order in 'wait' is an active order, waiting fullfillment; a 'done' order is an order fullfilled; 'cancel' means the order has been cancelled.
              "market": "otbeth",                            // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
              "created_at": "2017-02-01T00:00:00+08:00",     // Trade create time in iso8601 format.
              "volume": "100.0",                             // The amount user want to sell/buy. An order could be partially executed, e.g. an order sell 100 otb can be matched with a buy 60 otb order, left 40 otb to be sold; in this case the order's volume would be '100.0', its remaining_volume would be '40.0', its executed volume is '60.0'.
              "remaining_volume": "60.0",                    // The remaining volume
              "executed_volume": "40.0",                     // The executed volume
              "trades_count": 1                              // Number of trades under this order
           },
           {  
              "id":1,
              "side":"sell",
              "ord_type":"limit",
              "price":"0.0012",
              "avg_price":"0.0",
              "state":"wait",
              "market":"otbeth",
              "created_at":"2017-02-01T00:00:00+08:00",
              "volume": "100.0",
              "remaining_volume":"100.0",
              "executed_volume":"0.0",
              "trades_count":0
           }
        ]
        ```

### My Trades

* **URL**
  /api/v2/trades/my

* **Description**
  Get your executed trades. Trades are sorted in reverse creation order.

* **Method:**
  `GET`
  
* **Parameters**
    * **access_key`(required)`**: _Access key._

    * **signature`(required)`**: _The signature of your request payload, generated using your secret key._

    * **market`(required)`**: _Unique market id. It’s always in the form of xxxyyy, where xxx is the base currency code, yyy is the quote currency code, e.g. 'otbeth’. All available markets can be found at /api/v2/markets._

    * limit: _Limit the number of returned trades. Default to 50. Range 1..1000_

    * timestamp: _An integer represents the seconds elapsed since Unix epoch. If set, only trades executed before the time will be returned._

    * from: _Trade id. If set, only trades created after the trade will be returned._

    * to: _Trade id. If set, only trades created before the trade will be returned._

    * order_by:_If set, returned trades will be sorted in specific order, default to 'desc'. Values: 'asc', 'desc'_

* **Example Request:**
    * Access Key: `xxx`
    * Secret Key: `yyy`
    * payload: `GET|/api/v2/trades/my|access_key=xxx&market=otbeth`
    * Calculated signature: `9dbe0ecdcd3db5030486764dd8b8a4f15a6ee3eae47856f864d11f34c9eba478`
    * Example Request: `GET https://bb.otcbtc.com/api/v2/trades/my?access_key=xxx&market=otbeth&signature=9dbe0ecdcd3db5030486764dd8b8a4f15a6ee3eae47856f864d11f34c9eba478`    

    * **Success Response:**
        * **Code:** 200
        * **Response Body:** 

        ```
        [  
           {  
              "id": 2,                                   // Unique trade id. 
              "price": "0.0015",                         // Price for each unit. e.g. If you sell/buy 2 OTB at 0.0015 ETH, the price is '0.0015'.
              "volume": "2.0",                           // The amount of base unit. e.g. If you sell/buy 2 OTB at 0.0015 ETH, the volume is '2.0'.
              "funds": "0.003",                          // The amouut of quote unit. e.g. If you sell/buy 2 OTB at 0.0015 ETH, the funds is '0.003' ETH.
              "market": "otbeth",                        // The market in which the order is placed, e.g. 'otbeth'. All available markets can be found at /api/v2/markets.
              "created_at": "2017-01-31T00:00:00+08:00", // Trade create time in iso8601 format.
              "at": 1485792000,                          // An integer represents the seconds elapsed since Unix epoch.
              "side": "bid",                             // Either 'bid' or 'ask'.
              "order_id": 3                              // Unique order id. 
           },
           {  
              "id": 1,
              "price": "0.0018",
              "volume": "1.0",
              "funds": "0.0018",
              "market": "otbeth",
              "created_at": "2017-01-30T00:00:00+08:00",
              "at": 1485705600,
              "side": "ask",
              "order_id": 1
           }
        ]
        ```
