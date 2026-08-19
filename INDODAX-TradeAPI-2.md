# TAPI v2: Private Endpoints

**Table of Contents**

- [INDODAX Trade API 2.0](#indodax-trade-api-20)
- [General API Information](#general-api-information)
- [Error Codes](#error-codes)
- [General Information on Endpoints](#general-information-on-endpoints)
  - [Generate API Key](#generate-api-key)
  - [General Rate Limit by Endpoints](#general-rate-limit-by-endpoints)
  - [Endpoint Security Type](#endpoint-security-type)
  - [SIGNED Endpoint Examples](#signed-endpoint-examples)
- [Private API Endpoints](#private-api-endpoints)
  - [Create Order](#create-order)
  - [Cancel Order](#cancel-order)
  - [Pending Order](#pending-order)
  - [Get Order](#get-order)
  - [Get Account Information](#get-account-information)
  - [Get Withdraw Coin Information History](#get-withdraw-coin-information-history)
  - [Get Deposit Coin Information History](#get-deposit-coin-information-history)
  - [Get Withdraw/Deposit Fiat Information History](#get-withdrawdeposit-fiat-information-history)
  - [Withdraw Coin](#withdraw-coin)
  - [List Deposit Address](#list-deposit-address)
  - [Withdraw IDR](#withdraw-idr)
  - [Order History](#order-history)
  - [Trade History](#trade-history)

---

## **INDODAX Trade API 2.0**

## General API Information

- The base endpoint is [**https://api.indodax.com/**](http://api.indodax.com)
- All endpoints return **JSON objects or arrays**
- Data results are sorted in **descending order** → `newest first`, `oldest last`
- All **timestamps** and **time-related fields** are in **milliseconds (ms)**
- For SIGNED endpoints, pass the signature using the `Sign` request header, or as a query parameter or request body field named `signature`

#### Request Header

| **Key** | **Type** | **Mandatory** | **Value** |
| --- | --- | --- | --- |
| `Accept` | string | yes | Must be `application/json` |
| `Content-Type` | string | no | - For endpoints with method `POST` must sent `Content-Type` with `application/x-www-form-urlencoded`<br>- For the other method like `GET`, `DELETE` doesn’t require to sent `Content-Type` |
| `X-APIKEY` | string | yes | User’s [API key](#generate-api-key) |
| `Sign` | string | no | SIGNED endpoint (must be **HMAC-SHA256** encrypted using secret key on query string)  e.g., `?param=value&param1=value1` |

## Error Codes

- Any endpoint can return an ERROR

#### Sample Error Response:

```json
{
    "code": -1121,
    "msg": "Invalid Symbol"
}
```

#### Trade API Error Codes

Error codes are grouped by category:

1. **General Server / Network Issues (10xx)**

| **HTTP Status** | **Code** | **Error Description** |
| --- | --- | --- |
| 500 Internal Server Error | -1001 | Internal error; unable to process your request.  Please try again. |
| 401 Unauthorized | -1002 | Invalid credentials. API not found or session has expired. |
| 400 Bad Request | -1021 | Invalid timestamp. Timestamp for this request is outside of the recvWindow. |
| 400 Bad Request | -1022 | Invalid nonce/signature. The nonce or signature provided in the request is invalid, or sign not found in the header. |
| 431 Request Header Fields Too Large / 405 Method Not Allowed / 400 Bad Request | -1000 | Unknown error. Request header fields too large, method not allowed, or duplicate request. |

2. **Request Issues (11xx-2xxx)** 

| **HTTP Status** | **Code** | **Error Description** |
| --- | --- | --- |
| 400 Bad Request | -1102 | A mandatory parameter was not sent, was empty/null, or malformed. |
| 400 Bad Request | -1130 | Invalid parameter value. |
| 400 Bad Request | -1121 | Invalid symbol. |
| 400 Bad Request | -1111 | Quantity validation failed. |
| 400 Bad Request | -2014 | API key not found in header. |

3. **Permission / Access Issues**

| **HTTP Status** | **Code** | **Error Description** |
| --- | --- | --- |
| 403 Forbidden | -2015 | Access denied. Trade API is disabled, account is locked/disabled, no permission, unauthorized IP address, or invalid TAPI version key. |

4. **Resource / Rate Limit Issues**

| **HTTP Status** | **Code** | **Error Description** |
| --- | --- | --- |
| 404 Not Found | -1099 | The requested resource does not exist. |
| 404 Not Found | -2013 | Order not found. |
| 429 Too Many Requests | -1003 | Too many requests. |
| 400 Bad Request | -1016 | Market is suspended. |

5. **Trading / Order Issues (20xx)**

| **HTTP Status** | **Code** | **Error Description** |
| --- | --- | --- |
| 400 Bad Request | -2010 | Order was rejected. Insufficient balance, duplicate client order ID, or order rejected for other reasons. |

6. **Withdrawal / Capital Issues (40xx)**

| **HTTP Status** | **Code** | **Error Description** |
| --- | --- | --- |
| 400 Bad Request | -4033 | Invalid address. Address is illegal, or you're trying to withdraw to your own account address. |
| 400 Bad Request | -4039 | Username is not found. |
| 400 Bad Request | -4023 | Withdrawal limit exceeded. |
| 400 Bad Request | -4035 | Address is not whitelisted. |
| 400 Bad Request | -4022 | Withdrawal less than limit. |
| 400 Bad Request | -4019 | Withdrawal bank not allowed. |
| 400 Bad Request | -4060 | Withdrawal violates lock balance constraint. |
| 400 Bad Request | -4026 | Insufficient balance. |

## General Information on Endpoints

- For `GET` and `DELETE` endpoints, parameters must be sent as `query string`
- For `POST` endpoints, the parameters may sent in the `request body` with content type `application/x-www-form-urlencoded`
- Each request must include a `nonce` or `timestamp` parameter to be valid
- For details on supported enum values and their descriptions, please refer to the [**Enums**](tapi-v2/enums.md) page

#### Request Parameters

| **Parameter** | **Mandatory** | **Description** | **Value** |
| --- | --- | --- | --- |
| `nonce` | yes | An increment integer (learn more about [nonce](http://en.wikipedia.org/wiki/Cryptographic_nonce)). | Must be greater than the nonce of the last request.  e.g., if the last request's nonce is `1000`, the next request should be `1001` or greater. |

| **Parameter** | **Mandatory** | **Description** | **Value** |
| --- | --- | --- | --- |
| `timestamp` | yes | Millisecond timestamp when the request was generated. | Timestamp in milliseconds (ms) when the request was created and sent. |
| `recvWindow` | no | Validity window for request in milliseconds (ms). | Default: `5000 ms`  The value should specify the number of milliseconds after the timestamp where your request is valid.  e.g., your request is still valid if it is sent and processed within the `timestamp` and the `timestamp + recvWindow`. |

> ℹ️ **Notes**
>
> - The request must include either `nonce` or `timestamp`. If both parameters are provided, `timestamp` will be used as the effective parameter.

### Generate API Key

TAPIv2 requires specific authorization for API key generation to securely integrate their account to place orders, cancel orders, or withdraw funds by adding multiple security controls.

Please note that **TAPIv2 can only be accessed with a dedicated TAPIv2 API key**. If you already have an existing TAPI key, you’ll still need to generate a new TAPIv2 key, as the existing key cannot be reused for TAPIv2.

To generate a TAPIv2 API key, go to: [**https://indodax.com/trade_api**](https://indodax.com/trade_api)

#### Security Improvements

| **Feature** | **Feature** **Description** |
| --- | --- |
| IP Permission | Additional API access security that restricts requests to registered IP addresses, with View Only access being optional and transaction (Trade and Withdrawal) APIs requiring IP restrictions. |
| Wallet Address & Username Whitelisting | Additional security layer for crypto withdrawals, ensuring funds can only be withdrawn to registered wallet addresses and/or other INDODAX accounts identified by username. |

#### Permission Scope

An API key cannot be activated for a permission unless the corresponding whitelist fields are populated:

| **Permission** | **IP Permission** | **Whitelist Address** | **Whitelist Username** |
| --- | --- | --- | --- |
| Reading (View Only) | **optional** | **not required** | **not required** |
| Spot Trading  (Create & Cancel Orders) | **required** | **not required** | **not required** |
| IDR & Crypto Withdrawal | **required** | **either** `Address` **or** `Username` **required** | **either** `Address` **or** `Username` **required** |

> ℹ️ **Notes**
>
> - IP Whitelisting is mandatory for all transaction-related APIs in Trade API V2.
> - For Reading (View Only) permission, enabling the IP Permission will restrict the access to the registered IP Address only.
> - Wallet Address or Username whitelisting is mandatory for **Crypto Withdrawal** permission with the following formats are accepted:
>
>   - **Wallet Address**: Must contain only letters, numbers, colons (:), periods (.), underscores (_), and hyphens (-).
>   - **Username**: Must contain only letters, numbers, underscores (_), and hyphens (-), with a minimum length of 4 characters.
> - Wallet Address and Username Whitelisting adds an additional layer of protection by ensuring funds can only be withdrawn to pre-approved wallet addresses or INDODAX accounts, even if the API Key is compromised.
> - After users regenerate API keys, coin withdrawals via API will only be available after 24 hours. During that period, users can still make coin withdrawals via website and mobile app.
> - Users should keep their whitelist configurations up to date whenever server IPs or withdrawal destinations change.
> - IDR withdrawal must be made to a bank account registered under the same name as the KYC-verified account holder.

### General Rate Limit by Endpoints

API requests are rate-limited **per IP address**. The applicable limit depends on the endpoint.

| Endpoint Name | Method | Endpoint Path | Rate Limit |
|---|---|---|---|
| [Create Order](#create-order) | POST | `/api/v2/order` | 300 requests / minute |
| [Cancel Order](#cancel-order) | DELETE | `/api/v2/order` | 300 requests / minute |
| [Pending Order](#pending-order) | GET | `/api/v2/openOrders` | 300 requests / minute |
| [Get Order](#get-order) | GET | `/api/v2/order` | 300 requests / minute |
| [Account Information](#get-account-information) | GET | `/api/v2/account` | 300 requests / minute |
| [Withdraw Coin Information History](#get-withdraw-coin-information-history) | GET | `/api/v2/capital/withdraw/history` | 50 requests / minute |
| [Deposit Coin Information History](#get-deposit-coin-information-history) | GET | `/api/v2/capital/deposit/hisrec` | 50 requests / minute |
| [Withdraw/Deposit Fiat Information History](#get-withdrawdeposit-fiat-information-history) | GET | `/api/v2/fiat/orders` | 50 requests / minute |
| [Withdraw Coin](#withdraw-coin) | POST | `/api/v2/capital/withdraw/apply` | 50 requests / minute |
| [List Deposit Address](#list-deposit-address) | GET | `/api/v2/capital/deposit/address/list` | 50 requests / minute |
| [Withdraw IDR](#withdraw-idr) | POST | `/api/v2/fiat/withdraw` | 50 requests / minute |
| [Order History](#order-history) | GET | `/api/v2/order/histories` | 300 requests / minute |
| [Trade History](#trade-history) | GET | `/api/v2/myTrades` | 300 requests / minute |

> ℹ️ **Notes** 
>
> If the rate limit is exceeded, the API may reject subsequent requests until the applicable rate-limit window resets.
>
> The rate limits in the table above are the **general rules** that apply to all API endpoints. However, some endpoints have additional rate-limit rules beyond those specified in the table, specifically [Create Order](#rate-limit) and [Cancel Order](#rate-limit-1).

### Endpoint Security Type

#### API Key Usage

- API-keys are passed into the Rest API via the `X-APIKEY` header.
- API-keys and secret-keys are **case sensitive**.

#### Signed (`TRADE` and `USER_DATA`) Endpoint Security

- The `signature` can be sent in one of the following ways:

  - In the `Sign` request header.
  - As the `signature` field in the request body.
  - As the `signature` query parameter.
- The `signature` value itself is **not case sensitive**
- Any params must be constructed as the query string concatenated with the `request body`   
  (e.g., `*(?param=val&param1=val1) encrypted with method HMAC-SHA256 using secret key*)`

#### Timing Security

- A `SIGNED` endpoint also requires a `timestamp` parameter defined as the **millisecond timestamp** at which the request was created and sent.
- An optional `recvWindow` parameter may be sent to specify the validity duration (in milliseconds) of the request after the `timestamp`. If `recvWindow` is not provided, it defaults to `5000` ms.
- The logic is as follows:

  ```
  if (( timestamp >= serverTime + 1000 || serverTime - timestamp > recvWindow )); then
      # Reject request
      echo "Request rejected"
  else
      # Process request
      echo "Request processed"
  fi
  ```

> ℹ️ **Notes**:
>
> `Serious trading is about timing`. Networks can be unstable and unreliable, which can lead to requests taking varying amounts of time to reach the servers.
>
> With `recvWindow` parameter, user can specify that the request must be processed within a certain number of milliseconds or be rejected by the server.

### SIGNED Endpoint Examples

Step-by-step example on how to send a valid signed payload from the Linux command line using `curl` for GET /api/v2/myTrades:

| **Key** | **Value** |
| --- | --- |
| apiKey | AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA |
| secretKey | f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d |

| **Parameter** | **Value** |
| --- | --- |
| symbol | btcidr |
| limit | 100 |
| timestamp | 1578304294000 |
| recvWindow | 1578303937000 |

##### Example:

- Send parameter using query string

  - **queryString:** symbol=btcidr&limit=100&timestamp=1578304294000&recvWindow=1578303937000
  - **HMAC SHA256 signature:**

    ```json
    [linux]$ echo -n "symbol=btcidr&limit=100&timestamp=1578304294000&recvWindow=1578303937000" | openssl dgst -sha256 -hmac "f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d"
    (stdin)= bab004e5a518740d7a33b38b44dbebecd3fb39f40b42391af39fcce06edabff5233b3e8064a07c528d1c751a6923d5116026c7786e01b22e2d35277a098cae99
    ```
  - **curl command:**

    ```
    (HMAC SHA256)
    [linux]$ curl -H "X-APIKEY: AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA" -H "Sign: bab004e5a518740d7a33b38b44dbebecd3fb39f40b42391af39fcce06edabff5233b3e8064a07c528d1c751a6923d5116026c7786e01b22e2d35277a098cae99" -X GET 'https://api.indodax.com/api/v2/myTrades?symbol=btcidr&limit=100&timestamp=1578304294000&recvWindow=1578303937000'
    ```

Here is a sample Python script performing all the steps above:

```python
#!/usr/bin/env python3

import hashlib
import hmac
import os
import time
import urllib.parse
import requests

# ===== Configuration =====
# Load credentials from environment variables for security
API_KEY = "YOUR-API-KEY-HERE"  # Replace with your actual API key
SECRET_KEY = "YOUR-SECRET-KEY-HERE"  # Replace with your actual secret key

BASE_URL = "https://api.indodax.com"
ENDPOINT = "/api/v2/order"

params = {
    "symbol": "BTCIDR",
    "side": "BUY",
    "type": "LIMIT",
    "price": "500000000",
    "quantity": "0.001",
    "recvWindow": 5000
}

timestamp = int(time.time() * 1000)
params['timestamp'] = timestamp

# Encode query string / POST body
post_body = urllib.parse.urlencode(params)

# Sign request using HMAC-SHA256
signature = hmac.new(
    SECRET_KEY.encode("utf-8"),
    post_body.encode("utf-8"),
    hashlib.sha256
).hexdigest()

# Set headers
headers = {
    "Content-Type": "application/x-www-form-urlencoded",
    "X-APIKEY": API_KEY,
    "Sign": signature,
}

# Send POST request with payload in the body
url = f"{BASE_URL}{ENDPOINT}"
response = requests.post(url, headers=headers, data=post_body)

# Print response
try:
    print(response.json())
except requests.exceptions.JSONDecodeError:
    print(response.text)
```

## Private API Endpoints

TAPIv2 introduces new features and enhancements, providing a more standardized, secure, reliable, and easier-to-use integration experience, while improving overall stability and performance.

The TAPIv2 endpoints are as follows:

| **Endpoint** | **Purpose** |
| --- | --- |
| `POST /api/v2/order` | To **create a new order** |
| `DELETE /api/v2/order` | To **cancel an existing order** |
| `GET /api/v2/openOrders` | To retrieve all **pending (open) orders** |
| `GET /api/v2/order` | To retrieve the **details of a specific order** |
| `GET /api/v2/account` | To retrieve the **account information** |
| `GET /api/v2/capital/withdraw/history` | To retrieve the **withdrawal coin history** |
| `GET /api/v2/capital/deposit/hisrec` | To retrieve the **deposit coin history** |
| `GET /api/v2/fiat/orders` | To retrieve the **fiat deposit & withdrawal history** |
| `POST /api/v2/capital/withdraw/apply` | To **submit a** **coin withdrawal request** |
| `GET /api/v2/capital/deposit/address/list` | To retrieve the **list of deposit addresses** |
| `POST /api/v2/fiat/withdraw` | To **submit an** **IDR withdrawal request** |
| `GET /api/v2/order/histories` | To retrieve the **account Order History** |
| `GET /api/v2/myTrades` | To retrieve the **account Trade History** |

> **ℹ️ Important Information**
>
> - Existing integrations using the [legacy endpoint](https://github.com/btcid/indodax-official-api-docs/blob/2ab6aa6f51355d0581cadb64ca32dfca3d6a8210/Private-RestAPI.md#private-api-endpoint) must be migrated to the corresponding TAPIv2 REST endpoints.
> - The [legacy endpoint](https://github.com/btcid/indodax-official-api-docs/blob/2ab6aa6f51355d0581cadb64ca32dfca3d6a8210/Private-RestAPI.md#private-api-endpoint) will continue to support existing `method` parameters for a limited transition period. However, this functionality will be deprecated and decommissioned in a future release (a decommission date will be announced in advance).
> - To ensure uninterrupted service and access to the latest features and improvements, all users are strongly encouraged to migrate.

### Create Order

```
POST /api/v2/order
```

#### Description

This REST endpoint serves to create a new spot trading order for the authenticated account. Both **LIMIT** and **MARKET** orders are supported. Upon successful submission, the API returns the newly created order information, including:

- System-generated and client-defined order identifiers (`orderId` and `clientOrderId`)
- Trading pair symbol
- Order side and order type
- Order price (for `LIMIT` orders)
- Original and executed quantities
- System-generated order reference (`fullOrderId`)

#### **Rate Limit**

This endpoint is limited to **20 requests per second per authenticated user for each trading pair**.

For example, requests to `BTCIDR` and `ETHIDR` are counted separately. This rate limit applies to all users, including requests originating from whitelisted IP addresses.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `symbol` | yes | Trading pair symbol | string | e.g., `BTCIDR`, `ETHIDR` |  |
| `side` | yes | Order side, please see [Enums](tapi-v2/enums.md#order-side-side) for supported values. | string | `BUY`, `SELL` |  |
| `type` | yes | Order type, please see [Enums](tapi-v2/enums.md#order-types) for supported values. | string | `LIMIT`, `MARKET` |  |
| `price` | required for `LIMIT` order | Order price | decimal | e.g., `10000` |  |
| `quantity` | required for ALL orders except `BUY` `MARKET` orders | Base asset quantity (`BTC`, `ETH`, etc) | decimal | e.g., `0.1` |  |
| `quoteOrderQty` | required for `BUY` side on `MARKET` orders only | Quote asset quantity (`IDR` only) | int64 | Cannot be used together with `quantity`  e.g., `10000` |  |
| `newClientOrderId` | no | Client-defined unique order identifier | string | Alphanumeric + `_` `-` only (max. 36 characters)  e.g., `btcidr-buy-1` | Auto-generated if not set |
| `timeInForce` | no | Order execution instruction, only affected for `LIMIT` order.  Please see [timeInForce](tapi-v2/enums.md#time-in-force-timeinforce) for supported values. | string | `GTC`, `MOC` | `GTC` |
| `selfTradePreventionMode` | no | Self-trade prevention behaviour.  The possible supported values are: [selfTradePreventionMode](tapi-v2/enums.md#self-trade-prevention-selftradepreventionmode) | string | `EXPIRE_TAKER`, `EXPIRE_MAKER`, `EXPIRE_BOTH` | `EXPIRE_MAKER` |

> ℹ️ **Notes**
>
> - The Order Response contains conditional fields whose presence depends on the order type.
> - The `price` field is included only when the order type is `LIMIT`.

#### Sample Response Body

##### Positive Case

```json
{
  "symbol": "BTCIDR",
  "orderId": 6423,
  "clientOrderId": "btcidr-buy-1",
  "side": "SELL",
  "type": "LIMIT",
  "price": "421004000",
  "origQty": "4988.0",
  "executedQty": "0.0",
  "fullOrderId" : "btcidr-limit-1"
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| symbol | Trading pair symbol. |
| orderId | Unique identifier for the order. |
| clientOrderId | Client-specified ID for the order. |
| side | [Order side](tapi-v2/enums.md#order-side-side) (`BUY` or `SELL`). |
| type | [Order type](tapi-v2/enums.md#order-types) (`LIMIT` or `MARKET`). |
| price | Order price per unit of the base asset: IDR or USDT. |
| origQty | Original order quantity. |
| executedQty | Quantity that has already been executed. |
| fullOrderId | System-generated order reference. |

##### Negative Case

- Invalid symbol

  ```json
  {
      "code": -1121,
      "msg": "Invalid symbol."
  }
  ```
- Invalid side

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'side', must be BUY or SELL"
  }
  ```
- Invalid order type

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'type', must be LIMIT or MARKET"
  }
  ```
- Not support order type (STOP_LIMIT)

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'type', stop_limit_not_available"
  }
  ```
- Invalid price when order type `LIMIT`

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'price', required for LIMIT order"
  }
  ```

### Cancel Order

```
DELETE /api/v2/order
```

#### Description

This REST endpoint serves to cancel an existing open order. Once the cancellation request is successful, the API returns the details of the cancelled order, including:

- Order identifiers
- Trading pair symbol
- Order side and type
- Order price
- Original and executed quantities
- System-generated order reference (`fullOrderId`)

#### **Rate Limit**

This endpoint is limited to **30 requests per second per authenticated user** when cancelling an order using either `orderId` or `origClientOrderId`.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `symbol` | yes | Trading pair symbol | string | e.g., `BTCIDR`, `ETHIDR` |  |
| `orderId` | yes | Unique identifier for the order | int64 | e.g., `6436` |  |
| `origClientOrderId` | yes | Client-specified ID for the order set at order creation | string | e.g., `btcidr-buy-1` |  |

> ℹ️ **Notes**
>
> - The request must include either `orderId` or `origClientOrderId`. If both parameters are provided, `orderId` will be used as the effective parameter.

#### Sample Response Body

##### Positive Case

```json
{
  "symbol": "BTCIDR",
  "orderId": 6436,
  "origClientOrderId": "btcidr-buy-1",
  "side": "SELL",
  "type": "LIMIT",
  "price": "421004000",
  "stopPrice": "421003000",
  "origQty": "4988.0",
  "executedQty": "0.0",
  "fullOrderId" : "btcidr-limit-1"
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| symbol | Trading pair symbol. |
| orderId | Unique identifier for the order. |
| origClientOrderId | Client-specified ID for the cancelled order. |
| side | [Order side](tapi-v2/enums.md#order-side-side) (`BUY` or `SELL`). |
| type | [Order type](tapi-v2/enums.md#order-types) (`LIMIT` or `MARKET`). |
| price | Order price per unit of the base asset: IDR or USDT. |
| stopPrice | Stop price configured for the order, if applicable. |
| origQty | Original order quantity. |
| executedQty | Quantity that had already been executed before cancellation. |
| fullOrderId | System-generated order reference. |

##### Negative Case

- Invalid symbol

  ```json
  {
      "code": -1121,
      "msg": "Invalid symbol."
  }
  ```
- Order not exists with orderId

  ```json
  {
      "code": -2013,
      "msg": "order 30325 is not exists"
  }
  ```
- - Order not exists with origClientOrderId
  ```json
  {
      "code": -2013,
      "msg": "order buy-11 is not exists"
  }
  ```

### Pending Order

```
GET /api/v2/openOrders
```

#### Description

This REST endpoint serves to retrieve all currently open orders for the authenticated account. Open orders include orders that are waiting to be filled or have been partially filled. Each order contains:

- Order identifiers
- Trading pair symbol
- Order side, type, and status
- Original and executed quantities
- Order timestamp
- System-generated order reference (`fullOrderId`)

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `symbol` | no | Trading pair symbol | string | e.g., `BTCIDR`, `ETHIDR` |  |

#### Sample Response Body

##### Positive Case

- Doesn’t have any pending orders

```json
[]
```

- Pending orders exists

```json
[
  {
    "symbol": "BTCIDR",
    "orderId": 6423,
    "clientOrderId": "clientx-sj82ks82j",
    "side": "SELL",
    "price": "421004000",
    "stopPrice": "135632",
    "origQty": "0.02000000",
    "executedQty": "0",
    "status": "NEW",
    "type": "LIMIT",
    "time": 1723442692520,
    "fullOrderId" : "btcidr-limit-1"
  }
]
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| symbol | Trading pair symbol. |
| orderId | Unique identifier for the order. |
| clientOrderId | Client-specified ID for the order. |
| side | [Order side](tapi-v2/enums.md#order-side-side) (`BUY` or `SELL`). |
| price | Order price per unit of the base asset: IDR or USDT. |
| stopPrice | Stop price configured for the order, if applicable. |
| origQty | Original order quantity. |
| executedQty | Quantity that has already been executed. |
| status | Current order status (see [Enums](tapi-v2/enums.md#order-status-status)) |
| type | [Order type](tapi-v2/enums.md#order-types) (`LIMIT` or `MARKET`). |
| time | Order creation timestamp (Unix time in milliseconds). |
| fullOrderId | System-generated order reference. |

##### Negative Case

- Invalid symbol

  ```json
  {
      "code": -1121,
      "msg": "Invalid symbol."
  }
  ```

### Get Order

```
GET /api/v2/order
```

#### Description

This REST endpoint serves to retrieve detailed information about a specific order. The response contains the latest execution progress and current order status, including:

- Order identifiers
- Trading pair symbol
- Order side, type, and status
- Original and executed quantities
- Order timestamp
- System-generated order reference (`fullOrderId`)

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `symbol` | yes | Trading pair symbol | string | e.g., `BTCIDR`, `ETHIDR` |  |
| `orderId` | yes | Unique identifier for the order | string | e.g., `6423` |  |
| `origClientOrderId` | yes | Client-specified ID for the order set at order creation | string | e.g., `clientx-sj82ks82j` |  |

> ℹ️ **Notes**
>
> - The request must include either `orderId` or `origClientOrderId`. If both parameters are provided, `orderId` will be used as the effective parameter.

#### Sample Response Body

##### Positive Case

```json
{
  "symbol": "BTCIDR",
  "orderId": 6423,
  "clientOrderId": "clientx-sj82ks82j",
  "side": "SELL",
  "price": "421004000",
  "stopPrice": "135632",
  "origQty": "0.02000000",
  "executedQty": "0",
  "status": "NEW",
  "type": "LIMIT",
  "time": 1723442692520,
  "fullOrderId" : "btcidr-limit-1"
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| symbol | Trading pair symbol. |
| orderId | Unique identifier for the order. |
| clientOrderId | Client-specified ID for the order. |
| side | [Order side](tapi-v2/enums.md#order-side-side) (`BUY` or `SELL`). |
| price | Order price per unit of the base asset: IDR or USDT. |
| stopPrice | Stop price configured for the order, if applicable. |
| origQty | Original order quantity. |
| executedQty | Quantity that has already been executed. |
| status | Current order status (see [Enums](tapi-v2/enums.md#order-status-status)). |
| type | [Order type](tapi-v2/enums.md#order-types) (`LIMIT` or `MARKET`). |
| time | Order creation timestamp (Unix time in milliseconds). |
| fullOrderId | System-generated order reference. |

##### Negative Case

- Invalid Symbol

  ```json
  {
      "code": -1121,
      "msg": "Invalid symbol."
  }
  ```
- Order Not Found

  ```json
  {
      "code": -2013,
      "msg": "Order not found."
  }
  ```

### Get Account Information

```
GET /api/v2/account
```

#### Description

This REST endpoint serves to retrieve account information for the authenticated user. The response includes the account permissions, account type, and the current balance of each asset held by the account, including both available and locked balances.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `omitZeroBalances` | no | Excludes assets with zero balances from the response | boolean | `true`, `false` | `false` |

#### Sample Response Body

##### Positive Case

```json
{
  "canTrade": true,
  "canWithdraw": true,
  "accountType": "Regular",
  "balances": [
    {
      "asset": "BTC",
      "free": "4723846.89208129",
      "locked": "0.00000000"
    }
  ],
  "uid": 1776329213
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| canTrade | Indicates whether trading is enabled for the account. |
| canWithdraw | Indicates whether withdrawals are enabled for the account. |
| accountType | Account type. |
| balances | List of account balances. |
| balances.asset | Asset symbol. |
| balances.free | Available balance that can be traded or withdrawn. |
| balances.locked | Balance currently locked in open orders or pending transactions. |
| uid | Unique account identifier. |

##### Negative Case

- Invalid credentials

  ```json
  {
      "code": -1002,
      "msg": "Invalid credentials. API not found or session has expired. Please generate new Key"
  }
  ```

### Get Withdraw Coin Information History

```
GET /api/v2/capital/withdraw/history
```

#### Description

This REST endpoint serves to retrieve the cryptocurrency withdrawal history for the authenticated account. Each withdrawal record includes the withdrawal details, destination address, transaction fee, blockchain transaction reference, and current processing status, with the query supports a maximum time range of **90 days** and returns a maximum of **1,000 records**.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `coin` | no | Cryptocurrency symbol | string | e.g., `BTC`, `ETH`, `USDT` |  |
| `startTime` | no | Start of the query time range  (Unix timestamp in milliseconds) | int64 | Timestamp (ms) e.g., `1723442692520` | Last 90 days |
| `endTime` | no | End of the query time range  (Unix timestamp in milliseconds) | int64 | Timestamp (ms) e.g., `1723442692520` | Current time (now) |
| `limit` | no | Maximum number of records returned | int | min. `10`, max. `1000` | `1000` |
| `WithdrawStatus` | no | Withdrawal status filter  (see [Enums](tapi-v2/enums.md#deposit-and-withdraw-coin-status) for support values) | string | e.g., `success`, `pending`, `failed` |  |

> ℹ️ **Notes**
>
> - Currently, the Deposit & Withdrawal Information History API does not support retrieving history for all coins at once, so users must specify a specific coin. If no coin is specified, the API will return only the history for `BTC` by default.

#### Sample Response Body

##### Positive Case

- Doesn’t have withdraw coin history

```json
[]
```

- Withdraw Coin History Exists

```json
[
  {
    "id": "3602369",
    "amount": "0.00150000",
    "transactionFee": "0.004",
    "coin": "BTC",
    "address": "1FZdVHtiBqMrWdjPyRPULCUceZPJ2WLCsB",
    "txId": "60fd9007ebfddc753455f95fafa808c4302c836e4d1eebc5a132c36c1d8ac354",
    "network": "",
    "status": "success",
    "applyTime": 1661493146000,
    "completeTime": 1661493146000
  }
]
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| id | Unique withdrawal record identifier. |
| amount | Withdrawal amount. |
| transactionFee | Withdrawal fee charged. |
| coin | Cryptocurrency symbol. |
| address | Destination wallet address. |
| txId | Blockchain transaction hash. |
| network | Blockchain network used for the withdrawal. |
| status | Current withdrawal status. |
| applyTime | Withdrawal request timestamp (Unix time in milliseconds). |
| completeTime | Withdrawal completion timestamp (Unix time in milliseconds). |

##### Negative Case

- Failed to get coin

  ```json
  {
      "code": -1130,
      "msg": "Failed to get coin history"
  }
  ```
- Invalid time range, days greater than 90 days

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'timeRange', time range cannot exceed 90 days"
  }
  ```

- Start time greater then end time

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'startTime', startTime must be less than endTime"
  }
  ```

### Get Deposit Coin Information History

```
GET /api/v2/capital/deposit/hisrec
```

#### Description

This REST endpoint serves to retrieve the cryptocurrency deposit history for the authenticated account. Each deposit record includes the deposited amount, blockchain transaction reference, destination address, and current deposit status, with the query supports a maximum time range of **90 days** and returns a maximum of **1,000 records**.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `coin` | no | Cryptocurrency symbol | string | e.g., `BTC`, `ETH`, `USDT` |  |
| `startTime` | no | Start of the query time range  (Unix timestamp in milliseconds) | int64 | Timestamp (ms) e.g., `1723442692520` | Last 90 days |
| `endTime` | no | End of the query time range  (Unix timestamp in milliseconds) | int64 | Timestamp (ms) e.g., `1723442692520` | Current time (now) |
| `limit` | no | Maximum number of records returned | int | min. `10`, max. `1000` | `1000` |
| `depositStatus` | no | Deposit status filter (see [Enums](tapi-v2/enums.md#deposit-and-withdraw-coin-status) for support values) | string | e.g., `success`, `pending`, `failed` |  |

> ℹ️ **Notes**
>
> - Currently, the Deposit & Withdrawal Information History API does not support retrieving history for all coins at once, so users must specify a specific coin. If no coin is specified, the API will return only the history for `BTC` by default.

#### Sample Response Body

##### Positive Case

- Doesn’t have withdraw coin history

```json
[]
```

- Deposit coin history exists

```json
[
  {
    "id": "3602369",
    "amount": "0.001",
    "coin": "BNB",
    "txId": "c816aeb35a5b42f389970325a32aff69bb6b2126784dcda8f23b9dd9570d6573",
    "address": "bnb136ns6lfw4zs5hg4n85vdthaad7hq5m4gtkgf23",
    "addressTag": "",
    "network": "",
    "status": "success",
    "insertTime": 1661493146000,
    "completeTime": 1661493146000
  }
]
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| id | Unique deposit record identifier. |
| amount | Deposited amount. |
| coin | Cryptocurrency symbol. |
| txId | Blockchain transaction hash. |
| address | Deposit wallet address. |
| addressTag | Destination tag or memo, if applicable. |
| network | Blockchain network used for the deposit. |
| status | Current deposit status (see [Enums](tapi-v2/enums.md#deposit-and-withdraw-coin-status) for support values) |
| insertTime | Deposit detection timestamp (Unix time in milliseconds). |
| completeTime | Deposit completion timestamp (Unix time in milliseconds). |

##### Negative Case

- Failed to get coin history

  ```json
  {
      "code": -1130,
      "msg": "Failed to get coin history"
  }
  ```
- Invalid time range, days greater than 90 days

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'timeRange', time range cannot exceed 90 days"
  }
  ```
- Start time greater then end time

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'startTime', startTime must be less than endTime"
  }
  ```

### Get Withdraw/Deposit Fiat Information History

```
GET /api/v2/fiat/orders
```

#### Description

This REST endpoint serves to retrieve the fiat deposit and withdrawal history for the authenticated account. Each transaction record includes the transaction amount, payment method, status, timestamps, and destination account information where applicable, with the query supports a maximum time range of **30 days** and returns a maximum of **1,000 records**.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `transactionType` | no | Transaction type | string | `"0"` (Deposit),  `"1"` (Withdrawal) | `"1"` |
| `beginTime` | no | Start of the query time range  (Unix timestamp in milliseconds) | int64 | Timestamp (ms) e.g., `1723442692520` | Last 30 days |
| `endTime` | no | End of the query time range  (Unix timestamp in milliseconds) | int64 | Timestamp (ms) e.g., `1723442692520` | Current time (now) |
| `limit` | no | Maximum number of records returned | int | min. `10`, max. `1000` | `1000` |

#### Sample Response Body

##### Positive Case

- Doesn’t have Withdraw/Deposit fiat history

```json
{
    "data": []
}
```

- Withdraw/Deposit fiat history exists

```json
{
  "data": [
    {
      "orderNo": "25011997",
      "amount": "1490000",
      "totalFee": "10000",
      "method": "bank",
      "fiatCurrency": "IDR",
      "status": "success",
      "createTime": 1723131894000,
      "updateTime": 1723131894000,
      "bankName": "Permata Bank",
      "bankTag": "anshar permata",
      "bankAccountNumber": "9850579277"
    }
  ]
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| data | List of fiat transaction records. |
| orderNo | Unique fiat transaction identifier. |
| amount | Transaction amount. |
| totalFee | Transaction fee charged. |
| method | Payment method used. |
| fiatCurrency | Fiat currency (IDR). |
| status | Current transaction status. |
| createTime | Transaction creation timestamp (Unix time in milliseconds). |
| updateTime | Last update timestamp (Unix time in milliseconds). |
| bankName | Destination Bank account name |
| bankTag | Destination Bank account tag |
| bankAccountNumber | Destination bank account number. |

##### Negative Case

- Invalid time range, days greater than 30 days

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'timeRange', time range cannot exceed 30 days"
  }
  ```
- Begin time greater then end time

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'beginTime', beginTime must be less than endTime"
  }
  ```

### Withdraw Coin

```
POST /api/v2/capital/withdraw/apply
```

#### Description

This REST endpoint serves to submit a cryptocurrency withdrawal request from the authenticated account. Upon successful submission, the API returns the withdrawal details, including the withdrawal amount, applicable fee, destination address, and the assigned withdrawal identifier.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `coin` | yes | Cryptocurrency symbol | string | e.g., `BTC`, `ETH`, `DOGE` |  |
| `network` | yes | Blockchain network | string | Only if applicable e.g., `ERC20`, `TRC20`, `BEP20`, etc |  |
| `address` | yes | Destination wallet address | string | Must contain only letters, numbers, colons (:), periods (.), underscores (_), and hyphens (-) |  |
| `amount` | yes | Withdrawal amount | string | e.g., `10.00000000` |  |
| `addressTag` | no | Destination tag or memo | string | Only if supported by the asset platform |  |
| `withdrawOrderId` | yes | Client-defined unique withdrawal identifier | string | Alphanumeric (max. 255 characters) |  |
| `withdrawMethod` | no | Withdrawal destination type | string | `address`, `username` | `address` |
| `withdrawUsername` | no | Recipient username | string | Must contain only letters, numbers, underscores (_), and hyphens (-), with a minimum length of 4 characters. |  |

> ℹ️ **Notes**
>
> - Crypto withdrawal requires additional security with Wallet Address & Username Whitelisting (see [Permission Scope](#permission-scope))
> - Please note that after users regenerate API keys, coin withdrawals via API will only be available after 24 hours. During that period, users can still make coin withdrawals via website and mobile app.
> - Exchanges use memo for accepting deposits for certain assets. For example: Destination Tag (for Ripple), Message (for NXT), Memo (for BitShares).
> - The parameter `withdrawUsername` is mandatory when `withdrawMethod=username`.

#### Sample Response Body

##### Positive Case

```json
{
  "id": "12345",
  "coin": "doge",
  "network": "",
  "address": "D9iCdBLBosJzGSvpQGMSobwtdgB2rS1zam",
  "amount": "10.00000000",
  "transactionFee": "5.00000000",
  "amountAfterFee": "5.00000000",
  "withdrawOrderId": "12345"
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| id | Unique withdrawal request identifier. |
| coin | Cryptocurrency symbol. |
| network | Blockchain network used for the withdrawal. |
| address | Destination wallet address. |
| amount | Requested withdrawal amount. |
| transactionFee | Withdrawal fee charged. |
| amountAfterFee | Amount transferred after deducting the withdrawal fee. |
| withdrawOrderId | Client-defined withdrawal identifier. |

##### Negative Case

- A newly created API key only becomes available for coin withdrawals after 24 hours.

  ```json
  {
      "code": -2015,
      "msg": "As a security measure, a new withdraw API key requires 24 hours waiting period to work normally."
  }
  ```
- Recipient is same with requested user

  ```json
  {
      "code": -4033,
      "msg": "Please use recipient address other than your Indodax account address"
  }
  ```
- duplicate request

  ```json
  {
      "code": -1130,
      "msg": "request_id is already exist"
  }
  ```
- Invalid network

  ```json
  {
      "code": -1130,
      "msg": "Invalid network, please fill with one of this (erc20, bep20)"
  }
  ```
- Withdraw username is not whitelisted

  ```json
  {
      "code": -1001,
      "msg": "Withdraw username is not whitelisted."
  }
  ```
- Withdraw address is not whitelisted

  ```json
  {
      "code": -4035,
      "msg": "Withdraw address is not whitelisted."
  }
  ```
- Insufficient balance

  ```json
  {
      "code": -4026,
      "msg": "Insufficient balance to withdraw!"
  }
  ```

### List Deposit Address

```
GET /api/v2/capital/deposit/address/list
```

#### Description

This REST endpoint serves to retrieve the deposit address for the specified cryptocurrency and blockchain network. For assets that require a destination tag or memo, the corresponding value is also returned.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `coin` | yes | Cryptocurrency symbol | string | e.g., `BTC`, `ETH`, `DOGE` |  |
| `network` | yes | Blockchain network | string | Only if applicable e.g., `ERC20`, `TRC20`, `BEP20`, etc |  |

> ℹ️ **Notes**
>
> - To get the deposit address list, you must first create one by logging in to your INDODAX account, go to <https://indodax.com/finance/>, and click **Deposit** for the desired asset to generate a deposit address.

#### Sample Response Body

##### Positive Case

- Doesn’t have deposit coin address

  ```json
  [
      {
          "coin": "XR",
          "address": "",
          "tag": "",
          "network": "bep20"
      }
  ]
  ```
- Has deposit coin address

  ```json
  [
      {
          "coin": "USDT",
          "address": "0xAF1A5c4DC5ccC511DbFA3c8bfEA24a41Bea27D8c",
          "tag": "",
          "network" : "erc20"
      }
  ]
  ```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| coin | Cryptocurrency symbol. |
| address | Deposit wallet address. |
| tag | Destination tag or memo, if applicable. |
| network | Blockchain network. |

##### Negative Case

- Invalid currency

  ```json
  {
      "code": -1121,
      "msg": "invalid currency"
  }
  ```
- Invalid coin

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'coin', coin parameter is required"
  }
  ```

### Withdraw IDR

```
POST /api/v2/fiat/withdraw
```

#### Description

This REST endpoint serves to submit an Indonesian Rupiah (IDR) withdrawal request to a registered bank account. Upon successful submission, the API returns the unique withdrawal order identifier that can be used to track the withdrawal request.

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `apiPaymentMethod` | yes | Withdrawal payment method | string | Only support `bank_transfer` |  |
| `currency` | yes | Fiat currency | string | Only support `IDR` |  |
| `amount` | yes | Withdrawal amount | int64 | e.g., `1000000` |  |
| `accountInfo` | yes | Destination bank account information | object | e.g., `{"accountNumber" : "8760673466", "bankCodeForPix":"014"}` |  |
| `clientRequestId` | no | Client-defined idempotency key | string | Optional (max. 36 characters) |  |

#### Account Info Information

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `accountNumber` | yes | Destination Bank account number | string | e.g., `8760673466` |  |
| `bankCodeForPix` | yes | Destination Bank account code. | string | e.g., `014` |  |

> ℹ️ **Notes**
>
> - `bankCodeForPix` must be provided as a **3-digit code**. For codes with fewer than three digits, prepend `0` (e.g., `9` → `009` for BNI and `14` → `014` for BCA). See [supported banks list](tapi-v2/supported-banks-idr-withdrawal.md) for more details.
> - Fiat (IDR) withdrawal must be made to a bank account registered under the same name as the KYC-verified account holder.
> - Fiat (IDR) withdrawals currently support **Indonesian banks only**. Withdrawals to banks outside Indonesia are not supported.

#### Sample Response Body

##### Positive Case

```json
{ 
   "data": {
    "orderId": "04595xxxxxxxxx37"
  }
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| data | Fiat withdrawal information. |
| data.orderId | Unique identifier assigned to the withdrawal request. |

##### Negative Case

- Invalid minimum withdrawal amount

  ```json
  {
      "code": -1130,
      "msg": "Withdrawal amount must be greater than 100000"
  }
  ```
- Invalid maximum withdrawal amount

  ```json
  {
      "code": -1130,
      "msg": "Withdrawal amount must be less than 1000000000"
  }
  ```
- Invalid payment method

  ```json
  {
      "code": -1130,
      "msg": "Invalid parameter value.\n\nInvalid parameter 'apiPaymentMethod', only support bank_transfer"
  }
  ```

### Order History

```
GET /api/v2/order/histories
```

#### Description

This REST endpoint serves to retrieve an account’s order history for a specific trading pair symbol by returning all orders placed by the account within the specified timeframe. Each order history contains:

- `orderId` and `clientOrderId`
- Trading pair symbol
- Order price and side of the order
- Original and executed quantities
- Order type and status (`FILLED`, `CANCELLED`, `REJECTED`)
- Timestamps for submission and completion
- `cancelReason` (optional, present when status is `CANCELLED` due to Self-Trade Prevention)

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `symbol` | yes | Trading pair symbol | string | e.g., btcidr, ethidr, etc |  |
| `startTime` | no | Start of query range (Timestamp) | int64 | Unix in milliseconds (UTC)  e.g., 1723442692520 | Last 24 hours |
| `endTime` | no | End of query range (Timestamp) | int64 | Unix in milliseconds (UTC)  e.g., 1723442692520 | Current time (now) |
| `limit` | no | Number of orders to be returned for display | int | Allowed range:  min. 10, max. 1000 | 100 |
| `sort` | no | Sorting order for returned data | string | **asc or desc** | desc |

> ℹ️ **Notes**
>
> 1. **Default Range:** If `startTime` and `endTime` are not set, the date range defaults to the last 24 hours.
> 2. **Maximum Range:** The interval between `startTime` and `endTime` cannot exceed 7 days.
> 3. **Supported Parameter Combinations:**
>
>    - `symbol`
>    - `symbol` + `startTime`
>    - `symbol` + `endTime`
>    - `symbol` + `startTime` + `endTime`
> 4. **Self-Trade Prevention (STP):** For orders cancelled due to Self-Trade Prevention, the response will include a `cancelReason` field set to `"SELF_TRADE_PREVENTION"`. For complete details on STP, please refer to the [Self-Trade Prevention (STP) Documentation](https://github.com/btcid/indodax-official-api-docs/blob/master/Self-Trade%20Prevention-TradeAPI.md).

#### Sample Response Body

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
    },
    {
      "orderId": "btcidr-limit-31695",  // the order id
      "clientOrderId": "btcidr-limit-31695", // the client order id
      "symbol": "btcidr",               // symbol
      "side": "BUY",                    // side of the order, BUY or SELL
      "type": "LIMIT",                  // order type
      "status": "CANCELLED",            // order status
      "price": "0.001",                 // order price
      "oriQty": "10000000",             // original order quantity
      "executedQty": "0",               // executed order quantity
      "submitTime": 1781153210349,      // time of the order submitted
      "finishTime": 1781153238386,      // time of the order finish
      "cancelReason": "SELF_TRADE_PREVENTION" // present when order was cancelled by STP
    }
  ]
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| orderId | Unique identifier for the order. |
| clientOrderId | Client-specified ID for the order. |
| symbol | Trading pair symbol (e.g., aaveidr). |
| side | Side of the order: [BUY or SELL](tapi-v2/enums.md#order-side-side). |
| type | Type of the order: [LIMIT or MARKET](tapi-v2/enums.md#order-types). |
| status | Current status of the order: [FILLED, CANCELLED, or REJECTED](tapi-v2/enums.md#order-status-status). |
| price | Order price per unit of the base asset: IDR or USDT. |
| oriQty | Original quantity specified in the order. |
| executedQty | Executed quantity of the order |
| submitTime | Timestamp when the order was submitted (milliseconds since epoch). |
| finishTime | Timestamp when the order was completed (milliseconds since epoch). |

### Trade History

```
GET /api/v2/myTrades
```

#### Description

This REST endpoint serves to retrieve an account’s trade execution history for a specific trading pair symbol by returning each trade fill executed from orders placed by the account. Each trade history includes:

- `tradeId`, `orderId`, and `clientOrderId`
- Trading pair symbol
- Order price and quantities (`qty`, `quoteQty`)
- Fee + tax commission and asset used
- Trade role (`isBuyer`, `isMaker`)
- Timestamp of trade execution

#### Parameters

| **Name** | **Mandatory** | **Description** | **Type** | **Value** | **Default** |
| --- | --- | --- | --- | --- | --- |
| `symbol` | yes | Trading pair symbol | string | e.g., `btcidr`, `ethidr`, etc |  |
| `orderId` | no | Filter trades by orderId; must be used with `symbol` | string | e.g., `aaveidr-limit-3568` |  |
| `clientOrderId` | no | Filter trades by clientOrderId; must be used with `symbol` | string | e.g., `clientx-1` |  |
| `startTime` | no | Start of query range (Timestamp) | int64 | Unix in milliseconds (UTC)  e.g., `1723442692520` | Last 24 hours |
| `endTime` | no | End of query range (Timestamp) | int64 | Unix in milliseconds (UTC)  e.g., `1723442692520` | Current time (now) |
| `limit` | no | Number of orders to be returned for display | int | Allowed range:  min. 10, max. 1000 | 500 |
| `sort` | no | Sorting order for returned data | string | **asc or desc** | desc |

> ℹ️ **Notes**
>
> 1. **Default Range:** If `startTime` and `endTime` are not set, the date range defaults to the last 24 hours.
> 2. **Maximum Range:** The interval between `startTime` and `endTime` cannot exceed 7 days.
> 3. **Supported Parameter Combinations:**
>
>    - `symbol`
>    - `symbol` + `orderId`
>    - `symbol` + `clientOrderId`
>    - `symbol` + `startTime`
>    - `symbol` + `endTime`
>    - `symbol` + `startTime` + `endTime`
> 4. **Removed Old Params:** Query parameters `from_id` and `end_id` have been removed as trade history now uses timestamp parameters (`startTime`, `endTime`).

#### Sample Response Body

```json
{
  "data": [
    {
      "tradeId": "72057594037936570",
      "orderId": "aaveidr-limit-3568",
      "clientOrderId": "clientx-1",
      "symbol": "aaveidr",
      "price": "1564455",
      "qty": "0.1",
      "quoteQty": "156445",
      "commission": "468",
      "commissionAsset": "idr",
      "isBuyer": false,
      "isMaker": false,
      "time": 1723442692520
    }
  ]
}
```

#### Response Description

| **Field** | **Description** |
| --- | --- |
| tradeId | Unique identifier for the trade. |
| orderId | Unique identifier for the order. |
| clientOrderId | Client-specified ID for the order. |
| symbol | Trading pair symbol (e.g., aaveidr). |
| price | Order price per unit of the base asset: IDR or USDT. |
| qty | Base asset quantity executed in this trade (e.g., btcidr, qty is btc). |
| quoteQty | Total trade value in the quote asset: IDR or USDT (e.g., btcusdt, quoteQty is USDT), calculated as qty × price. |
| commission | Total fees paid for the trade (incl. trading fees and any applicable taxes). |
| commissionAsset | Asset used to pay the commission: IDR or USDT. |
| isBuyer | true if the account is the Buyer side in this trade; otherwise false. |
| isMaker | true if the account is the Maker in this trade; otherwise false. |
| time | Timestamp when the trade was executed (milliseconds since epoch). |
