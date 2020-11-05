---
title: 配置conda和pip镜像
tags: 杂项
key: d4118946b491478be61c5a3d80760555
---

## conda

> 注意：首先去Miniconda/Anaconda官网下载安装conda

搜索各开源镜像站的Anaconda镜像，找到相应的配置指引，本文的配置方式摘自[清华大学Anaconda镜像](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)。

创建并修改`~/.condarc`文件为下面的内容（清华源）。

> Windows需要执行`conda config --set show_channel_urls yes`，在`C:\Users\{你的用户名}`下生成.condarc

```yaml
channels:
  - defaults
show_channel_urls: true
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

## pip

搜索各开源镜像站的pypi镜像，找到相应的配置指引，本文的配置方式摘自 [清华大学pypi镜像](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/) 。

使用下面的命令进行配置：

```
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

> 执行`pip --version`查看版本，若`<10.0.0`，则执行`pip install pip -U`更新pip。
