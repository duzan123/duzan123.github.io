---
title: "如何搭建个人博客"
date: 2025-12-15
draft: false
categories: ["其他"]
tags: ["Ubuntu", "Hugo"]
---

### 1.安装和配置 git

1. 安装命令来自git官网：https://git-scm.com/install/linux

   ```bash
   sudo add-apt-repository ppa:git-core/ppa
   sudo apt update
   sudo apt install git
   ```

2. 验证git安装好了，会出现git的版本号

   ```bash
   git --version
   ```

3. 配置git，把用户名和邮箱换成你自己的（这里我和我的github账号一致）

   ```bash
   git config --global user.name "duzan123"
   git config --global user.email "1974693174@qq.com"
   ```

### 2.安装Hugo

1. 下载hugo

   进入hugo的github仓库：https://github.com/gohugoio/hugo，点击`Branches`右边的`Tags`，我这里选的是`v0.152.2`，下拉到`Assets`，我选的是`hugo_extended_0.152.2_Linux-64bit.tar.gz`，点击开始下载。

2. 解压文件，并将hugo安装到系统目录

   ```bash
   cd ~/Downloads
   tar -zxvf hugo_extended_0.152.2_Linux-64bit.tar.gz
   sudo mv hugo /usr/local/bin/
   ```

3. 验证安装

   ```bash
   hugo version
   ```

   如果出现`hugo v0.152.2-xxxxxxxx+extended linux/amd64 ...`的输出，出现**v0.152.2** 和 **extended**这两个关键词，说明安装成功！

4. 清理剩余文件

   ```bash
   rm hugo_extended_0.152.2_Linux-64bit.tar.gz README.md LICENSE
   ```

### 3.在本地搭建博客

1. 创建博客文件夹

   ```bash
   cd ~
   hugo new site myblog
   ```

2. 下载博客主题，我选的是`PaperMod`

   ```bash
   cd myblog
   git init
   git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
   ```

3. 配置`PaperMod`主题

   ```bash
   gedit hugo.toml
   ```

   在打开的文件中，**追加**（或者修改）下面这行内容：

   ```bash
   theme = 'PaperMod'
   baseURL = 'https://duzan123.github.io/'	# baseURL = "https://你的GitHub用户名.github.io/"
   
   # 添加分类和标签
   [menu]
     [[menu.main]]
       identifier = "categories"
       name = "分类"
       url = "/categories/"
       weight = 10
     [[menu.main]]
       identifier = "tags"
       name = "标签"
       url = "/tags/"
       weight = 20
   ```

4. 创建一篇文章(markdown文件)，编辑好之后把头部信息里的`draft: true`改为`draft: false`

5. 本地预览

   ```bash
   hugo server -D
   ```

   命令运行后，终端会显示一个地址，通常是 `http://localhost:1313/`。按住 `Ctrl` 键并点击这个链接，浏览器就会打开你的博客预览。

### 4.部署到github

#### 1. 创建 GitHub 仓库

1. 登录你的 [GitHub](https://github.com)。
2. 点击右上角的 `+` 号，选择 **New repository**。
3. **Repository name** (仓库名) **必须** 填写为：`你的用户名.github.io`
4. 勾选 **Public**。
5. 点击 **Create repository**。

#### 2. 配置 SSH 密钥 (解决权限问题)

因为GitHub不认识你的电脑，此时最容易遇到的报错是 "Permission denied"。我们需要配一把“钥匙”。

1. 终端输入

   ```bash
   ssh-keygen -t ed25519 -C "1974693174@qq.com"
   ```

   - 一直按回车，直到命令结束。

2. 查看生成的公钥

   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```

   复制终端里显示出来的以 `ssh-ed25519` 开头的那一长串字符。终端输出的全部内容都要复制。

3. 回到 GitHub 网页 -> 点击右上角头像 -> **Settings** -> 左侧菜单 **SSH and GPG keys** -> **New SSH key**。

4. Title随便填，Key 粘贴刚才复制的内容，点击 **Add SSH key**。

#### 3.将本地代码推送到github

回到终端（确保你在 `myblog` 目录下）：

```bash
git add .
git commit -m "我的第一次提交"
git branch -M main
git remote add origin git@github.com:你的用户名/你的用户名.github.io.git
git push -u origin main
```

#### 4.设置 GitHub Pages (最后一步！)

1. 在你的 `myblog` 文件夹下，创建目录：

   ```bash
   mkdir -p .github/workflows
   ```

2. 创建配置文件：

   ```bash
   gedit .github/workflows/hugo.yaml
   ```

3. 把下面的内容完全复制进去，保存并关闭：

   ```bash
   name: Deploy Hugo site to Pages
   
   on:
     push:
       branches: ["main"]
     workflow_dispatch:
   
   permissions:
     contents: read
     pages: write
     id-token: write
   
   concurrency:
     group: "pages"
     cancel-in-progress: false
   
   jobs:
     build:
       runs-on: ubuntu-latest
       steps:
         - name: Checkout
           uses: actions/checkout@v4
           with:
             submodules: recursive
         - name: Setup Hugo
           uses: peaceiris/actions-hugo@v2
           with:
             hugo-version: 'latest'
             extended: true
         - name: Build
           run: hugo --minify
         - name: Upload artifact
           uses: actions/upload-pages-artifact@v3
           with:
             path: ./public
   
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

4. 再次提交代码：

   ```bash
   git add .
   git commit -m "添加自动化部署脚本"
   git push
   ```

5. 打开你的 GitHub 仓库页面，点击上方的 **Settings** 选项卡，在左侧侧边栏找到 **Pages**

6. 在 **Build and deployment** 下的 **Source** 选项中，选择 **GitHub Actions** (这一步很重要，现代Hugo部署推荐用这个)。

7. 等待大约1分钟（你可以点击仓库上方的 Actions 标签查看进度），等它是绿色对号时，你的博客就上线了！

8. 访问 `https://你的用户名.github.io` 查看成果。



### 以后如何写新文章？

1. 在你想要的位置新建文章并编辑保存，把 draft: true 改为 false。

2. 提交到github

   ```bash
   git add .
   git commit -m "新增了一篇文章"
   git push
   ```

3. 等一分钟，GitHub会自动帮你更新网站。









