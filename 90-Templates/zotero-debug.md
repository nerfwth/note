---
title: "DEBUG: {{title}}"
citekey: "{{citekey}}"
---

# DEBUG: {{title}}

## 顶层字段

- **title**: {{title}}
- **date**: `{{date}}`
- **date (raw string)**: `{{date | string}}`
- **itemType**: `{{itemType}}`
- **DOI**: `{{DOI}}`
- **citekey**: `{{citekey}}`
- **desktopURI**: `{{desktopURI}}`
- **bibliography**: `{{bibliography}}`

## creators 原始数据

{% if creators %}
 creators.length = {{creators.length}}
{% for c in creators %}
- index: {{loop.index0}}
  - firstName: `{{c.firstName}}`
  - lastName: `{{c.lastName}}`
  - name: `{{c.name}}`
  - creatorType: `{{c.creatorType}}`
{% endfor %}
{% else %}
(no creators / undefined)
{% endif %}

## attachments 原始数据

{% if attachments %}
attachments.length = {{attachments.length}}
{% for a in attachments %}
- index: {{loop.index0}}
  - title: `{{a.title}}`
  - path: `{{a.path}}`
  - contentType: `{{a.contentType}}`
  - pdfURI: `{{a.pdfURI}}`
  - desktopURI: `{{a.desktopURI}}`
{% endfor %}
{% else %}
(no attachments / undefined)
{% endif %}
