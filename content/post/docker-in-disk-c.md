---
title: "Docker 使用 WSL2 吃满 C 盘空间"
date: 2023-07-04T15:54:11+08:00
tags: ["docker"]
draft: false
---

docker 现在默认使用 wsl2，但是这里的 wsl2 并不是用户自己安装的，而是 docker 自己安装的

而 docker 自己安装的 wsl2 会默认放在 C 盘，所以我们需要手动做一下迁移防止 C 盘爆炸

<!--more-->

有些辅助迁移的软件如: `LxRunOffline.exe` 是不能够迁移 docker 安装的 wsl2 的，只能作用于
用户自己安装的 wsl2

所以我们需要以下的命令[^1](PowerShell 或者 CMD)[^2]:

```shell
# 关闭所有发行版：
wsl --shutdown

# 将docker-desktop-data导出到D:\SoftwareData\wsl\docker-desktop-data\docker-desktop-data.tar（注意，原有的docker images不会一起导出）
wsl --export docker-desktop-data D:\SoftwareData\wsl\docker-desktop-data\docker-desktop-data.tar

# 注销 docker-desktop-data
wsl --unregister docker-desktop-data

# 重新导入 docker-desktop-data 到要存放的文件夹：D:\SoftwareData\wsl\docker-desktop-data\
wsl --import docker-desktop-data D:\SoftwareData\wsl\docker-desktop-data\ D:\SoftwareData\wsl\docker-desktop-data\docker-desktop-data.tar --version 2
```

[^1]: https://www.cnblogs.com/xhznl/p/13184398.html
[^2]: https://github.com/docker/for-win/issues/7348
