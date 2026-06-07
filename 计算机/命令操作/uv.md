### 1. 项目初始化与管理
* `uv init`                    # 在当前目录初始化一个新的 Python 项目
* `uv add <package>`           # 添加并安装依赖，自动更新 pyproject.toml
* `uv remove <package>`        # 移除依赖
* `uv sync`                    # 强制同步环境（根据 lock 文件安装/删除包）
* `uv lock`                    # 仅更新 uv.lock 文件而不安装包

### 2. 运行与脚本执行
* `uv run <script.py>`         # 在项目环境下运行脚本（自动管理虚拟环境）
* `uv run python`              # 进入当前项目的 Python 交互环境
* `uv run --with <package> <script.py>` # 临时为一个脚本添加依赖并运行

### 3. 虚拟环境管理 (底层操作)
* `uv venv`                    # 创建默认虚拟环境 (.venv)
* `uv venv --python 3.12`      # 指定 Python 版本创建虚拟环境
* `source .venv/bin/activate`  # 激活环境 (macOS/Linux)
* `uv pip install -r req.txt`  # 使用 uv 的高速引擎通过 pip 协议安装依赖
* `uv pip list`                # 查看当前环境下安装的所有包

### 4. Python 版本管理 (替代 pyenv)
* `uv python list`             # 查看可安装和已安装的 Python 版本
* `uv python install 3.13`     # 下载并安装指定的 Python 版本
* `uv python pin 3.11`         # 将当前项目固定在特定版本

### 5. 工具管理 (替代 pipx)
* `uv tool install <tool>`     # 全局安装命令行工具（如 ruff, black, bypass）
* `uv tool list`               # 列出所有全局安装的工具
* `uv tool uninstall <tool>`   # 卸载工具
* `uvx <tool>`                 # 一次性运行某个工具（免安装，运行完即销毁）

### 6. 缓存与维护
* `uv cache clean`             # 清理缓存，释放磁盘空间
* `uv cache dir`               # 查看缓存存储路径
* `uv self update`             # 升级 uv 软件本身