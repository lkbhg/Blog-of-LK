# CVAT的本地安装说明.

---

## 版权声明

© 2026 [Kan Liu]。保留所有权利。

本文为原创作品，未经允许不得转载。可以通过以下方式联系作者：[lkbhg@outlook.com]。

## 系统配置
这里使用的系统主要为原生Linux,这是因为我发现WSL会导致Windows系统变得缓慢，且容易产生一些奇奇怪怪的Bug。而Docker系统没有原生的Windows支持。

## CVAT源代码获取
必须要从`Github`的`CVAT`项目中获取源代码，并谨慎选择分支。不要使用任何来自`gitee`或者其他地方的源。

Github上的默认分支是`Dev`版本，推荐切换到最新的`Release`版本。我这里使用的是`release-2.59`。

使用`git clone``拉取或者下载压缩包都可以。

## Docker的Compose
在旧版本中，Docker的compose依赖于python环境，这里的命令为`docker-compose up -d`。

而在新版本中，命令变为了`docker compose up -d`。

而具体是在哪个版本中发生的改变，已经不好考证了。所以这里不做赘述。

### 网络问题
一般来说，直接执行`docker compose`都会遇到网络问题。这里需要配置一些加速源，请自行搜索。
```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<EOF
{
 "registry-mirrors": [
   "...",
 ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```


