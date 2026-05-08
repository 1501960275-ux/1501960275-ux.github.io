---
title: 项目展示
icon: fas fa-project-diagram
order: 4
---

# 我的项目

这里展示了我的一些项目作品。

{% for project in site.projects %}
## {{ project.title }}

{{ project.description }}

[查看详情]({{ project.url | relative_url }})

---
{% endfor %}
