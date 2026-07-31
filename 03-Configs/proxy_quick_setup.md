# 🌐 海外代理快速配置指南

> 适用系统：Ubuntu 20.04+ / Debian 11+（amd64）
> 协议：VLESS + WebSocket + TLS（sing-box）

---

## 📦 第一步：安装 sing-box

```bash
# 下载 sing-box（以 1.12.0 为例，请检查最新版本）
curl -L -o /tmp/sing-box.tar.gz \
  "https://github.com/SagerNet/sing-box/releases/download/v1.12.0/sing-box-1.12.0-linux-amd64.tar.gz"

# 解压并安装
tar -zxvf /tmp/sing-box.tar.gz -C /tmp/
sudo mv /tmp/sing-box-1.12.0-linux-amd64/sing-box /usr/local/bin/
sudo chmod +x /usr/local/bin/sing-box

# 验证
sing-box version
```

---

## ⚙️ 第二步：创建配置文件

创建目录和配置文件：

```bash
mkdir -p ~/.config/sing-box
```

创建 `~/.config/sing-box/config.json`，内容如下：

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
          "google.com", "googlevideo.com", "ytimg.com",
          "x.com", "twitter.com", "t.co", "twimg.com",
          "huggingface.co", "hf.co", "cloudfront.net"
        ],
        "outbound": "proxy"
      },
      {
        "domain": [
          "github.com", "*.github.com", "*.githubusercontent.com", "*.github.io",
          "docker.com", "*.docker.com", "*.docker.io",
          "nomachine.com", "*.nomachine.com", "openai.com"
        ],
        "outbound": "proxy"
      }
    ],
    "final": "direct",
    "auto_detect_interface": true
  }
}
```

> **重要**：以上配置为**智能分流**模式，海外网站走代理，国内直连。
> 如需**全局代理**，将 `"final": "direct"` 改为 `"final": "proxy"`。

---

## 🚀 第三步：启动代理

### 前台启动（调试用）
```bash
sing-box run -c ~/.config/sing-box/config.json
```

### 后台启动（日常用）
```bash
nohup sing-box run -c ~/.config/sing-box/config.json > /tmp/sing-box.log 2>&1 &
echo $! > /tmp/sing-box.pid
```

### 验证启动
```bash
# 检查端口
ss -tlnp | grep 1080

# 检查日志
tail -f /tmp/sing-box.log
```

---

## 🔌 第四步：使用代理

### 方式 A：命令行（curl/wget/apt）
```bash
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080

# 测试
curl -x http://127.0.0.1:1080 -s -o /dev/null -w "HTTP %{http_code}\n" https://www.google.com
```

### 方式 B：浏览器
安装浏览器插件（如 **Proxy SwitchyOmega**）：
- 协议：HTTP
- 地址：127.0.0.1
- 端口：1080

### 方式 C：全局系统代理（设置 → 网络 → 代理）
- HTTP 代理：127.0.0.1:1080
- HTTPS 代理：127.0.0.1:1080

---

## ✅ 测试清单

```bash
# 基础连通性
curl -x http://127.0.0.1:1080 -s -o /dev/null -w "Google:    %{http_code}\n" https://www.google.com
curl -x http://127.0.0.1:1080 -s -o /dev/null -w "GitHub:    %{http_code}\n" https://github.com
curl -x http://127.0.0.1:1080 -s -o /dev/null -w "YouTube:   %{http_code}\n" https://www.youtube.com
curl -x http://127.0.0.1:1080 -s -o /dev/null -w "OpenAI:    %{http_code}\n" https://openai.com

# 国内直连验证
curl -s -o /dev/null -w "百度(直连): %{http_code}\n" https://www.baidu.com
```

预期输出：全部 HTTP 200（OpenAI 可能 403，表示连接成功）

---

## 🛠️ 常见问题

### Q1: 启动失败 `bind: address already in use`
```bash
# 杀掉占用端口的进程
sudo lsof -ti:1080 | xargs kill -9
```

### Q2: 无法访问任何网站
```bash
# 检查代理是否运行
ps aux | grep sing-box | grep -v grep

# 检查 DNS 解析
nslookup api-dev-sync.umersoft.com
# 应返回 47.101.56.101
```

### Q3: 部分网站能访问，部分不能
- 检查路由规则：该网站域名是否在 `proxy` 规则中
- 临时改为全局代理：将配置中 `"final": "direct"` 改为 `"final": "proxy"`，重启

### Q4: 设置开机自启
```bash
cat > /etc/systemd/system/sing-box.service << 'EOF'
[Unit]
Description=sing-box proxy
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/sing-box run -c /root/.config/sing-box/config.json
Restart=always

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable sing-box
sudo systemctl start sing-box
```

---

## 📋 配置参数速查

| 参数 | 值 |
|------|------|
| 服务器 | `api-dev-sync.umersoft.com` |
| IP | `47.101.56.101` |
| 端口 | `443` |
| 协议 | VLESS |
| UUID | `12345678-90ab-cdef-1234-567890abcdef` |
| 传输 | WebSocket |
| 路径 | `/ws/devops/health` |
| TLS | 启用 |
| SNI | `api-dev-sync.umersoft.com` |
| 本地代理 | `http://127.0.0.1:1080` |

---

> ⚠️ 本配置仅供学习交流使用，请遵守当地法律法规。
