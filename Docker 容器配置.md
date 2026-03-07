
## 0.1 Docker配置-OpenSSH配置-IP映射
### 0.1.1 分配容器并在容器内进行SSH设置

#### 0.1.1.1 分配容器

>[!example] **容器分配指令**
> - **指令一**
>```
sudo docker run -itd --name 容器名 -p 端口号:22 -v ~/docker_path/容器文件夹:/workspace --shm-size="容量" --gpus device=显卡编号 镜像路径/镜像名 /bin/bash 
>```
>
> - **指令二**
>```
sudo docker run -itd --name canq_fn -p 40233:22 -v ~/docker_path/canq_fn:/workspace --shm-size="64G" --gpus all pytorch/pytorch:2.1.0-cuda11.8-cudnn8-devel /bin/bash
>```
>
> - **指令三**
>```
sudo docker run -itd --name canq_fn -p 40233:22 -v ~/docker_path/canq_fn:/workspace --shm-size="64G" --gpus '"device=0,1"' pytorch/pytorch:2.1.0-cuda11.8-cudnn8-devel /bin/bash
>```

![](../../attach/Pasted%20image%2020231123163600.png)

> **注**：其中端口号需严格编号，做 **IP** 转发的唯一识别标志，容器名自定义设置

> [!info] **查看目前有哪些镜像或者容器**
>	使用 `sudo docker images` ，查看已存在的镜像
>	使用 `sudo docker ps -a` ，查看已存在的容器

#### 0.1.1.2 启动容器

```shell
sudo docker exec -it 容器名 /bin/bash
```

![](../../attach/Pasted%20image%2020231123151855.png)

#### 0.1.1.3 容器内更新 `apt-get` 工具

```c++
apt-get update
```

#### 0.1.1.4 安装 `vim` 编辑器

```c++
apt-get install vim
```

#### 0.1.1.5 安装 `CURL` 工具（可选）

```python
apt-get install curl
```

#### 0.1.1.6 安装 Open SSH 服务

>[!bug] **Error description**
如果出现 `Ubuntu20.04` 子系统自带的ssh服务无法连接，需卸载后重新安装。

```timeline-labeled
[line-4, body-2]（不同的列表样式，事件点样式，换数字即可）

date: 第一步
title: 开启 root 权限
content: 使用 **ROOT** 或具有 **管理员权限** 的用户登录到 **Linux** 系统


date: 第二步
title: 宿主主机检查是否已安装OpenSSH服务器
content:>**`sudo apt list --installed i grep openssh-server`**
>**`apt list --installed i grep openssh-server`**

如果输出中显示 **openssh-server**，表示已安装

date: 第三步
title: 卸载 **OpenSSH** 服务
content:>**`apt remove openssh-server`**

date: 第四步
title: 安装 **Open-SSH** 服务
content:>**`apt install openssh-server`**

下载过程出现时间地区选择，选择 **亚洲Asia** 和 **上海Shanghai**
```

### 0.1.2 修改 **Open-SSH** 配置信息

#### 0.1.2.1 编辑 `/etc/ssh/sshd_config` 文件。

```shell
vim /etc/ssh/sshd_config
```

#### 0.1.2.2 设置 `PermitRoot Login` 权限

执行 `vim /etc/ssh/sshd_config` 指令并进入编辑界面后，找到 `PermitRootLogin` 设置为 **yes**

![](../../attach/Pasted%20image%2020231123151208.png)
#### 0.1.2.3 重启 **Open-SSH** 服务。

```shell
service ssh restart
```

![](../../attach/Pasted%20image%2020231123153234.png)

**执行SSH重启后，得到的结果如上则表示已经重启SSH服务**

#### 0.1.2.4 查看 **Open-SSH** 状态

>[!example] **容器和宿主主机查看SSH状态**
>- **容器** 使用此指令
>```shell
service ssh status
>```
>- **宿主主机** 使用此指令
>```shell
systemctl status ssh # 宿主主机使用此条
>```

#### 0.1.2.5 设置 **Open-SSH** 开机自启

**自启动设置**

- 在宿主机上设置开机自启
    
```c++
$ sudo systemctl enable ssh
```
    
- 在容器中设置开机自启（**登录容器时触发**）
    
```c++
# 找到并打开文件/root/.bashrc
$ vim /root/.bashrc
# 在.bashrc末尾添加如下代码
$ service ssh start
```

**重启容器**

![](../../attach/Pasted%20image%2020231123163652.png)

**容器内查看 `Open-SSH` 状态**

![](../../attach/Pasted%20image%2020231123160641.png)

**可以看到 `sshd is running` ，成功自动运行。**
#### 0.1.2.6 修改 **ROOT** 密码

执行下方指令，设置 **ROOT** 用户密码

```c++
passwd root
```

### 0.1.3 容器内字体管理

```python
FROM python:3.11-slim

# 安装字体管理工具
RUN apt-get update && \
    apt-get install -y --no-install-recommends fontconfig && \
    rm -rf /var/lib/apt/lists/*

# 复制 SimHei 字体（确保 simhei.ttf 在构建上下文中）
COPY simhei.ttf /usr/share/fonts/truetype/

# 刷新字体缓存
RUN fc-cache -fv

# 安装 Python 依赖等...
```




