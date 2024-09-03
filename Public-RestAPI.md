# Public REST API  
These are open data for public. It doesn't need an API key to call these methods. You can call simple GET request or open it directly from the browser.
#### Table of Contents
 - [General API Information](#general-api-information)
 - [Limit](#limit)
 - [API](#api)
     - [/api/server_time](#server-time) 
     - [/api/pairs](#pairs)  
     - [/api/price_increments](#price-increments)
     - [/api/summaries](#summaries)
     - [/api/ticker/$pair_id](#ticker)
     - [/api/ticker_all](#ticker-all)
     - [/api/trades/$pair_id](#trades)
     - [/api/depth/$pair_id](#depth)


## General API Information
 - The base endpoint is: https://indodax.com
 - All endpoints return either a JSON object or array.
 - All time and timestamp related fields are in milliseconds.

## Limit
Public API rate limited to 180request/minute.

## Server Time
Provide server time on exchange
#### Request
```
/api/server_time
```
#### Response
```json
{
    "timezone": "UTC",
    "server_time": 1571205969552
}
```

## Pairs
Provide available pairs on exchange
#### Request
```
/api/pairs
```
#### Response
```json
[
    {
        "id": "btcidr",
        "symbol": "BTCIDR",
        "base_currency": "idr",
        "traded_currency": "btc",
        "traded_currency_unit": "BTC",
        "description": "BTC/IDR",
        "ticker_id": "btc_idr",
        "volume_precision": 0,
        "price_precision": 1000,
        "price_round": 8,
        "pricescale": 1000,
        "trade_min_base_currency": 50000,
        "trade_min_traded_currency": 0.0001,
        "has_memo": false,
        "memo_name": false,
        "url_logo": "https://indodax.com/v2/logo/svg/color/btc.svg",
        "url_logo_png": "https://indodax.com/v2/logo/png/color/btc.png"
    }   
]
```

## Price Increments
Provide price increments of each pairs on exchange
#### Request
```
/api/price_increments
```
#### Response
```json
{
    "increments": {
        "btc_idr": "1000",
        "ten_idr": "1",
        "abyss_idr": "1",
        "act_idr": "1",
        "ada_idr": "1"
    }
}
```

## Summaries
Provide summary information for the pairs
#### Request
```
/api/summaries
```
#### Response
```json
{
    "tickers": {
        "btc_idr": {
            "high": "120009000",
            "low": "116735000",
            "vol_btc": "218.31103295",
            "vol_idr": "25831203178",
            "last": "117136000",
            "buy": "116938000",
            "sell": "117136000",
            "server_time": 1571206340,
            "name": "Bitcoin"
        }
    },
    "prices_24h": {
        "btcidr": "120002000",
        "tenidr": "521",
        "abyssidr": "148",
        "actidr": "125",
        "adaidr": "592"
    },
    "prices_7d": {
        "btcidr": "116001000",
        "tenidr": "517",
        "abyssidr": "147",
        "actidr": "128",
        "adaidr": "580"
    },
}
```

## Ticker
Provide Single Ticker Price on each pair in exchange
#### Request
```
/api/ticker/$pair_id
```
##### Parameter 
| Type | Mandatory | Description |
| ------ | ------ | ------ |
| String | Optional| Example: `btdidr`, `tenidr`, `ethidr`. For specific pair id please use Response from API [`/api/pairs`](#pairs). Default `$pair_id` is `btcidr`. |

#### Response
```json
{
    "ticker": {
        "high": "523",
        "low": "505",
        "vol_ten": "153588.49847928",
        "vol_idr": "78884203",
        "last": "511",
        "buy": "511",
        "sell": "512",
        "server_time": 1571207668
    }
}
```

## Ticker All
Provide All Ticker Prices in exchange
#### Request
```
/api/ticker_all
```
#### Response
```json
{
    "tickers": {
        "btc_idr": {
            "high": "120009000",
            "low": "116735000",
            "vol_btc": "218.13777777",
            "vol_idr": "25800033297",
            "last": "117088000",
            "buy": "117002000",
            "sell": "117078000",
            "server_time": 1571207881
        }  
    }
}
```

## Trades
Provide transaction Information from all pairs/selected pair
#### Request
```
/api/trades/$pair_id
```
##### Parameter 
| Type | Mandatory | Description |
| ------ | ------ | ------ |
| String | Optional| Example: `btdidr`, `tenidr`, `ethidr`. For specific pair id please use Response from API [`/api/pairs`](#pairs). Default `$pair_id` is `btcidr`. |

#### Response
```json
[
    {
        "date": "1571207255",
        "price": "511",
        "amount": "123.19523759",
        "tid": "1623490",
        "type": "sell"
    },
    {
        "date": "1571207236",
        "price": "512",
        "amount": "121.42187500",
        "tid": "1623489",
        "type": "buy"
    }
]
```

## Depth
Provide Volume price Buy and Sell on each pair in exchange
#### Request
```
/api/depth/$pair_id
```
##### Parameter 
| Type | Mandatory | Description |
| ------ | ------ | ------ |
| String | Optional| Example: `btdidr`, `tenidr`, `ethidr`. For specific pair id please use Response from API [`/api/pairs`](#pairs). Default `$pair_id` is `btcidr`. |
#### Response
```json
{
    "buy": [
        [
            511,
            "176.61056751"
        ],
        [
            510,
            "100.00000000"
        ]
    ],
   "sell": [
        [
            512,
            "1591.21213341"
        ],
        [
            513,
            "0.88109162"
        ]
    ]
}
```

## OHLC History
Provide OHLC (Open, High, Low, Close) Charting History Information for pairs
#### Request
```
/tradingview/history_v2?from=1698742200&symbol=$pair_id&tf=15&to=1699347009
```
##### Parameter 
| Name | Type | Mandatory | Description |
| ------ | ------ | ------ | ------ |
| from | Int | Required| beginning of time frame (unixtimestamp), Example: 1698742200 |
| to | Int | Required| end of time frame (unixtimestamp), Example: 1699347009 |
| tf | String | Required| time frame range in minute, day, or week (see [`Timeframe List`](#timeframe-list)) |
| symbol | String | Required| Example: `BTCIDR`, `ETHIDR`, `IDXIDR`. For specific pair id please use Response from API [`/api/pairs`](#pairs) |

##### Timeframe List
| Value | Description |
| ------ | ------ |
| 1 | 1 minute time frame |
| 15 | 15 minute time frame |
| 30 | 30 minute time frame |
| 60 | 60 minute / 1 Hour time frame |
| 240 | 240 minute / 4 Hours time frame |
| 1D | 1 Day time frame |
| 3D | 3 Day time frame |
| 1W | 1 Week time frame |

#### Response
```json
[
  {
    "Time": 1699328700,
    "Open": 0.9999,
    "High": 0.9999,
    "Low": 0.9999,
    "Close": 0.9999,
    "Volume": "14814.00000000"
  },
  {
    "Time": 1699329600,
    "Open": 0.9996,
    "High": 0.9996,
    "Low": 0.9996,
    "Close": 0.9996,
    "Volume": "12359.00000000"
  },
  {
    "Time": 1699330500,
    "Open": 0.9996,
    "High": 0.9996,
    "Low": 0.9996,
    "Close": 0.9996,
    "Volume": "0"
  },
]
```
