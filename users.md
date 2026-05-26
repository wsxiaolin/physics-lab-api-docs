# 用户 API 文档

## 概述

用户控制器 (`Users`) 处理所有与用户相关的操作，包括认证、注册、资料管理、好友关系、活动等。

## API 端点

### GET /

获取基本服务信息。

**请求**
```
GET /Users/
```

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "Region": "US",
    "Domain": "Physics",
    "Timestamp": 1640995200
  },
  "Status": 200
}
```

### POST /Authenticate

用户认证和启动包获取。

**请求**
```
POST /Users/Authenticate
Content-Type: application/json

{
  "Login": "user@example.com",
  "Password": "password",
  "Device": {
    "Identifier": "ad30dc0e8cb3ac808ec26a9c97100000",
    "Platform": "iOS",
    "System": "iOS 15.0",
    "Language": "Chinese"
  },
  "Version": 200
}
```

**参数**
- `Login`: 字符串，登录凭据（邮箱、手机号或设备令牌）
- `Password`: 字符串，密码
- `Device`: 对象，设备信息
- `Version`: 整数，客户端版本

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Activities": [ /* 活动列表 */ ],
    "Backpack": { /* 背包对象 */ },
    "ObjectPrices": { /* 物品价格 */ },
    "ChargePrices": { /* 充值价格 */ },
    "ContentTags": [ /* 内容标签 */ ],
    "Surveys": [ /* 调查列表 */ ],
    "Blacklist": [ /* 黑名单 */ ],
    "Library": { /* 库内容 */ },
    "Statistic": { /* 用户统计 */ },
    "DeviceToken": "token123"
  },
  "Status": 200,
  "Token": "new_token",
  "AuthCode": "auth_code"
}
```

### POST /Logout

用户登出。

**请求**
```
POST /Users/Logout
Content-Type: application/json

{
  "PushToken": "push_token_123"
}
```

**参数**
- `PushToken` (string, optional): 推送令牌；登出时可传以清理服务端推送绑定。

**响应**
```json
{
  "Message": "Success",
  "Data": "",
  "Status": 200
}
```

### POST /CancelAccount

取消账户。

**请求**
```
POST /Users/CancelAccount
Content-Type: application/json

{
  "PushToken": "push_token_123"
}
```

**参数**
- `PushToken` (string, optional): 推送令牌；登出时可传以清理服务端推送绑定。

**响应**
```json
{
  "Message": "Success",
  "Data": null,
  "Status": 200
}
```

### GET /Subscribe

订阅邮件列表。

**请求**
```
GET /Users/Subscribe?Email=user@example.com
```

**参数**
- `Email`: 查询参数，邮箱地址

### POST /SubmitLog

提交日志。

**请求**
```
POST /Users/SubmitLog
Content-Type: application/octet-stream

[二进制日志数据]
```

**参数**
- 请求体为二进制流（Content-Type: `application/octet-stream`），服务端按块接收；无 JSON 参数。

**响应**
无特殊响应体（仅返回 HTTP 状态码）

### POST /BindAccount

绑定账户。

**请求**
```
POST /Users/BindAccount
Content-Type: application/json

{
  "Login": "user@example.com",
  "Password": "password123",
  "Language": "Chinese"
}
```

**参数**
- `Login` (string, required): 待绑定的手机号或邮箱。
- `Password` (string, optional): 密码；绑定邮箱/手机时用于校验或设置密码。
- `Language` (string, required): 语言，用于选择 SSO 产品文案（如 `Chinese`）。

**响应**
```json
{
  "Message": "Success",
  "Data": null,
  "Status": 200
}
```

### POST /ConfirmBinding

确认绑定。

**请求**
```
POST /Users/ConfirmBinding
Content-Type: application/json

{
  "Login": "user@example.com",
  "Token": "verification_token"
}
```

**参数**
- `Login` (string, required): 与绑定对应的账号。
- `Token` (string, required): 验证码/令牌。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Activities": [ /* 活动列表 */ ],
    "Backpack": { /* 背包对象 */ },
    "ObjectPrices": { /* 物品价格 */ },
    "ChargePrices": { /* 充值价格 */ },
    "ContentTags": [ /* 内容标签 */ ],
    "Surveys": [ /* 调查列表 */ ],
    "Blacklist": [ /* 黑名单 */ ],
    "Library": { /* 库内容 */ },
    "Statistic": { /* 用户统计 */ },
    "DeviceToken": "token123"
  },
  "Status": 200,
  "Token": "new_token",
  "AuthCode": "auth_code"
}
```

### POST /FindPassword

找回密码。

**请求**
```
POST /Users/FindPassword
Content-Type: application/json

{
  "Login": "user@example.com",
  "Language": "Chinese"
}
```

**参数**
- `Login` (string, optional): 账号（邮箱或手机号）；为空且当前请求中存在有效设备 `Token` 时，会使用 Token 对应的账户。
- `Language` (string, required): 语言，用于 SSO 服务调用。

**响应**
```json
{
  "Message": "Success",
  "Data": "user@example.com",
  "Status": 200
}
```

### POST /ResetPassword

重置密码。

**请求**
```
POST /Users/ResetPassword
Content-Type: application/json

{
  "Login": "user@example.com",
  "Token": "reset_token",
  "Password": "new_password"
}
```

**参数**
- `Login` (string, conditional): 账号，可为空（使用 Token 的情况下）；若为空且无 Token，将返回错误。
- `Token` (string, required): 重置令牌。
- `Password` (string, required): 新密码。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Activities": [ /* 活动列表 */ ],
    "Backpack": { /* 背包对象 */ },
    "ObjectPrices": { /* 物品价格 */ },
    "ChargePrices": { /* 充值价格 */ },
    "ContentTags": [ /* 内容标签 */ ],
    "Surveys": [ /* 调查列表 */ ],
    "Blacklist": [ /* 黑名单 */ ],
    "Library": { /* 库内容 */ },
    "Statistic": { /* 用户统计 */ },
    "DeviceToken": "token123"
  },
  "Status": 200,
  "Token": "new_token",
  "AuthCode": "auth_code"
}
```

### POST /SetPassword

设置密码。

**请求**
```
POST /Users/SetPassword
Content-Type: application/json

{
  "OldPassword": "old_password",
  "NewPassword": "new_password"
}
```

**参数**
- `OldPassword` (string, optional): 旧密码（当账户已绑定邮箱/手机时需要）。
- `NewPassword` (string, required): 新密码。

**响应**
```json
{
  "Message": "Success",
  "Data": null,
  "Status": 200,
  "Token": "new_token",
  "AuthCode": "new_auth_code"
}
```

### POST /SwitchRegion

切换区域。

**请求**
```
POST /Users/SwitchRegion
Content-Type: application/json

{
  "Region": "China",
  "Transfer": true
}
```

**参数**
- `Region` (string, required): 目标区域，值同 `ServiceRegion` 枚举（`US`、`China`）。
- `Transfer` (bool, optional): 是否携带配置/数据迁移（默认 false）。

**响应**
```json
{
  "Message": "Success",
  "Data": { /* 迁移包 */ },
  "Status": 200
}
```

### POST /MigrateTo

迁移到区域。

**请求**
```
POST /Users/MigrateTo
Content-Type: application/json

{
  "Source": "US",
  "Statistic": { /* 用户统计 */ },
  "Backpack": { /* 背包 */ },
  "Profile": { /* SSO 配置文件 */ }
}
```

**参数**
- `Source` (string, required): 源区域，值同 `ServiceRegion` 枚举（`US`、`China`）。
- `Statistic` (object, optional): 迁移所需的用户统计包，结构参考 `UserStatistic`。
- `Backpack` (object, optional): 迁移所需的背包数据，结构参考 `(UserBackpack)(别搜了目前文档项目没这个东西)`。
- `Profile` (object, optional): 迁移所需的 SSO 配置文件，结构参考 `SSOToken`。

**响应**
```json
{
  "Message": "Success",
  "Data": { /* 迁移包 */ },
  "Status": 200
}
```

### POST /BindSocial

绑定社交账户。

**请求**
```
POST /Users/BindSocial
Content-Type: application/json

{
  "Token": "social_token",
  "UserID": "social_user_id",
  "Platform": "WeChat"
}
```

**参数**
- `Token` (string, required): 第三方社交平台令牌。
- `UserID` (string, required): 第三方平台用户 ID。
- `Platform` (string, required): 平台标识（如 `WeChat`、`QQ` 等）。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ }
  },
  "Status": 200
}
```

### POST /SyncActivities

同步活动信息。

**请求**
```
POST /Users/SyncActivities
Content-Type: application/json

{
  "Statistic": { /* 用户统计 */ }
}
```

**参数**
- `Statistic` (object, optional): 用户统计对象（`UserStatistic`）。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Activities": [ /* 活动列表 */ ],
    "Statistic": { /* 用户统计 */ }
  },
  "Status": 200
}
```

### POST /ReceiveBonus

领取活动奖励。

**请求**
```
POST /Users/ReceiveBonus
Content-Type: application/json

{
  "ActivityID": "activity_id",
  "Index": 0,
  "Statistic": { /* 用户统计 */ }
}
```

**参数**
- `ActivityID` (string, required): 活动 ID（ObjectId 字符串）。
- `Index` (int, required): 奖励索引。
- `Statistic` (object, optional): 用户统计对象（`UserStatistic`）。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Activities": [ /* 活动列表 */ ],
    "Statistic": { /* 用户统计 */ },
    "Bonuses": { /* 奖励对象 */ }
  },
  "Status": 200
}
```

### POST /RedeemPromo

兑换促销码。

**请求**
```
POST /Users/RedeemPromo
Content-Type: application/json

{
  "Code": "PROMO123"
}
```

**参数**
- `Code` (string, required): 促销码。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Bonuses": { /* 奖励对象 */ }
  },
  "Status": 200
}
```

### POST /SubmitFeedback

提交反馈。

**请求**
```
POST /Users/SubmitFeedback
Content-Type: application/json

{
  "ActivityID": "activity_id",
  "Content": "反馈内容"
}
```

**参数**
- `ActivityID` (string, required): 活动 ID（ObjectId 字符串）。
- `Content` (string, required): 反馈内容，长度限制 3–65535。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Statistic": { /* 用户统计 */ }
  },
  "Status": 200
}
```

### GET /UpgradeApp

升级应用（重定向）。

**请求**
```
GET /Users/UpgradeApp?Region=CN
```

**参数**
- `Region`: 查询参数，区域

### POST /Rename

重命名用户。

**请求**
```
POST /Users/Rename
Content-Type: application/json

{
  "Target": "新昵称",
  "UserID": "target_user_id"
}
```

**参数**
- `Target` (string, required): 新昵称。昵称长度以字节计需在 3–20 之间（GBK/936 编码下），不得包含停用词。
- `UserID` (string, optional): 目标用户 ID；为空时修改当前用户。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ }
  },
  "Status": 200
}
```

### POST /ModifyInformation

修改用户信息。

**请求**
```
POST /Users/ModifyInformation
Content-Type: application/json

{
  "Field": "Signature",
  "Target": "新签名"
}
```

**参数**
- `Field` (string, required): 支持例如 `Signature`。
- `Target` (string, required): 目标值。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ }
  },
  "Status": 200
}
```

### POST /Upgrade

用户升级。

**请求**
```
POST /Users/Upgrade
Content-Type: application/json
```

**参数**
- 无请求参数。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Bonuses": { /* 奖励对象 */ }
  },
  "Status": 200
}
```

### POST /RequestAvatar

请求头像更新令牌。

**请求**
```
POST /Users/RequestAvatar
Content-Type: application/json

{
  "Request": {
    "FileSize": 1024000
  }
}
```

**参数**
- `Request` (object, required): 上传请求，需包含 `FileSize` (>0) 等信息，用于返回上传令牌。

**响应**
```json
{
  "Message": "Success",
  "Data": { /* 上传令牌 */ },
  "Status": 200
}
```

### POST /ConfirmAvatar

确认头像更新。

**请求**
```
POST /Users/ConfirmAvatar
Content-Type: application/json

{
  "Avatar": 1,
  "Extension": "jpg"
}
```

**参数**
- `Avatar` (int, required): 头像版本号；当上传文件随请求提交时，可由服务器替换为新版本。
- `Extension` (string, optional): 文件扩展名（如 jpg/png）。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ }
  },
  "Status": 200
}
```

### POST /GetUser

获取用户信息。

**请求**
```
POST /Users/GetUser
Content-Type: application/json

{
  "ID": "user_id",
  "Name": "用户名"
}
```

**参数**
- `ID` (string, optional): 用户 ID（24 字符）；支持用户 ID 或用户名检索。
- `Name` (string, optional): 用户名；支持用户名检索。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Statistic": { /* 用户统计 */ },
    "Relation": { /* 关系对象 */ }
  },
  "Status": 200
}
```

### POST /SetCover

设置封面。

**请求**
```
POST /Users/SetCover
Content-Type: application/json

{
  "ContentID": "content_id",
  "Category": "Experiment"
}
```

**参数**
- `ContentID` (string, required): 内容 ID。
- `Category` (string, required): 内容类别（如 `Experiment`）。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Statistic": { /* 用户统计 */ }
  },
  "Status": 200
}
```

### POST /Follow

关注/取消关注用户。

**请求**
```
POST /Users/Follow
Content-Type: application/json

{
  "TargetID": "target_user_id",
  "Action": 1
}
```

**参数**
- `TargetID` (string, required): 目标用户 ID；不可对自己执行（服务端会校验）。
- `Action` (int, required): 1 = 启用（关注），0 = 取消（取消关注）。

**响应**
```json
{
  "Message": "Success",
  "Data": { /* 关系对象 */ },
  "Status": 200
}
```

### POST /Block

屏蔽/取消屏蔽用户。

**请求**
```
POST /Users/Block
Content-Type: application/json

{
  "TargetID": "target_user_id",
  "Action": 1
}
```

**参数**
- `TargetID` (string, required): 目标用户 ID；不可对自己执行（服务端会校验）。
- `Action` (int, required): 1 = 启用（屏蔽），0 = 取消（取消屏蔽）。

**响应**
```json
{
  "Message": "Success",
  "Data": null,
  "Status": 200
}
```

### POST /GetRelations

获取用户关系列表。

**请求**
```
POST /Users/GetRelations
Content-Type: application/json

{
  "UserID": "user_id",
  "DisplayType": 0,
  "Skip": 0,
  "Take": 20,
  "Query": "搜索词"
}
```

**参数**
- `UserID` (string, required): 用户 ID。
- `DisplayType` (int, required): 关系类型筛选器。
- `Skip` (int, required): 分页：跳过的记录数。
- `Take` (int, required): 分页：取得的记录数，上限通常为 20–50。
- `Query` (string, optional): 搜索关键词。

**响应**
```json
{
  "Message": "Success",
  "Data": [ /* 用户列表 */ ],
  "Status": 200
}
```

### POST /Ban

封禁用户。

**请求**
```
POST /Users/Ban
Content-Type: application/json

{
  "TargetID": "target_user_id",
  "Reason": "违规原因",
  "Length": 7
}
```

**参数**
- `TargetID` (string, required): 目标用户 ID；不可对自己执行（服务端会校验）。
- `Reason` (string, required): 封禁原因。
- `Length` (int, optional): 封禁天数（>=0）。

**响应**
```json
{
  "Message": "Success",
  "Data": null,
  "Status": 200
}
```

### POST /Unban

解封用户。

**请求**
```
POST /Users/Unban
Content-Type: application/json

{
  "TargetID": "target_user_id",
  "Reason": "解封原因"
}
```

**参数**
- `TargetID` (string, required): 目标用户 ID；不可对自己执行（服务端会校验）。
- `Reason` (string, required): 解封原因。

**响应**
```json
{
  "Message": "Success",
  "Data": null,
  "Status": 200
}
```

### POST /Appoint

任命用户。

**请求**
```
POST /Users/Appoint
Content-Type: application/json

{
  "TargetID": "target_user_id",
  "Type": "Volunteer",
  "Reason": "任命原因",
  "Length": 30
}
```

**参数**
- `TargetID` (string, required): 目标用户 ID；不可对自己执行（服务端会校验）。
- `Type` (string, required): 任命类型（如 `Volunteer`）。
- `Reason` (string, required): 任命原因。
- `Length` (int, optional): 任命期限天数（>=0）。

**响应**
```json
{
  "Message": "Success",
  "Data": null,
  "Status": 200
}
```

### POST /Report

报告内容。

**请求**
```
POST /Users/Report
Content-Type: application/json

{
  "Category": "Experiment",
  "TargetID": "target_id",
  "Reason": "报告原因",
  "Flag": "inappropriate"
}
```

**参数**
- `Category` (string, required): 内容类别（如 `Experiment`）。
- `TargetID` (string, required): 目标 ID；不可对自己执行（服务端会校验）。
- `Reason` (string, required): 报告原因，长度需 >5。
- `Flag` (string, required): 违规标记（如 `inappropriate`）。

**响应**
```json
{
  "Message": "Success",
  "Data": null,
  "Status": 200
}
```

### POST /FindUsersWithSameDevices

查找使用相同设备的用户。

**请求**
```
POST /Users/FindUsersWithSameDevices
Content-Type: application/json

{
  "UserIDs": ["user1", "user2"]
}
```

**参数**
- `UserIDs` (array, required): 用户 ID 列表，长度限制 1–500。

**响应**
```json
{
  "Message": "Success",
  "Data": [ /* 用户列表 */ ],
  "Status": 200
}
```

### POST /AuthorizeForum

授权论坛登录。

**请求**
```
POST /Users/AuthorizeForum
Content-Type: application/json

{
  "Redirect": "https://example.com/callback"
}
```

**参数**
- `Redirect` (string, required): 重定向 URL，主机必须以 `turtlesim.com` 或 `netlogo.org` 结尾。

**响应**
```json
{
  "Message": "Success",
  "Data": "https://example.com/callback?code=auth_code",
  "Status": 200
}
```

### POST /ExchangeToken

交换令牌。

**请求**
```
POST /Users/ExchangeToken
Content-Type: application/x-www-form-urlencoded

client_id=community&client_secret=secret&code=auth_code
```

**参数**
- `client_id` (string, required): 客户端 ID（form field）。
- `client_secret` (string, required): 客户端密钥（form field）。
- `code` (string, required): 授权码（form field）；接口会严格校验此授权码并支持跨区域转发。

**响应**
```json
{
  "access_token": "token",
  "token_type": "bearer",
  "expires_in": 3600,
  "refresh_token": "token",
  "profile": {
    "id": "user_id",
    "email": "user@example.com",
    "nickname": "用户名",
    "avatar": "avatar_url"
  }
}
```

