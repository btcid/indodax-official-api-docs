# Market Data Websocket

## Client Libraries
You can integration using some of these [libraries](https://centrifugal.github.io/centrifugo/libraries/client)

## General Websocket Information
Name | Description | Value
------------ | ------------ | ------------
| Base Endpoint | Establish connection to this URL | wss://ws.indodax.com/ws/
| Token | Token to coonect in to websocket | eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxIiwiaW5mbyI6eyJuYW1lIjoiUHVibGljIn19.VJAHTrrfwxceSITpuPBl75LVM5bgojKGiUTOwCZxw-k

### Example
```
<!DOCTYPE html>
<title>WebSocket Test</title>
<script type="text/javascript" src="https://cdn.jsdelivr.net/gh/centrifugal/centrifuge-js@2.X.X/dist/centrifuge.min.js"></script>

<script type="text/javascript">
    function testws() {

        var host = "wss://ws.indodax.com/ws/";
        var token = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxIiwiaW5mbyI6eyJuYW1lIjoiUHVibGljIn19.VJAHTrrfwxceSITpuPBl75LVM5bgojKGiUTOwCZxw-k";

        var centrifuge = new Centrifuge(host);
        centrifuge.setToken(token);

        centrifuge.subscribe("btcidr.trade", function(message) {
            console.log(message);
        });

        centrifuge.connect();
    }

    document.addEventListener('DOMContentLoaded', function() {
        testws();
    }, false);
</script>
```


## Understanding the Subscription Response
Every subscription will have a response.
```json
{
  "data": {}, #Data subscription
}
```

## Public Channels
### Open Order
Fetches the orderbook with a depth of 50 orders per side

The data is ordered by price, order sell starting from lowest price and order buy starting from the highest price.

**Channel**
$SYMBOL.depth => eg. btcidr.depth

**Response Parameter**
|Parameter|Type|Comment|
|-|-|-|
|ask.order|float|Amount traded currency|
|ask.price|string|Order price|
|bid.order|float|Amount traded currency|
|bid.price|string|Order price|

**Example**
```json
{
  "data": {
    "ask": [
      {
        "order": 2.16143518,
        "price": "133610000"
      },
      {
        "order": 5,
        "price": "139000000"
      }
    ],
    "bid": [
      {
        "order": 1.22858981,
        "price": "130599000"
      },
      {
        "order": 3.84769292,
        "price": "129948000"
      }
    ]
  }
}
```

### Trade
Get real-time trading information.

**Channel**
$SYMBOL.trade => eg. btcidr.trade

**Response Parameter**
|Parameter|Type|Comment|
|-|-|-|
|amount|float|amount|
|direction|int| 1=buy |
|price|float|Order price|
|symbol|string|symbol|
|trade_time|int|trade time in unix timestamp|

**Example**
```json
{
  "data": {
    "amount": 348954,
    "direction": 1,
    "price": 133610000,
    "symbol": "btcidr",
    "trade_time": 1602575220
  }
}
```

### Market Summaries
Get real-time summaries for all coin

**Channel**
market.summaries

**Response Parameter**
|Parameter|Type|Comment|
|-|-|-|
|prices_24h|||
|prices_24h.$symbol|int|last prices in 24 Hours|
|prices_7d|||
|prices_7d.$symbol|int|last prices in last 7 days|
|tickers|||
|tickers.$symbol|||
|tickers.$symbol.close|int|closing price|
|tickers.$symbol.high|int|maximum price|
|tickers.$symbol.low|int|minimum price|
|tickers.$symbol.name|int|traded currency|
|tickers.$symbol.open|int|starting price|
|tickers.$symbol.server_time|int|trade time in unix timestamp|
|tickers.$symbol.volume|float|trading volume|

**Example**
```json
{
  "data": {
    "prices_24h": {
      "abyssidr": 102,
      "actidr": 72
    },
    "prices_7d": {
      "abyssidr": 102,
      "actidr": 72
    },
    "tickers": {
      "abyssidr": {
        "close": 102,
        "high": 102,
        "low": 102,
        "name": "abyss",
        "open": 102,
        "server_time": 1602578898,
        "volume": 0
      },
      "actidr": {
        "close": 72,
        "high": 73,
        "low": 72,
        "name": "act",
        "open": 80,
        "server_time": 1602578898,
        "volume": 184082.23124547
      }
    }
  }
}
```