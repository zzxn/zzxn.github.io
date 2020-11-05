---
title: Jupyter Notebook 安装运行
tags: 杂项
key: fef1f7b91de648e79a1d5a1aba82ebe5
---

[Jupyter](https://jupyter.org/) 是一个在线IDE + 增强版的交互式shell

## 安装

### conda

```
conda install -c conda-forge notebook
```
### pip

```
pip install notebook
```

## 运行

### 本地运行（Windows/Ubuntu桌面）

```
jupyter notebook
```

访问显示的地址即可（默认会自动打开浏览器）

### 服务器运行（Ubuntu 16.04+）

生成配置文件到`~/.jupyter/jupyter_notebook_config.py`

```
jupyter notebook --generate-config
```

配置密码

```
jupyter notebook password
```

修改`jupyter_notebook_config.py`，增加下列内容：

```
c.NotebookApp.ip = '0.0.0.0'        # serve for all
c.NotebookApp.open_browser = False  # do not open browser
c.NotebookApp.port = 12105          # any port you like
c.NotebookApp.allow_root = True     # optional, may exist security issue
```
编写下列shell脚本用于快速启动Jupyter Notebook：

```bash
#!/bin/sh

NOTEBOOK_ROOT="/home/${USER}/notebookroot"
LOG_FILE="/home/${USER}/jupyter.log"

mkdir $NOTEBOOK_ROOT &&
cd $NOTEBOOK_ROOT &&
nohup jupyter notebook > $LOG_FILE 2>&1 &
```

该脚本在用户目录下创建一个叫做`notebookroot`的目录作为notebook的根目录，然后在后台运行jupyter notebook，并把标准输入输出重定向到用户目录下的`jupyter.log`文件中。
