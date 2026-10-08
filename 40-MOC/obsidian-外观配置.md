# Obsidian 外观配置指南

> 适用场景：文献管理 + 个人知识库 + 中文阅读。本文档随 `E:\note` 自动备份到 GitHub。

## 一、推荐组合

| 方案 | 包含 | 适合 |
|---|---|---|
| **主推** | Minimal 主题 + Minimal Theme Settings（调参）+ Hider（隐藏冗余） | 大量文献阅读、纯写作、清爽不抢戏 |
| **备选** | AnuPpuccin 主题 | 想要"漂亮不花哨"、彩色标签、浅/暗一键切换、中文友好 |

## 二、安装步骤

1. 打开 Obsidian → **设置 → 外观 → 管理**（社区主题市场）。
2. 搜 **Minimal**，安装并「启用」。
3. 打开 **设置 → 第三方插件 → 浏览**（社区插件市场）：
   - 搜 **Minimal Theme Settings** 安装启用（调字体/间距/配色用）。
   - 搜 **Hider** 安装启用（隐藏侧边栏边框、顶栏、标签页等）。
   - （可选）搜 **Style Settings** 安装启用（进阶微调主题细节）。

> AnuPpuccin 是主题，直接在「外观 → 浏览」里搜到安装即可，无需额外插件（但配 Style Settings 更好看）。

## 三、中文字体（最关键的一步）

主题默认英文字体对中文渲染不好看，必须换：

1. 设置 → 外观 → **格式**。
2. 修改以下字体（系统需先装好对应字体）：
   - **正文字体**：思源黑体 / Noto Sans SC / 霞鹜文楷（阅读推荐霞鹜文楷，屏幕久看不累）。
   - **标题字体**：思源黑体 或 思源宋体。
   - **等宽字体**：JetBrains Mono / Fira Code（代码块、Mermaid 图、行内代码）。

**字体下载（若系统没有）：**
- 思源黑体 / Noto Sans SC：Google Fonts 或 Adobe Fonts。
- 霞鹜文楷：`lxgw-wenkai` 项目（GitHub 搜索即可，免费开源）。

## 四、Minimal 推荐参数（在 Minimal Theme Settings 插件里调）

- **Base font size**：16–18（长时间阅读舒适）。
- **Layout**：开启 "Native colors" 或自选 accent 强调色。
- **Font**: 正文字体填上面选的中文字体名。
- 配合 **Hider** 勾选：隐藏侧边栏左右边框、隐藏顶栏、隐藏标签页、隐藏元数据（frontmatter 仍可在编辑态查看）。

## 五、暗色 / 浅色切换

- 设置 → 外观 直接切换「浅色 / 暗色」。
- Minimal 跟随切换；若想要更细腻的配色，用 Style Settings 插件选预设。
- （进阶）可让 Obsidian 跟随系统深色模式自动切换。

## 六、AnuPpuccin 备选方案

若选 AnuPpuccin：
1. 安装主题后，装 **Style Settings** 插件。
2. 在 Style Settings 里挑预设（如 Rose Pine / Tokyo Night / Catppuccin）。
3. 开启「彩色标签」「彩色侧边栏」等开关，中文渲染良好，颜值高。

## 七、小技巧

- 用 Hider 把界面收拾干净，专注内容本身。
- 想进一步定制可加 CSS snippets（设置 → 外观 → CSS 片段，进阶玩法）。
- 改完任意配置，Obsidian Git 每 10 分钟自动备份 + 推送到 GitHub，无需手动操作。
- 想换主题随时在「外观 → 管理」切换，不影响笔记内容。
