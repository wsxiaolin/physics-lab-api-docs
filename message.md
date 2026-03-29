# 消息 API 文档

## 概述

消息控制器 (`Messages`) 处理所有与消息、评论和通知相关的操作。

## API 端点

### POST /GetMessage

获取消息。

**请求**
```
POST /Messages/GetMessage
Content-Type: application/json

{
  "MessageID": "message_id"
}
```

**参数**
- `MessageID` (string, required): 消息 ID（通常为 24 字符的 ObjectId）。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Bonuses": { /* 奖励对象 */ },
    "Statistic": { /* 统计对象 */ }
  },
  "Status": 200
}
```

### POST /HandleInvitation

处理邀请。

**请求**
```
POST /Messages/HandleInvitation
Content-Type: application/json

{
  "MessageID": "message_id"
}
```

**参数**
- `MessageID` (string, required): 消息 ID（通常为 24 字符的 ObjectId）。

### POST /GetMessages

获取消息列表。

**请求**
```
POST /Messages/GetMessages
Content-Type: application/json

{
  "CategoryID": 0,
  "Skip": 0,
  "Take": 20,
  "NoTemplates": false
}
```

**参数**
- `CategoryID` (int, required): 消息类别编号（参见 `MessageCategory` 枚举）。
- `Skip` (int, required): 跳过数量（>= 0）。
- `Take` (int, required): 返回数量，最大 20（服务端会限制）。
- `NoTemplates` (bool, optional): 是否不返回模板（默认 false）。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "Messages": [ /* 消息列表 */ ],
    "Templates": [ /* 模板列表 */ ]
  },
  "Status": 200
}
```

### POST /RemoveComment

移除评论。

**请求**
```
POST /Messages/RemoveComment
Content-Type: application/json

{
  "CommentID": "comment_id",
  "TargetType": "Experiment"
}
```

**参数**
- `CommentID` (string, required): 评论 ID（通常为 24 字符的 ObjectId）。
- `TargetType` (string, required): 目标类型。常见值：`User`（用户）、`Experiment`、`Model`、`Discussion` 等。

### POST /PostComment

发布评论。

**请求**
```
POST /Messages/PostComment
Content-Type: application/json

{
  "TargetID": "target_id",
  "TargetType": "Experiment",
  "Content": "评论内容",
  "ReplyID": "reply_id",
  "Language": "Chinese",
  "Special": "special_flag"
}
```

**参数**
- `TargetID` (string, required): 目标 ID（当 `TargetType` 为 `User` 时为用户 ID，否则为内容 ID）。
- `TargetType` (string, required): 目标类型，常见值：`User`、`Experiment`、`Model`、`Discussion` 等。
- `Content` (string, required): 评论文本，建议长度 1–65535；系统会进行停用词/敏感词检查，可能被拒绝或审查。
- `ReplyID` (string, optional): 被回复的评论 ID（用于回复）。
- `Language` (string, optional): 语言标识（例如 `Chinese`）。
- `Special` (string, optional): 特殊标志；仅当调用者为志愿者（`User.IsVolunteer`）时有效。

备注：
- 当 `TargetType == "User"` 时，被评论的用户必须存在且调用者不可被封禁；若双方互相屏蔽则操作被拒绝。
- 发布评论会触发审查与计数逻辑，可能返回 `Comment.Not.Allowed`、`Stopword.Blocked` 或 `Stopword.Blocked.Moderated` 等错误码。

### POST /GetComments

获取评论列表。

**请求**
```
POST /Messages/GetComments
Content-Type: application/json

{
  "TargetID": "target_id",
  "TargetType": "Experiment",
  "CommentID": "comment_id",
  "Special": "special_flag",
  "Skip": 0,
  "Take": 20
}
```

**参数**
- `TargetID` (string, required): 目标 ID（用户 ID 或内容 ID）。
- `TargetType` (string, required): 目标类型（`User` 或内容类别）。
- `CommentID` (string, optional): 起始评论 ID（用于定位分页）。
- `Special` (string, optional): 特殊筛选标志。
- `Skip` (long, required): 分页偏移。
- `Take` (int, required): 返回条数，最大 20（服务端会限制）。

**参数**
- `TargetID` (string, required): 目标 ID（用户 ID 或内容 ID）。
- `TargetType` (string, required): 目标类型（`User` 或内容类别）。
- `CommentID` (string, optional): 起始评论 ID（用于定位分页）。如果提供，服务端会读取该评论的 `Timestamp` 并以 `Timestamp + 1` 作为时间线起点，只返回时间戳小于该值的评论（用于基于评论定位的分页）。
- `Special` (string, optional): 特殊筛选标志。该值用于与评论文档的 `Flags` 字段（字符串数组）做等值匹配——只有包含该 flag 的评论会被返回。常见 flag 示例：`Locked`（被锁定的评论）、`Anonymous`（匿名评论）、`Reminder`（提醒类评论）。注意：部分 flag 由服务端或管理员/志愿者设置（例如当提交带 `Reminder` 标志的评论时，服务端会同时添加 `Locked`）。客户端应传入确切的 flag 字符串以做过滤；若不传则不按 flags 过滤。
- `Skip` (long, required): 时间戳分页上限（Unix 毫秒）。若为 0 则从最新开始；否则返回 `Timestamp < Skip` 的评论（与 `CommentID` 配合使用可实现基于时间的分页）。
- `Take` (int, required): 返回条数，最大 20（服务端会限制）。

备注：默认不返回已隐藏的评论（服务端仅返回 `Hidden == false` 的条目）；内部或管理员接口可能允许包含已隐藏评论。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "Target": { /* 目标对象 */ },
    "Comments": [ /* 评论列表 */ ],
    "Count": 100
  },
  "Status": 200
}
```

### POST /SendMessages

发送消息给多个用户。

**请求**
```
POST /Messages/SendMessages
Content-Type: application/json

{
  "TargetID": ["user1", "user2"],
  "Category": "Experiment",
  "SummaryID": ["summary1", "summary2"],
  "TemplateID": "template_id"
}
```

**参数**
- `TargetID` (string[], required): 目标用户 ID 列表，长度 1–50。
- `Category` (string, required): 消息/摘要所属类别（用于匹配 `SummaryID`）。
- `SummaryID` (string[], optional): 与 `TargetID` 对应的一组摘要 ID；若提供，长度必须等于 `TargetID` 的长度。
- `TemplateID` (string, required): 模板 ID，用于生成消息内容。

验证规则：仅管理员可调用；`TargetID` 长度上限 50；若 `SummaryID` 非空，其长度须与 `TargetID` 一致。