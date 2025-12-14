---
title: "Ubuntu下使用Hugo+github搭建个人博客"
date: 2025-12-14T1:55:00+03:00
draft: false
categories: ["其他"]
tags: ["Ubuntu", "Hugo"]
---

## 1.安装和配置 git

- 安装命令来自git官网：https://git-scm.com/install/linux

```bash
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install git
```

- 验证git安装好了，会出现git的版本号

```bash
git --version
```

- 配置git，把用户名和邮箱换成你自己的（这里我和我的github账号一致）：

```bash
git config --global user.name "duzan123"
git config --global user.email "1974693174@qq.com"
```

### 2.安装Hugo

- 进入hugo的github仓库：https://github.com/gohugoio/hugo，点击`Branches`右边的`Tags`，我这里选的是`v0.152.2`，下拉到`Assets`，我这里选的是`hugo_extended_0.152.2_Linux-64bit.tar.gz`，点击开始下载。

- 解压文件，并将hugo安装到系统目录

```bash
cd ~/Downloads
tar -zxvf hugo_extended_0.152.2_Linux-64bit.tar.gz
sudo mv hugo /usr/local/bin/
```

- 验证安装

```bash
hugo version
```

如果出现`hugo v0.152.2-xxxxxxxx+extended linux/amd64 ...`的输出，出现**v0.152.2** 和 **extended**这两个关键词，说明安装成功！

- 清理剩余文件

```bash
rm hugo_extended_0.152.2_Linux-64bit.tar.gz README.md LICENSE
```

