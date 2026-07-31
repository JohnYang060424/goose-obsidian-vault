# ️ Yang_Goose_Files 文件管理规范 (Yang_Goose_Protocol)

> **适用对象**：所有连接到本仓库的 Goose Agent (Windows / Ubuntu)  
> **核心原则**：严禁在根目录乱放文件，必须归档至对应分类。保持整洁，方便检索。

## 1. 目录结构职责 (Directory Responsibilities)

所有文件必须进入以下文件夹之一，根目录仅保留索引文件 (`MOC`, `Protocol`)。

| 文件夹 | 职责 (Duty) | 存放内容 (Content) |
| :--- | :--- | :--- |
| `00-Inbox/` | **临时中转 (Temp)** | 刚下载的文件、临时数据、未确认的草稿。**定期清理** |
| `01-Projects/` | **正式项目 (Projects)** | 独立的开发项目，包含代码、Readme、工程文件 |
| `02-Resources/` | **知识库 (Knowledge)** | Obsidian 笔记、API 文档、教程、PDF、参考资料 |
| `03-Configs/` | **系统配置 (Configs)** | API Key 记录、代理设置、环境变量、数据库连接串 |
| `04-Scripts/` | **工具脚本 (Scripts)** | `.py`, `.sh`, `.js` 等自动化脚本、工具程序 |
| `99-Archives/` | **归档区 (Archives)** | 已完成的项目、废弃文件、历史备份 |

## 2. 文件命名规范 (Naming Convention)

- **文件命名**：`YYYY-MM-DD_描述.后缀` (英文为主，中文为辅)
  - ✅ 正确：`2026-07-29_proxy-setup.md`, `image_downloader.py`
  - ❌ 错误：`新建文本文档.txt`, `123.py`
- **文件夹命名**：全小写英文，使用连字符
  - ✅ 正确：`01-Projects/my-web-app`

## 3. Agent 执行守则 (Agent Rules)

1.  **默认落脚点**：所有新生成、新下载的文件，**首先**放入 `00-Inbox/`。
2.  **脚本存放**：任何代码文件 (`.py`, `.js`, `.sh`) 必须移动至 `04-Scripts/`。
3.  **配置隔离**：
    - 严禁在代码中硬编码 API Key 或密码。
    - 所有敏感配置信息记录在 `03-Configs/` 的 Markdown 文档中。
4.  **同步要求**：每次任务结束后，必须执行 `git add . && git commit && git push` 同步到 GitHub。
5.  **清理机制**：
    - 任务完成后，将 `00-Inbox/` 中确认有效的文件移动到 `01-Projects` 或 `02-Resources`。
    - 删除 `00-Inbox` 中的垃圾文件。

## 4. 跨平台同步提示 (Cross-Platform Sync)

- **Windows 路径**: `D:\Yang_Goose_Files\test-clone-repo`
- **Ubuntu 路径**: `~/yang_goose_files` (假设路径)
- **Git**: 使用 Git 保持两端一致，**禁止**直接修改对方未推送的文件。
