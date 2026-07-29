# 海外代理配置笔记

## 代理信息

| 字段 | 值 |
|------|------|
| **名称** | Trae跳板机 |
| **类型** | VLESS + WebSocket + TLS |
| **服务器** | `api-dev-sync.umersoft.com` |
| **IP** | `47.101.56.101` (阿里云) |
| **端口** | `443` |
| **UUID** | `12345678-90ab-cdef-1234-567890abcdef` ✅ 已验证有效 |
| **路径** | `/ws/devops/health` |
| **TLS** | 启用 |
| **SNI** | `api-dev-sync.umersoft.com` |
| **Host** | `api-dev-sync.umersoft.com` |
| **证书** | `*.umersoft.com` (上海麦色医疗科技有限公司) |

## 测试结果 (2026-07-24 14:46)

| 测试项 | 结果 |
|--------|------|
| TCP 连接 | ✅ |
| TLS 握手 | ✅ |
| WebSocket 升级 | ✅ |
| Google 访问 | ✅ HTTP 200 |
| GitHub 访问 | ✅ HTTP 200 |
| NoMachine 访问 | ✅ HTTP 200 |

## sing-box 配置

### 主配置 (`/root/.proxy/sing-box/config.json`)

```json
{
  "log": {"level": "info", "timestamp": true},
  "inbounds": [
    {"type": "http", "tag": "http-in", "listen": "127.0.0.1", "listen_port": 1080}
  ],
  "outbounds": [
    {
      "type": "vless",
      "tag": "proxy",
      "server": "api-dev-sync.umersoft.com",
      "server_port": 443,
      "uuid": "12345678-90ab-cdef-1234-567890abcdef",
      "transport": {
        "type": "ws",
        "path": "/ws/devops/health",
        "headers": {"Host": "api-dev-sync.umersoft.com"}
      },
      "tls": {
        "enabled": true,
        "server_name": "api-dev-sync.umersoft.com"
      }
    },
    {"type": "direct", "tag": "direct"}
  ],
  "route": {
    "rules": [
      {
        "domain_suffix": [
          "youtube.com", "googlevideo.com", "ytimg.com",
          "x.com", "twitter.com", "t.co", "twimg.com",
          "huggingface.co", "*.huggingface.co", "hf.co", "*.hf.co",
          "dashscope.aliyuncs.com", "*.dashscope.aliyuncs.com"
        ],
        "outbound": "proxy"
      },
      {
        "domain": [
          "github.com", "*.github.com", "*.githubusercontent.com", "*.github.io",
          "registry-1.docker.io", "*.docker.io", "*.docker.com",
          "*.cloudfront.net"
        ],
        "outbound": "proxy"
      }
    ],
    "final": "direct",
    "auto_detect_interface": true
  }
}
```

### 启动命令

```bash
sing-box run -c /root/.proxy/sing-box/config.json &
```

### 使用方式

```bash
# HTTP 代理
curl -x http://127.0.0.1:1080 https://www.google.com

# 设置环境变量
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080
```

## 注意事项

1. **UUID 已验证**: `12345678-90ab-cdef-1234-567890abcdef` 是唯一有效的 UUID
2. **另一个 UUID** (`F3055F86-4B30-4FF8-A6F6-6F7F78E45118`) **无效**
3. 路由规则采用智能分流：海外网站走代理，国内直连
4. sing-box 版本: 1.12+ (支持新 DNS 配置格式)
