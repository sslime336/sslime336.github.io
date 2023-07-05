---
title: "搭建过程"
date: 2023-05-30T15:11:14+08:00
tags: ["intro"]
draft: false
---

> 简单写写0 v0

<!--more-->

## 需求

1. 一个简单方便操作的个人博客，不需要太多特性，力求简洁
2. 平时记录点学习过的知识点
3. 发牢骚

## 选型

- GitHub Page

    免费，好用，自动部署也方便

- [Hugo](https://gohugo.io/)

    操作简单，生成文章速度快，主题模板较为丰富

- [fuji](https://github.com/dsrkafuu/hugo-theme-fuji/blob/master/README_CN.md)
    
    当前使用的主题

## 详细流程(基于 fuji)

1. 建仓库 \<username\>.github.io.git
2. 拉取到本地，hugo 建站，添加主题

```bash
$ git clone <git> && \
    cd <git_dir> && \
    git checkout -b hugo && \
    hugo new site . && \
    git submodule add https://github.com/dsrkafuu/hugo-theme-fuji.git themes/fuji
```
3. 根据 [fuji 文档](https://github.com/dsrkafuu/hugo-theme-fuji/blob/master/README_CN.md#-%E5%BC%80%E5%A7%8B%E4%BD%BF%E7%94%A8)简单操作

> 然后把 `exampleSite` 复制出来，并修改 `config.toml`

复用了一下 `exampleSite` 里面的东西

4. 配置自动部署

当前的分支是 `hugo`，我希望在我往这个分支 push 的时候，能自动构建并将 hugo 生成的静态页部署到主分支(master)

所以使用了下 `GitHub Action`(参考了[王路飞的知乎](https://zhuanlan.zhihu.com/p/403221054)):

```yaml
# .github/workflows/deploy.yml
name: Update sslime336.github.io
on:
  push:
    branches:
      - hugo

jobs:
  deploy2master:
    name: Build & Deploy
    runs-on: ubuntu-latest
    steps:
      - name: Check out source code on branch hugo
        uses: actions/checkout@v3
        with:
          ref: hugo
          submodules: recursive # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0 # Fetch all history for .GitInfo and .Lastmod
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: latest
      - name: Build
        run: hugo
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.ACTION_DEPLOY_KEY }}
          external_repository: sslime336/sslime336.github.io # Page 仓库
          publish_branch: master # Page 仓库的分支
          publish_dir: ./public # 静态网页路径
          commit_message: ${{ github.event.head_commit.message }}
```

这里唯一需要注意一下的是 `deploy_key: ${{ secrets.ACTION_DEPLOY_KEY }}`

这里使用 external_repository 并不合适，以后再改 :P

准备自动部署:

- 在项目根目录新建 `.github/workflows/deploy.yml`
- cp 上面的内容并修改
- 处理 `deploy_key`, [点我查看](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-create-ssh-deploy-key)

5. `git add . && git commit -m "xxx" && git push -set-upstream origin hugo`

## *当然还是建议主分支写东西然后部署到 `gh-pages` 分支*
