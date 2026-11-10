# TAPI v2: Public Endpoints

**Table of Contents**

- [INDODAX Trade API 2.0](#indodax-trade-api-20)
- [General API Information](#general-api-information)
- [Rate Limit](#rate-limit)
- [Public API Endpoints](#public-api-endpoints)
  * [Order Book](#order-book)
  * [Recent Trades List](#recent-trades-list)
  * [24hr Ticker Price Change Statistics](#24hr-ticker-price-change-statistics)
  * [Symbol Price Ticker](#symbol-price-ticker)
  * [Symbol Order Book Ticker](#symbol-order-book-ticker)

---

## **INDODAX Trade API 2.0**

## General API Information

INDODAX provides public API endpoints that allow users and developers to access market data without requiring an API key or authentication. Data can be retrieved using simple HTTP `GET` requests or by accessing the endpoint URLs directly through a web browser.

- The base endpoint is [**https://api.indodax.com/**](https://api.indodax.com/)
- All endpoints return **JSON objects or arrays**
- All **timestamps** and **time-related fields** are in **milliseconds (ms)**

## Rate Limit

- Public API requests are limited to **300 requests per minute**.

## Public API Endpoints

TAPIv2 introduces new endpoints for accessing publicly available market data and cryptocurrency information, providing a more standardized, reliable, and easy-to-use integration experience. These endpoints allow users to retrieve real-time market information, monitor trading activity, and access order book data without requiring API key authentication.

The TAPIv2 Public API endpoints are as follows:

| **Endpoint**                    | **Purpose**                                                              |
| ------------------------------- | ------------------------------------------------------------------------ |
| `GET /api/v2/depth`             | To retrieve the **order book** (buy and sell orders) for a trading pair  |
| `GET /api/v2/trades`            | To retrieve the **recent trade history** for a trading pair              |
| `GET /api/v2/ticker/24hr`       | To retrieve **24-hour price change statistics** for one or more trading pairs |
| `GET /api/v2/ticker/price`      | To retrieve the **latest trading price** for one or more trading pairs   |
| `GET /api/v2/ticker/bookTicker` | To retrieve the **best bid and ask prices and quantities** for one or more trading pairs |

> ℹ️ **Important Information**
>
> Public API endpoints do not require an API key or authentication. All requests can be made using simple HTTP `GET` requests.

### Order Book

```
GET /api/v2/depth
```

#### Description

This REST endpoint serves to retrieve the current order book for a specified trading pair. The response includes the list of buy orders (bids) and sell orders (asks), with their respective prices and quantities, providing an overview of the current market depth.

#### Parameters

| **Name** | **Mandatory** | **Description**                           | **Type** | **Value**                       | **Default** |
| -------- | ------------- | ----------------------------------------- | -------- | ------------------------------- | ----------- |
| `symbol` | yes           | Trading pair symbol                       | string   | e.g., `BTCIDR`, `ETHIDR`        |             |
| `limit`  | no            | Number of order book entries for depth count | int64 | e.g., `123` (max. `5000`)       | `100`       |

#### Sample Response Body

##### Positive Case

```
{
    "bids": [
        [
            "1236777000",
            "0.00332046"
        ],
        [
            "1236776000",
            "0.02966778"
        ],
        [
            "1236766000",
            "0.00040374"
        ],
        [
            "1236755000",
            "0.01036517"
        ],
        [
            "1236754000",
            "0.00011292"
        ]
    ],
    "asks": [
        [
            "1236800000",
            "0.13607853"
        ],
        [
            "1236900000",
            "0.24355392"
        ],
        [
            "1238000000",
            "0.12000000"
        ],
        [
            "1238001000",
            "0.00002433"
        ]
    ]
}
```

#### Response Description

| **Field** | **Description**                                                       |
| --------- | --------------------------------------------------------------------- |
| bids      | List of buy orders, sorted by price in descending order (highest to lowest). |
| asks      | List of sell orders, sorted by price in ascending order (lowest to highest). |


##### Negative Case

- Invalid `symbol` value

```
{
    "code": -1121,
    "msg": "Invalid symbol."
}
```

- Invalid `limit` (value must be between 1 and 5000)

```
{
    "code": -1130,
    "msg": "Invalid parameter value.\n\nInvalid parameter 'limit', must be between 1 and 5000"
}
```

- Missing mandatory `symbol` parameter

```
{
    "code": -1102,
    "msg": "A mandatory parameter was not sent, was empty/null, or malformed.\n\nMandatory parameters symbol were not sent"
}
```

### Recent Trades List

```
GET /api/v2/trades
```

#### Description

This REST endpoint serves to retrieve the most recent executed trades for a specified trading pair. The response includes trade information such as the trade ID, execution price, traded quantity, quote quantity, execution timestamp, and best-match indicator.

#### Parameters

| **Name** | **Mandatory** | **Description**                    | **Type** | **Value**                 | **Default** |
| -------- | ------------- | ---------------------------------- | -------- | ------------------------- | ----------- |
| `symbol` | yes           | Trading pair symbol                | string   | e.g., `BTCIDR`, `ETHIDR`  |             |
| `limit`  | no            | Maximum number of records returned | int64    | e.g., `123` (max. `500`)  | `500`       |

#### Sample Response Body

##### Positive Case

```
[
    {
        "id": 72057594038071868,
        "price": "1236777000",
        "qty": "0.00002103",
        "quoteQty": "26009",
        "time": 1792610765000,
        "isBestMatch": true
    },
    {
        "id": 72057594038071867,
        "price": "1236800000",
        "qty": "0.00002098",
        "quoteQty": "25948",
        "time": 1792610754000,
        "isBestMatch": true
    },
    {
        "id": 72057594038071866,
        "price": "1236777000",
        "qty": "0.00002103",
        "quoteQty": "26009",
        "time": 1792610718000,
        "isBestMatch": true
    }
]
```

#### Response Description

| **Field**   | **Description**                                                                                                  |
| ----------- | ---------------------------------------------------------------------------------------------------------------- |
| id          | Unique identifier for the trade.                                                                                 |
| price       | Executed order price per unit of the base asset: IDR or USDT.                                                    |
| qty         | Base asset quantity executed in this trade (e.g., `btcidr`, qty is BTC).                                         |
| quoteQty    | Total trade value in the quote asset: IDR or USDT (e.g., `btcusdt`, quoteQty is USDT), calculated as `qty` × `price`. |
| time        | Timestamp when the trade was executed (milliseconds since epoch).                                                |
| isBestMatch | Best available price match status (`true` for a best price match; `false` if otherwise).                        |

##### Negative Case

- Invalid `symbol` value

```
{
    "code": -1121,
    "msg": "Invalid symbol."
}
```

- Invalid `limit` (value must be between 1 and 500)

```
{
    "code": -1130,
    "msg": "Invalid parameter value.\n\nInvalid parameter 'limit', must be between 1 and 500"
}
```

- Missing mandatory `symbol` parameter

```
{
    "code": -1102,
    "msg": "A mandatory parameter was not sent, was empty/null, or malformed.\n\nMandatory parameters symbol were not sent"
}
```

### 24hr Ticker Price Change Statistics

```
GET /api/v2/ticker/24hr
```

#### Description

This REST endpoint serves to retrieve 24-hour price change statistics for one or more trading pairs. The response includes market information such as price changes, price change percentages, latest traded prices, highest and lowest prices, trading volumes, and the best bid and ask prices within the 24-hour period.

#### Parameters

| **Name**  | **Mandatory** | **Description**          | **Type** | **Value**                                                   | **Default** |
| --------- | ------------- | ------------------------ | -------- | ----------------------------------------------------------- | ----------- |
| `symbol`  | no            | Single trading symbol    | string   | e.g., `BTCIDR`                                              |             |
| `symbols` | no            | Array of trading symbols | string[] | e.g., `BTCIDR,BTCUSDT` or `["BTCIDR","BTCUSDT"]`            |             |
| `type`    | no            | Output format            | string   | `FULL` or `MINI`                                            | `FULL`      |

#### Sample Response Body

##### Positive Case

```
{
    "symbol": "BTCIDR",
    "priceChange": "-23000",
    "priceChangePercent": "-0.002",
    "lastPrice": "1236777000",
    "lastQty": "0.00002103",
    "bidPrice": "1236777000",
    "bidQty": "0.00332046",
    "askPrice": "1236800000",
    "askQty": "0.13607853",
    "highPrice": "1236800000",
    "lowPrice": "1236777000",
    "volume": "0.02016334",
    "quoteVolume": "24937955",
    "openTime": 1792564738791,
    "closeTime": 1792651138791
}
```

#### Response Description

| **Field**          | **Description**                                                               |
| ------------------ | ----------------------------------------------------------------------------- |
| symbol             | Trading pair symbol (e.g., `BTCIDR`).                                         |
| priceChange        | Absolute price change over the last 24 hours (`lastPrice` − price at `openTime`). |
| priceChangePercent | Percentage price change over the last 24 hours.                               |
| lastPrice          | Latest traded price.                                                          |
| lastQty            | Quantity of the latest trade.                                                 |
| bidPrice           | Current best (highest) bid price.                                             |
| bidQty             | Quantity available at the best bid price.                                     |
| askPrice           | Current best (lowest) ask price.                                              |
| askQty             | Quantity available at the best ask price.                                     |
| highPrice          | Highest traded price in the last 24 hours.                                    |
| lowPrice           | Lowest traded price in the last 24 hours.                                     |
| volume             | Total traded volume in the last 24 hours, in base asset.                      |
| quoteVolume        | Total traded volume in the last 24 hours, in quote asset.                     |
| openTime           | Start timestamp of the 24-hour statistics period, in milliseconds.            |
| closeTime          | End timestamp of the 24-hour statistics period, in milliseconds.              |

##### Negative Case

- Invalid `symbol` value

```
{
    "code": -1121,
    "msg": "Invalid symbol."
}
```

- Invalid `type` value

```
{
    "code": -1130,
    "msg": "Invalid parameter value.\n\nInvalid parameter 'type', must be FULL or MINI"
}
```

### Symbol Price Ticker

```
GET /api/v2/ticker/price
```

#### Description

This REST endpoint serves to retrieve the latest trading price for one or more trading pairs. The response includes the trading pair symbol and its latest price, allowing users to obtain current market prices without retrieving additional ticker statistics.

#### Parameters

| **Name**  | **Mandatory** | **Description**          | **Type** | **Value**                                        | **Default** |
| --------- | ------------- | ------------------------ | -------- | ------------------------------------------------ | ----------- |
| `symbol`  | no            | Single trading symbol    | string   | e.g., `BTCIDR`                                   |             |
| `symbols` | no            | Array of trading symbols | string[] | e.g., `BTCIDR,BTCUSDT` or `["BTCIDR","BTCUSDT"]` |             |

#### Sample Response Body

##### Positive Case

- Single symbol request

```
{
    "symbol": "BTCIDR",
    "price": "1236777000"
}
```

- Multi-symbol / All symbols request

```
[
    {
        "symbol": "BTCIDR",
        "price": "1236777000"
    },
    {
        "symbol": "BTCUSDT",
        "price": "76404.99999900"
    }
]
```

#### Response Description

| **Field** | **Description**                                    |
| --------- | -------------------------------------------------- |
| symbol    | Trading pair symbol (e.g., `BTCIDR`).              |
| price     | Latest traded price for the symbol, in quote asset. |

##### Negative Case

- Invalid `symbol` value

```
{
    "code": -1121,
    "msg": "Invalid symbol."
}
```

### Symbol Order Book Ticker

```
GET /api/v2/ticker/bookTicker
```

#### Description

This REST endpoint serves to retrieve the best bid and ask prices for one or more trading pairs. The response includes the highest available buy price (bid), lowest available sell price (ask), and their respective quantities, providing a quick overview of the current best market prices.

If a symbol is specified, the response is a single object. If no symbol is specified, the response is an array containing the best bid and ask of every available symbol. This is a lightweight alternative to `/api/v2/depth` when only the top of the order book is needed.

#### Parameters

| **Name**  | **Mandatory** | **Description**          | **Type** | **Value**                                        | **Default** |
| --------- | ------------- | ------------------------ | -------- | ------------------------------------------------ | ----------- |
| `symbol`  | no            | Single trading symbol    | string   | e.g., `BTCIDR`                                   |             |
| `symbols` | no            | Array of trading symbols | string[] | e.g., `BTCIDR,BTCUSDT` or `["BTCIDR","BTCUSDT"]` |             |

#### Sample Response Body

##### Positive Case

- Single symbol request

```
{
    "symbol": "BTCIDR",
    "bidPrice": "1236777000",
    "bidQty": "0.00332046",
    "askPrice": "1236800000",
    "askQty": "0.13607853"
}
```

- Multi-symbol / All symbols request

```
[
    {
        "symbol": "BTCIDR",
        "bidPrice": "1236777000",
        "bidQty": "0.00332046",
        "askPrice": "1236800000",
        "askQty": "0.13607853"
    },
    {
        "symbol": "BTCUSDT",
        "bidPrice": "1000.00000000",
        "bidQty": "99888.236806",
        "askPrice": "0.00000000",
        "askQty": "0.000000"
    }
]
```

#### Response Description

| **Field** | **Description**                                    |
| --------- | -------------------------------------------------- |
| symbol    | Trading pair symbol (e.g., `BTCIDR`).              |
| bidPrice  | Current best (highest) bid price in the order book. |
| bidQty    | Quantity available at the best bid price.          |
| askPrice  | Current best (lowest) ask price in the order book. |
| askQty    | Quantity available at the best ask price.          |

##### Negative Case

- Invalid `symbol` value

```
{
    "code": -1121,
    "msg": "Invalid symbol."
}
```
