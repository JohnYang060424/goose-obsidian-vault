# 通过代理下载并安装 NoMachine 完整指南

> 最后更新：2026-07-24
> **当前版本**：NoMachine 9.8.2 ✅ 已安装运行

---

## 🎯 最快下载方式（已验证）

从 `traesite.umersoft.com:8975` 下载 zip 包，配合 sing-box 代理 + aria2 多线程：

```bash
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080

# 16线程下载（约 30 秒完成）
aria2c -x 16 -s 16 -k 4M -d /tmp -o nomachine.zip \
  "https://traesite.umersoft.com:8975/uploads/private_clipboard/1784877691689-c549a89701b7.zip"

# 解压
unzip -o /tmp/nomachine.zip -d /tmp/

# 安装
echo 'K9#mP2$vL9@qR5wZ1nJ8*cT4&yH6bF3!xW7@' | su - root -c '
  dpkg -i /tmp/nomachine_9.8.2_1_amd64.deb
  systemctl enable nxserver && systemctl start nxserver
'
```

## 前置条件

- 系统：Ubuntu 24.04 x86_64
- 普通用户：`john`（密码：`93220`）
- Root 密码：`K9#mP2$vL9@qR5wZ1nJ8*cT4&yH6bF3!xW7@`
- 代理：sing-box VLESS 代理（HTTP 端口 1080）

---

## 1️⃣ Root 登录方式

```bash
# 方式一：su 切换
su - root
# 输入密码：K9#mP2$vL9@qR5wZ1nJ8*cT4&yH6bF3!xW7@

# 方式二：su -c 执行命令
echo 'K9#mP2$vL9@qR5wZ1nJ8*cT4&yH6bF3!xW7@' | su - root -c '你的命令'

# 方式三：sudo
echo '93220' | sudo -S bash -c '你的命令'
```

---

## 2️⃣ 配置 sing-box 代理

### 代理配置文件位置
```
/root/.proxy/sing-box/config.json
```

### 代理参数
| 参数 | 值 |
|------|------|
| 类型 | VLESS + WebSocket + TLS |
| 服务器 | `api-dev-sync.umersoft.com` |
| IP | `47.101.56.101` |
| 端口 | `443` |
| UUID | `12345678-90ab-cdef-1234-567890abcdef` |
| 路径 | `/ws/devops/health` |
| HTTP 代理端口 | `127.0.0.1:1080` |

### 启动代理

```bash
# root 身份启动
echo 'K9#mP2$vL9@qR5wZ1nJ8*cT4&yH6bF3!xW7@' | su - root -c '
  pkill -9 sing-box 2>/dev/null
  sleep 2
  nohup sing-box run -c /root/.proxy/sing-box/config.json > /root/.proxy/sing-box/sing-box.log 2>&1 &
  sleep 3
  ss -tlnp | grep 1080
'
```

### 设置环境变量
```bash
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080
```

### 测试代理
```bash
curl -x http://127.0.0.1:1080 -s -o /dev/null -w "HTTP %{http_code}\n" https://www.google.com
curl -x http://127.0.0.1:1080 -s -o /dev/null -w "HTTP %{http_code}\n" https://github.com
```

---

## 3️⃣ 下载 NoMachine

### ⚠️ 重要说明
NoMachine 官网使用 Cloudflare 反爬保护，**命令行下载无法获取真正的 .deb 文件**（总是返回 HTML 页面）。

### 推荐方法：手动下载后传输

1. **在 Windows 浏览器打开**：
   ```
   https://www.nomachine.com/download-linux&id=1
   ```
2. 点击 `nomachine_8.18.1_1_x86_64.deb` 下载
3. **传输到 Ubuntu**（任选一种）：
   ```bash
   # 方法 A：scp
   scp nomachine_8.18.1_1_x86_64.deb john@<Ubuntu IP>:/tmp/
   
   # 方法 B：U盘拷贝到 /tmp/
   
   # 方法 C：共享文件夹
   ```

### 备用方法：尝试代理下载（可能失败）
```bash
export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080

# 尝试直接下载 CDN 链接
curl -L -o /tmp/nomachine.deb \
  -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0" \
  "https://download.nomachine.com/download/8.18/Linux/nomachine_8.18.1_1_x86_64.deb"

# 验证是否为有效 deb
file /tmp/nomachine.deb
# 正确输出应包含：Debian binary package
# 错误输出：HTML document
```

---

## 4️⃣ 安装 NoMachine

```bash
echo 'K9#mP2$vL9@qR5wZ1nJ8*cT4&yH6bF3!xW7@' | su - root -c '
  # 安装
  dpkg -i /tmp/nomachine_8.18.1_1_x86_64.deb
  apt-get install -f -y  # 修复依赖

  # 启动服务
  systemctl enable nxserver
  systemctl start nxserver

  # 验证
  systemctl status nxserver --no-pager | head -10
'
```

### 一键安装脚本
```bash
bash /home/john/yang_goose_files/scripts/deploy/install_nomachine.sh
```

---

## 5️⃣ 连接 NoMachine

### Windows 客户端
1. 下载：https://www.nomachine.com/download-windows
2. 安装后打开 NoMachine
3. 添加新连接：
   - **协议**：NX
   - **地址**：Ubuntu 的 IP 地址
   - **端口**：4000
   - **用户名**：`john`
   - **密码**：`93220`

---

## 🔄 备选方案：XRDP（已安装可用）

如果 NoMachine 下载困难，可使用系统已安装的 XRDP：

1. **Windows 端**：打开"远程桌面连接"（mstsc）
2. **地址**：Ubuntu IP，端口 3389
3. **登录**：`john` / `93220`

---

## 📋 快速检查清单

```bash
# 1. 检查代理
ss -tlnp | grep 1080

# 2. 检查 deb 文件
ls -lh /tmp/nomachine*.deb
file /tmp/nomachine*.deb

# 3. 检查安装
systemctl is-active nxserver
dpkg -l | grep nomachine

# 4. 检查端口
ss -tlnp | grep 4000
```
