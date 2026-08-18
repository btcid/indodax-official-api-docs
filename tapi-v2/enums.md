# TAPI v2: Enums

## Enums

This documentation defines the enum values used across the API, including order parameters, order statuses, self-trade prevention modes, and deposit & withdrawal statuses.

### Order Side (`side`)

Defines whether an order is a buy or sell order.

| **Value** | **Description** |
| --- | --- |
| `BUY` | Buy order |
| `SELL` | Sell order |

### Order Types

Defines how an order is executed.

| **Value** | **Description** |
| --- | --- |
| `MARKET` | Executes the order at the best available market price. |
| `LIMIT` | Executes the order at user’s specified price. |

### Order Status (`status`)

Defines the current execution status of an order.

| **Status** | **Description** |
| --- | --- |
| `NEW` | The order has been successfully created and is pending for execution. |
| `CANCELLED` | The order has been cancelled and will not be executed. |
| `REJECTED` | The order was rejected and was not accepted for execution. |
| `PARTIALLY_FILLED` | A part of the order has been filled. |
| `FILLED` | The order has been fully executed. |

### Time in Force (`timeInForce`)

Specifies the order execution instruction that determines how long an order remains active.

| `timeInForce` | **Description** | **Order Type** | **Example Scenario** |
| --- | --- | --- | --- |
| `GTC` | GTC (Good 'Til Cancelled) is when the order remains active until it is fully executed or cancelled. | `LIMIT` | A limit order is placed at a specific price and remains open until it is fully filled or manually cancelled. |
| `MOC` | MOC (Maker or Cancel) is when the order is only accepted if it can be placed as a maker order. Otherwise, the order is cancelled. | `LIMIT` | A limit order is placed at a price that does not immediately match an existing order. If it would immediately match, the order is cancelled. |
| `FOK` | FOK (Fill or Kill) requires the order to be fully executed immediately. If the full order cannot be filled, it is cancelled. | `MARKET` | A market order is placed to buy 1 BTC, but only 0.5 BTC is available. The entire order is cancelled because it cannot be fully filled immediately. |

### Self-Trade Prevention (`selfTradePreventionMode`)

Specifies the behaviour to apply when an order would match with another order from the same account.

| `selfTradePreventionMode` | **Description** | **Example Scenario** |
| --- | --- | --- |
| `EXPIRE_TAKER` | Cancels the taker order that would cause the self-trade. | Keep the existing order and cancel the new order that would match it. |
| `EXPIRE_MAKER` | Cancels the existing maker order that would be matched. | Cancel the existing order and allow the new order to proceed. |
| `EXPIRE_BOTH` | Cancels both the maker and taker orders. | Cancel both orders to prevent the self-trade. |

### Deposit & Withdraw Coin Status

Defines the processing status of a deposit or withdrawal.

| **Status** | **Description** |
| --- | --- |
| `pending` | The deposit or withdrawal is still being processed. |
| `failed` | The deposit or withdrawal could not be completed. |
| `success` | The deposit or withdrawal was successfully completed. |
