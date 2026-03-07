 

[参考文章：Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/#prerequisites)

#### 0.1.1.1 文章目录

```
*   *   [安装步骤](about:blank#_11)
    *   *   [下载安装包](about:blank#_12)
        *   [拷贝到目标主机并执行安装命令](about:blank#_123)
    *   [验证](about:blank#_196)
    *   *   [拉取运行容器](about:blank#_198)
        *   [测试build dockerfile](about:blank#build_dockerfile_212)
        *   [测试持久运行容器](about:blank#_217)
        *   [测试主机重启后，docker各服务是否正常自启](about:blank#docker_220)
    *   [卸载方法](about:blank#_228)
    *   [附：各安装包作用说明（以及插件）](about:blank#_288)
    *   *   *   [1\. \`containerd.io\_<version>\_<arch>.deb\`](about:blank#1_containerdio_version_archdeb_291)
            *   [2\. \`docker-ce\_<version>\_<arch>.deb\`](about:blank#2_dockerce_version_archdeb_294)
            *   [3\. \`docker-ce-cli\_<version>\_<arch>.deb\`](about:blank#3_dockercecli_version_archdeb_297)
            *   [4\. \`docker-buildx-plugin\_<version>\_<arch>.deb\`](about:blank#4_dockerbuildxplugin_version_archdeb_300)
            *   [5\. \`docker-compose-plugin\_<version>\_<arch>.deb\`](about:blank#5_dockercomposeplugin_version_archdeb_303)
    *   [附：\`docker-ce-rootless-extras\_<version>\_<arch>.deb\`和\`docker-scan-plugin\_<version>\_<arch>.deb \`是什么？](about:blank#dockercerootlessextras_version_archdebdockerscanplugin_version_archdeb___306)
```

[https://docs.docker.com/desktop/install/ubuntu/](https://docs.docker.com/desktop/install/ubuntu/)

[https://docs.docker.com/engine/install/ubuntu/#install-from-a-package](https://docs.docker.com/engine/install/ubuntu/#install-from-a-package)

说明：这个安装方式是下载.deb包安装，最终效果几乎与用`apt install docker.io`完全相同。不仅安装方便，卸载起来也十分方便，不会破环系统环境。

### 0.1.2 [](https://blog.csdn.net/Dontla/article/details/131855662)安装步骤

#### 0.1.2.1 [](https://blog.csdn.net/Dontla/article/details/131855662)下载安装包

[https://download.docker.com/linux/ubuntu/dists/](https://download.docker.com/linux/ubuntu/dists/)

我是ubuntu20.04，选择focal：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/70ca936287a31abb19dfca92a430004e.png)

选择pool（官网让选这的）：  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e0c5ac376bde78686862bbd46f7bec67.png)  
解释：

目录说明：

*   `edge/`：包含一些实验性或开发中的软件包，可能不稳定或不适合生产环境使用。
*   `nightly/`：包含每日构建的软件包，用于测试和开发目的。
*   `pool/`：存放软件包的目录。
*   `stable/`：包含稳定版本的软件包，适合生产环境使用。
*   `test/`：包含一些测试相关的软件包。

文件说明：

*   `InRelease`：包含软件包的元数据和数字签名，用于验证软件包的完整性和真实性。
*   `Release`：包含软件包的元数据，如软件包列表、版本信息等。
*   `Release.gpg`：包含对`Release`文件的数字签名，用于验证`Release`文件的真实性。

选择stable：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/cf0ecbb522e130a012391a0f89ced6a3.png)

选择amd64（根据系统来，我的目前是amd64）：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/63a96750f451698ed36a2a492c5807c4.png)

将下列文件全下载下来：

```
containerd.io_<version>_<arch>.deb
docker-ce_<version>_<arch>.deb
docker-ce-cli_<version>_<arch>.deb
docker-buildx-plugin_<version>_<arch>.deb
docker-compose-plugin_<version>_<arch>.deb

```

关于下载什么版本的，我就根据我ubuntu20.04虚拟机上之前用`apt install docker.io`的来吧：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/42ea952085049205ba0c06d7a464e323.png)

这是我们ubuntu20.04arm盒子的，我也备份下：

```
root@ubuntu:~# docker version
Client:
 Version:           20.10.21
 API version:       1.41
 Go version:        go1.18.1
 Git commit:        20.10.21-0ubuntu1~20.04.2
 Built:             Thu Apr 27 05:56:44 2023
 OS/Arch:           linux/arm64
 Context:           default
 Experimental:      true

Server:
 Engine:
  Version:          20.10.21
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.18.1
  Git commit:       20.10.21-0ubuntu1~20.04.2
  Built:            Thu Apr 27 05:37:01 2023
  OS/Arch:          linux/arm64
  Experimental:     false
 containerd:
  Version:          1.6.12-0ubuntu1~20.04.1
  GitCommit:        
 runc:
  Version:          1.1.4-0ubuntu1~20.04.3
  GitCommit:        
 docker-init:
  Version:          0.19.0
  GitCommit:        
root@ubuntu:~# 
root@ubuntu:~# 


```

不过我的虚拟机貌似没装buildx插件和compose，我用命令装下：

```
apt install docker.io

docker buildx install

apt install docker-compose

```

算了，还是不装了，不装好像也没事，docker基本功能能用就行。（我后来试了，上面命令好像有问题，buildx还装不了。。。）

那我们就只装下面这几个，保证基本功能能用就行：

```
containerd.io_1.6.12-1_amd64.deb
docker-ce-cli_20.10.21~3-0~ubuntu-focal_amd64.deb
docker-ce_20.10.21~3-0~ubuntu-focal_amd64.deb

```

这也奇怪，列表中文件名有`~`符号，下下来文件又没了。。。。

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0783ed58ecd74fcd79f7166db8246edd.png)

#### 0.1.2.2 [](https://blog.csdn.net/Dontla/article/details/131855662)拷贝到目标主机并执行安装命令

把下好的文件拷贝到目标虚拟机中：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4d4d83205ea2e7038d4575f1efae9f2a.png)

然后根据官网提供的方法安装各安装包：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/e62c027630235e55afd89a65c0c8584e.png)

我这为了方便和可维护，直接做了个脚本：

```
#!/bin/bash

# 打印所有，包括注释
# set -v
# 打印执行命令
# set -x
# 命令出错退出
set -e
# 使用未初始化变量退出
set -u

USER=root

# Check user: only support root
WHO=$(whoami | grep "${USER}$")
if [ -z "${WHO}" ]; then
    echo
    echo "Please change to \"${USER}\" user mode first!"
    echo
    exit 1
fi

# 获取脚本所在目录
SCRIPT_DIR=$(
    cd "$(dirname "$0")" || {
        echo "cd Failure"
        exit 1
    }
    pwd
)

DEB_PATH_DOCKER_CONTAINERD_IO=$SCRIPT_DIR/containerd.io_1.6.12-1_amd64.deb
DEB_PATH_DOCKER_CE=$SCRIPT_DIR/docker-ce-cli_20.10.21_3-0_ubuntu-focal_amd64.deb
DEB_PATH_DOCKER_CE_CLI=$SCRIPT_DIR/docker-ce_20.10.21_3-0_ubuntu-focal_amd64.deb
# DEB_PATH_DOCKER_BUILDX=$SCRIPT_DIR/docker-buildx-plugin_0.11.1-1_ubuntu.20.04_focal_amd64.deb
# DEB_PATH_DOCKER_COMPOSE=$SCRIPT_DIR/docker-buildx-plugin_0.11.1-1_ubuntu.20.04_focal_amd64.deb

# 安装.deb包（注意是有顺序的，顺序错了安装不起来）

# （网友提醒，这个顺序不对，我未做验证）
# dpkg -i $DEB_PATH_DOCKER_CONTAINERD_IO \
#     $DEB_PATH_DOCKER_CE \
#     $DEB_PATH_DOCKER_CE_CLI

dpkg -i $DEB_PATH_DOCKER_CE_CLI \
    $DEB_PATH_DOCKER_CONTAINERD_IO \
    $DEB_PATH_DOCKER_CE 


```

把脚本搞到目录下，并且运行：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2273028486d4497ff97408765741e036.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6bf5cff8cfcbd59675c9fe09eacbf1f5.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8fdedb99f8db90674100bdc70fc031ec.png)

### 0.1.3 [](https://blog.csdn.net/Dontla/article/details/131855662)验证

#### 0.1.3.1 [](https://blog.csdn.net/Dontla/article/details/131855662)拉取运行容器

```
# 第一次安装可以不用，但是卸载后再安装，就需要执行一下，否则会报错：“Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?”
sudo service docker start

```
```
sudo docker run hello-world

```

成功了！

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/8824cad90a64f7d5033721651516ec13.png)

#### 0.1.3.2 [](https://blog.csdn.net/Dontla/article/details/131855662)测试build dockerfile

测试build dockerfile也没有问题，那那个buildx应该是build跨平台镜像用的：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/665336c9df1ec33b18e0655eb8db3190.png)

#### 0.1.3.3 [](https://blog.csdn.net/Dontla/article/details/131855662)测试持久运行容器

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4554fce5a121d98e0081eaddcebdb742.png)

#### 0.1.3.4 [](https://blog.csdn.net/Dontla/article/details/131855662)测试主机重启后，docker各服务是否正常自启

重启后：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ae1fbbbac487f3da7d993e0d86505822.png)

看起来应该是没有什么问题，跟用`apt install docker.io`效果一样

### 0.1.4 [](https://blog.csdn.net/Dontla/article/details/131855662)卸载方法

可以用`dpkg -r <package_name>`命令来卸载包，

我写了个卸载脚本，执行脚本可以一键卸载docker（卸载上面安装的那三个.deb包）：

```
#!/bin/bash

# 打印所有，包括注释
# set -v
# 打印执行命令
# set -x
# 命令出错退出
set -e
# 使用未初始化变量退出
set -u

USER=root

# Check user: only support root
WHO=$(whoami | grep "${USER}$")
if [ -z "${WHO}" ]; then
    echo
    echo "Please change to \"${USER}\" user mode first!"
    echo
    exit 1
fi

# 卸载函数
uninstall_package() {
    package_name=$1

    dpkg -r $package_name
    if [ $? -ne 0 ]; then
        echo "卸载 $package_name 失败!"
        echo
        exit 1
    fi
    echo "卸载 $package_name 成功"
    echo
}

# 注意卸载顺序：docker-ce 依赖 docker-ce-cli，不能先卸载 docker-ce-cli

# 卸载docker-ce
uninstall_package "docker-ce"

# 卸载docker-ce-cli
uninstall_package "docker-ce-cli"

# 卸载containerd.io
uninstall_package "containerd.io"


```

执行结果：

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b24eba2310b12fa14eac4b05fe9a8152.png)

### 0.1.5 [](https://blog.csdn.net/Dontla/article/details/131855662)附：各安装包作用说明（以及插件）

下面是Docker相关软件包的安装文件，各自的作用如下：

##### 0.1.5.1.1 [](https://blog.csdn.net/Dontla/article/details/131855662)1\. `containerd.io_<version>_<arch>.deb`

这是Docker容器运行时（containerd）的软件包。Containerd是一个开源的容器运行时，用于管理和运行容器。

##### 0.1.5.1.2 [](https://blog.csdn.net/Dontla/article/details/131855662)2\. `docker-ce_<version>_<arch>.deb`

这是Docker社区版（Community Edition）的软件包。Docker CE是免费的Docker版本，适用于个人和小型团队使用。

##### 0.1.5.1.3 [](https://blog.csdn.net/Dontla/article/details/131855662)3\. `docker-ce-cli_<version>_<arch>.deb`

这是Docker社区版的命令行界面（CLI）的软件包。它提供了与Docker守护进程进行交互的命令行工具。

##### 0.1.5.1.4 [](https://blog.csdn.net/Dontla/article/details/131855662)4\. `docker-buildx-plugin_<version>_<arch>.deb`

这是Docker Buildx插件的软件包。Buildx是一个用于构建多平台镜像的工具，它可以同时构建多个平台的镜像，并支持交叉编译。

##### 0.1.5.1.5 [](https://blog.csdn.net/Dontla/article/details/131855662)5\. `docker-compose-plugin_<version>_<arch>.deb`

这是Docker Compose插件的软件包。Docker Compose是一个用于定义和运行多容器应用的工具，它使用一个YAML文件来配置应用的服务、网络和卷等。

### 0.1.6 [](https://blog.csdn.net/Dontla/article/details/131855662)附：`docker-ce-rootless-extras_<version>_<arch>.deb`和`docker-scan-plugin_<version>_<arch>.deb` 是什么？

[https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/amd64/](https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/amd64/)

下载文件列表里有这两个，不知道是啥？

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/36e88a4aa34c0841a4c313a51aba2e8f.png)

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/0d7832e19ad906da0efd9e8d73424c6a.png)

`docker-ce-rootless-extras_<version>_<arch>.deb` 是Docker社区版（Community Edition）的非root用户额外组件的软件包。它包含了一些用于在非root用户下运行Docker的额外工具和插件。这些组件允许非特权用户在没有root权限的情况下使用Docker。

`docker-scan-plugin_<version>_<arch>.deb` 是Docker的扫描插件的软件包。这个插件允许用户对Docker镜像进行安全扫描，以检测其中的漏洞和安全问题。它可以帮助用户在构建和部署容器时提前发现潜在的安全风险。

这两个软件包是Docker的附加组件，可以根据需要选择安装。`docker-ce-rootless-extras`适用于非root用户使用Docker的场景，而`docker-scan-plugin`适用于进行Docker镜像的安全扫描。

 




