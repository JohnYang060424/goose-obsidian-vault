# Private Clipboard API

> 2026-07-24 · 从 Hermes Vault 移植  
> 来源：[[配置/API配置/private-clipboard-api]]

## 1. 鉴权信息

| 项目 | 值 |
|------|------|
| **API Id** | `private_clipboard` |
| **API Key** | `pcb_nuS0qf-W63qcvAPsrG5ePA` |
| **Base URL (内网)** | `http://localhost:8976` |
| **Base URL (外网)** | `https://traesite.umersoft.com:8975` |
| **速率限制** | 60 次/分钟 |

### 请求头要求

所有请求必须携带以下 Header：

```
X-API-Id: private_clipboard
X-API-Key: pcb_nuS0qf-W63qcvAPsrG5ePA
```

---

## 2. 接口列表

### 2.1 写入文本

```
POST /api/v1/ingest/private_clipboard/text
Content-Type: application/json
```

**请求体**：

```json
{
  "text": "这是一段要保存到剪贴板的文字",
  "group_id": -1721462400000
}
```

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `text` | string | 是 | 文本内容，最大 50,000 字符 |
| `group_id` | number | 否 | 分组 ID（负数时间戳），用于将多条内容归为一组 |

**成功响应**：

```json
{
  "ok": true,
  "id": 42,
  "type": "text",
  "created_at": 1721462400000
}
```

---

### 2.2 上传文件/图片（支持多文件）

```
POST /api/v1/ingest/private_clipboard/upload
Content-Type: multipart/form-data
```

**请求体**（multipart/form-data）：

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `files` | File[] | 是 | 文件数组，最多 10 个，单文件最大 200MB |
| `group_id` | number | 否 | 分组 ID，用于将多个文件归为一组 |

**成功响应**：

```json
{
  "ok": true,
  "group_id": -1721462400000,
  "items": [
    {
      "id": 43,
      "type": "image",
      "file_name": "screenshot.png",
      "file_mime": "image/png",
      "file_size": 123456,
      "file_url": "/uploads/private_clipboard/1721462400000-abc123.png",
      "created_at": 1721462400000
    }
  ]
}
```

---

### 2.3 查询最近 50 条

```
GET /api/v1/ingest/private_clipboard/latest?limit=50
```

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `limit` | number | 否 | 返回条数，默认 50，最大 500 |

**成功响应**：

```json
{
  "ok": true,
  "items": [
    {
      "id": 42,
      "type": "text",
      "text_content": "Hello from API",
      "created_at": 1721462400000,
      "items": [],
      "group_types": ["text"]
    }
  ],
  "total": 1
}
```

---

## 3. 常见错误码

| HTTP 状态码 | 错误信息 | 说明 |
|-------------|----------|------|
| 400 | `文本内容不能为空` | text 字段为空 |
| 400 | `未收到文件` | upload 接口没有文件 |
| 400 | `文件数量不能超过 10 个` | 超过单次上传限制 |
| 401 | `missing api id or key` | 缺少 X-API-Id 或 X-API-Key |
| 401 | `invalid api key` | API Key 错误 |
| 403 | `skill id mismatch` | X-API-Id 不是 private_clipboard |
| 429 | `请求过于频繁，请在 X 秒后重试` | 超过 60 次/分钟限制 |
| 500 | `操作失败` | 服务端异常 |

---

## 4. 分组机制说明

`group_id` 用于将多条内容（文本 + 图片 + 文件）关联为一条"组合记录"：

1. **生成方式**：使用负数时间戳，如 `-Date.now()`
2. **使用场景**：
   - 先调用 `/text` 写入文本，带上 `group_id: -1721462400000`
   - 再调用 `/upload` 上传图片/文件，带上相同的 `group_id: -1721462400000`
   - 查询时，它们会合并为一条 `type: "mixed"` 的记录
3. **不传 group_id**：
   - `/text` 会自动成为独立记录
   - `/upload` 会自动生成一个 `group_id`（负数时间戳）

---

## 5. 注意事项

1. **字符编码（重要）**：发送 JSON 请求时，必须确保请求头携带 `Content-Type: application/json; charset=utf-8`。如果使用 Python `requests` 库，`json=` 参数会自动处理；如果用 `data=` 手动传字符串，需显式设置 `headers={'Content-Type': 'application/json; charset=utf-8'}`，否则中文等非 ASCII 字符可能被替换为 `?` 导致数据损坏。
2. **北京时间**：所有时间戳均为毫秒级 Unix 时间戳，前端展示时需转换为北京时间（UTC+8）
3. **文件访问**：返回的 `file_url` 是相对路径，完整 URL 为 `http://localhost:8976/uploads/private_clipboard/xxx.png`
4. **API Key 安全**：`pcb_nuS0qf-W63qcvAPsrG5ePA` 是明文密钥，请妥善保管，不要泄露到公开代码仓库
5. **内网/外网**：
   - 本机调用使用 `http://localhost:8976`
   - 外网调用使用 `https://traesite.umersoft.com:8975`

---

## 6. 安全说明

### 已实现的安全防护

| 防护项 | 实现方式 |
|--------|----------|
| **鉴权** | API Key SHA256 哈希校验，Skill ID 精确匹配 |
| **存储型 XSS** | 文本存库前 HTML 转义（`< > & " '` 全部转义为实体） |
| **路径穿越** | 文件名清洗：移除 `..` 模式、剥离目录路径、禁止危险字符 |
| **SQL 注入** | 全参数化查询（`?` 占位符），无字符串拼接 |
| **速率限制** | 同一 IP 每分钟最多 60 次请求，超限返回 429 |
| **大小限制** | 文本 50,000 字符，单文件 200MB，单次最多 10 文件 |
| **原型污染** | Express 默认防护，无 `__proto__` 直接操作 |

---

## 7. Goose 使用场景

| 场景 | 说明 |
|------|------|
| **跨设备复制粘贴** | Windows 复制内容 → Ubuntu 粘贴，或反向操作 |
| **代码片段共享** | 将生成的代码片段存入剪贴板，Windows 端直接取用 |
| **文件传输** | 小文件（<200MB）通过剪贴板 API 中转传输 |
| **任务状态同步** | 将任务执行状态/结果存入剪贴板，供其他设备查询 |
| **图片中转** | 截图后上传到剪贴板，Windows 端直接查看 |

## curl 示例

```bash
# 写入文本
curl -X POST http://localhost:8976/api/v1/ingest/private_clipboard/text \
  -H "X-API-Id: private_clipboard" \
  -H "X-API-Key: pcb_nuS0qf-W63qcvAPsrG5ePA" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d '{"text": "Hello from Goose Agent"}'

# 上传文件
curl -X POST http://localhost:8976/api/v1/ingest/private_clipboard/upload \
  -H "X-API-Id: private_clipboard" \
  -H "X-API-Key: pcb_nuS0qf-W63qcvAPsrG5ePA" \
  -F "files=@/path/to/screenshot.png"

# 查询最近内容
curl http://localhost:8976/api/v1/ingest/private_clipboard/latest?limit=10 \
  -H "X-API-Id: private_clipboard" \
  -H "X-API-Key: pcb_nuS0qf-W63qcvAPsrG5ePA"
```

## Python 示例

```python
import requests
import time

API_BASE = "http://localhost:8976"
HEADERS = {
    "X-API-Id": "private_clipboard",
    "X-API-Key": "pcb_nuS0qf-W63qcvAPsrG5ePA",
    "Content-Type": "application/json; charset=utf-8"
}

# 写入文本
def write_text(text, group_id=None):
    payload = {"text": text}
    if group_id:
        payload["group_id"] = group_id
    resp = requests.post(f"{API_BASE}/api/v1/ingest/private_clipboard/text", 
                         headers=HEADERS, json=payload)
    return resp.json()

# 查询最新内容
def get_latest(limit=10):
    resp = requests.get(f"{API_BASE}/api/v1/ingest/private_clipboard/latest?limit={limit}",
                        headers=HEADERS)
    return resp.json()

# 使用示例
group_id = -int(time.time() * 1000)  # 负数时间戳
write_text("这是来自 Goose 的消息", group_id=group_id)
print(get_latest(5))
```

## 相关笔记

- [[配置/API配置/External Storage (OSS)]]
- [[配置/API配置/Email Sender]]
- [[00-Goose-MOC]]
