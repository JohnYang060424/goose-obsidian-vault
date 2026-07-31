# 📁 Yang_Goose_Files 工作目录规范 (Goose Protocol v2)

> **适用对象**：所有连接到本仓库的 Goose Agent (Windows / Ubuntu)  
> **核心原则**：严禁在根目录乱放文件，必须归档至对应分类。保持整洁，方便检索。

## 1. 目录结构职责 (Directory Responsibilities)

所有文件必须进入以下文件夹之一，**根目录仅保留索引文件** (`MOC`, `Protocol`)。

| 文件夹 | 职责 (Duty) | 存放内容 (Content) | 示例 |
| :--- | :--- | :--- | :--- |
| `00-Inbox/` | **临时中转 (Temp)** | 刚下载的文件、临时数据、未确认的草稿。定期清理 | `temp_api_test.json`, `draft.md` |
| `01-Projects/` | **正式项目 (Projects)** | 独立的开发项目，包含代码、Readme、工程文件 | `01-Projects/weather-scraper/` |
| `02-Resources/` | **知识库 (Knowledge)** | Obsidian 笔记、API 文档、教程、PDF、参考资料 | `02-Resources/API-Docs/` |
| `03-Configs/` | **系统配置 (Configs)** | API Key 记录、代理设置、环境变量、数据库连接串 | `03-Configs/proxy-setup.md` |
| `04-Scripts/` | **工具脚本 (Scripts)** | `.py`, `.sh`, `.js` 等自动化脚本、工具程序 | `04-Scripts/clean_logs.py` |
| `99-Archives/` | **归档区 (Archives)** | 已完成的项目、废弃文件、历史备份 | `99-Archives/2026-Q1-report/` |

## 2. 文件命名规范 (Naming Convention)

- **文件命名**：`YYYY-MM-DD_描述.后缀` (英文为主，中文为辅)
  - ✅ 正确：`2026-07-29_proxy-setup.md`, `image_downloader.py`
  - ❌ 错误：`新建文本文档.txt`, `123.py`, `test.py`
- **文件夹命名**：全小写英文，使用连字符，禁止空格和特殊字符
  - ✅ 正确：`01-Projects/my-web-app/`, `02-Resources/api-docs/`
  - ❌ 错误：`01-Projects/我的项目/`, `temp files/`

## 3. Agent 执行流程 (Agent Workflow)

### 🟢 任务开始前
1. **检查规范**：确认存放位置（本项目 vs 临时文件）
2. **检查代理**：如需访问海外资源，先检查 `03-Configs/proxy-setup.md`，确认代理状态
3. **清理旧文件**：检查 `00-Inbox/` 是否有残留垃圾文件

### 🟡 任务进行中
1. **默认落脚点**：所有新生成/下载的文件，**首先**放入 `00-Inbox/`
2. **脚本隔离**：任何代码文件 (`.py`, `.js`, `.sh`) 直接放入 `04-Scripts/`
3. **配置隔离**：严禁在代码中硬编码 API Key/密码，敏感信息统一存入 `03-Configs/` 的 Markdown 文档

### 🔴 任务结束后
1. **归档**：将 `00-Inbox/` 中确认有效的文件移至 `01-Projects/` 或 `02-Resources/`
2. **清理**：删除 `00-Inbox/` 中的临时/垃圾文件
3. **同步**：执行 `git add . && git commit -m "描述变更" && git push`

## 4. 配置与安全管理 (Security & Configs)

- **严禁硬编码**：代码中不得出现明文 API Key、Token、密码。
- **统一记录**：所有配置信息以 Markdown 格式记录在 `03-Configs/` 目录下，格式示例：
  ```markdown
  # API 配置：XXX
  - **Endpoint**: `https://api.example.com/v1`
  - **API Key**: `sk-xxx...`
  - **备注**: 每日限额 1000 次请求
  ```
- **Git 忽略**：敏感文件应加入 `.gitignore`（如 `.env` 文件），仅通过文档记录。

## 5. 跨平台同步约定 (Cross-Platform Sync)

| 平台 | 路径 |
|------|------|
| **Windows (mini-win-pc)** | `C:\Yang_Goose_Files\` 或 `D:\Yang_Goose_Files\` |
| **Ubuntu (服务器)** | `~/yang_goose_files/` |

- **Git 同步**：使用 Git 保持两端一致，禁止直接修改对方未推送的文件。
- **冲突处理**：优先 `git pull` 再修改，遇到冲突手动解决 `<<<<<<<` 标记后提交。

## 6. 与 Hermes/Obsidian 体系的联动

- **Obsidian Vault**：固定路径 `obsidian-vault/`（通过 GitHub `obsidian-vault-us-ecs` 同步），不存放在本工作目录。
- **Hermes 临时文件**：Hermes 产生的临时文件统一放入 `C:\Users\John\yang_file\temp\`，本 Goose 目录与之隔离。
- **共享经验**：本规范与服务器端 Hermes Agent 共享，双方严格遵守。
