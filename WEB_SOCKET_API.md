# OTCBTC Exchange Websocket API Documentation

## Beta Warning
Please be kindly noticed that currently this version is in BETA and we may keep developing. Some changes would be expected in later versions.

## Index
- [Start Guide](#start-guide)
- [Websocket Channels](#websocket-channels)
    - [Global Channel](#global-channel)
      - [Tickers Event](#tickers)
    - [Market Channel](#market-channel)
      - [Update Event](#update)
      - [Trades Event](#trades)

## Start Guide

* Client: [Pusher](https://pusher.com/)
* Quick Start: [Javascript](https://pusher.com/docs/javascript_quick_start)
* Key:

  ```
  app_id = "431127"
  key = "aa53d2154fcdf7c92a1a"
  cluster = "ap1"
  ```


## Websocket Channels

### Global Channel

#### Subscribe

  ```js
  const globalChannel = socket.subscribe("market-global");
  ```

#### Events

  ##### tickers
  - Description: _Market tickers information._

  - Bind Event

    ```js
    globalChannel.bind("tickers", function handleTickersUpdate(data) {
      //...
    });
    ```

  - Websocket Data

    ```
    {
      "btceth": {
        "name": "BTC/ETH",
        "base_unit": "btc",
        "quote_unit": "eth",
        "low": 0.0,
        "high": 0.0,
        "last": 0.001,
        "open": 0.001,
        "volume": 0.0,
        "sell": 0.0,
        "buy": 0.001,
        "at": 1517323431
      },
      "otbeth": {
        ...
      }
      ...
    }
    ```

### Market Channel

#### Subscribe

  ```js
  const marketName = "btceth";
  const marketChannel = socket.subscribe(`market-${marketName}-global`);
  ```

#### Events

  ##### update

  - Description: _Order book information._

  - Bind Event

    ```js
    marketChannel.bind("update", function updateOrderBook(data) { 
      //...
    });
    ```

  - Websocket Data

    ```
    {
      "asks": [
        [
          "13.0",      // Price
          "0.00385041" // Volume
        ],
        [
          "13.09999999",
          "0.01628663"
        ],
        [
          "13.1",
          "0.30314825"
        ],
        ...
      ],
      "bids": [
        [
          "12.70000103",
          "0.00036908"
        ],
        [
          "12.70000102",
          "0.02260229"
        ],
        [
          "12.70000004",
          "0.23849869"
        ],    
        ...
      ]
    }
    ```

  ##### trades

  - Description: _Latest trades information._

  - Bind Event

    ```js
    marketChannel.bind("trades", function addTrades(data) {
      // ...
    });
    ```

  - Websocket Data

    ```
    [
      {
        "tid": 8018,
        "type": "buy",
        "date": 1517908850,
        "price": "0.00192897",
        "amount": "1.0"
      },
      {
        "tid": 8017,
        "type": "buy",
        "date": 1517908850,
        "price": "0.00192897",
        "amount": "2000.0"
      }
      ...
    ]
    ```
