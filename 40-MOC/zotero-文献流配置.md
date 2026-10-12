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

## 三、配置 Zotero Integration（在 Obsidian 里，按截图填）

打开 Obsidian → 设置 → Zotero Integration：

1. **连接确认**：顶部应显示已连上 Zotero（显示版本号即成功）。若报错「无法连接」，回到第二节检查端口与 BBT。

2. **顶部通用设置**（你截图里这一页）：
   | 设置项 | 填什么 |
   |---|---|
   | Database | `Zotero`（默认已选） |
   | Note Import Location | `10-Literature`（这一项是给「导入 Zotero 自带笔记」用的；主流程也能复用） |
   | Open the created or updated note(s) after import | 开启（导入后自动打开笔记） |
   | PDF Utility | 如果你只想导入文献元信息 + PDF 链接，这一项**不需要下载**；只有「把 PDF 高亮转成 Obsidian 标注」时才需要 |

3. **添加导入格式（Import Formats）**：
   - 点击你截图里的 **「Add Import Format」**。
   - 在弹出的对话框里按下面填写：
   | 设置项 | 填什么 |
   |---|---|
   | Name | `Literature`（这个名字会变成命令面板里的命令） |
   | Template | `90-Templates/literature.md` |
   | Output path | `10-Literature/{{citekey}}.md`（含文件名；完整输出路径） |
   | Image output path | `assets/{{citekey}}/`（PDF 标注图片放这里；不需要也可留默认） |
   | Image base name | `image`（默认即可） |
   | CSL Style | 默认即可，除非你想要特定引文格式 |

4. **命令面板确认**：填好后，按 `Ctrl+P` 应该能搜到 **`Zotero Integration: Literature`**。

> 模板用的是 **Nunjucks** 语法（不是 Templater）。想查看某篇 Zotero 文献有哪些可用字段，用 `Ctrl+P` → `Data explorer`。

## 四、一键导入（跑通文献流）

1. 在 Obsidian 按 `Ctrl+P` → 搜 **`Zotero Integration: Literature`** → 回车。
2. 会弹出 Zotero 文献搜索框 → 输入标题/作者/引用键 → 选中一篇文献 → 回车。
3. 结果：在 `10-Literature/` 下生成 `《引用键》.md`，自动填好标题、作者、年份、DOI、Zotero 链接、PDF 嵌入、引用键（写进 `aliases`）。`40-MOC/文献管理 MOC.md` 用 Dataview 会自动把它汇总进「未读文献」表。

> 如果命令面板里只找到 `Zotero Integration: Import notes`，那是用来导入「Zotero 自带的笔记」的，不是我们要的元数据导入；确认第三节第 3 步的 Import Format 已添加。

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
- **报错 `Import failed for 90-Templates/literature.md`**：把 **Template** 和 **Output path** 填反了。Template 是模板文件 `90-Templates/literature.md`；Output path 是导入后笔记要存到哪，应填 `10-Literature/{{citekey}}.md`。

## 八、全流程：从网上找一篇论文 → 落进 Obsidian

> 核心链路：浏览器抓文献 → 存进 Zotero（BBT 给引用键）→ Obsidian 一键导入成笔记 → 读/批注/织网 → Git 备份。

### A. 收集（在浏览器里把论文存进 Zotero）
1. 装 **Zotero Connector** 浏览器扩展（Chrome/Edge/Firefox 扩展商店搜 "Zotero Connector"）。
2. 打开论文页（arXiv / Google Scholar / 出版社 / 期刊页均可）。
3. 点浏览器工具栏的 **Zotero 图标** → 选保存到哪个分类 → 回车。
   - 网站提供 PDF 时，Connector 通常会同时抓取 PDF 作为附件。
   - 没抓到 PDF：去 Zotero 里右键该条目 → Add Attachment → Add PDF；或直接把下载好的 PDF 文件拖进 Zotero 窗口（会自动生成父条目+附件）。
4. Zotero 中该条目立刻生成，**BBT 自动分配引用键**（如 `Zhang2024Attention`）。

### B. 转成笔记（在 Obsidian 里生成文献笔记）
5. Obsidian 按 `Ctrl+P` → 搜 **`Zotero Integration: Literature`** → 回车。
6. 弹出的搜索框里输入标题 / 作者 / 引用键 → 选中 → 回车。
7. 自动在 `10-Literature/引用键.md` 生成笔记，填好：标题、作者、年份、DOI、Zotero 链接、PDF 链接、引用键（写进 `aliases`，可 `[[引用键]]` 互链）。

### C. 读 & 织网
8. 点笔记里的 PDF 链接（配合 **PDF++**）打开阅读，高亮/批注可双向链接回笔记。
9. 在「我的批注」「关联文献」里写想法，用 `[[概念]]` 互联。
10. 提炼成 `20-Notes/` 永久卡片。
11. `40-MOC/文献管理 MOC.md` 用 Dataview 自动汇总未读/已读/主题。

### D. 备份
12. **Obsidian Git** 每 10 分钟自动提交 + 推送 GitHub。（PDF 不进 git，只留在 Zotero，仓库保持轻量。）

### 常见备选 / 坑
- **没网络 / 没装 Connector**：直接下载 PDF 文件 → 拖进 Zotero 窗口 → 自动建条目，再走 B 步。
- **导入报「无法连接」**：Zotero 没开 / 没勾「允许其他应用通信」/ 23119 不通。
- **`{{citekey}}` 空白**：BBT 没启用。
- **PDF 不在 vault 里**：`{{PDFAttachments}}` 默认链接到 Zotero 存储里的 PDF（不复制进 vault），这是预期行为，想本地化需另行配置附件复制。
