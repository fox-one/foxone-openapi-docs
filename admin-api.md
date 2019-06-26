# Gateway Admin API

## Error Code

| code | msg                                  |
| ---- | ------------------------------------ |
| 1105 | admin auth failed                    |
| 1106 | admin permission deny                |
| 1107 | admin login failed                   |
| 1001 | lack of parameters                   |
| 1003 | invalid data (提交的参数有错误)      |
| 1108 | wrong password (修改密码的时候)      |
| 1120 | update withdraw request state failed |


## Admin API

### Login

```
POST /admin/login
```

**Param:**

```javascript
{
    "username" : "spin",
    "password" : "123456"
}
```

**Response:**

```javascript
{
  "admin": {
    "id": 9,
    "created_at": 1550819427,
    "is_super": false,
    "merchant": "0e47f9ea0dd758aea23257853f087ec7",
    "username": "spin",
    "role" : 1
  },
  "merchant": {
    "created_at": 1550044663,
    "key": "0e47f9ea0dd758aea23257853f087ec7",
    "kind": "cloud",
    "logo": "",
    "name": "f1ex",
    "pin_hosted": true
  },
  "role": {
    "id": 1,
    "created_at": 1550816273,
    "description": "处理 kyc",
    "name": "kyc helper 2",
    "permissions": [
      {
        "group": "USER",
        "permissions": [
          {
            "name": "read:blocked_users",
            "group": "USER",
            "dependency": "",
            "description": "Access blocked user list",
            "description_cn": "查看被拉黑的用户列表"
          },
          {
            "name": "read:kyc_profile",
            "group": "USER",
            "dependency": "",
            "description": "Read user's kyc status",
            "description_cn": "查看用户的 kyc 状态"
          },
          {
            "name": "read:users",
            "group": "USER",
            "dependency": "",
            "description": "Access user list",
            "description_cn": "查看用户列表"
          },
          {
            "name": "write:kyc_profile",
            "group": "USER",
            "dependency": "read:kyc_profile",
            "description": "Update user's kyc status",
            "description_cn": "更新用户的 kyc 状态"
          }
        ]
      }
    ]
  },
  "session": {
    "created_at": 1550819608,
    "expired_at": 1551078808,
    "key": "7d2c9088accd35b14c38d976926d817f-9",
    "secret": "8e6613ae4566ed039e0b540db438d671"
  }
}
```

### Create Admin

```
POST /admin/create-admin [write:admin_users]
```

**Param:**

```javascript
{
    "username" : "spin",
    "password" : "123456",
    "role" : 1 // ，权限角色，可选
}
```

**Response:**

```javascript
{
  "admin": {
    "id": 9,
    "created_at": 1550819426,
    "is_super": false,
    "merchant": "0e47f9ea0dd758aea23257853f087ec7",
    "username": "spin",
    "role": 1
  },
  "role": {
    "id": 1,
    "created_at": 1550816273,
    "description": "处理 kyc",
    "name": "kyc helper 2",
    "permissions": [
      {
        "group": "USER",
        "permissions": [
          {
            "name": "read:blocked_users",
            "group": "USER",
            "dependency": "",
            "description": "Access blocked user list",
            "description_cn": "查看被拉黑的用户列表"
          },
          {
            "name": "read:kyc_profile",
            "group": "USER",
            "dependency": "",
            "description": "Read user's kyc status",
            "description_cn": "查看用户的 kyc 状态"
          },
          {
            "name": "read:users",
            "group": "USER",
            "dependency": "",
            "description": "Access user list",
            "description_cn": "查看用户列表"
          },
          {
            "name": "write:kyc_profile",
            "group": "USER",
            "dependency": "read:kyc_profile",
            "description": "Update user's kyc status",
            "description_cn": "更新用户的 kyc 状态"
          }
        ]
      }
    ]
  }
}
```

### List Admins

```
GET /admin/list-admins [read:admin_users]
```

**Param:**
NONE

**Response:**

```javascript
[{
    "id": 12,
    "created_at": 1550821015,
    "is_super": false,
    "merchant": "0e47f9ea0dd758aea23257853f087ec7",
    "username": "spin-2",
    "role" : 1
  },
  {
    "id": 13,
    "created_at": 1550821125,
    "is_super": false,
    "merchant": "0e47f9ea0dd758aea23257853f087ec7",
    "username": "spin-3",
    "role" : 1
  },
  {
    "id": 14,
    "created_at": 1550821156,
    "is_super": false,
    "merchant": "0e47f9ea0dd758aea23257853f087ec7",
    "username": "spin-4",
    "role" : 1
  },
  {
    "id": 15,
    "created_at": 1550821470,
    "is_super": false,
    "merchant": "0e47f9ea0dd758aea23257853f087ec7",
    "username": "spin-5",
    "role" : 1
}]
```


### Admin Detail

```
GET /admin/detail

GET /admin/detail/:id [read:admin_users]
```

**Param:**
NONE

**Response:**

```javascript
{
  "admin": {
    "id": 1,
    "created_at": 1550044663,
    "is_super": true, // 是否是超级管理员
    "merchant": "0e47f9ea0dd758aea23257853f087ec7",
    "username": "yiplee",
    "role" : 1
  },
  "merchant": {
    "created_at": 1550044663,
    "key": "0e47f9ea0dd758aea23257853f087ec7",
    "kind": "cloud",
    "logo": "",
    "name": "f1ex",
    "pin_hosted": true
  }
}
```

### ModifyPassword

```
PUT /admin/password
```

**Paras:**

```javascript
{
    "password" : "123456",
    "new_password" : "654321"
}
```

**Response:**

```javascript
{}
```

### Reset Password

```
PUT /admin/password/:id [write:admin_users]
```

**Paras:**

```javascript
{
    "new_password" : "654321"
}
```

**Response:**

```javascript
{}
```

### Batch Delete Admins

```
POST /admin/delete-admins [write:admin_users]
```

**Param:**

```javascript
[9,10]
```

**Response:**

```javascript
{
  "deleted" : 2 // 删除的 admin user 数量；开发环境才会返回
}
```

### Get Permissions

```
GET /admin/permissions
```

**Param:**
NONE

**Response:**

```javascript
[
    {
        "group": "USER",
        "permissions": [
            {
                "name": "read:users",
                "description": "Access user list",
                "description_cn": "查看用户列表"
            },
            {
                "name": "read:blocked_users",
                "description": "Access blocked user list",
                "description_cn": "查看被拉黑的用户列表"
            },
            {
                "name": "write:blocked_users",
                "description": "Update user block status",
                "description_cn": "管理用户黑名单状态",
                "dependency": "read:blocked_users"
            },
            {
                "name": "read:kyc_profile",
                "description": "Read user's kyc status",
                "description_cn": "查看用户的 kyc 状态"
            },
            {
                "name": "write:kyc_profile",
                "description": "Update user's kyc status",
                "description_cn": "更新用户的 kyc 状态",
                "dependency": "read:kyc_profile"
            },
            {
                "name": "write:tfa_status",
                "description": "reset user's 2-Step verification status",
                "description_cn": "重置用户的两步验证状态"
            },
            {
                "name": "read:user_asset",
                "description": "Read user's asset info",
                "description_cn": "查看用户的资产信息"
            },
            {
                "name": "write:user_asset",
                "description": "Manage user's assets",
                "description_cn": "管理用户资产",
                "dependency": "read:user_asset"
            }
        ]
    },
    {
        "group": "EXCHANGE",
        "permissions": [
            {
                "name": "read:exchange",
                "description": "Read exchange reports",
                "description_cn": "查看交易所表报"
            },
            {
                "name": "read:markets",
                "description": "Read market pairs",
                "description_cn": "查看交易所币对信息"
            },
            {
                "name": "write:markets",
                "description": "Manage market pairs",
                "description_cn": "管理交易所币对信息",
                "dependency": "read:markets"
            }
        ]
    },
    {
        "group": "ADMIN",
        "permissions": [
            {
                "name": "read:admin_roles",
                "description": "Access admin roles",
                "description_cn": "访问管理员角色信息"
            },
            {
                "name": "write:admin_roles",
                "description": "Create admin roles",
                "description_cn": "创建管理员角色",
                "dependency": "read:admin_roles"
            },
            {
                "name": "read:admin_users",
                "description": "Access admin user list",
                "description_cn": "查看商户管理员信息"
            },
            {
                "name": "write:admin_users",
                "description": "Register new admin user",
                "description_cn": "创建新的管理员账户",
                "dependency": "read:admin_users"
            },
            {
                "name": "read:assets",
                "description": "Access wallets",
                "description_cn": "查看商户资产"
            },
            {
                "name": "write:assets",
                "description": "Withdrawal assets",
                "description_cn": "申请提现以及提现",
                "dependency": "read:assets"
            },
            {
                "name": "assets:review",
                "description": "Review withdrawal request",
                "description_cn": "审批提现申请"
            },
            {
                "name": "write:merchant-settings",
                "description": "Update merchant settings",
                "description_cn": "更新商户配置"
            }
        ]
    },
    {
        "group": "DATA",
        "permissions": [
            {
                "name": "read:user_report",
                "description": "Access user report",
                "description_cn": "访问用户表报"
            },
            {
                "name": "read:assets_report",
                "description": "Access assets report",
                "description_cn": "访问用户资金出入表报"
            }
        ]
    }
]
```

### Create Admin Role

```
POST /admin/role [write:admin_roles]
```

**Param:**

```javascript
{
    "name" : "kyc 小助手",
    "description" : "处理用户 kyc",
    "permissions" : [
        "read:users","read:kyc_profile","write:kyc_profile"
    ]
}

/*
1. name, permissions 是必需的，不能为空
2. permissions 里面包含非法的 permission 会报错 invalid data
3. name 长度限制 36，description 长度限制 128
*/
```

**Response:**

```javascript
{
  "role": {
    "id": 1,
    "created_at": 1550816272,
    "description": "处理用户 kyc",
    "name": "kyc 小助手",
    "permissions": [
      {
        "group": "USER",
        "permissions": [
          {
            "name": "read:kyc_profile",
            "group": "USER",
            "dependency": "",
            "description": "Read user's kyc status",
            "description_cn": "查看用户的 kyc 状态"
          },
          {
            "name": "read:users",
            "group": "USER",
            "dependency": "",
            "description": "Access user list",
            "description_cn": "查看用户列表"
          },
          {
            "name": "write:kyc_profile",
            "group": "USER",
            "dependency": "read:kyc_profile",
            "description": "Update user's kyc status",
            "description_cn": "更新用户的 kyc 状态"
          }
        ]
      }
    ]
  }
}
```

### List Admin Roles

```
GET /admin/roles [read:admin_roles]
```

**Param:**
NONE

**Response:**

```javascript
[
  {
    "id": 1,
    "created_at": 1550816273,
    "description": "处理用户 kyc",
    "name": "kyc 小助手"
  }
]
```

### Role Detail

```
GET /admin/role/1 [read:admin_roles]
```

**Param:**
NONE

**Response:**

```javascript
{
  "role": {
    "id": 1,
    "created_at": 1550816273,
    "description": "处理用户 kyc",
    "name": "kyc 小助手",
    "permissions": [
      {
        "group": "USER",
        "permissions": [
          {
            "name": "read:kyc_profile",
            "group": "USER",
            "dependency": "",
            "description": "Read user's kyc status",
            "description_cn": "查看用户的 kyc 状态"
          },
          {
            "name": "read:users",
            "group": "USER",
            "dependency": "",
            "description": "Access user list",
            "description_cn": "查看用户列表"
          },
          {
            "name": "write:kyc_profile",
            "group": "USER",
            "dependency": "read:kyc_profile",
            "description": "Update user's kyc status",
            "description_cn": "更新用户的 kyc 状态"
          }
        ]
      }
    ]
  }
}
```

### Update Admin Role

```
PUT /admin/role/:id [write:admin_roles]
```

**Param:**

```javascript
{
    "name" : "kyc helper 2",
    "description" : "处理 kyc",
    "permissions" : [
       "read:users","read:kyc_profile","write:kyc_profile","read:blocked_users"
    ]
}

/*
1. name 不传或者传空的将被忽略
2. description 不想修改的话就别传，传空的 description 将会把 description 置空
3. permissions 为全量更新，传空的将被忽略
4. name 长度限制 36，description 长度限制 128
*/
```

**Response:**

```javascript
{
  "role": {
    "id": 1,
    "created_at": 1550816273,
    "description": "处理 kyc",
    "name": "kyc helper 2"
  }
}
```

### Delete Role

```
DELETE /admin/role/:id [write:admin_roles]
```

**Param:**
NONE

**Response:**

```javascript
{
  "deleted_role_permissions": 3, // 删除的 role permission 数量
  "deleted_roles": 1, // 删除的 role 数量
  "updated_admins": 0 // 受影响的 admin user 数量
}

/*
以上数量只在开发环境返回，调试用
*/
```

### Batch Delete Roles

```
POST /admin/roles/batch-delete [write:admin_roles]
```

**Param:**

```javascript
[3,4]
```

**Response:**

```javascript
{
  "deleted_role_permissions": 3, // 删除的 role permission 数量
  "deleted_roles": 1, // 删除的 role 数量
  "updated_admins": 0 // 受影响的 admin user 数量
}

/*
以上数量只在开发环境返回，调试用
*/
```

### Assign Role

```
POST /admin/assign-role [write:admin_users]
```

**Param:**

```javascript
{
  "admin_ids" : [11,12],
  "role_id" : 1
}
```

**Response:**

```javascript
{
  "updated_admins": 0 // 只在开发环境返回
}
```

### Request Withdraw

```
POST /admin/request-withdraw [write:assets]
```

**Param:**

```javascript
{
    "asset_id" : "965e5c6e-434c-3fa9-b780-c50f43cd955c",
    "amount" : "100",
    "memo" : "memo",        // 可选
    "intro" : "提现买车",    // 提现目的描述，可选
    "service" : "exchange", // 操作钱包所属的业务
    "public_key" : "0xefb655b1f52FA1cd00C7183F5EC9406c5052573D", // 非 EOS 收款地址
    "account_name" : "", // EOS 收款账户名字
    "account_tag"  : ""  // EOS 收款账户 tag
}
```

**Response:**

```javascript
{
  "withdraw_request": {
    "id": 3,
    "account_name": "",
    "account_tag": "",
    "public_key": "0xefb655b1f52FA1cd00C7183F5EC9406c5052573D", // public key 和 account name & Tag 只会同时存在一组
    "amount": "100",
    "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
    "asset": {
      "symbol": "CNB",
      "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
      "name": "Chui Niu Bi",
      "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
      "icon_url": "https://images.mixin.one/0sQY63dDMkWTURkJVjowWY6Le4ICjAFuu3ANVyZA4uI3UdkbuOT5fjJUT82ArNYmZvVcxDXyNjxoOv0TAYbQTNKS=s128",
      "type": "asset",
      "precision": 8,
      "price" : "1",
      "price_usd" : "1",
      "change" : "0.01"
    },
    "created_at": 1551080048,
    "creator_id": 1, // 申请人 id
    "creator": {     // 申请人简介
      "id": 1,
      "username": "yiplee"
    },
    "intro": "提现买车",
    "memo": "memo",
    "reviewed_at": 0, // 审批时间，单位秒
    "reviewer_id": 0, // 审批人 id
    "state": 1 // 审批结果， 1 : 待审核 2 : 申请已取消（申请人可以在审批之前取消申请） 3 : 拒绝 4 : 通过
  }
}
```

### My Withdraw Requests

```bash
GET /admin/my-withdraw-requests （查看自己发起的）

GET /admin/withdraw-requests [assets:review]
```

**Param:**

```javascript
state  = 1,2,3,4 // 1 : 待审核 2 : 申请已取消（申请人可以在审批之前取消申请） 3 : 拒绝 4 : 通过；不传的话返回所有状态的
offset = 10
limit  = 20 // 默认 50
```

**Response:**

```javascript
{
  "total": 3,
  "withdraw_requests": [
    {
      "account_name": "",
      "account_tag": "",
      "amount": "100",
      "asset": {
        "symbol": "CNB",
        "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
        "name": "Chui Niu Bi",
        "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
        "icon_url": "https://images.mixin.one/0sQY63dDMkWTURkJVjowWY6Le4ICjAFuu3ANVyZA4uI3UdkbuOT5fjJUT82ArNYmZvVcxDXyNjxoOv0TAYbQTNKS=s128",
        "type": "asset",
        "precision": 8,
        "price" : "1",
        "price_usd" : "1",
        "change" : "0.01"
      },
      "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
      "created_at": 1551080049,
      "creator": {
        "id": 1,
        "username": "yiplee"
      },
      "creator_id": 1,
      "id": 3,
      "intro": "提现买车",
      "memo": "memo",
      "public_key": "0xefb655b1f52FA1cd00C7183F5EC9406c5052573D",
      "reviewed_at": 0,
      "reviewer_id": 0,
      "state": 1
    }
  ]
}
```

### Withdraw Request Detail

```bash
GET /admin/my-withdraw-request/:id （查看自己发起的）

GET /admin/withdraw-request/:id [assets:review]
```

**Param:**
NONE

**Response:**

```javascript
{
  "withdraw_request": {
    "account_name": "",
    "account_tag": "",
    "amount": "100",
    "asset": {
      "symbol": "CNB",
      "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
      "name": "Chui Niu Bi",
      "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
      "icon_url": "https://images.mixin.one/0sQY63dDMkWTURkJVjowWY6Le4ICjAFuu3ANVyZA4uI3UdkbuOT5fjJUT82ArNYmZvVcxDXyNjxoOv0TAYbQTNKS=s128",
      "type": "asset",
      "precision": 8,
      "price" : "1",
      "price_usd" : "1",
      "change" : "0.01"
    },
    "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
    "created_at": 1551079677,
    "creator": {
      "id": 1,
      "username": "yiplee"
    },
    "creator_id": 1,
    "id": 1,
    "intro": "提现买车",
    "memo": "memo",
    "public_key": "0xefb655b1f52FA1cd00C7183F5EC9406c5052573D",
    "reviewed_at": 0,
    "reviewer_id": 0,
    "state": 1
  }
}
```

### Cancel Withdraw Request

```
POST /admin/withdraw-request/:id/cancel
```

只有发起人自己能取消

**Param:**
NONE

**Response:**

```javascript
{}
```

### Decline Withdraw Request

```
POST /admin/withdraw-request/:id/decline [assets:review]
```

**Param:**
NONE

**Response:**

```javascript
{}
```

### Approve Withdraw Request

```
POST /admin/withdraw-request/:id/approve [assets:review]
```

通过之后，会直接发起提现操作，如果因为余额不足等等原因导致提现失败，
那么该操作也会失败，申请状态不会改变

**Param:**
NONE

**Response:**

```javascript
{}
```

### Read Wallet Assets

```bash
GET /admin/:service/assets

GET /admin/member/:service/assets?member_id=<member_id>
```

> service 为具体业务代号，比如 payment , exchange, otc 等

**Param:**

```javascript
chain = 1 // 补上主链
```

**Response:**

```javascript
[
  {
    "account_name": "",
    "account_tag": "",
    "asset_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "balance": "0",
    "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "change": "-0.15318178",
    "icon_url": "https://www.fox.one/assets/coins/eth.png",
    "name": "Ether",
    "price": "933.41111086",
    "price_usd": "138.950067272",
    "public_key": "0x1dB6159daD8071Df7A103D2d3Cde569c69233c32",
    "symbol": "ETH"
  },
  {
    "asset_id": "990c4c29-57e9-48f6-9819-7d986ea44985",
    "chain_id": "990c4c29-57e9-48f6-9819-7d986ea44985",
    "change": "-0.09110769",
    "icon_url": "https://www.fox.one/assets/coins/sc.png",
    "name": "Siacoin",
    "price": "0.01606486",
    "price_usd": "0.0023914577676",
    "symbol": "SC"
  }
]
```

### Read Wallet Asset Detail

```bash
GET /admin/:service/asset/:asset_id

GET /admin/member/:service/asset/:asset_id?member_id=<member_id>
```

> service 为具体业务代号，比如 payment , exchange, otc 等

**Param:**
NONE

**Response:**

```javascript
{
  "asset": {
    "account_name": "",
    "account_tag": "",
    "asset_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "balance": "0",
    "chain_id": "43d61dcd-e413-450d-80b8-101d5e903357",
    "change": "-0.15318178",
    "icon_url": "https://www.fox.one/assets/coins/eth.png",
    "name": "Ether",
    "price": "933.41111086",
    "price_usd": "138.950067272",
    "public_key": "0x1dB6159daD8071Df7A103D2d3Cde569c69233c32",
    "symbol": "ETH"
  }
}
```

### Read Wallet Snapshots

```bash
GET /admin/merchant/snapshots

GET /admin/member/:service/snapshots?member_id=<member_id>
```

**Param:**

```form
service: exchange // payment, otc, exchange
order: ASC // ASC, DESC
from: 1548038449231344000
to: 1548038449231344001
offset: 1
limit: 1 // max: 100
```

**Response:**

```javascript
{
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
  ],
  "total": 1
}
```

### Reset User's TFA Status

```
POST /admin/user/:id/reset-tfa
```

> id 为用户 id

**Param:**
NONE

**Response:**

```javascript
{}
```

### Validate Token

> 验证 Token 有效性

- URL: /admin/validate
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
  "admin": {
    "id": 1,
    "created_at": 123,
    "username": "name",
    "merchant": "xxx"
  }
}
```

### 查询开通的服务

- URL: /admin/merchant/services
- Method: GET
- Return: [{"merchant_id":"xxx", "service":"xxx", "wallet_id":"xxx", "status": 0, "created_at": 1548040714}]

### transaction report

- URL: /admin/report/transaction
- Method: GET
- Parmas: type=day&service=exchange&asset_id=965e5c6e-434c-3fa9-b780-c50f43cd955c&limit=31
- Required: type, Optional:service, asset_id, limit
- Return:

```json
{
  "965e5c6e-434c-3fa9-b780-c50f43cd955c": [
    {
      "time": "2019-01-21",
      "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
      "withdraw_count": 1,
      "withdraw_amount": -0.1,
      "deposit_count": 1,
      "deposit_amount": 10
    }
  ]
}
```

### 交易所手续费 report

- URL: /admin/exchange/report/fee
- Method: GET
- Parmas: type=day&symbol=CNB
- Return:

```json
{
  "CNB": [
    {
      "time": "2019-01-21",
      "asset": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
      "count": 0,
      "amount": 0
    }
  ]
}
```

### 交易所交易 report

- URL: /admin/exchange/report/trades
- Method: GET
- Parmas: type=day&symbol=CNBUSDT
- Return:

```json
{
  "CNBUSDT": [
    {
      "time": "2019-01-21",
      "base": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
      "quote": "815b0b1a-2764-3736-8faa-42d694fa620a",
      "count": 0,
      "amount": 0,
      "funds": 0
    }
  ]
}
```

### 交易所订单

```
GET /admin/exchange/orders?symbol=BTCUSDT&state=DONE&offset=2&limit=10&start=1548301460&end=1548301460
```

_Response_

```javascript
{
  "orders" : [
    {
      "id" : "xxxxxxxx",
      "created_at" : "129384939", // ms
      "order_type" : "LIMIT",
      "user_id" : "xxxxxxxxxxx",
      "quote_asset_id" : "xxxxxxxxxxxxx",
      "base_asset_id" : "xxxxxxxxxxxxx",
      "side" : "ASK",
      "price" : "0.12",
      "remaining_amount" : "100",
      "filled_amount" : "50",
      "remaining_fund" : "0",
      "filled_fund" : "0",
      "state" : "DONE"
    }
  ],
  "total" : 100,
}
```

### 订单详情

```
GET /admin/exchange/order/:id?user_id=xxxxxxxxx
```

_Response_

```javascript
{
  "id" : "xxxxxxxx",
  "created_at" : "129384939", // ms
  "order_type" : "LIMIT",
  "user_id" : "xxxxxxxxxxx",
  "quote_asset_id" : "xxxxxxxxxxxxx",
  "base_asset_id" : "xxxxxxxxxxxxx",
  "side" : "ASK",
  "price" : "0.12",
  "remaining_amount" : "100",
  "filled_amount" : "50",
  "remaining_fund" : "0",
  "filled_fund" : "0",
  "state" : "DONE"
}
```

### 订单下属 Trades

```
GET /admin/exchange/order/:id/trades?user_id=xxxxx
```

_Response_

```javascript
[
  {
    id: "tarde id",
    time: 1232434434, // ms
    price: "0.12",
    amount: "100",
    side: "ASK or BID"
  }
];
```

### 上架币对/修改币对信息

```bash
POST /api/admin/market/pairs
```

**Param:**

```javascript
{
  "symbol":"EOSUSDT",    //必填
  "status":"TRADING",    //TRADING 或 PENDING
  "taker_fee":"0.001",   //精度小数点后4位，最小0，最大0.002, 默认0.002
  "maker_fee":"0.002",   //精度小数点后4位，最小0，最大0.002, 默认0.002
}
```

### 下架交易对

```bash
DELETE /api/admin/market/pair/:symbol
```

**Param:**
NONE

**Response:**

```javascript
{}
```

### 用户提现申请 列表

```text
GET /api/admin/user-withdraws
```

**Params:**

```form
offset: 1
limit: 1 // max 100, default 20
```

**Response:**

```javascript
{
  "withdraw_records": [{
    "account_name": "",
    "account_tag": "",
    "amount": "100",
    "amount_cny": "0",
    "amount_usd": "0",
    "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
    "created_at": 1552443896,
    "fail_cause": "",
    "id": 8,
    "memo": "",
    "public_key": "0x849Dd061C5f8F4E2F58fe25Ab7d39c6891210388",
    "service": "exchange",
    "state": 1
  }],
  "total": 1
}
```

### 审核 提现申请

```text
PUT /api/admin/user-withdraw
```

**Params:**

```javascript
{
  "id": 1,
  "state": 3,
  "fail_cause:": "不让提"
}
```

**Response:**

```javascript
{
  "withdraw_record": {
    "account_name": "",
    "account_tag": "",
    "amount": "100",
    "amount_cny": "0",
    "amount_usd": "0",
    "asset_id": "965e5c6e-434c-3fa9-b780-c50f43cd955c",
    "created_at": 1552443896,
    "fail_cause": "",
    "id": 8,
    "memo": "",
    "public_key": "0x849Dd061C5f8F4E2F58fe25Ab7d39c6891210388",
    "service": "exchange",
    "state": 1
  }
}
```

### 获取 商户信息

```text
GET /api/admin/merchant
```

**Response:**

```javascript
{
  "merchant_config": {
    "main_color": "xxx",
    "display_logo": "xxx",
    "display_name": "xxx",
    "callback_url": "xxx",
    "hosts": ["127.0.0.1"],
    "telesign_key": "xxx",
    "register_whitelist": true,
    "withdraw_review": true,
    "zendesk_domain": "fox",
    "zendesk_url": "https://fox.zendesk.com",
    "zendesk_support_email": "xxx@xxx.com",
    "zendesk_api_key_binded" : true
  }
}
```

### 更新 商户信息

```bash
PUT /api/admin/merchant
```

**Params:**

```javascript
{
  "name": "F1EX",
  "logo": "https://xxx",
  "hosts": "127.0.0.1,127.0.0.2", // cors settings, 3 ip at most
  "telesign_key": "xx",
  "telesign_secret": "Xx",
  "register_whitelist": true,
  "withdraw_review": true,
  "zendesk_domain": "fox",
  "zendesk_username": "xxx@xxx.com",
  "zendesk_api_key" : "xxxxx", // zendesk_username 必须和 zendesk_api_key 同时提供
  "zendesk_support_email": "xxx@xxx.com",
  "withdraw_levels": [
    {
      "limit_single": "0.5", // 单笔提现最大额度，单位 BTC
      "limit_24hr" : "1" // 24h 累计最大提现额度，单位 BTC
    }
  ]
}
```

**Response:**

```javascript
{
  "merchant_config": {
    "main_color": "xxx",
    "display_logo": "xxx",
    "display_name": "xxx",
    "callback_url": "xxx",
    "hosts": ["127.0.0.1"],
    "telesign_key": "xxx",
    "register_whitelist": true,
    "withdraw_review": true,
    "zendesk_domain": "fox",
    "zendesk_url": "https://fox.zendesk.com",
    "zendesk_support_email": "xxx@xxx.com",
    "zendesk_api_key_binded" : true,
    "withdraw_levels": [
      {
        "limit_single": "0.5", // 单笔提现最大额度，单位 BTC
        "limit_24hr" : "1" // 24h 累计最大提现额度，单位 BTC
      },
      {
        "limit_single": "5", // 单笔提现最大额度，单位 BTC
        "limit_24hr" : "10" // 24h 累计最大提现额度，单位 BTC
      }
    ]
  }
}
```

### Put Bucket

```bash
PUT /api/admin/bucket/:key
```

**Param:**

```javascript
{
  "foo": "your custom field",
  "bar": "custom"
}
```

> 目前要求 request 带的 body 必须是 json string

**Response:**

```javascript
{}
```

### Read Bucket

```bash
GET /api/merchant/bucket/:key (```fox-cloud-merchant-id``` required)

GET /api/admin/bucket/:key (login required)

GET /api/admin/bucket?k=:key (login required)
```

**Param:**
NONE

**Response:**

```javascript
{
  "foo": "your custom field",
  "bar": "custom"
}
```

### Read Zendesk Tickets

```bash
GET /api/admin/zendesk/tickets
```

**Parameters:**

| key    | value                                                                 | desc     |
| ------ | --------------------------------------------------------------------- | -------- |
| statue | Possible values: "new", "open", "pending", "hold", "solved", "closed" | Optional |

**Response:**

```javascript
{
  "tickets" : [
    {
      "id":               35436,
      "url":              "https://company.zendesk.com/api/v2/tickets/35436.json",
      "external_id":      "ahg35h3jh",
      "created_at":       "2009-07-20T22:55:29Z",
      "updated_at":       "2011-05-05T10:38:52Z",
      "type":             "incident",
      "subject":          "Help, my printer is on fire!",
      "raw_subject":      "{{dc.printer_on_fire}}",
      "description":      "The fire is very colorful.",
      "priority":         "high",
      "status":           "open",
      "recipient":        "support@company.com",
      "requester_id":     20978392,
      "submitter_id":     76872,
      "assignee_id":      235323,
      "organization_id":  509974,
      "group_id":         98738,
      "collaborator_ids": [35334, 234],
      "follower_ids":     [35334, 234], // This functionally is the same as collaborators for now.
      "problem_id":       9873764,
      "has_incidents":    false,
      "due_at":           null,
      "tags":             ["enterprise", "other_tag"],
      "via": {
        "channel": "web"
      },
      "custom_fields": [
        {
          "id":    27642,
          "value": "745"
        },
        {
          "id":    27648,
          "value": "yes"
        }
      ],
      "satisfaction_rating": {
        "id": 1234,
        "score": "good",
        "comment": "Great support!"
      },
      "sharing_agreement_ids": [84432]
    }
  ]
}
```