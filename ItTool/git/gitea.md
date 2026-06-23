# gitea

Gitea 是轻量级开源 Git 代码托管平台，支持跨平台部署（Linux/Windows/macOS）及 Docker 快速部署。本文涵盖 Linux、Windows、Docker 三种部署完整流程，优先使用内置 SQLite 简化配置。

前置说明

- 推荐版本：Gitea 稳定版（如 1.22.x），官方下载地址：[Gitea 下载页](https://dl.gitea.com/gitea/)
- 基础依赖：Git（≥2.0），无需额外数据库（SQLite 内置）
- 网络要求：开放指定端口（默认 HTTP 3000，SSH 22/222）

## gitea部署

### Linux 系统部署（CentOS/Debian/Ubuntu）

#### 环境准备

##### 安装 Git

```bash
# CentOS/RHEL
yum install git -y

# Debian/Ubuntu
apt update && apt install git -y

# 验证版本
git --version
```

##### 创建 Gitea 用户

```bash
# 创建无登录权限用户
useradd -m -s /bin/bash gitea
# （可选）设置密码，无需登录
# passwd gitea
```

##### 下载 Gitea 二进制包

```bash
# 切换到系统可执行目录
cd /usr/local/bin

# 下载最新稳定版
wget https://dl.gitea.com/gitea/1.22.0/gitea-1.22.0-linux-amd64 -O gitea

# 添加可执行权限
chmod +x gitea

# 验证版本
./gitea --version
```

#### 配置目录结构

```bash
# 创建核心目录（数据/配置/日志）
mkdir -p /var/lib/gitea/{custom,data,log}
mkdir -p /etc/gitea

# 设置目录归属 gitea 用户
chown -R gitea:gitea /var/lib/gitea/
chown -R gitea:gitea /etc/gitea/

# 限制目录权限（仅所有者）
chmod -R 700 /var/lib/gitea/
chmod -R 700 /etc/gitea/
```

#### 创建 Systemd 服务（开机自启）

##### 编写服务文件

```bash
vim /etc/systemd/system/gitea.service
```

写入以下内容：

```ini
[Unit]
Description=Gitea (Git with a cup of tea)
After=network.target

[Service]
User=gitea
Group=gitea
Type=simple
WorkingDirectory=/var/lib/gitea/
ExecStart=/usr/local/bin/gitea web --config /etc/gitea/app.ini
Restart=always
Environment=USER=gitea HOME=/home/gitea GITEA_WORK_DIR=/var/lib/gitea

[Install]
WantedBy=multi-user.target
```

##### 启动并设置开机自启

```bash
# 重载 systemd 配置
systemctl daemon-reload

# 启动 Gitea
systemctl start gitea

# 设置开机自启
systemctl enable gitea

# 查看服务状态
systemctl status gitea
```

#### 网页初始化配置

##### 核心配置项：

| 配置项     | 推荐值                                 |
| :--------- | :------------------------------------- |
| 数据库类型 | SQLite3                                |
| 数据库路径 | /var/lib/gitea/data/gitea.db           |
| 站点标题   | 自定义                                 |
| 仓库根目录 | /var/lib/gitea/data/gitea-repositories |
| 运行用户   | gitea                                  |
| 服务器域名 | 服务器 IP / 域名                       |
| SSH 端口   | 22                                     |
| 管理员账号 | 自定义用户名 + 密码                    |

点击「安装 Gitea」，完成后自动跳转登录页。

#### 验证与常用命令

```bash
# 重启 Gitea
systemctl restart gitea

# 查看实时日志
journalctl -u gitea -f

# 停止 Gitea
systemctl stop gitea
```

#### 常见问题

- 端口 3000 被占用：修改 `/etc/gitea/app.ini` 中 `HTTP_PORT`，或关闭占用进程；
- 权限不足：检查 `/var/lib/gitea` 和 `/etc/gitea` 目录的归属（gitea:gitea）；
- 服务启动失败：通过 `journalctl -u gitea` 查看日志，排查配置文件语法错误。

### Windows 系统部署

##### 环境准备

###### 安装 Git

1. 下载地址：[Git for Windows](https://git-scm.com/download/win)

2. 安装：默认配置，完成后验证：

   ```bash
   git --version
   ```

##### 下载 Gitea

1. 下载地址：[Gitea Windows 版](https://dl.gitea.com/gitea/1.22.0/gitea-1.22.0-windows-amd64.exe)
2. 新建目录 `D:\gitea`，将文件放入并改名为 `gitea.exe`。

##### 配置目录结构

在 `D:\gitea` 下创建：

```markdown
D:\gitea\custom
D:\gitea\data
D:\gitea\log
D:\gitea\config
```

```cmd
md custom
md data
md log
md config
```

##### 启动 Gitea（两种方式）

###### 临时启动

1. 管理员 PowerShell，切换目录：

   ```powershell
   cd D:\gitea
   ```

2. 启动：

   ```powershell
   .\gitea.exe web --config D:\gitea\config\app.ini
   ```

   > 关闭窗口则停止。

###### 方式2：注册为 Windows 服务（开机自启）

1. 管理员 PowerShell 执行：

   ```powershell
   .\gitea.exe service install --config D:\gitea\config\app.ini --service-name "Gitea" --service-display-name "Gitea Git Service"
   .\gitea.exe service start
   # 停止/卸载
   # .\gitea.exe service stop
   # .\gitea.exe service uninstall
   ```

2. 也可通过「服务面板」管理：`services.msc` → 找到「Gitea Git Service」→ 设置开机自启。

##### 网页初始化配置

1. 访问：`http://本机IP:3000` 或 `http://localhost:3000`

2. 核心配置项：

   | 配置项     | 推荐值                           |
   | :--------- | :------------------------------- |
   | 数据库类型 | SQLite3                          |
   | 数据库路径 | D:\gitea\data\gitea.db           |
   | 仓库根目录 | D:\gitea\data\gitea-repositories |
   | 运行用户   | 当前 Windows 用户名              |
   | 服务器域名 | 本机 IP 或 localhost             |

3. 设置管理员账号，点击「安装 Gitea」。

##### 验证与日志

- 日志：`D:\gitea\log\gitea.log`
- 防火墙：开放 3000 端口（Windows Defender → 高级设置 → 入站规则 → 新建端口规则）。

##### 常见问题

- 服务注册失败：管理员运行 PowerShell；
- 端口被占用：启动时指定 `--port 3001`；
- 访问超时：检查防火墙放行 3000 端口。

### Docker 部署（跨平台）

#### 环境准备

1. 安装 Docker：

   - Linux：[官方文档](https://docs.docker.com/engine/install/)
   - Windows/macOS：[Docker Desktop](https://www.docker.com/products/docker-desktop/)

2. 验证：

   ```bash
   docker --version
   docker compose version
   ```

#### 方式1：Docker Run 快速部署

##### 创建持久化目录

```bash
# Linux
mkdir -p /var/lib/gitea

# Windows（PowerShell）
# mkdir C:\gitea
```

##### 启动容器

```bash
docker run -d \
  --name gitea \
  --restart always \
  -p 3000:3000 \
  -p 222:22 \
  -v /var/lib/gitea:/data \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  gitea/gitea:1.22.0
```

#### Docker Compose 部署（推荐）

##### 编写 docker-compose.yml

创建 `~/gitea`（Linux）或 `C:\gitea`（Windows），新建 `docker-compose.yml`：

yaml

```
version: "3"

services:
  gitea:
    image: gitea/gitea:1.22.0
    container_name: gitea
    restart: always
    ports:
      - "3000:3000"
      - "222:22"
    volumes:
      - ./data:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    environment:
      - USER_UID=1000
      - USER_GID=1000
      # Windows 可删除 USER_UID/USER_GID
```

##### 启动容器

```bash
cd ~/gitea  # Linux
# cd C:\gitea  # Windows
docker compose up -d # 后台启动容器
docker compose ps # 查看容器状态
docker compose logs -f gitea # 查看实时日志
```

#### 网页初始化配置

1. 访问 `http://服务器IP:3000`
2. SSH 端口填 `222`，其他同 Linux/Windows
3. 设置管理员账号，完成安装

#### 常用操作

```bash
docker compose restart gitea # 重启容器
docker compose down # 停止容器
docker compose pull gitea && docker compose up -d # 升级 Gitea（先备份数据）
cp -r ~/gitea/data /backup/gitea-data  # Linux备份数据（复制持久化目录）
```

#### 常见问题

- 数据卷权限错误（Linux）：确保 `~/gitea/data` 权限为 `1000:1000`
- SSH 克隆失败：地址格式 `ssh://git@服务器IP:222/用户名/仓库名.git`
- 容器启动失败：查看 `docker compose logs gitea`

#### 通用注意事项

1. **防火墙**：开放 3000、SSH 端口（主机22或映射222）
2. **备份**：定期备份数据目录（Linux：`/var/lib/gitea/data`；Windows：`D:\gitea\data`；Docker：`~/gitea/data`）
3. **HTTPS**：生产环境建议用 Nginx/Caddy 反向代理
4. **升级**：
   - 二进制：替换文件，重启服务
   - Docker：拉取新镜像，重启容器（备份数据）

## gitea编译

### Ubuntu 24+ 交叉编译 Windows 版 Gitea.exe

前置说明

- **目标**：Ubuntu 24.04/24.10 x86_64 下编译 Windows x86_64 的 gitea.exe（含 bindata、SQLite）。
- **依赖**：Go 1.25+、Node.js 22+ LTS、Zig、Git、Make、GCC。
- **原则**：启用 CGO（SQLite）、GOOS=windows/GOARCH=amd64、Zig 编译器。

#### 系统基础环境初始化

##### 操作命令

```bash
# 更新系统包索引并升级现有包
sudo apt update && sudo apt upgrade -y
# 安装基础构建工具（必选）
sudo apt install -y git make curl wget gcc build-essential libssl-dev pkg-config libsqlite3-dev
```

##### 可能错误及解决

| 错误类型          | 报错示例                                   | 解决                                                         |
| :---------------- | :----------------------------------------- | :----------------------------------------------------------- |
| apt 超时/源不可用 | `Failed to fetch ...`                      | 换阿里云源：`sudo sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list` 并 `sudo apt update` |
| 包安装失败        | `Unable to locate package build-essential` | `sudo add-apt-repository universe` → `sudo apt update`       |
| 权限不足          | `Sorry, try again.`                        | 确认用户属 sudo 组：`groups $USER`，无则 `sudo usermod -aG sudo 用户名` |

#### 安装 Go 1.25+

##### 操作命令

```bash
# 下载 Go 1.25.3（Ubuntu x86_64 版本，可替换为官网最新 1.25+ 版本）
wget https://dl.google.com/go/go1.25.3.linux-amd64.tar.gz -O /tmp/go1.25.3.tar.gz
# 解压到系统标准路径（覆盖旧版本）
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf /tmp/go1.25.3.tar.gz
```

##### 可能错误及解决

| 错误类型     | 报错示例                           | 解决                                                     |
| :----------- | :--------------------------------- | :------------------------------------------------------- |
| 下载失败     | `wget: unable to resolve host`     | 改用国内镜像：`https://golang.google.cn/dl/...` 或设代理 |
| 解压权限不足 | `tar: cannot open '/usr/local/go'` | `sudo rm -rf /usr/local/go` 后重试                       |
| 架构不匹配   | `Unexpected EOF`                   | `uname -m` 确认架构，下载对应包                          |

#### 配置 Go 环境变量（永久）

##### 操作命令

```bash
# 编辑 bash 配置文件（zsh 用户替换为 ~/.zshrc）
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin' >> ~/.bashrc
echo 'export GO111MODULE=on' >> ~/.bashrc
echo 'export GOPROXY=https://goproxy.cn,direct' >> ~/.bashrc
# 生效配置
source ~/.bashrc
go version
go env | grep GOPATH
# 永久配置国内 Go 代理（覆盖默认的 proxy.golang.org）
go env -w GOPROXY=https://goproxy.cn,direct
# 预下载 Gitea 依赖的 Go toolchain（替换为你当前的 Go 版本）
go install golang.org/toolchain@v0.0.1-go1.25.4.linux-amd64
```

##### 可能错误及解决

| 错误类型              | 报错示例                              | 解决                                              |
| :-------------------- | :------------------------------------ | :------------------------------------------------ |
| go: command not found | `go version` 无响应                   | 检查 `/usr/local/go/bin/go` 存在，PATH 包含该路径 |
| GOPATH 未生效         | `go env GOPATH` 空                    | 检查 ~/.bashrc 拼写，新开终端                     |
| Go Modules 报错       | `modules disabled by GO111MODULE=off` | 确认 `echo $GO111MODULE` 输出 `on`                |

#### 安装 Node.js 22+ LTS 和 npm

##### 操作命令

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # 加载nvm的bash补全（可选）
source ~/.bashrc
nvm install 22
nvm use 22
node -v
npm -v
```

##### 安装 pnpm

```bash
npm install -g pnpm
pnpm -v
```

##### 可能错误及解决

| 错误类型                    | 报错示例                               | 解决                                                         |
| :-------------------------- | :------------------------------------- | :----------------------------------------------------------- |
| nvm 安装失败（GitHub 超时） | `curl: (7) Failed to connect`          | 用 Gitee 镜像：`curl -o- https://gitee.com/mirrors/nvm/raw/v0.40.1/install.sh | bash` 或设代理 |
| nvm use 22 提示未安装       | `version "22 -> N/A" is not installed` | `nvm ls-remote | grep v22`，设 `NVM_NODEJS_ORG_MIRROR=https://npmmirror.com/mirrors/node` 重装 |
| npm 找不到                  | `npm: command not found`               | 确认 nvm 生效，检查 `~/.nvm/versions/node/v22.x.x/bin/npm` 存在 |

#### 安装 Zig（交叉编译 C 编译器）

##### 操作命令

```bash
# 下载 Zig 0.13.0（稳定版，适配 Ubuntu x86_64）
wget https://ziglang.org/download/0.13.0/zig-linux-x86_64-0.13.0.tar.xz -O /tmp/zig.tar.xz
# 解压到系统路径
sudo mkdir -p /usr/local/zig
sudo tar -C /usr/local/zig -xJf /tmp/zig.tar.xz --strip-components=1
# 添加到 PATH
echo 'export PATH=$PATH:/usr/local/zig' >> ~/.bashrc
source ~/.bashrc
zig version
```

##### 可能错误及解决

| 错误类型               | 报错示例                                     | 解决                                                        |
| :--------------------- | :------------------------------------------- | :---------------------------------------------------------- |
| 下载失败               | `404 Not Found`                              | 查看官网最新版，或使用镜像 `https://mirror.ghproxy.com/...` |
| zig: command not found | `zig version` 无响应                         | 检查 `/usr/local/zig/zig` 存在，PATH 包含 `/usr/local/zig`  |
| 解压报错               | `tar: This does not look like a tar archive` | 检查文件 MD5，重新下载                                      |

#### 下载 Gitea 源码并切换版本

##### 操作命令

```bash
# 创建构建目录
mkdir -p ~/gitea-build && cd ~/gitea-build
# 克隆 Gitea 源码（国内镜像加速）
git clone https://gitee.com/mirrors/gitea.git
cd gitea
# 拉取所有标签（确保能切换到指定版本）
git fetch --tags
# 查看可用版本
git tag -l | grep v1.25
# 切换到稳定版本
git checkout v1.25.0
# 验证版本
git rev-parse --short HEAD
```

##### 可能错误及解决

| 错误类型                | 报错示例                           | 解决                                                         |
| :---------------------- | :--------------------------------- | :----------------------------------------------------------- |
| git clone 超时          | `fatal: unable to access`          | `ping gitee.com`，设 git 代理                                |
| git checkout 标签不存在 | `pathspec 'v1.25.0' did not match` | `git tag -l | grep 1.25`，重新 `git fetch --tags`            |
| 目录权限不足            | `mkdir: Permission denied`         | `chmod 755 ~` 或 `sudo mkdir -p ~/gitea-build && sudo chown $USER:$USER ~/gitea-build` |

#### 交叉编译

##### 核心命令（生产环境）

```bash
cd ~/gitea-build/gitea
# 环境变量配置（临时生效，仅对本次make命令有效）
# 执行Makefile中的build目标
CC="zig cc -target x86_64-windows-gnu" CGO_ENABLED=1 CGO_CFLAGS="-O2 -g" GOOS=windows GOARCH=amd64 TAGS="bindata sqlite sqlite_unlock_notify" make build
```

##### 参数说明

- `CC="zig cc -target x86_64-windows-gnu"`：指定 Zig 为 C 编译器，目标 Windows x86_64 GNU 运行时。
- `CGO_ENABLED=1`：启用 CGO（SQLite 需要）。
- `CGO_CFLAGS="-O2 -g"`：C 编译优化级别 O2，生成调试信息。
- `GOOS=windows`：目标系统 Windows。
- `GOARCH=amd64`：目标架构 x86_64。
- `TAGS="bindata sqlite sqlite_unlock_notify"`：构建标签（嵌入静态文件、SQLite 支持、解锁通知）。
- `make build`：执行 Makefile 的 build 目标。
  - 补充：make 的常用参数：
    - `-f MyMakefile`：指定非默认的 Makefile。
    - `-j4`：并行构建（4 线程，加快编译速度）。
    - `-n`：仅打印命令不执行（调试 Makefile）。

##### 可能错误及解决

| 错误类型                             | 报错示例                                      | 解决                                                         |
| :----------------------------------- | :-------------------------------------------- | :----------------------------------------------------------- |
| make: No rule to make target 'build' | `make: *** No rule to make target 'build'.`   | 确认在 Gitea 源码根目录（有 Makefile）                       |
| CGO 编译错误（SQLite）               | `sqlite3.h: No such file`                     | `sudo apt install libsqlite3-dev`，确认 `CGO_ENABLED=1`      |
| Go 模块下载失败                      | `go: module ... not found`                    | `go env GOPROXY` 应含 `https://goproxy.cn`，或 `go clean -modcache` |
| 前端构建失败（npm）                  | `npm ERR! code EAI_AGAIN`                     | `npm config set registry https://registry.npmmirror.com`，`npm cache clean --force` |
| Zig 参数错误                         | `unrecognized command line option '-pthread'` | 移除 `-pthread`（CGO_CFLAGS 中），确保 Zig ≥0.13.0           |

#### 验证构建结果

##### 操作命令

```bash
ls -lh ~/gitea-build/gitea/gitea.exe
file ~/gitea-build/gitea/gitea.exe
# 复制到 Windows 测试：scp ... 或共享文件夹
# Windows 端执行 gitea.exe web，访问 localhost:3000
```

##### 可能错误及解决

| 错误类型           | 报错示例                        | 解决                                                         |
| :----------------- | :------------------------------ | :----------------------------------------------------------- |
| 未生成 exe         | `No such file or directory`     | 检查编译日志，确认 GOOS/GOARCH 正确                          |
| Windows 运行缺 DLL | `缺少 msvcrt.dll / sqlite3.dll` | 静态编译：添加 `LDFLAGS="-linkmode external -extldflags '-static'"` |
| 配置文件不存在     | `app.ini not found`             | 创建 `custom/conf/app.ini` 或启动时指定 `-c`                 |

#### 控制前端 Source Map 生成

```bash
# 生成完整 Source Map（开发）
CC="zig cc -target x86_64-windows-gnu" CGO_ENABLED=1 CGO_CFLAGS="-O2 -g" GOOS=windows GOARCH=amd64 TAGS="bindata sqlite sqlite_unlock_notify" ENABLE_SOURCEMAP=true make build

# 禁用 Source Map（生产）
CC="zig cc -target x86_64-windows-gnu" CGO_ENABLED=1 CGO_CFLAGS="-O2 -g" GOOS=windows GOARCH=amd64 TAGS="bindata sqlite sqlite_unlock_notify" ENABLE_SOURCEMAP=false make build
```

可能错误及解决：禁用后仍生成 .map → 清理 `web_src/node_modules/.cache web_src/dist`，`unset ENABLE_SOURCEMAP` 后重试。

#### 清理构建缓存（可选）

```bash
cd ~/gitea-build/gitea
make clean
go clean -modcache
rm -rf ~/gitea-build/gitea/gitea.exe
```

可能错误及解决：`make clean` 无规则 → 手动删除 `dist/ gitea.exe tmp/ web_src/dist`

#### 最终产物说明

- 编译出的 `gitea.exe` 在 `~/gitea-build/gitea/`，可直接运行于 Windows x86_64。
- 生产建议：静态编译、禁用 Source Map、固定 AppWorkPath。

```bash
# 进阶优化（可选）：用 Inkscape 转换 PNG 到 SVG（效果更好）
sudo apt install inkscape -y
inkscape fanction.png -o fanction.svg --export-background-opacity=0 --export-dpi=300
```

### Ubuntu 24+ 编译并部署 Linux 版 Gitea

**前置说明**

- 目标：Ubuntu 24.04/24.10 x86_64 下编译并部署 Linux x86_64 版 Gitea（含 bindata、SQLite）。
- 依赖：Go 1.25+、Node.js 22+ LTS、Git、Make、GCC、libsqlite3-dev。
- 原则：启用 CGO（SQLite），系统原生编译器，生产优化。

#### 系统基础环境

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git make curl wget gcc build-essential libssl-dev pkg-config libsqlite3-dev zlib1g-dev
```

**可能错误**

- apt 超时：换阿里云源（`sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list`）后 `apt update`。
- 依赖冲突：`sudo apt -f install`。

#### 安装 Go 1.25+

```bash
wget https://dl.google.com/go/go1.25.3.linux-amd64.tar.gz -O /tmp/go1.25.3.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf /tmp/go1.25.3.tar.gz
```

**可能错误**

- 下载失败：用国内镜像 `https://golang.google.cn/dl/...`。
- 解压权限：`sudo chmod 755 /usr/local`。

#### 配置 Go 环境变量

```bash
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin' >> ~/.bashrc
echo 'export GO111MODULE=on' >> ~/.bashrc
echo 'export GOPROXY=https://goproxy.cn,direct' >> ~/.bashrc
source ~/.bashrc
go version  # 应输出 go1.25.3 linux/amd64
```

**可能错误**

- `go: command not found`：检查 `/usr/local/go/bin/go` 是否存在。

#### 安装 Node.js 22+ LTS

```bash
# 安装 nvm 版本管理器
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
nvm install 22
nvm use 22
node -v  # v22.x.x
npm -v   # 10.x.x
```

**可能错误**

- nvm 安装超时：用 Gitee 镜像 `curl -o- https://gitee.com/mirrors/nvm/raw/v0.40.1/install.sh | bash`。

#### 下载 Gitea 源码并切换版本

```bash
mkdir -p ~/gitea-build && cd ~/gitea-build
git clone https://gitee.com/mirrors/gitea.git
cd gitea
git fetch --tags
git checkout v1.25.0
git rev-parse --short HEAD
```

**可能错误**

- 标签不存在：`git tag -l | grep v1.25`，重新 `git fetch --tags`。

#### 编译 Linux 版 Gitea

```bash
npm install -g pnpm
CGO_ENABLED=1 TAGS="bindata sqlite sqlite_unlock_notify" make build
```

**可选（减小体积）**

```bash
CGO_ENABLED=1 TAGS="bindata sqlite sqlite_unlock_notify" LDFLAGS="-s -w" make build
```

### 自定义数据存储路径

```bash
CGO_ENABLED=1 TAGS="bindata sqlite sqlite_unlock_notify" \
LDFLAGS="-X \"code.gitea.io/gitea/modules/setting.CustomPath=/var/lib/gitea/custom\" \
         -X \"code.gitea.io/gitea/modules/setting.AppWorkPath=/var/lib/gitea\"" \
make build
```

**错误及解决**

| 错误类型        | 报错示例                               | 解决                                                         |
| :-------------- | :------------------------------------- | :----------------------------------------------------------- |
| 前端构建失败    | `npm ERR!`                             | 配置 npm 镜像：`npm config set registry https://registry.npmmirror.com`；清理缓存：`npm cache clean --force` |
| SQLite 依赖错误 | `sqlite3.h: No such file or directory` | `sudo apt install libsqlite3-dev`                            |

#### 验证编译结果

```bash
ls -lh gitea
file gitea          # 应输出 ELF 64-bit LSB executable
./gitea --version   # 显示版本信息
```

### 部署到生产环境

#### 创建用户及目录

```bash
sudo adduser --system --group --disabled-password --shell /bin/bash --home /var/lib/gitea gitea
sudo mkdir -p /var/lib/gitea/{custom,data,log}
sudo mkdir -p /etc/gitea
sudo chown -R gitea:gitea /var/lib/gitea/
sudo chmod -R 750 /var/lib/gitea/
sudo chown root:gitea /etc/gitea
sudo chmod 770 /etc/gitea   # 首次配置后改为 750
```

#### 复制可执行文件

```bash
sudo cp ~/gitea-build/gitea/gitea /usr/local/bin/
sudo chmod +x /usr/local/bin/gitea
```

#### 配置 systemd 服务

```bash
sudo nano /etc/systemd/system/gitea.service
```

服务文件内容：

```ini
[Unit]
Description=Gitea (Git with a cup of tea)
After=network.target
# 若使用 MySQL/PostgreSQL 需添加对应 After，SQLite 可省略

[Service]
User=gitea
Group=gitea
WorkingDirectory=/var/lib/gitea
ExecStart=/usr/local/bin/gitea web --config /etc/gitea/app.ini
Restart=always
Environment=USER=gitea HOME=/var/lib/gitea GITEA_WORK_DIR=/var/lib/gitea

[Install]
WantedBy=multi-user.target
```

#### 启动并设置开机自启

```bash
sudo systemctl daemon-reload
sudo systemctl start gitea
sudo systemctl enable gitea
sudo systemctl status gitea
```

**错误及解决**

| 错误类型     | 报错示例                       | 解决                                                        |
| :----------- | :----------------------------- | :---------------------------------------------------------- |
| 服务启动失败 | `Job for gitea.service failed` | 查看日志：`sudo journalctl -u gitea -f`；检查目录权限或配置 |
| 端口被占用   | `address already in use`       | 修改 `/etc/gitea/app.ini` 中 `[server]` 的 `HTTP_PORT`      |

#### 初始化配置

1. 浏览器访问 `服务器IP:3000`

2. 选择数据库（SQLite 或其它），填写站点信息、管理员账号

3. 限制配置文件权限：

   ```bash
   sudo chmod 750 /etc/gitea
   sudo chmod 640 /etc/gitea/app.ini
   ```

#### 防火墙（可选）

```bash
sudo ufw allow 3000/tcp
# 若使用 80/443 需配合反向代理：
# sudo ufw allow 80/tcp
# sudo ufw allow 443/tcp
sudo ufw enable
```

### 日常维护

```bash
sudo systemctl restart gitea          # 重启
sudo journalctl -u gitea -n 100       # 查看日志
# 升级：停止服务 → 重新编译替换 → 启动
sudo systemctl stop gitea
# 编译新版后替换 /usr/local/bin/gitea
sudo systemctl start gitea
```

### 最终验证

1. 访问 `http://服务器IP:3000` 正常加载
2. 登录管理员账号，创建测试仓库
3. 检查数据目录 `ls -lh /var/lib/gitea/data`

部署完成，Gitea 以服务运行，数据存储于 `/var/lib/gitea`。

## gitea 使用方式

官方文档：[What is Gitea? | Gitea Documentation](https://docs.gitea.com/)

直接双击 `gitea.exe` 或在命令提示符中进入目录执行：

```cmd
gitea web
```

**本机访问**
浏览器打开：`http://localhost:3000/lingkong/AiSoftwareTesting`
克隆仓库（HTTPS，需账号密码）：

```bash
git clone http://localhost:3000/lingkong/AiSoftwareTesting.git
```

### 局域网伙伴访问设置

#### 修改 Gitea 配置

编辑 `app.ini`（手动安装：`Gitea安装目录/custom/conf/app.ini`；Docker：`挂载的data目录/gitea/conf/app.ini`），修改 `[server]` 部分：

```ini
DOMAIN = 你的服务器局域网IP（如 192.168.1.100）
ROOT_URL = http://你的服务器局域网IP:3000/
```

重启 Gitea 服务。

#### 局域网伙伴访问

- 浏览器：`http://你的服务器局域网IP:3000/lingkong/AiSoftwareTesting`

- 克隆仓库

  ```bash
  git clone http://你的服务器局域网IP:3000/lingkong/AiSoftwareTesting.git
  ```

#### 补充说明

- 确保服务器 3000 端口已开放（防火墙/安全软件允许局域网访问）。
- 可配置 SSH 密钥免密码登录（伙伴将公钥添加到 Gitea 账号）。







