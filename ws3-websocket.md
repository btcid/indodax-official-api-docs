
# WS3 WebSocket Documentation

This documentation provide examples on how to use **Indodax WS3 WebSocket** which replaces the **deprecated** [K-Line Web Socket](https://github.com/btcid/indodax-official-api-docs/blob/master/kline-websocket.md).

## Table of Contents

- [General Information](#general-information)
- [Authentication](#authentication)
- [Ping/Pong](#pingpong)
- [Subscribing to Channel](#subscribing-to-channel)
  - [Chart Data](#chart-data)
  - [Market Summary](#market-summary)
  - [Trade Activity](#trade-activity)
  - [Orderbook](#orderbook)
- [Unsubscribing from Channel](#unsubscribing-from-channel)
- [Get Data from Specific Offset and Subscribe](#get-data-from-specific-offset-and-subscribe)

## General Information

WS3 base URL is:

```text
wss://ws3.indodax.com/ws/
```

After connected, you will have to **authenticate** yourself using request provided in [Authentication](#authentication) section below to make further request.

The `id` field in request or response is used as an identifier to uniquely identify them.

## Authentication

Request:

```json
{
    "params": {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE5NDY2MTg0MTV9.UR1lBM6Eqh0yWz-PVirw1uPCxe60FdchR8eNVdsskeo"
    },
    "id": 1
}
```

Response:

```json
{
    "id": 1,
    "result": {
        "client": "9690f773-a529-4277-9b09-57fa3ddea251",
        "version": "2.8.6",
        "expires": true,
        "ttl": 311392452
    }
}
```

## Ping/Pong

Use `7` as `method`.

Request:

```json
{
    "method": 7,
    "id": 3
}
```

Response:

```json
{
    "id": 3
}
```

## Subscribing to Channel

You can **subscribe** to a **channel** to get live stream of events using a **single** WebSocket connection by using `1` as `method` in the request body.

### Chart Data

Use `chart:tick-<pair>` as `channel`. Change `<pair>` to the one that you want to subscribe to.

Request:

```json
{
    "method": 1,
    "params": {
        "channel": "chart:tick-usdtidr"
    },
    "id": 2
}
```

Response:

```json
{
    "id": 2,
    "result": {
        "recoverable": true,
        "epoch": "1630401092",
        "offset": 814137
    }
}
```

Received Message:

```json
{
    "result": {
        "channel": "chart:tick-usdtidr",
        "data": {
            "data": [
                [
                    1632717721,        // epoch timestamp in second
                    4087327,           // sequence number
                    14340,             // price
                    "1063.73019525"    // volume
                ]
            ],
            "offset": 266983           // offset of the data
        }
    }
}
```

### Market Summary

Use `market:summary-24h` as `channel`.

Request:

```json
{
    "method": 1,
    "params": {
        "channel": "market:summary-24h"
    },
    "id": 2
}
```

Response:

```json
{
    "id": 2,
    "result": {
        "recoverable": true,
        "epoch": "1633995795",
        "offset": 2820132
    }
}
```

Received Message:

```json
{
    "result": {
        "channel": "market:summary-24h",
        "data": {
            "data": [
                [
                    "dogeidr",                  // pair
                    1635134109,                 // epoch timestamp in second
                    3810,                       // last price
                    3480,                       // lowest price in the last 24h
                    3980,                       // highest price in the last 24h
                    3523,                       // price at T-24h
                    "112745093944.00000000",    // IDR volume in the last 24h (DOGE/IDR)
                    "30241791.15270789"         // DOGE volume in the last 24h (DOGE/IDR)
                ],
                [
                    "usdtidr",
                    1635134410,
                    14124,
                    14076,
                    14130,
                    14123,
                    "194798674207.00000000",    // IDR volume in the last 24h (USDT/IDR)
                    "13798116.12995762"         // USDT volume in the last 24h (USDT/IDR)
                ]
            ],
            "offset": 2444948
        }
    }
}
```

### Trade Activity

Use `market:trade-activity-<pair>` as `channel`. Change `<pair>` to the one that you want to subscribe to.

Request:

```json
{
    "method": 1,
    "params": {
        "channel": "market:trade-activity-btcidr"
    },
    "id": 2
}
```

Response:

```json
{
    "id": 2,
    "result": {
        "recoverable": true,
        "epoch": "1633996541",
        "offset": 243555
    }
}
```

Received Message:

```json
{
    "result": {
        "channel": "market:trade-activity-btcidr",
        "data": {
            "data": [
                [
                    "btcidr",       // pair
                    1635274052,     // epoch timestamp in second
                    21999427,       // sequence number
                    "buy",          // side (buy/sell)
                    881991000,      // filled price
                    "29740",        // IDR volume (BTC/IDR)
                    "0.00003372"    // BTC volume (BTC/IDR)
                ]
            ],
            "offset": 243556
        }
    }
}
```

### Orderbook

Use `market:order-book-<pair>` as `channel`.

Request:

```json
{
	"method": 1,
	"params": {
		"channel": "market:order-book-btcidr"
	},
	"id": 4
}
```

Response:

```json
{
	"result": {
		"channel": "market:order-book-btcidr",
		"data": {
			"data": {
				"pair": "btcidr",
				"ask": [{
						"btc_volume": "0.11035661",
						"idr_volume": "35251984",
						"price": "319437000"
					},
					{
						"btc_volume": "0.20000000",
						"idr_volume": "63950800",
						"price": "319754000"
					}
				],
				"bid": [{
						"btc_volume": "0.61427265",
						"idr_volume": "196220798",
						"price": "319436000"
					},
					{
						"btc_volume": "0.00697822",
						"idr_volume": "2228655",
						"price": "319373000"
					}
				]
			},
			"offset": 67409
		}
	}
}
```

## Unsubscribing from Channel

To **unsubscribe** from a **channel**, use `2` as `method` in the request body.

Request:

```json
{
    "method": 2,
    "params": {
        "channel": "chart:tick-usdtidr"
    },
    "id": 3
}
```

Response:

```json
{
    "id":  3,
    "result": {}
}
```

## Get Data from Specific Offset and Subscribe

In case of connection problem, you can get data from specific **offset** and subscribe again. Use `true` as `recover` and specify the last `offset` that you already have. The **response** will contain data from the **next** offset and you will be subscribed again and receive messages.

Request:

```json
{
    "method": 1,
    "params": {
        "channel": "chart:tick-usdtidr",
        "recover": true,
        "offset": 820574
    },
    "id": 2
}
```

Response:

```json
{
    "id": 2,
    "result": {
        "recoverable": true,
        "epoch": "1630401092",
        "publications": [
            {
                "data": [
                    [
                        1635245109,
                        4673347,
                        14130,
                        "17.75166439"
                    ]
                ],
                "offset": 820574
            },
            {
                "data": [
                    [
                        1635245114,
                        4673348,
                        14130,
                        "10.00000000"
                    ]
                ],
                "offset": 820575
            },
            {
                "data": [
                    [
                        1635245117,
                        4673349,
                        14131,
                        "221.07734767"
                    ]
                ],
                "offset": 820576
            },
            {
                "data": [
                    [
                        1635245127,
                        4673350,
                        14131,
                        "3.24202108"
                    ]
                ],
                "offset": 820577
            }
        ],
        "offset": 820577
    }
}
```
