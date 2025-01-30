# Deadman Switch Documentation

**Table of Contents**
- [General Information](#general-information)
	- [Base URL](#base-url)
    - [Headers](#headers)
- [API Endpoint](#api-endpoint)
    - [SIGN Endpoint Security ](#sign-endpoint-security)
        - [Example](#example)
            - [Using Query String + URL Encoded](#using-query-string--url-encoded)
            - [Using Request Body](#using-request-body)
    - [POST /countdownCancelAll](#post-countdowncancelall)
        - [Request Parameters](#request-parameters)
        - [Response](#response)
        - [Error Codes](#error-codes)

## General Information
This rest endpoint means to ensure your open orders are canceled in case of an outage. The endpoint should be called repeatedly as heartbeats so that the existing countdown time can be canceled and replaced by a new one.

Example, you call this endpoint at 30s intervals with an `countdownTime` of 120000 (120s). If this endpoint is not called within 120 seconds, all your orders of the specified symbol will be automatically canceled. If this endpoint is called with an `countdownTime` of 0, the countdown timer will be stopped.

The system will check all countdowns **approximately every 10 milliseconds**, so please note that sufficient redundancy should be considered when using this function. We do not recommend setting the countdown time to be too precise or too small.

**Additional Notes :** 
* All `recvWindow` and `timestamp` related fields are in **milliseconds**.
* All endpoints return a JSON object.
* URL Ratelimit 10 requests per 10 seconds per IP.

### Base URL
| **Environment**  | **Base URL**               | **Description**												|
| ---------------- | ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| Production       | https://indodax.com/tapi, https://btcapi.net/tapi     | Access for production. |
| Demo             | https://demo-indodax.com/tapi | Access for demo. |

### Headers

| Name         | Value                                  | Description                                     |
| ---------------| -------------------------------------- | ----------------------------------------------- |
| `Key`        | `{{tapi-key}}`                         | The API key for authentication.                |
| `Sign`       | `{{sign}}` (using HMAC SHA512)         | The signature for request validation.          |
| `Content-Type` | `text/plain`                           | The type of content being sent in the request. |

## API Endpoint 

### SIGN Endpoint Security 
* `SIGNED` endpoints require an additional parameter `Sign`, to be
  sent in the  `header`.
* Endpoints use `HMAC SHA512` signatures.
  Use your `Key` as the key and `totalParams` as the value for the HMAC operation.
* The `signature` is **not case sensitive**.
* `totalParams` is defined as the `query string` concatenated with the
  `request body`. 

#### **Example**
Here is a step-by-step example of how to send a vaild signed payload from the
Linux command line using `curl`.

| Key | Value 
|--|--|
| `apiKey` | LSCE7NJG-JACRNTBX-D834R4UG-KMMTV8OP-PS1NHRBA 
| `secretKey` | da78a39e9dda31c399bcc293d997a347dc7fd0408cb5151931243a302b273ec3238510ea61e11f7c 

| Parameter | Value | Description |
|--|--|--|
|`pair`| btc_idr,eth_idr | To get available pairs, access the [api/pairs](./Public-RestAPI.md#pairs) endpoint and use the `ticker_id` values.
|`countdownTime`| 10000| Field in milliseconds
|`timestamp`| 1578304294001 | Field in milliseconds
|`recvWindow`| 1578303937000 | Field in milliseconds

#### **Using Query String + URL Encoded**

Generate the HMAC SHA512 signature using the query string:

```bash
➜ echo -n "pair=btc_idr%2Ceth_idr&countdownTime=10000&timestamp=1578304294001&recvWindow=1578303937000" | openssl dgst -sha512 -hmac "da78a39e9dda31c399bcc293d997a347dc7fd0408cb5151931243a302b273ec3238510ea61e11f7c"
SHA2-512(stdin)= 29ff89378b9f33954b0f5319488190078f091c7723d886c5c2a4a0b06ef793d7d3b99155d63410203a21355e5e2757cb4e566adbd67ec37b8257a68d8c72877c
```
Perform the API request using curl:
```bash
curl --location -X POST 'https://demo-indodax.com/tapi/countdownCancelAll?pair=btc_idr%2Ceth_idr&countdownTime=10000&timestamp=1578304294001&recvWindow=1578303937000' \
--header 'Key: LSCE7NJG-JACRNTBX-D834R4UG-KMMTV8OP-PS1NHRBA' \
--header 'Sign: 29ff89378b9f33954b0f5319488190078f091c7723d886c5c2a4a0b06ef793d7d3b99155d63410203a21355e5e2757cb4e566adbd67ec37b8257a68d8c72877c'
```

#### **Using Request Body**
Generate the HMAC SHA512 signature using the request body:
```bash
➜ echo -n "pair=btc_idr,eth_idr&countdownTime=10000&timestamp=1578304294001&recvWindow=1578303937000" | openssl dgst -sha512 -hmac "da78a39e9dda31c399bcc293d997a347dc7fd0408cb5151931243a302b273ec3238510ea61e11f7c"
SHA2-512(stdin)= b4f03574d264ffbaa37eadd8460f50dbb9ae6f12d4852a46d8654d472838aaa1de99248e958c904333e61738a00462d49f32bcd3258d8a3defca8c73b8d60d09
```
Perform the API request using curl:

```bash
curl --location -X POST 'https://demo-indodax.com/tapi/countdownCancelAll' \
--header 'Key: LSCE7NJG-JACRNTBX-D834R4UG-KMMTV8OP-PS1NHRBA' \
--header 'Sign: b4f03574d264ffbaa37eadd8460f50dbb9ae6f12d4852a46d8654d472838aaa1de99248e958c904333e61738a00462d49f32bcd3258d8a3defca8c73b8d60d09' \
--header 'Content-Type: text/plain' \
--data 'pair=btc_idr,eth_idr&countdownTime=10000&timestamp=1578304294001&recvWindow=1578303937000'
```

### POST /countdownCancelAll 
Cancel all open orders of the specified pair at the end of the specified countdown.

### **Request Parameters**

You can choose to fill either the `nonce` field or the `recvWindow` and `timestamp` fields.

| Parameter       | Type                                         | Mandatory | Description                                                                                                                                                      |
| --------------- | -------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| `countdownTime` | `integer` | Optional (default: `5000`) | Countdown time in milliseconds. Use `1000` for 1 second. Set to `0` to cancel the timer.                                                                          |
| `pair`          | `string` | Required | Specifies the trading pair(s). Use a comma separator for multiple pairs. Example values: `btc_idr`, `btc_idr,eth_idr`, `btc_idr%2Ceth_idr` (URL encoded).        |
| `timestamp`     |   `integer`    | Optional (required if `nonce` is empty) | The millisecond timestamp of when the request was created and sent.                                                                                               |
| `recvWindow`    |   `integer`    | Optional (required if `nonce` is empty) | Specifies how many milliseconds after the timestamp the request is valid. The request is valid between `timestamp` and `timestamp + recvWindow`. Default is `5000` ms. |
| `nonce`         | `integer` | Optional (required if `timestamp` and `recvWindow` are empty) | An incremental integer. For example, if the last request's `nonce` was `1000`, the next request should be `1001` or a larger number.    

### **Response**
Success
```json
"HttpCode": 200
"Response Body":
{
  "success": 1
}
```
Error
```json
"HttpCode": 200
"Response Body":
{
  "success": 0,
  "error": "API key not found in header",
  "error_code": "key_not_found"
}
```
### **Error Codes**
| Error Code              | Error Message                              |
| ----------------------- | ------------------------------------------ |
| `internal_server_error` | Failed to process request                  |
| `internal_server_error` | Validation signature failed.               |
| `bad_request`           | Pair `btc_idr` disabled                    |
| `bad_request`          | Invalid pair                               |
| `bad_request`           | Invalid countdown time                     |
| `bad_request`           | Pair is empty                              |
| `sign_not_found`        | Sign not found in header.                  |
| `key_not_found`         | API key not found in header.               |
| `bad_sign`              | Invalid credentials. Bad sign.             |
