

***
## 0.1 背景说明

Overleaf 又挂了，数据放在别人手里真是不靠谱。反正Overleaf是开源的，所以自己搭建一个。

![Image 14](https://i-blog.csdnimg.cn/direct/fed2cceb21db4564969d6080445af8fe.png)

![Image 15](https://i-blog.csdnimg.cn/direct/c075c147ff1b4c999d519dbf26515abb.png)

[Overleaf Status](https://status.overleaf.com/ "Overleaf Status")

> 教程来自官方：[toolkit/doc/quick-start-guide.md at master · overleaf/toolkit · GitHub](https://github.com/overleaf/toolkit/blob/master/doc/quick-start-guide.md "toolkit/doc/quick-start-guide.md at master · overleaf/toolkit · GitHub")

## 0.2 下载仓库

首先，让我们将这个git存储库克隆到你的机器上：

```bash
git clone https://github.com/overleaf/toolkit.git ./overleaf-toolkit
```

![Image 16](https://i-blog.csdnimg.cn/direct/91f5f6544ae547948e91130b151e69f0.png)

接下来让我们进入这个目录：

```bash
cd ./overleaf-toolkit
```

AI写代码 让我们看一下存储库的结构：

```bash
ls -l
```

AI写代码 它将打印如下内容：

```haskell
bin    CHANGELOG.md    config    data    doc    lib    LICENSE    README.md
```

AI写代码

*   该`README.md`文件包含一些关于项目的有用信息
*   `doc`目录包含使用该工具包所需的所有留档
*   `config`目录将包含本地配置文件（我们稍后将创建）
*   `bin`目录包含管理背面实例的脚本集合

## 0.3 [初始化](https://so.csdn.net/so/search?q=%E5%88%9D%E5%A7%8B%E5%8C%96&spm=1001.2101.3001.7020)配置

让我们通过运行`bin/init`来创建本地配置：

```bash
bin/init
```

AI写代码 ![Image 17](https://i-blog.csdnimg.cn/direct/990da247ecef4082a6ff5d474d56a9ef.png)

现在检查`config/`目录的内容

```bash
ls config# overleaf.rc     variables.env     version
```

AI写代码 这是将与之交互的三个配置文件：

*   `overleaf.rc`：主要的顶级配置文件
*   `variables.env`：加载到docker容器中的环境变量
*   `version`：要使用的docker映像的版本

> *   默认情况下，工具包使用免费的社区版
> *   默认情况下，overleaf使用texlive，并且宏包是不全的

## 0.4 修改监听IP和端口

在`./config/overleaf.rc`中，需要修改以下字段：

```bash
OVERLEAF_LISTEN_IP=0.0.0.0 # 监听所有的IPOVERLEAF_PORT=8000         # 默认是80端口
```

AI写代码 ![Image 18](https://i-blog.csdnimg.cn/direct/f11619b22a6545608ea4b1741988400f.png)

## 0.5 自定义网站名称

在`./config/variables.env`文件中，修改：

```bash
OVERLEAF_APP_NAME="Overleaf Instance"
OVERLEAF_SITE_URL=xxxOVERLEAF_NAV_TITLE="Overleaf Instance"
OVERLEAF_ADMIN_EMAIL=mail@xxx.site
```

AI写代码 ![Image 19](https://i-blog.csdnimg.cn/direct/23101f800c434cc7b7d326e62c292bb6.png)

修改完配置文件之后，需要重新build才可以应用配置。

```bash
sudo bin/down sudo bin/up
```

## 0.6 AI写代码 修改Mongo版本

> [MongoDB](https://so.csdn.net/so/search?q=MongoDB&spm=1001.2101.3001.7020) 官方从 5.0 开始引入的硬件限制，强制要求支持 AVX，**无法通过任何软件方法绕过**。所以需要降低版本。

在`./config/overleaf.rc`中，需要修改以下字段：

```bash
MONGO_VERSION=4.4
```

## 0.7 AI写代码 修改数据存放位置

在`./config/overleaf.rc`中，需要修改以下字段：

![Image 20](https://i-blog.csdnimg.cn/direct/b974ad0234004520a2cdd8a4c51600a9.png)

## 0.8 更换 Docker 源

可以看这篇：[【教程】最新可用！Docker国内镜像源列表\_docker镜像源-CSDN博客](https://blog.csdn.net/sxf1061700625/article/details/140895299 "【教程】最新可用！Docker国内镜像源列表_docker镜像源-CSDN博客")

> 推荐：[https://docker.1panel.live](https://docker.1panel.live)

## 0.9 更换Docker存储位置

可以看这篇：[【教程】Docker更换存储位置-CSDN博客](https://blog.csdn.net/sxf1061700625/article/details/147956294 "【教程】Docker更换存储位置-CSDN博客")

## 0.10 启动Overleaf

该工具包使用`docker compose`来管理docker容器。该工具包提供了一组脚本来包装`docker compose`，并处理大部分细节。

可以先检查一下是否存在隐性问题：

```bash
sudo bin/doctor
```

AI写代码 ![Image 21](https://i-blog.csdnimg.cn/direct/0b24dd1c69eb4bcdb4244af0c38e46db.png)

然后让我们启动docker服务：

```bash
sudo bin/up # 后台运行：# sudo bin/up -d
```

AI写代码 应该看到docker容器的一些日志输出，表明容器正在运行。如果在终端按`CTRL-C`，服务将关闭。可以通过运行`bin/start`再次启动它们（而不附加到日志输出）。更一般地说，如果发现脚本没有涵盖你的用例，可以运行`bin/docker-compose`来直接控制`docker compose`系统。

![Image 22](https://i-blog.csdnimg.cn/direct/134cf00d3d03441f87517e35a504f53e.png)

## 0.11 创建管理员帐户

1.  在浏览器中，打开[http:///launchpad](http://localhost/launchpad "http://<ip>/launchpad")。应该会看到一个包含电子邮件和密码字段的表单。用想用作管理员帐户的凭据填写这些凭据，然后单击“注册”。
2.  然后单击链接进入登录页面（[http:///login](http://localhost/login "http://<ip>/login")）。输入凭据。登录后，将被带到欢迎页面。
3.  单击页面底部的绿色按钮开始使用。

![Image 23](https://i-blog.csdnimg.cn/direct/21350faa7c6f4dd5988e0595c0237e66.png)

## 0.12 创建第一个项目

1.  在[http:///project](http://localhost/project "http://<ip>/project")页面上，将看到一个按钮，提示创建第一个项目。单击按钮并按照说明进行操作。
2.  然后，你应该被带到新项目，在那里将看到一个文本编辑器和一个PDF预览。

![Image 24](https://i-blog.csdnimg.cn/direct/bfcc7c3806c44a0eab95958ba2b0941f.png)

## 0.13 创建其他账户

![Image 25](https://i-blog.csdnimg.cn/direct/d14020b024554e3ca725f2a6ab7a75ae.png)

![Image 26](https://i-blog.csdnimg.cn/direct/b2f98427220f4f7fbbff1fec89641151.png)

## 0.14 安装完整环境

1、进入镜像

```bash
docker exec -it sharelatex bash
```

AI写代码 2、进入目录

```bash
cd /usr/local/texlive
```

AI写代码 3、下载文件

```bash
wget http://mirror.ctan.org/systems/texlive/tlnet/update-tlmgr-latest.sh --no-check-certificate
```

AI写代码 4、换国内源

```bash
tlmgr option repository https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet/
```

AI写代码 5、执行更新

```bash
sh update-tlmgr-latest.sh -- --upgradetlmgr option repository ctantlmgr update --self --allluaotfload-tool -fu
```

AI写代码 6、确认更新

```bash
tlmgr install scheme-full
```

AI写代码 ![Image 27](https://i-blog.csdnimg.cn/direct/3cba164a6a1b45dab1f504237a0e9a3f.png)

7、退出并重启Overleaf即可

```bash
sudo bin/stopsudo bin/start
```

AI写代码

> 另一种方法：也可以在**overleaf.rc**中把**OVERLEAF\_IMAGE\_NAME**改为**tuetenk0pp/sharelatex-full**，这样在执行**bin/up**时候就会自动拉取完整版的镜像，更方便。
> 
> ![Image 28](https://i-blog.csdnimg.cn/direct/ab6f03e2e3de449bac94a16ff3b482b9.png)

## 0.15 配置邮件提醒

![Image 29](https://i-blog.csdnimg.cn/direct/a8e038af0af64f5fbf822ba41333f5f9.png)

![Image 30](https://img-blog.csdnimg.cn/6232cd49a9694dcb963dd5d7c81615f2.jpeg)
![Image 166](https://csdnimg.cn/release/blogv2/dist/pc/img/quoteClose1White.png)

![Image 167](https://i-blog.csdnimg.cn/direct/fed2cceb21db4564969d6080445af8fe.png)

![Image 168](https://i-blog.csdnimg.cn/direct/c075c147ff1b4c999d519dbf26515abb.png)

![Image 169](https://i-blog.csdnimg.cn/direct/91f5f6544ae547948e91130b151e69f0.png)

![Image 170](https://i-blog.csdnimg.cn/direct/990da247ecef4082a6ff5d474d56a9ef.png)

![Image 171](https://i-blog.csdnimg.cn/direct/f11619b22a6545608ea4b1741988400f.png)

![Image 172](https://i-blog.csdnimg.cn/direct/23101f800c434cc7b7d326e62c292bb6.png)

![Image 173](https://i-blog.csdnimg.cn/direct/b974ad0234004520a2cdd8a4c51600a9.png)

![Image 174](https://i-blog.csdnimg.cn/direct/0b24dd1c69eb4bcdb4244af0c38e46db.png)

![Image 175](https://i-blog.csdnimg.cn/direct/134cf00d3d03441f87517e35a504f53e.png)

![Image 176](https://i-blog.csdnimg.cn/direct/21350faa7c6f4dd5988e0595c0237e66.png)

![Image 177](https://i-blog.csdnimg.cn/direct/bfcc7c3806c44a0eab95958ba2b0941f.png)

![Image 178](https://i-blog.csdnimg.cn/direct/d14020b024554e3ca725f2a6ab7a75ae.png)

![Image 179](https://i-blog.csdnimg.cn/direct/b2f98427220f4f7fbbff1fec89641151.png)

![Image 180](https://i-blog.csdnimg.cn/direct/3cba164a6a1b45dab1f504237a0e9a3f.png)

![Image 181](https://i-blog.csdnimg.cn/direct/ab6f03e2e3de449bac94a16ff3b482b9.png)

![Image 182](https://i-blog.csdnimg.cn/direct/a8e038af0af64f5fbf822ba41333f5f9.png)



