# Merchant API

## Validate Token

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

- Return:

```json
{
  "merchant_id": "xxx",
  "member_id": "xxx"
}
```

## Merchant Services

> 商户开启的业务

- URL: /merchant/services
- Method: GET
- Return:

```javascript
[{
    "merchant_id": "xxx",
    "service": "exchange",
    "wallet_id": "xxx",
    "status": 1, // 1 OK; 2 Frozen
    "created_at": 1234,
}]
```

## 创建用户

> 商户创建用户

- URL: /merchant/member/new
- Method: POST
- Return:

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

## 登陆用户

- URL: /merchant/member/login
- Method: POST
- Params:

```javascript
{
    "id": "member_id",
    "expire": 1111 // active duration in seconds
}
```

- Return:

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

## 登出用户

- URL: /merchant/member/logout
- Method: POST
- Params:

```javascript
{
    "id": "member_id",
    "session_key": "xxx" // optional
}
```

- Return:

```json
{}
```

## 用户业务钱包列表

- URL: /merchant/member/services?member_id=xxx&service=xxx
- Method: GET
- Return:

```javascript
[{
    "member_id": "xxx",
    "wallet_id": "xxx",
    "service":   "exchange",
    "label":     "xxx",
}]
```

> service为可选参数。若不传则查询所有

## 用户转账记录

- URL: /merchant/member/snapshots
- Method: GET
- Param: service=exchange&asset_id=xxx&cursor=xxx&order=ASC|DESC&limit=50
- Return:

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
