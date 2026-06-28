# uv 命令大全

uv 是极速的 Python 包与项目管理器，由 Rust 编写，可替代 pip、pip-tools、poetry、pyenv 等。以下覆盖常用命令，标注常用选项。

## 全局选项

| 选项 | 说明 |
|------|------|
| `--verbose, -v` | 详细输出 |
| `--quiet, -q` | 只输出错误 |
| `--no-cache, -n` | 不使用缓存 |
| `--color <WHEN>` | 颜色控制：auto, always, never |
| `--native-tls` / `--no-native-tls` | 是否使用系统 TLS |

---

## 项目管理

### `uv init` – 初始化项目
```bash
uv init [PATH] [OPTIONS]
```
- 在当前或指定目录生成 `pyproject.toml`、`.python-version` 等。
- `--name <NAME>` – 项目名称
- `--package` – 创建可打包项目（含 `src/` 和 `__init__.py`）
- `--no-readme` – 不生成 README
- `--python <PYTHON>` – 指定 Python 版本或路径

### `uv add` – 添加依赖
```bash
uv add <PACKAGE>... [OPTIONS]
```
- 安装包并写入 `pyproject.toml`。
- `--dev` – 已弃用，请用 `--group dev`
- `--group <GROUP>` – 添加到指定依赖组
- `--optional <EXTRAS>` – 可选依赖
- `--raw-sources` – 使用原始源声明
- `--rev` / `--tag` / `--branch` – Git 引用
- `--path <PATH>` – 本地路径
- `--editable` – 可编辑安装

示例：
```bash
uv add requests
uv add --group dev pytest
uv add git+https://github.com/encode/httpx.git
```

### `uv remove` – 移除依赖
```bash
uv remove <PACKAGE>... [OPTIONS]
```
- 卸载包并从 `pyproject.toml` 删除。
- `--group <GROUP>` – 从指定组移除
- `--dev` – 等同 `--group dev`

### `uv sync` – 同步环境
```bash
uv sync [OPTIONS]
```
- 根据 `pyproject.toml` 和 `uv.lock` 安装/移除包，保证环境完全匹配。
- `--group <GROUP>` – 包含特定依赖组
- `--all-groups` – 包含所有依赖组
- `--no-dev` – 不安装 dev 依赖
- `--inexact` – 不要求精确匹配锁文件
- `--frozen` – 不更新锁文件，仅同步

### `uv lock` – 锁定依赖
```bash
uv lock [OPTIONS]
```
- 解析依赖并更新 `uv.lock`。
- `--upgrade` / `-U` – 升级所有依赖
- `--upgrade-package <PKG>` – 仅升级特定包
- `--resolution <STRATEGY>` – lowest, highest, lowest-direct

### `uv run` – 运行命令
```bash
uv run [OPTIONS] <COMMAND>
```
- 在项目环境中执行命令（无需手动激活 venv）。
- `--with <PKG>` – 临时添加包后执行
- `--isolated` – 忽略项目环境，使用隔离环境
- 示例：`uv run python main.py`、`uv run pytest`

### `uv tree` – 依赖树
```bash
uv tree [OPTIONS]
```
- `--depth <N>` – 显示深度
- `--prune <PKG>` – 剪枝某个包
- `--outdated` – 显示可升级版本
- `--no-dev` – 不显示 dev 依赖

### `uv build` – 构建分发包
```bash
uv build [SRC] [OPTIONS]
```
- 构建 sdist 和 wheel，输出到 `dist/`。
- `--package <PKG>` – 指定包名
- `--wheel` / `--sdist` – 仅构建指定格式
- `--out-dir <DIR>` – 输出目录

### `uv export` – 导出依赖
```bash
uv export [OPTIONS]
```
- 导出为 `requirements.txt` 等格式。
- `--format <FMT>` – requirements-txt (默认)
- `--group <GROUP>` – 指定包含的组
- `--no-dev` – 排除开发依赖
- `--no-hashes` – 不包含哈希

---

## Python 版本管理

### `uv python install` – 安装 Python
```bash
uv python install <VERSION>... [OPTIONS]
```
- 安装指定 Python 版本，如 `3.12`、`3.11.5`。
- `--reinstall` – 重新安装
- `--default` – 设为默认 Python

### `uv python list` – 列出 Python
```bash
uv python list [OPTIONS]
```
- 列出已安装和可下载的 Python。
- `--only-installed` – 仅显示已安装
- `--all-versions` – 包含预发布、已终止支持版本

### `uv python find` – 查找 Python
```bash
uv python find [VERSION] [OPTIONS]
```
- 查找匹配的 Python 解释器路径。
- `--no-project` – 不在项目环境中查找

### `uv python pin` – 固定 Python 版本
```bash
uv python pin <VERSION>
```
- 创建或更新 `.python-version` 文件。

### `uv python uninstall` – 卸载 Python
```bash
uv python uninstall <VERSION>...
```

---

## 虚拟环境

### `uv venv` – 创建虚拟环境
```bash
uv venv [PATH] [OPTIONS]
```
- 默认创建 `.venv`。
- `--python <PYTHON>` – 指定 Python
- `--prompt <PROMPT>` – 环境提示名
- `--system-site-packages` – 允许使用系统包
- `--seed` – 预装 pip、setuptools

---

## 工具管理

### `uv tool install` – 安装全局工具
```bash
uv tool install <PACKAGE> [OPTIONS]
```
- `--from <PKG>` – 从哪个包安装命令
- `--with <EXTRAS>` – 附加依赖
- `--python <PYTHON>` – 指定 Python
- `--force` – 覆盖已存在安装

### `uv tool run` / `uvx` – 一次性运行工具
```bash
uv tool run <PACKAGE> [OPTIONS] [-- <ARGS>...]
uvx <PACKAGE> [OPTIONS] [-- <ARGS>...]
```
- 临时下载并运行，不永久安装。
- 示例：`uvx ruff check .`、`uvx --with pandas ipython`

### `uv tool list` – 列出已安装工具
```bash
uv tool list
```

### `uv tool uninstall` – 卸载工具
```bash
uv tool uninstall <PACKAGE>...
```

### `uv tool update` – 更新工具
```bash
uv tool update <PACKAGE>...
```

### `uv tool dir` – 显示工具环境目录
```bash
uv tool dir [--bin]
```

---

## pip 兼容接口

### `uv pip install` – 安装包
```bash
uv pip install <PACKAGE>... [OPTIONS]
```
- 兼容 pip 工作流，需在 venv 中使用。
- `-r <FILE>` – 从 requirements 文件安装
- `-e <PATH>` – 可编辑安装
- `--index-url` / `--extra-index-url`
- `--constraint <FILE>` – 约束文件
- `--reinstall` / `--no-deps`
- `--target <DIR>` – 安装到指定目录

### `uv pip compile` – 编译锁定依赖
```bash
uv pip compile <FILE>... [OPTIONS]
```
- 将 `requirements.in` 等编译成锁定格式。
- `--output-file <FILE>`, `-o` – 输出文件
- `--generate-hashes` – 生成哈希
- `--upgrade` / `--upgrade-package`
- `--resolution <STRATEGY>`

### `uv pip sync` – 同步环境
```bash
uv pip sync <FILE>... [OPTIONS]
```
- 精确匹配 requirements 文件，移除多余包。
- `--reinstall` – 重装所有包

### `uv pip list` – 列出包
```bash
uv pip list [OPTIONS]
```
- `--editable` – 仅可编辑包
- `--outdated` – 显示可升级
- `--format <FMT>` – columns, freeze, json

### `uv pip show` – 显示包信息
```bash
uv pip show <PACKAGE>...
```

### `uv pip uninstall` – 卸载包
```bash
uv pip uninstall <PACKAGE>... [OPTIONS]
```
- `-r <FILE>` – 从文件卸载

### `uv pip freeze` – 导出已安装
```bash
uv pip freeze [OPTIONS]
```
- `--path <DIR>` – 指定环境路径

### `uv pip check` – 检查依赖冲突
```bash
uv pip check
```

---

## 发布与其它

### `uv publish` – 发布到索引
```bash
uv publish [FILES] [OPTIONS]
```
- 发布构建好的包到 PyPI 等。
- `--publish-url <URL>` – 仓库地址
- `--token <TOKEN>` – 认证令牌
- `--username` / `--password`
- `--skip-existing` – 跳过已存在版本

### `uv cache clean` – 清理缓存
```bash
uv cache clean [PACKAGE]...
```
- 清理所有或指定包的缓存。

### `uv cache dir` – 显示缓存目录
```bash
uv cache dir
```

### `uv self update` – 更新 uv
```bash
uv self update [VERSION]
```
- 升级到最新或指定版本。

### `uv version` – 显示版本
```bash
uv version
```

### `uv help` – 帮助
```bash
uv help [COMMAND]
```
---
# 环境设置
### 1. 索引 / 源
| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_INDEX_URL` | 主索引 URL（如 PyPI 镜像） | `https://pypi.org/simple/` |
| `UV_EXTRA_INDEX_URL` | 额外索引，多个用空格分隔 | 无 |
| `UV_INDEX_STRATEGY` | 跨索引选择策略：`first-index`(先找到即用) 或 `unsafe-best-match`(找最高版本) | `first-index` |
| `UV_NO_INDEX` | 设为 `1` 禁用所有索引（仅用本地或直接依赖） | 无 |

**示例**（使用清华镜像）：
```bash
export UV_INDEX_URL=https://pypi.tuna.tsinghua.edu.cn/simple
```

---

### 2. 缓存
| 变量             | 说明          | 默认值                         |
| -------------- | ----------- | --------------------------- |
| `UV_CACHE_DIR` | 缓存目录路径      | 平台默认（如 Linux `~/.cache/uv`） |
| `UV_NO_CACHE`  | 设为 `1` 禁用缓存 | `0`                         |

---

### 3. 并发控制
| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_CONCURRENT_DOWNLOADS` | 同时下载的最大数量 | `50` |
| `UV_CONCURRENT_BUILDS` | 同时构建的最大数量 | CPU 核心数 |
| `UV_CONCURRENT_INSTALLS` | 同时安装的最大数量 | CPU 核心数 |

---

### 4. 网络与离线
| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_NATIVE_TLS` | 设为 `1` 强制使用系统 TLS（不走 uv 内置的 rustls） | `0` |
| `UV_HTTP_TIMEOUT` | HTTP 请求超时秒数 | `30` |
| `UV_OFFLINE` | 设为 `1` 离线模式，不访问网络 | `0` |

---

### 5. Python 解释器
| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_PYTHON` | 指定 Python 解释器路径 | 自动查找 |
| `UV_PYTHON_PREFERENCE` | 解释器偏好：`only-managed` / `managed` / `system` / `only-system` | `managed` |
| `UV_PYTHON_INSTALL_DIR` | uv 托管 Python 的安装目录 | 平台默认 |
| `UV_PYTHON_DOWNLOADS` | 托管 Python 下载策略：`never` / `automatic` / `manual` | `automatic` |

---

### 6. 解析与锁定
| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_RESOLUTION` | 依赖解析策略：`highest` / `lowest` / `lowest-direct` | `highest` |
| `UV_LOCKED` | 设为 `1` 要求 `uv.lock` 文件为最新 | `0` |
| `UV_FROZEN` | 设为 `1` 不更新 `uv.lock`，直接安装 | `0` |
| `UV_PREVIEW` | 设为 `1` 启用预览/实验功能 | `0` |

---

### 7. 构建与安装

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_NO_BUILD_ISOLATION` | 设为 `1` 禁用构建隔离（不使用虚拟环境构建） | `0` |
| `UV_COMPILE_BYTECODE` | 设为 `1` 安装后编译 `.pyc` 字节码 | `0` |
| `UV_LINK_MODE` | 文件链接模式：`clone` / `copy` / `hardlink` / `symlink` | 平台默认（如 macOS 为 `clone`） |
| `UV_NO_SYNC` | 设为 `1` 仅安装锁定的包，不自动添加/移除 | `0` |

---

### 8. 虚拟环境
| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_PROJECT_ENVIRONMENT` | 项目虚拟环境路径（相当于 `--python` + 目录） | `.venv` |
| `VIRTUAL_ENV` | 若已存在，uv 会自动识别当前虚拟环境 | 无 |
| `UV_VENV_SEED` | 设为 `1` 在虚拟环境中包含 `pip` 等种子包（默认 true，uv 创建 venv 时会包含） | `1` |

---

### 9. 工具目录
| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_TOOL_DIR` | 通过 `uv tool install` 安装的工具存储目录 | 平台默认 |
| `UV_TOOL_BIN_DIR` | 工具可执行文件的链接目录 | `~/.local/bin`（Linux） |

---

### 10. 认证与发布
| 变量                    | 说明                                           | 默认值          |
| --------------------- | -------------------------------------------- | ------------ |
| `UV_PUBLISH_TOKEN`    | `uv publish` 时的 token（PyPI 等）                | 无            |
| `UV_PUBLISH_USERNAME` | 用户名                                          | 无            |
| `UV_PUBLISH_PASSWORD` | 密码                                           | 无            |
| `UV_KEYRING_PROVIDER` | 密钥环后端：`subprocess`（调用系统 keyring）或 `disabled` | `subprocess` |
| `UV_NATIVE_TLS`       | 同样影响认证连接（使用系统证书）                             | 见上           |

---

### 11. 其他实用变量
| 变量 | 说明 | 默认值 |
|------|------|--------|
| `UV_PRINT_ENV` | 设为 `1` 时，uv 会在运行前打印出所有生效的环境变量（调试用） | `0` |
| `UV_NO_CONFIG` | 设为 `1` 避免读取任何配置文件 | `0` |
| `UV_CONFIG_FILE` | 指定配置文件的路径（替代默认） | 无 |

---

### 使用提示
- **临时设置**：`UV_INDEX_URL=... uv pip install xxx`
- **永久生效**：写入 shell 配置文件（`~/.bashrc`, `~/.zshrc` 等）并用 `export`。
- **配置文件**：uv 也支持 `pyproject.toml` 里的 `[tool.uv]` 或全局 `uv.toml`，环境变量优先级更高。
- **大小写敏感**：全部大写，`UV_` 前缀。

# 命令
`uv` 支持用 `uv config set` 命令直接将配置写入文件，无需手写环境变量。配置键名与环境变量对应，但用“小写+连字符”形式。

### 常用设置命令示例

**设置主索引（镜像）**

bash

uv config set index-url https://pypi.tuna.tsinghua.edu.cn/simple

效果等同于 `UV_INDEX_URL=...`，且写入全局配置（对所有项目生效）。

**设置额外索引**

bash

uv config set extra-index-url https://private.example.com/simple

**设置并发下载数**

bash

uv config set concurrent-downloads 100

**设置缓存目录**

bash

uv config set cache-dir /path/to/custom/cache

**设置为离线模式**

bash

uv config set offline true

**锁定解析策略**

bash

uv config set resolution lowest

**设置 Python 托管安装目录**

bash

uv config set python-install-dir /opt/uv-pythons

这些配置默认保存在：

- Linux/macOS: `~/.config/uv/uv.toml`
    
- Windows: `%APPDATA%\uv\uv.toml`
    

你也可以用 `uv config set --local` 只在当前项目生效（写入项目下的 `.venv/../uv.toml` 或 `pyproject.toml` 的 `[tool.uv]` 节）。

---

### 环境变量 vs. uv config 对照速查

|环境变量|对应 `uv config set` 键名|
|---|---|
|`UV_INDEX_URL`|`index-url`|
|`UV_EXTRA_INDEX_URL`|`extra-index-url`|
|`UV_CONCURRENT_DOWNLOADS`|`concurrent-downloads`|
|`UV_CONCURRENT_BUILDS`|`concurrent-builds`|
|`UV_CACHE_DIR`|`cache-dir`|
|`UV_OFFLINE`|`offline`|
|`UV_HTTP_TIMEOUT`|`http-timeout`|
|`UV_NO_CACHE`|`no-cache`|
|`UV_LINK_MODE`|`link-mode`|
|`UV_RESOLUTION`|`resolution`|
|`UV_PYTHON_PREFERENCE`|`python-preference`|
|`UV_PYTHON_INSTALL_DIR`|`python-install-dir`|

查看完整列表：`uv config list` 或 `uv config show`。