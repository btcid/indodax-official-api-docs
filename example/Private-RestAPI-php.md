**Table of Contents**
- [Get Info](#get-info)
- [Transaction History](#transaction-history)
- [Trade](#trade)
- [Trade History](#trade-history)
- [Open Orders](#open-orders)
- [Order History](#order-history)
- [Get Order](#get-order)
- [Cancel Order](#cancel-order)
- [Withdraw Coin](#withdraw-coin)

# Example Request By PHP
## Get Info
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
	        'method' => 'getInfo',
	        'timestamp' => '1578304294000',
	        'recvWindow' => '1578303937000'
	    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);
    
    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Transaction History
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
        'method' => 'transHistory',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000'
    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Trade
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
        'method' => 'trade',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000',
        'pair' => 'btc_idr',
        'type' => 'sell',
        'price' => '107202000',
        'idr' => '',
        'btc' => '0.00313482'
    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Trade History
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
    $data = [
        'method' => 'tradeHistory',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000',
        'count' => '',
        'from_id' => '',
        'end_id' => '',
        'order' => '',
        'since' => '',
        'end' => '',
        'pair' => ''
    ];
    $post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Open Orders
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
        'method' => 'openOrders',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000',
        'pair' => 'btc_idr'
    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Order History
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
        'method' => 'orderHistory',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000',
        'pair' => 'btc_idr',
        'count' => '',
        'from' => ''
    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Get Order
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
        'method' => 'getOrder',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000',
        'pair' => 'btc_idr',
        'order_id' => '59639504'
    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Cancel Order
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
        'method' => 'cancelOrder',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000',
        'pair' => 'btc_idr',
        'order_id' => '59977301',
        'type' => 'buy'
    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Withdraw Fee
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
        'method' => 'withdrawFee',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000',
        'currency' => 'eth'
    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```

## Withdraw Coin
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    // Please find Key from trade API Indodax exchange
    $key = '****';
    // Please find Secret Key from trade API Indodax exchange
    $secretKey = '****';
    
	$data = [
        'method' => 'withdrawCoin',
        'timestamp' => '1578304294000',
        'recvWindow' => '1578303937000',
        'currency' => 'doge',
        'withdraw_address' => 'D9iCdBLBosJzGSvpQGMSobwtdgB2rS1zam',
        'withdraw_amount' => '10',
        'withdraw_memo' => 'memo',
        'request_id' => 'trx002'
    ];
	$post_data = http_build_query($data, '', '&');
    $sign = hash_hmac('sha512', $post_data, $secretKey);

    $headers = ['Key:'.$key,'Sign:'.$sign];

    $curl = curl_init();

    curl_setopt_array($curl, array(
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_URL => $url,
        CURLOPT_POST => true,
        CURLOPT_POSTFIELDS => $data,
        CURLOPT_RETURNTRANSFER => true
    ));

    $response = curl_exec($curl);

    curl_close($curl);
    echo $response;
```