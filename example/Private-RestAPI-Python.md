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

# Example Request By Python 3
## Get Info
Sample code below :
```python
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'getInfo',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000'
        }

post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```

## Transaction History
Sample code below :
```python
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'transHistory',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000'
        }

post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```

## Trade
Sample code below :
```python
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'trade',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000',
        'pair' : 'btc_idr',
        'type' : 'sell',
        'price' : '107202000',
        'idr' : '',
        'btc' : '0.00313482'
        }

post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```

## Trade History
Sample code below :
```python
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'tradeHistory',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000',
        'count' : '',
        'from_id' : '',
        'end_id' : '',
        'order' : '',
        'since' : '',
        'end' : '',
        'pair' : ''
        }

post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```

## Open Orders
Sample code below :
```python
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'openOrders',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000',
        'pair' : 'btc_idr'
        }

post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```

## Order History
Sample code below :
```python
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'orderHistory',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000',
        'pair' : 'btc_idr',
        'count' : '',
        'from' : ''
        }

post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```

## Get Order
Sample code below :
```python
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'getOrder',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000',
        'pair' : 'btc_idr',
        'order_id' : '59639504'
        }

post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```

## Cancel Order
Sample code below :
```php
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'cancelOrder',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000',
        'pair' : 'btc_idr',
        'order_id' : '59977301',
        'type' : 'buy'
        }

post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```

## Withdraw Coin
Sample code below :
```php
from urllib.parse import urlencode, quote_plus
import hashlib
import hmac
import time
import requests as rq
import json

url = 'https://indodax.com/tapi';
# Please find Key from trade API Indodax exchange
key = '****';
# Please find Secret Key from trade API Indodax exchange
secretKey = '****';

data = {
        'method' : 'withdrawCoin',
        # 'timestamp' : str(int(time.time()+0.5 * 1000)),
        'timestamp' : '1578304294000',
        # 'recvWindow' : str(int(time.time() * 1000))
        'recvWindow' : '1578303937000',
        'currency' : 'doge',
        'withdraw_address' : 'D9iCdBLBosJzGSvpQGMSobwtdgB2rS1zam',
        'withdraw_amount' : '10',
        'withdraw_memo' : 'memo',
        'request_id' : 'trx002'
        }

	post_data = urlencode(data, quote_via=quote_plus).encode('utf8')
sign = hmac.new(secretKey,post_data,digestmod=hashlib.sha512).hexdigest()

header = {
        'Key': key,
        'Sign': sign
        }

result = rq.post(url,data, headers=header).json()
print (json.dumps(result,indent=4))
```
