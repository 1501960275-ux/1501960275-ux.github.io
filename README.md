# zhanglei 的个人博客

基于 Jekyll + Chirpy 主题构建的现代化个人技术博客，支持暗色/亮色模式、全文搜索、自动部署。

---

## 🌐 在线预览

**[https://1501960275-ux.github.io](https://1501960275-ux.github.io)**

---

## 🛠 技术栈

| 技术 | 用途 |
|------|------|
| [Jekyll](https://jekyllrb.com/) | 静态网站生成器 |
| [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) | Jekyll 主题（现代、响应式、暗色/亮色模式） |
| [Ruby](https://www.ruby-lang.org/) | Jekyll 运行时环境 |
| [GitHub Pages](https://pages.github.com/) | 静态网站托管 |
| [GitHub Actions](https://github.com/features/actions) | 自动构建与部署 |

---

## ✨ 功能特性

- 🌓 **暗色/亮色模式** — 自动跟随系统主题，支持手动切换
- 🔍 **全文搜索** — 实时搜索文章标题和内容
- 📑 **文章目录导航** — 右侧自动生成 TOC，快速定位章节
- 🏷️ **标签与分类** — 文章多维度归档，便于内容检索
- 🚀 **自动部署** — Push 到 `main` 分支后自动构建并部署

---

## 🚀 快速开始

### 环境要求

- Ruby >= 3.1
- Bundler

### 安装依赖

```bash
git clone https://github.com/1501960275-ux/1501960275-ux.github.io.git
cd 1501960275-ux.github.io
bundle install
```

### 本地运行

```bash
bundle exec jekyll serve --livereload
```

服务启动后访问：`http://127.0.0.1:4000`

> `--livereload` 参数会在文件修改后自动刷新浏览器。

### 构建站点

```bash
bundle exec jekyll build
```

构建产物输出到 `_site/` 目录。

---

## 📁 项目结构

```
1501960275-ux.github.io/
├── .github/
│   └── workflows/
│       └── jekyll.yml              # GitHub Actions 部署工作流
├── _config.yml                     # Jekyll 站点全局配置
├── _data/
│   └── contact.yml                 # 社交联系方式配置
├── _plugins/                       # Jekyll 插件
├── _posts/                         # 📄 博客文章存放目录
│   └── 2026-05-08-hello-world.md   # 文章示例
├── _projects/                      # 🚀 项目展示数据
│   └── example-project.md          # 项目示例
├── _tabs/
│   ├── about.md                    # 关于我页面
│   ├── archives.md                 # 归档页
│   ├── categories.md               # 分类页
│   ├── projects.md                 # 项目展示页
│   └── tags.md                     # 标签页
├── assets/                         # 静态资源（图片、字体等）
├── _site/                          # 构建输出目录（.gitignore）
├── Gemfile                         # Ruby 依赖定义
└── index.html                      # 首页
```

### 关键文件说明

| 文件/目录 | 说明 |
|-----------|------|
| `_config.yml` | 站点标题、作者、URL、语言、主题等全局配置 |
| `_posts/` | **文章存放路径**。文件命名格式：`YYYY-MM-DD-title.md` |
| `_projects/` | 项目展示数据文件，每个项目一个 Markdown 文件 |
| `_tabs/` | 顶部导航栏页面（关于、归档、分类、标签等） |
| `assets/` | 静态资源目录，图片建议放 `assets/img/` 下 |
| `.github/workflows/jekyll.yml` | GitHub Actions 工作流，Push 后自动构建部署 |

---

## 🚢 部署方式

### 方式一：GitHub Actions 自动部署（推荐）

1. Push 代码到 `main` 分支：

```bash
git add .
git commit -m "feat: add new post"
git push origin main
```

2. GitHub Actions 自动触发构建，构建成功后自动部署到 GitHub Pages。

3. 在仓库 **Actions** 标签页查看构建状态。

### 方式二：手动部署

如需手动部署到 `gh-pages` 分支：

```bash
# 安装 jekyll-deploy 工具
bundle exec jekyll build

# 将 _site/ 内容推送到 gh-pages 分支
# （需配合 git subtree 或其他部署工具）
```

> 本项目已配置 GitHub Actions，无需手动操作。

---

## ✍️ 写作指南

### 文章存放路径

所有博客文章存放在 **`_posts/`** 目录下。

**文件名规范**：
```
_posts/YYYY-MM-DD-文章标题.md
```

例如：
```
_posts/2026-05-08-hello-world.md
_posts/2026-05-10-jekyll-tutorial.md
```

### 支持的 Frontmatter 字段

每篇文章开头必须包含 YAML Frontmatter：

```yaml
---
title: "文章标题"                    # 必填。文章标题
date: YYYY-MM-DD HH:MM:SS +0800     # 必填。发布时间（含时区）
categories: [分类1, 分类2]           # 可选。文章分类，会生成分类页
tags: [标签1, 标签2, 标签3]          # 可选。文章标签，会生成标签云
description: "文章摘要"              # 可选。SEO 描述和列表页摘要
image: /assets/img/cover.jpg         # 可选。文章封面图
pin: true                            # 可选。设为 true 置顶文章
---
```

### 示例文章

```markdown
---
title: "Hello World - 博客搭建完成"
date: 2026-05-08 10:00:00 +0800
categories: [博客, 生活]
tags: [jekyll, github-pages, chirpy]
description: "我的第一篇博客文章，记录博客搭建的过程。"
---

## Hello World

这是我的第一篇博客文章！

## 博客搭建

经过一段时间的筹备，我的个人博客终于搭建完成了。

- **Jekyll** - 静态网站生成器
- **Chirpy 主题** - 现代化的 Jekyll 主题
- **GitHub Pages** - 免费的静态网站托管

## 结语

希望这个博客能成为我持续学习和输出的动力。

> 千里之行，始于足下。
```

### 图片引用方式

#### 方式一：本地图片（推荐）

将图片放入 `assets/img/` 目录，文章中用相对路径引用：

```markdown
![图片描述](/assets/img/example.png)
```

#### 方式二：图床外链

直接引用外部图床 URL：

```markdown
![图片描述](https://[图床域名]/image.png)
```

> 💡 建议优先使用本地图片，避免外链失效。本地图片需一并提交到 Git 仓库。

---

## 📄 许可证

本项目采用 [MIT License](LICENSE) 开源许可。

博客文章内容版权归作者所有。

---

> 由 [zhanglei](https://github.com/1501960275-ux) 用 ❤️ 构建
