# External Storage (OSS) API

> 2026-07-24 · 从 Hermes Vault 移植  
> 来源：[[配置/API配置/External Storage]]

## 概述

Toolbox 提供的外部存储接口，允许第三方程序通过 API 读写阿里云 OSS 的 `external_storage` 区域。

**存储隔离**: 所有操作限定在 `550e8400-e29b-41d4-a716-446655442082/external_storage/` 路径下

## 基础地址

| 网络 | URL |
|------|-----|
| **外网** | `https://traesite.umersoft.com:8975/api/v1/skills/external_storage` |
| **内网** | `https://192.168.0.231:8975/api/v1/skills/external_storage` |
| **内网上传用** | `https://192.168.0.231:8975/` (避免 VPC 对等延迟超时) |

## 鉴权

```
X-API-Id: external_storage
X-API-Key: 4a211084d059Pk0C6t8dlk3ew61d46
```

## Bucket 信息

| 项目 | 值 |
|------|-----|
| **Bucket** | `android-o1-images.oss-cn-shanghai.aliyuncs.com` |
| **加速域名** | `oss-accelerate.aliyuncs.com` |
| **OSS Key 前缀** | `550e8400-e29b-41d4-a716-446655442082/external_storage/` |

## 接口列表

### 1. 上传文件
`POST /api/v1/skills/external_storage/upload?path=subdir/`
- multipart/form-data，字段名 `files`
- 上传后自动设为公共读

### 2. 下载文件
`GET /api/v1/skills/external_storage/download?path=xxx`

### 3. 获取原始 URL
`GET /api/v1/skills/external_storage/raw_url?path=xxx`

### 4. 列出文件
`GET /api/v1/skills/external_storage/list?prefix=subdir/&recursive=true`

### 5. 删除文件
`DELETE /api/v1/skills/external_storage/delete?path=xxx`

### 6. 文件夹打包下载
`GET /api/v1/skills/external_storage/folder/download?path=subdir/`

### 7. 获取文件信息
`GET /api/v1/skills/external_storage/info?path=xxx`

## 限制

- 单文件最大 **50MB**
- 路径遍历防护（不允许 `..`）
- 上传文件自动 **public-read**

## Goose 使用场景

| 场景 | 说明 |
|------|------|
| **笔记附件同步** | 将 Obsidian 附件上传到 OSS，实现多端共享 |
| **脚本分发** | 上传常用脚本到 OSS，其他服务器通过 API 下载 |
| **数据备份** | 定期备份重要数据到 OSS |
| **文件共享** | 生成公共链接分享文件给外部用户 |

## curl 示例

```bash
# 上传文件
curl -X POST "https://traesite.umersoft.com:8975/api/v1/skills/external_storage/upload?path=test/" \
  -H "X-API-Id: external_storage" \
  -H "X-API-Key: 4a211084d059Pk0C6t8dlk3ew61d46" \
  -F "files=@/path/to/file.txt"

# 下载文件
curl -o output.txt "https://traesite.umersoft.com:8975/api/v1/skills/external_storage/download?path=test/file.txt" \
  -H "X-API-Id: external_storage" \
  -H "X-API-Key: 4a211084d059Pk0C6t8dlk3ew61d46"

# 列出文件
curl "https://traesite.umersoft.com:8975/api/v1/skills/external_storage/list?prefix=test/&recursive=true" \
  -H "X-API-Id: external_storage" \
  -H "X-API-Key: 4a211084d059Pk0C6t8dlk3ew61d46"
```

## 相关笔记

- [[配置/API配置/Email Sender]]
- [[配置/API配置/Private Clipboard]]
- [[00-Goose-MOC]]
