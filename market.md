# 市场 API 文档

## 概述

市场控制器 (`Markets`) 处理所有与购买、支付和物品相关的操作。小道消息，以后可能会废弃。

## API 端点

### GET /

获取基本市场信息。

**请求**
```
GET /Markets/
```

**响应**
```json
{
  "Message": "Success",
  "Data": {},
  "Status": 200
}
```

### POST /PurchaseObject

购买物品。

**请求**
```
POST /Markets/PurchaseObject
Content-Type: application/json

{
  "Target": "item_name",
  "Index": 0
}
```

**参数**
**参数**
- `Target` (string, required): 物品标识（例如商品 key 或对象名），用于指定要购买的物品。
- `Index` (int, required): 物品索引或子项索引（>= 0）。

备注：若用户已拥有该物品，接口通常直接返回当前背包信息而不重复扣费。

**响应**
```json
{
  "Message": "Success",
  "Data": {
    "User": { /* 用户对象 */ },
    "Backpack": { /* 背包对象 */ }
  },
  "Status": 200
}
```

### POST /VerifyReceipt

验证支付收据。

**请求**
```
POST /Markets/VerifyReceipt
Content-Type: application/json

{
  "Receipt": "receipt_data",
  "Platform": "iOS",
  "CountryCode": "US"
}
```

**参数**
**参数**
- `Receipt` (string, required): 原始收据或凭证（由客户端提交，用于服务器侧验证）。
- `Platform` (string, required): 平台标识，例如 `iOS` 或 `Android`。
- `CountryCode` (string, optional): 国家/地区代码（默认 `US`）。

说明：验证结果可能返回以下错误情况：
- `Receipt.Validation.Failed`：收据无效或被撤销（购买未通过）。
- `Receipt.Server.Failed`：远端验证（App Store / Play）出现服务器错误。
接口在验证成功后会保存收据并完成发货，返回更新后的 `User` 对象和背包信息。

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