# Zotero 文献流配置指引

> 目标：用 Zotero 管文献 + Obsidian 做笔记与知识网络。本文档随 `E:\note` 自动备份。

## 当前进度（截至 2026-10-12）

| 组件 | 状态 |
|---|---|
| Obsidian 插件 Templater | ✅ 已安装并启用 |
| Obsidian 插件 Zotero Integration | ✅ 已安装并启用 |
| Zotero 桌面端 | ✅ 已安装（D:\paperworkingflow\zotero.exe） |
| Better BibTeX (Zotero 插件) | ✅ 已安装并启用 |
| Templater 模板文件夹 | ✅ 指向 `90-Templates` |
| Zotero Integration 导入模板 | ✅ `90-Templates/literature.md`（已适配 BBT 的 `{{citekey}}`） |

## 一、BBT 装好了干嘛？（它就是为这一步服务的）

Better BibTeX 的核心作用是给每篇文献生成一个**稳定、可读的引用键**（citation key），例如 `Zhang2024Attention`。

- Zotero Integration 导入文献时，模板里的 `{{citekey}}` 就是由 BBT 提供的——所以**必须先装 BBT，引用键才有值**。
- 这个引用键同时可用于 LaTeX / Word 的正文引用、以及 Obsidian 里用 `[[Zhang2024Attention]]` 直接链接到这篇文献笔记（模板已把它写进 `aliases`）。
- BBT 还能一键导出 `.bib` 文献库，供论文写作引用。

## 二、让 Zotero 能被 Obsidian 连上（先看这一条）

Zotero Integration 靠 Zotero 本地的 **23119 端口**通信，必须打开开关：

1. 打开 Zotero → 编辑 → 首选项 → 高级（Advanced）→ 勾选 **「允许其他应用程序与 Zotero 通信」**（Allow other applications to communicate with Zotero）。
2. 确认 Zotero 正在后台运行。
3. 验证：浏览器打开 <http://127.0.0.1:23119/> 应有响应（显示 Zotero 本地服务信息）。

## 三、配置 Zotero Integration（在 Obsidian 里，3 步）

打开 Obsidian → 设置 → Zotero Integration：

1. **连接确认**：顶部应显示已连上 Zotero（显示版本号即成功）。若报错「无法连接」，回到第二节检查端口与 BBT。
2. **导入模板（Template）**：选择 `90-Templates/literature.md`。
3. 按下面逐项填写：
   | 设置项 | 填什么 |
   |---|---|
   | Import note to folder（导入到文件夹） | `10-Literature` |
   | Import note as（笔记文件名） | `{{citekey}}` |
   | Attachment folder（附件文件夹） | `{{citekey}}` |
   | BibTeX / 引用键格式 | **Better BibTeX**（这就是 BBT 的用武之地） |
   | Import annotations（导入标注） | 按需开启（配合 PDF++ 体验更好） |
   | Open note after import（导入后打开） | 开启 |

> 找不到某项英文名时，直接搜中文含义对应的英文（如 "folder"、"citekey"、"Better BibTeX"）。

## 四、一键导入（跑通文献流）

任意一种方式：

- **方式 A（推荐）**：在 Obsidian 按 `Ctrl+P` 打开命令面板 → 搜 `Zotero Integration: Import` / 「导入」→ 选 Zotero 里的一篇文献 → 回车。
- **方式 B**：Zotero 里选中条目 → 右键 → 通过 Zotero Connector /「Add note from template」推送到 Obsidian（取决于你装的连接器版本）。

结果：在 `10-Literature/` 下生成 `《引用键》.md`，自动填好标题、作者、年份、DOI、Zotero 链接、PDF 嵌入、引用键（写进 `aliases`）。`40-MOC/文献管理 MOC.md` 用 Dataview 会自动把它汇总进「未读文献」表。

## 五、Templater（手动建笔记也用得上）

1. Obsidian 设置 → Templater → **Template folder location** 填：`90-Templates`。
2. 新建笔记后 `Ctrl+P` → `Templater: Insert Template` → 选 `literature` 或 `zettel`。
3. 手动建时 `{{citekey}}` 留空正常；只有走 Zotero Integration 导入才会自动带值。

## 六、全链路

```
Zotero 收集文献（BBT 生成引用键）
   ↓ 命令面板 → Zotero Integration: Import
生成 10-Literature/《引用键》.md（自动填元信息 + 链接模板）
   ↓ 读文献写「我的批注」，用 [[概念]] 互联
提炼成 20-Notes/ 永久卡片
   ↓
40-MOC 用 Dataview 自动汇总
   ↓
Obsidian Git 每 10 分钟自动备份 + 推送 GitHub
```

## 七、常见问题

- **「无法连接 Zotero」**：Zotero 没开，或第二节的「允许通信」没勾，或 BBT 没装。先确认 Zotero 在跑、23119 端口通。
- **`{{citekey}}` 空白**：BBT 没启用，或 Zotero Integration 的引用键格式没选 Better BibTeX。
- **PDF 不进 git**：`.gitignore` 已排除 `*.pdf`，PDF 只在本地 / Zotero 里，仓库保持轻量。
- **导入后不在 MOC 出现**：确认笔记落到了 `10-Literature/`，且 frontmatter 有 `status: unread`、标签含 `literature`。
