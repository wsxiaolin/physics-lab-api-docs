# 调查 API 文档

## 概述

调查控制器 (`Surveys`) 处理所有与调查和反馈相关的操作。

## API 端点

### POST /GetSurvey

获取特定调查。

**请求**
```
POST /Surveys/GetSurvey
Content-Type: application/json

{
  "SurveyID": "survey_id"
}
```

**参数**
- `SurveyID`: 字符串，调查 ID

**响应**
```json
{
  "Message": "Success",
  "Data": { /* 调查对象 */ },
  "Status": 200
}
```

### POST /SubmitSurvey

提交调查结果。

**请求**
```
POST /Surveys/SubmitSurvey
Content-Type: application/json

{
  "SurveyID": "survey_id",
  "Record": {
    "UserID": "user_id",
    "SurveyID": "survey_id",
    "Trigger": "trigger_data",
    "Answers": { /* 答案数据 */ }
  }
}
```

**参数**
**参数**
- `SurveyID` (string, required): 调查 ID（通常为 24 字符的 ObjectId）。
- `Record` (object, required): 调查记录（`SurveyRecord`），结构如下：
  - `ID` (string, optional): 记录 ID。
  - `UserID` (string, required): 提交者用户 ID（ObjectId），必须等于当前请求用户 ID。
  - `SurveyID` (string, required): 调查 ID（ObjectId），应与外层 `SurveyID` 一致。
  - `Trigger` (string, required): 触发器/来源标识（不得为 null）。
  - `Conditions` (string[], optional): 附加条件或上下文标签列表。
  - `StartDate` (long, optional): 开始时间（Unix 时间戳）。
  - `FinishDate` (long, optional): 完成时间（Unix 时间戳）。
  - `Activities` (object, optional): 用户活动信息（类型 `SurveyUser`，内部使用）。
  - `Answers` (array, required): 答案数组，每项为 `SurveyAnswer`：
    - `Result` (string[]): 答案内容数组（可能为多选或文本）。
    - `Duration` (int): 答题耗时（秒）。
    - `Activated` (int[]): 激活的选项索引集合。

验证规则：
- `Record.UserID` 必须等于当前登录用户 ID；
- `Record.Trigger` 必须存在且非空；
- `Record.SurveyID` 必须与请求中的 `SurveyID` 一致；
否则接口会返回 `FieldIllegal` 或 403 错误。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Statistic": { /* 统计对象 */ }
  },
  "Status": 200
}
```