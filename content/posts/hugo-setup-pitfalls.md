---
title: "Hugo 搭建踩坑记录（2026-09-06）"
date: 2026-09-06T08:30:00+08:00
tags: ["hugo", "踩坑", "PaperMod"]
draft: false
---

今天第一次搭 Hugo + PaperMod + Cloudflare Pages，照着网上笔记抄，结果踩了 4 个坑。记录下来防自己再踩。

## 坑 1：`hugo.toml` 里的连字符不是普通 `-`

我抄的笔记里写的是 `languageCode = "zh‑CN"`，看着像普通连字符，实际上是 **U+2011 non-breaking hyphen**。TOML 解析没问题，但语言码识别不对。

**解决**：全部用 ASCII 普通连字符 `"zh-CN"`。

类似的，`baseURL = "https://xxx.pages.dev"` 里的 `xxx` 占位符没换成本地预览也能跑，但部署后所有相对路径都会指向 `xxx.pages.dev`，必须换成 Cloudflare 分配的真实地址。

## 坑 2：Hugo v0.158+ 弃用了 `languageCode`

我的版本是 0.165，弃用警告：

```
WARN  deprecated: project config key languageCode was deprecated in Hugo v0.158.0
```

**解决**：用新的 `[languages.zh]` 配置块：

```toml
defaultContentLanguage = "zh"

[languages.zh]
  languageName = "中文"
  weight = 1
```

⚠️ 但**单语言博客继续用 `languageCode` 也行**（只是有警告），不必为了消除警告折腾。PaperMod 主题本身也还在用旧的 `.Language.LanguageDirection` 等，新警告都是 cosmetic 的。

## 坑 3：`hugo new site` 不会自动 `git init`

网上很多老教程说"现在初始化 git 仓库"，但 v0.165 不会自动做了。

**解决**：自己手动 `git init`。

## 坑 4：文章日期设成"今天 10 点"可能变成未来文章 ← 最隐蔽

我第一篇笔记写 `date: 2026-09-06T10:00:00+08:00`，但当时服务器 UTC 时间还在前一天。Hugo 把这篇文章当作 `future post` 过滤掉了，访问 `/posts/first-note/` 直接 404。

**症状**：`hugo list all` 能看到文章，`hugo list published` 返回空。

**解决**：把日期设成过去时间（早于当前服务器 UTC 时间）。或者部署时加 `--buildFuture`，但生产环境不应该用。

> 这个坑不查 git 仓库构建日志根本发现不了，搜索"hugo 文章 404"也很难直接定位到日期问题。

## 部署相关

- **Cloudflare Pages 部署 Hugo**，环境变量要设 `HUGO_VERSION=0.165.0`（和本地一致）+ `TZ=Asia/Shanghai`
- **`HUGO_VERSION` 漏设**的话 Cloudflare 默认用最新 Hugo Extended，但和本地不一致可能编译失败
- **第一次部署成功后**把分配的 `xxx.pages.dev` 填回 `baseURL`，再 push 一次
