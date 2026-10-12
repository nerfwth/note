---
title: "{{title}}"
authors: "{% for c in creators if c.creatorType == 'author' %}{{([c.firstName, c.lastName] | join(' ')) | trim}}{% if not loop.last %}, {% endif %}{% endfor %}"
year: "{{year}}"
rating: 
status: unread
bibkey: "{{citekey}}"
aliases: ["{{citekey}}"]
tags: [literature]
---

# {{title}}

> [!info] 文献元信息
> - **作者**：{% for c in creators if c.creatorType == 'author' %}{{([c.firstName, c.lastName] | join(' ')) | trim}}{% if not loop.last %}, {% endif %}{% endfor %}
> - **年份**：{{year}}
> - **类型**：{{itemType}}
> - **DOI**：{{DOI}}
> - **引用键**：`{{citekey}}`
> - **Zotero**：{{localLibrary}}

## 摘要
{{abstractNote}}

## 附件 / PDF
{{PDFAttachments}}

## 核心贡献
- 

## 方法 / 框架
- 

## 我的批注
{% persist "notes" %}
{% if isFirstImport %}- {% endif %}
{% endpersist %}

## 关联文献
{% persist "links" %}
{% if isFirstImport %}- [[ ]] {% endif %}
{% endpersist %}
