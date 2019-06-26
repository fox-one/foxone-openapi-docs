# 错误码

## ▎Account

| code | msg                              |
| ---- | -------------------------------- |
| 1101 | merchant auth failed             |
| 1102 | member not found                 |
| 1103 | member auth failed               |
| 1104 | member pin auth failed           |
| 1105 | admin auth failed                |
| 1106 | admin permission deny            |
| 1107 | admin login failed               |
| 1108 | wrong password                   |
| 1109 | password not change              |
| 1152 | service not open                 |
| 1153 | service not available at present |
| 1201 | admin username not available     |

## ▎Wallet 操作

| code | msg                  |
| ---- | -------------------- |
| 1160 | amount too small     |
| 1161 | insufficient balance |
| 1162 | invalid trace id     |

## ▎exchange

| code | msg              |
| ---- | ---------------- |
| 2001 | invalid symbol   |
| 2002 | invalid interval |

## ▎两步验证

| code | msg                                            |
| ---- | ---------------------------------------------- |
| 1110 | 2-step verification required                   |
| 1111 | 2-step verification verify failed              |
| 1112 | 2-step verification is already enabled         |
| 1113 | 2-step verification is not enabled             |
| 1114 | 2-step verification is used, wait for next one |

## ▎提现

| code  | msg                    |
| ----- | ---------------------- |
| 10401 | 提现额度不够           |
| 10402 | 提现操作暂时被禁止     |
| 10403 | 提现申请已经 auth 过了 |
| 10404 | 提现申请已过期         |
| 10405 | 超出单笔提现限制       |

## ▎![]()User Auth

| code  | msg            |
| ----- | -------------- |
| 10204 | 手机验证码错误 |
| 10205 | 邮箱验证码错误 |
