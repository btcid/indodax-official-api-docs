**Table of Contents**
- [Overview](#overview)
- [General API Information](#general-api-information)
    - [Request Header](#request-header)
    - [Response Body](#response-body)
    - [Error Codes](#error-codes)
        - [Sample Error Response](#sample-error-response)
        - [Trade API Error Codes](#trade-api-error-codes)
    - [General Information on Endpoints](#general-information-on-endpoints)
    - [Endpoint Security Type](#endpoint-security-type)
        - [API Key Usage](#api-key-usage)
        - [Signed (TRADE and USER_DATA) Endpoint Security](#signed-trade-and-user_data-endpoint-security)
        - [Timing Security](#timing-security)
        - [SIGNED Endpoint Examples for GET /api/v2/myTrades](#signed-endpoint-examples-for-get-apiv2mytrades)
- [Order History](#order-history)
- [Trade History](#trade-history)

## Overview
Previously, [orderHistory](./Private-RestAPI.md#order-history) and [tradeHistory](./Private-RestAPI.md#trade-history-endpoints) parameter methods were accessed through the single base endpoint `/tapi`.

As of `Jan 20, 2026` these endpoints now have dedicated REST endpoints for improved stability and performance as follows:

| Endpoint | Purpose |
|----------|---------|
| GET `/api/v2/order/histories` | To retrieve the **account Order History** |
| GET `/api/v2/myTrades` | To retrieve the **account Trade History** |

## 🔑 Key Improvements

- **Dedicated endpoints for Order History and Trade History** to improve Trade API (TAPI) stability.
- **Improved Trade ID consistency** between the Private WebSocket (PWS) and REST API.
- **Simplified and standardized parameters** for better clarity and usability.
- **Updated default values and limits** to enhance overall reliability.

    > ℹ️ Important Information
    > - Users of the old `/tapi` endpoint **must migrate to these new endpoint**s for Order & Trade History queries.
    > - The old endpoint will **continue to function** for historical trades and orders, but is scheduled for decommission on `Mar 23, 2026`.

## General API Information
* **The base endpoint is:** 
    - **https://tapi.indodax.com**
    - **https://tapi.btcapi.net**
* All endpoints return **JSON objects or arrays**
* Data results are sorted in **descending order** → `newest first`, `oldest last`
* All **timestamps** and **time-related fields** are in **milliseconds (ms)**

### Request Header
| Key | Type | Mandatory | Value |
|-----|------|-----------|-------------------|
| `Accept` | string | yes | Must be `application/json` |
| `Content-Type` | string | yes | Must be `application/json` |
| `X-APIKEY` | string | yes | User’s API key |
| `Sign` | string | yes | SIGNED endpoint (must be HMAC-SHA512 encrypted using secret key on query string)<br><br> e.g., `?param=value&param1=value1` |

### Response Body
| Key | Type | Value |
|-----|------|-------|
| data | JSON | Response data |
| code | int | Error code |
| error | string | Error message |

### Error Codes
- Any endpoint can return an ERROR

#### Sample Error Response:
```JSON
{
    "code": 1109,
    "error": "Invalid Symbol"
}
```

#### Trade API Error Codes
Error codes are grouped by category:

**1. General Server / Network Issues (10xx)**

| HTTP Status | Code |Error Description |
|-------------|------|------------------|
| 503 Internal Server Error | 1000 | Internal error<br> Unable to process your request. Please try again.|
| 401 Unauthorized | 1001 |Invalid credentials. API not found or session expired. Please generate a new key. |
| 400 Bad Request | 1002 | Invalid timestamp.<br> Timestamp outside `recvWindow`. |
| 400 Bad Request | 1003 | Invalid nonce.<br> Nonce must be greater than `last_nonce`. |

**2. Request Issues (11xx)**
| HTTP Status | Code |Error Description |
|-------------|------|------------------|
| 400 Bad Request | 1101 | Sign not found in header. |
| 400 Bad Request | 1102 | API key not found in header.|
| 403 Forbidden | 1103 | Trade API is disabled. |
| 403 Forbidden | 1104 | Account locked. |
| 403 Forbidden | 1105 | Account disabled. |
| 403 Forbidden | 1106 | No permission. |
| 400 Bad Request | 1107 | Request header fields too large. |
| 400 Bad Request | 1108 | Mandatory parameter not sent, empty/null, or malformed.<br> e.g.,`Mandatory parameter '%s' was not sent Param '%s' or '%s' must be sent` |
| 400 Bad Request | 1109 | Invalid parameter value. |
| 400 Bad Request | 1110 | Invalid symbol. |

### General Information on Endpoints

* Parameters must be sent as `query strings`
* Each request must include a `nonce` or `timestamp` parameter to be valid

#### Request Parameters
| Parameter | Mandatory | Description | Value |
|----------|-----------|-------------|-------|
| `nonce` | no | An increment integer (learn more about [nonce](http://en.wikipedia.org/wiki/Cryptographic_nonce)). | Must be greater than the nonce of the last request.<br><br> e.g., if the last request's nonce is `1000`, the next request should be `1001` or greater. |

| Parameter | Mandatory | Description | Value |
|----------|-----------|-------------|-------|
| `timestamp` | no | Millisecond timestamp when the request was generated. | Timestamp in milliseconds (ms) when the request was created and sent. |
| `recvWindow` | no | Validity window for request in milliseconds (ms). | Default: `5000 ms`<br><br> The value should specify the number of milliseconds after the timestamp where your request is valid.<br><br> e.g., your request is still valid if it is sent and processed within the `timestamp` and the `timestamp + recvWindow`. |

### Endpoint Security Type
#### API Key Usage
- API-keys are passed into the Rest API via the `X-APIKEY` header.
- API-keys and secret-keys are **case sensitive**.

#### Signed (`TRADE` and `USER_DATA`) Endpoint Security
- `SIGNED` endpoints require an additional `signature` parameter sent in the `query string` or `request body`.
- The `signature` must be sent to the REST API in the `Sign` header.
- The `signature` value itself is **not case sensitive**
- `totalParams` is defined as the `query string` concatenated with the `request body`.
e.g., `*(?param=val&param1=val1) encrypted with method HMAC-SHA512 using secret key*`

#### Timing Security
- A `SIGNED` endpoint also requires a `timestamp` parameter defined as the **millisecond timestamp** at which the request was created and sent.
- An optional `recvWindow` parameter may be sent to specify the validity duration (in milliseconds) of the request after the `timestamp`. If `recvWindow` is not provided, it defaults to `5000` ms.
- The logic is as follows:
    ```bash
    if (( timestamp >= serverTime + 1000 || serverTime - timestamp > recvWindow )); then
        # Reject request
        echo "Request rejected"
    else
        # Process request
        echo "Request processed"
    fi
    ```
    > ℹ️  **Notes**:
    >
    > `Serious trading is about timing`. Networks can be unstable and unreliable, which can lead to requests taking varying amounts of time to reach the servers. 
    >
    > With `recvWindow` parameter, user can specify that the request must be processed within a certain number of milliseconds or be rejected by the server.

#### SIGNED Endpoint Examples for GET /api/v2/myTrades
Step-by-step example on how to send a valid signed payload from the Linux command line using `curl`:

| Key | Value |
|-----|-------|
| apiKey | AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA |
| secretKey | f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d |

| Parameter | Value |
|-----------|-------|
| symbol | btcidr |
| limit | 100 |
| timestamp | 1578304294000 |
| recvWindow | 1578303937000 |

#### Example: 
- Send parameter using query string
    - **queryString:** 
        symbol=btcidr&limit=100&timestamp=1578304294000&recvWindow=1578303937000
    - **HMAC SHA512 signature:**
        ```bash
        [linux]$ echo -n "symbol=btcidr&limit=100&timestamp=1578304294000&recvWindow=1578303937000" | openssl dgst -sha512 -hmac "f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d"
        (stdin)= bab004e5a518740d7a33b38b44dbebecd3fb39f40b42391af39fcce06edabff5233b3e8064a07c528d1c751a6923d5116026c7786e01b22e2d35277a098cae99
        ```
    - **curl command:**
        ```bash
        (HMAC SHA512)
        [linux]$ curl -H "X-APIKEY: AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA" -H "Sign: bab004e5a518740d7a33b38b44dbebecd3fb39f40b42391af39fcce06edabff5233b3e8064a07c528d1c751a6923d5116026c7786e01b22e2d35277a098cae99" -X GET 'https://tapi.btcapi.net/api/v2/myTrades?symbol=btcidr&limit=100&timestamp=1578304294000&recvWindow=1578303937000'
        ```

## Order History
```
GET /api/v2/order/histories
```
### Description
This REST endpoint serves to retrieve an account’s order history for a specific trading pair symbol by returning all orders placed by the account within the specified timeframe. Each order history contains:

- `orderId` and `clientOrderId`
- Trading pair symbol
- Order price and side of the order
- Original and executed quantities
- Order type and status (`FILLED`, `CANCELLED`, `REJECTED`)
- Timestamps for submission and completion

### Parameters
| Name | Mandatory | Description | Value | Default |
|------|-----------|-------------|-------|---------|
| symbol | yes | Trading pair symbol | e.g., btcidr, ethidr, etc | |
| startTime | no | Start of query range (Timestamp) | Unix in milliseconds (UTC)<br><br>e.g., 1723442692520 |Last 24 hours |
| endTime | no | End of query range (Timestamp) |Unix in milliseconds (UTC)<br><br> e.g., 1723442692520 | Last 24 hours |
| limit | no | Number of orders to be returned for display | Allowed range: min. 10, max. 1000 | 100 |
| sort | no | Sorting order for returned data | **asc or desc** | desc |

> ℹ️  Notes
> 
> 1. **Default Range:** If `startTime` and `endTime` are not set, the date range defaults to the last 24 hours.
> 2. **Maximum Range:** The interval between `startTime` and `endTime` cannot exceed 7 days.
> 3. **Supported Parameter Combinations:**
>
>       - `symbol`
>       - `symbol` + `startTime`
>       - `symbol` + `endTime`
>       - `symbol` + `startTime` + `endTime`

### Sample Response Body
```json
{
  "data": [
    {
      "orderId": "aaveidr-limit-3568",  // the order id
      "clientOrderId": "clientx-2",     // the client order id
      "symbol": "aaveidr",              // symbol
      "side": "SELL",                   // side of the order, BUY or SELL
      "type": "LIMIT",                  // order type
      "status": "FILLED",               // order status,FILLED|CANCELLED|REJECTED
      "price": "1564455",               // order price
      "oriQty": "0.1",                  // original order quantity
      "executedQty": "0.1",             // executed order quantity
      "submitTime": 1723442692520,      // time of the order submitted
      "finishTime": 1723442692520       // time of the order finish
    }
  ]
}
```

## Trade History
```
GET /api/v2/myTrades
```
### Description
This REST endpoint serves to retrieve an account’s trade execution history for a specific trading pair symbol by returning each trade fill executed from orders placed by the account. Each trade history includes:

- `tradeId`, `orderId`, and `clientOrderId`
- Trading pair symbol
- Order price and quantities (`qty`, `quoteQty`)
- Fee + tax commission and asset used
- Trade role (`isBuyer`, `isMaker`)
- Timestamp of trade execution

### Parameters
| Name | Mandatory | Description | Value | Default |
|------|-----------|-------------|-------|---------|
| symbol | yes | Trading pair symbol | e.g., `btcidr`, `ethidr`, etc | |
| orderId | no | Filter trades by order; must be used with `symbol` | e.g., `aaveidr-limit-3568` | |
| startTime | no | Start of query range (Timestamp) | Unix in milliseconds (UTC)<br><br>e.g., `1723442692520` |Last 24 hours |
| endTime | no | End of query range (Timestamp) |Unix in milliseconds (UTC)<br><br> e.g., `1723442692520` | Last 24 hours |
| limit | no | Number of orders to be returned for display | Allowed range: min. 10, max. 1000 | 500 |
| sort | no | Sorting order for returned data | **asc or desc** | desc |

> ℹ️  Notes
> 
> 1. **Default Range:** If `startTime` and `endTime` are not set, the date range defaults to the last 24 hours.
> 2. **Maximum Range:** The interval between `startTime` and `endTime` cannot exceed 7 days.
> 3. **Supported Parameter Combinations:**
>
>       - `symbol`
>       - `symbol` + `orderId`
>       - `symbol` + `startTime`
>       - `symbol` + `endTime`
>       - `symbol` + `startTime` + `endTime`
> 4. **Removed Old Params:** Query parameters `from_id` and `end_id` have been removed as trade history now uses timestamp parameters (`startTime`, `endTime`).

### Sample Response Body
```json
{
  "data": [
    {
      "tradeId": "72057594037936570",   // the trade id
      "orderId": "aaveidr-limit-3568",  // the order id
      "clientOrderId": "clientx-1",     // the client order id
      "symbol": "aaveidr",              // pair symbol
      "price": "1564455",               // order price
      "qty": "0.1",                     // base quantity
      "quoteQty": "156445",             // quote quantity
      "commission": "468",              // fee+tax commission on the trades of the order.
      "commissionAsset": "idr",         // commission asset
      "isBuyer": false,                 // the trade as buyer or not
      "isMaker": false,                 // the trade as maker or not
      "time": 1723442692520             // time execution of the trade
    }
  ]
}
``` 
