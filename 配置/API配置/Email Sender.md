# Email Sender API

> 2026-07-24 · 从 Hermes Vault 移植  
> 来源：[[配置/API配置/Email Sender]]

## 概述

Toolbox 提供的对外邮件发送接口，允许第三方程序通过 API 发送邮件并查询发送记录。

## 基础地址

| 网络 | URL |
|------|-----|
| **外网** | `https://traesite.umersoft.com:8975/api/v1/ingest/email_sender` |
| **内网** | `http://192.168.0.231:8975/api/v1/ingest/email_sender` |

## 鉴权

```
X-API-Id: email_sender
X-API-Key: es_hAtXkry70lHtCVMfr0TaQ
```

## 接口列表

### 1. 发送邮件
`POST /api/v1/ingest/email_sender/send`

**请求体**:
```json
{
  "to": "user@example.com",
  "subject": "主题",
  "text": "纯文本内容",
  "html": "<p>HTML 内容</p>",
  "attachments": [{"filename": "report.pdf", "path": "/absolute/path/to/report.pdf"}]
}
```

**约束**:
- `text` 和 `html` 至少提供一个
- 收件人最多逗号分隔多个
- 附件最多 **3 个**
- 纯文本最大 **50KB**，HTML 最大 **100KB**
- 每分钟最多 **30 封**

### 2. 查询发送日志
`GET /api/v1/ingest/email_sender/logs?limit=20&offset=0&status=sent&to=user@example.com&from_date=2026-06-01&to_date=2026-06-30`

### 3. 查询单条日志详情
`GET /api/v1/ingest/email_sender/logs/:id`

## SMTP 配置 (环境变量)

```env
EMAIL_SENDER_SMTP_HOST=smtp.gmail.com
EMAIL_SENDER_SMTP_PORT=465
EMAIL_SENDER_SMTP_SECURE=true
EMAIL_SENDER_SMTP_USER=your@gmail.com
EMAIL_SENDER_SMTP_PASS=<已配置>
X-API-Id: email_sender
X-API-Key: es_hAtXkry70lHtCVMfr0TaQ
EMAIL_SENDER_FROM=your@gmail.com
EMAIL_SENDER_DEFAULT_TO=yangxiangyuan@umer.com.cn,yangdafe@gmail.com
```

## Goose 使用场景

| 场景 | 说明 |
|------|------|
| **任务报告** | 自动化任务完成后发送邮件通知 |
| **告警通知** | 系统异常、磁盘满、服务宕机时告警 |
| **数据报告** | 定时发送数据汇总报告 |
| **日志摘要** | 每日/每周日志摘要发送给管理员 |

## curl 示例

```bash
# 发送邮件
curl -X POST "https://traesite.umersoft.com:8975/api/v1/ingest/email_sender/send" \
  -H "X-API-Id: email_sender" \
  -H "X-API-Key: es_hAtXkry70lHtCVMfr0TaQ" \
  -H "Content-Type: application/json" \
  -d '{
    "to": "yangdafe@gmail.com",
    "subject": "Goose 任务完成通知",
    "text": "任务已成功完成。\n时间: 2026-07-24 18:00:00",
    "html": "<h2>Goose 任务完成通知</h2><p>任务已成功完成。</p>"
  }'

# 查询发送日志
curl "https://traesite.umersoft.com:8975/api/v1/ingest/email_sender/logs?limit=10" \
  -H "X-API-Id: email_sender" \
  -H "X-API-Key: es_hAtXkry70lHtCVMfr0TaQ"
```

## Python 示例

```python
import requests

API_URL = "https://traesite.umersoft.com:8975/api/v1/ingest/email_sender/send"
HEADERS = {
    "X-API-Id": "email_sender",
    "X-API-Key": "es_hAtXkry70lHtCVMfr0TaQ",
    "Content-Type": "application/json"
}

payload = {
    "to": "yangdafe@gmail.com",
    "subject": "Goose 自动化报告",
    "text": "这是一封来自 Goose 的自动化邮件。",
    "html": "<h1>Goose 报告</h1><p>这是一封自动化邮件。</p>"
}

response = requests.post(API_URL, headers=HEADERS, json=payload)
print(response.json())
```

## 相关笔记

- [[配置/API配置/External Storage (OSS)]]
- [[配置/API配置/Private Clipboard]]
- [[00-Goose-MOC]]
