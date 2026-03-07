
### 0.1.1 升级 APT 工具

```bash
sudo apt-get update
```

### 0.1.2 安装 Nvidia 显卡驱动（可选）

```bash
sudo apt-get install nvidia-driver-535   # 安装 nvidia 12.1 版本的显卡驱动
```

### 0.1.3 添加 GPGKEY（可选）

#### 0.1.3.1 GPGKEY 地址

>**GPGKEY：** https://nvidia.github.io/libnvidia-container/gpgkey
#### 0.1.3.2 对应压缩包内的 **GPGKEY** 文件夹

```bash
sudo apt-key add gpgkey   # 添加密钥
```

### 0.1.4 软件包地址

**所有的软件包均已打包完整，若需安装其他版本软件包，可直接跳转下列地址\~~\~**
#### 0.1.4.1 Docker 软件包地址

>**Docker：** [https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/amd64/](https://download.docker.com/linux/ubuntu/dists/focal/pool/stable/amd64/)
#### 0.1.4.2 Nvidia-Docker2 软件包地址

>**Nvidia-Docker2：** [Index of /nvidia-docker/libnvidia-container/stable/ubuntu20.04/amd64/ (uchicago.edu)](https://mirror.cs.uchicago.edu/nvidia-docker/libnvidia-container/stable/ubuntu20.04/amd64/)

### 0.1.5 卸载原有的软件包（可选）

```bash
sudo dpkg -l   # 查看所有安装的软件包
```

```bash
sudo dpkg remove <package_name>   # 卸载软件包
```

### 0.1.6 安装 Docker-27.1.0

#### 0.1.6.1 对应压缩包内的 **Docker27.1.0** 文件夹

```bash
# 以下指令依次执行
sudo dpkg -i containerd.io_1.7.19-1_amd64.deb 
sudo dpkg -i docker-ce-cli_27.1.0-1~ubuntu.20.04~focal_amd64.deb 
sudo dpkg -i docker-ce_27.1.0-1~ubuntu.20.04~focal_amd64.deb
```

> **注意：如果出现依赖包不匹配导致的安装不成功，可选择执行下面指令\~\~\~**

> [!abstract] **借助安装工具升级依赖包**
>```bash
sudo apt-get update
sudo apt-get -y install 
sudo apt --fix-broken install
>```

### 0.1.7 检查 Docker  是否安装完整

```bash
docker -v
sudo docker images
sudo docker ps -a
```

### 0.1.8 设置 Docker 自启

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

### 0.1.9 关闭防火墙（可选）

```bash
systemctl stop firewalld
systemctl disable firewalld
```

### 0.1.10 安装 Nvidia-Docker2

#### 0.1.10.1 对应压缩包内的 **Nvidia-Docker2-2.13.0** 文件夹

```bash
# 以下指令依次执行
sudo dpkg -i libnvidia-container1_1.13.5-1_amd64.deb 
sudo dpkg -i libnvidia-container1-dbg_1.13.5-1_amd64.deb 
sudo dpkg -i libnvidia-container-tools_1.13.5-1_amd64.deb 
sudo dpkg -i nvidia-container-toolkit-base_1.13.5-1_amd64.deb 
sudo dpkg -i nvidia-container-toolkit_1.13.5-1_amd64.deb 
sudo dpkg -i nvidia-container-runtime_3.13.0-1_all.deb 
sudo dpkg -i nvidia-docker2_2.13.0-1_all.deb 
sudo systemctl restart docker
```

##### 0.1.10.1.1 调用 GPU 启动容器 

```bash
sudo docker run --rm --gpus all nvidia/cuda:11.0.3-base-ubuntu20.04 nvidia-smi
```

>[!abstract] **检测 GPU**
>**注意事项**：`Docker` 镜像中并没有 `nvidia/cuda:11.0.3-base-ubuntu20.04` ，所以容器启动不会成功，但可通过此条看出容器启动是否正常调用 GPU，即上述指令用于验证是否有检测不到 GPU 的报错，如果在输出中无 `[[GPU]]` 这类字眼的报错，则 `nvidia-docker2` 安装成功！！

##### 0.1.10.1.2 监控 Nvidia 显卡

```bash
pip install --upgrade nvitop
```

执行监控指令 `nvitop`

##### 0.1.10.1.3 系统 Docker Nvidia-Docker2

系统版本为 **Ubuntu 20.04**，安装的 Docker 版本为 **27.1.0**，发行日期为 2024年7月23日，Nvidia-Docker2 版本为 **2.13.0**，发行日期为 2023年3月31日；均为最新版本，可持续沿用....

##### 0.1.10.1.4 监控并杀掉进程

```bash
top
```

```bash
kill -9 PID
```

> [!success] **~ SUCCESS ~**
> **Docker、Nvidia-Docker2安装完成.....**










