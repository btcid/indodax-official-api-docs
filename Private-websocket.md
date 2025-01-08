# Private WebSocket Documentation

## Table of Contents

- [Request Private Token and Private Channel](#request-private-token-and-private-channel)
	- [General Information](#general-information)
	- [Generate Private Token and Private Channel](#generate-private-token-and-private-channel)
- [Private WebSocket](#private-websocket)
	- [Authentication](#authentication)
	- [Subscribing to Private Channel](#subscribing-to-private-channel)
		- [Order Update Event](#order-update-event)
- [Troubleshooting](#troubleshooting)
	- [Establishing WebSocket Connection](#establishing-websocket-connection)
	- [Disconnected Client Issue](./Marketdata-websocket.md#disconnected-client-issue)
  - [Connection Expired](#connection-expired)



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
            "orderId": "aaveidr-limit-13525",
            "tradeId": "72057594037968281",
            "symbol": "aaveidr",
            "side": "BUY",
            "origQty": "0.0046462",
            "unfilledQty": "0",
            "executedQty": "0.0046462",
            "price": "4289899",
            "description": "AAVE/IDR",
            "status": "FILL",
            "transactionTime": 1734490232679,
            "fillInformation": {
              "participant": "TAKER",
              "filledQty": "0.0046462",
              "qty": "0.0046462",
              "feeAsset": "idr",
              "feeRate": 0.002,
              "fee": "39",
              "taxAsset": "idr",
              "taxRate": 0.0012,
              "tax": "23",
              "clearingAsset": "idr",
              "clearingRate": 2.22E-4,
              "clearing": "4"
            },
            "clientOrderId": "lendidr-limit-13525"
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
# Troubleshooting
## Establishing WebSocket Connection

### Issue Description
When establishing a WebSocket connection, regardless of the programming language used (Python, Go, PHP, etc.), the default behavior of the client library often sets the `Origin` header to a value that doesn't match, resulting in the client receiving a 403 Forbidden error response.

### Solution
To address this issue, it's essential to ensure that the `Origin` header sent during the WebSocket handshake matches the expected value or is removed altogether.

#### Python Solution (as per provided code)
For Python, using the `websocket-client` library, the solution involves setting the `suppress_origin=True` option when calling `pws.run_forever()`. This removes the `Origin` header from the WebSocket handshake request. Reference:  [websocket-client](https://websocket-client.readthedocs.io/en/latest/examples.html#suppress-origin-header)


#### Go Solution
For Go, when using libraries like `gorilla/websocket`, you can set the `Origin` header manually or remove it altogether when establishing the WebSocket connection.

#### PHP Solution
For PHP, utilizing an unset() if isset() header origin exist.

### Implementation

#### Python

```python
def start_websocket():
    global pws
    url = "wss://pws.indodax.com/ws/?cf_ws_frame_ping_pong=true"
    pws_instance = PWS()
    pws = websocket.WebSocketApp(url,
                                on_message=pws_instance.on_message,
                                on_error=pws_instance.on_error,
                                on_close=pws_instance.on_close,
                                on_open = pws_instance.on_open)
    pws.run_forever(suppress_origin=True)
```
#### Golang
```go
func main() {
    u := url.URL{Scheme: "wss", Host: "pws.indodax.com", Path: "/ws/", RawQuery: "cf_ws_frame_ping_pong=true"}

    c, _, err := websocket.DefaultDialer.Dial(u.String(), nil)
    if err != nil {
        log.Fatal("Error connecting to WebSocket:", err)
    }
    defer c.Close()
}
```

#### PHP
```php
if (isset($options['headers']['Origin'])) {
    unset($options['headers']['Origin']);
}
```

## Connection Expired
### Description
A token is required to connect to our private WebSocket and is valid for only 24 hours. Clients should request a new token each time they connect and continue to request it regularly.
### Solution
This is sample to request a new token
### Implementation
#### Javascript Implementation
```
const crypto = require("crypto");

function generateHMAC(requestBody, secret) {
  const key = Buffer.from(secret, "utf-8");
  const hmac = crypto.createHmac("sha512", key);
  hmac.update(requestBody, "utf-8");
  return hmac.digest("hex");
}

async function RequestToken() {
  const url = "https://indodax.com/api/private_ws/v1/generate_token";
  const secret = <tapi_secret>;
  const key = <tapi_key>;

  const requestBody = `client=tapi&tapi_key=${key}`;

  const sign = generateHMAC(requestBody, secret);

  const opt = {
    method: "POST",
    headers: {
      "Content-Type": "application/x-www-form-urlencoded",
      Sign: sign,
    },
    body: requestBody,
  };

  let response;
  try {
    response = await fetch(url, opt);
  } catch (error) {
    throw new Error(error.message);
  }

  const responseData = await response.json()

  return responseData
}

RequestToken()
  .then((data) => {
    console.log("token:", data.return.connToken);
    console.log("channel:", data.return.channel)
  })
  .catch((error) => {
    console.log(error);
  });
```
#### Go Implementation
```
package main

import (
	"crypto/hmac"
	"crypto/sha512"
	"encoding/hex"
	"encoding/json"
	"fmt"
	"io/ioutil"
	"net/http"
	"strings"
)

type PwsToken struct {
	ConnToken string `json:"connToken"`
	Channel   string `json:"channel"`
}

type Response struct {
	Success int      `json:"success"`
	Token   PwsToken `json:"return"`
}

func generateHMAC(requestBody, secret string) string {
	key := []byte(secret)
	h := hmac.New(sha512.New, key)
	h.Write([]byte(requestBody))
	return hex.EncodeToString(h.Sum(nil))
}

func sendRequest(url, method, requestBody, sign string) (Response, error) {
	payload := strings.NewReader(requestBody)
	client := &http.Client{}
	req, err := http.NewRequest(method, url, payload)
	if err != nil {
		return Response{}, nil
	}

	req.Header.Add("Sign", sign)
	req.Header.Add("Content-Type", "application/x-www-form-urlencoded")

	res, err := client.Do(req)
	if err != nil {
		return Response{}, nil
	}
	defer res.Body.Close()

	body, err := ioutil.ReadAll(res.Body)
	if err != nil {
		return Response{}, err
	}

	var response Response

	err = json.Unmarshal(body, &response)
	if err != nil {
		return Response{}, nil
	}

	return response, nil
}

func main() {
	url := "https://indodax.com/api/private_ws/v1/generate_token"
	method := "POST"
	secret := <tapi_secret>
	key := <tapi_key>

	requestBody := fmt.Sprintf("client=tapi&tapi_key=%s", key)

	sign := generateHMAC(requestBody, secret)

	res, err := sendRequest(url, method, requestBody, sign)
	if err != nil {
		fmt.Println(err)
		return
	}

	fmt.Println("token", res.Token.ConnToken)
	fmt.Println("channel", res.Token.Channel)
}
```
#### Python Implementation
```
import hmac
import hashlib
import requests

def generate_hmac(request_body, secret):
    key = secret.encode('utf-8')
    hmac_obj = hmac.new(key, request_body.encode('utf-8'), hashlib.sha512)
    return hmac_obj.hexdigest()

def request_token():
    url = 'https://indodax.com/api/private_ws/v1/generate_token'
    secret = <tapi_secret>
    key = <tapi_key>

    request_body = f'client=tapi&tapi_key={key}'
    sign = generate_hmac(request_body, secret)

    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Sign': sign
    }

    try:
        response = requests.post(url, headers=headers, data=request_body)
        response.raise_for_status()
    except requests.RequestException as e:
        raise SystemExit(e)

    response_data = response.json()
    return response_data

try:
    data = request_token()
    print("token:", data['return']['connToken'])
    print("channel:", data['return']['channel'])
except Exception as e:
    print("Error:", e)
```