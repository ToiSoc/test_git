
---

# 1 部署 Dify 和 Ollama 运行本地模型（Ubuntu 22.04）

本文介绍在 Ubuntu 22.04 上安装 Docker、Docker Compose、Dify 和 Ollama，并通过 Dify 配置 Ollama 运行本地模型（如 Qwen2.5-7B-Instruct），支持智能体应用和知识库功能。以下步骤经过测试，确保简洁高效。

---

## 1.1 安装 Docker（已安装可跳过）

### 1.1.1 准备工作

#### 1.1.1.1 卸载旧版本

确保系统无旧版 Docker，以避免冲突：

```bash
sudo apt-get remove -y docker docker-engine docker.io containerd runc
```

#### 1.1.1.2 安装依赖

安装支持 HTTPS 和密钥管理的工具：

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common gnupg lsb-release
```

### 1.1.2 配置 Docker 源

#### 1.1.2.1 添加 GPG 密钥

使用阿里云镜像源（国内访问更稳定）：

```bash
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

#### 1.1.2.2 添加 APT 源

配置阿里云 Docker 仓库：

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### 1.1.2.3 更新源

```bash
sudo apt-get update
```

### 1.1.3 安装 Docker

安装最新版 Docker：

```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

验证安装：

```bash
docker --version
sudo systemctl status docker
```

- 确保 Docker 服务运行（`active (running)`）。

### 1.1.4 配置非 root 用户（可选，需谨慎）

当我们安装好了Docker之后，有两种方式来执行docker 命令

*   在 Docker 命令前加上 `sudo` , 比如：`sudo docker ps`
*   `sudo -i `切换至root用户，再执行 **Docker** 命令

是不是可以让当前用户在不切root，或者不加 `sudo` 的情况下正常使用 docker 命令呢？答案是有的。

#### 1.1.4.1 创建 Docker 用户组

```bash
sudo groupadd -f docker
```

#### 1.1.4.2 添加用户到组

```bash
sudo usermod -aG docker $USER
```

#### 1.1.4.3 生效权限

```bash
newgrp docker
```

#### 1.1.4.4 配置自动生效

编辑 `~/.bashrc`，添加：

```bash
echo "newgrp docker" >> ~/.bashrc
```

```python
source ~/.bashrc
```
#### 1.1.4.5 测试

```bash
docker ps -a
```

- 若无错误，配置成功。
#### 1.1.4.6 最后一步 更新.bashrc文件

我们需要编辑 `~/.bashrc` 文件，并在文件末尾增加如下一行,如果不在.bashrc文件中增加下面这一行命令

```shell
# 如果没有此行命令，你会发现，当你每次打开新的终端
# 你都必须先执行一次 “newgrp docker” 命令
# 否则当前用户还是不可以执行docker命令
groupadd -f docker
```

### 1.1.5 安装命令补全（可选）

增强 Docker 命令行体验：

```bash
sudo apt-get install -y bash-completion
curl -L https://raw.githubusercontent.com/docker/docker-ce/master/components/cli/contrib/completion/bash/docker -o /etc/bash_completion.d/docker.sh
source /etc/bash_completion.d/docker.sh
```

---

## 1.2 安装 Docker Compose

如果 **Docker** 已安装但缺少 **Docker Compose** 工具，如下所示。

```python
$ docker compose up
docker: 'compose' is not a docker command. see 'docker --help'
```

### 1.2.1 安装 Docker Compose

`Docker Compose` 工具的 **Github** 仓库

[Releases · docker/compose](https://github.com/docker/compose/releases/)

![](attach/Pasted%20image%2020250411203240.png)

#### 1.2.1.1 下载二进制文件

使用最新稳定版（此处以 v2.35.0 为例）：

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.35.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

> **离线安装**：下载 `docker-compose-linux-x86_64` 文件，上传至服务器，放置于 `/usr/local/bin/docker-compose`。

#### 1.2.1.2 添加执行权限

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

#### 1.2.1.3 验证

```bash
docker-compose --version
```

![Docker Compose 版本](attach/Pasted%20image%2020250411204304.png)

> **注意**：Docker Compose 命令为 `docker-compose`（带横杠），区别于 Docker 的 `docker compose` 子命令。

> 对于离线安装的 docker-compose， 工具调用指令为 `docker-compose` ，需要加上横杠 **`-`**

在使用 docker-compose 工具查看容器状态时，确保所在路径是包含 `docker-compose.yaml` 配置文件的目录

```python
sudo docker-compose -f /path/to/your/docker-compose.yaml ps -a
```

1. **检查目录**：确认你在执行命令时位于包含 `docker-compose.yaml` 文件的正确目录下。

2. **权限问题**：虽然你使用了 `sudo` 来以超级用户权限运行命令，但还是确保你的用户对 `docker-compose.yaml` 文件及其所在目录具有适当的读取权限。

### 1.2.2 配置 Docker 镜像源

最近国内无法访问到 **Docker**，因此在安装的时候，我们需要更换源为国内镜像源。

另外，我们需要在 Docker 的 daemon 配置文件中增加国内的可用的 docker hub mirror，

#### 1.2.2.1 编辑 daemon.json

创建或编辑 `/etc/docker/daemon.json`：

目前测试后可用的源：

```
https://docker.ketches.cn
https://docker.1panel.live
https://docker.m.daocloud.io
```

```json
{
  "registry-mirrors": [
    "https://docker.m.daocloud.io",
    "https://docker.ketches.cn",
    "https://docker.1panel.live"
  ]
}
```

#### 1.2.2.2 重启 Docker

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

> **验证镜像源**：访问 `https://docker.m.daocloud.io`（或其他源），确保页面加载正常。

---

## 1.3 部署 Dify

Dify 是一个开源 LLM 应用平台，支持本地模型集成和知识库管理。

**仓库地址**：[langgenius/dify](https://github.com/langgenius/dify)

### 1.3.1 部署步骤

```timeline-labeled
[line-4, body-2]（不同的列表样式，事件点样式，换数字即可）

date: 第一步
title: 克隆仓库
content: >**`git clone https://github.com/langgenius/dify.git`**
>**`cd dify/docker`**

将 **DIFY** 项目仓库克隆指定位置，并进入项目中的 **docker** 目录


date: 第二步
title: 克隆环境变量
content:>**`cp .env.example .env`**

date: 第三步
title: 启动服务
content:>**`docker compose up -d`**

执行此条指令后，docker 会自动读取 **docker-compose.yaml** 的配置文件拉取镜像（包括 Nginx、PostgreSQL、Redis、Weaviate 等）启动容器。

date: 第四步
title: 检查启动后的容器状态
content:>**`docker compose ps`**
```

#### 1.3.1.1 检查容器状态

**查看启动后的容器**

```python
sudo docker-compose ps -a
```

![](attach/Pasted%20image%2020250411211641.png)

```python
sudo docker ps -a
```

![](attach/Pasted%20image%2020250411212134.png)

通过以上两个指令的效果，可以看出 `docker-compose` 仅仅显示了该工具启动的容器，可通过 `docker-compose ps` 来查看 `docker-compose` 工具启动的容器，与 `docker` 工具启动的容器分离显示。

>[!error] **注意事项**
> 注：`docker-compose ps -a` 仅在包含 `docker-compose.yaml` 的目录下使用才能生效。

![](attach/Pasted%20image%2020250412145429.png)


### 1.3.2 修改 Nginx 端口（可选）

**Nginx** 代理对外暴露的默认端口号为 **80**，可根据需要将其修改为其他端口号。

1. 编辑  `docker/.env.example` ：

   - 找到 `EXPOSE_NGINX_PORT`，改为自定义端口（如 `1210`）：

 ```bash
 EXPOSE_NGINX_PORT=1210
 ```

![Nginx 端口配置](attach/Pasted%20image%2020250411195539.png)

2. 更新环境变量并重启：

![](attach/Pasted%20image%2020250411202355.png)

```python
cd dify/docker
cp .env.example .env
docker compose up -d
```

**拉取镜像部署完成后效果如下：**

![](attach/Pasted%20image%2020250411211512.png)

### 1.3.3 访问 Dify

- **无端口转发**：访问 `http://<服务器IP>:<Nginx端口>`（如 `192.168.x.x:1210`）。
- **端口转发**：在交换机后台配置端口映射（例如 1210 映射到服务器 IP）。

![500](attach/Pasted%20image%2020250411213015.png)

然后，本地电脑接入校园网，访问对应的 **服务器IP** 加 **转发端口** 访问

```python
192.168.xx.xx:1210
```

![Dify 登录页面](attach/Pasted%20image%2020250411213049.png)

#### 1.3.3.1 初始化管理员账号

![初始化账号](attach/Pasted%20image%2020250411214011.png)

#### 1.3.3.2 登录

![登录成功](attach/Pasted%20image%2020250411214136.png)


> [!success] **~ SUCCESS ~**
>**Dify 配置完整完成！！！！！！**

---

## 1.4 安装 Ollama

Ollama 是一个轻量级本地模型运行框架，支持 GGUF 格式模型。

**官网**：[https://ollama.com](https://ollama.com)  
**GitHub 文档**：[Ollama Linux 安装](https://github.com/ollama/ollama/blob/main/docs/linux.md)

### 1.4.1 安装步骤（Ubuntu 20.04/22.04）

本文以 **Ubuntu 20.04** 安装 **Ollama** 为例
### 1.4.2 下载ollama安装包

**Ollama** 官网地址：[https://ollama.com/download/linux](https://ollama.com/download/linux)

**GitHub** 手动安装文档地址：[https://github.com/ollama/ollama/blob/main/docs/linux.md](https://github.com/ollama/ollama/blob/main/docs/linux.md)

>[!abstract] **安装包下载**
>安装包下载地址：[https://github.com/ollama/ollama/releases/download/v0.6.5/ollama-linux-arm64.tgz](https://github.com/ollama/ollama/releases/download/v0.6.5/ollama-linux-arm64.tgz)

>[!note] **解压缩**
> 这里我以上传到`/home/zeng-2-3090/dify-llama`目录为例
>```
>tar -xzf ollama-linux-amd64.tgz
>```

### 1.4.3 创建ollama.service文件

```python
vim /etc/systemd/system/ollama.service
```


```python
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/home/zeng-2-3090/dify-llama/ollama/bin/ollama serve
User=root
Group=root
Restart=always
RestartSec=3
Environment="PATH=$PATH"
Environment="OLLAMA_MODELS=/home/zeng-2-3090/dify-llama/ollama_models"
Environment="OLLAMA_HOST=0.0.0.0:11434"

[Install]
WantedBy=default.target
```

```python
[Unit]
Description=Ollama Service
After=network-online.target

[Service]
ExecStart=/home/zeng-3-3090/docker/ollama/bin/ollama serve
User=root
Group=root
Restart=always
RestartSec=3
Environment="PATH=$PATH"
Environment="OLLAMA_MODELS=/home/zeng-3-3090/docker/ollama/ollama_models"
Environment="OLLAMA_HOST=0.0.0.0:11434"

[Install]
WantedBy=default.target                
```

> **说明**：
> - `OLLAMA_MODELS`：指定模型存储目录（支持离线导入）。
> - `OLLAMA_HOST`：绑定 0.0.0.0:11434，允许外部访问。

刷新服务：

```bash
sudo systemctl daemon-reload
```

#### 1.4.3.1 管理服务

```bash
sudo systemctl enable ollama   # 开机自启
sudo systemctl start ollama    # 启动
sudo systemctl stop ollama     # 停止
sudo systemctl restart ollama  # 重启
sudo systemctl status ollama   # 查看状态
```

![服务状态](attach/Pasted%20image%2020250411221426.png)

![运行状态](attach/Pasted%20image%2020250411223846.png)

#### 1.4.3.2 创建软连接的步骤：

##### 1.4.3.2.1 确认目标文件存在且可执行

```python
# 2 进入你解压的目录，确认ollama可执行文件存在
cd /home/zeng-3-3090/docker/ollama/bin
ls -l ollama
```

```python
# 3 如果没有可执行权限，先添加
chmod +x ollama
```

##### 1.4.3.2.2 创建软连接到系统路径（需要sudo权限）

```python
# 2 创建软连接，将指定位置的ollama链接到/usr/local/bin
sudo ln -sf /home/zeng-3-3090/docker/ollama/bin/ollama /usr/local/bin/ollama
```

##### 1.4.3.2.3 可选

```python
# 3 或者，如果你希望链接到/usr/bin（也是系统路径）
sudo ln -sf /home/zeng-3-3090/docker/ollama/bin/ollama /usr/bin/ollama
```

##### 1.4.3.2.4 验证软连接

```python
# 5 检查软连接是否创建成功
ls -l /usr/local/bin/ollama
```

```python
# 6 测试命令
ollama --version
```


#### 1.4.3.3 配置环境变量

添加 **Ollama** 到 **PATH** 并更新环境变量：

```python
vim ~/.bashrc
```

```python
# 添加 ollama 环境变量
export PATH=$PATH:/home/zeng-2-3090/dify-llama/ollama/bin
```

```python
# 刷新
source ~/.bashrc
```

#### 1.4.3.4 验证安装

```bash
ollama ls
```

- 输出类似 `NAME ID SIZE MODIFIED` ，则表示安装成功。

### 1.4.4 拉取模型

Ollama 支持 GGUF 格式模型，可从 [Hugging Face](https://huggingface.co/) 或 [ModelScope](https://modelscope.cn/) 下载。

示例（`Qwen2.5-7B-Instruct`）：

```bash
ollama pull qwen2.5:7b-instruct
ollama run qwen2.5:7b-instruct
```

**创建 `Modelfile` 并指定模型路径**：需要模型文件的绝对路径并在 `Modelfile` 中使用 `FROM` 指令指向该路径。以下是具体步骤：

> [!abstract] **创建 Modelfile 并导入模型**  
> 在任意目录下创建名为 `Modelfile` 的文件（无后缀），内容如下：  
> ```
> FROM /absolute/path/to/your/model.gguf
> ```
> - 将 `/absolute/path/to/your/model.gguf` 替换为 GGUF 文件的实际路径，例如：  
>   - Windows: `C:\Users\YourName\Models\qwen2.5-7b-instruct-q4_0.gguf`  
>   - Linux/Mac: `/home/yourname/models/qwen2.5-7b-instruct-q4_0.gguf`  
> - （可选）可添加配置，如：  
>   ```
>   FROM /absolute/path/to/your/model.gguf
>   PARAMETER temperature 0.7
>   SYSTEM "You are a helpful assistant."
>   ```  
> 保存后，使用以下命令导入模型：  
> ```bash  
> ollama create your-model-name -f Modelfile  
> ```  
> 替换 `your-model-name` 为自定义名称，如 `qwen2.5-7b-instruct`。确保路径正确且对文件有读权限。

---

## 1.5 Dify 集成 Ollama 运行本地模型

以下说明如何在 Dify 中配置 Ollama 运行本地模型（如 Qwen2.5-7B-Instruct），并设置知识库。

在 **Docker** 里面部署好了 **Dify**，就可以在浏览器中输入 `https://xxx.xx.xx.xx:Nginx暴露端口号/install` 来打开 **Dify** 配置页面。

### 1.5.1 配置智能体应用

#### 1.5.1.1 创建应用

1. 登录 **Dify**，点击 **创建空白应用** > **Agent**。
2. 设置名称和图标，创建完成。

![创建智能体](attach/Pasted%20image%2020250411192152.png)

#### 1.5.1.2 配置模型

点击上一步中创建好的智能体，点击去 "去设置"， 就可以输入申请的 **API Key** 或者 **本地大模型** 。

1. 进入应用，点击 **去设置**。
2. 选择 **Ollama**，点击 **添加模型**。

![模型设置](attach/Pasted%20image%2020250411192204.png)

![Ollama 配置](attach/Pasted%20image%2020250411192218.png)

![添加模型](attach/Pasted%20image%2020250412174633.png)

![模型页面](attach/Pasted%20image%2020250412174512.png)

> **替代路径**：点击右上角账户名 > **设置**，进入模型配置页面。

![240](attach/Pasted%20image%2020250412173916.png)

### 1.5.2 服务器端 Ollama 配置

由于 **Docker** 上部署的 **DIFY** 调用本地模型有独特逻辑，同时 **Docker** 有自己的url路由策略，因此为了幸福生活，请不要在 **url** 填写：`http://localhost:11434` 、 `http://127.0.0.1:11434` 、`http://0.0.0.0:11434` 等地址。

应该填写为：**局域网IP** 或者 **服务器IP**

这里的 **局域网IP** 为：`192.168.8.103`

![400](attach/Pasted%20image%2020250412165113.png)

![配置完成](attach/Pasted%20image%2020250412164543.png)


> [!success] **~ SUCCESS ~**
>**Dify 与 服务器端 Ollama 配置完整完成！！！！！！**

## 1.6 电脑端部署的 Ollama 配置

事先我已经安装了Ollama并下载了几个大模型，如果大家没有事先准备好。那先下载安装Ollama，并在命令行工具里面下载运行大模型即可，简单的命令如下：

![](attach/Pasted%20image%2020250411192237.png)

如果有必要，我可以再写一篇关于Ollama相关的文章，这里不再描述。然后在弹出页面中输入具体内容，如下红色箭头部分不能直接输入 [http://localhost:11434](http://localhost:11434)

![400](attach/Pasted%20image%2020250411192246.png)

输入localhost，点击保存总是报错

```python
"An error occurred during credentials validation: HTTPConnectionPool(host='localhost', port=11434): Max retries exceeded with url: /api/chat (Caused by NewConnectionError('<urllib3.connection.HTTPConnection object at 0x7f4a84ce0590>: Failed to establish a new connection: [Errno 111] Connection refused'))"。
```

问题主要出在docker类似于虚拟机，如果直接写 `http://localhost:11434`，其实访问的是docker本身的服务，肯定就找不到了。其实当前请求相当于docker要访问主机机器的地址，那就需要把主机的ollama地址暴露出来，步骤如下：

系统变量里面加 OLLAMA\_HOST，然后输入局域网地址或者直接输入"0.0.0.0"; 如果是对外的网络地址也行。然后在path里面增加%OLLAMA\_HOST%，重启Ollama即可。

![](attach/Pasted%20image%2020250411192314.png)

### 1.6.1 配置知识库[Embedding模型](https://zhida.zhihu.com/search?content_id=253575662&content_type=Article&match_order=1&q=Embedding%E6%A8%A1%E5%9E%8B&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDQ1NDMxODksInEiOiJFbWJlZGRpbmfmqKHlnosiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNTM1NzU2NjIsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.YJnB3ptq54DoDoCr0n0feWDdKIsHpdRLoyqg_YpnRGU&zhida_source=entity)

逻辑推理用deepseek大模型, 知识库Embedding不用deepseek，说命中率不高，回答问题效果不好，所以选用[BGE-M3](https://zhida.zhihu.com/search?content_id=253575662&content_type=Article&match_order=1&q=BGE-M3&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NDQ1NDMxODksInEiOiJCR0UtTTMiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyNTM1NzU2NjIsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.NPDar6N5JNYVEu7jpxXPvNfpK9pLEc51qZWCRAwKA24&zhida_source=entity)。按如下红色箭头命令操作，然后查看一下，模型已经下载完成。

![](attach/Pasted%20image%2020250411192323.png)

BGE (BAAI General Embedding) 专注于检索增强LLM领域，对中文场景支持效果更好，当然也有很多其他embedding模型可供选择，可以根据自己的场景，在ollama上搜索“embedding”查询适合自己的嵌入模型。

![](attach/Pasted%20image%2020250411192335.png)

配置如上图，如果说连不上报错，要确认Ollama是否启动，就直接在浏览器里面输入URL看看是否有“Ollama is running”字样。点击保存按钮，就可以看到如下所示LLM用了deepseek-r1:14b而TEXT EMBEDDING用的是bge-m3。

![](attach/Pasted%20image%2020250411192345.png)

> **至此，电脑端 Ollama 部署配置完成**~~~~

## 1.7 知识库配置

### 1.7.1 创建知识库

如下图操作

![](attach/Pasted%20image%2020250411192401.png)

### 1.7.2 上传RAG资料

可以看到有三步，即选择数据源，文本分段与清洗，处理并完成。资料可以是本地的文本文件，或者直接同步网络资料等等。

![](attach/Pasted%20image%2020250411192413.png)

支持的文本文件类型也很多，不过要注意单个文件不能超过15M。如果文件大了怎么办，拆呗。

### 1.7.3 保存资料并处理

我上传了一个自己写的用户手册，pdf格式，12.86M，可以处理。点击下一步，如下图。

![](attach/Pasted%20image%2020250411192423.png)

![](attach/Pasted%20image%2020250411192434.png)

分段设置直接用了通用的，索引方式用高质量，Embedding模型用bge-m3，检索设置用混合检索。点击保存并处理，等待处理完成。

![](attach/Pasted%20image%2020250411192442.png)

完成前往文档，知识库里面就有一个文档知识库内容了。

![](attach/Pasted%20image%2020250411192452.png)

### 1.7.4 知识库测试

点击工作室，并打开已经创建完成的智能体(Agent)

![](attach/Pasted%20image%2020250411192501.png)


> [!success] **~ SUCCESS ~**
>**Dify 与 电脑端 Ollama 配置完整完成！！！！！！**


---

## 1.8 常见问题及解决

### 1.8.1 Docker 安装

- **源不可用**：
  - 更换其他镜像源（如 `https://docker.ketches.cn`）。
  - 检查网络：

```bash
ping mirrors.aliyun.com
```

### 1.8.2 Dify 部署

#### 1.8.2.1 **容器未启动**：

##### 1.8.2.1.1 查看日志：

```bash
docker logs dify-api
```

##### 1.8.2.1.2 确保端口未被占用：

```bash
sudo netstat -tuln | grep 80
```

#### 1.8.2.2 **访问失败**：
  
- 确认 Nginx 端口（`EXPOSE_NGINX_PORT`）。
 - 检查防火墙：

```bash
sudo ufw allow 1210
```

### 1.8.3 Ollama 配置

- **模型拉取慢**：

  - 使用国内镜像（如 [魔塔社区](https://modelscope.cn/home)）。
  - 离线导入 `GGUF` 文件。

- **Dify 连接 Ollama 失败**：

  - 确认 Ollama 服务：`curl http://192.168.8.103:11434`  
  - 使用正确 IP（非 `localhost`）。

- **内存不足**：
  
  - 选择低量化模型（如 `Q3_K_M`）。
  - 增加虚拟内存：

```bash
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```





# 2 DIFY 无损升级与多个 DIFY 服务部署

---
## 2.1 DIFY 无损升级

---

### 2.1.1 备份数据

在 **DIFY** 中，所有用户的数据保存在 `dify/docekr/volumes` 目录

> DIFY 主目录 -> docker 目录

```bash
cd dify/docker
```

![](attach/Pasted%20image%2020250628165421.png)

**备份 `volumes` 目录**

```python
# 备份可命名为：volumes-版本号
sudo cp volumns volumes-1.5
```

**注意：** 备份之后，可找个位置严格保存好 `volumes` ，避免误操作删除！！！


---

### 2.1.2 记录环境配置中的端口设定

备份好数据之后，同时记录下 `.env.example` 中指定的端口设置，如下：

```bash
cat .env.example
```

```bash
# 主要记录四个配置端口： 
# EXPOSE_NGINX_PORT、EXPOSE_NGINX_SSL_PORT
# PLUGIN_DEBUGGING_PORT、EXPOSE_PLUGIN_DEBUGGING_PORT

EXPOSE_NGINX_PORT=1210
EXPOSE_NGINX_SSL_PORT=443

PLUGIN_DEBUGGING_PORT=5003
EXPOSE_PLUGIN_DEBUGGING_PORT=5003
```

![](attach/Pasted%20image%2020250628165902.png)

![](attach/Pasted%20image%2020250628165943.png)


---

### 2.1.3 获取最新的 DIFY 项目包

#### 2.1.3.1 官方网址

[langgenius/dify: Production-ready platform for agentic workflow development.](https://github.com/langgenius/dify)

#### 2.1.3.2 下载最新版本的项目包

![](attach/Pasted%20image%2020250628171147.png)

#### 2.1.3.3 将下载好的 `DIFY` 项目包放置到用户自定义位置

**DIFY 的项目包可命名为：`DIFY-版本号`

![](attach/Pasted%20image%2020250628171344.png)

#### 2.1.3.4 **复制**备份数据到新版本的 DIFY 目录

这里以 `dify1.4` 为旧版本，`dify1.5` 为新版本为例子：

在新版本项目的 `dify/docker` 下，将前面备份的 `volumes-版本号` 替换掉原来的 `volumes`，并将名字从 `volumes-版本号` 改为 `volumes` 。

```bash
sudo cp -r dify1.4/docker/volumes-1.4 dify1.5/docekr/
sudo mv dify1.5/docekr/volumes-1.5 dify1.5/docekr/volumes
```

#### 2.1.3.5 编辑环境配置文件 `.env.example` 

这里以 `dify1.4` 为旧版本，`dify1.5` 为新版本为例子：

根据前面记录好的 `dify1.4` 环境配置，如下所示：

```bash
# 主要记录四个配置端口： 
# EXPOSE_NGINX_PORT、EXPOSE_NGINX_SSL_PORT
# PLUGIN_DEBUGGING_PORT、EXPOSE_PLUGIN_DEBUGGING_PORT

EXPOSE_NGINX_PORT=1210
EXPOSE_NGINX_SSL_PORT=443

PLUGIN_DEBUGGING_PORT=5003
EXPOSE_PLUGIN_DEBUGGING_PORT=5003
```

![](attach/Pasted%20image%2020250628163520.png)

![](attach/Pasted%20image%2020250628174031.png)

编辑 `dify1.5` 的环境配置 `.env.example`

```bash
vim dify1.5/docker/.env.example
```

填入与旧版本一致的端口

```bash
EXPOSE_NGINX_PORT=1210
EXPOSE_NGINX_SSL_PORT=443

PLUGIN_DEBUGGING_PORT=5003
EXPOSE_PLUGIN_DEBUGGING_PORT=5003
```

至此，环境配置完成！！！

#### 2.1.3.6 启动新版本的容器

这里以 `dify1.4` 为旧版本，`dify1.5` 为新版本为例子：

```bash
cd dify1.5/docker
```

启动容器

```bash
sudo docker-compose up -d
```

等待镜像拉取并启动容器完成

![](attach/Pasted%20image%2020250628175153.png)

访问服务：浏览器输入  **服务器IP** : **`EXPOSE_NGINX_PORT`端口**（如 `http://xxx.xxx.xxx.xxx:1210`）。

![](attach/Pasted%20image%2020250628175331.png)

![](attach/Pasted%20image%2020250628175342.png)


> [!success] **~ SUCCESS ~**
>**DIFY 升级完美完成！！！！！！**


---
# 3 多实例部署 DIFY 服务指南

---

## 3.1 实现原理

通过 `docker-compose` 的 `-p` 参数指定不同项目名称，配合独立的配置文件和端口设置，实现**多实例部署**。

每个实例需确保网络**端口不冲突**，且使用独立的配置文件。

---

## 3.2 部署步骤

### 3.2.1 启动第一个 `dify` 实例，命名为 `dify-1_4`

```bash
sudo docker-compose -p dify-1_4 up -d
```

![](attach/Pasted%20image%2020250628183756.png)

此时已经启动了 `dify-1_4` ，若需在启动一个 `dify` 服务，需要复制一份 `dify` 的项目包，将下列的几个端口全都修改

**注意：** 确保各个端口**互不冲突**。

```bash
# 主要修改以下四个配置端口

EXPOSE_NGINX_PORT=xxxx
EXPOSE_NGINX_SSL_PORT=xxxx

PLUGIN_DEBUGGING_PORT=xxxx
EXPOSE_PLUGIN_DEBUGGING_PORT=xxxx
```

---
### 3.2.2 创建项目副本

```bash
# 原始项目目录（假设已存在）
cd /path/to/dify

# 创建新实例副本（建议使用数字后缀命名）
cp -r dify dify-01
````

---
### 3.2.3 修改端口配置

```bash
cd dify-01
sudo vim .env.example              # 编辑环境变量
```

**需修改的核心端口参数**（确保与已部署实例不冲突）：

- 已启动的 `dify-1_4` 占用的端口

```bash
# 主要记录四个配置端口： 
# EXPOSE_NGINX_PORT、EXPOSE_NGINX_SSL_PORT
# PLUGIN_DEBUGGING_PORT、EXPOSE_PLUGIN_DEBUGGING_PORT

EXPOSE_NGINX_PORT=1210
EXPOSE_NGINX_SSL_PORT=443

PLUGIN_DEBUGGING_PORT=5003
EXPOSE_PLUGIN_DEBUGGING_PORT=5003
```

- 未确保不冲突，新修改的端口如下

```bash
# 主要记录四个配置端口： 
# EXPOSE_NGINX_PORT、EXPOSE_NGINX_SSL_PORT
# PLUGIN_DEBUGGING_PORT、EXPOSE_PLUGIN_DEBUGGING_PORT

EXPOSE_NGINX_PORT=1212
EXPOSE_NGINX_SSL_PORT=441

PLUGIN_DEBUGGING_PORT=5013
EXPOSE_PLUGIN_DEBUGGING_PORT=5013
```

---
### 3.2.4 启动新实例

```bash
# 使用指定项目名称启动服务（-p 参数）
sudo docker-compose -p dify-1_5 up -d
```

![](attach/Pasted%20image%2020250628183522.png)

观察容器状态

```bash
sudo docker ps -a
```

![](attach/Pasted%20image%2020250628183921.png)

可以看到 `dify-1_4` 和 `dify-1_5` 两个示例均在运行，多实例部署成功！！！

---

## 3.3 注意事项

1. **命名规范**
    
    - 项目名称（`-p` 参数值）必须为纯字母数字组合
    - 建议使用 `dify-序号` 格式（如 `dify-1`、`dify-2`）

2. **端口管理**
    
    - 检查系统端口占用情况：`netstat -tuln`
    - 常见冲突端口：80/443/5000 系列
    - 建议为每个实例预留连续端口段（如 1210-1219）

3. **配置文件管理**
    
    - 每个实例应使用独立的 `.env` 文件
    - 修改后建议执行 `docker-compose config` 验证配置

4. **资源分配**
    
    - 单机部署时注意 CPU/内存限制
    - 生产环境建议使用 Kubernetes 等编排工具

---


> [!success] **~ SUCCESS ~**
>**多个示例部署 DIFY 完美完成！！！！！！**















