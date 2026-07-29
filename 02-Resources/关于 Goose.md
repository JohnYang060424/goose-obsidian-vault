# 🦢 Goose Agent 自我介绍

> **首次创建**: 2026-07-29  
> **运行环境**: Windows (D:\Yang_Goose_Files)  
> **身份**: AAIF 开源 Goose AI Agent

---

## 📌 基本信息

| 项目 | 值 |
|------|------|
| **名称** | Goose |
| **开发者** | AAIF (Agentic AI Foundation) |
| **类型** | 开源 AI Agent |
| **运行平台** | Windows (本实例) / Ubuntu (Hermes 实例) |
| **工作目录** | `D:\Yang_Goose_Files` |
| **关联 Vault** | `goose-obsidian-vault` |

---

## 🛠️ 核心能力

### 1. 工具集
- **文件操作**: 读取、写入、编辑文件
- **Shell 命令**: 执行终端命令、脚本
- **代码分析**: 通过 tree-sitter 分析代码结构
- **任务管理**: TODO 追踪、子代理委派
- **应用创建**: 生成 HTML/CSS/JS 应用

### 2. 扩展支持
- **analyze**: 代码结构分析
- **apps**: 应用管理
- **developer**: 终端与构建工具
- **extensionmanager**: 扩展管理
- **skills**: 技能加载
- **todo**: 任务管理

---

## 🌐 网络与代理

| 配置项 | 值 |
|--------|------|
| **代理协议** | SOCKS5 |
| **代理地址** | `127.0.0.1:10808` |
| **Git HTTP 代理** | `socks5://127.0.0.1:10808` |
| **GitHub 认证** | Personal Access Token (ghp_***) |
| **GitHub 用户** | JohnYang060424 |

---

## 📁 工作原则

1. **第一原则**: 所有文件仅存于 `D:\Yang_Goose_Files`
2. **笔记规范**: 遵循 Obsidian Markdown 格式，使用 `[[双向链接]]`
3. **同步策略**: 本地 Git Commit + Push 到 GitHub
4. **任务闭环**: 完成后总结为笔记入库

---

## 🔗 关联笔记

- [[00-Goose-MOC]] - 主索引
- [[配置/API配置/Email Sender]] - 邮件接口
- [[配置/API配置/External Storage (OSS)]] - 对象存储
- [[配置/API配置/Private Clipboard]] - 剪贴板同步
- [[docs/代理快速配置指南]] - 代理设置

---

## 📝 与 Hermes 的区别

| 维度 | Hermes (Ubuntu) | Goose (Windows) |
|------|-----------------|-----------------|
| **操作系统** | Ubuntu 20.04+ | Windows |
| **Shell** | bash | cmd / powershell |
| **Goose 目录** | `/home/john/yang_goose_files` | `D:\Yang_Goose_Files` |
| **代理端口** | 1080 (sing-box) | 10808 (外部提供) |
| **Vault 位置** | `/home/john/obsidian-vault-us-ecs` | `D:\Yang_Goose_Files\test-clone-repo` |

> 💡 **注意**: 两台机器共享同一个 GitHub Vault，通过 Git 实现双向同步。
