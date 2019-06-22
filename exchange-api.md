[TOC]

# REST接口

## info
  host  _```https://openapi.fox.one```_
## table

|attribute| method | uri                                                 |  description              |
|--------|--------|-----------------------------------------------------|---------------------------|
| public | GET    | [/exchange/market/assets](#assets)                  | 平台支持的币种              |
| public | GET    | [/exchange/market/pairs](#pairs)                    | 平台支持的交易对             |
| public | GET    | [/exchange/kline](#kline)                           | k线                        |
| public | GET    | [/exchange/depth](#depth)                           | 深度                        |
| public | GET    | [/exchange/trades](#trades)                         | 成交历史                     |
| public | GET    | [/exchange/ticker/24h](#ticker)                     | ticker                    |
| private| GET    | [/member/exchange/assets](#money)                   | 查询资产                    |
| private| GET    | [/member/exchange/orders](#query_orders)            | 查询订单                    |
| private| POST   | [/member/exchange/order](#create_order)             | 下单                       |
| private| GET    | [/member/exchange/order](#get_order)                | 查看订单                    |
| private| DELETE | [/member/exchange/order/:id](#cancel_order)         | 撤单                       |
| private| GET    | [/member/exchange/order/:id/trades](#order_trades)  | 查询订单成交记录             |
| private| POST   | [/member/exchange/listen_key](#get_listen_key)      | 生成listen key，用于获取长链接信息|
| private| PUT    | [/member/exchange/listen_key](#reset_listen_key)    | 重置listen key |
| private| DELETE | [/member/exchange/listen_key](#delete_listen_key)   | 删除listen key |

## signature
  1. 基于安全考虑，所有private类型接口的请求都必须进行签名，并在请求的头部添加 "Authorization: Bearer token", 其中token为生成的jwt。
  2. jwt的payload中包含三个字段exp, sign, key，其中exp为过期时间(Unix time),用户自行指定。sign为请求的method,uri,body进行SHA-256运算并base64后的结果。
  3. jwt的签名算法为HS256 (HMAC with SHA-256),签名私钥为字节形式的secret。key, secret由商户申请或用户登录后给出。
  4. 为防重放攻击，所有请求中均需包含_ts,_nonce参数，其中_ts 为当前时间，与服务器时间不得相差半个小时；_nonce 可以使用随机的 uuid，一个小时内不得重复。在GET、DELETE请求中_ts,_nonce放在query里，POST、PUT等放在body里。

生成签名的golang代码如下：

```go
func SignAuthenticationToken(method, uri, body string, key, secret string) (string, error) {
	expire := time.Now().Add(time.Hour * 24 * 30 * 3)
	sum := sha256.Sum256([]byte(method + uri + body))
	claims := jwt.MapClaims{
        "exp":  expire.Unix(),
        "sign": base64.StdEncoding.EncodeToString((sum[:])),
        "key":  key,
	}

	token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	return token.SignedString([]byte(secret))
}
```

以创建订单为例, 
给定
```
    key = f2769d0dac654385aefe96b69feba206bxd 
    secret = f2ea74e96433ecef3b2e0c59bce97539 
```
请求参数
    method = POST
    uri =  /member/exchange/order
    body:
```javascript
{   "_nonce": "94e988fc-d5cc-4080-9cff-dcb25b3fc2e7",
    "_ts":    1553242283,
    "amount": "0.4",
    "price":  "3.5",
    "side":   "ASK",
    "symbol": "EOSUSDT",
    "type":   "LIMIT"
}
```
则jwt中payload为
```javascript
{
  "exp": 1561018283,
  "key": "f2769d0dac654385aefe96b69feba206bxd",
  "sign": "aCi3bB/RJYW28W1VreBqy5Ij/BfXLkOI7tz4f2o77dA="
}
```

用secret签名后生成的token如下
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjEwMTgyODMsImtleSI6ImYyNzY5ZDBkYWM2NTQzODVhZWZlOTZiNjlmZWJhMjA2YnhkIiwic2lnbiI6ImFDaTNiQi9SSllXMjhXMVZyZUJxeTVJai9CZlhMa09JN3R6NGYybzc3ZEE9In0.htm-X4jAXMLSabvZePdoBlELHTfm1glapBp1Sa12sko
```

可以到这里[https://jwt.io/](https://jwt.io/)查看并验证签名

cURL Example
```
curl -i -H "Content-Type: application/json" -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjEwMTgyODMsImtleSI6ImYyNzY5ZDBkYWM2NTQzODVhZWZlOTZiNjlmZWJhMjA2YnhkIiwic2lnbiI6ImFDaTNiQi9SSllXMjhXMVZyZUJxeTVJai9CZlhMa09JN3R6NGYybzc3ZEE9In0.htm-X4jAXMLSabvZePdoBlELHTfm1glapBp1Sa12sko" https://openapi.fox.one/member/exchange/order -X POST --data '{"_nonce": "94e988fc-d5cc-4080-9cff-dcb25b3fc2e7","_ts":1553242283,"amount": "0.4","price": "3.5","side": "ASK","symbol": "EOSUSDT","type": "LIMIT"}'
```

## details

1. <span id="assets">GET /exchange/market/assets </span>


* cURL example
```
    curl -i https://openapi.fox.one/exchange/market/assets
```

* response 

```javascript
[{  "asset_id":"815b0b1a-2764-3736-8faa-42d694fa620a",   //币种ID
    "chain_id":"c6d0c728-2624-429b-8e0d-d9d19b6592fa",   //币种基于的主链ID,c6d0c728-2624-429b-8e0d-d9d19b6592fa表示BTC
    "detail":{
        "circulating_supply":2806421736,
        "description":"Tether is an unregulated cryptocurrency token that was issued on the Bitcoin blockchain (before transitioning to the Litecoin blockchain) through the Omni Layer Protocol. Although Tether Limited has promised but continued to refuse or failed to make any audits publicly available, they claim that each unit of Tether is backed by one United States dollar held in reserve by Tether Limited, and may be redeemed through the Tether Platform.\r\n\r\nThe primary objective is to facilitate transactions with a rate fixed to the United States Dollar to circumvent government intervention and regulatory oversight.",
        "explorer_url":
        "http://omnichest.info/lookupsp.aspx?sp=31",
        "ico_date":1416988739,
        "max_supply":2580109970,
        "mineable":false,
        "official_url":"https://tether.to/",
        "repo_url":"",
        "services_url":"",
        "total_supply":3080109502,
        "white_paper_url":"",
        "wiki_url":"",
        "tags":[{"logo":"https://www.fox.one/assets/tags/Payment.png","tag":"Payment","description":"Payment, Wallet and Trading."}]},
    "icon_url":"https://www.fox.one/assets/coins/usdt.png",
    "name":"Tether",
    "precision":8,                                       //数量精度为8位，最小数量为小数点后8位
    "symbol":"USDT",
    "type":"asset"
    }]
```
 
 2. <span id="pairs">GET /exchange/market/pairs</span>

* cURL example
```
    curl -i https://openapi.fox.one/exchange/market/assets
```

* response
```javascript
[{
    "symbol":"EOSUSDT",                                     //交易对符号，唯一
    "logo":"https://www.fox.one/assets/coins/eos.png",
    "base_asset_id":"6cfe566e-4aad-470b-8c9a-2fd35b49c68d", 
    "base_asset":"EOS",
    "quote_asset_id":"815b0b1a-2764-3736-8faa-42d694fa620a",
    "quote_asset":"USDT",
    "price_precision":8,                                    //价格精度为8位
    "amount_precision":4,                                   //数量精度为4位
    "status":"TRADING",                                     //交易对状态，TRADING目前可以交易
    "base_min_amount":"0.0001",                             //单笔交易最少0.0001个EOS
    "base_max_amount":"500000",                             //单笔交易最多500000个EOS
    "quote_min_amount":"1",                                //单笔交易最少1个USDT
    "quote_max_amount":"50000000000"                        //单笔交易最多50000000000个USDT
}]
//注:交易对的下单数量限制为"且"的关系，即单笔交易不得少于0.0001个EOS，算上价格后同时不得少于1个USDT。
```

3. <span id ="kline">GET /exchange/kline </span>

* parameters
   
| parameters | type        | example             | description                             |
|------------|-------------|---------------------|------------------------------------     |
| symbol     | string      | EOSUSDT             | 交易对符号，必填                          |
| interval   | string      |   1m                | 时间间隔，必填，支持1m、5m、15m、1h、6h、1d   |
| limit      | int         |   10                | 查询的最多条数                       |
| from       | int64       |1553227800000        | 查询的起始时间, 单位ms                |               
| to         | int64       |1553228340000        | 查询的终止时间, 单位ms                |


* cURL example
```
curl https://openapi.fox.one/exchange/kline?symbol=EOSUSDT&interval=1d&limit=10
```

* response
```javascript
[  //时间,开盘价，最高价，最低价，收盘价，成交量
    [1552435200000,"3.6377","3.6443","3.512","3.5697","879.4342"],
    [1552521600000,"3.5697","3.6336","3.5044","3.578","1074.4232"],
    [1552608000000,"3.578","3.7118","3.5755","3.6845","1149.2457"],
    [1552694400000,"3.6868","3.838","3.6849","3.7963","669.4297"],
    [1552780800000,"3.7958","3.799","3.7209","3.7376","231.7639"],
    [1552867200000,"3.7253","3.787","3.6865","3.7094","118.2907"],
    [1552953600000,"3.7078","3.7386","3.6779","3.7257","525.6301"],
    [1553040000000,"3.726","3.7324","3.5418","3.6791","232.4148"],
    [1553126400000,"3.6791","3.68","3.5537","3.6014","265.1845"],
    [1553212800000,"3.5","3.5","3.5","3.5","0.4"]
]
```

4. <span id="depth">GET /exchange/depth </span>

* parameters
  
| parameters | type        | example | description    |
|------------|-------------|---------|-------------   |
| symbol     | string      | EOSUSDT |  交易对符号      |
| limit      | int         |   10    |前limit档买卖单   |

* cURL example
```
curl https://openapi.fox.one/exchange/depth?symbol=EOSUSDT&interval=1m&limit=10
```
* response
```javascript
{
    "last_update_id":758069,
    //买单
    "bids":[],    
    //卖单: 价格  数量          
    "asks":[["3.68","0.365"],
            ["3.6805","0.5"],
            ["3.6807","0.5"],
            ["3.6808","0.5"],
            ["3.6811","0.5"],
            ["3.691","0.5"],
            ["3.6911","0.5"],
            ["3.6912","0.755"],
            ["3.6914","0.94"],
            ["3.6944","1.5"]]
}
```

5. <span id="trades">GET /exchange/trades </span>

* parameters

| parameters | type        | example | description |
|------------|-------------|---------|-------------|
| symbol     | string      | EOSUSDT |  交易对符号   |
| limit      | int         |   10    | 获取的记录条数 |

* cURL example

```
curl https://openapi.fox.one/exchange/trades?symbol=EOSUSDT&limit=10
```

* response
```javascript
[
    //成交ID，成交时间,成交价格，成交数量，成交方向(以买单成交或卖单成交)
    {"id":"551885ad-9c9a-3d40-94b8-53c974cdaaaf","time":1553199482167,"price":"3.6014","amount":"0.0002","side":"BID"},
    {"id":"4dcb0fb5-1fba-3691-9c14-9cbf24019b30","time":1553199482078,"price":"3.6014","amount":"0.4998","side":"BID"},
    {"id":"9c8b109e-eef6-3f3c-82aa-01cfce59ec8c","time":1553199461450,"price":"3.6014","amount":"0.0002","side":"BID"},
    {"id":"2addf39c-b224-3b29-8f46-62953e4ec04b","time":1553199461361,"price":"3.6014","amount":"0.4998","side":"BID"},
    {"id":"4f791762-d11d-32e5-91cc-c620a0376ac3","time":1553199441239,"price":"3.6014","amount":"0.0002","side":"BID"},
    {"id":"0944d0ab-9f2c-37f0-86b5-0bbe77b15911","time":1553199441149,"price":"3.5994","amount":"0.5","side":"BID"},
    {"id":"85ccae20-8c00-3ea3-9b78-dbd7a6dac4e9","time":1553199256087,"price":"3.5986","amount":"0","side":"BID"},
    {"id":"21665363-4dfd-3884-bb7f-a2e09abf2a59","time":1553199256000,"price":"3.5986","amount":"0.5","side":"BID"},
    {"id":"aa97c599-ba96-380b-a490-2d522fb78789","time":1553199008421,"price":"3.5985","amount":"0.5","side":"ASK"},
    {"id":"481f10a9-411d-3b64-8be1-0721bf016867","time":1553198987756,"price":"3.5985","amount":"0.5","side":"ASK"}
]
```

6. <span id="ticker">GET /exchange/ticker/24h </span>

* parameter
   
| parameters | type        | example | description |
|------------|-------------|---------|-------------|
| symbol     | string      | EOSUSDT |  交易对符号   |

* cURL example

```
curl https://openapi.fox.one/exchange/ticker/24h?symbol=EOSUSDT
```

* response
```javascript
{   "price":"3.6014",                                //最近一笔交易成交价
    "price_change":"-0.047",                         //24h内成交价变化量
    "price_change_percent":"-0.0128823593904177",    //24h内成交价变化率
    "open_time":1553146200000,                       //开盘时间
    "open_price":"3.6484",                           //开盘价
    "high_price":"3.68",                             //最高价
    "low_price":"3.5537",                            //最低价
    "volume":"265.1844999999999",                    //24h base 成交量
    "quote_volume":"952.5584435700002"               //24h quote 成交量
}
```

7. <span id="money">GET /exchange/assets </span>

* response
```javascript
  [{
    "account_name": "",
    "account_tag": "",
    "asset_id": "c6d0c728-2624-429b-8e0d-d9d19b6592fa",         //币种ID
    "balance": "0.00028711",                                    //账户余额
    "chain_id": "c6d0c728-2624-429b-8e0d-d9d19b6592fa",
    "change": "-0.00954034",
    "icon_url": "https://www.fox.one/assets/coins/btc.png",
    "name": "Bitcoin",
    "price": "27008.0747514",
    "price_usd": "4029.31250365",
    "public_key": "1Jr3xc8LLfgr9S3EyLiASHUhpPa13joCKe",         //充值地址
    "symbol": "BTC"
  },
  {
    "account_name": "",
    "account_tag": "",
    "asset_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "balance": "0.00613338",
    "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "change": "-0.02142155",
    "icon_url": "https://www.fox.one/assets/coins/eth.png",
    "name": "Ether",
    "price": "917.92987781",
    "price_usd": "136.945205024",
    "public_key": "0xd1FBa29123f8A4408AfD552B2891c84348b35e4A",
    "symbol": "ETH"
  },
    {
    "account_name": "eoswithmixin",                               //EOS账户地址
    "account_tag": "cb1773b3837547eee1264d337c56f2dd",            //EOS账户备注
    "asset_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",
    "balance": "0.17258047",                                     
    "chain_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",
    "change": "-0.02702369",
    "icon_url": "https://www.fox.one/assets/coins/eos.png",
    "name": "EOS",
    "price": "24.3407216",
    "price_usd": "3.63137227604",
    "public_key": "",
    "symbol": "EOS"
  }]
```

8. <span id="query_orders">GET /member/exchange/orders </span>

* parameters

| parameters | type        | example | description                    |
|------------|-------------|---------|--------------------------------|
| symbol     | string      | EOSUSDT |  交易对符号                      |
| state      | string      | PENDING | 订单待成交(PENDING)或已完结(DONE) |
| offset     | int         | 0       | 偏移量                          |
| limit      | int         | 10      | 返回记录的最大值                  |
| order      | string      | ASC     | 按时间正序(ASC)或逆序(DESC)       |


* cURL example
```
curl -i -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjEwMTE2NjgsImtleSI6IjkxYzkzMmVlZmJlNTQ0ZjY0NDhlMzAyMTlmMmY3ZmVjNm9WIiwic2lnbiI6InQ4QjNpTkJNeDVKdk9JK0ZKYnZSdDJWbXhiV2xqUmhDVy9INi9OSUxxR0k9In0.QHJDFqUCT0LBkBaBE7lsZonx_G3fwp68DH-AlH7-awM"
https://openapi.fox.one/member/exchange/orders?order=DESC&state=DONE&limit=3&offset=2000&_ts=1553235668&_nonce=e23b5e45-4af3-4f6a-91bb-82b5e3626c30
```

* response
```javascript
  "orders": [
    {
      "base_asset_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",             //base币ID
      "created_at": 1553189988000,                                         //订单创建时间
      "filled_amount": "0.3999",                                           //订单已成交数量
      "filled_fund": "0.00036083",                                         //订单已成交金融
      "id": "042a6295-a2e0-4ade-83fb-0139c4e04b5d",                        //订单ID
      "order_type": "LIMIT",                                               //订单类型，LIMIT限价单,MARKET市价单
      "price": "0.00090208",                                               //挂单价
      "quote_asset_id": "c6d0c728-2624-429b-8e0d-d9d19b6592fa",            //quote币ID
      "remaining_amount": "0",                                             //订单剩余数量      
      "remaining_fund": "0",                                               //订单剩余金额
      "side": "BID",                                                       //订单方向，BID买，ASK卖
      "state": "DONE",                                                     //订单状态，DONE已完成，PENDING待成交
      "symbol": "EOSBTC",                                                  //币对符号
      "user_id": "dfbadace-2dd2-3139-b2eb-18dc8d4e8e57"                    //下单的钱包ID
    },
    {
      "base_asset_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",
      "created_at": 1553189986000,
      "filled_amount": "0",
      "filled_fund": "0",
      "id": "865c4600-2198-4631-8684-a09bf9645167",
      "order_type": "LIMIT",
      "price": "3.5746",
      "quote_asset_id": "815b0b1a-2764-3736-8faa-42d694fa620a",
      "remaining_amount": "0",
      "remaining_fund": "1.7873",
      "side": "BID",
      "state": "DONE",
      "symbol": "EOSUSDT",
      "user_id": "dfbadace-2dd2-3139-b2eb-18dc8d4e8e57"
    },
    {
      "base_asset_id": "43d61dcd-e413-450d-80b8-101d5e903357",
      "created_at": 1553189983000,
      "filled_amount": "0.0211",
      "filled_fund": "2.833519",
      "id": "9050ffb3-32cc-4aee-947e-0824e8a03db7",
      "order_type": "LIMIT",
      "price": "134.29",
      "quote_asset_id": "815b0b1a-2764-3736-8faa-42d694fa620a",
      "remaining_amount": "0",
      "remaining_fund": "0",
      "side": "ASK",
      "state": "DONE",
      "symbol": "ETHUSDT",
      "user_id": "dfbadace-2dd2-3139-b2eb-18dc8d4e8e57"
    }
  ],
  "total": 140431      //总记录数
}
```

9. <span id="create_order">POST /member/exchange/order </span>

* parameters

| parameters | type        | example | description                    |
|------------|-------------|---------|--------------------------------|
| symbol     | string      | EOSUSDT |  交易对符号                      |
| side       | string      | ASK     | 买单(BID)或卖单(BID)             |
| type       | string      | LIMIT   | 市价单(MARKET)或限价单(LIMIT)     |
| price      | string      | 3.5    | 价格,订单为市加单时价格必须是0      |
| amount     | string      | 0.4     | 要支付的币的数量                  |
注:上述参数含义为，卖单时，以3.5 EOS/USDT的价格卖出0.4个EOS; 买单时，以3.5 EOS/USDT的价格买入0.4个USDT，约0.1143个EOS

* response 

转账详情
```javascript q{
  "amount": "-0.4",                                          //支付的币数量
  "asset_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",        //支付的币种ID
  "created_at": 1553245127528602600,
  "memo": "hqFBsIFbCxonZDc2j6pC1pT6YgqhTbDD92yvy1UwhYWWAlqNh8uyoU+wAAAAAAAAAAAAAAAAAAAAAKFQozMuNaFToUGhVKFM",
  "opponent_id": "ec785609-a43c-3b18-a388-8772fcfe81ab",
  "snapshot_id": "f7ea8e6b-37e9-4892-ae38-235261b6f122",
  "source": "",
  "trace_id": "38044393-f742-4455-9250-12c44df560f3",         //订单ID
  "user_id": ""
}
```

10. <span id="get_order">GET /member/exchange/order/:id <span>
* response 
```javascript
{
  "base_asset_id": "6cfe566e-4aad-470b-8c9a-2fd35b49c68d",
  "created_at": 1553245127000,                          //订单创建时间
  "filled_amount": "0",                                //已成交数量
  "filled_fund": "0",                                  //已成交金额
  "id": "38044393-f742-4455-9250-12c44df560f3",        //订单ID
  "order_type": "LIMIT",                               //类型，市价单或限价单
  "price": "3.5",                                      //价格
  "quote_asset_id": "815b0b1a-2764-3736-8faa-42d694fa620a",
  "remaining_amount": "0.4",                           //待成交数量
  "remaining_fund": "0",                               //待成交金额
  "side": "ASK",                                      //订单方向
  "state": "PENDING",                                  //订单状态，PENDING待成交，DONE完结(已成交或已撤单)
  "symbol": "EOSUSDT",
  "user_id": "b6c6cb11-ead6-3e7e-80e9-f688a7333193"
}
```

11. <span id="cancel_order">DELETE /member/exchange/order/:id <span>
* response 
  ```javascript
  {}
  ```

12.  <span id="order_trades">GET /member/exchange/order/:id/trades <span>

* Response
```javascript
 [
  {
    "amount": "0.4",                                  //成交数量
    "id": "8ecc0b5c-fd37-32fd-9ad1-ef9b1209dc1e",     //成交ID
    "price": "3.5",                                   //成交价
    "side": "ASK",                                    //成交方向
    "time": 1553246631596                             //成交时间
  }
]
```

13. <span id="get_listen_key">POST /member/exchange/listen_key </span>
* response
```javascript
{"key":"f1124c337e5706e5c32a2580e35ce992ea97504f114af702a9aed96f91153d7b"}
```

14. <span id="reset_listen_key">PUT /member/exchange/listen_key </span>
* body
```javascript
{"key":"f1124c337e5706e5c32a2580e35ce992ea97504f114af702a9aed96f91153d7b"}
```
* response
```
{}
```

15. <span id="delete_listen_key">DELETE /member/exchange/listen_key/:key </span>
* response
```
{}
```

# websocket 接口

## info

1. host为 _```wss://openapi.fox.one```_
2. 订阅数据 ```/ws?topic=<topic>``` 或 ```/ws/?topics=<topic>,<topic>```
3. 每个长连接的有效期为 24h
4. 服务端每一分钟 ping 一次，客户端需要在三分钟之内回复 pong
5. 返回的数据经过gzip压缩，需要自行解压缩

## table

|attribute | type                 | topic                        | note                                         |example| 
|---       |----------------------|------------------------------| ---------------------------------------------|------------|
| public   |kline                 | symbol@kline_interval (#ws_kline)|interval可取1m,5m,15m,1h,6h,1d| BTCUSDT@kline_1m|
| public   |ticker                | symbol@ticker(#ticker)       |                                              | BTCUSDT@ticker|
| public   |all ticker            | ticker@all                   |                                              | ticker@all |
| public   |depth update          | symbol@depth                 |                                              | BTCUSDT@depth |
| public   |depth with level      | symbol@depth*                | *可取5,10,20                                  | BTCUSDT@depth5|
| public   |trade                 | symbol@trade                 |                                               |BTCUSDT@trade|
| private  |order and transaction | listen_key                   | 从[/member/exchange/listen_key](#get_listen_key)得到| f1124c337e5706e5c32a2580e35ce992ea97504f114af702a9aed96f91153d7b  |
注: 表中symbol均为交易对符号

## details

1. <span id="ws_kline">kline</span>

* Topic
```
    symbol@kline_interval
```

* response

```javascript
{
    "e":  "kline",
    "ts": 12345634,    // 推送时间,精确到秒
    "k" : {
        "s":  "BTCUSDT",   // symbol
        "t":  27678642783, // 这根 k 线开始时间
        "o":  "4000.23",   // 开盘价
        "h":  "4300.21",   // 最高价
        "l":  "3977.2",    // 最低价
        "c":  "4012.2",    // 最新价格
        "v":  "1234",      // base asset volume
        "V":  "234455",    // quote asset volume
        "i":  "1m",        // interval
    }
}
```

2. <span id="ws_24hr_ticker"> 24hr ticker  </span>

* topic 
```
    symbol@ticker
```

* response

```javascript
{
    "e":  "24hr_ticker",
    "ts": 12345634,    // 推送时间,精确到秒
    "k" : {
        "s":  "BTCUSDT",   // symbol
        "c": "4000.23",    // latest price
        "p": "30.2",       // price change
        "P": "0.034",      // price change percent
        "o": 767217636725, // 24hr ticker ticker open time
        "O" "3987.23",     // 24hr ticker open price
        "h":  "4300.21",   // high price
        "l":  "3977.2",    // low price
        "v":  "1234",      // base asset volume
        "V":  "234455",    // quote asset volume
    }
}
```

3. <span id="ws_24hr_tickers">24hr all tickers </span>

* topic 
```
    ticker@all
```

* response

```javascript
{
  "e":  "24hr_ticker_all",
  "ts": 12345634,    // 推送时间,精确到秒
  "k" : {
    "BTCUSDT" : {
        // Same as 24hr_ticker payload
    },
  }
}
```

4. <span id="ws_depth_update"> Depth Update </span>

* topic 
```
    symbol@depth
```

* response

```javascript
{
    "e":  "depth_update", // event
    "ts": 12334455, // 推送时间,精确到秒
    "k" : {
        "m":  "965e5c6e-434c-3fa9-b780-c50f43cd955c-815b0b1a-2764-3736-8faa-42d694fa620a", // ocean one market id
        "s":  "BTCUSDT", // symbol
        "U":  123,       // first update id
        "u":  125,       // last update id
        "b": [           // Bids to be updated
            [
            "0.0024",    // Price level to be updated
            "10",        // amount, if zero, remove this price, else overewrite the amount
            ]
        ],
        "a": [           // Asks to be updated
            [
            "0.0026",    // Price level to be updated
            "100",       // amount
            ]
        ]
    }
}
```

1. <span id="ws_depth_level">Depth with level </span>

* topic 
```
 symbol@depth*
```

* response

```javascript
{
    "e":  "depth", // event
    "ts": 12334455, // 推送时间,精确到秒
    "k" : {
        "m":  "965e5c6e-434c-3fa9-b780-c50f43cd955c-815b0b1a-2764-3736-8faa-42d694fa620a", // ocean one market id
        "s":  "BTCUSDT", // symbol
        "u":  125,       // last update id
        "b": [           // Bids to be updated
            [
            "0.0024",    // Price level to be updated
            "10",        // amount
            ]
        ],
        "a": [           // Asks to be updated
            [
            "0.0026",    // Price level to be updated
            "100",       // amount
            ]
        ]
    }
}
```

6. <span id=“ws_trade”>Trade </span>

* topic 
```
 symbol@trade
```

* response

```javascript
{
    "e":  "trade",
    "ts": 12334455,   // 推送时间,精确到秒
    "k" : {
        "m":  "965e5c6e-434c-3fa9-b780-c50f43cd955c-815b0b1a-2764-3736-8faa-42d694fa620a", // ocean one market id
        "s":  "BTCUSDT",  // symbol
        "t" : 12334455,   // trade time in ms
        "id": 123,        // trade id
        "p":  "4000.23",  // price
        "a":  "12",       // amount
        "S":  "ASK or BID", // taker side
    }
}
```

6. user order and transaction 

* topic 
```
  listen_key
```

* response

1. Order Open & Order Cancel

```javascript
{
    "e" : "ORDER-OPEN or ORDER-CANCEL",
    "ts" : 1234444,
    "k" : {
        "m" : "965e5c6e-434c-3fa9-b780-c50f43cd955c-815b0b1a-2764-3736-8faa-42d694fa620a", // ocean.one market
        "s" : "BTCUSDT", // symbol
        "p" : "4000.2",  // price
        "a" : "1.23",    // amount
        "S" : "ASK",     // order side
        "o" :  {
            "id": "order id",
            "ts": 1265256723, // order create time, ms
            "t":  "LIMIT or MARKET", // order type
            "m": "965e5c6e-434c-3fa9-b780-c50f43cd955c-815b0b1a-2764-3736-8faa-42d694fa620a", // ocean.one market
            "s": "BTCUSDT", // symbol
            "S": "ASK",     // order side
            "p": "40000.2", // price
            "x": "PENDING", // order state
            "r": "1.23",    // order remaining amount
            "f": "0.3",     // order filling amount
            "R": "0",       // order remain funds
            "F": "0",       // order filled funds
        }
    }
}
```

2.  Order Match

```javascript
{
    "e" : "ORDER-MATCH",
    "ts": 1234444,
    "k" : {
        "m" : "965e5c6e-434c-3fa9-b780-c50f43cd955c-815b0b1a-2764-3736-8faa-42d694fa620a", // ocean.one market
        "s" : "BTCUSDT", // symbol
        "p" : "4000.2",  // price
        "a" : "1.23",    // amount
        "o" : {
            "id": "order id",
            "ts": 1265256723, // order create time, ms
            "t":  "LIMIT or MARKET", // order type
            "m": "965e5c6e-434c-3fa9-b780-c50f43cd955c-815b0b1a-2764-3736-8faa-42d694fa620a", // ocean.one market
            "s": "BTCUSDT", // symbol
            "S": "ASK",     // order side
            "p": "40000.2", // price
            "x": "PENDING", // order state
            "r": "1.23",    // order remaining amount
            "f": "0.3",     // order filling amount
            "R": "0",       // order remain funds
            "F": "0",       // order filled funds
        }
    }
}
```

3. Transaction

```javascript
{
    "e":  "transaction",
    "ts": 1234555,
    "k": {
        "id": "965e5c6e-434c-3fa9-b780-c50f43cd955c-815b0b1a-2764-3736-8faa-42d694fa620a", // snapshot id
        "t":  12355666400, // transaction time
        "a":  "965e5c6e-434c-3fa9-b780-c50f43cd955c", // asset id
        "s":  "BTC", // asset symbol
        "A":  "0.23", // amount >0 表示转入，< 0 表示转出
        "m":  "memo", // 备注
        "S":  "sfdkfjdkfjkd", // sender wallet address, 只有 mixin 与外部转账才有
        "R":  "skljklfsdsdak", // receiver wallet address, 只有 mixin 与外部转账才有
        "h":  "ahjdkhdfjskhfjkahdsk", // transaction hash，只有 mixin 与外部转账才有
    },
}
```

