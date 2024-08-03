<details>
<summary>

 # Installing

</summary>
 
## Use this package as a library
### Depend on it

Run this command for:

With Dart:
```shell
 $ dart pub add indodax
```
With Flutter:
```shell
$ flutter pub add indodax
```

This will add a line like this to your package's pubspec.yaml (and run an implicit ```dart pub get```):
```yaml
dependencies:
  indodax: ^1.0.2
```

Alternatively, your editor might support ```dart pub get``` or ```flutter pub get```.

### Import it
Now in your Dart code, you can use:
```dart
import 'package:indodax/indodax.dart';
```

</details>

<details>
<summary>

 # Usage

</summary>

A simple asynchronous API Client Indodax

This library is implementing [Indodax Official API](https://github.com/btcid/indodax-official-api-docs).

[![Indodax Official API](https://indodax.com/homepage-assets/favicon.ico)](https://github.com/btcid/indodax-official-api-docs)

This package contains a set of high-level functions and classes that make it
easy to consume API.

Feel free to contribute!

https://github.com/wawan-ikhwan/indodax

## Using
1. Public API:

    You don't need authenticate for PublicAPI.

    Get server time example (Synchronous):
    ```dart
    import 'package:indodax/indodax.dart';

    void main(){
      PublicAPI.serverTime.then((t) {
        print(t.timeZone);
        print(t.time);
        });
    }
    ```

    Get server time example (Asynchronous):
    ```dart
    import 'package:indodax/indodax.dart';

    Future<void> main() async {
      var response = await PublicAPI.serverTime;
      print(response.timeZone);
      print(response.time);
    }
    ```
    Get summaries example (Asynchronous):
    ```dart
    import 'package:indodax/indodax.dart';

    Future<void> main() async {
      var response = await PublicAPI.summaries;
      print(response.prices7d);
      print(response.prices24h);
    }
    ```
2. Private API:

    You need **API_KEY** and **SECRET KEY** for Private API, you can make it with visit https://indodax.com/trade_api .

    Get info trader (ujang)
    ```dart
    import 'package:indodax/indodax.dart';

    Future<void> main() async {
      var ujang = PrivateAPI(
        key: 'OOZJORLL-XFEC6V3D-EDUZHELU-PHP8YF9F-GSSXV2K6',
        secret: 'b11c56f740d358b1640e17beb72bd3c137b58b02170aa4f3cf28327c3f87fb73cc4e6b3085b7f7f',
      );
      var infoUjang = await ujang.info
      print(infoUjang);
      ujang.close();
    }
    ```
    Or you can use valid **auth.json** file

    ```json
    {
      "API": "OOZJORLL-XFEC6V3D-EDUZHELU-PHP8YF9F-GSSXV2K6",
      "SECRET": "b11c56f740d358b1640e17beb72bd3c137b58b02170aa4f3cf28327c3f87fb73cc4e6b3085b7f7fb"
    }
    ```
    Then you can use specified path where you saved the file.
    ```dart
    import 'package:indodax/indodax.dart';

    Future<void> main() async {
      var ujang = PrivateAPI(authPath: './auth.json');
      var infoUjang = await ujang.info
      print(infoUjang);
      ujang.close();
    }
    ```
## Supported Method
1. PublicAPI:
    * open() -> Openning connection, actually it's closing then opening (reopenning).
    * close() -> Closing connection, it's possible to reopen using open().
    * serverTime -> Get current server time (millisecond since epoch).
    * pairs -> Get list of currency pairs (cryptoCurrency_fiatCurrency).
    * priceIncrements -> Get list of price increments.
    * getTicker(id) -> Get a ticker by pair_id.
    * tickerAll -> Get list of tickers.
    * summaries -> Get summaries.
    * getTrade(id) -> Get public trade history by pair_id.
    * getDepth(id) -> Get bid/ask depth by pair_id.
2. PrivateAPI:
    * close() -> Close connection. It's not possible to reopen, but you can create new trader (new object).
    * info -> Get info trader.
    * getTransHistory(start,end) -> Get transaction history.
    * trade(pair,type,price,cryptoAmount,fiatAmount) -> Do trade (open order or instant order)
    * getTradeHistory(pair,count,fromID,endID,order,since,end) -> Get trading history.
    * getOpenOrders(pair) -> Get current trader's book order.
    * getOrderHistory(pair,count,from) -> Get order history (canceled, success, etc).
    * getOrder(pair,orderID) -> get detail of order.
    * cancelOrder(pair,orderID,type) -> Do cancel order in current trader's book order.

## Does closing connection matter?
It's depends. 

PublicAPI class are static class so it's automatically implement client interface from http library.

PrivateAPI is object based, it's automatically implement client from http library when the object is created. We can say whenever object created we are creating trader because we created new client interface. So you can use multiple account in this private api as long the api credential is different.
But when the trader is not needed anymore, you can close the object so the client interface will close and save bandwidth.

So, keeping connection open is better for multiple request to server.


</details>

