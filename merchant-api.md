# Merchant API

商户是由 Fox.ONE 来创建的，请联系客服人员。

会分配 `key/secret` 给到商户开发者，然后通过加密生成 JWT 和 API 通讯。

<br>
## ▎HOST

* 测试环境：https://dev-gateway.fox.one
* 生产环境：https://openapi.fox.one

<br>
## ▎请求签名 & HTTP Authorization


### > 请求签名

出于安全的考虑，每一个请求都要附带一个签名（sign），来防止请求被篡改，同时为防重放攻击，所有请求中均需包含 _ts, _nonce 参数，其中 _ts 为当前时间，与服务器时间不得相差半个小时；_nonce 可以使用随机的 uuid，一个小时内不得重复。_ts, _nonce 参数可在 query 里，也可在 json body 里。


#### - 签名示例 golang

请求的签名为 base64 (sha256(http_method+uri+payload))


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

### > HTTP Authorization

鉴权使用 JWT，HTTP 请求时，在 Header 中添加 "Authorization: Bearer token"

#### - JWT 使用说明

* JWT 的签名算法为 `HS256 (HMAC with SHA-256)`
* 签名密钥为 Merchant secret（由 Fox.ONE  签发）

#### - JWT Payload

```javascript
{
    "exp": 11111,  // 过期时间，unix timestamp
    "key": "xxx",  // memrchant key
    "sign": "xxx", // 请求签名
    "mem": "xxx"   // optional, member id, 只在指定用户的请求时需要。如查询某用户余额
}
```


<br>
## ▎API 使用说明

### > Validate Token

> 验证 Token 有效性

- URL: /merchant/validate
- Method: POST
- Params:

```json
{
  "uri": "/api/xxx",
  "method": "POST",
  "body": "xxx",
  "token": "xxxx"
}
```

#### - Return:

```json
{
  "merchant_id": "xxx",
  "member_id": "xxx"
}
```

### > Merchant Services

> 商户开启的业务

- URL: /merchant/services
- Method: GET


#### - Return:

```javascript
[{
    "merchant_id": "xxx",
    "service": "exchange",
    "wallet_id": "xxx",
    "status": 1, // 1 OK; 2 Frozen
    "created_at": 1234,
}]
```

### > 创建用户

> 商户创建用户

- URL: /merchant/member/new
- Method: POST

#### - Return:

```javascript
{  
   "member":{  
      "id":"987aeabe84654087bb3cb5ca602c7b1f",
      "created_at":1562125853,
      "is_pin_set":false
   },
   "session":{  
      "key":"874869407833634c678ff766dbc4b472-987aeabe84654087bb3cb5ca602c7b1f",
      "secret":"79ce901ee75bc9afc48856b0c3109370",
      "created_at":1562125853,
      "expired_at":1562212253
   },
   "wallets":[  
      {  
         "label":"",
         "member_id":"987aeabe84654087bb3cb5ca602c7b1f",
         "service":"payment",
         "wallet_id":"874ac4c9-0042-3099-8e2e-28111928510a",
         // the session_id and session_key are only available when you create a member
         // the wallet_id 、 session_id and session_key can be used to access Mixin Api
         "session_id":"75bd97b5-724b-4f54-bcde-a412fd776794",
         "session_key":"-----BEGIN RSA PRIVATE KEY-----\nMIICXAIBAAKBgQDZ2RYv1EM4sqXB7YURO7YgMPCih3OO+G6ERZpxYcPItI34fXa/\nUo9aLvi1doiyONM1F40lE9fr1Ahx6rmmzJPPNFcKH6lhDlenSWKVrg4KKFcK/EMD\nxHgGZxzfJB3sPOWLnCIgNwk1rffQGVcumWF3E8aND00Nl7D1IVlLk2N3OwIDAQAB\nAoGATp0adpQg1fsR+hOeq4Niy+cdT2mV+AgKyczcWQIwxuLxQLT1/0Dp3l+I/OMT\nnU0IWuZu1ux8ROw1R/aunFTDGZ5OMUE9kr3w5xDdMV7cdTxJNwweUJW6Hz6raoe3\nTheR8J7PHkDp2tObU69PrrZrUScj3v8Eybpy7R6v4/jDg5ECQQDe8/nNjJkuUUqC\nVuCjyEylteulvSiC7hGF85tHUgWZ9HZJ5tM2Uww39qONYJzv3CzIZGS2pomtz06b\n7GJn9UPNAkEA+iNmDp+VogacfkJCn9NuqAi4Z2qBkETSFLjZqWG8btqQ82lhTyqi\n+57wNDRriYBb3lkFLbkEYyb/PkEK72GvJwJABWAEabw2BTPYhAPsLoapsmUMZVaG\nH4H10jDpUXLcx7VpFKcH+ItQBBliIApwPigkvEAPXYfuUc5pqsCsLq1vEQJBANwA\nUF3iPDAiknd1/bUmt/ewm8fRZB0oeoFhR4dzf9EcCUsdT0na3ThjxS6VQFPSgnqg\nXy6kwNgYT3xIpr5+cxcCQA8f2VcFt/3QT/uOpmQtA4AbmH/A7fbycZ1RG7I13FtH\nROhqSZrBCS2IYdYJ+RrNJhhW6Nmu21mEvu7fwFNVu+g=\n-----END RSA PRIVATE KEY-----\n"
      }
   ]
}
```


### > 登陆用户

- URL: /merchant/member/login
- Method: POST
- Params:

```javascript
{
    "id": "member_id",
    "expire": 1111 // active duration in seconds
}
```

#### - Return:

```javascript
{
    "member": {
        "id": "xxx",
        "created_at": 123,
        "is_pin_set": false,
    },
    "session": {
        "key": "xxx",
        "secret": "xxx",
        "created_at": 123,
        "expired_at": 123,
    }
}
```

### > 登出用户

- URL: /merchant/member/logout
- Method: POST

#### - Params:

```javascript
{
    "id": "member_id",
    "session_key": "xxx" // optional
}
```

#### - Return:

```json
{}
```



### > 查询用户业务钱包列表

- URL: /merchant/member/services?member_id=xxx&service=xxx
- Method: GET

#### - Return:

```javascript
[{
    "member_id": "xxx",
    "wallet_id": "xxx",
    "service":   "exchange",
    "label":     "xxx",
}]
```

> service为可选参数。若不传则查询所有


### > 查询用户转账记录

- URL: /merchant/member/snapshots
- Method: GET

#### - Param: 

?service=exchange&asset_id=xxx&cursor=xxx&order=ASC|DESC&limit=50

#### - Return:

```javascript
{
    "snapshots" : [{
      "snapshot_id": "56048f80-ff17-44a2-81ea-bd96840cdec5",
      "trace_id": "1e6c2635-11de-34df-bc12-f10803895e06",
      "wallet_id": "8256c737-a11a-3b0d-bd25-9e18aac7db96",
      "asset_id": "c94ac88f-4671-3976-b60a-09064f1811e8",
      "opponent_id": "91e067e6-4bfd-309f-9d46-5cf06a778a6c",
      "source": "MATCH",
      "amount": "0.00004884",
      "memo": "haFBsJZeXG5DTD+pt4DFD0PNlVyhQ6NBU0uhT7DHgfWrBz5LJ5KFdTU4evAboVCpMC4wMDAwMDE0oVOlTUFUQ0g=",
      "member_id": "137b4c927fc1441a8524345e7c532a18",
      "service": "exchange",
      "created_at": 1551376859938,
      "asset": {
        "asset_id": "c94ac88f-4671-3976-b60a-09064f1811e8",
        "asset_key": "0xa974c709cfb4566686553a20790685a47aceaa33",
        "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
        "name": "Mixin",
        "symbol": "XIN",
        "icon_url": "https://www.fox.one/assets/coins/xin.png",
        "price": "956.79711445",
        "price_usd": "143.179558669",
        "change": "0.00588117"
      }
    }],
    "pagination" : {
        "next_cursor" : "MTU1MTM1NTU0MDA4ODQ0NjAwMDsx",
        "has_next" : true
    }
}
```

> service 必选，可选 exchange,otc,payment...
