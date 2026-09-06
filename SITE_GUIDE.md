# 个人网站结构与操作指南（SITE_GUIDE）

> 本文档梳理本地 git 仓库的结构与技术栈，供维护网站时快速查阅。
> 仓库路径：`C:\Users\55871\天堂洗衣机的目录`
> 远程仓库：`git@github.com:dsjmg1226/heaven_wash1.git`（GitHub）

---

## 1. 网站概览

| 项目 | 说明 |
| ---- | ---- |
| 框架 | **Hexo 7.3.0**（静态博客生成器） |
| 主题 | **Butterfly 5.4.2**（npm 安装） |
| 部署方式 | 尚未配置正式部署（`deploy.type` 为空；package.json 中有 netlify 构建脚本，但无 netlify.toml / GitHub Actions） |
| 文章源 | `source/_posts/`（Markdown） |
| 构建产物 | `public/`（静态 HTML） |
| 语言 | 站点配置 `language: en`，默认内容为英文模板 |

## 2. 目录结构总览

```
天堂洗衣机的目录/
├── _config.yml                # Hexo 站点主配置（站点信息、URL、目录、部署等）
├── _config.butterfly.yml      # ⚠️ 主题配置覆盖文件（当前内容有误，见 §4.1）
├── _config.landscape.yml      # 旧主题 landscape 的残留配置（当前主题是 butterfly，已不使用）
├── package.json               # npm 依赖与脚本（build/clean/deploy/server/netlify）
├── package-lock.json          # 依赖锁文件
├── db.json                    # Hexo 缓存数据库（自动生成，不应入库）
├── README.md                  # 仓库简介
├── .github/dependabot.yml     # 依赖自动更新机器人配置
├── node_modules/              # npm 依赖（含 hexo-theme-butterfly 主题本体）
├── scaffolds/                 # 新建文件模板
│   ├── post.md                #   hexo new 新建文章的模板
│   ├── page.md                #   hexo new page 新建页面的模板
│   └── draft.md               #   草稿模板
├── source/                    # 内容源目录（Markdown）
│   ├── _posts/                #   所有文章（当前：hello-world.md、test.md）
│   └── （可新建其他目录如 about/、categories/ 等）
├── themes/                    # 主题目录（当前为空；主题实际在 node_modules）
└── public/                    # hexo generate 的构建产物（⚠️ 当前被误提交进 git，见 §4.2）
```

## 3. 关键文件详解

### 3.1 `_config.yml`（站点主配置）

当前有效配置要点：

| 配置项 | 当前值 | 说明 |
| ---- | ---- | ---- |
| `title` | Heaven Washing Machine's BLOG | 站点标题 |
| `author` | John Doe | ⚠️ 还是默认值，建议改为自己的名字 |
| `language` | en | 站点语言，可改为 `zh-CN` |
| `url` | http://example.com | ⚠️ 默认占位值，正式部署后需改为真实域名 |
| `permalink` | :year/:month/:day/:title/ | 文章 URL 格式（如 /2025/07/15/test/） |
| `theme` | butterfly | 当前主题 |
| `deploy` | type: '' | ⚠️ 未配置部署方式 |

### 3.2 主题配置文件（Butterfly）

Hexo 的主题配置查找顺序：
1. 根目录 `_config.butterfly.yml`（存在则**覆盖**主题默认配置）
2. 主题目录 `node_modules/hexo-theme-butterfly/_config.yml`（主题默认配置，27KB，包含 nav/menu/code_blocks/avatar 等全部选项）

⚠️ **当前问题**：根目录的 `_config.butterfly.yml` 内容是 `_config.yml` 的整份复制（站点配置），并非 butterfly 主题配置。想改主题样式/导航等，应编辑 `node_modules/hexo-theme-butterfly/_config.yml`（或用根目录 `_config.butterfly.yml` 只写需要覆盖的键）。

### 3.3 `source/_posts/`（文章）

每篇文章 = 一个 Markdown 文件，顶部是 **Front-matter**（YAML 格式元信息）：

```yaml
---
title: 文章标题
date: 2025-07-15 11:50:44
tags: [标签1, 标签2]      # 可选
categories: [分类]         # 可选
---
正文内容（Markdown）
```

### 3.4 `scaffolds/`（模板）

`hexo new "标题"` 会用 `scaffolds/post.md` 生成新文章。可自定义模板内容（如预置 front-matter 字段）。

## 4. 当前已知问题（坑）

### 4.1 `_config.butterfly.yml` 内容错误
根目录主题配置文件是站点配置的复制品。修改主题配置的正确方式是编辑主题默认配置（`node_modules/hexo-theme-butterfly/_config.yml`）或在 `_config.butterfly.yml` 中**只写要覆盖的键**（Hexo 会合并两者，根目录文件优先）。

### 4.2 `public/` 和 `db.json` 被 git 跟踪
仓库**没有 .gitignore**，导致构建产物和缓存入库。建议：
1. 新建 `.gitignore`，加入 `public/`、`db.json`、`node_modules/`（node_modules 当前未被跟踪则无需处理）
2. 执行 `git rm -r --cached public db.json` 从版本控制中移除（本地文件保留）
3. 提交一次"清理仓库"的 commit

### 4.3 站点配置仍是默认值
`author: John Doe`、`url: http://example.com`、`language: en` 未修改，发布前需更新。

## 5. 常用命令速查

| 操作 | 命令 | 说明 |
| ---- | ---- | ---- |
| 新建文章 | `hexo new "文章标题"` | 在 source/_posts/ 生成 md 文件 |
| 新建草稿 | `hexo new draft "标题"` | 生成草稿（默认不渲染） |
| 本地预览 | `hexo server` | 默认 http://localhost:4000 |
| 构建静态文件 | `hexo generate`（或 `hexo g`） | 输出到 public/ |
| 清理缓存 | `hexo clean` | 删除 db.json 和 public/ 后重新生成 |
| 完整构建（Netlify 用） | `npm run netlify` | = clean + generate |
| 提交推送 | `git add . && git commit -m "msg" && git push` | 推送到 GitHub |

> Windows 注意：以上命令需在仓库目录（或该目录的终端）执行。若提示 `hexo` 不是命令，改用 `npx hexo ...`。

## 6. 部署方案（待选择）

当前仓库未配置部署，可选三种方式（需自行选择一个并补充配置）：

1. **Netlify**（最省事）：GitHub 仓库 → Netlify 导入 → Build command 填 `npm run netlify` → Publish directory 填 `public`
2. **GitHub Pages**：需在 `_config.yml` 配置 `deploy.type: git`，或添加 GitHub Actions workflow 自动构建
3. **Vercel**：同 Netlify，导入仓库后配置构建命令

> ⚠️ 无论哪种方式，部署后需把 `_config.yml` 的 `url` 改为真实站点地址。
