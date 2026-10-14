---
title: "{{title}}"
authors: "{% for c in creators %}{% if c.creatorType == 'author' %}{{ c.firstName }} {{ c.lastName | trim }}{% if not loop.last %}, {% endif %}{% endif %}{% endfor %}"
year: "{{ date | format('YYYY') if date else '' }}"
rating: 
status: unread
bibkey: "{{citekey}}"
aliases: ["{{citekey}}"]
tags: [literature]
---

# {{title}}

> [!info] 文献元信息
> - **作者**：{% for c in creators %}{% if c.creatorType == 'author' %}{{ c.firstName }} {{ c.lastName | trim }}{% if not loop.last %}, {% endif %}{% endif %}{% endfor %}
> - **年份**：{{ date | format('YYYY') if date else '' }}
> - **类型**：{{itemType}}
> - **DOI**：{{DOI}}
> - **引用键**：`{{citekey}}`
> - **Zotero**：{% if desktopURI %}[在 Zotero 中打开]({{desktopURI}}){% endif %}

## 摘要
{{abstractNote}}

## 附件 / PDF
{% for a in attachments %}{% if a.pdfURI %}- [PDF]({{a.pdfURI}})
{% endif %}{% endfor %}

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
