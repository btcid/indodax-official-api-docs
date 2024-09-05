
# Market Data WebSocket Documentation

## Table of Contents

- [General Information](#general-information)
- [Authentication](#authentication)
- [Ping/Pong](#pingpong)
- [Subscribing to Channel](#subscribing-to-channel)
  - [Chart Data](#chart-data)
  - [Market Summary](#market-summary)
  - [Trade Activity](#trade-activity)
  - [Orderbook](#orderbook)
  	 - [Streaming Result](#streaming-result)
- [Unsubscribing from Channel](#unsubscribing-from-channel)
- [Get Data from Specific Offset and Subscribe](#get-data-from-specific-offset-and-subscribe)
- [Troubleshooting](#troubleshooting)
    - [Disconnected Client Issue](#disconnected-client-issue)

## General Information

| **Environment**  | **Market Data Base URL**               | **Static Token**												|
| ---------------- | ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| Production       | wss://ws3.indodax.com/ws/      | eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE5NDY2MTg0MTV9.UR1lBM6Eqh0yWz-PVirw1uPCxe60FdchR8eNVdsskeo |
| Demo             | wss://ws.demo-indodax.com/ws/ | eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.OqWmyrtOvp_DvIdBnOgU9gyXhURkEOtu8brZtvwjYAw |

After connected, you will have to **authenticate** yourself using request provided in [Authentication](#authentication) section below to make further request.

The `id` field in request or response is used as an identifier to uniquely identify them.

## Authentication

Connect to Postman using Market Data Base URL by clicking connect, until the connect button changes to disconnect.  

Send request after insert message field with `id` and `static token` as shown below

![auth and connect](https://github.com/btcid/indodax-official-api-docs/blob/b5c1f8f5fcc79469d3f790e417b9988ac7bd26bb/assets/auth.jpg)

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

Provide a command to send a test signal to a server to check for its availability and response time. Use 7 as method.


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

Provide a visualization cryptocurrency price movements and trends for easy analysis. Use chart:tick-<pair> as channel. Change <pair> to the one that you want to subscribe to.

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

Provides a quick overview of essential cryptocurrency market information in one place. Use market:summary-24h as channel.


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

Provide real-time transactions and trading activity for cryptocurrencies, offering insights into market dynamics and trends. Use market:trade-activity-<pair> as channel. Change <pair> to the one that you want to subscribe to.

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

Provide real-time buy and sell orders for cryptocurrencies, giving insights into market depth and trading liquidity. Use market:order-book-<pair> as channel. Change <pair> to the one that you want to subscribe to.

Send request so it will give the following response.

![orderbook](https://github.com/btcid/indodax-official-api-docs/blob/e4ddf92cc75d0fca3bfe501d17c8d95f09423ffc/assets/orderbooks.png)

Request:

| **FIELD** | **TYPE**         | **DESCRIPTION**                                              | **MANDATORY** | **DEFAULT** |
| --------- | ---------------- | ------------------------------------------------------------ | ------------- | ----------- |
| method    | string           | specify the method you want to call                          | yes           |             |
| params    | application/json | data type of the received response                           | yes           |             |
| channel   | string           | pair information to be subscribed `market:order-book-<pair>` | yes           | ““          |
| id        | int              | is a random request ID specified by WebSocket client         | yes           | ““          |

```json
{
	"method": 1,
	"params": {
		"channel": "market:order-book-btcidr"
	},
	"id": 4 // is a random request ID specified by WebSocket client
}
```

Response:

| **FIELD**      | **TYPE** | **DESCRIPTION**                   | **MANDATORY** | **DEFAULT** |
| -------------- | -------- | --------------------------------- | ------------- | ----------- |
| result         | json     | result subscribe/streaming        | yes           |             |
| channel        | string   | pair information to be subscribed | yes           |             |
| data           | json     | response data                     | yes           |             |
| data.pair      | string   | btc / idr / coin name             | yes           |             |
| data.ask       | json     | required on limit order           | yes           |             |
| ask.btc_volume | float    | trading volume                    | yes           |             |
| ask.idr_volume | float    | trading volume                    | yes           |             |
| ask.price      | numeric  | order pice                        | yes           |             |
| bid.btc_volume | float    | trading volume                    | yes           |             |
| bid.idr_volume | float    | trading volume                    | yes           |             |
| bid.price      | numeric  | order price                       | yes           |             |
| offset         | int      | offset of the data                | yes           |             |

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

#### Streaming Result

By following the steps starting from connecting to Market Data & authenticate using [Authentication](#authentication) section, then following the streaming steps 

in the [Orderbook](#orderbook) section so you can get Market Data WebSocket as long as they are connected to the WS3. 

![result](https://github.com/btcid/indodax-official-api-docs/blob/e4ddf92cc75d0fca3bfe501d17c8d95f09423ffc/assets/results.png)

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
# Troubleshooting
## Disconnected Client Issue
### Description
WebSocket clients may get disconnected from the server due to internal rebalancing, potentially causing some messages to be missed.
### Solution
If you experience this issue, you can add a handler to reconnect upon disconnection.
### Implementation
#### Javascript Implementation
```
<script  src="https://unpkg.com/centrifuge@5.0.1/dist/centrifuge.js"></script>
<script  type="text/javascript">
	const  websocketURL = "wss://indodax.com/ws/";

	function  connectWS() {
		const  ws = new  WebSocket(websocketURL);

		ws.onclose = (event) => {
			// Handle on close event

			connectWS()
		};

        ws.onerror = (error) => {
            // Handle on error event

            // trigger on close event
            ws.close()
        };
	}

	connectWS()
</script>
```
#### Go Implementation
Example below use `gorilla/websocket`.
```
type  wsClient  struct {
	conn  *websocket.Conn
}
  
type  wsError  struct {
	Reason 		string  `json:"reason"`
	Reconnect	bool 	`json:"reconnect"`
}

func (c *wsClient) connectWS() {
	if  c.conn  !=  nil {
		c.conn.Close()
	}

	u  :=  url.URL{Scheme: "wss", Host: "indodax.com", Path: "/ws/"}

	conn, _, err  :=  websocket.DefaultDialer.Dial(u.String(), nil)
	if  err  !=  nil {
		log.Fatal("Dial:", err)
	}

	c.conn  =  conn
}

func (c *wsClient) handleMessages() {
	for {
		messageType, msg, err  :=  c.conn.ReadMessage()
		if  err  !=  nil {
			errText  :=  err.(*websocket.CloseError).Text
			
			var  wsErr  wsError

			errUnmarshal  :=  json.Unmarshal([]byte(errText), &wsErr)
			if  errUnmarshal  !=  nil {
				log.Fatal("error unmarshal:", errUnmarshal)
			}

			if  !wsErr.Reconnect {
				log.Println("Read error:", err)
				return
			}

			c.connectWS()
			continue
	    }

	    // Handle messages
    }
}

func  main() {
	client  :=  wsClient{}

	client.connectWS()
	defer  client.conn.Close()

	go  client.handleMessages()

	quit  :=  make(chan  os.Signal, 10)

	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)

	<-quit
	log.Print("Shutdown client ...")
}
```
#### Python Implementation
```
def  on_close(ws, code, reason):
	// Handle on close event

	start_websocket()

def on_error(ws, error):
    // Handle on error event

    // Trigger on close event
    ws.close()

def  start_websocket():
	url  =  "wss://indodax.com/ws/"
	ws  =  websocket.WebSocketApp(url,
		on_message  =  on_message,
		on_error  =  on_error,
		on_close  =  on_close,
		on_open  =  on_open)
	ws.run_forever(suppress_origin=True)

start_websocket()
```