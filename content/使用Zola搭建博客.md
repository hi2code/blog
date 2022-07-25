+++
title = "使用Zola搭建博客"
description = "使用 Rust 静态网站生成器 Zola 在Github Pages上搭建博客教程"
date = 2021-07-06T14:08:08+00:00
[taxonomies]
tags = ["Rust","Tool"]
categories = ["Tool"]
[extra]
author = "hi2code"

+++



# 介绍

Zola 是rust写的静态网站生成器，可以将组织好的markdown文件，生成出一个静态网页

# 安装 Zola

根据 [文档](https://www.getzola.org/documentation/getting-started/installation/) 在本地机器上安装 Zola，

- macOS

  ```bash
  $ brew install zola
  ```

- Windows平台上推荐使用

  下载[预编译版本](https://github.com/getzola/zola/releases)，解压后是一个exe文件，放到一个常用路径中，并添加环境变量

安装后，测试是否安装成功

```bash
$zola --version
zola 0.15.3
```

# 初始化项目

使用如下命令初始化项目，

```bash
$zola init blog
```

可以看到如下项目结构

```bash
blog
├── config.toml #配置
├── content #存放markdown文件
├── sass #样式
├── static #静态文件
├── templates #模板
└── themes #主题
```

# 安装主题

zola 有多款主题，在 [主题库](https://www.getzola.org/themes/) 中选择一款喜欢的主题进入它的 Homepage主页，用 git submodule 的方式添加到我们仓库中。

下面以 [even](https://github.com/getzola/even) 这款主题为例

```bash
$git init
$git submodule add https://github.com/getzola/even.git themes/even
```

通常主题都有示例文件，在 `blog\themes\tale-zola` 目录下：

- 将 `config.toml.example` 文件复制到项目根路径下，并改名为 `config.toml` ；
- 将 `content`文件夹下的所有文件及文件夹，复制到项目根路径的 `content`文件夹下。

# 本地查看

```bash
$zola serve
Building site...
Checking all internal links with anchors.
> Successfully checked 0 internal link(s) with anchors.
-> Creating 1 pages (0 orphan) and 1 sections
Done in 155ms.

Web server is available at http://127.0.0.1:1024
```
用浏览器访问链接（上例中为http://127.0.0.1:1024）可以看到最终的博客效果

# 设置远程仓库

1. 在 GitHub 上建立一个仓库，名为 blog

2. 将远程仓库关联到本地仓库，并推送


   ```bash
   git commit -m "first commit"
   git branch -M main
   git remote add origin https://github.com/your-github-name/blog.git
   git push -u origin main
   ```

# 配置 Github Page

在仓库中新建一个gh-pages分支，在setting页面pages选项，配置Source为gh-pages分支

![Github-Page](/使用Zola搭建博客.assets/Github-Page.png)

# 配置 Github Action

在项目的`.github/workflows`目录下，添加一个main.yaml文件，内容如下。可以在我们修改仓库文件后，自动编译部署

```yaml
# On every push this script is executed
on: [push]
name: Build and deploy GH Pages
jobs:
  build:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: checkout
        uses: actions/checkout@v3.0.0
      - name: build_and_deploy
        uses: shalzz/zola-deploy-action@master
        env:
          # Target branch
          PAGES_BRANCH: gh-pages
          # Provide personal access token
          TOKEN: ${{ secrets.GITHUB_TOKEN }}
          BUILD_THEMES: false
```

# 撰写博客

我们的所有markdown文件都放在项目的content路径下。

在 Zola 中有两个概念：

- [Section](https://www.getzola.org/documentation/content/section/)：在content目录下包含`_index.md`的文件夹
- [Page](https://www.getzola.org/documentation/content/page/)：在content目录下的markdown文件

# FQA

## 如何引用图片

`content`目录中的图片不会被操作，需要将图片放在`static`目录下。

例如，`static/demo/demo.png`在markdown文件中引用方式如下（注意：路径需要以`/`开头）：

```markdown
![demo](/demo/demo.png)
```

