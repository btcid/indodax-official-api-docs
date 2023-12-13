# Private WebSocket Documentation

## Table of Contents

- [Request Private Token and Private Channel](#request-private-token-and-private-channel)
	- [General Information](#general-information)
	- [Generate Private Token and Private Channel](#generate-private-token-and-private-channel)
- [Private WebSocket](#private-websocket)
	- [Authentication](#authentication)
	- [Subscribing to Private Channel](#subscribing-to-private-channel)
		- [Order Update Event](#order-update-event)


## Request Private Token and Private Channel

### General Information

#### Base URL
| **Environment**  	| **Base URL**              									| **Description**																											|
| -----------------	| ------------------------------------------- | ------------------------------------------------------------------- |
| Production       	| https://indodax.com/ , https://btcapi.net   | Access for production. For https://btcapi.net need to be whitelist	|
| Demo             	| https://demo-indodax.com/  	 								|	Access for demo.																										|

#### Request Header
| **Parameter**  	| **Type** 	| **Mandatory**	| **Description**			|
| --------------	| --------- | ------------- |-------------------- |
| `Sign`      		| string 		| yes 					| `Sign` Encrypted with method `HMAC-SHA512` using `tapi secret key`. (Request body (?param=val&param1=val1))   |

### Generate Private Token and Private Channel
Provide private token and private channel to use in WebSocket

#### PATH
```
POST {base_url}/api/private_ws/v1/generate_token
```

#### Request Body
| **Parameter**  		| **Type** 	| **Mandatory**	| **Description**		| **Default** |
| ----------------	| --------- | ------------- |------------------ | ----------- |
| `client` 					| string 		| yes 					| `tapi`						| `tapi`			|
| `tapi_key` 				| string 		| yes	 					| API Key						| 						|

#### Response Body
| **Parameter**  		| **Type** 	| **Description**																								|
| -----------------	| --------- | ------------------------------------------------------------- |
| success 					| int				| if success=1 and failed=0 																		|
| return						| object		| 																															|
| return.connToken 	| string		| Use to established connection and verify user private channel |
| return.channel		| string		| private channel to subscribe 																	|
| error							| string		| error message of request 																			|
| error_code				| string		| type of error  																								|

**Success Response**
```json
{
	"success": 1,
	"return": {
		"connToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMTIifQ.D4YxclnNWCRH6w47MNV9XcbpKT4aLrMboLg6DzCwm1M",
		"channel": "pws:#31ae0e3b5573e325a550c5d860c5b47d42b7df31"
	}
}
```

**Failed Response**
```json
{
    "success": 0,
    "error": "Invalid TAPI key",
    "error_code": "invalid_tapi_key"
}
```
#### Error Code
| **error_code**  		| **Error**              										| **Description**									|
| -----------------		| ------------------------------- 								| --------------- 									|
| invalid_tapi_key 		| Invalid TAPI key  											| `tapi_key` not properly sent or empty `tapi_key`. |
| invalid_credentials 	| Invalid credentials. API not found or session has expired.	| Invalid `tapi_key` or doesn't exist. 				|
| bad_sign			 	| Invalid credentials. Bad sign.								| Invalid `tapi_secret` to Encrypt Sign request 	|



## Private WebSocket

| **Environment**  	| **Base URL**              			| **Description**	|
| -----------------	| ------------------------------- | --------------- |
| Production       	| wss://ws3.indodax.com/ws/   		| 								|
| Demo             	| wss://ws.demo-indodax.com/ws/  	|									|

Connect using Environment Base URL.

### Authentication
Send request message with field `id` and `token` you get from [Generate Private Token and Private Channel](#generate-private-token-and-private-channel).

Request Message:

```json
{
    "params": {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMTIifQ.D4YxclnNWCRH6w47MNV9XcbpKT4aLrMboLg6DzCwm1M"
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

### Subscribing to Private Channel

You can **subscribe** to **private channel** using `channel` you get from [Generate Private Token and Private Channel](#generate-private-token-and-private-channel).

### Order Update Event

Request Message:

```json
{
    "method": 1,
    "params": {
        "channel": "pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e"
    },
    "id": 2
}
```

Response New Order:
```json
{
	"result": {
		"channel": "pws:#31ae0e3b5573e325a550c5d860c5b47d42b7df31",
		"data": {
			"data": [
				{
					"event_type": "order_update",
					"order": {
						"orderId": "vcgidr-limit-93",
						"symbol": "vcgidr",
						"side": "BUY",
						"origQty": "10000",
						"unfilledQty": "10000",
						"executedQty": "0",
						"price": "1",
						"pairRebrand": "vcgidr",
						"description": "VCG/IDR",
						"status": "NEW",
						"transactionTime": 1698130393714
					}
				},
				{
					"event_type": "order_update",
					"order": {
						"orderId": "vcgidr-limit-94",
						"symbol": "vcgidr",
						"side": "BUY",
						"origQty": "10000",
						"unfilledQty": "10000",
						"executedQty": "0",
						"price": "1",
						"pairRebrand": "vcgidr",
						"description": "VCG/IDR",
						"status": "NEW",
						"transactionTime": 1698130393720
					}
				}
			],
			"offset": 1
		}
	}
}
```

Response Fills Order:
```json
{
	"result": {
		"channel": "pws:#31ae0e3b5573e325a550c5d860c5b47d42b7df31",
		"data": {
			"data": [
				{
					"event_type": "order_update",
					"order": {
						"orderId": "btcidr-stoplimit-399",
						"symbol": "btcidr",
						"side": "BUY", //BUY OR SELL
						"origQty": "236.15269175",
						"unfilledQty": "227.73699863",
						"executedQty": "8.41569312",
						"price": "394800000",
						"pairRebrand": "btcidr",
						"description": "BTC/IDR",
						"status": "FILL",
						"transactionTime": 1698130393714,
						"fillInformation": {
							"participant": "MAKER", //MAKER OR TAKER
							"fillQty": "0.0001",
							"feeAsset": "idr",
							"feeRate": 0.001,
							"fee": "40",
							"taxAsset": "idr",
							"taxRate": 0.001,
							"tax": "39"
						}
					}
				}
			],
			"offset": 1
		}
	}
}
```

Response Cancel Order:
```json
{
	"result": {
		"channel": "pws:#31ae0e3b5573e325a550c5d860c5b47d42b7df31",
		"data": {
			"data": [
				{
					"event_type": "order_update",
					"order": {
						"orderId": "elfidr-limit-504",
						"symbol": "elfidr",
						"side": "BUY", //BUY OR SELL
						"origQty": "10000",
						"unfilledQty": "10000",
						"executedQty": "0",
						"price": "1",
						"pairRebrand": "elfidr",
						"description": "ELF/IDR",
						"status": "CANCELLED",
						"transactionTime": 1698130393714
					}
				}
			],
			"offset": 1
		}
	}
}
```

Response Done Order:
```json
{
	"result": {
		"channel": "pws:#31ae0e3b5573e325a550c5d860c5b47d42b7df31",
		"data": {
			"data": [
				{
					"event_type": "order_update",
					"order": {
						"orderId": "mkridr-limit-91",
						"symbol": "mkridr",
						"side": "BUY", //BUY OR SELL
						"origQty": "0.00058623",
						"unfilledQty": "0",
						"executedQty": "0.00058623",
						"price": "17005000",
						"pairRebrand": "mkridr",
						"description": "MKR/IDR",
						"status": "DONE",
						"transactionTime": 1698130393714
					}
				}
			],
			"offset": 1
		}
	}
}
```

Response Rejected (FOK) Order:
```json
{
	"result": {
		"channel": "pws:#31ae0e3b5573e325a550c5d860c5b47d42b7df31",
		"data": {
			"data": [
				{
					"event_type": "order_update",
					"order": {
						"orderId": "ankridr-market-1133",
						"symbol": "ankridr",
						"side": "BUY", //BUY OR SELL
						"origQty": "20",
						"unfilledQty": "20",
						"executedQty": "0",
						"price": "0",
						"pairRebrand": "ankridr",
						"description": "ANKR/IDR",
						"status": "REJECTED",
						"transactionTime": 1698130393714
					}
				}
			],
			"offset": 1
		}
	}
}
```