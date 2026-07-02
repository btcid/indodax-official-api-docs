# Self-Trade Prevention (STP)

## Overview

### What is Self-Trade Prevention?

Self-Trade Prevention (STP) is a risk control feature that prevents orders from the same market participant from matching against each other. 

Since the same market participant is on both sides of the trade, there is no actual change in asset ownership. This distorts price discovery, inflates trading volume, and constitutes market abuse if done intentionally. 

INDODAX strictly prohibits any form of market manipulation or activity intended to create a false or misleading impression of trading activity under its Terms of Use.

### How Does STP Work?

A self-trade occurs when a buy and sell order from the same market participant, identified by the same account (`user_id`), match in the order book. 

Self-trades do not always occur intentionally. Users running multiple strategies or automated systems placing high-frequency orders may accidentally submit opposing orders that match. STP prevents both intentional and accidental self-trades.

A potential self-trade occurs when the incoming order and any resting order in the order book share the same STP configuration. The conflict resolution mode determines which order gets cancelled.

> ℹ️ **Important Information**
> 
> * STP parameters take effect on **Jul 14, 2026**. Requests submitted before this date will not have STP parameters applied.
> * STP is evaluated before order execution and applies only to orders created on or after the effective date. Pre-existing open orders are not evaluated retroactively.
> * STP applies automatically to all orders using fixed default parameters.
> * Trade API users can configure STP parameters on a per-order basis.

---

## General API Information

### Base API Endpoints

| Base Endpoint | Description |
|---|---|
| **Private REST API**<br>`https://indodax.com/tapi` | For `trade` method:<br>`POST https://indodax.com/tapi` |
| **Trade API 2.0**<br>`https://tapi.indodax.com`<br>`https://tapi.btcapi.net` | For Order History endpoint:<br>`GET /api/v2/order/histories` |
| **Private WebSocket**<br>`https://indodax.com/`<br>`https://btcapi.net` | Access for `https://btcapi.net` needs to be whitelisted |

* All timestamps and time-related fields are in milliseconds (ms).
* Data results are sorted in descending order → newest first, oldest last.
* All endpoints return JSON objects or arrays.

---

## API Endpoints

### POST `/tapi` (STP Parameters for `createOrder`)

Self-Trade Prevention (STP) is evaluated by the matching engine before the order is being executed. To place an order with STP parameters, all order creation requests must be sent using the [Trade](./Private-RestAPI.md#trade-endpoints) method to this endpoint:

`POST https://indodax.com/tapi`

#### Request Headers

| Name | Type | Mandatory | Value |
|---|---|---|---|
| `Key` | string | yes | API Key |
| `Sign` | string | yes | Encrypted with method HMAC-SHA512 using secret key. (Request body (?param=val&param1=val1)) |

#### Request Parameters

| Parameter | Mandatory | Description | Value |
|---|---|---|---|
| `method` | yes | Specifies the API method to call. Must be `trade`. | `trade` |
| `pair` | yes | Trading pair for the order. | `btc_idr`, `ltc_btc`, `doge_btc`, etc. |
| `type` | yes | Order side. | `buy` / `sell` |
| `price` | Required for limit orders | Limit price of the order. Not required for market orders. | `5000000` |
| `idr` | Required for limit and market buy orders using IDR amount | Amount of IDR to spend when placing a buy order. | `1000000` |
| `btc` | Required for sell orders and limit buy orders using coin quantity | Amount of the asset to buy or sell (varies depending on the base asset, e.g., btc, eth, doge). | `0.001` |
| `order_type` | optional | Order type. Default: `limit`. | `limit` / `market` |
| `client_order_id` | optional | User-defined client order identifier. Maximum 36 characters. Allowed characters: alphanumeric, `_`, `-`. | e.g., `clientx-sj82ks82j` |
| `time_in_force` | optional | Time-in-force policy for limit orders. Determines how long the order remains active before expiring. Default: `GTC`. | `GTC`, `MOC` |
| `smp_cancel` | optional | Self-Trade Prevention (STP) cancellation mode. Determines which order is cancelled when a self-match is detected. Default: `MAKER`. | `MAKER`, `TAKER`, `BOTH` |

> ℹ️ **Notes**
> 
> * A request will be rejected if a `BUY` order is submitted with both `idr` set and `order_type` set to `limit`.
> * Market Buy orders currently support only IDR-based amounts. To place a Market Buy order, specify the order amount using the `idr` parameter.
> * Trade requests with `order_type = limit` and `time_in_force = MOC` will be rejected if the submitted price is equal to or better than the current top of the order book, resulting in an immediate match. An `MOC` (Maker-Or-Cancel) order must enter the order book as a maker order and cannot be executed immediately.

---

### Self-Trade Prevention Cancellation Mode (`smp_cancel`)

The `smp_cancel` parameter specifies how the matching engine resolves a self-trade when the incoming (taker) order would match against an existing order from the same account.

| Value | Description |
|---|---|
| `MAKER` (default) | Cancels the resting (MAKER) order. The incoming (TAKER) order continues matching against the remaining order book. |
| `TAKER` | Cancels the incoming (TAKER) order. The resting (MAKER) order remains on the order book. |
| `BOTH` | Cancels both the incoming (TAKER) order and the resting (MAKER) order. |

> ℹ️ **Notes**
> 
> * The `smp_cancel` value is evaluated only when a self-trade is detected. The setting on the incoming (taker) order determines the cancellation behavior.
> * If `smp_cancel` is not specified, the system applies the default `MAKER` value.

#### Sample Response Body

```json
{
    "success": 1,
    "return": {
        "sold_btc": 0,
        "receive_rp": 0,
        "fee": 0,
        "remain_btc": "0.00001436",
        "order_id": 310130,
        "remain": "0.00001436",
        "client_order_id": "btcidr-limit-310130"
    }
}
```

> ℹ️ **Note:** The response format remains unchanged. STP cancellation details are available through the [Trade API 2.0: Order History](./INDODAX-TradeAPI-2.md#order-history) endpoint.

---

### GET `/api/v2/order/histories` (STP Cancellation Reason)

When an order is cancelled by the Self-Trade Prevention (STP), the cancellation reason is recorded in the order history for post-trade reconciliation, auditing, and monitoring purposes. 

To verify an order’s cancellation status, check the `cancelReason` field in the Order History response. A value of `SELF_TRADE_PREVENTION` indicates the order was cancelled by STP. This information can be retrieved via the [Order History](./INDODAX-TradeAPI-2.md#order-history) endpoint:

`GET /api/v2/order/histories`

#### Request Headers

| Name | Type | Mandatory | Value |
|---|---|---|---|
| `X-APIKEY` | string | yes | User’s API key |
| `Sign` | string | yes | SIGNED endpoint (must be HMAC-SHA512 encrypted using secret key on query string, e.g., `?param=value&param1=value1`) |

#### Request Parameters

| Name | Mandatory | Description | Value | Default |
|---|---|---|---|---|
| `symbol` | yes | Trading pair symbol | e.g., `btcidr`, `ethidr`, etc. | |
| `startTime` | no | Start of query range (Timestamp) | Unix in milliseconds (UTC), e.g., `1723442692520` | Last 24 hours |
| `endTime` | no | End of query range (Timestamp) | Unix in milliseconds (UTC), e.g., `1723442692520` | Last 24 hours |
| `limit` | no | Number of orders to be returned for display | Allowed range: min. 10, max. 1000 | `100` |
| `sort` | no | Sorting order for returned data | `asc` or `desc` | `desc` |

> ℹ️ **Notes**
> 
> * **Default Range:** If `startTime` and `endTime` are not set, the date range defaults to the last 24 hours.
> * **Maximum Range:** The interval between `startTime` and `endTime` cannot exceed 7 days.
> * **Supported Parameter Combinations:**
>   * `symbol`
>   * `symbol + startTime`
>   * `symbol + endTime`
>   * `symbol + startTime + endTime`

#### Sample Response Body

```json
{
  "data": [
    {
      "orderId": "btcidr-limit-31695",
      "clientOrderId": "btcidr-limit-31695",
      "symbol": "btcidr",
      "side": "BUY",
      "type": "LIMIT",
      "status": "CANCELLED",
      "price": "0.001",
      "oriQty": "10000000",
      "executedQty": "0",
      "submitTime": 1781153210349,
      "finishTime": 1781153238386,
      "cancelReason": "SELF_TRADE_PREVENTION"
    }
  ]
}
```

> ℹ️ **Note:** The `cancelReason` field is only present when the order was cancelled by STP. Orders cancelled manually by the user will not carry this field.

---

### Private WebSocket (STP Order Update Event)

When Self-Trade Prevention (STP) cancels an order, a real-time order update event gets published via the Private WebSocket. Users must be subscribed to the [Private WebSocket (PWS)](./Private-websocket.md#subscribing-to-private-channel) to receive this event. 

Without an active PWS subscription, STP cancellations will only be visible via `/api/v2/order/histories`.

You can subscribe to a private channel using the channel you get from the [Generate Private Token and Private Channel](./Private-websocket.md#generate-private-token-and-private-channel).

#### Request Message

```json
{
  "subscribe": {
    "channel": "pws:#c12d3ca099785ede15c37c9b7642ab89d19bc96e"
  },
  "id": 2
}
```

#### Sample Response Body (Cancel Order)

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
            "clientOrderId": "lendidr-limit-3399",
            "cancelReason": "SELF_TRADE_PREVENTION"
          }
        }
      ]
    }
  }
}
```

> ℹ️ **Note:** The `cancelReason` field in the PWS event provides immediate, real-time visibility into STP-triggered cancellations, reducing ambiguity for automated trading systems and enabling simultaneous monitoring of multiple trading strategies.
