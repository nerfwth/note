# 文献管理 MOC

> 知识库入口。文献笔记放 `10-Literature/`，永久卡片放 `20-Notes/`，本页用 Dataview 自动汇总。
> 依赖：**Dataview**（必需）；**Templater** + **Zotero Integration**（自动填充模板变量，可选）。

## 📚 未读文献
```dataview
TABLE year, authors, rating FROM "10-Literature" WHERE status = "unread" SORT year DESC
```

## ✅ 已读文献
```dataview
TABLE year, authors, rating FROM "10-Literature" WHERE status = "done" SORT year DESC
```

## 🌱 最新卡片（seed）
```dataview
LIST FROM #seed SORT file.mtime DESC LIMIT 15
```

## 🗂️ 按主题
```dataview
TABLE topic FROM #seed SORT topic
```

## 使用流程
1. Zotero 收文献 → Zotero Integration 导入生成文献笔记到 `10-Literature/`
2. 读时填「我的批注」并用 `[[ ]]` 链接概念
3. 提炼成永久卡片到 `20-Notes/`，链回文献
4. 本页自动汇总，知识库就长出来了

## 提示
- 模板在 `90-Templates/`：用 Templater 时把「模板文件夹」指向它
- 想读 PDF 并让标注双向链接笔记，装 **PDF++**
- PDF 文献已加入 `.gitignore`，不会进 git 仓库（仓库保持轻量）
- 临时想法先丢 `00-Inbox/`，定期整理
