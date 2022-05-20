---
title: v2rayN Linux 客户端配置指南
tags: 科学上网
key: 2022-05-20-v2rayN-config
---

这是在Linux上使用v2rayN客户端的配置指南。

## 安装运行

我之前买了v2ray的服务，可以在windows上愉快地在Google上查资料，但是最近GitHub的访问变得很不稳定，导致在服务器上的工作收到影响，所以我想直接在Linux服务器上配置v2ray的客户端。

步骤如下：
1. 依照[安装指南](https://guide.v2fly.org/prep/install.html)，下载安装脚本install-release.sh
2. 修改安装脚本，将其中的 `DOWNLOAD_LINK` 改成代理地址，避免直接从下载github失败
```
...
download_v2ray() {
  DOWNLOAD_LINK="https://ghproxy.com/https://github.com/v2fly/v2ray-core/releases/download/$RELEASE_VERSION/v2ray-linux-$MACHINE.zip"
  echo "Downloading V2Ray archive: $DOWNLOAD_LINK"
...
```
3. `sudo bash install-releases.sh` 安装v2ray
4. 从Windows客户端导出配置（右键服务器-导出所选服务器为客户端配置）为 v2ray.json ，上传到服务器
5. 运行 `v2ray -config path/to/v2ray.json`

## 测试

能够正常访问google就OK

```
export http_proxy=...
export https_proxy=...
curl https://www.google.com
```

