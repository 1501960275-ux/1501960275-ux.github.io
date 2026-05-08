# 设计文档：增加 OpenAi 分类

## 日期
2026-05-08

## 目标
在基于 Jekyll + Chirpy 主题的个人博客中增加一个名为 **OpenAi** 的分类，并通过一篇新文章让分类页面自动显示该分类。

## 项目上下文

- 框架：Jekyll + Chirpy 主题
- 语言：zh-CN
- 文章目录：`_posts/`
- 分类机制：Chirpy 主题通过 `jekyll-archives` 插件自动从文章 Frontmatter 的 `categories` 字段聚合分类列表，并在 `/categories/` 页面渲染
- 现有分类：`博客`、`生活`（来自文章 `2026-05-08-hello-world.md`）

## 需求澄清结果

1. **分类名称**：`OpenAi`
2. **文章标题**：`OpenAi学习-frontmatter`
3. **文章内容**：占位内容（后续可编辑）
4. **发布日期**：2026-05-08
5. **实现方式**：创建一篇带有 `categories: [OpenAi]` 的新文章，依赖主题自动聚合

## 方案对比

| 方案 | 描述 | 优点 | 缺点 | 推荐度 |
|------|------|------|------|--------|
| **A** | 直接创建新文章，Frontmatter 中设置 `categories: [OpenAi]` | 无需修改配置，符合 Jekyll 原生机制，后续主题升级无影响 | 无 | ⭐ 推荐 |
| B | 创建文章 + 手动增强分类页 | 可自定义分类页样式 | 破坏主题默认行为，后续升级需额外维护 | 不推荐 |

**选定方案：A**

## 设计细节

### 新建文件
- **路径**：`_posts/2026-05-08-openai-frontmatter.md`
- **Frontmatter**：
  ```yaml
  ---
  title: "OpenAi学习-frontmatter"
  date: 2026-05-08 10:00:00 +0800
  categories: [OpenAi]
  tags: [frontmatter, openai]
  ---
  ```
- **正文**：占位内容，简要介绍 Frontmatter 在 Jekyll 中的作用

### 预期效果
- Jekyll 构建后，访问 `/categories/` 页面会自动出现「OpenAi」分类
- 点击「OpenAi」分类可进入该分类的归档页 `/categories/openai/`，显示该文章

## 不变更的文件
- `_config.yml` — 无需修改，现有 `jekyll-archives` 配置已支持自动分类
- `_tabs/categories.md` — 无需修改，Chirpy 主题的分类布局会自动处理

## 后续扩展
- 用户可继续向 `_posts/` 目录添加带有 `categories: [OpenAi]` 的文章，分类页会自动聚合
- 文章正文中的占位内容可随时替换为正式内容
