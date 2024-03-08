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
		"channel": "pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e"
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
| Production       	| wss://pws.indodax.com/ws/?cf_ws_frame_ping_pong=true   		| Access for production							|
| Demo             	| wss://pws.demo-indodax.com/ws/?cf_ws_frame_ping_pong=true  	| Access for staging									|

Connect using Environment Base URL.

### Authentication
Send request message with field `id` and `token` you get from [Generate Private Token and Private Channel](#generate-private-token-and-private-channel).

Request Message:

```json
{"connect":{"token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIzMWFlMGUzYjU1NzNlMzI1YTU1MGM1ZDg2MGM1YjQ3ZDQyYjdkZjMxIiwiZXhwIjoxNzA1NzE4NjEyfQ.T1dymQ7dGS4sASLlZC3VE4JTjdvdsasJSsadaK"},"id": 1}
```

#### Success Response

```json
{
    "id": 1,
    "connect": {
        "client": "0f812879-fc64-482f-a220-eb1860c71189",
        "version": "5.2.0",
        "expires": true,
        "ttl": 86133
    }
}
```

#### Expired Token Response

```json
{
    "id": 1,
    "error": {
        "code": 109,
        "message": "token expired"
    }
}
```

When your token has expired, you will need to generate a new token. 

### Subscribing to Private Channel

You can **subscribe** to **private channel** using `channel` you get from [Generate Private Token and Private Channel](#generate-private-token-and-private-channel).

### Order Update Event

Request Message:

```json
{"subscribe":{"channel":"pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e"},"id":2}
```

#### Success Response
Response New Order:
```json
{
  "push": {
    "channel": "pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e",
    "pub": {
      "data": [
        {
          "eventType": "order_update",
          "order": {
            "orderId": "aaveidr-limit-3397",
            "symbol": "aaveidr",
            "side": "BUY",
            "origQty": "0.00996909",
            "unfilledQty": "0.00996909",
            "executedQty": "0",
            "price": "2000000",
            "description": "AAVE/IDR",
            "status": "NEW",
            "transactionTime": 1705635775203,
            "clientOrderId": "lendidr-limit-3397"
          }
        }
      ]
    }
  }
}
```

Response Fills Order:
```json
{
  "push": {
    "channel": "pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e",
    "pub": {
      "data": [
        {
          "eventType": "order_update",
          "order": {
            "orderId": "aaveidr-limit-3397",
            "tradeId": "144332127176055681",
            "symbol": "aaveidr",
            "side": "BUY",
            "origQty": "0.00996909",
            "unfilledQty": "0",
            "executedQty": "0.00996909",
            "price": "2000000",
            "description": "AAVE/IDR",
            "status": "FILL",
            "transactionTime": 1705635775203,
            "fillInformation": {
              "participant": "TAKER",
              "fillQty": "0.00996909",
              "feeAsset": "idr",
              "feeRate": 0.002,
              "fee": "39",
              "taxAsset": "idr",
              "taxRate": 0.0011,
              "tax": "21"
            }
          }
        }
      ]
    }
  }
}
```

Response Cancel Order:
```json
{
  "push": {
    "channel": "pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e",
    "pub": {
      "data": [
        {
          "eventType": "order_update",
          "order": {
            "orderId": "aaveidr-limit-3399",
            "symbol": "aaveidr",
            "side": "SELL",
            "origQty": "0.05015045",
            "unfilledQty": "0.05015045",
            "executedQty": "0",
            "price": "2000000",
            "description": "AAVE/IDR",
            "status": "CANCELLED",
            "transactionTime": 1705636274643,
            "clientOrderId": "lendidr-limit-3399"
          }
        }
      ]
    }
  }
}
```

Response Done Order:
```json
{
  "push": {
    "channel": "pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e",
    "pub": {
      "data": [
        {
          "eventType": "order_update",
          "order": {
            "orderId": "aaveidr-limit-3397",
            "symbol": "aaveidr",
            "side": "BUY",
            "origQty": "0.00996909",
            "unfilledQty": "0",
            "executedQty": "0.00996909",
            "price": "2000000",
            "description": "AAVE/IDR",
            "status": "DONE",
            "transactionTime": 1705635775203,
            "clientOrderId": "lendidr-limit-3397"
          }
        }
      ]
    }
  }
}
```

Response Rejected (FOK) Order:
```json
{
  "push": {
    "channel": "pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e",
    "pub": {
      "data": [
        {
          "eventType": "order_update",
          "order": {
            "orderId": "aaveidr-limit-3397",
            "symbol": "aaveidr",
            "side": "BUY",
            "origQty": "0.00996909",
            "unfilledQty": "0",
            "executedQty": "0.00996909",
            "price": "2000000",
            "description": "AAVE/IDR",
            "status": "REJECTED",
            "transactionTime": 1705635775203,
            "clientOrderId": "lendidr-limit-3397"
          }
        }
      ]
    }
  }
}
```

#### Failed Response
When you subscribe to wrong `channel`, the response will failed as below.

```json
{
    "id": 2,
    "error": {
        "code": 103,
        "message": "permission denied"
    }
}
```