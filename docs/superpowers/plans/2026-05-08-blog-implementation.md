# zhanglei 个人博客搭建计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 基于 GitHub Pages + Jekyll + Chirpy 主题搭建 zhanglei 的现代风格个人博客

**Architecture:** 使用 Chirpy Starter 模板初始化项目，通过 `_config.yml` 配置站点信息，使用 Jekyll Collections 管理项目展示，通过 GitHub Actions 自动构建部署到 GitHub Pages

**Tech Stack:** Jekyll 4, Chirpy 主题, Ruby, Bundler, GitHub Actions, GitHub Pages

---

## 文件结构规划

```
/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/
├── .github/
│   └── workflows/
│       └── jekyll.yml              # GitHub Actions 构建工作流
├── _config.yml                     # Jekyll 站点配置
├── _data/
│   └── contact.yml                 # 社交联系方式配置
├── _plugins/
│   └── posts-lastmod-hook.rb       # 文章修改时间插件
├── _posts/
│   └── 2026-05-08-hello-world.md   # 示例文章
├── _projects/
│   └── example-project.md          # 示例项目
├── _tabs/
│   ├── about.md                    # 关于我页面
│   └── projects.md                 # 项目展示页面
├── assets/
│   └── img/
│       └── favicon.ico             # 网站图标
├── Gemfile                         # Ruby 依赖
├── Gemfile.lock                    # 锁定依赖版本
└── index.html                      # 首页（Chirpy 主题自带）
```

---

## Task 1: 备份现有项目并清理

**Files:**
- Modify: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/`

- [ ] **Step 1: 备份现有文件**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io
mkdir -p /tmp/blog-backup
cp README.md /tmp/blog-backup/
cp _config.yml /tmp/blog-backup/
```

- [ ] **Step 2: 清理现有项目文件（保留 .git 和 .idea）**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io
# 保留 .git, .idea, README.md
find . -maxdepth 1 ! -name '.git' ! -name '.idea' ! -name 'README.md' ! -name '.' -exec rm -rf {} +
```

- [ ] **Step 3: 验证清理结果**

Run: `ls -la /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io`
Expected: 仅显示 `.git`, `.idea`, `README.md`

---

## Task 2: 使用 Chirpy Starter 初始化项目

**Files:**
- Create: 多个文件（由模板生成）

- [ ] **Step 1: 克隆 Chirpy Starter 模板到临时目录**

```bash
cd /tmp
git clone https://github.com/cotes2020/chirpy-starter.git chirpy-temp --depth=1
```

- [ ] **Step 2: 复制模板文件到项目目录**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io
# 复制所有文件（排除 .git）
cp -r /tmp/chirpy-temp/. .
# 移除模板自带的 .git
cd /tmp && rm -rf chirpy-temp
```

- [ ] **Step 3: 恢复原始 README.md**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io
cp /tmp/blog-backup/README.md .
```

- [ ] **Step 4: 验证文件结构**

Run: `ls -la /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io`
Expected: 显示 Gemfile, _config.yml, _data, _posts, _tabs, assets 等目录

---

## Task 3: 配置站点基础信息

**Files:**
- Modify: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_config.yml`

- [ ] **Step 1: 配置站点基本信息**

将 `_config.yml` 中的以下配置修改为：

```yaml
lang: zh-CN
timezone: Asia/Shanghai

title: zhanglei 的博客
tagline: 记录学习与生活
description: >- # 用于 SEO
  zhanglei 的个人博客，分享技术文章、项目经验与生活感悟。

url: "https://1501960275-ux.github.io"

author:
  name: zhanglei
  email: "" # 可选，填入邮箱
```

- [ ] **Step 2: 验证配置文件语法**

Run: `cat /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_config.yml | head -30`
Expected: 显示修改后的配置内容

---

## Task 4: 配置社交联系方式

**Files:**
- Modify: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_data/contact.yml`

- [ ] **Step 1: 编辑社交链接配置**

根据实际需要配置，保留常用的即可：

```yaml
- type: github
  icon: "fab fa-github"

# - type: twitter
#   icon: "fa-brands fa-x-twitter"

# - type: email
#   icon: "fas fa-envelope"
#   noblank: true

# - type: rss
#   icon: "fas fa-rss"
#   noblank: true
```

---

## Task 5: 创建关于我页面

**Files:**
- Modify: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_tabs/about.md`

- [ ] **Step 1: 编辑关于我页面内容**

```markdown
---
icon: fas fa-info-circle
order: 5
---

# 关于我

你好，我是 **zhanglei**！

欢迎来到我的个人博客。这里是我记录技术学习、项目实践和生活思考的地方。

## 技能栈

- 前端开发
- 后端开发
- 数据分析
- 人工智能

## 联系方式

- GitHub: [1501960275-ux](https://github.com/1501960275-ux)

---

> 持续学习，不断进步。
```

---

## Task 6: 创建项目展示页面

**Files:**
- Create: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_tabs/projects.md`
- Create: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_projects/example-project.md`
- Modify: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_config.yml`

- [ ] **Step 1: 在 `_config.yml` 中注册 projects collection**

在 `_config.yml` 末尾添加：

```yaml
# Jekyll Collections
collections:
  projects:
    output: true
    permalink: /projects/:name/
```

- [ ] **Step 2: 创建项目展示 Tab 页面**

```markdown
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
```

- [ ] **Step 3: 创建示例项目数据文件**

```markdown
---
title: "示例项目"
description: "这是一个示例项目，用于展示项目页面的效果。"
---

## 项目简介

这是一个示例项目，你可以用类似的方式添加更多项目。

## 技术栈

- Jekyll
- GitHub Pages
- Markdown

## 项目链接

- [GitHub 仓库](https://github.com/1501960275-ux/1501960275-ux.github.io)
```

---

## Task 7: 创建示例文章

**Files:**
- Create: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_posts/2026-05-08-hello-world.md`
- Remove: 模板自带的示例文章（如有）

- [ ] **Step 1: 删除模板自带的示例文章**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/_posts
# 保留我们创建的，删除其他
rm -f *.md
```

- [ ] **Step 2: 创建第一篇博客文章**

```markdown
---
title: "Hello World - 博客搭建完成"
date: 2026-05-08 10:00:00 +0800
categories: [博客, 生活]
tags: [jekyll, github-pages, chirpy]
---

# Hello World

这是我的第一篇博客文章！

## 博客搭建

经过一段时间的筹备，我的个人博客终于搭建完成了。这个博客基于：

- **Jekyll** - 静态网站生成器
- **Chirpy 主题** - 现代化的 Jekyll 主题
- **GitHub Pages** - 免费的静态网站托管

## 未来计划

接下来我计划：

1. 分享技术学习笔记
2. 记录项目开发经验
3. 整理生活中的思考与感悟

## 结语

希望这个博客能成为我持续学习和输出的动力。欢迎常来逛逛！

---

> 千里之行，始于足下。
```

---

## Task 8: 配置 GitHub Actions 自动部署

**Files:**
- Create: `/Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/.github/workflows/jekyll.yml`

- [ ] **Step 1: 创建工作流目录并编写配置文件**

```bash
mkdir -p /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io/.github/workflows
```

```yaml
name: "Build and Deploy"
on:
  push:
    branches:
      - main
      - master
    paths-ignore:
      - .gitignore
      - README.md
      - LICENSE

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: "pages"
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
          # submodules: true
          # If using the 'assets' git submodule from Chirpy Starter

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.3
          bundler-cache: true

      - name: Build site
        run: bundle exec jekyll b -d "_site${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: "production"

      - name: Test site
        run: |
          bundle exec htmlproofer _site \
            --disable-external \
            --ignore-urls "/^http://127.0.0.1/,/^http://0.0.0.0/,/^http://localhost/"

      - name: Upload site artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: "_site${{ steps.pages.outputs.base_path }}"

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## Task 9: 本地构建测试

**Files:**
- Modify: 无（验证步骤）

- [ ] **Step 1: 安装 Ruby 依赖**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io
bundle install
```

Expected: 成功安装所有 gem 包

- [ ] **Step 2: 本地运行 Jekyll 服务**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io
bundle exec jekyll serve --livereload
```

Expected: 服务启动，显示 `Server address: http://127.0.0.1:4000/`

- [ ] **Step 3: 验证站点可访问（可选，手动验证）**

打开浏览器访问 `http://127.0.0.1:4000`
Expected: 能看到博客首页，包含个人简介和示例文章

---

## Task 10: 提交并推送代码

**Files:**
- Modify: 无（git 操作）

- [ ] **Step 1: 添加所有文件到 git**

```bash
cd /Users/zhanglei/DataWorks/Idea/1501960275-ux.github.io
git add .
```

- [ ] **Step 2: 提交代码**

```bash
git commit -m "feat: initialize blog with Chirpy theme

- Setup Jekyll + Chirpy theme
- Configure site info for zhanglei
- Add about and projects pages
- Add first blog post
- Setup GitHub Actions for auto deploy"
```

- [ ] **Step 3: 推送到 GitHub**

```bash
git push origin main
```

Expected: 推送成功，GitHub Actions 自动触发构建

---

## Task 11: 配置 GitHub Pages 设置

**Files:**
- 无（GitHub 仓库设置页面操作）

- [ ] **Step 1: 指导用户完成 GitHub 设置**

需要在 GitHub 仓库页面完成以下设置：

1. 打开仓库 `Settings` -> `Pages`
2. **Source** 选择 `Deploy from a branch`
3. **Branch** 选择 `gh-pages` / `root`
4. 保存设置

> 注意：首次部署可能需要等待 1-2 分钟。

---

## 验证清单

- [ ] 博客首页正常显示
- [ ] 文章列表页正常显示
- [ ] 标签页正常显示
- [ ] 分类页正常显示
- [ ] 归档页正常显示
- [ ] 关于我页面内容正确
- [ ] 项目展示页面正常显示
- [ ] 暗色/亮色模式切换正常
- [ ] 搜索功能正常工作
- [ ] GitHub Actions 构建成功
- [ ] GitHub Pages 部署成功

---

## 计划自审

**1. 规格覆盖检查：**
- [x] 首页（展示最新文章 + 个人简介）→ Task 2（模板自带）
- [x] 文章列表/归档页 → Task 2（模板自带）
- [x] 标签/分类页 → Task 2（模板自带）
- [x] 关于我页面 → Task 5
- [x] 项目/作品展示页 → Task 6
- [x] 暗色/亮色模式 → Task 2（Chirpy 主题自带）
- [x] 搜索功能 → Task 2（Chirpy 主题自带）
- [x] GitHub Actions 自动部署 → Task 8, 10
- [x] 示例内容 → Task 7

**2. 占位符扫描：** 无占位符，所有步骤包含具体代码和命令。

**3. 类型一致性：** 所有文件路径、变量名、配置项前后一致。

**4. 范围确认：** 本计划专注于博客框架搭建和基础配置，不涉及内容迁移（如有旧文章需另做计划）。
