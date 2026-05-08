---
title: "OpenAi学习-frontmatter"
date: 2026-05-08 10:00:00 +0800
categories: [OpenAi]
tags: [frontmatter, openai]
---

## 前言

本文作为 OpenAi 分类下的占位文章，后续将补充更多关于 OpenAI 学习的内容。

## 什么是 Frontmatter

在 Jekyll 中，Frontmatter 是每篇 Markdown 文章顶部的 YAML 配置块，位于两条 `---` 之间。它用于定义文章的元数据，例如标题、日期、分类、标签等。

## 常用的 Frontmatter 字段

| 字段 | 说明 |
|------|------|
| `title` | 文章标题 |
| `date` | 发布日期（含时区） |
| `categories` | 文章分类，会生成分类页面 |
| `tags` | 文章标签，会生成标签云 |
| `description` | SEO 描述和列表页摘要 |
| `image` | 文章封面图 |
| `pin` | 设为 `true` 可置顶文章 |

## 示例

```yaml
---
title: "文章标题"
date: 2026-05-08 10:00:00 +0800
categories: [OpenAi]
tags: [frontmatter, openai]
---
```

---

> 占位内容，后续编辑。
