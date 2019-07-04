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
exp, key, sign、pin字段含义同merchant-api.md,pin


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
