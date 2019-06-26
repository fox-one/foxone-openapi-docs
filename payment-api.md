# 支付业务 API


<br>
## ▎Fox.ONE Payment 业务接入说明。

Payment Service 提供了两种层面的接入方式：

* 一种是面向 APP 端，直接使用 API。
* 另一种情况为第三方后端使用的 API。


这两种接入方式使用的 API 不一致，其签名方式也不一致：

- 第一种由后端签发动态 Key/secret，然后 APP 负责签名鉴权
- Service 使用 Fox.ONE 签发的固定的 key/secret


<br>
## ▎HOST

* 测试环境：https://dev-gateway.fox.one
* 生产环境：https://openapi.fox.one

<br>
## ▎API 详情

### > 读取用户某币种余额

##### APP 接口

- URL: /member/payment/asset/:asset_id

##### Service API

- URL: /merchant/member/payment/asset/:asset_id

### 接口详情：

- Method: GET
- Return:

```json
{
  "asset": {
    "asset_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "balance": "0",
    "price": "0.111",
    "change": "0.01",
    "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "name": "Ether",
    "symbol": "ETH",
    "icon_url": "https://images.mixin.one/zVDjOxNTQvVsA8h2B4ZVxuHoCF3DJszufYKWpd9duXUSbSapoZadC7_13cnWBqg0EmwmRcKGbJaUpA8wFfpgZA=s128",
    "public_key": "0x2A5206b0130D0e165eeB184744a73FEcFA193998",
    "account_name": "",
    "account_tag": ""
  }
}
```

### > 读取用户余额

##### APP 接口

- URL: /member/payment/assets

##### Service API

- URL: /merchant/member/payment/assets

#### 接口详情：

- Method: GET
- Params: chain=1 (补上全部主链的 assets)
- Return:

```json
[
  {
    "asset_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "balance": "0",
    "price": "0.111",
    "change": "0.01",
    "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "name": "Ether",
    "symbol": "ETH",
    "icon_url": "https://images.mixin.one/zVDjOxNTQvVsA8h2B4ZVxuHoCF3DJszufYKWpd9duXUSbSapoZadC7_13cnWBqg0EmwmRcKGbJaUpA8wFfpgZA=s128",
    "public_key": "0x2A5206b0130D0e165eeB184744a73FEcFA193998",
    "account_name": "",
    "account_tag": ""
  }
]
```

<br>
### >  读取转账记录列表

##### APP 接口

- URL: /member/payment/snapshots

##### Service API

- URL: /merchant/member/payment/snapshots

#### 接口详情：

- Method: GET
- Params: asset_id=xxx&cursor=xxx&limit=100&order=ASC
- Return:

```json
{
  "pagination": {
    "has_next": true,
    "next_cursor": "MTU0ODAzODQ0OTIzMTM0NDAwMDsx"
  },
  "snapshots": [
    {
      "snapshot_id": "bf0d044a-28de-456c-8e64-3f421fbef273",
      "trace_id": "32379d70-1ce5-41b4-bd09-f5ed038fe521",
      "wallet_id": "ab243363-ae56-3d4e-8575-31c6af655a41",
      "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
      "opponent_id": "318df485-02e1-3c10-8ffd-b241d10dcfd3",
      "source": "TRANSFER_INITIALIZED",
      "amount": "10",
      "memo": "haha",
      "member_id": "6cab7b5a491547a090ffaef24d59910f",
      "service": "payment",
      "label": "create by required",
      "data": null,
      "created_at": 1548038449231344000,
      "asset": {
        "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
        "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
        "name": "Chui Niu Bi",
        "symbol": "CNB",
        "icon_url": "https://images.mixin.one/0sQY63dDMkWTURkJVjowWY6Le4ICjAFuu3ANVyZA4uI3UdkbuOT5fjJUT82ArNYmZvVcxDXyNjxoOv0TAYbQTNKS=s128"
      }
    }
  ]
}
```

> ps: order: ASC/DESC; limit < 500; asset_id 可选
> 提现/充值时：transaction_hash 不为空; sender/receiver

<br>
### > 读取转账记录

##### APP 接口

- URL: `/member/payment/snapshot/:snapshot_id`

##### Service API

- URL: `/merchant/member/payment/snapshot/:snapshot_id`

#### 接口详情：

- Method: GET
- Return:

```json
{
  "snapshot": {
    "snapshot_id": "bf0d044a-28de-456c-8e64-3f421fbef273",
    "trace_id": "32379d70-1ce5-41b4-bd09-f5ed038fe521",
    "wallet_id": "ab243363-ae56-3d4e-8575-31c6af655a41",
    "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
    "opponent_id": "318df485-02e1-3c10-8ffd-b241d10dcfd3",
    "source": "TRANSFER_INITIALIZED",
    "amount": "10",
    "memo": "haha",
    "member_id": "6cab7b5a491547a090ffaef24d59910f",
    "service": "payment",
    "label": "create by required",
    "data": null,
    "created_at": 1548038449231344000,
    "asset": {
      "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
      "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
      "name": "Chui Niu Bi",
      "symbol": "CNB",
      "icon_url": "https://images.mixin.one/0sQY63dDMkWTURkJVjowWY6Le4ICjAFuu3ANVyZA4uI3UdkbuOT5fjJUT82ArNYmZvVcxDXyNjxoOv0TAYbQTNKS=s128"
    }
  }
}
```

<br>
### > 提现

##### APP 接口

- URL: /member/payment/withdraw

##### Service API

- URL: /merchant/member/payment/withdraw

#### 接口详情：

- Method: POST
- Params:

```json
{
  "asset_id": "xxx",
  "amount": "0.1",
  "public_key": "xxx",
  "memo": "haha",
  "account_name": "xxx",
  "account_tag": "xxx",
  "trace_id": "xxx"
}
```

- Return:

```json
{
  "snapshot": SNAPSHOT_OBJECT
}
```

<br>
### > 转账

##### APP 接口

- URL: /member/payment/transfer

##### Service API

- URL: /merchant/member/payment/transfer

#### 接口详情：

- Method: POST
- Params:

```json
{
  "asset_id": "xxx",
  "amount": "0.1",
  "opponent_id": "xxx",
  "memo": "haha",
  "trace_id": "xxx"
}
```

- Return:

```json
{
  "snapshot": {
    "snapshot_id": "2818acd8-ba95-4495-bdc7-4e0e493a3945",
    "trace_id": "bf0960b6-0fe4-43ba-ab79-614d2865a465",
    "wallet_id": "",
    "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
    "opponent_id": "318df485-02e1-3c10-8ffd-b241d10dcfd3",
    "source": "",
    "amount": "-0.1",
    "memo": "test",
    "data": null,
    "created_at": 1548060483604680369
  }
}
```

<br>
### > 转账到业务

##### APP 接口

- URL: /member/payment/service-transfer

##### Service API

- URL: /merchant/member/payment/service-transfer

#### 接口详情：

- Method: POST
- Params:

```javascript
{
  "to_member": "xxx", // member id. 必须是同个商户下的用户。不传时为自身。
  "service": "exchange", // service (payment/exchange/otc)。不传时为当前业务。本参数只在转账给自己时有效。
  "asset_id": "xxx",
  "amount": "0.1",
  "memo": "haha",
  "trace_id": "xxx"
}
```

- Return:

```json
{
  "snapshot": {
    "snapshot_id": "2818acd8-ba95-4495-bdc7-4e0e493a3945",
    "trace_id": "bf0960b6-0fe4-43ba-ab79-614d2865a465",
    "wallet_id": "",
    "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
    "opponent_id": "318df485-02e1-3c10-8ffd-b241d10dcfd3",
    "source": "",
    "amount": "-0.1",
    "memo": "test",
    "data": null,
    "created_at": 1548060483604680369
  }
}
```
