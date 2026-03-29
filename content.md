## 概述

内容控制器 (`Contents`) 处理所有与项目（实验、模型、讨论）相关的操作，包括发布、查询、点赞、评论等。

## API 端点

### GET /

获取基本服务信息。

**请求**
```
GET /Contents/
```

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "$type": "<>f__AnonymousType3`3[[System.String, System.Private.CoreLib],[System.String, System.Private.CoreLib],[System.Int64, System.Private.CoreLib]], Quantum API",
    "Region": "US",
    "Domain": "Physics",
    "Timestamp": 1640995200
  },
  "Status": 200
}
```

### POST /SubmitExperiment

提交实验。

**请求**
```
POST /Contents/SubmitExperiment
Content-Type: application/json

{
  "Summary": {
    "ID": "experiment_id",
    "Title": "实验标题",
    "Description": "实验描述",
    "Category": "Experiment",
    "Tags": ["标签1", "标签2"]
  },
  "Experiment": { /* 实验数据 */ },
  "Workspace": { /* 工作区数据 */ },
  "Localization": "本地化数据",
  "Request": {
    "FileSize": 1024000
  }
}
```

**参数**
  - `Summary` (object, required): 项目摘要（`ExperimentSummary`），包含 `Title`、`Description`、`Category`、`Tags` 等。
  - `Experiment` / `Workspace` (object, conditional): 视 `Startup.Domain` 决定，物理域为 `Experiment` 对象，NetLogo 可能使用 `Workspace`。
  - `Localization` (string, optional): 本地化信息。
  - `Request` (object, optional): 上传请求，包含 `FileSize` 等；若提供，接口会返回上传令牌。
  - 校验：新提交且为 `Model` 类别时，非 `Junior` 用户可能被限制；`Summary` 的内容会做停用词检查。

### POST /ConfirmExperiment

确认项目图像更新。

**请求**
```
POST /Contents/ConfirmExperiment
Content-Type: application/json

{
  "SummaryID": "summary_id",
  "Category": "Experiment",
  "Image": 1,
  "Extension": "jpg"
}
```

**参数**
  - `SummaryID` (string, required): 摘要 ID（24 字符）。
  - `Category` (string, required): 类别。
  - `Image` (int, required): 图片版本号；若随请求上传文件，则服务器会替换为新版本。
  - `Extension` (string, optional): 文件扩展名（当文件随请求提交时）。

### POST /RemoveExperiment

移除项目。

**请求**
```
POST /Contents/RemoveExperiment
Content-Type: application/json

{
  "SummaryID": "summary_id",
  "Category": "Experiment",
  "Hiding": false
}
```

**参数**
- `SummaryID`: 字符串，摘要 ID
- `Category`: 字符串，类别
- `Hiding`: 布尔值，是否隐藏

### POST /RestoreExperiment

恢复项目。

**请求**
```
POST /Contents/RestoreExperiment
Content-Type: application/json

{
  "SummaryID": "summary_id",
  "Category": "Experiment"
}
```

**参数**
- `SummaryID`: 字符串，摘要 ID
- `Category`: 字符串，类别

### POST /SelectExperiment

精选/取消精选项目。

**请求**
```
POST /Contents/SelectExperiment
Content-Type: application/json

{
  "SummaryID": "summary_id",
  "Category": "Experiment"
}
```

**参数**
- `SummaryID`: 字符串，摘要 ID
- `Category`: 字符串，类别

### POST /AcknowledgeExperiment

为项目添加标签。

**请求**
```
POST /Contents/AcknowledgeExperiment
Content-Type: application/json

{
  "SummaryID": "summary_id",
  "Category": "Experiment",
  "Tag": "精选"
}
```

**参数**
- `SummaryID`: 字符串，摘要 ID
- `Category`: 字符串，类别
- `Tag`: 字符串，标签

### POST /MoveCategory

移动项目到其他类别。

**请求**
```
POST /Contents/MoveCategory
Content-Type: application/json

{
  "SummaryID": "summary_id",
  "Category": "Experiment",
  "Target": "Model"
}
```

**参数**
- `SummaryID`: 字符串，摘要 ID
- `Category`: 字符串，当前类别
- `Target`: 字符串，目标类别

### POST /GetLibrary

获取库内容。

**请求**
```
POST /Contents/GetLibrary
Content-Type: application/json

{
  "Identifier": "library_id",
  "Language": "Chinese"
}
```

**参数**
- `Identifier`: 字符串，库标识符
- `Language`: 字符串，语言

### POST /QueryExperiments

查询项目列表。

**请求**
```
POST /Contents/QueryExperiments
Content-Type: application/json

{
  "Query": {
    "Category": "Experiment",
    "UserID": "user_id",
    "Tags": ["标签"],
    "Skip": 0,
    "Take": 20,
    "Sort": "Popularity",
    "Languages": ["Chinese"]
  }
}
```

**参数**
- `Query`: 对象，查询参数，包含以下字段：
  - `Category` (string, 可选，默认 "Experiment"): 实体类别，常见值 "Experiment"、"Model"、"Discussion" 等。若为 "Model"，部分字段（如 `Languages`) 可能不必提供。
  - `Languages` (string[], 条件必需): 语言数组；当 `UserID` 为 `null` 且 `Category` != "Model" 时必须提供。示例: ["Chinese"]。
  - `ExcludeLanguages` (string[], 可选): 排除的语言数组。
  - `Tags` (string[], 可选): 标签数组；当 `UserID` 为 `null` 时最多允许 2 个标签，用于按标签过滤。
  - `ModelTags` (string[], 可选): 专用于模型的标签数组。
  - `ExcludeTags` (string[], 可选): 要排除的标签数组。
  - `ModelID` (string, 可选): 模型 ID，24 字符的 ObjectId。
  - `ParentID` (string, 可选): 父项目 ID，24 字符。
  - `UserID` (string, 可选): 用户 ID；特殊值 "Mine" 表示当前用户，"Favorite" 表示收藏；若不为空且不是空字符串，长度必须为 24。
  - `Special` (string, 可选): 特殊查询类型；当前支持 `"Star"`、`"Support"` 等（视 API 实现）。
  - `From` (string, 可选): 游标分页用 ID（24 字符）。
  - `Skip` (int, 可选): 跳过数量（>=0）。
  - `Take` (int, 可选): 返回条数，最大 30（服务端会校验），若未提供服务端通常采用默认值（例如 20）。
  - `Days` (int, 可选): 按最近 N 天过滤。
  - `Sort` (enum|string, 可选): 排序方式，支持：`Default` (0)、`Popularity` (1)、`Historical` (2)、`Special` (3)、`Random` (4)。可传字符串或对应数字。
  - `ShowAnnouncement` (bool, 可选): 是否包含公告类内容。
  - `ShowOffline` (bool, 可选): 是否包含离线内容（通常内部使用）。
- 验证规则：
  - 当 `UserID == null` 时，若 `Category != "Model"` 则 `Languages` 不可为空；`Tags` 最多 2 个。
  - `UserID` 非空且非空字符串时长度必须为 24。
  - `From` 必须为 24 字符 ID（若提供）。
  - `Take` 不得大于 30（服务端校验）。
- 示例：
```
{
  "Query": {
    "Category": "Experiment",
    "Languages": ["Chinese"],
    "Tags": ["热学","动能"],
    "Skip": 0,
    "Take": 20,
    "Sort": "Popularity"
  }
}
```

### POST /GetSummary

获取项目摘要。

**请求**
```
POST /Contents/GetSummary
Content-Type: application/json

{
  "ContentID": "content_id",
  "Category": "Experiment"
}
```

**参数**
  - `SummaryID` (string, required): 内容/摘要 ID（24 字符）。
  - `Category` (string, required): 类别。
  - `WithSummary` / `Language` / `Skip` / `Take`：用于控制返回的扩展信息与分页。

### POST /GetDerivatives

获取项目衍生内容。

**请求**
```
POST /Contents/GetDerivatives
Content-Type: application/json

{
  "ContentID": "content_id",
  "Category": "Experiment",
  "WithSummary": true,
  "Language": "Chinese"
}
```

**参数**
  - `ContentID`  (string, required): 内容/摘要 ID（24 字符）。
  - `Category` (string, required): 类别。
  - `WithSummary` / `Language` / `Skip` / `Take`：用于控制返回的扩展信息与分页。

### POST /GetSupporters

获取项目支持者。

**请求**
```
POST /Contents/GetSupporters
Content-Type: application/json

{
  "ContentID": "content_id",
  "Category": "Experiment",
  "Skip": 0,
  "Take": 10
}
```

**参数**
- `ContentID`: 字符串，内容 ID
- `Category`: 字符串，类别
- `Skip`: 整数，跳过数量
- `Take`: 整数，返回数量

### POST /GetProfile

获取用户资料。

**请求**
```
POST /Contents/GetProfile
Content-Type: application/json

{
  "ID": "user_id"
}
```

**参数**
- `ID`: 字符串，用户 ID

### POST /VisitExperiment

记录项目访问。

**请求**
```
POST /Contents/VisitExperiment
Content-Type: application/json

{
  "SummaryID": "summary_id",
  "Category": "Experiment"
}
```

**参数**
- `SummaryID`: 字符串，摘要 ID
- `Category`: 字符串，类别

### POST /GetExperiment

获取实验保存数据。

**请求**
```
POST /Contents/GetExperiment
Content-Type: application/json

{
  "ContentID": "content_id"
}
```

**参数**
- `ContentID`: 字符串，内容 ID

### POST /GetWorkspace

获取工作区保存数据。

**请求**
```
POST /Contents/GetWorkspace
Content-Type: application/json

{
  "ContentID": "content_id"
}
```

**参数**
- `ContentID`: 字符串，内容 ID

### POST /IsStarred

检查项目是否已星标（已废弃）。

**请求**
```
POST /Contents/IsStarred
Content-Type: application/json

{
  "ContentID": "content_id",
  "Category": "Experiment"
}
```

**参数**
- `ContentID`: 字符串，内容 ID
- `Category`: 字符串，类别

### POST /StarContent

星标/取消星标项目。

**请求**
```
POST /Contents/StarContent
Content-Type: application/json

{
  "ContentID": "content_id",
  "Category": "Experiment",
  "Status": true,
  "Type": "Star"
}
```

**参数**
- `ContentID`: 字符串，内容 ID
- `Category`: 字符串，类别
- `Status`: 布尔值，星标状态
- `Type`: 字符串，星标类型（"Star" 或 "Support"）

### POST /InviteCoauthor

邀请协作者。

**请求**
```
POST /Contents/InviteCoauthor
Content-Type: application/json

{
  "Category": "Experiment",
  "SummaryID": "summary_id",
  "TargetID": "target_user_id"
}
```

**参数**
- `Category`: 字符串，类别
- `SummaryID`: 字符串，摘要 ID
- `TargetID`: 字符串，目标用户 ID

### POST /RemoveCoauthor

移除协作者。

**请求**
```
POST /Contents/RemoveCoauthor
Content-Type: application/json

{
  "Category": "Experiment",
  "SummaryID": "summary_id",
  "TargetID": "target_user_id"
}
```

**参数**
- `Category`: 字符串，类别
- `SummaryID`: 字符串，摘要 ID
- `TargetID`: 字符串，目标用户 ID

