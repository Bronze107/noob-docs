# uv 快速入门：常用命令

这是一份给新手的 `uv` 速查文档，适合刚开始接触 Python 包管理、虚拟环境和项目依赖管理的人。

`uv` 很快，也很适合拿来替代一部分你以前用 `pip`、`venv`、`pip-tools` 做的事情。

## 1. uv 是什么

`uv` 是一个现代化的 Python 工具链，常见用途包括：

- 管理 Python 版本
- 创建虚拟环境
- 安装依赖
- 运行 Python 脚本
- 管理项目依赖

如果你以前接触过这些工具：

- `pip`
- `venv`
- `virtualenv`
- `pip-tools`

那可以先简单理解成：

`uv` 想把这些常见操作统一起来，而且速度更快。

## 2. 安装 uv

常见安装方式：

### Windows PowerShell

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### macOS / Linux

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

安装完成后，检查版本：

```bash
uv --version
```

## 3. uv 最常见的几个用途

新手最常遇到的场景一般是：

- 新建一个 Python 项目
- 创建虚拟环境
- 安装依赖
- 运行脚本
- 同步项目依赖

## 4. 创建项目

### 新建一个项目

```bash
uv init myproject
```

进入项目目录：

```bash
cd myproject
```

这个命令通常会帮你初始化一个基础 Python 项目结构。

### 在当前目录初始化项目

```bash
uv init
```

## 5. 创建虚拟环境

### 创建默认虚拟环境

```bash
uv venv
```

通常会在当前目录创建一个 `.venv`。

### 指定 Python 版本创建虚拟环境

```bash
uv venv --python 3.12
```

## 6. 激活虚拟环境

### Windows PowerShell

```powershell
.venv\Scripts\Activate.ps1
```

### Windows CMD

```cmd
.venv\Scripts\activate.bat
```

### macOS / Linux / WSL

```bash
source .venv/bin/activate
```

激活后，终端前面一般会看到类似：

```text
(.venv)
```

### 退出虚拟环境

```bash
deactivate
```

## 7. 安装依赖

### 安装单个包

```bash
uv add requests
```

### 安装多个包

```bash
uv add requests flask pandas
```

### 安装开发依赖

```bash
uv add --dev pytest ruff
```

说明：

- 普通依赖给项目运行使用
- `--dev` 一般用于测试、格式化、检查工具

## 8. 删除依赖

```bash
uv remove requests
```

删除开发依赖也是一样的思路：

```bash
uv remove pytest
```

## 9. 同步依赖

如果项目已经有依赖配置文件，可以同步安装：

```bash
uv sync
```

这个命令常用在：

- 新拉下来的项目
- 依赖变更之后
- 想让本地环境和项目配置保持一致

### 同步开发依赖

```bash
uv sync --dev
```

## 10. 运行 Python 脚本

### 直接运行脚本

```bash
uv run python main.py
```

### 运行模块

```bash
uv run python -m app
```

### 运行测试

```bash
uv run pytest
```

### 运行格式检查

```bash
uv run ruff check .
```

## 11. 安装工具到全局环境

如果你想装一些常用命令行工具，可以用：

```bash
uv tool install ruff
```

例如：

```bash
uv tool install black
uv tool install httpie
```

### 查看已安装工具

```bash
uv tool list
```

### 卸载工具

```bash
uv tool uninstall ruff
```

## 12. Python 版本管理

### 安装 Python

```bash
uv python install 3.12
```

### 查看已安装的 Python

```bash
uv python list
```

### 使用指定 Python 创建环境

```bash
uv venv --python 3.11
```

## 13. 从 requirements.txt 安装依赖

如果你拿到的是老项目，可能会看到 `requirements.txt`。

可以这样安装：

```bash
uv pip install -r requirements.txt
```

如果你只是想临时把 `uv` 当成更快的 `pip` 来用，这个方式很常见。

## 14. 使用 uv pip

`uv` 也兼容一部分 `pip` 风格命令。

### 安装包

```bash
uv pip install requests
```

### 卸载包

```bash
uv pip uninstall requests
```

### 查看已安装包

```bash
uv pip list
```

### 导出依赖

```bash
uv pip freeze
```

## 15. 项目里最常见的一套流程

如果你新建一个项目，最常见流程大概是：

```bash
uv init myproject
cd myproject
uv venv
uv add requests
uv run python main.py
```

如果你拿到一个已有项目，最常见流程大概是：

```bash
cd myproject
uv sync
uv run python main.py
```

## 16. 常见文件怎么理解

在使用 `uv` 的项目里，你常会看到这些文件：

### `pyproject.toml`

项目配置文件，常用来记录：

- 项目名称
- Python 版本要求
- 依赖
- 工具配置

### `uv.lock`

依赖锁定文件，用来保证团队里安装出来的版本尽量一致。

### `.venv`

项目本地虚拟环境目录。

## 17. 常见建议

- 一个项目一个虚拟环境
- 优先使用项目本地的 `.venv`
- 新拉项目优先试 `uv sync`
- 运行命令优先试 `uv run ...`
- 团队项目里不要随便删 `uv.lock`

## 18. 新手最该先记住的命令

```bash
uv --version
uv init myproject
uv venv
uv add requests
uv add --dev pytest ruff
uv remove requests
uv sync
uv run python main.py
uv run pytest
uv tool install ruff
uv python install 3.12
```

## 19. uv 和 pip 怎么选

如果你只是偶尔装一个包，`pip` 也能用。

但如果你想要这些体验：

- 更快的安装速度
- 更统一的项目管理
- 更方便的虚拟环境和依赖同步

那 `uv` 会更顺手。

对新手来说，一个很实用的思路是：

- 老项目兼容 `pip` 时，用 `uv pip`
- 新项目直接用 `uv init`、`uv add`、`uv sync`

## 20. 一句话总结

你可以把 `uv` 理解成一个更现代、更快、更顺手的 Python 开发工具箱。

先记住下面这条主线就够用了：

```bash
uv init
uv venv
uv add 包名
uv sync
uv run python main.py
```
