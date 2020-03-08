---
title: Travis CI 自动部署 Hexo 博客到 Github Pages
date: 2020-03-08 10:16:51
tags:
- Github
- Markdown
categories:
- [Github]
- [Markdown]
---

在[如何用Github搭建博客](https://mp.weixin.qq.com/s?__biz=MjM5NzUyNjc2MQ==&mid=2247483886&idx=1&sn=0dcf5d9e511cee5aa499ecdce877cfae&chksm=a6d9ec6891ae657e879da3f07fa7dfc297e6ec885a3fef1b603e32bcec479ee0f6c057e7fcbd&token=1068564806&lang=zh_CN#rd)中，我们基于 Git 仓库建立了一个分支 blog-source 来管理博客的源码，每次在 source/_post 下创建新文章，利用 GitHub 图床，在 source/images 文件夹中存放文章中需要用到的图片，关于 Github 图床的搭建及使用在以后的文章中讲解。写好博客文章后，只需要`git push`把博客源码推送到 Github 的远程仓库，Travis CI 便完成自动部署。

```bash
# 创建一篇新文章或新的页面
hexo new [layout] <title>
```

## 手动部署

使用 Hexo 生成静态文件，自动部署网站，可以执行下列的命令：

```bash
hexo generate --deploy
# 命令简写
hexo g -d
```

这样就可以完成博客的更新了。

## Travis CI 部署 GitHub Pages

### 创建一个 Github repo 并且同步代码到 GitHub 上

- 新建一个名称为 `<username>.github.io` 的 Github repo，这里的`<username>`是你的用户名称，例如我的 GitHub 用户名是 padluo，我建立的 repo 是`padluo.github.io`。
- 建立一个分支来管理博客的配置和 Markdown 文件，例如，padluo 建立了一个名称为 blog-source 的分支，管理博客的源代码。
- 将你的 Hexo 站点文件夹推送到 GitHub 远程仓库的 blog-source 分支中。默认情况下 public 目录将不会被推送到 repository 中，你应该检查 .gitignore 文件中是否包含 public 一行，如果没有请加上。

### 设置 Travis CI 和 GitHub

- 将 Travis CI 添加到你的 GitHub 账户中，用 GitHub 账户登录[Travis CI](https://travis-ci.org/)即可。
- 前往 GitHub 的 Applications settings，配置 Travis CI 权限，使其能够访问你的 repository。
- 你应该会被重定向到 Travis CI 的页面。如果没有，请手动前往[Travis CI](https://travis-ci.org/)。
- 在浏览器新建一个标签页，前往 GitHub-Settings-Developer settings 新建 Personal Access Token，只勾选 repo 的权限并生成一个新的 Token。Token 生成后请复制并保存好。
- 回到 Travis CI，前往你的 Settings-repository 的设置页面，在 padluo.github.io 仓库的 Settings-Environment Variables 下新建一个环境变量，Name 为 GH_TOKEN，Value 为刚才你在 GitHub 生成的 Token。确保 DISPLAY VALUE IN BUILD LOG 保持 不被勾选，避免你的 Token 泄漏。点击 Add 保存。

### 编辑 Travis CI 部署配置文件

在你的本地 Hexo 站点项目根目录中新建一个 .travis.yml文件，内容如下：

```yml
sudo: false
language: node_js
node_js:
  - 12 # use nodejs v12 LTS
cache: npm
branches:
  only:
    - blog-source # 仅从 blog-source 分支构建
script:
  - hexo generate # generate static files
deploy:
  provider: pages
  skip-cleanup: true
  github-token: $GH_TOKEN
  keep-history: true
  committer_from_gh: true
  target_branch: master # 部署到 master 分支
  on:
    branch: blog-source
  local-dir: public
```

将 .travis.yml 推送到 GitHub 远程仓库中，Travis CI 应该会自动开始运行，并将生成的文件推送到同一 repository 下的 master 分支下。

前往 `https://<username>.github.io` 查看你的站点是否可以访问。这可能需要一些时间。

> 参考资料
>
> 1. <https://hexo.io/zh-cn/docs/github-pages>

---
微信公众号「padluo」，分享数据科学家的自我修养，既然遇见，不如一起成长。

![数据分析二维码.gif](https://raw.githubusercontent.com/padluo/padluo.github.io/blog-source/source/images/%E6%95%B0%E6%8D%AE%E5%88%86%E6%9E%90%E4%BA%8C%E7%BB%B4%E7%A0%81.gif)

---
读者交流电报群

[https://t.me/sspadluo](https://t.me/sspadluo)

---
知识星球交流群

![知识星球读者交流群.jpeg](https://raw.githubusercontent.com/padluo/padluo.github.io/blog-source/source/images/%E7%9F%A5%E8%AF%86%E6%98%9F%E7%90%83%E8%AF%BB%E8%80%85%E4%BA%A4%E6%B5%81%E7%BE%A4.jpeg)

