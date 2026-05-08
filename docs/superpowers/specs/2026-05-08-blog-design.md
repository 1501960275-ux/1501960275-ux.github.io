# zhanglei 个人博客设计文档

## 1. 项目概述

基于 GitHub Pages 搭建 zhanglei 的个人博客，采用 Jekyll + Chirpy 主题，呈现现代风格的个人技术博客。

## 2. 技术架构

- **平台**: GitHub Pages
- **生成器**: Jekyll
- **主题**: Chirpy (11k+ stars, 现代化设计)
- **作者**: zhanglei
- **语言**: 中文 (zh-CN)
- **部署**: GitHub Actions 自动构建部署

## 3. 页面结构

| 页面 | 路径 | 功能描述 |
|------|------|----------|
| **首页** | `/` | 顶部个人简介卡片 + 最新文章列表 |
| **文章列表** | `/posts/` | 所有文章按时间倒序排列，支持分页 |
| **标签页** | `/tags/` | 标签云 + 各标签下的文章聚合 |
| **分类页** | `/categories/` | 分类列表 + 各分类下的文章聚合 |
| **归档页** | `/archives/` | 按年月分组的文章时间线 |
| **关于我** | `/about/` | 个人介绍、技能栈、联系方式 |
| **项目展示** | `/projects/` | 作品集/项目展示页面（Jekyll Collection） |

## 4. 核心功能

- **暗色/亮色模式**: 自动检测系统主题，支持手动切换
- **全局搜索**: 实时搜索文章标题和内容
- **文章目录**: 右侧自动生成的目录导航（TOC）
- **代码高亮**: 支持多种编程语言的语法高亮
- **社交链接**: GitHub、邮箱等（可扩展）
- **SEO 优化**: 自动生成 Open Graph、Twitter Card 元数据
- **评论系统**: 集成 Giscus（基于 GitHub Discussions）

## 5. 定制计划

### 5.1 基础配置
- 站点标题、作者名（zhanglei）、描述、语言设置
- 时区、日期格式
- 头像、个人简介

### 5.2 导航与布局
- 自定义顶部导航菜单项（首页、文章、标签、分类、归档、关于、项目）
- 调整页脚信息

### 5.3 视觉定制
- 微调主色调（可选）
- 配置适合中文的字体栈
- 暗色/亮色模式下的配色适配

### 5.4 内容组织
- 文章放在 `_posts/` 目录，使用 `YYYY-MM-DD-title.md` 命名
- 项目数据通过 `_projects/` collection 管理
- 关于页面使用 `_tabs/about.md`
- 项目展示页使用 `_tabs/projects.md` + `_projects/` 数据文件

## 6. 部署流程

1. 本地开发调试（`bundle exec jekyll serve`）
2. 推送到 GitHub `main` 分支
3. GitHub Actions 自动构建并部署到 `gh-pages` 分支
4. GitHub Pages 从 `gh-pages` 分支发布站点
