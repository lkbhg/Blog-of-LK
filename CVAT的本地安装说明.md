# CVAT的本地安装说明.

---

## 版权声明

© 2026 [Kan Liu]。保留所有权利。

本文为原创作品，未经允许不得转载。可以通过以下方式联系作者：[lkbhg@outlook.com]。

---

## 💡 系统配置
这里使用的系统主要为原生Linux,这是因为我发现WSL会导致Windows系统变得缓慢，且容易产生一些奇奇怪怪的Bug。而Docker系统没有原生的Windows支持。

以下是CVAT官方采用的基础环境需求：
<details>
  
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# Install the Docker packages.
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

</details>

实际上对于大多数系统来说，只需要直接安装`docker`相关组件即可，无需注册`GPG key`:
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

> [!IMPORTANT]
> 为保证docker的socket能够被用户正确访问，可以选择加入docker用户组，或者每次使用sudo来调用docker相关命令。后面不再赘述。
> ```bash
> sudo groupadd docker
> sudo usermod -aG docker $USER
> ```

## 📊 CVAT源代码获取
必须要从`Github`的`CVAT`项目中获取源代码，并谨慎选择分支。不要使用任何来自`gitee`或者其他地方的源。

Github上的默认分支是`Dev`版本，推荐切换到最新的`Release`版本。我这里使用的是`release-2.59`。

使用`git clone``拉取或者下载压缩包都可以。

## 🛠️ Docker的Compose
在旧版本中，Docker的compose依赖于python环境，这里的命令为`docker-compose up -d`。

而在新版本中，已经不再依赖`python`。命令变为了`docker compose up -d`。

而具体是在哪个版本中发生的改变，已经不好考证了。所以这里不做赘述。

### 🔍 网络问题
一般来说，直接执行`docker compose`都会遇到网络问题。这里需要配置一些加速源，请自行搜索。

这里给出一个可以使用的示例指令。
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

## 📌 注册超级用户

直接使用官方的管理脚本即可：
```bash
docker exec -it cvat_server bash -ic 'python3 ~/manage.py createsuperuser'

```
这里需要具备基础的python环境


## 🛡️ 网络访问

CVAT默认采用`localhost`访问，但提供了配置`CVAT_HOST`的选项。

```bash
export CVAT_HOST=<YOUR_DOMAIN>
```

或者使用下面的选项来实现https的功能
```bash
export CVAT_HOST=<YOUR_DOMAIN>
export ACME_EMAIL=<YOUR_EMAIL>
docker compose -f docker-compose.yml -f docker-compose.https.yml up -d
```

也可以直接把配置写入`.bashrc`。但这可能在一些版本中，出现公网访问时缺少路由的情况。

这里推荐两个额外的方式进行修改，对于大多数的CVAT的版本都能够解决。

1. 在CVAT文件夹中使用：
```bash
echo "CVAT_HOST=<domain>" > .env
```

2. 修改docker-compose.yml中的`cvat_server`里的`labels`和`cvat_ui`下的`CVAT_HOST`为目标domain

> [!WARNING]
> 修改配置后最好重新启动docker：
> ```bash
> docker compose down
> docker compose up -d
> ```
