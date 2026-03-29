## physicslab-api

这是物理实验室 APP的一些常用网络接口的封装，AI初步整理后我自己写的。安装rest clinet插件可以在.http文件里面直接POST

所有 API 响应都遵循以下标准 JSON 格式：

```json
{
  "Message": "成功消息或错误信息",
  "Data": { /* 响应数据 */ },
  "Status": 200,
  "Token": "可选的新令牌",
  "AuthCode": "可选的认证码"
}
```

### 字段说明

- **Message**: 字符串，描述操作结果或错误信息
- **Data**: 对象，包含实际的响应数据
- **Status**: 整数，HTTP 状态码
- **Token**: 字符串，可选，用于身份验证的新令牌
- **AuthCode**: 字符串，可选，用于认证的代码

## 错误码解析

### 常见错误消息

- `Login.Required`: 需要登录
- `User.Not.Allowed`: 用户无权限
- `Content.Invalid`: 内容无效
- `Input.Field.Missing`: 缺少必需字段
- `Input.Field.Invalid`: 字段值无效
- `Input.Field.Illegal`: 字段值非法
- `Input.Parsing.Failed`: 输入解析失败
- `Timestamp.Expired`: 时间戳过期
- `Wrong.Service.Token`: 服务令牌错误
- `Login.Expired`: 登录过期
- `Login.Invalid`: 登录无效
- `Login.Password.Invalid`: 密码无效
- `Register.Failed`: 注册失败
- `SSO.Service.Unavailable`: SSO 服务不可用
- `Stopword.Blocked`: 包含禁用词
- `Cash.Not.Sufficient`: 金币不足
- `Comment.Not.Allowed`: 不允许评论
- `Comment.Maximum`: 评论数量达到上限
- `Content.Not.Found`: 内容未找到
- `User.Not.Found`: 用户未找到
- `Receipt.Validation.Failed`: 收据验证失败
- `Receipt.Server.Failed`: 收据服务器失败
- `Invitation.Already.Handled`: 邀请已处理
- `Message.Send.Failed`: 消息发送失败
- `Collaborator.Maximum`: 协作者数量达到上限
- `Activity.Bonus.Received`: 活动奖励已领取
- `Redeem.Failed`: 兑换失败
- `Social.Email.Banned`: 邮箱被禁止
- `Social.Verification.Failed`: 社交验证失败
- `Social.Already.Binded`: 已绑定社交账户
- `Publish.Failed.5`: 发布失败（组件不足）
- `Experience.Not.Enough`: 经验不足
- `Upload.Illegal`: 上传非法
- `Nickname.Duplicated`: 昵称重复
- `OAuth.Invalid.Client`: OAuth 客户端无效
- `OAuth.Invalid.Code`: OAuth 代码无效

## 认证方式

### 请求头与响应头都可能携带以下参数

- `x-API-Token`: API 令牌
- `x-API-AuthCode`: 认证码
- `x-API-ServiceToken`: 服务间调用令牌（带时间戳）


- `x-API-Token`: 用于普通 API 调用的短期访问令牌。部分接口会在响应中返回新的 `Token`，客户端应在后续请求中更新并使用它。
- `x-API-AuthCode`: SSO/设备绑定相关的认证码，通常用于换取 `Token` 或执行敏感操作。
- `x-API-ServiceToken`: 服务间调用使用的签名/令牌，通常包含时间戳以防重放攻击；跨区域调用时务必使用正确的区域凭证。

注意：
- 当接口需要同时传递 `Token` 与 `AuthCode` 时，请按 API 文档要求传入对应 Header，否则可能返回 `Login.Expired` 或 `Wrong.Service.Token`。
- 对于 OAuth/第三方回调（如 `ExchangeToken`），部分参数使用 `application/x-www-form-urlencoded` 传递，请确保 Content-Type 匹配。

## 数据类型

### 常见数据类型

- **User**: 用户对象
- **ExperimentSummary**: 实验摘要
- **Experiment**: 实验详情
- **Workspace**: 工作区
- **Message**: 消息
- **Comment**: 评论
- **Survey**: 调查
- **Activity**: 活动
- **Backpack**: 背包
- **Statistic**: 统计信息

### 枚举类型

- **ServiceRegion**: 服务区域 (US, China)
- **Domain**: 域 (Physics, NetLogo)
- **ContentVisibility**: 内容可见性 (Public, Private, Removed)
- **StarType**: 星标类型 (Star, Support)
- **MessageCategory**: 消息类别
- **RelationDisplayType**: 关系显示类型

## 分页参数

许多查询 API 支持分页：

- **Skip**: 整数，跳过的记录数
- **Take**: 整数，返回的记录数（通常不超过 20-50，但是可以使用-99绕过）

