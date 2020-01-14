**Table of Contents**
- [Get Info Endpoints](#get-info-endpoints)
- [Transaction History Endpoints](#transaction-history-endpoints)
- [Trade Endpoints](#trade-endpoints)
- [Trade History Endpoints](#trade-history-endpoints)
- [Open Orders Endpoints](#open-orders-endpoints)
- [Order History](#order-history)
- [Get Order Endpoints](#get-order-endpoints)
- [Cancel Order Endpoints](#cancel-order-endpoints)
- [Withdraw Coin Endpoints](#withdraw-coin-endpoints)

# Example Request By PHP
## Get Info Endpoints
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $secretKey = 'f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d';
    
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

## Transaction History Endpoints
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $secretKey = 'f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d';
    
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

## Trade Endpoints
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $secretKey = 'f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d';
    
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

## Trade History Endpoints
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $sign = 'dd3655b2b638c9f645fe8ad51089646f186600492864540e88fa23b6632c6d04d48891ef1a274a4c2139ba1c9e710b4fc32d59c4492ecb72189b4d2d630e14ef';
    $headers = ['Key:'.$key,'Sign:'.$sign];
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

## Open Orders Endpoints
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $secretKey = 'f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d';
    
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
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $secretKey = 'f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d';
    
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

## Get Order Endpoints
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $secretKey = 'f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d';
    
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

## Cancel Order Endpoints
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $secretKey = 'f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d';
    
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

## Withdraw Coin Endpoints
Sample code below :
```php
<?php
    $url = 'https://indodax.com/tapi';
    $key = 'AEDHIGAT-QATEGWOX-OPCSCPQX-2E00B1L7-VJBXXKMA';
    $secretKey = 'f60617a68fcce028f0a90bc9eb765d17379eb548cc935c01a7ee3186eecf870e9b68f27a31bcfe8d';
    
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