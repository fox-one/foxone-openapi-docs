# 申请 App id 和 App Secret

这一步由 Fox.ONE 完成。需要接入方提供产品名字。

# API

## 签名

1. 生成时间戳和 nonce，时间戳精确到秒，nonce 建议用 uuid.
2. 使用 app id，时间戳和 nonce 构造待签名内容，格式为 appid={app id}&ts={timestamp}&nonce={nonce}
3. 用 hmac 计算上述内容的签名，key 为 app secret，hash 算法为 sha256
4. 最后再 base64 encode 一下

```go
// example with Go

h := hmac.New(sha256.New, []byte(secret))
payload := fmt.Sprintf("appid=%s&ts=%d&nonce=%s", appid, ts, nonce)
h.Write([]byte(payload))
sign := base64.StdEncoding.EncodeToString(h.Sum(nil))
```

## 创建用户

```
POST https://api.fox.one/api/broker/register
```

**Parameters:**

| Name      | Type   | Description          |
| --------- | ------ | -------------------- |
| appid     | string | app id               |
| timestamp | int    | timestamp            |
| nonce     | string | uuid nonce           |
| sign      | string | sign                 |
| trace_id  | string | unique trace id      |
| name      | string | user name (optional) |
| avatar    | string | avatar (optional)    |

> trace_id 是这一次创建用户的唯一标示，同一个 trace_id 会一直返回同一个用户的信息

**Response**

```javascript
{
  "code": 0,
  "data": {
    "expires_in": 5184000, // token 有效期
    "token": "dSw0eVYsZCw0ZyxlLDFnSmF2cQ==.B4MV7RMauEw101wQOTT7O/8uD4vC3D1k2KRXCLlR3OM=", // fox user token
    "user": {
      "avatar": "https://www.fox.one/123456.png",
      "fox_id": "f2e6ae4a-97c3-3012-99d8-5a715b0d75bd", // fox user id，可用于以后继续登录该用户
      "name": "haha"
    }
  }
}
```

## 登录用户

```
POST https://api.fox.one/api/broker/login
```

**Parameters:**

| Name      | Type   | Description |
| --------- | ------ | ----------- |
| appid     | string | app id      |
| timestamp | int    | timestamp   |
| nonce     | string | uuid nonce  |
| sign      | string | sign        |
| fox_id    | string | fox id      |

**Response**

```javascript
{
  "code": 0,
  "data": {
    "expires_in": 5184000,
    "token": "dSw0eVYsZCw0ZyxlLDFnSmF2bQ==.CbkIxhcMqqmTFArCa6TcEYK1fxRKdwpILA5KiB7oioo=",
    "user": {
      "avatar": "https://www.fox.one/123456.png",
      "fox_id": "f2e6ae4a-97c3-3012-99d8-5a715b0d75bd",
      "name": "haha"
    }
  }
}
```

## 更新用户资料

```
PUT https://api.fox.one/api/broker/user
```

**Parameters:**

| Name      | Type   | Description          |
| --------- | ------ | -------------------- |
| appid     | string | app id               |
| timestamp | int    | timestamp            |
| nonce     | string | uuid nonce           |
| sign      | string | sign                 |
| fox_id    | string | fox id               |
| name      | string | user name (optional) |
| avatar    | string | avatar (optional)    |

**Response**

```javascript
{
  "code": 0,
  "data": {
    "user": {
      "avatar": "https://www.fox.one/123456.png",
      "fox_id": "f2e6ae4a-97c3-3012-99d8-5a715b0d75bd",
      "name": "haha"
    }
  }
}
```

## 获取钱包地址

```
PUT https://api.fox.one/api/wallet/assets/:asset-id
```

**Http Headers:**

"Authorization: Bearer token"
P
**Response**

```javascript
{
  "code": 0,
  "data": {
    "asset": {
      "accountName": "",
      "accountTag": "",
      "assetId": "c6d0c728-2624-429b-8e0d-d9d19b6592fa",
      "assetKey": "c6d0c728-2624-429b-8e0d-d9d19b6592fa",
      "balance": 0.0040715,
      "chain": {
        "logo": "https://www.fox.one/assets/coins/btc.png",
        "mixinAssetId": "c6d0c728-2624-429b-8e0d-d9d19b6592fa",
        "name": "Bitcoin",
        "symbol": "BTC"
      },
      "chainId": "c6d0c728-2624-429b-8e0d-d9d19b6592fa",
      "changeBtc": 0,
      "changeUsd": 0.01771034154666372,
      "coin": {
        "logo": "https://www.fox.one/assets/coins/btc.png",
        "mixinAssetId": "c6d0c728-2624-429b-8e0d-d9d19b6592fa",
        "name": "Bitcoin",
        "symbol": "BTC"
      },
      "confirmations": 12,
      "icon": "https://images.mixin.one/HvYGJsV5TGeZ-X9Ek3FEQohQZ3fE9LBEBGcOcn4c4BNHovP4fW4YB97Dg5LcXoQ1hUjMEgjbl1DPlKg1TW7kK6XP=s128",
      "name": "Bitcoin",
      "priceBtc": 1,
      "priceUsd": 6413.61433535,
      "publicKey": "1NsstDwq5uCRYj925w4tCLn1tzZzqhrfzo",
      "symbol": "BTC"
    }
  }
}
```

## 转账提醒

**请求**

```
  POST your_callback_url
```

**签名**

请求的头部中包含有签名

```
  "fox-signature":signature
```

signature 的生成规则如下

```go
  method := "POST"
  h := hmac.New(sha256.New, []byte(your_app_secret))
  h.Write([]byte(method + your_callback_url + payload))
  signature := base64.StdEncoding.EncodeToString(h.Sum(nil))
```

其中

payload 为 POST 请求的 body,json 格式的字符串,包含的信息如下

```javascript
  {
    "foxId":"7488887d-3232-3048-a272-d90efd1d6b73", // 相关用户的 fox id
    "type":"snapshot", // 消息类型
    "data": {
      "snapshotId":"fb2486b9-8f7c-4c84-b676-c9084f155a02", // snapshot id form mixin,可作为转账的唯一 id
      "traceId":"39fc3342-0630-4c4d-95a5-af01953b6baf",    // trace id，ignore
      "assetId":"965e5c6e-434c-3fa9-b780-c50f43cd955c",    // mixin 上面 coin/token 的 id，常用 asset id 见下面
      "userId":"451183a9-1da1-3adf-9cb8-c81d295debcf",     // 相关用户在 mixin 上的 user id
      "createdAt":1537867131494600000,                     // 交易时间
      "opponentId":"825d5134-c921-3cf9-a83b-848b73c9e83b",  // 交易对面方的 mixin user id，如果是 mixin 内部转账的话会有这个信息
      "source":"TRANSFER_INITIALIZED",
      "amount":"321",                                       // 转账金额，如果是收款，则是正数；否则是负数
      "memo":"created by foxone",                           // 转账备注
      "asset": {                                            // 转的 coin/token 的详细信息
        "assetId":"965e5c6e-434c-3fa9-b780-c50f43cd955c",
        "assetKey":"0xec2a0550a2e4da2a027b3fc06f70ba15a94a6dac",
        "chainId":"43d61dcd-e413-450d-80b8-101d5e903357",   // token 所在的主链的 asset id
        "icon":"https://images.mixin.one/0sQY63dDMkWTURkJVjowWY6Le4ICjAFuu3ANVyZA4uI3UdkbuOT5fjJUT82ArNYmZvVcxDXyNjxoOv0TAYbQTNKS=s128",
        "name":"Chui Niu Bi",
        "symbol":"CNB"
      },
      "sender":"",            // 交易转出方的钱包地址，仅在 mixin 和外部链上进行转账才会有，mixin 内部转账没有这个信息
      "receiver":"",          // 交易收款方的钱包地址，仅在 mixin 和外部链上进行转账才会有，mixin 内部转账没有这个信息
      "transactionHash":""    // 链上的 transaction hash，仅在 mixin 和外部链上进行转账才会有，mixin 内部转账没有这个信息
    }
  }
```

**消息类型列表**

| type     | desc                      |
| -------- | ------------------------- |
| snapshot | 转账通知（包括收款和转出) |

**常用 asset id**

```
Bitcoin         (BTC)       c6d0c728-2624-429b-8e0d-d9d19b6592fa
Bitcoin Cash    (BCH)       fd11b6e3-0b87-41f1-a41f-f0e9b49e5bf0
Dash            (DASH)      6472e7e3-75fd-48b6-b1dc-28d294ee1476
Dogecoin        (DOGE)      6770a1e5-6086-44d5-b60f-545f9d9e8ffd
EOS             (EOS)       6cfe566e-4aad-470b-8c9a-2fd35b49c68d
Ethereum        (ETH)       43d61dcd-e413-450d-80b8-101d5e903357
ETC             (ETC)       2204c1ee-0ea2-4add-bb9a-b3719cfff93a
Litecoin        (LTC)       76c802a2-7c88-447f-a93e-c29c9e5dd9c8
NEM             (XEM)       27921032-f73e-434e-955f-43d55672ee31
Ripple          (XRP)       23dfb5a5-5d7b-48b6-905f-3970e3176e27
Siacoin         (SC)        990c4c29-57e9-48f6-9819-7d986ea44985
Zcash           (ZEC)       c996abc9-d94e-4494-b1cf-2a3fd3ac5714
```
