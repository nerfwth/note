# Zotero 文献流配置指引

> 目标：用 Zotero 管文献 + Obsidian 做笔记与知识网络。本文档随 `E:\note` 自动备份。

## 当前进度（截至配置时）

| 组件 | 状态 |
|---|---|
| Obsidian 插件 Templater | ✅ 已安装并启用 |
| Obsidian 插件 Zotero Integration | ✅ 已安装并启用（待 Zotero 端就绪） |
| Zotero 桌面端 | ❌ 未安装（需先装） |
| Better BibTeX (Zotero 插件) | ❌ 未安装（需先装） |
| Templater 模板文件夹 | ⚠️ 待设置（见第四节） |
| Zotero Integration 导入模板 | ⚠️ 待设置（见第三节，需 Zotero 装好后） |

## 一、安装 Zotero（本机）

1. 官网 <https://www.zotero.org/download/> 下载 **Zotero 7**（Windows 版）。
2. 安装并启动一次，注册/登录 Zotero 账号（同步文献元数据用，可选）。
3. 确认 Zotero 在后台运行——Zotero Integration 靠它本地的 23119 端口通信。

## 二、安装 Better BibTeX

1. Zotero 里：工具 → Add-ons（或 设置 → 插件）→ 齿轮 → Install Add-on From File。
2. 下载 Better BibTeX 最新 `.xpi`：<https://retorque.re/zotero-better-bibtex/installation/>（或 GitHub releases）。
3. 装好后重启 Zotero。Better BibTeX 负责生成**稳定引用键**（如 `Zhang2024Attention`），Zotero Integration 导入时会填入 `{{citekey}}`。
4. 推荐设置：Better BibTeX 首选项 → 引用键格式设为你喜欢的模板（默认即可）。

## 三、配置 Zotero Integration（Zotero 装好后）

1. Obsidian 设置 → Zotero Integration：
   - 确认「Zotero 笔记导入」已连接（显示 Zotero 版本即成功）。
2. 设置**导入模板**指向本库已有的文献笔记模板：
   - 模板文件：`90-Templates/literature.md`
   - 在 Zotero Integration 的模板设置里，把「Note Import Template」设为该文件（或把 `literature.md` 复制进插件要求的模板目录）。
3. 可选：开启「导入 PDF 附件的标注」→ 配合 PDF++ 把高亮转成笔记。

## 四、配置 Templater（现在就能做）

1. Obsidian 设置 → Templater → **Template folder location** 填：`90-Templates`
2. （可选）勾选「Trigger Templater on new file creation」方便新建即套模板。
3. 用法：
   - 新建笔记后，命令面板（`Ctrl+P`）→ `Templater: Insert Template` → 选 `literature` 或 `zettel`。
   - 模板里的 `{{title}}`、`{{date}}`、`{{citekey}}` 等变量会自动填入（citekey 需 Zotero Integration 导入时才会有值，手动用时先留空）。

## 五、一键文献流（全链路跑通后）

```
Zotero 收集文献
   ↓ 选中文献 → 右键 → 「Import to Obsidian」（Zotero Integration）
生成 10-Literature/《标题》.md（自动填标题/作者/引用键 + 链接模板）
   ↓ 读文献时写批注、用 [[概念]] 互联
提炼成 20-Notes/ 永久卡片（Templater 生成）
   ↓
40-MOC 用 Dataview 自动汇总未读/已读/主题
   ↓
Obsidian Git 每 10 分钟自动备份 + 推送 GitHub
```

## 六、常见问题

- **导入时报错「无法连接 Zotero」**：Zotero 没开，或 Better BibTeX 没装。先确认 Zotero 在运行且 23119 端口通（浏览器开 `http://127.0.0.1:23119/` 应有响应）。
- **模板变量不填**：手动新建时 `{{citekey}}` 留空正常；走 Zotero Integration 导入才会自动带值。
- **PDF 不进 git**：`.gitignore` 已排除 `*.pdf`，PDF 只在本地/Zotero 里，仓库保持轻量。

## 七、插件清单（已装）

`obsidian-git` · `dataview` · `pdf-plus` · `templater-obsidian` · `obsidian-zotero-integration`
