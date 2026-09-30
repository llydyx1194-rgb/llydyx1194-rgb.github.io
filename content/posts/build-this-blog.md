---
title: "从零搭一个 Hugo 博客：PaperMod + GitHub Pages"
date: 2026-09-30T20:00:00+08:00
draft: false
tags: ["Hugo", "GitHub Pages", "折腾"]
summary: "记录这个博客的搭建过程：为什么选 Hugo，怎么装在非系统盘，怎么做到 push 即发布，以及路上踩到的一个小坑。"
---

一直没有自己的博客，笔记散在各处的 Markdown 文件里。今天索性搭了一个，这篇就当作第一篇正式文章，顺便把过程记下来。

## 为什么选 Hugo

静态博客的选择很多，我最后选了 Hugo，理由很实际：

- **单个可执行文件**，不依赖 Node。我的机器上 Node 环境本来就有些历史包袱，能绕开就绕开。
- **构建极快**，十几个页面不到一秒。
- **主题成熟**。PaperMod 简洁、支持深色模式、自带搜索和归档，对中文也友好。

## 安装：别占系统盘

我习惯把工具都装在非系统盘，所以用 Scoop 安装（Scoop 本身就在 D 盘）：

```powershell
scoop install hugo-extended
```

一定要装 `extended` 版本，很多主题需要它来处理 SCSS。

## 建站

项目放在 E 盘的仓库目录里。仓库名必须是 `<用户名>.github.io`，这样 GitHub 才会把它当作个人主页站点：

```bash
hugo new site llydyx1194-rgb.github.io --format toml
cd llydyx1194-rgb.github.io
git init -b main
git clone --depth 1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
rm -rf themes/PaperMod/.git
```

主题我是直接拷进 `themes/` 目录，并删掉了它自己的 `.git`，没有用 submodule。好处是仓库自包含，CI 里不用额外拉子模块；代价是升级主题要手动更新。对个人博客来说，这个取舍我能接受。

`hugo.toml` 里最关键的是这几项：

```toml
baseURL = "https://llydyx1194-rgb.github.io/"
languageCode = "zh-CN"
defaultContentLanguage = "zh-cn"
theme = "PaperMod"
hasCJKLanguage = true
```

`hasCJKLanguage` 不能漏，否则字数统计和阅读时间对中文是错的。

## push 即发布

用 GitHub Actions 自动构建并部署到 Pages，工作流大致是：检出代码、装 Hugo、`hugo --minify`、上传产物、部署。在仓库的 Pages 设置里把来源选为 **GitHub Actions**，之后每次推送到 `main`，大约 30 秒站点就更新了。

以后写文章的流程只剩三步：

1. 在 `content/posts/` 下新建一个 Markdown 文件；
2. 本地用 `hugo server -D` 预览；
3. `git push`。

## 踩到的一个小坑

克隆主题时，`git clone` 报了 `Connection was reset`。在国内直连 GitHub 有时不稳定，如果你有可用的代理，可以只给这一次命令指定：

```bash
git -c http.proxy=http://<代理地址>:<端口> clone --depth 1 <主题仓库地址> themes/PaperMod
```

用 `-c` 只对单条命令生效，不需要改全局配置。

## 接下来

博客有了，接下来的计划很朴素：把平时折腾时的记录整理成文章，比如环境配置、踩坑笔记和一些小工具。写得慢没关系，先让它有内容。
