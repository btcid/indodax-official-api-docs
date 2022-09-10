

**Table of Contents**
- [General API Information](#general-api-information)
- [Error Codes](#error-codes)
- [General Information on Endpoints](#general-information-on-endpoints)
- [Endpoint security type](#endpoint-security-type)
- [SIGNED (TRADE and USER_DATA) Endpoint security](#signed-trade-and-user_data-endpoint-security)
    - [Timing Security](#timing-security)
    - [SIGNED Endpoint](#signed-endpoint-examples-for-post-getinfo)
- [Private API Endpoint](#private-api-endpoint)
    - [ENUM definitions](#enum-definitions)
    - [Private API endpoints](#private-api-endpoints)

## General API Information
* The base endpoint is: **https://indodax.com/tapi**
* All endpoints return either a JSON object or array.
* Data is returned in **descending** order. newest first, oldest last.
* All time and timestamp related fields are in **milliseconds**.

## Error Codes
* Any endpoint can return an ERROR

Sample Payload below:
```javascript
{
    "success": 0,
    "error": "Invalid credentials. API not found or session has expired.",
    "error_code": "invalid_credentials"
}
```

## General Information on Endpoints
* Parameters may be sent in any order.
* All requests must be sent with POST.
* For each request you need to include these variable to make the call valid: method and nonce or timestamp.

    | Parameter | Description | Optional | Example |
    |-|-|-|-|
    |`method`| Specify the method you want to call | no | getInfo|
    |`nonce`| An increment integer. For example if the last request's nonce is 1000, the next request should be 1001 or a larger number. To learn more about [nonce](http://en.wikipedia.org/wiki/Cryptographic_nonce)  | no | 1000

    | Parameter | Description | Optional | Example |
    |-|-|-| - |
    |`method`| Specify the method you want to call | no | getInfo
    |`timestamp`| This parameter should be the millisecond timestamp of when the request was created and sent | no | 1578303960000
    |`recvWindow`| The value should specify the number of millisecond after timestamp where your request is valid. That mean your request still valid if it sent and processed within timestamp and timestamp + recvWindow. Default value is 5000 (milliseconds) | no | 1578303937000
    
## Endpoint Security Type
* API-keys are passed into the Rest API via the `Key`
  header.
* API-keys and secret-keys **are case sensitive**.
* There are 3 different permissions that can be applied to API Key: view, trade and withdraw

    |Permission | Allowed Methods|
    |-|-|
    |view | getInfo, transHistory, tradeHistory, openOrders, orderHistory, getOrder |
    |trade | trade, cancelOrder |
    |withdraw | withdrawFeee, withdrawCoin |
    
## Signed (TRADE and USER_DATA) Endpoint Security
* `SIGNED` endpoints require an additional parameter, `Sign`, to be
  sent in the  `header`.
* Endpoints use `HMAC SHA512` signatures.
  Use your `secretKey` as the key and `totalParams` as the value for the HMAC operation.
* The `signature` is **not case sensitive**.
* `totalParams` is defined as the `query string` concatenated with the
  `request body`. example `*(?param=val&param1=val1) encrypted with method HMAC-SHA512 using secret key*`
  
### Timing Security
* A `SIGNED` endpoint also requires a parameter, `timestamp`, to be sent which
  should be the millisecond timestamp of when the request was created and sent.
* An additional parameter, `recvWindow`, may be sent to specify the number of
  milliseconds after `timestamp` the request is valid for. If `recvWindow`
  is not sent, **it defaults to 5000**.
* The logic is as follows:
  ```php
  if ($timestamp >= ($serverTime + 1000) || ($serverTime - $timestamp) > $recvWindow) {
      // reject request
  } else {
      // process request
  }
  ```
**Serious trading is about timing.** Networks can be unstable and unreliable, which can lead to requests taking varying amounts of time to reach the servers. With `recvWindow`, you can specify that the request must be processed within a certain number of milliseconds or be rejected by the server.

### SIGNED Endpoint Examples for POST getInfo
Here is a step-by-step example of how to send a vaild signed payload from the
Linux command line using. `curl`

| Key | Value
|-|-
| `apiKey` | AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA
| `secretKey` | f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d

| Parameter | Value |
|-|-|
|`method`| getInfo |
|`timestamp`| 1578304294000 |
|`recvWindow`| 1578303937000 |

#### Example : send parameter using request body
* **requestBody:** method=getInfo&timestamp=1578304294000&recvWindow=1578303937000
* **HMAC SHA512 signature:**
    ```
    [linux]$ echo -n "method=getInfo&timestamp=1578304294000&recvWindow=1578303937000" | openssl dgst -sha512 -hmac "f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d"
    (stdin)= bab004e5a518740d7a33b38b44dbebecd3fb39f40b42391af39fcce06edabff5233b3e8064a07c528d1c751a6923d5116026c7786e01b22e2d35277a098cae99
    ```
* **curl command:**
    ```
    (HMAC SHA512)
    [linux]$ curl -H "Key: AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA" -H "Sign: bab004e5a518740d7a33b38b44dbebecd3fb39f40b42391af39fcce06edabff5233b3e8064a07c528d1c751a6923d5116026c7786e01b22e2d35277a098cae99" -X POST 'https://indodax.com/tapi' -d 'method=getInfo&timestamp=1578304294000&recvWindow=1578303937000'
    ```
## Private API Endpoint
### ENUM Definitions
**Parameter method (Method) :**
* getInfo
* transHistory
* trade
* tradeHistory
* openOrders
* orderHistory
* getOrder
* cancelOrder
* withdrawFee
* withdrawCoin
* listDownline
* checkDownline
* createVoucher (Partner Only)

**Transaction type (type):**
* buy
* sell

**Sort by (order):**
* asc
* desc

**Pair to get the information from (pair):**
* btc_idr
* ltc_btc
* doge_btc

**Currency to withdraw (currency):**
* btc
* eth
* doge
* ltc

### Private API Endpoints
All request sent to this endpoint

    POST https://indodax.com/tapi

All request sent with Request header

| Name | Type | Mandatory | Description |
|-|-|-|-|
|`Key`| string | yes | API Key|
|`Sign`| string | yes | Encrypted with method HMAC-SHA512 using secret key. (Request body (?param=val&param1=val1))|

All request sent with Request body

| Name | Type | Mandatory | Description |
|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |
|`timestamp`| timestamp in milisecond | optional when sending request using `nonce` | The millisecond timestamp of when the request was created and sent. Default value is 5000 (milliseconds).|
|`recvWindow`| timestamp in milisecond | no | This parameter is optional when you sending request using timestamp. The value should specify the number of millisecond after timestamp where your request is valid. That mean your request still valid if it sent and processed within timestamp and timestamp + recvWindow. Default value is 5000 (milliseconds).|
|`nonce`|int|optional when sending request using `timestamp`|An increment integer. For example if the last request's nonce is 1000, the next request should be 1001 or a larger number.

#### Get Info Endpoint
This method gives user balances and server's timestamp.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |getInfo| |

Response
```json
{
    "success": 1,
    "return": {
        "server_time": 1578638762,
        "balance": {
            "idr": 0,
            "btc": "0.00000000",
            ...
        },
        "balance_hold": {
            "idr": 0,
            "btc": "0.00000000",
            ...
        },
        "address": {
            "btc": "1GE7CxpQT7zMRrdA5s6U53sHuuBbEijkYc",
            "abyss": "0x4c7d06aa44220ebc321e1e3ad5457d5d6dbd927b"
        },
        "user_id": "00001",
        "name": "Jhon Doe",
        "email": "jhonDoe@mail.com",
        "profile_picture": null,
        "verification_status": "verified",
        "gauth_enable": true
    }
}
```

#### Transaction History Endpoints
This method gives list of deposits and withdrawals of all currencies.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |transHistory||
|`start`| date |optional|Specify the start date of transaction history you want to search |Y-m-d (eg: 2021-07-17)||
|`end`| date |required if start date is set|Specify the end date of transaction history you want to search |Y-m-d (eg: 2021-07-17)||

Response
```json
{
    "success": 1,
    "return": {
        "withdraw": {
            "idr": [
                {
                    "status": "success",
                    "type": "coupon",
                    "rp": "115205",
                    "fee": "500",
                    "amount": "114705",
                    "submit_time": "1539844166",
                    "success_time": "1539844189",
                    "withdraw_id": "1783717",
                    "tx": "BTC-IDR-RDTVVO2P-ETD0EVAW-VTNZGMIR-HTNTUAPI-84ULM9OI",
                    "sender": "boris",
                    "used_by": "viginia88"
                },
                ...
            ],
            "btc": [],
            "abyss": [],
            ...
        },
        "deposit": {
            "idr": [
                {
                    "status": "success",
                    "type": "duitku",
                    "rp": "393000",
                    "fee": "5895",
                    "amount": "387105",
                    "submit_time": "1576555012",
                    "success_time": "1576555012",
                    "deposit_id": "3395438",
                    "tx": "Duitku OVO Settlement"
                },
                ...
            ],
            "btc": [
                {
                    "status": "success",
                    "btc": "0.00118769",
                    "amount": "0.00118769",
                    "success_time": "1539529208",
                    "deposit_id": "3602369",
                    "tx": "c816aeb35a5b42f389970325a32aff69bb6b2126784dcda8f23b9dd9570d6573"
                },
                ...
            ],
            "abyss": [],
            ...
        }
    }
}
```

#### Trade Endpoints
This method is for opening a new order

> ℹ️ **Important Updates**
> 
> As per 10 September 2022,
> - You may experience under filled order if using `idr` parameter when create buy order. To solve this issue, simply send `btc` instead `idr` and use `order_type : "limit"`.
> - You can create buy limit order using coin (eg: `btc`) as amount.
> - You can use `order_type : "market"` to create market order.
> - You can try the new API version by creating account in https://demo-indodax.com. You will receive balance for some coins, approximately 1 minute after successfully sign-up.
> - These coins can be used for trade testing, but can't be withdrawn. You also can't deposit any coin to demo-indodax.com.
>
> ℹ️ **Information**
> 
> We also have a rate limit feature in place for the trade api. If you call the trade api for the same `pair` for more than 10 times in a second you would get the following error and would not be able to trade that `pair` for the next 5 seconds
> ```json
> status code: 429
> content type: application/json
> body: { "message": "Your User ID sent too many trade request for pair BTCIDR, please try again in 5 seconds" }
> ```

\
Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |trade| |
|`pair`|string|yes|Pair to get the information from| btc_idr, ltc_btc, doge_btc, etc| |
|`type`|string|yes|transaction type (buy or sell)|||
|`price`|numeric|required on limit order|order price|buy/sell||
|`idr`|numeric|required for (limit/market) buy order with amount in IDR|amount of rupiah to buy coin|||
|`btc`|numeric|required for limit buy order with amount in coin or sell order|amount of coin to buy/sell|||
|`order_type`|string|optional|type of order|limit/market|limit|

**Notes**
- Request will be rejected if you send BUY order request with both `idr` set & `order_type` set to LIMIT.
- Currently MARKET BUY order only support amount in `idr`.

Sample Payload for limit order using idr amount:
```javascript
{
	"method": "trade"
	"nonce": 4531235
        "idr": 100000,
	"price": 500000,
	"type": "buy"
}
```

*Sample Payload for limit order using coin amount:
```javascript
{
	"method": "trade",
	"nonce": 4531235,
        "btc": 0.001,
	"order_type": "limit",
	"price": 500000,
	"type": "buy"
}
```

*Sample Payload for market order:
```javascript
{
	"method": "trade",
	"nonce": 4531235,
    	"idr": 200000,
    	"order_type": "market",
    	"type": "buy"
}
```

Response
```json
{
    "success": 1,
    "return": {
        "receive_btc": "0.00000000",
        "spend_rp": 0,
        "fee": 0,
        "remain_rp": 5000000,
        "order_id": 59632813
    }
}
```

nb : *effectively per 10 september 2022

#### Trade History Endpoints
This method gives information about transaction in buying and selling history.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |tradeHistory||
|`count`|numeric|no|number of transaction which will be displayed||1000|
|`from_id`|numeric|no|first ID||0|
|`end_id`|numeric|no|end ID||0|
|`order`|string|no|sort by|asc / desc|desc|
|`since`|timestamp|no|start time||unix time|
|`end`|timestamp|no|end time||unix time|
|`pair`|string|yes|Pair to get the information from|btc_idr, ltc_btc, doge_btc, etc|btc_idr|

Response
```json
{
    "success": 1,
    "return": {
        "trades": [
            {
                "trade_id": "17393994",
                "order_id": "59636253",
                "type": "sell",
                "btc": "0.00313482",
                "price": "107202000",
                "fee": "0",
                "trade_time": "1578645297"
            },
            ...
        ]
    }
}
```

#### Open Orders Endpoints
This method gives the list of current open orders (buy and sell).

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |openOrders||
|`pair`|string|no|Pair to get the information from|btc_idr, ltc_btc, doge_btc, etc||

Response 
```json
{
    "success": 1,
    "return": {
        "orders": [
            {
                "order_id": "59639504",
                "submit_time": "1578648363",
                "price": "100207000",
                "type": "buy",
                "order_idr": "33605800",
                "remain_idr": "33605800"
            }
        ]
    }
}
```
Response if pair is not set
```json
{
    "success": 1,
    "return": {
        "orders": {
            "btc_idr": [
                {
                    "order_id": "59639504",
                    "submit_time": "1578648363",
                    "price": "100207000",
                    "type": "buy",
                    "order_idr": "33605800",
                    "remain_idr": "33605800"
                }
            ],
            "npxs_idr": [
                {
                    "order_id": "666883",
                    "submit_time": "1578641963",
                    "price": "2",
                    "type": "sell",
                    "order_npxs": "50000.00000000",
                    "remain_npxs": "50000.00000000"
                }
            ]
        }
    }
}
```

#### Order History
This method gives the list of order history (buy and sell)

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |orderHistory||
|`pair`|string|yes|Pair to get the information from|btc_idr, ltc_btc, doge_btc, etc|btc_idr|
|`count`|int|no||||
|`from`|int|no||||

Response
```json
{
    "success": 1,
    "return": {
        "orders": [
            {
                "order_id": "59639504",
                "type": "buy",
                "price": "100207000",
                "submit_time": "1578648363",
                "finish_time": "1578649332",
                "status": "cancelled",
                "order_idr": "336058",
                "remain_idr": "336058"
            },
            {
                "order_id": "59636253",
                "type": "sell",
                "price": "107202000",
                "submit_time": "1578645288",
                "finish_time": "1578645297",
                "status": "filled",
                "order_btc": "0.00313482",
                "remain_btc": "0.00000000"
            }...
        ]
    }
}
```

#### Get Order Endpoints
Use getOrder to get specific order details.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |getOrder||
|`pair`|string|yes|Pair to get the information from|btc_idr, ltc_btc, doge_btc, etc|btc_idr|
|`order_id`|int|yes|Order ID|||

Response
```json
{
    "success": 1,
    "return": {
        "order": {
            "order_id": "59639504",
            "price": "100207000",
            "type": "buy",
            "order_rp": "336058",
            "remain_rp": "336058",
            "submit_time": "1578648363",
            "finish_time": "1578649332",
            "status": "cancelled"
        }
    }
}
```

#### Cancel Order Endpoints
This method is for canceling an existing open order.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |cancelOrder||
|`pair`|string|yes|Pair to get the information from|btc_idr, ltc_btc, doge_btc, etc|btc_idr|
|`order_id`|int|yes|Order ID|||
|`type`|int|yes|Transaction type|buy / sell||

Response
```json
{
    "success": 1,
    "return": {
        "order_id": 666883,
        "type": "sell",
        "pair": "btc_idr",
        "balance": {
            "idr": "33605800",
            "btc": "0.00000000",
            ...
            "frozen_idr": "0",
            "frozen_btc": "0.00000000",
            ...
        }
    }
}
```

#### Withdraw Fee Endpoints
This method is for check withdraw fee

To be able to use this method you need to enable withdraw permission when you generate the API Key. Otherwise you will get “No permission” error. 

Request Body

| Name | Type | Mandatory | Description | Value | default | 
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |withdrawFee||
|`currency`|string|yes|Currency for check withdraw fee |btc, ltc, doge, eth, etc||

Response
```json
{
    "success": 1,
    "return": {
        "server_time": 1607923272,
        "withdraw_fee": 0.005,
        "currency": "eth"
    }
}
```

#### Withdraw Coin Endpoints
This method is for withdrawing assets (except IDR).

To be able to use this method you need to enable withdraw permission when you generate the API Key. Otherwise you will get “No permission” error. 

You also need to prepare a Callback URL. Callback URL is a URL that our system will call to verify your withdrawal requests. Various parameters will be sent to Callback URL, make sure to check this information on your server side. If all the data is correct, print out a string “ok”  (without quotes). We will continue the request if only we receive “ok” (without quotes) response, otherwise the request will be failed. 

Callback call will be sent through a POST request, with 5 seconds connection timeout.


Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |withdrawCoin||
|`currency`|string|yes|Currency to withdraw|btc, ltc, doge, eth, etc||
|`network`|string|yes|Currency network if exist|erc20, trc20, bep2, bep20||
|`withdraw_address`|string|yes|Receiver address|a valid address||
|`withdraw_amount`|numeric|yes|Amount to send|||
|`withdraw_memo`|string|no|Memo to be sent to the receiver, if supported by the asset platform. Exchanges use this memo for accepting deposits for certain assets.Example: Destination Tag (for Ripple)Message (for NXT)Memo (for BitShares)|a valid memo/message/destination tag||
|`request_id`|alphanumeric max 255 char|yes|Custom string you need to provide to identify each withdrawal request.|request_id will be passed to callback call so your system can identify the request.d|||

Response
```json
{
    "success": 1,
    "status": "approved",
    "withdraw_currency": "doge",
    "withdraw_address": "D9iCdBLBosJzGSvpQGMSobwtdgB2rS1zam",
    "withdraw_amount": "10.00000000",
    "fee": "5.00000000",
    "amount_after_fee": "5.00000000",
    "submit_time": "1578909560",
    "withdraw_id": "doge-1941965",
    "txid": ""
}
```

Callback Parameter Sent to Client

|Parameter|Description|
|-|-|
|request_id|request_id from your request|
|withdraw_currency|currency from your request|
|withdraw_address|withdraw_address from your request|
|withdraw_amount|withdraw_amount from your request|
|withdraw_memo|withdraw_memo from your request (if any)|
|requester_ip|requester_ip of the request|
|request_date|time the request submitted |

#### List Downline Endpoints
This method is for list all downline in current user

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |listDownline||
|`page`| int |yes|Set the page you want to show |1||
|`limit`| int |yes|Set how many data you want to show |10|200|

Response
```json
{
  "success": 1,
  "return": {
    "curr_page": 1,
    "total_page": 102,
    "total_data_per_page": 2,
    "total": 203,
    "data": [
      {
        "name": "btc users",
        "username": "btcusers",
        "registration_date": "9-Jun-20 15:54",
        "email_verified": true,
        "id_verified": true,
        "level": "n/a",
        "end": "n/a",
        "start": "n/a"
      },
      {
        "name": "idx users",
        "username": "idxusers",
        "registration_date": "8-Jun-20 15:51",
        "email_verified": true,
        "id_verified": false,
        "level": "n/a",
        "end": "n/a",
        "start": "n/a"
      }
    ]
  }
}
```

#### Check Downline Endpoints
This method is for check wheter email exists in current user downline or not 
return is 1 or 0.
1 means this email is exists in current user downline
0 means email doesn't exists in current user downline

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |listDownline||
|`email`|string|yes|Email want to check|btc@gmail.com, idx@yahoo.com||

Response
```json
{
    "success": "1",
    "is_downline": "0"
}
```

#### Create Voucher Endpoints
This method used to generate Indodax voucher programmatically. To be able to use this method you need to be a partner and sign official aggreement between you and Indodax.

Request Body

| Name | Type | Mandatory | Description | Value | default |
|-|-|-|-|-|-|
|`method`| string |yes|Specify the method you want to call |createVoucher||
|`amount`|number|yes|The voucher value in rupiah|Minimum 1000||
|`to_email`|strinng|yes|The recipient email address which registered in Indodax|e.g. idx@gmail.com||

Response
```json
{
    "success": 1,
    "withdraw_id": 1234,
    "rp": 10000,
    "submit_time": "1578909560",
    "voucher": "BTC-IDR-XXXX"
}
```
