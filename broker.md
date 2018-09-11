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
