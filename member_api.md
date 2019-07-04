# Member API

## Auth
- jwt的payload 为

```javascript
{
  "exp": 1562199964,
  "key": "2c4462569ead43de76a157bb9a3d3d34-8a915638d50c44378148fb89f83c5d86",
  "pin": "176mH4AD1yzZy9KJmapgGm8IklzVOMR2Fx59D/ww8j09kxBM5MCBLfJHeK3crJqb1FsPjD2BGVnKYhipjUJlEy2Qz7LnaZy7mKZotKMDOX0dvtUpyiIzt5FiSU3LesOvSM5381UcB5CEvgOp9+qkcRJ3JmPQTFmU2Xwo4piPJahh21+PnEy6KTCsq2bngkou547vJ2kfTLsB/pkeLaAXxTx9Hrk1bAORNGe8MHxPn4Es1qkvJVOjrVxqh2yL7oGQD2cx1HC+BTgjkRQPxqzVgYTeN4x+iawLxzwKVe5evPQP8TBYAlvmA1F8VqKySj4FqUxNUfWz4jma1jxplqxMuQ==",  // if required.
  "sign": "9+PC7wSAqnqVqQdgEkFmt3ObxTpPdLR4YO8BlB7jv6c="
}
```
exp, key, sign字段含义同merchant-api.md,pin，其中pin为使用RSA OAEP算法对pin加密后的结果，加密时需包含如下信息

```javascript
{
    "t": 1562199964,                // current unix timestamp
    "n": "somethin unique",         // nonce, usually the uuid
    "p": "123456"                   // your raw pin
}
```

- RSA的公钥为

**prod:**

```text
-----BEGIN RSA PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA0/SMrN1Ki50xAD0mjIjA
NroYtZ+dtFh9i2gT8ANy9ObQplKJQedM5VDviEqnNiyNgQj6byso3EnykgG7JbpQ
qwt7XgAwO+uE01EdGi46G59DzvobBfwchmV9q9caHE0od95XukCq7vQzlpL/IS2+
BWaG6RjYeqcE7mxdmcVIzQ6ifcY4tfcAnEXqVz5kAcKM+GbLVDOhdeb3LPpkydNT
Li+q8vY1PrnnWDlGnJORosBuRS5IXab7QxojKFx1lrq4EvnKGeyB6m3+h14Ixlcv
/QO5p7RR4lI9hs11Ecatritck25xQQ+YO4n0gYAvScxV0t0nQGBjmsN11Nm4Hl1x
kwIDAQAB
-----END RSA PUBLIC KEY-----
```

**dev:**

```text
-----BEGIN RSA PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA5PbIXYPU+leiueNmwzbk
u05eXRJ79gQCPyS3mwoUIySAMmPaU2/RN47lhhJO3YHJQ/J/u2jqgTntQKdmmjei
0L0odUyL3JaSrqirLi6JVWKaF3o9YkX88xwyLLWNdWywFPL9CPWcqLvTbymrS2zN
l0U8zGVXft+aDlRYdCaBtQWF1a2tmNpKXfOlPOaZotXO/iN4Diqpl+vTqUqRxb0q
FkPcAH1XKpWTDP7XSAVLyh2CIf2GEZFzt55nMudfUXMEwv0aAUIgsxK/Yk2cyTbY
qrYnTJbX/WysMtg+vhVy7DJznwx5sPl1huPO5ytfwTagKgQArF34WfLEB7OIZuZL
+QIDAQAB
-----END RSA PUBLIC KEY-----
```

## Note
You can use merchant key/secret or member key/secret to access the api below.
When merchant key/secret is used, you need put `/merchant` before every uri, 
like `/member/info` -> `/merchant/member/info`


## GET Info

```
    GET /member/info
```

** Return **

```javascript
{  
   "id":"e82e7a6ed335451b83fe33e7477b03ac",
   "created_at":1562142129,
   "is_pin_set":false
}
```

## Modify PIN

```
    PUT /member/pin
```

**Note**
If `is_pin_set` is false, do not put pin in the authorization token.
Namely if the member is newly created, use this api to set pin without decrypted pin in token.
Otherwise put decrypted pin in token to modify pin.

**Param**

```javascript
{
    "pin" : "mAvjFxe8Ozkr8pO7xImEL0H83Ja6JCkVS0mT029V+nC3pM9xjHZFX50882+iY+GNNhqVQC2cWHkiYJBoum9BkW3NhL0js4AFULRKFXoKnkMOoXlgAKcYZmE/cihJnwR6L6KeN7c8ycTYPUNBnSxvkvT2PcD59xBqDEyXcB4LnaL5AncpevtTAiejFE25u2CsLl/4EB0s42+yxbj5CTob73RUhgVndJB9b38MEQn7cDKmod/Uej7/T+/K47DMEvOmndt+YGNlK8iBfViO6T/QOZC0JKs5yWkaZ8wyfRyW9bT1oqDriIzVvFxHCQXPWVPoK/h25EEfHOVfdzBXLgGM2g=="
}
```

** Return **

```javascript
{}
```

## Verify PIN

```
    POST /member/pin
```

** Return **

```javascript
{}
```
