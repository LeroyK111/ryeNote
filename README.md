# rye python包管理

官网地址：https://rye-up.com/guide/
github： https://github.com/mitsuhiko/rye
本文档主要以windows 平台为主。
其他平台还请自行查看。

为什么选择使用它，而不是用其他的python包管理？
很简单，我相信rust的未来，同时也是作为一个rust开发者的理想。

## 安装方式
https://rye-up.com/guide/installation/
![](readme.assets/Pasted%20image%2020230919092816.png)
配置环境变量
记得如果你的环境中还有其他python的路径，记得要改变path的排列顺序，才能使用rye中的python解释器。
![](readme.assets/Pasted%20image%2020230919093019.png)
 
## 命令
```shell
# 查看
rye --version
# 自我升级
rye self update

# 自我卸载
rye self uninstall

# windows下通过设置自动关闭安装
set RYE_NO_AUTO_INSTALL=1
```

### 创建python项目
```shell
# 初始化一个新python项目
rye init my-project
cd my-project

```
```
# 项目文件夹结构
.
├── .git # git 版本核心文件
├── .gitignore # git 排除文件/文件夹
├── .python-version # python 版本文件
├── README.md # 描述文件
├── pyproject.toml # 包配置文件，详见https://github.com/LeroyK111/python_packing_notes
└── src # 开发目录
    └── my_project # 主程序目录入口
        └── __init__.py # # 每个文件夹的初始化空间

```

### 设置python版本
```shell
# .python-version
rye pin 3.10
```



### 同步虚拟环境
这里会自动下载最新的python解释器， 作为rye默认的python启动器。
这里就是带宽问题了，反正比较慢。
```shell
rye sync
```
![](readme.assets/Pasted%20image%2020230919133249.png)
#### ☆坑
如果你写的项目不是基于最新python解释器，而是其他版本的cpython那么你还需要
等更长时间去下载其他的cpython😂

下图是rye实现成功。
![](readme.assets/Pasted%20image%2020230919163653.png)
为了快，我们就默认使用最新的cpython环境。
#### 激活虚拟环境

```
.venv\Scripts\activate
```

#### 退出虚拟环境
```
deactivate
```

#### 依赖包管理
```shell
# 添加依赖
rye add "flask>=2.0"
# 开发依赖
rye add black --dev 

# 删除依赖
rye remove flask

# 安装依赖
rye sync
```


##### 同步和锁定

###### 锁定包的更新
```shell
# 将特定或所有要求更新到最新和最好的版本。如果没有此标志，则仅在必要时更新依赖项。
rye lock --update-all
# 只有pandas需要更新
rye lock --update pandas
```

###### 锁定包的额外依赖
```shell
# 可选依赖
rye add --optional=web flask 
# 注册额外依赖
rye lock --features=web
# 使用工作区时，额外命名
rye lock --features=package-name/feature-name
# 打开所有功能
rye lock --all-features
```

###### 预发布版本锁定
```shell
rye lock Flask --pre
```

###### 同步
```shell
# 要防止锁定步骤自动运行
rye sync --no-lock

# 仅基于生产锁文件 ( `requirements.lock`) 而不是开发锁文件 ( `requirements-dev.lock`) 进行同步。
rye sync --no-dev
```


#### 全局工具安装
将工具安装到 中`~/.rye/tools/ruff`，并将必要的垫片放入 中`~/.rye/shims`。
使用`rye tools install`（别名为`rye install`）命令通过填充程序全局安装工具：
```
$ rye install pycowsay
$ pycowsay Wow

  ---
< Wow >
  ---
   \   ^__^
    \  (oo)\_______
       (__)\       )\/\
           ||----w |
           ||     ||
$ rye uninstall pycowsay
```

##### 额外要求

某些工具不会声明其所有依赖项，因为它们可能是可选的。在某些情况下，可以通过将额外功能传递给安装程序来声明这些：

```shell
rye install black --features colorama
```


如果根本没有指定依赖项，则可以通过`--extra-requirement`. 如果工具使用`pkg_resources`（的一部分 `setuptools`）但忘记声明该依赖项，有时这尤其必要：

```shell
rye install gradio --extra-requirement setuptools
```


##### 展示工具

如果您想查看安装了哪些工具，可以使用`rye tools list`：

```shell
rye tools list
```

要查看这些工具提供的脚本，还可以通过`--include-scripts`

```shell
rye tools list --include-scripts
```
##### 卸载工具

要再次卸载工具，请使用`rye tools uninstall`（别名为`rye uninstall`）：

```
rye uninstall black
```
#### 执行脚本
```shell
rye run key
```

##### 报错
如果你发生如下报错，说明是python环境发生了冲突。
注释掉环境中的其他python，并且使用纯英文路径。

![](readme.assets/Pasted%20image%2020230919170957.png)
这里问题是老问题了。毕竟是下一代包管理器。



## rye配置目录
windows环境下：
```shell
# 用户目录下 .rye/ 文件夹
# %USERPROFILE%\.rye

# .rye/ 文件夹结构
.
├── py # python解释器版本存放处
├── self # 虽然 Rye 是用 Rust 编写的，但它内部使用了很多 Python 工具。
└── shims # tools 可执行
└── config.toml # 默认是需要自己创建的。
```
#### 默认配置 config.toml
```toml
[default]
#这是写入new pyproject.toml的默认值项目文件。requires-python的关键
# requires-python = ">= 3.8"

# 这是使用的默认工具链
# toolchain = "cpython@3.8.17"

# 这是使用的默认构建系统
# build-system = "hatchling"

# 这是遵从的开源协议
# license = "MIT"

# 这会设置默认的作者(覆盖git的默认值)。
# "Name <email>"

# author = "{ name = 'LeroyK', email = '785632486@qq.com' }"

# 依赖选项.  The options are
# '>=', '~=', and '=='.  The default currently is '>='.  This affects the behavior
# of `rye add`.

# dependency-operator = ">="

[proxy]
# 用于HTTP的代理(由http_proxy环境变量覆盖)
# http = "http://127.0.0.1:4000"
# the proxy to use for HTTPS (overridden by the https_proxy environment variable)
# https = "http://127.0.0.1:4000"

[behavior]
# 当设置为true时，' managed '标志总是被假定为true。
# force-rye-managed = false

#当设置为“true”时启用全局垫片。这意味着安装' python ' shim将解析为一个Rye管理的工具链，甚至在外部虚拟环境。
# global-python = false

# 具有可选源的表数组。格式与pyproject.toml相同
# [[sources]]
# name = "default"
# url = "http://pypi.org/simple/"

```

可以使用 读取和修改配置`rye config`。
键以点符号表示。
`--get`读取一个键、`--set`、`--set-int`、 `--set-bool`、 或`--unset`修改一个键。
```shell
rye config --set proxy.http=http://127.0.0.1:4000 
rye config --set-bool behavior.rye-force-managed=true 
rye config --get default.requires-python
rye config --set-bool behavior.global-python=true
```



## 构建和发布
Rye 目前使用[build](https://github.com/pypa/build)来构建包并使用[twine](https://github.com/pypa/twine)来发布它。

默认情况下，`rye`将在目录中构建 sdist 和wheel 目标`dist`。

### 构建轮子or源码
```shell
rye build
```

您可以使用`--sdist`或`--wheel`标志来构建特定目标，或使用 指定输出目录`--out`。
```shell
rye build --wheel --out target
```

如果您想在构建之前清理构建目录，请运行：
```shell
rye build --clean
```


### 发布
`dist`Rye默认会将该目录下的分发文件发布到PyPI中。
```shell
rye publish
```

如果需要，可能会要求您输入访问令牌和一些其他信息。
```shell
No access token found, generate one at: https://pypi.org/manage/account/token/ Access token:
```

您还可以指定要发布的分发文件：
```shell
rye publish dist/example-0.1.0.tar.gz
```

`--repository`Rye 支持使用和标志将包发布到不同的存储库`--repository-url`。例如，要发布到测试 PyPI 存储库：
```shell
rye publish --repository testpypi --repository-url https://test.pypi.org/legacy/
```

您可以选择设置`--yes`标志以跳过确认提示。这对于 CI/CD 管道非常有用。

```shell
rye publish --token <your_token> --yes
```
Rye 将存储您的存储库信息`$HOME/.rye/credentials`以供将来使用。


## 导入Rust模块
Rye 建议使用[maturin](https://github.com/PyO3/maturin)开发 Rust Python 扩展模块。这个过程很大程度上是自动化的，并且可以使用创建新项目`rye init`。

新项目
```shell
rye init my-project --build-system maturin
cd maturin
```

将创建以下结构：
```
.
├── .git
├── .gitignore
├── .python-version
├── README.md
├── pyproject.toml
├── Cargo.toml
├── python
    └── my_project
        └── __init__.py
└── src
    └── lib.rs
```

迭代
当您使用 maturin 作为构建系统时，rye sync将自动将 rust 扩展模块构建到您的 venv 中。同样，rye build将使用 maturin 来触发轮子构建。为了更快的迭代，建议maturin直接使用。
如果您想使用其他 maturin 命令，例如maturin develop可以将其安装为全局工具：
```shell
rye install maturin
```

请注意，maturin develop需要pip安装到 virtualenv 中。在使用它之前，您需要添加它：

```shell
rye add --dev pip
rye sync
```

Rye 建议混合使用 python/rust 模块。在这种情况下，您可以通过运行以下命令来节省一些宝贵的迭代时间maturin develop --skip-install：

```shell
maturin develop --skip-install
```

rust和python相互粘合。

## ☆依赖关系

### 基本依赖
要添加常规依赖项，只需`rye add`使用 Python 包的名称进行调用：
```shell
rye add Flask
```


如果您还想定义版本，请使用[PEP 508](https://peps.python.org/pep-0508/) 要求：
```shell
rye add "Flask>=2.0"
```

对于额外/功能依赖项，您可以使用 PEP 508 语法或使用`--features`：

```shell
rye add "Flask[dotenv]" rye add Flask --features=dotenv
```


这些依赖项存储在[`project.dependencies`](https://rye-up.com/guide/pyproject/#projectdependencies).

关于预发布的注意事项

默认情况下`add`不会考虑预发布。这意味着如果您添加版本号具有`.dev`或相似的依赖项，您将找不到匹配项。要考虑它们，请将它们添加为`--pre`：

```shell
rye add "Flask==2.0.0rc2" --pre
```
### 开发依赖
对于仅应在开发过程中安装的依赖项--dev
```shell
rye add --dev black
```


这些依赖关系存储在非标准 tool.rye.dev-dependencies密钥中。

要运行以这种方式添加的工具而不启用 virtualenv，请使用rye run：

```shell
rye run black
```


### Git/本地依赖项
要添加本地或 git 依赖项，您可以传递其他参数，例如--path 或--git：

```shell
rye add Flask --git=https://github.com/pallets/flask
rye add My-Utility --path ./my-utility
```
请注意，添加此类依赖项时，还必须提供包的名称。此外，对于 git 依赖项，还支持各种额外参数，例如--tag,--rev或。--branch

当使用本地依赖项时，强烈建议配置 工作区。

## ★工具链管理

Rye 的独特之处在于它不使用系统 Python 安装。相反，如果下载并管理 Python 安装本身（称为工具链）。目前，Rye 支持三种类型的工具链，需要对它们进行一些了解：

- <span style="color: red">
可移植的 CPython：Rye 会自行下载可移植的 CPython 版本来满足其大部分需求。这些是从 indygreg/python-build-standalone获取的
</span>

- 官方 PyPy 版本：官方发布版本支持 PyPy。

- <span style="color: green">
自定义本地工具链：可以在 Rye 中注册本地安装的 Python 解释器。之后它们可以与任何 Rye 管理的项目一起使用。
</span>

### 固定工具链
要使项目使用特定的工具链，请将工具链的名称写入文件 .python-version或使用pin命令。对于固定， 可以省略前缀cpython。cpython@

```shell
rye pin cpython@3.11.4
```


固定可下载版本意味着 Rye 将在必要时自动获取它。默认情况下，工具链固定到精确版本。这意味着即使您编写rye pin cpython@3.11，也会将一个非常特定的 cpython 版本写入该 .python-version文件中。从 Rye 0.5.0 开始，可以执行“宽松”的引脚：

```shell
rye pin --relaxed cpython@3.11
```


然后，这将保留3.11在.python-version文件中，并且 Rye 将使用虚拟环境的最新可用兼容版本。

0.5.0中更改

添加了轻松固定rye pin --relaxed。

### 列出工具链
要查看安装了哪些工具链，rye toolchain list请打印列表：

```shell
rye toolchain list
```

cpython@3.11.1 (C:\Users\armin\.rye\py\cpython@3.11.1\install\python.exe)
pypy@3.9.16 (C:\Users\armin\.rye\py\pypy@3.9.16\python.exe)
要查看可以安装哪些工具链，请另外传递--include-downloadable：

```shell
rye toolchain list --include-downloadable
```
### 获取工具链
通常，Rye 会自动下载工具链，但可以使用以下命令显式获取它们rye toolchain fetch（也别名为rye fetch）：

```shell
rye toolchain fetch cpython@3.8.5
```

工具链从两个来源获取：

Indygreg 的可移植 Python为 CPython构建
PyPy.org用于 PyPy

### 注册工具链
此外，还可以使用该rye toolchain register 命令注册外部工具链。

rye toolchain register /path/to/python
工具链的名称是根据解释器选择的。例如，链接常规 cpython 安装将被称为cpython@version，而链接 pypy 将显示为pypy@version。从 Rye 0.5.0 开始，-dbg如果是调试版本，则会附加到工具链的名称中。要覆盖您可以传递的名称--name：

```shell
rye toolchain register --name=custom /path/to/python
```

### 删除工具链
要删除已获取的工具链，请运行rye toolchain remove. 请注意，这也适用于链接的工具链：

```shell
rye toolchain remove cpython@3.8.5
```

警告删除活跃使用的工具链将导致引用 use 的 virtualenv 被破坏。
 