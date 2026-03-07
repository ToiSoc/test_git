
## 0.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)一、[uv](https://so.csdn.net/so/search?q=uv&spm=1001.2101.3001.7020)简介

uv 是由 Astral（前身为 Basis）团队开发的 [Python 包](https://so.csdn.net/so/search?q=Python%20%E5%8C%85&spm=1001.2101.3001.7020)安装器和解析器，完全使用 Rust 语言编写。与传统 Python 工具不同，uv 将多个工具的功能整合到一个高性能的解决方案中，旨在提供更现代、更高效的 Python 开发体验。

### 0.1.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)底层技术原理

uv 之所以能够实现显著的性能提升，主要基于几个关键技术：

1.  **Rust 实现**：利用 Rust 语言的内存安全和高性能特性，避免了 Python 自身的解释开销
2.  **并行处理**：在依赖解析和包下载安装过程中大量使用并行处理
3.  **优化的缓存策略**：智能缓存机制减少重复下载和编译
4.  **零拷贝设计**：减少内存使用和系统调用
5.  **编译优化**：对于需要编译的包，采用更高效的编译策略

![Image 14](https://i-blog.csdnimg.cn/direct/35310b0fdf774a32b8f1cc805210094c.png)

### 0.1.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)uv性能

uv一个极快的 Python 包和项目管理器，用 Rust 编写，有多快呢，看图说话：

![Image 15](https://i-blog.csdnimg.cn/direct/d7731999d9374bdf8a49372456a73336.png)

### 0.1.3 [](https://blog.csdn.net/2501_90561511/article/details/147746031)突出特点

​🚀 一款工具，可替代 pip、pip-tools、pipx、poetry、pyenv、twine、virtualenv 等，并更多。

⚡️比 pip 快 10-100 倍。

🗂️ 提供全面的项目管理，具有 通用的锁文件。

❇️运行脚本，支持 内联依赖元数据.

🐍安装和管理 Python 版本。

🛠️运行和安装 发布为 Python 包的工具。

🔩 包含一个 与 pip 兼容的接口，以熟悉的 CLI 提升性能。

🏢 支持 Cargo 风格的工作区，适用于可扩展的项目。

💾 磁盘空间高效，具有全局缓存以进行依赖项去重。

⏬ 无需 Rust 或 Python，通过 curl 或 pip 即可安装。

🖥️ 支持 macOS、Linux 和 Windows。

## 0.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)二、安装

### 0.2.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)不同环境安装

根据你的操作系统，选择安装方式，Windows 安装，需要powershell。

```cobol
# On macOS and Linux.curl -LsSf https://astral.sh/uv/install.sh | sh # On Windows.powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex" # With pip.pip install uv #如果你支持pipx，也可以安装到隔离环境中pipx install uv
```

运行本项目

![Image 16](https://csdnimg.cn/release/blogv2/dist/pc/img/runCode/icon-arrowwhite.png)

执行界面：

![Image 17](https://i-blog.csdnimg.cn/direct/3e3c6b12514b430ebc0b9fb482c809f5.png)

注：dos窗口重新打开，才能开到更新的环境变量，才能使用新安装的uv工具。

另：如果需要更多安装方式或者卸载，见：[https://docs.astral.sh/uv/getting-started/installation/#pypi](https://docs.astral.sh/uv/getting-started/installation/#pypi "https://docs.astral.sh/uv/getting-started/installation/#pypi")

### 0.2.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)查看版本

要检查已安装的版本：

```undefined
uv version
```

运行本项目

以下内容也有效：

*   uv --version # Same output as `uv version`
*   uv -V # Will not include the build commit and date
*   uv pip --version # Can be used with a subcommand

## 0.3 [](https://blog.csdn.net/2501_90561511/article/details/147746031)三、uv功能

uv 为 Python 开发提供必要功能——从安装 Python 和编写简单的脚本到处理支持多个 Python 版本和平台的复杂项目。

### 0.3.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.1 Python 版本管理

Python 版本由 Python 解释器（即`python`可执行文件）、标准库和 其他 支持文件组成。安装和管理 Python 本身。

*   uv python install：安装 Python 版本。
*   uv python list：查看可用的 Python 版本。
*   uv python find：查找已安装的 Python 版本。
*   uv python pin：将当前项目固定到使用特定 Python 版本。
*   uv python uninstall：卸载 Python 版本。

命令示例：

```cobol
uv python list  # 查看uv支持的python版本 uv python install 3.10 3.11 3.12 # 安装指定版本的Python uv python find 3.10  # 查找特定版本的python uv python uninstall 3.10  # 卸载特定版本的python uv run --python 3.12 python  # 指定版本运行python交互界面uv run -p 3.12 python  # 指定版本运行python交互界面uv run --python pypy@3.8 pythonuv run -p pypy@3.8 python uv python pin 3.11  # 在当前目录中使用特定的 Python 版本
```

运行本项目

![Image 18](https://csdnimg.cn/release/blogv2/dist/pc/img/runCode/icon-arrowwhite.png)

### 0.3.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.2 运行脚本

#### 0.3.2.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)运行无依赖项的脚本

执行独立的 Python 脚本，例如 example.py。

> example.py
> 
> `print("Hello world")`

执行它：

![Image 19](https://i-blog.csdnimg.cn/direct/3f1a04bcfd21445986b22f1d8048d6ba.png)

如果您的脚本依赖于 standard 库中的模块，也不需要安装依赖，执行命令是一样的。

如果需要向脚本提供参数，如：

> example.py
> 
> `import sys print(" ".join(sys.argv[1:]))`

执行效果：

![Image 20](https://i-blog.csdnimg.cn/direct/c0d1cde77e654039b90224fce6fe450a.png)

此外，您可以直接从 stdin 读取您的脚本：

```php
$ echo 'print("hello world!")' | uv run -
```

运行本项目

> 注意，如果你在一个 _项目中_ 使用`uv run`，即一个带有`pyproject.toml`的目录，它会在运行脚本之前进行安装。如果您的脚本不依赖于项目，可以使用`--no-project`标志跳过此步骤：
> 
> ```perl
> # Note: the `--no-project` flag must be provided _before_ the script name.uv run --no-project example.py
> ```
> 
> 运行本项目

#### 0.3.2.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)运行有依赖项的脚本

当您的脚本需要其他软件包时，必须将它们安装到 脚本运行环境中。可以使用`pyproject.toml管理依赖，uv会在执行脚步前进行安装。同时，`UV 也支持每次调用请求依赖项。

例如，以下脚本需要`rich`。

> example.py
> 
> ```cobol
> import timefrom rich.progress import track for i in track(range(20), description="For example:"):    time.sleep(0.05)
> ```
> 
> 运行本项目

使用`--with`选项请求依赖项：

```crystal
$ uv run --with rich example.py
```

运行本项目

执行效果：

![Image 21](https://i-blog.csdnimg.cn/direct/eaa4df5514184a45866188903185429d.png)

如果需要特定版本，可以将 constraints 添加到请求的依赖项中：

```cobol
$ uv run --with 'rich>12,<13' example.py
```

运行本项目

可以通过使用`--with`选项重复来请求多个依赖项。

#### 0.3.2.3 [](https://blog.csdn.net/2501_90561511/article/details/147746031)运动带有Python内置依赖

如果python脚本，在脚本本身中声明脚本的依赖项，用--script参数执行。

> 以下是包含嵌入元数据的脚本示例：
> 
> _\# /// script_ _\# requires-python = ">=3.11"_ _\# dependencies = \[_ _\# "requests<3",_ _\# "rich",_ _\# \]_ _\# ///_**import** requests**from** rich.pretty **import** pprint resp **\=** requests\*\*.**get**(**"[https://peps.python.org/api/peps.json](https://peps.python.org/api/peps.json)"**)**data **\=** resp**.**json**()**pprint**(\[(**k**,\*\* v\*\*\[**"title"**\])\*\* **for** k\*\*,\*\* v **in** data\*\*.**items**()\]\[:****10****\])\*\*

使用`uv init --script`使用内联元数据初始化脚本，它允许选择 Python 版本和定义依赖项：

```scss
uv init --script example.py --python 3.12
```

运行本项目

UV 支持为您添加和更新内联脚本元数据。使用`uv add --script`声明脚本的依赖项：

```csharp
uv add --script example.py 'requests<3' 'rich'
```

运行本项目

这将在脚本顶部添加一个`脚本`部分，使用 TOML 声明依赖项：

> example.py
> 
> ```haskell
> # /// script# dependencies = [#   "requests<3",#   "rich",# ]# /// import requestsfrom rich.pretty import pprint resp = requests.get("https://peps.python.org/api/peps.json")data = resp.json()pprint([(k, v["title"]) for k, v in data.items()][:10])
> ```
> 
> 运行本项目
> 
> ![Image 22](https://csdnimg.cn/release/blogv2/dist/pc/img/runCode/icon-arrowwhite.png)

#### 0.3.2.4 [](https://blog.csdn.net/2501_90561511/article/details/147746031)命令一览表

*   uv run：运行脚本。
*   uv add --script：向脚本添加依赖。
*   uv remove --script: 从脚本中移除依赖

### 0.3.3 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.3 虚拟环境管理

#### 0.3.3.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)创建虚拟环境

UV 支持创建虚拟环境，例如，在 .venv 中创建虚拟环境：

```undefined
uv venv
```

运行本项目

可以指定特定的名称或路径，例如，在 my-name 处创建虚拟环境：

```perl
uv venv my-name
```

运行本项目

可以请求 Python 版本，例如，使用 Python 3.11 创建虚拟环境：

```sql
uv venv --python 3.11
```

运行本项目

#### 0.3.3.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)使用虚拟环境

使用默认虚拟环境名称时，uv 将在后续调用期间自动查找并使用虚拟环境。

```php
uv venv # Install a package in the new virtual environmentuv pip install ruff
```

运行本项目

可以 “激活” 虚拟环境以使其软件包可用：

```bash
#Windows.venv（环境所在目录，就是环境名）\Scripts\activate #macOS and Linux#终端类型：fish，执行source .venv/bin/activate#终端类型：csh/tcsh，执行source .venv/bin/activate.csh#终端类型：Nushell，执行use .venv\Scripts\activate.nu
```

运行本项目

![Image 23](https://csdnimg.cn/release/blogv2/dist/pc/img/runCode/icon-arrowwhite.png)

#### 0.3.3.3 [](https://blog.csdn.net/2501_90561511/article/details/147746031)退出环境

要退出虚拟环境，请使用`deactivate`命令：

```undefined
deactivate
```

运行本项目

#### 0.3.3.4 [](https://blog.csdn.net/2501_90561511/article/details/147746031)使用任意 Python 环境

由于 uv 不依赖于 Python，因此它可以安装到自己的虚拟环境中。例如，设置 VIRTUAL\_ENV=/path/to/venv 将导致 uv 安装到 /path/to/venv 中，无论之前 uv 安装在何处。请注意，如果 VIRTUAL\_ENV 设置为 不是符合 PEP 405 的虚拟环境，它将被忽略。

UV 还可以安装到任意甚至非虚拟环境中，只需向 uv pip sync 或 uv pip install 提供 --python 参数即可。例如 uv pip install --python /path/to/python 将安装到链接到 /path/to/python 解释器。

为方便起见，uv pip install --system 将安装到系统 Python 环境中。用 --system 大致等同于 uv pip install --python $(which python) 。

uv 本身不依赖于 Python，但它确实需要找到一个 Python 环境来 （1） 将依赖项安装到环境中，以及 （2） 构建源代码分发。

#### 0.3.3.5 [](https://blog.csdn.net/2501_90561511/article/details/147746031)发现 Python 环境

当运行改变环境的命令（如 uv pip sync 或 uv pip install）时，uv 将按以下顺序搜索虚拟环境：

*   基于 VIRTUAL\_ENV 环境变量的已激活虚拟环境。
*   基于 CONDA\_PREFIX 环境变量的已激活 Conda 环境。
*   当前目录或最近的父目录中 .venv 的虚拟环境。
*   如果未找到虚拟环境，uv 将提示用户通过 uv venv 在当前目录中创建一个虚拟环境。

### 0.3.4 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.4 兼容pip

UV 提供了常见`pip`、`pip-tools`和`virtualenv`命令的直接替代品。这些命令直接与虚拟环境一起使用，而 uv 的主界面则自动管理虚拟环境。`uv pip`向尚未准备好从`pip`、`pip-tools`过渡的高级用户和项目展示了 uv 的速度和功能。

在环境中管理包（替换 pip 和 pipdeptree）:

*   uv pip install：将包安装到当前环境。
*   uv pip show：显示已安装包的详细信息。
*   uv pip freeze：列出已安装的包及其版本。
*   uv pip check：检查当前环境是否有兼容的包。
*   uv pip list：列出已安装的包。
*   uv pip uninstall：卸载包。
*   uv pip tree：查看环境的依赖树。

锁定环境中的包（替换 pip-tools）：

*   uv pip compile：将需求编译到锁文件中。
*   uv pip sync: 使用锁文件同步环境。

重要提示：这些命令和原pip命令不完全一致，具体使用时，需进一步阅读手册。

### 0.3.5 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.5 包管理

#### 0.3.5.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)安装软件包

要将软件包安装到虚拟环境中，例如 Flask：

```undefined
uv pip install flask
```

运行本项目

要安装启用了可选依赖项的包，例如，带有 “dotenv” extra 的 Flask：

```csharp
uv pip install "flask[dotenv]"
```

运行本项目

要安装多个软件包，例如 Flask 和 Ruff：

```undefined
uv pip install flask ruff
```

运行本项目

要安装带有约束的包，例如 Ruff v0.2.0 或更高版本：

```csharp
uv pip install 'ruff>=0.2.0'
```

运行本项目

要从磁盘安装软件包：

```csharp
uv pip install "ruff @ ./projects/ruff"
```

运行本项目

要从 GitHub 安装包：

```csharp
uv pip install "git+https://github.com/astral-sh/ruff"
```

运行本项目

要在特定引用处从 GitHub 安装包，请执行以下作：

```perl
# Install a taguv pip install "git+https://github.com/astral-sh/ruff@v0.2.0" # Install a commituv pip install "git+https://github.com/astral-sh/ruff@1fadefa67b26508cc59cf38e6130bde2243c929d" # Install a branchuv pip install "git+https://github.com/astral-sh/ruff@main"
```

运行本项目

#### 0.3.5.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)从文件安装软件

可以从标准文件格式一次安装多个软件包。

从 requirements.txt 文件安装：

```undefined
uv pip install -r requirements.txt
```

运行本项目

从 pyproject.toml 文件安装：

```undefined
uv pip install -r pyproject.toml
```

运行本项目

#### 0.3.5.3 [](https://blog.csdn.net/2501_90561511/article/details/147746031)卸载软件包

要卸载包，例如 Flask：

```undefined
uv pip uninstall flask
```

运行本项目

要卸载多个软件包，例如 Flask 和 Ruff：

```undefined
uv pip uninstall flask ruff
```

运行本项目

### 0.3.6 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.6 项目

uv 支持管理 Python 项目，这些项目在 pyproject.toml 文件中定义它们的依赖项。

#### 0.3.6.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)创建新项目

您可以使用 uv init 命令创建新的 Python 项目：

```csharp
uv init hello-worldcd hello-world
```

运行本项目

或者，您可以在工作目录中初始化一个项目：

```bash
mkdir hello-worldcd hello-worlduv init
```

运行本项目

UV 将创建以下文件：

> .
> 
> ├── .python-version
> 
> ├── README.md
> 
> ├── main.py
> 
> └── pyproject.toml

main.py 文件包含一个简单的 “Hello world” 程序。用 uv run 试试：

```less
uv run main.py
```

运行本项目

#### 0.3.6.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)项目结构

项目由几个重要的部分组成，这些部分协同工作并允许 uv 管理您的项目。除了 uv init 创建的文件外，uv 在你第一次执行项目命令，即 uv run， uv sync或uv lock时，会在项目根目录中,创建一个虚拟环境和 uv.lock 文件。

完整的列表如下所示：

> .
> 
> ├── .venv
> 
> │ ├── bin
> 
> │ ├── lib
> 
> │ └── pyvenv.cfg
> 
> ├── .python-version
> 
> ├── README.md
> 
> ├── main.py
> 
> ├── pyproject.toml
> 
> └── uv.lock

#### 0.3.6.3 [](https://blog.csdn.net/2501_90561511/article/details/147746031)pyproject.toml 文件

pyproject.toml 包含有关项目的元数据：

pyproject.toml 文件

```cobol
[project]name = "hello-world"version = "0.1.0"description = "Add your description here"readme = "README.md"dependencies = []
```

运行本项目

您将使用此文件指定依赖项，以及有关工程的详细信息，例如其描述或许可证。您可以手动编辑此文件，也可以使用 uv add 和 UV Remove 从终端管理您的项目。

#### 0.3.6.4 [](https://blog.csdn.net/2501_90561511/article/details/147746031)命令一览表：

*   uv init: 创建新的 Python 项目。
*   uv add: 向项目添加依赖。
*   uv remove: 从项目中移除依赖。
*   uv sync: 将项目的依赖与环境同步。
*   uv lock: 为项目的依赖创建锁文件。
*   uv run：在项目环境中运行命令。
*   uv tree：查看项目的依赖树。
*   uv build：将项目构建为分发存档。
*   uv publish：将项目发布到包索引。

### 0.3.7 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.7 工具（tool）

UV 包括一个用于与工具交互的功能。可以使用`uv tool run`在不安装的情况下调用工具，uv提供的uvx命令，和uv tool run 这两个命令完全等效。

如果经常使用某个工具，使用`uv tool install`安装，安装后除非卸载该工具，否则不会删除环境。

#### 0.3.7.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)工作原理

默认情况下，uv 工具目录名为 tools，位于 uv 应用程序目录中，例如 ~/.local/share/uv/tools。

使用uvx/uv tool run 运行工具时，虚拟环境将存储在 uv 缓存目录中，并被视为一次性环境，即，如果运行 uv cache clean，环境将被删除。

使用 uv tool install 安装工具时，将在 uv tools 目录中创建一个虚拟环境。除非卸载该工具，否则不会删除环境。如果手动删除环境，该工具将无法运行。

#### 0.3.7.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)命令一览表

*   `uvx`/`uv tool run` ：在临时环境中运行工具。
*   uv tool install ：全局安装工具。
*   uv tool uninstall ：卸载工具。
*   uv tool list ：列出已安装的工具。
*   uv tool update-shell：更新 shell 以包含工具可执行文件。

### 0.3.8 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.8 构建和发布包

uv 支持通过 uv build 将 Python 包构建到源代码和二进制分发中，并使用 UV publish 将它们上传到注册表。

准备要打包的项目，必须构建 Python 项目才能安装，此过程通常称为 “打包”。Python 项目元数据在 pyproject.toml 文件。

#### 0.3.8.1 [](https://blog.csdn.net/2501_90561511/article/details/147746031)构建包

使用 uv build 构建包：

```undefined
uv build
```

运行本项目

默认情况下，uv build 将在当前目录中构建项目，并将构建的工件放在 dist/ 子目录中。

#### 0.3.8.2 [](https://blog.csdn.net/2501_90561511/article/details/147746031)发布包

使用 uv publish 发布包：

```undefined
uv publish
```

运行本项目

使用 --token 或 UV\_PUBLISH\_TOKEN 设置 PyPI 令牌，PyPI 不再支持使用用户名和密码进行发布，而是需要生成令牌。

#### 0.3.8.3 [](https://blog.csdn.net/2501_90561511/article/details/147746031)安装软件包

测试是否可以使用 uv run 安装和导入包：

```cobol
uv run --with <PACKAGE> --no-project -- python -c "import <PACKAGE>"
```

运行本项目

\--no-project 标志用于避免从本地项目目录安装软件包。

### 0.3.9 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.9 应用

管理和检查 uv 的状态，例如缓存、存储目录或执行自我更新：

*   uv cache clean ：删除缓存条目。
*   uv cache prune：删除过时的缓存条目。
*   uv cache dir：显示 uv 缓存目录路径。
*   uv tool dir：显示 uv 工具目录路径。
*   uv python dir：显示 uv 安装的 Python 版本路径。
*   uv self update ：将 uv 更新到最新版本。

### 0.3.10 [](https://blog.csdn.net/2501_90561511/article/details/147746031)3.10 帮助菜单

\--help 标志可用于查看命令的帮助菜单，例如，对于 uv：

```sql
uv --help
```

运行本项目

要查看特定命令的帮助菜单，例如 uv init：

```csharp
uv init --help
```

运行本项目

使用 --help 标志时，uv 会显示一个精简的帮助菜单。要查看命令的较长帮助菜单，请使用 uv 帮助 ：

```scss
uv help
```

运行本项目

要查看特定命令的长帮助菜单，例如 uv init：

```csharp
uv help init
```

运行本项目

当使用较长的帮助菜单时，uv 将尝试使用更少或更多的 “page” 输出，以便它不会一次全部显示。要退出寻呼机，请按 q。

## 0.4 [](https://blog.csdn.net/2501_90561511/article/details/147746031)四、uvx运行时工具

许多 Python 包都提供了可用作工具的应用程序。UV 具有专门的支持，uvx 命令可以调用工具而不安装，使用`uvx`时，工具会被安装到临时的、隔离的环境中，和uv tool run 这两个命令完全等效。

例如，要运行 ruff：

```undefined
uvx ruff
```

运行本项目

这正是等价的：

```cobol
uv tool run ruff
```

运行本项目

## 0.5 [](https://blog.csdn.net/2501_90561511/article/details/147746031)五、官方参考

官方文档：[https://docs.astral.sh/uv/](https://docs.astral.sh/uv/ "https://docs.astral.sh/uv/")



