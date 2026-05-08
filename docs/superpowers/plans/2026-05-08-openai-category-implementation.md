# 增加 OpenAi 分类 实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 通过创建一篇带有 `categories: [OpenAi]` 的新文章，让博客分类页面自动显示「OpenAi」分类。

**Architecture:** 利用 Jekyll + Chirpy 主题的原生分类机制，`jekyll-archives` 插件会自动从文章 Frontmatter 的 `categories` 字段聚合分类列表，无需修改任何配置。

**Tech Stack:** Jekyll, Chirpy 主题, Markdown

---

## 文件结构规划

```
/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/
├── _posts/
│   ├── 2026-05-08-hello-world.md        # 现有文章
│   └── 2026-05-08-openai-frontmatter.md # 新建文章（本计划唯一创建的文件）
```

---

## Task 1: 创建 OpenAi 分类文章

**Files:**
- Create: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_posts/2026-05-08-openai-frontmatter.md`

- [ ] **Step 1: 编写新文章**

```markdown
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
```

- [ ] **Step 2: 验证文件已创建**

Run: `cat /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_posts/2026-05-08-openai-frontmatter.md | head -10`
Expected: 显示文章 Frontmatter，包含 `categories: [OpenAi]`

- [ ] **Step 3: 本地构建验证（可选，如有 Jekyll 环境）**

Run: `cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io && bundle exec jekyll build`
Expected: 构建成功，无错误

- [ ] **Step 4: 提交到 Git**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io
git add _posts/2026-05-08-openai-frontmatter.md
git commit -m "feat: add OpenAi category with placeholder post"
```

---

## 验证清单

- [ ] `_posts/2026-05-08-openai-frontmatter.md` 文件已创建
- [ ] 文件 Frontmatter 中包含 `categories: [OpenAi]`
- [ ] Jekyll 构建成功（如环境可用）
- [ ] 代码已提交到 Git

**部署后验证（Push 到 GitHub 后）：**
- [ ] 访问 `/categories/` 页面能看到「OpenAi」分类
- [ ] 点击「OpenAi」分类进入 `/categories/openai/` 能看到该文章

---

## 计划自审

**1. 规格覆盖检查：**
- [x] 分类名称：`OpenAi` → Step 1
- [x] 文章标题：`OpenAi学习-frontmatter` → Step 1
- [x] 占位内容 → Step 1
- [x] 日期：2026-05-08 → Step 1
- [x] 无需修改 `_config.yml` 或 `_tabs/categories.md` → 无相关 Task（符合方案 A）

**2. 占位符扫描：** 无占位符。所有步骤包含具体代码和命令。

**3. 类型一致性：** 文件路径、分类名称、日期格式前后一致。

**4. 范围确认：** 本计划仅创建一篇文章，不修改任何现有配置，最小侵入式实现。
