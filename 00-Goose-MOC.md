#  Goose Agent MOC (Map of Content)

> **第一原则**：所有文件严格限于 `Yang_Goose_Files` 工作区。

## 📜 核心规范 (Protocol)
- [[00-Yang_Goose_Protocol]]：⚠️ **必读** - 文件夹规划与文件存放守则 (跨端同步指南)

##  关于 Goose
- [[02-Resources/关于 Goose]]：Goose Agent 自我介绍与配置概览

## 🔑 核心 API 配置 (Configs)

> 集中管理所有 API 密钥与设置

- [[03-Configs/Email Sender]]：邮件发送与日志查询接口
- [[03-Configs/External Storage (OSS)]]：阿里云 OSS 对象存储接口
- [[03-Configs/Private Clipboard]]：跨设备剪贴板同步接口

## 📚 知识库与文档 (Resources)

> 沉淀的经验、教程与测试报告

- [[02-Resources/Hermes-笔记管理最佳实践]]：笔记管理策略参考
- [[02-Resources/proxy_quick_setup]]：Sing-box 代理快速部署
- [[02-Resources/nomachine_proxy_guide]]：NoMachine 远程桌面与代理
- [[02-Resources/API测试报告]]：接口连通性测试结果
- [[docs/无头主机显示修复]]：Dummy Driver 配置
- [[docs/Hermes-笔记管理最佳实践]]：Hermes 笔记管理方法论
- [[docs/API测试报告]]：三大 API 首次全面测试记录
- [[docs/2026欧美流行金曲Top50任务]]：音乐下载与 OSS 上传任务

## 📝 工作流

- 笔记同步：本地 Git 自动 Push
- 远程访问：NoMachine / XRDP
- 代理访问：Sing-box VLESS 127.0.0.1:1080
