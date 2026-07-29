# API 测试报告

> 2026-07-24 22:35 · Goose Agent 首次全面测试

## 📊 测试结果总览

| API | 状态 | 测试项 | 结果 |
|-----|------|--------|------|
| **Private Clipboard** | ✅ 通过 | 写入文本、查询最新 | 正常 |
| **Email Sender** | ✅ 通过 | 发送邮件、查询日志 | 正常 |
| **External Storage (OSS)** | ✅ 通过 | 上传、列出、获取URL | 正常 |

---

## 1. Private Clipboard 测试

### 写入测试
```json
{"ok":true,"id":527,"type":"text","created_at":1784903718016}
```

### 查询测试
成功返回最新 3 条记录，包含：
- ID 527: 🦢 Goose Agent 测试文本
- ID 526: proxy_quick_setup.md 文件

### 注意事项
- ❌ 本地 `localhost:8976` 未运行服务
- ✅ 外网 `https://traesite.umersoft.com:8975` 正常
- 以后调用统一使用外网地址

---

## 2. Email Sender 测试

### 发送测试
```json
{"ok":true,"email_id":65,"accepted":["yangdafe@gmail.com"],"rejected":[]}
```

### 日志查询
- 成功查询到发送记录
- SMTP 响应：`250 2.0.0 OK`（Gmail 成功接收）
- 总发送记录：65 封

### 中文测试
邮件主题和正文中文显示正常（Unicode 编码正确传输）

---

## 3. External Storage (OSS) 测试

### 上传测试
```json
{
  "ok": true,
  "count": 1,
  "files": [{
    "path": "goose-test/oss_test_file.txt",
    "oss_key": "550e8400-.../external_storage/goose-test/oss_test_file.txt",
    "raw_url": "https://android-o1-images.oss-accelerate.aliyuncs.com/...",
    "name": "oss_test_file.txt",
    "size": 50,
    "content_type": "text/plain"
  }]
}
```

### 列出文件
成功列出 `goose-test/` 目录下的文件

### 获取 URL
返回可公开访问的 OSS 加速域名链接

---

## 🎯 总结

三大 API **全部可用**，可以正式投入使用。

### 后续使用规范

1. **剪贴板**：统一使用外网地址 `https://traesite.umersoft.com:8975`
2. **邮件**：发送前确认收件人，注意 30封/分钟 限制
3. **OSS**：文件按用途分子目录，注意 50MB 单文件限制

## 相关笔记

- [[配置/API配置/External Storage (OSS)]]
- [[配置/API配置/Email Sender]]
- [[配置/API配置/Private Clipboard]]
- [[00-Goose-MOC]]
