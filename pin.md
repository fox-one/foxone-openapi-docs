# Pin & Wallet

## 设置 pin

```
PUT /api/account/pin
```

**Headers**

| Name           | Description                                     |
| -------------- | ----------------------------------------------- |
| Authorization  | "Bearer " + token                               |
| fox-client-pin | 用户原来的 pin 生成的 pin token，生成方式见下文 |

> 如果用户没有设置过 pin，则不需要提供 fox-client-pin；token 为用户登录注册
> 的时候返回的 token

**Parameters**

| Name    | Type   | Description |
| ------- | ------ | ----------- |
| newPin  | string | 新的 pin    |
| pinType | int    | 设置成 1    |

**Response**

```javascript
{
    "code" : 0,
    "data" : {} // 设置成功
}
```

## 转账到钱包

```
POST /api/wallet/withdraw
```

**Headers**

| Name           | Description                                 |
| -------------- | ------------------------------------------- |
| Authorization  | "Bearer " + token                           |
| fox-client-pin | 用户的 pin 生成的 pin token，生成方式见下文 |

**Parameters**

| Name      | Type   | Description           |
| --------- | ------ | --------------------- |
| publicKey | string | 钱包地址              |
| assetId   | string | 要转账的币的 asset id |
| amount    | string | 转账币的数量          |
| memo      | string | 转账说明              |

**Response**

```javascript
{
    "code" : 0,
    "data" : {
        "snapshot": {
            "coinId": 2437,
            "snapshotId": "8d49ab57-116a-44a2-ae96-ed631b9f66a6",
            "assetId": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
            "traceId": "e5bc2d22-717a-4f4d-9b49-7b84c7e75498",
            "amount": -100,
            "memo": "恭喜发财",
            "createdAt": 1539422980,
            "counterUserId": "170e40f0-627f-4af2-acf5-0f25c009e523",
            "sender": "",
            "receiver": "",
            "transactionHash": ""
        }
    }
}
```

## Pin Token

```json
{
  "p": "123456",
  "t": 1533199266,
  "n": "330b1809-5575-47ac-955d-2b222eb134ba"
}
```

- p -> raw pin
- t -> timestamp (精确到秒)
- n -> uuid nonce

### 生成 pin token 放在 Header 里面

fox-client-pin = base64(rsa(public key,pin payload json string)),rsa 加密方法是 **RSA-OAEP** ，加密所用的公钥匙见下文

## 加密用的公钥

dev :
-----BEGIN RSA PUBLIC KEY----- MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA5PbIXYPU+leiueNmwzbk u05eXRJ79gQCPyS3mwoUIySAMmPaU2/RN47lhhJO3YHJQ/J/u2jqgTntQKdmmjei 0L0odUyL3JaSrqirLi6JVWKaF3o9YkX88xwyLLWNdWywFPL9CPWcqLvTbymrS2zN l0U8zGVXft+aDlRYdCaBtQWF1a2tmNpKXfOlPOaZotXO/iN4Diqpl+vTqUqRxb0q FkPcAH1XKpWTDP7XSAVLyh2CIf2GEZFzt55nMudfUXMEwv0aAUIgsxK/Yk2cyTbY qrYnTJbX/WysMtg+vhVy7DJznwx5sPl1huPO5ytfwTagKgQArF34WfLEB7OIZuZL +QIDAQAB -----END RSA PUBLIC KEY-----

prod:
-----BEGIN RSA PUBLIC KEY----- MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0/SMrN1Ki50xAD0mjIjA NroYtZ+dtFh9i2gT8ANy9ObQplKJQedM5VDviEqnNiyNgQj6byso3EnykgG7JbpQ qwt7XgAwO+uE01EdGi46G59DzvobBfwchmV9q9caHE0od95XukCq7vQzlpL/IS2+ BWaG6RjYeqcE7mxdmcVIzQ6ifcY4tfcAnEXqVz5kAcKM+GbLVDOhdeb3LPpkydNT Li+q8vY1PrnnWDlGnJORosBuRS5IXab7QxojKFx1lrq4EvnKGeyB6m3+h14Ixlcv /QO5p7RR4lI9hs11Ecatritck25xQQ+YO4n0gYAvScxV0t0nQGBjmsN11Nm4Hl1x kwIDAQAB -----END RSA PUBLIC KEY-----
