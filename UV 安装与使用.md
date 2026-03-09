
# 1 uv 工具安装与使用
## 1.1 核心优势

### 1.1.1 🚀 极致性能

*   基于 Rust 实现，依赖解析和包安装速度显著提升
*   并行下载和安装，充分利用系统资源
*   智能缓存机制，避免重复下载

### 1.1.2 🛠️ 统一工具链

*   一个工具解决多个需求：项目创建、环境管理、依赖安装
*   标准化的项目结构和配置文件
*   与现有 Python 生态系统完全兼容

### 1.1.3 📦 现代化依赖管理

*   精确的版本锁定，确保跨平台一致性
*   支持开发/生产环境依赖分离
*   自动处理依赖冲突和版本兼容性


## 1.2 **uv** 工具安装

### 1.2.1 官方仓库

[astral-sh/uv: An extremely fast Python package and project manager, written in Rust.](https://github.com/astral-sh/uv)

![](attach/Pasted%20image%2020250903113514.png)

### 1.2.2 安装指令

```python
curl -LsSf https://astral.sh/uv/install.sh | sh
```

![](attach/Pasted%20image%2020250901113106.png)

## 1.3 **uv** 工具使用注意事项

>[!info] **注意事项** 
>- 安装 **uv** 后 **必须重启当前终端**，否则无法识别新命令
>- **uv** 的虚拟环境不支持多版本 **Python**（与 **Conda** 不同）
>- 可通过 **Conda** 提供的 **Python** 版本配合使用
>```bash
># 验证 uv 安装
uv --version
>```

## 1.4 创建 Conda 环境（为 **uv** 提供不同 Python）版本

```python
# 创建 Python 3.9 环境
conda create -n py39 python=3.9

# 创建 Python 3.11 环境
conda create -n py311 python=3.11

# 创建 Python 3.12 环境
conda create -n py312 python=3.12
```

![](attach/Pasted%20image%2020250902153009.png)

## 1.5 禁止 Conda 自动激活环境（关键配置）

```python
# 防止 Conda 与 uv 虚拟环境冲突
conda config --set auto_activate_base false
```

---

## 1.6 使用 **uv** 创建虚拟环境的完整流程


>[!example] **重要注意事项** ⚠️
>- 禁止同时激活 **Conda** 和 **uv** 环境
> 
>```
两者环境隔离机制冲突，必须严格遵循「 先激活 Conda → 创建 uv 环境 → 退出 Conda 」的顺序
>```
>
>- **环境路径确认**
>```
uv venv 生成的虚拟环境路径默认为当前目录下的 .venv 可通过 --venv 参数自定义路径
>```


### 1.6.1 **uv** 虚拟环境使用流程

---

```timeline-labeled
[line-4, body-2]（不同的列表样式，事件点样式，换数字即可）

date: 第一步
title: 激活指定 **Python** 版本的 **Conda** 环境
content: >**`conda activate py311`**

**`py311`** 为先前使用 **Conda** 创建好的 **`Python 3.11`** 虚拟环境

date: 第二步
title: 使用当前激活的 **`py311`** 创建 **uv** 虚拟环境
content:>**`uv venv --seed -p 3.11`**

创建好虚拟环境之后，当前目录会生成 **`.venv`** 文件夹，存放该虚拟环境的**相关配置**

![](attach/Pasted%20image%2020250901120609.png)

date: 第三步
title: 退出 **Conda** 环境
content:>**`conda deactivate`**

注意：需要完全退出 **Conda** 的虚拟环境！！

date: 第四步
title: 激活 **uv** 创建的虚拟环境
content:>**`source ./.venv/bin/activate`**

注意：路径需根据 **实际环境** 调整，将路径调整到与 `.venv` 同级目录下


date: 第五步
title: 安装项目运行所需的库
content:>**`uv pip install xxxx`**
>**`uv pip install -r requirements.txt`**


date: 第六步
title: 运行项目
content:>**`python main.py`**

此时，**uv** 的虚拟环境已经激活，可直接使用 **`python`** 来运行项目

>**`uv run hello.py`**

使用 **uv** 运行脚本（自动激活虚拟环境）
```

---

### 1.6.2 **uv** 虚拟环境激活状态验证

```bash
# 激活虚拟环境后验证
source .venv/bin/activate

# 1. 检查 Python 路径
which python
# 应该输出：/path_to_your_project/.venv/bin/python

# 2. 检查 pip 路径
which pip
# 应该输出：/path_to_your_project/.venv/bin/pip

# 3. 查看已安装的包
pip list

# 4. 验证 Python 版本
python --version

# 5. 检查环境变量
echo $VIRTUAL_ENV
# 应该输出：/path_to_your_project/.venv
```


### 1.6.3 可能遇到的问题

创建虚拟环境时，出现下列的**警告**，涉及到：

![](attach/Pasted%20image%2020250901120021.png)

- **`Failed to hardlink files`**  
    `uv` 尝试使用 **硬链接（hardlink）** 将缓存中的文件直接链接到目标路径（如虚拟环境的 `site-packages` 目录），但操作失败。
    
- **`falling back to full copy`**  
    硬链接失败后，`uv` 回退到 **全量复制文件**（即直接复制文件内容），这可能导致性能下降（尤其是处理大量文件时）。
    
- **`different filesystems`**  
    硬链接通常要求源文件和目标路径位于 **同一文件系统（分区）**。如果 `uv` 的缓存目录（默认在 `C:\Users\用户名\AppData\Local\uv\cache`）和你的项目目录在 **不同分区**，硬链接会失败。

### 1.6.4 解决方式

```python
vim /root/.bashrc
```

- 添加下面的指令

```bash
export UV_CACHE_DIR=/workspace/uv_CACHE_LOCK
```

![](attach/Pasted%20image%2020250901115735.png)

- 在指定的目录下创建虚拟环境的配置，生成 `.venv` 文件，警告去除！！

![](attach/Pasted%20image%2020250901120322.png)

## 1.7 **uv** 设置镜像源

>[!note] **uv 设置清华源步骤**
>- **清华源**
>```python
https://pypi.tuna.tsinghua.edu.cn/simple
>```
>
>- **Linux/macOS** 配置文件路径：`~/.config/uv/uv.toml`  
>```shell
vim ~/.config/uv/uv.toml
>```
>    - 添加以下**内容**：
>```python
[[index]]
url = "https://pypi.tuna.tsinghua.edu.cn/simple"
default = true
>```

## 1.8 项目管理流程

### 1.8.1 创建新项目

```bash
# 初始化项目
uv init my-python-project
cd my-python-project

# 查看生成的文件结构
ls -la
```

uv 会自动创建以下文件：

*   `pyproject.toml` - 项目配置和依赖声明
*   `README.md` - 项目说明文档
*   `.gitignore` - Git 忽略规则
*   `hello.py` - 示例代码文件

### 1.8.2 环境同步

```bash
# 创建虚拟环境并安装依赖
uv sync
```

这个命令会：

1.  检测合适的 Python 版本
2.  创建项目专用的虚拟环境（`.venv` 目录）
3.  生成锁定文件 `uv.lock`
4.  安装所有声明的依赖

### 1.8.3 运行代码

```bash
# 使用 uv 运行脚本（自动激活虚拟环境）
uv run hello.py

# 或者激活环境后运行
source .venv/bin/activate  # Linux/macOS
python hello.py
```

### 1.8.4 常用工作流程

#### 1.8.4.1 开发工作流程

```bash
# 1. 进入项目目录
cd my-python-project

# 2. 同步依赖（如果需要）
uv sync

# 3. 激活虚拟环境
source .venv/bin/activate

# 4. 开发工作
python script.py
pip install some-package  # 临时安装包测试
python -m pytest         # 运行测试

# 5. 退出环境
deactivate
```

#### 1.8.4.2 IDE 集成工作流程

```bash
# VS Code 示例
# 1. 激活环境
source .venv/bin/activate

# 2. 启动 VS Code（会自动检测虚拟环境）
code .

# 3. 在 VS Code 中：
# - Ctrl+Shift+P -> "Python: Select Interpreter"
# - 选择 .venv/bin/python

# PyCharm 示例
# 1. 打开项目
# 2. File -> Settings -> Project -> Python Interpreter
# 3. 添加已存在的环境：选择 .venv/bin/python
```

### 1.8.5 环境隔离验证

```bash
# 在项目外测试环境隔离
cd /tmp

# 1. 未激活环境时
python -c "import sys; print(sys.path)"

# 2. 激活项目环境
source /path_to_your_project/.venv/bin/activate

# 3. 激活后再次检查
python -c "import sys; print(sys.path)"
# 应该看到 .venv 路径在前面

# 4. 安装包测试隔离性
pip install some-test-package
pip list  # 只在虚拟环境中可见

# 5. 退出环境
deactivate

# 6. 再次检查（包应该不可见）
python -c "import some_test_package"  # 应该报错
```

## 1.9 Python 版本管理（可选）

>[!abstract] **Error description**
在前面的步骤中，我们使用 **Conda** 来获取 **Python** 版本，而 **uv** 同样自带了强大的 **Python** 版本管理功能，可以自动下载、安装和管理不同版本的 Python，无需手动安装或使用其他工具。因此此节可根据需要**来折腾！！！**

### 1.9.1 安装 Python 版本

#### 1.9.1.1 基本安装命令

```bash
# 安装特定版本的 Python
uv python install 3.12

# 安装多个版本
uv python install 3.11 3.12 3.13

# 安装最新版本
uv python install 3.12.latest

# 安装预览版本
uv python install 3.13.0b4
```

#### 1.9.1.2 查看可用版本

```bash
# 列出所有可安装的 Python 版本
uv python list --all

# 查看已安装的版本
uv python list

# 查看当前使用的版本
uv python find
```

#### 1.9.1.3 安装示例

>[!example] **详细安装过程示例**
>- `uv python install 3.12`
>```shell
Searching for Python installations Installed Python 3.12.7 in 2.5s + cpython-3.12.7-linux-x86_64-gnu
>```
>- `uv python list`
>```python
cpython-3.11.9-linux-x86_64-gnu   /home/user/.local/share/uv/python/cpython-3.11.9-linux-x86_64-gnu/bin/python3
cpython-3.12.7-linux-x86_64-gnu   /home/user/.local/share/uv/python/cpython-3.12.7-linux-x86_64-gnu/bin/python3
>```

### 1.9.2 创建指定版本的虚拟环境

#### 1.9.2.1 使用 uv venv 创建环境

```bash
# 创建使用 Python 3.12 的虚拟环境
uv venv --python 3.12

# 指定虚拟环境名称
uv venv myenv --python 3.12

# 创建在指定路径
uv venv /path/to/venv --python 3.12

# 使用具体的版本号
uv venv --python 3.12.7
```

#### 1.9.2.2 项目中指定 Python 版本

```bash
# 方法一：在创建项目时指定版本
uv init my-project --python 3.12

# 方法二：在现有项目中设置版本
echo "3.12" > .python-version
uv sync

# 方法三：在 pyproject.toml 中指定
# 编辑 pyproject.toml 文件
```

在 `pyproject.toml` 中配置：

```toml
[project]
requires-python = ">=3.12,<3.13"

# 或者更灵活的版本要求
requires-python = ">=3.11"
```

### 1.9.3 实际使用流程

#### 1.9.3.1 完整的项目创建流程

```bash
# 1. 确保已安装所需的 Python 版本
uv python install 3.12

# 2. 创建项目并指定 Python 版本
uv init my-web-app --python 3.12
cd my-web-app

# 3. 查看自动生成的版本配置
cat .python-version  # 输出: 3.12

# 4. 同步环境（会使用指定的 Python 版本）
uv sync

# 5. 验证 Python 版本
uv run python --version  # 输出: Python 3.12.x
```

#### 1.9.3.2 切换现有项目的 Python 版本

```bash
# 当前项目使用 Python 3.11，想切换到 3.12

# 1. 安装新版本（如果未安装）
uv python install 3.12

# 2. 更新版本文件
echo "3.12" > .python-version

# 3. 删除旧环境
rm -rf .venv

# 4. 重新创建环境
uv sync

# 5. 验证版本
uv run python --version
```



> [!success] **~ SUCCESS ~**
>**`uv` 安装与使用教程到此结束！！！！！！**






ahoawf
woefnqowihfqw


fqwefqw
ef
qwfqweihfqowefhohwfq
fqwfekhqwefih;osfqwf
a;fwenmf
kjk


