# Fox.ONE Account 文档

## 注册

```
POST /api/account/register
```

**Parameters:**

| Name     | Type   | Description    |
| -------- | ------ | -------------- |
| tel      | string | 86 18512345678 |
| password | string | 123456         |

**Response**

```javascript
{
  "code": 0,
  "data": {
    "user": {
      "avatar": "",
      "bio": "",
      "createdAt": 1536562646,
      "tel": "86 18512345678",
      "fullname": "",
      "isActive": false,
      "isPinSet": false,
      "language": "",
      "mixinUserId": "",
      "pinType": 1,
      "userId": 19130
    }
  }
}
```

## 发送激活码

```
POST /api/account/resend_activation_code
```

**Parameters:**

| Name | Type   | Description    |
| ---- | ------ | -------------- |
| tel  | string | 86 18512345678 |

**Response**

```javascript
{
  "code": 0,
  "data": {}
}
```

## 激活

```
POST /api/account/activate
```

**Parameters:**

| Name           | Type   | Description                  |
| -------------- | ------ | ---------------------------- |
| tel            | string | 86 18512345678               |
| validationCode | string | code for active (length = 6) |

**Response**

```javascript
{
  "code": 0,
  "data": {
    "user": {
      "avatar": "",
      "bio": "",
      "createdAt": 1536562646,
      "tel": "86 18512345678",
      "fullname": "",
      "isActive": true,
      "isPinSet": false,
      "language": "",
      "mixinUserId": "",
      "pinType": 1,
      "userId": 19130
    }
  }
}
```

## 登录

```
POST /api/account/login
```

**Parameters:**

| Name     | Type   | Description    |
| -------- | ------ | -------------- |
| tel      | string | 86 18512345678 |
| password | string | password       |

```javascript
{
  "code": 0,
  "data": {
    "user": {
      "avatar": "",
      "bio": "",
      "createdAt": 1536562646,
      "tel": "86 18512345678",
      "fullname": "",
      "isActive": true,
      "isPinSet": false,
      "language": "",
      "mixinUserId": "",
      "pinType": 1,
      "userId": 19130
    },
    "token" : "aghjdgakjsfhksjfhskjdghkajhfkjd"
  }
}
```

## 申请重置密码

```
POST /api/account/request_reset_password
```

**Parameters:**

| Name | Type   | Description    |
| ---- | ------ | -------------- |
| tel  | string | 86 18512345678 |

**Response**

```javascript
{
  "code": 0,
  "data": {}
}
```

## 重置密码

```
POST /api/account/reset_password
```

**Parameters:**

| Name           | Type   | Description                          |
| -------------- | ------ | ------------------------------------ |
| tel            | string | 86 18512345678                       |
| password       | string | new password                         |
| validationCode | string | code for reset password (length = 6) |

**Response**

```javascript
{
  "code": 0,
  "data": {} // 重置成功
}
```

## 修改密码

```
POST /api/account/password
```

**Parameters:**

| Name        | Type   | Description     |
| ----------- | ------ | --------------- |
| tel         | string | 86 18512345678  |
| password    | string | origin password |
| newPassword | string | new password    |

```javascript
{
  "code": 0,
  "data": {} // 修改成功
}
```

## 更新用户资料

```
POST /api/account/modify
```

**Parameters:**

| Name        | Type   | Description   |
| ----------- | ------ | ------------- |
| fullname    | string | user name     |
| avatar      | string | user avatar   |
| newPassword | string | new password  |
| bio         | string | user bio      |
| language    | string | user language |

**Response**

```javascript
{
  "code": 0,
  "data": {
    "user": {
      "avatar": "",
      "bio": "",
      "createdAt": 1536562646,
      "tel": "86 18512345678",
      "fullname": "",
      "isActive": false,
      "isPinSet": false,
      "language": "",
      "mixinUserId": "",
      "pinType": 1,
      "userId": 19130
    }
  }
}
```

## Errors

| Code | Description                       |
| ---- | --------------------------------- |
| 1537 | login required                    |
| 1539 | login failed                      |
| 1544 | phone has already been registered |

**Error Response**

```javascript
{
  "code": 1539,
  "msg" : "login failed"
}
```
