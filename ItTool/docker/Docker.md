# Docker 全流程使用指南

本文档基于截至 2026 年 4 月的 Docker 官方最新稳定版本编写，覆盖从安装、基础操作、进阶定制到企业级生产落地的全流程，命令可直接复制运行，兼顾新手入门与生产实践，同时适配 v29 版本核心变更。

------

## 一、版本公告与核心变更（必看）

### 1.1 最新稳定版本信息

|      组件      |    最新稳定版    |                           发布说明                           |
| :------------: | :--------------: | :----------------------------------------------------------: |
| Docker Engine  |     v29.3.1      |     2026 年 3 月 26 日发布，配套 Docker Desktop v4.66.1      |
| Docker Compose |      v5.1.1      | 已内置到 Docker Desktop 与 Docker Engine 官方安装包，V1（docker-compose）已停止维护 |
|   容器运行时   | containerd 1.6.x |   Docker 默认集成的行业标准容器运行时，管理容器全生命周期    |

### 1.2 v29 版本核心变更预警

⚠️ **重大兼容性调整**：Docker Engine v29 将最低支持的 API 版本从 1.41 升至 1.44，部分旧版第三方管理工具（如 Watchtower、旧版 Portainer 等）可能出现功能失效，使用前请将相关工具升级至最新稳定版。

------

## 二、Docker 核心概述

### 2.1 核心定位

Docker 是开源的应用容器引擎，将应用及其依赖、配置打包成标准化容器，实现**一次构建，到处运行**，彻底解决开发与生产环境不一致的痛点。

### 2.2 容器 vs 传统虚拟机 核心对比

|    对比项    |       Docker 容器        |      传统虚拟机       |
| :----------: | :----------------------: | :-------------------: |
|   资源占用   | 10-500MB，共享宿主机内核 | 1-50GB，完整 Guest OS |
|   启动速度   |           秒级           |        分钟级         |
| 单机部署密度 |          数百个          |        10 余个        |
|   性能开销   |   接近原生，损耗 < 5%    |   5-20% 的系统损耗    |
|    隔离性    |  内核级 Namespace 隔离   |    硬件级完全隔离     |

### 2.3 核心架构与必懂概念

#### 核心架构

Docker 采用 C/S（客户端 - 服务器）架构，核心组件包括：

- **Docker Client**：CLI 命令行工具，通过 REST API 向守护进程发送指令
- **Docker Daemon (dockerd)**：后台服务，负责管理镜像、容器、网络、数据卷
- **containerd**：行业标准容器运行时，接管容器的创建、启停、销毁全生命周期
- **runc**：OCI 兼容的低级运行时，负责基于 Linux 内核 Namespace、Cgroup 实现容器的底层隔离

#### 底层核心原理

Docker 基于 Linux 内核三大核心机制实现容器能力：

1. **Namespace**：7 种命名空间，实现进程、网络、文件系统、用户等资源的隔离
2. **Cgroup**：控制组，限制容器的 CPU、内存、磁盘 IO 等资源使用上限
3. **UnionFS**：联合文件系统，实现镜像的分层存储、共享复用，大幅减小镜像体积

#### 核心基础概念

|       概念        |                           核心说明                           |
| :---------------: | :----------------------------------------------------------: |
|   镜像（Image）   | 容器的只读模板，包含应用运行所需的代码、依赖、环境变量、配置，类比「程序安装包」 |
| 容器（Container） | 镜像的运行实例，是一个独立的沙箱环境，类比「安装后运行的程序」 |
| 仓库（Registry）  | 镜像的存储与分发中心，官方仓库为 Docker Hub，国内常用阿里云、DaoCloud 镜像源 |
|    Dockerfile     |      构建自定义镜像的文本文件，包含一系列标准化构建指令      |
|  Docker Compose   | 多容器编排工具，通过 YAML 文件一键定义、启动、管理多个关联容器 |

------

## 三、全平台下载与安装

### 前置说明

- 个人开发 / 桌面端：优先安装**Docker Desktop**（内置 Docker Engine、Compose V2、可视化界面）
- 服务器生产环境：优先安装**Docker Engine**（纯命令行，轻量化，资源占用低）
- 官方下载地址：https://www.docker.com/get-started/Docker

### 3.1 系统要求

|  平台   |                      最低系统要求                       |          推荐配置           |
| :-----: | :-----------------------------------------------------: | :-------------------------: |
|  Linux  |    内核≥5.4，推荐 Ubuntu 24.04 LTS / CentOS Stream 9    | 8GB + 内存，50GB + 磁盘空间 |
| Windows |   Win11 / Win10 专业版 / 企业版，启用 WSL2 与 Hyper-V   |  8GB + 内存，64GB+SSD 空间  |
|  macOS  | macOS Sonoma 14.x 及以上，Apple Silicon 需安装 Rosetta2 |  8GB + 内存，64GB+SSD 空间  |

### 3.2 Windows & macOS 安装（桌面端首选）

桌面端优先安装**Docker Desktop**，内置 Docker Engine、Compose V2、可视化界面、Buildx 等全套组件，开箱即用。

1. 官方下载地址：https://www.docker.com/get-started/
2. 下载对应系统架构的安装包，双击按照向导完成安装
3. Windows 需勾选「Use the WSL 2 based engine」，macOS 无需额外配置
4. 安装完成后启动 Docker Desktop，打开终端执行`docker version`验证安装成功

### 3.3 Linux 安装（服务器生产环境首选）

以下提供两种稳定安装方式，均适配国内网络环境，优先推荐官方一键脚本安装。

#### 3.3.1 官方一键脚本安装（国内加速版，推荐）

基于官方`get.docker.com`脚本，内置阿里云镜像源，一键完成全组件安装，适配 Ubuntu/Debian/CentOS/RHEL 等主流发行版。

```
# 1. 下载并执行安装脚本，指定阿里云镜像加速
curl -fsSL https://get.docker.com -o install-docker.sh
sudo sh install-docker.sh --mirror Aliyun

# 2. 启动Docker服务并设置开机自启
sudo systemctl start docker
sudo systemctl enable docker

# 3. 将当前用户加入docker组，免sudo执行docker命令
sudo usermod -aG docker $USER
newgrp docker

# 4. 验证安装
docker --version
docker run --rm hello-world
```

> 脚本拓展参数：
>
> - `--version 29.3.1`：安装指定版本，不指定默认安装最新稳定版
> - `--channel test`：安装测试 / 预发布版本
> - `--setup-repo`：仅配置软件源，不安装 Docker
> - `--no-autostart`：安装后不自动启动 Docker 服务

#### 3.3.2 手动分步安装（Ubuntu，阿里云源）

基于阿里云官方 GPG 密钥与软件源，手动安装稳定版，适配 Ubuntu 全 LTS 版本。

```
# 1. 卸载系统自带的旧版本Docker
sudo apt-get remove -y docker docker-engine docker.io containerd runc

# 2. 安装系统依赖
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release

# 3. 导入阿里云Docker官方GPG密钥
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 4. 添加阿里云Docker软件源
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. 安装Docker核心全组件
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 6. 启动服务、配置权限、验证安装（同一键脚本步骤2-4）
```

------

## 四、安装后核心配置（必做）

### 4.1 国内镜像加速配置（解决拉取镜像超时 / 失败）

Docker 默认从 Docker Hub 拉取镜像，国内网络访问受限，必须配置国内镜像源，以下为 2026 年稳定可用的加速源。

#### 配置步骤

1. 创建 / 修改 Docker daemon 配置文件

   ```
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": [
       "https://docker.m.daocloud.io",
       "https://docker.mirrors.ustc.edu.cn",
       "https://hub-mirror.c.163.com",
       "https://mirror.ccs.tencentyun.com"
     ],
     "live-restore": true,
     "log-driver": "json-file",
     "log-opts": {
       "max-size": "100m",
       "max-file": "3",
       "compress": "true"
     }
   }
   EOF
   ```

   > 备注：`https://docker.1panel.live` 目前解析不稳定，暂不推荐加入配置；DaoCloud 镜像源支持全量镜像代理，稳定性最佳。

2. 重载配置并重启 Docker 生效

   ```
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

3. 验证配置是否生效

   ```
   docker info | grep Mirrors -A 5
   ```

#### 海外镜像应急拉取方案

若标准加速源仍无法拉取 gcr.io、quay.io 等海外仓库镜像，可使用 DaoCloud 前缀替换方案，直接拉取：

```
# 标准格式：m.daocloud.io/原镜像地址
# 示例1：拉取Docker Hub官方nginx镜像
docker pull m.daocloud.io/docker.io/library/nginx:stable-alpine
# 示例2：拉取k8s官方镜像
docker pull m.daocloud.io/registry.k8s.io/kube-apiserver:v1.31.0
```

### 4.2 基础环境验证

```
# 查看Docker完整版本信息（客户端+服务端均正常显示即为环境正常）
docker version
# 查看Docker系统配置与资源占用
docker info
# 运行测试容器，正常输出hello world即为全环境可用
docker run --rm hello-world
```

------

## 五、Docker 核心基础命令（全场景覆盖）

### 5.1 镜像核心操作

|                  命令                   |        核心说明        |                           实用示例                           |
| :-------------------------------------: | :--------------------: | :----------------------------------------------------------: |
|        `docker pull 镜像名:标签`        |     从仓库拉取镜像     | `docker pull nginx:stable-alpine`（不写标签默认拉取 latest 版） |
|             `docker images`             |    列出本地所有镜像    | `docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}\t{{.CreatedAt}}"`（格式化输出） |
|      `docker build -t 名称:标签 .`      | 从 Dockerfile 构建镜像 | `docker build -t my-spring-app:v1.0 .`（`.`为构建上下文路径） |
|      `docker rmi 镜像ID/名称:标签`      |      删除本地镜像      | `docker rmi nginx:stable-alpine`；`docker rmi -f 镜像ID`（强制删除） |
| `docker tag 源镜像:标签 目标镜像:标签`  |      给镜像打标签      | `docker tag nginx:alpine my-registry.com/nginx:v1.0`（用于推送到私有仓库） |
|        `docker push 镜像名:标签`        |     推送镜像到仓库     |           `docker push my-registry.com/nginx:v1.0`           |
| `docker save -o 导出包.tar 镜像名:标签` | 导出镜像为本地 tar 包  |        `docker save -o nginx-alpine.tar nginx:alpine`        |
|       `docker load -i 镜像包.tar`       |   从 tar 包导入镜像    |              `docker load -i nginx-alpine.tar`               |

### 5.2 容器核心操作（高频使用）

|                  命令                   |        核心说明        |                           实用示例                           |
| :-------------------------------------: | :--------------------: | :----------------------------------------------------------: |
|     `docker run [参数] 镜像名:标签`     |     创建并启动容器     | 核心示例：`docker run -d --name nginx-demo -p 80:80 -v ./html:/usr/share/nginx/html:ro --restart unless-stopped nginx:stable-alpine` |
|              核心参数详解               |           -            | `-d` 后台守护式运行；`--name` 指定容器唯一名称；`-p 主机端口:容器端口` 端口映射；`-v 主机路径:容器路径` 数据挂载；`--rm` 容器停止后自动删除；`-e 键=值` 设置环境变量；`--restart` 重启策略 |
|             `docker ps -a`              |      查看容器列表      | `docker ps` 仅查看运行中容器；`docker ps -a` 查看所有容器（含已停止） |
| `docker stop/start/restart 容器ID/名称` | 停止 / 启动 / 重启容器 | `docker stop nginx-demo`；`docker kill 容器ID`（强制立即停止） |
|         `docker rm 容器ID/名称`         |        删除容器        | `docker rm nginx-demo`（仅能删除已停止容器）；`docker rm -f 容器ID`（强制删除运行中容器）；`docker rm $(docker ps -aq)`（批量删除所有容器） |
|   `docker exec -it 容器ID/名称 终端`    |     进入运行中容器     | `docker exec -it nginx-demo /bin/sh`（alpine 镜像无 bash，用 sh） |
|      `docker logs -f 容器ID/名称`       |      查看容器日志      | `docker logs -f nginx-demo`（实时滚动日志）；`docker logs --tail 100 --since 1h nginx-demo`（查看最近 1 小时最后 100 行日志） |
|      `docker inspect 容器ID/名称`       |    查看容器完整详情    | 可获取容器 IP、挂载路径、网络配置、环境变量、健康检查等全量信息 |
|       `docker cp 源路径 目标路径`       |  主机与容器间文件拷贝  | `docker cp ./index.html nginx-demo:/usr/share/nginx/html/`（主机→容器）；反向拷贝同理 |
|             `docker stats`              |  实时查看容器资源占用  |     实时监控容器 CPU、内存、网络、磁盘 IO 等资源使用情况     |

### 5.3 数据卷核心操作（数据持久化）

容器删除后内部数据会全部丢失，必须通过数据卷实现持久化，Docker 提供 3 种挂载方式，优先使用**命名卷**。

```
# 1. 创建命名卷（生产环境首选，Docker统一管理，生命周期独立于容器）
docker volume create nginx-data
# 2. 查看所有数据卷
docker volume ls
# 3. 查看卷详情（获取宿主机挂载路径）
docker volume inspect nginx-data
# 4. 容器挂载命名卷
docker run -d --name nginx-demo -v nginx-data:/usr/share/nginx/html nginx:stable-alpine
# 5. 删除指定数据卷
docker volume rm nginx-data
# 6. 清理所有未被使用的卷（谨慎执行，会删除无容器关联的卷数据）
docker volume prune
```

### 5.4 网络核心操作

```
# 1. 查看所有Docker网络
docker network ls
# 2. 创建自定义桥接网络（生产推荐，同网络内容器可通过名称互通，隔离性好）
docker network create my-app-net
# 3. 容器加入自定义网络
docker run -d --name nginx-demo --network my-app-net nginx:stable-alpine
docker run -d --name redis-demo --network my-app-net redis:8-alpine
# 此时nginx容器内可直接通过容器名redis-demo访问redis服务，无需IP
# 4. 查看网络详情
docker network inspect my-app-net
# 5. 容器断开/连接网络
docker network disconnect my-app-net nginx-demo
docker network connect my-app-net nginx-demo
# 6. 清理无用网络
docker network prune
```

### 5.5 系统资源清理

```
# 一键清理未使用的容器、网络、悬空镜像（安全推荐）
docker system prune -f
# 深度清理：删除所有未被容器使用的镜像、卷、网络（谨慎执行，生产环境慎用）
docker system prune -a --volumes
# 查看Docker磁盘占用详情
docker system df
```

------

## 六、进阶核心 1：Dockerfile 自定义镜像构建

Dockerfile 是构建自定义镜像的标准化文本文件，通过指令定义镜像的每一层，实现可复现、可版本化的镜像构建。

### 6.1 核心指令速查表

|     指令     |                    核心作用                    |                           最佳实践                           |
| :----------: | :--------------------------------------------: | :----------------------------------------------------------: |
|    `FROM`    | 指定基础镜像，所有 Dockerfile 必须以 FROM 开头 |  优先使用官方 alpine 精简镜像，固定版本号，禁止使用 latest   |
|  `WORKDIR`   |   指定容器内工作目录，后续指令均在此目录执行   |   禁止用 cd 切换目录，统一用 WORKDIR，自动创建不存在的目录   |
|    `COPY`    |          复制主机文件 / 目录到镜像内           |           优先使用 COPY，仅自动解压 tar 包时用 ADD           |
|    `RUN`     |  构建镜像时执行的命令，用于安装依赖、编译代码  | 合并多条 RUN 指令，减少镜像层数；执行完清理缓存，减小镜像体积 |
|    `ENV`     |         设置环境变量，容器运行时可读取         |             用于配置应用环境，避免硬编码敏感信息             |
|   `EXPOSE`   |               声明容器监听的端口               |       仅为声明，不自动端口映射，需配合 run 的 - p 参数       |
|    `CMD`     |            容器启动时执行的默认命令            |  一个 Dockerfile 仅一个 CMD 生效，可被 docker run 命令覆盖   |
| `ENTRYPOINT` |                容器启动入口命令                |    与 CMD 配合，ENTRYPOINT 写固定启动命令，CMD 传默认参数    |
|    `USER`    |                切换容器运行用户                |           生产环境必须使用非 root 用户，提升安全性           |
|   `VOLUME`   |               声明容器持久化目录               |               提醒用户挂载数据卷，避免数据丢失               |

### 6.2 企业级多阶段构建示例（Go 应用）

多阶段构建是镜像优化的核心，分离构建环境与运行环境，仅保留运行所需文件，镜像体积可减少 90% 以上。

```
# 第一阶段：构建阶段，包含完整编译环境，用于代码编译
FROM golang:1.22-alpine AS builder
# 指定工作目录
WORKDIR /app
# 先复制go.mod/go.sum，利用Docker层缓存，依赖不变时无需重新下载
COPY go.mod go.sum ./
# 下载依赖
RUN go mod download
# 复制源码
COPY . .
# 静态编译，生成无依赖的二进制文件
RUN CGO_ENABLED=0 GOOS=linux go build -o main .

# 第二阶段：运行阶段，仅保留最小运行环境，生产镜像
FROM alpine:latest
# 维护者信息
MAINTAINER your-name <your-email@example.com>
# 安装必要工具
RUN apk --no-cache add ca-certificates tzdata
# 设置时区
ENV TZ=Asia/Shanghai
# 指定工作目录
WORKDIR /app
# 创建非root用户
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
# 从构建阶段复制编译好的二进制文件
COPY --from=builder /app/main .
# 授权非root用户
RUN chown -R appuser:appgroup /app
# 切换到非root用户
USER appuser
# 声明服务端口
EXPOSE 8080
# 健康检查
HEALTHCHECK --interval=30s --timeout=3s --retries=3 CMD wget -q -O /dev/null http://localhost:8080/health || exit 1
# 容器启动入口
ENTRYPOINT ["./main"]
```

### 镜像构建与运行

```
# 构建镜像，-t 指定镜像名和标签，. 表示当前目录为构建上下文
docker build -t my-spring-app:v1.0 .

# 运行构建好的镜像
docker run -d --name spring-app -p 8080:8080 my-spring-app:v1.0
```

### 6.3 .dockerignore 配置

作用：排除构建上下文中的无用文件，减小构建体积，加快构建速度，避免敏感文件泄露。

```
# 版本控制与文档
.git
.gitignore
*.md
README*
# 构建相关
Dockerfile
.dockerignore
# 编译产物与依赖缓存
target/
node_modules/
vendor/
# 日志与临时文件
logs/
*.log
tmp/
# 敏感文件
.env
*.pem
*.key
id_rsa*
```

### 6.4 镜像构建与运行

```
# 构建镜像，-t 指定镜像名和标签，. 为构建上下文
docker build -t my-go-app:v1.0 .
# 运行构建好的镜像
docker run -d --name go-app -p 8080:8080 my-go-app:v1.0
```

------

## 七、进阶核心 2：Docker Compose 多容器编排

Docker Compose 是官方多容器编排工具，通过`compose.yml`文件一键定义、启动、管理多个关联容器，替代繁琐的 docker run 命令，是微服务、多应用部署的核心工具。

> 重要说明：Compose V1（docker-compose，横杠分隔）已停止维护，本文档均使用 V2 版本（docker compose，空格分隔），已内置到 Docker 官方安装包中。

### 7.1 安装与验证

```
# 验证是否已安装（Docker Desktop/官方安装包默认自带）
docker compose version

# 备用手动安装（v5.1.1最新稳定版，Linux x86_64）
mkdir -p ~/.docker/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v5.1.1/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
```

### 7.2 完整可运行示例（前后端 + MySQL+Redis）

```
# compose.yml
version: '3.8'

# 服务定义：每个服务对应一个容器
services:
  # 前端服务：Nginx
  frontend:
    image: nginx:stable-alpine
    container_name: app-frontend
    ports:
      - "80:80"
    volumes:
      - ./frontend/html:/usr/share/nginx/html:ro
      - ./frontend/nginx.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - backend
    restart: unless-stopped
    networks:
      - app-net

  # 后端服务：Go/Java应用
  backend:
    build: ./backend  # 后端目录下的Dockerfile
    container_name: app-backend
    ports:
      - "8080:8080"
    environment:
      - DB_HOST=mysql
      - DB_PORT=3306
      - DB_USER=root
      - DB_PASSWORD=root123456
      - DB_NAME=app_db
      - REDIS_HOST=redis
      - REDIS_PORT=6379
      - REDIS_PASSWORD=redis123456
    depends_on:
      - mysql
      - redis
    restart: unless-stopped
    networks:
      - app-net

  # 数据库服务：MySQL
  mysql:
    image: mysql:8.4
    container_name: app-mysql
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=root123456
      - MYSQL_DATABASE=app_db
      - TZ=Asia/Shanghai
    volumes:
      - mysql-data:/var/lib/mysql  # 命名卷持久化数据
      - ./mysql/init.sql:/docker-entrypoint-initdb.d/init.sql:ro  # 初始化脚本
    command: --default-authentication-plugin=mysql_native_password
    restart: unless-stopped
    networks:
      - app-net

  # 缓存服务：Redis
  redis:
    image: redis:8-alpine
    container_name: app-redis
    ports:
      - "6379:6379"
    command: ["redis-server", "--appendonly", "yes", "--requirepass", "redis123456"]
    volumes:
      - redis-data:/data
    restart: unless-stopped
    networks:
      - app-net

# 全局命名卷：持久化数据，容器删除后数据不丢失
volumes:
  mysql-data:
  redis-data:

# 全局自定义网络：同网络内服务可通过服务名互通
networks:
  app-net:
    driver: bridge
```

|       配置项       |                           核心作用                           |
| :----------------: | :----------------------------------------------------------: |
|     `services`     | 定义服务集合，每个服务对应一个容器，可配置镜像、构建、端口、挂载等 |
|      `image`       |                      指定服务使用的镜像                      |
|      `build`       |             指定 Dockerfile 路径，构建自定义镜像             |
|      `ports`       |                端口映射，`主机端口:容器端口`                 |
|     `volumes`      |                数据挂载，支持绑定挂载、命名卷                |
|   `environment`    |                         环境变量配置                         |
|    `depends_on`    |           服务依赖，控制启动顺序，先启动依赖的服务           |
|     `networks`     |       指定服务加入的网络，同网络内服务可通过服务名互通       |
|     `restart`      |       重启策略，`always`/`unless-stopped`（生产推荐）        |
|     `command`      |                    覆盖容器启动的默认命令                    |
| `volumes`（顶层）  |               定义全局命名卷，可被多个服务共用               |
| `networks`（顶层） |                      定义全局自定义网络                      |

### 7.3 高频核心命令

所有命令需在`compose.yml`所在目录执行

|               命令                |                          核心作用                           |
| :-------------------------------: | :---------------------------------------------------------: |
|      `docker compose up -d`       |               一键启动所有服务，`-d` 后台运行               |
|  `docker compose up -d --build`   |               启动前重新构建自定义镜像的服务                |
|      `docker compose config`      |          检查 compose.yml 语法是否正确（排错首选）          |
|        `docker compose ps`        |               查看当前项目所有服务的运行状态                |
|     `docker compose logs -f`      |                   实时查看所有服务的日志                    |
|  `docker compose logs -f 服务名`  |             实时查看指定服务的日志（排错核心）              |
|       `docker compose stop`       |               停止所有服务，不删除容器和数据                |
|      `docker compose start`       |                    启动已停止的所有服务                     |
|     `docker compose restart`      |                        重启所有服务                         |
|       `docker compose down`       |            停止并删除所有容器、网络，保留数据卷             |
|     `docker compose down -v`      |        停止并删除所有容器、网络、数据卷（谨慎使用）         |
| `docker compose exec 服务名 终端` | 进入指定服务的容器，如`docker compose exec mysql /bin/bash` |

------

## 八、高阶拓展与企业级能力

### 8.1 网络模式深度解析与场景选型

|      网络模式      |                           核心特点                           |                    适用场景                     |
| :----------------: | :----------------------------------------------------------: | :---------------------------------------------: |
| bridge（默认桥接） |       宿主机独立网桥，容器通过网桥互通，需手动端口映射       |            单机单容器、开发测试环境             |
|        host        |  共享宿主机网络栈，端口直接绑定宿主机，无网络隔离，性能最优  |   极致性能需求场景，如网关、数据库、压测服务    |
|        none        |       完全无网络，容器彻底隔离，无法访问外网和其他容器       |     高安全要求的离线计算、敏感数据处理场景      |
|     container      | 共享另一个容器的网络命名空间，两个容器可通过[localhost](https://localhost)互通 | 边车 Sidecar 模式，如 Istio、日志代理、监控采集 |
|      overlay       |    跨主机分布式网络，实现多台 Docker 主机上的容器二层互通    |      Docker Swarm 集群、多节点容器部署场景      |

### 8.2 镜像极致优化技巧

1. **多阶段构建**：分离构建与运行环境，仅保留运行时必需文件，镜像体积可减少 80%+

2. **优选基础镜像**：优先使用 alpine、distroless 精简镜像，避免使用 full 完整版镜像；如 nginx:alpine 比 nginx:latest 小 70%

3. **最大化利用构建缓存**：将变更频率低的指令（依赖安装）放在 Dockerfile 前面，变更频率高的指令（代码复制）放在后面

4. 合并 RUN 指令：多条 RUN 命令合并为一条，减少镜像层数，同时执行完清理缓存

   ```
   # 反例：多层RUN，产生多余镜像层，缓存未清理
   RUN apt-get update
   RUN apt-get install -y curl
   # 正例：合并RUN，执行完清理缓存
   RUN apt-get update && apt-get install -y curl \
       && rm -rf /var/lib/apt/lsts/*
   ```

5. **移除不必要的工具**：生产镜像不安装编译、调试工具，减少攻击面和镜像体积

6. **使用.dockerignore**：排除无用文件，减小构建上下文大小

### 8.3 跨平台镜像构建（Buildx）

Docker Buildx 支持一次构建，生成支持多架构（amd64、arm64、arm 等）的镜像，适配 X86 服务器、树莓派、Mac M 系列芯片等不同架构。

```
# 1. 创建并启用buildx构建器
docker buildx create --name multiarch-builder --use
# 2. 启动构建器
docker buildx inspect multiarch-builder --bootstrap
# 3. 构建多架构镜像并推送到仓库
docker buildx build --platform linux/amd64,linux/arm64 -t my-registry.com/my-app:v1.0 --push .
```

### 8.4 私有镜像仓库搭建

#### 8.4.1 轻量级 Registry（单机测试 / 小型团队）

```
# 一键启动私有仓库
docker run -d \
  --name private-registry \
  -p 5000:5000 \
  -v registry-data:/var/lib/registry \
  --restart unless-stopped \
  registry:2

# 镜像打标签、推送、拉取
docker tag nginx:alpine localhost:5000/nginx:alpine
docker push localhost:5000/nginx:alpine
docker pull localhost:5000/nginx:alpine
```

#### 8.4.2 企业级 Harbor

Harbor 是 VMware 开源的企业级镜像仓库，支持权限管理、镜像漏洞扫描、多租户、镜像同步、审计日志等功能，是生产环境首选。

- 官方地址：https://goharbor.io/

### 8.5 CI/CD 集成方案

Docker 是 CI/CD 的核心组件，可实现代码提交后自动构建镜像、测试、推送仓库、部署生产，主流集成方案：

1. **GitHub Actions**：GitHub 自带 CI/CD 能力，通过 workflow 文件定义镜像构建、推送、部署全流程
2. **GitLab CI/CD**：在`.gitlab-ci.yml`中定义流水线，代码提交后自动执行
3. **Jenkins**：企业级 CI/CD 工具，通过 Pipeline 实现 Docker 镜像的全生命周期管理

核心流水线流程：代码提交 → 拉取代码 → 单元测试 → Dockerfile 构建镜像 → 镜像漏洞扫描 → 推送到私有仓库 → 自动部署到测试 / 生产环境

------

### 数据持久化方案深度对比

| 挂载类型 | 生命周期                   | 可移植性                 | 性能 | 适用场景                         |
| -------- | -------------------------- | ------------------------ | ---- | -------------------------------- |
| 命名卷   | Docker 管理，独立于容器    | 高，无需关注主机路径     | 高   | 生产环境数据库、中间件数据持久化 |
| 绑定挂载 | 主机管理，与主机路径强绑定 | 低，不同主机路径可能不同 | 中   | 开发环境配置文件、代码热更新     |
| tmpfs    | 内存存储，容器停止即销毁   | 无                       | 极高 | 敏感临时数据、高性能临时缓存     |

## 九、生产环境安全加固（必备）

|        安全实践         |                        实施方式与示例                        |
| :---------------------: | :----------------------------------------------------------: |
|       运行时降权        | Dockerfile 中通过 USER 指令切换到非 root 用户，禁止使用 root 用户运行容器 |
|      禁用特权模式       | 禁止使用`--privileged`参数，避免容器获得宿主机 root 权限，防止容器逃逸 |
|      只读文件系统       | 容器启动时添加`--read-only`参数，将容器文件系统设为只读，防止恶意篡改 |
| 限制 Linux Capabilities | 禁用所有默认权限，仅添加必需权限：`--cap-drop ALL --cap-add NET_ADMIN` |
|       资源硬限制        | 限制容器 CPU、内存使用，防止资源耗尽攻击：`--memory="512m" --cpus="1.0" --memory-swap="512m"` |
|      禁用权限提升       | 添加`--security-opt=no-new-privileges:true`，禁止容器内进程提升权限 |
|      启用安全模块       | 启用 seccomp/AppArmor 配置，限制容器可用的系统调用，减少攻击面 |
|      镜像安全扫描       | 使用 Trivy 等工具扫描镜像漏洞：`trivy image --severity CRITICAL myimage:latest` |
|      健康检查配置       |   配置容器健康检查，自动检测服务可用性，异常时自动重启容器   |
|      敏感信息管理       | 禁止在 Dockerfile、镜像中硬编码密码、密钥，通过环境变量、密钥管理工具注入 |

**安全运行完整示例**

```
docker run -d \
  --name secure-app \
  --read-only \
  --cap-drop ALL \
  --cap-add NET_ADMIN \
  --memory="512m" \
  --cpus="0.5" \
  --memory-swap="512m" \
  --security-opt=no-new-privileges:true \
  --restart unless-stopped \
  myapp:v1.0
```

------

## 十、2026 年稳定版常用镜像推荐

|    组件    |       推荐镜像标签       |                       核心说明                        |
| :--------: | :----------------------: | :---------------------------------------------------: |
|   Nginx    |   nginx:stable-alpine    | stable 分支 1.28.x，安全补丁及时，alpine 变体极致轻量 |
|   Redis    |      redis:8-alpine      |       8.4 稳定分支，alpine 变体体积小，性能稳定       |
| PostgreSQL |    postgres:18-alpine    |     最新 LTS 版本 18 系列，alpine 变体体积约 60MB     |
|   MySQL    |        mysql:8.4         |        官方 LTS 版本，生产环境首选，兼容性最佳        |
|  Node.js   |     node:lts-alpine      |  自动跟随当前 LTS 版本（24.x），适合前端 / Node 服务  |
|     Go     |    golang:1.22-alpine    |        最新稳定版编译环境，适合多阶段构建使用         |
|  Grafana   |  grafana/grafana:12.3.0  |           最新稳定版 12.3.x，监控可视化首选           |
| Fluent Bit | fluent/fluent-bit:stable |    distroless 基础镜像，极致轻量安全，日志采集首选    |

> 标签策略：开发 / 测试环境可用 stable/lts 标签；生产环境必须固定具体版本号（如 postgres:18.1-alpine），配合 Renovate/Dependabot 实现自动化安全更新。

------

## 十一、高频故障排查速查

|             问题现象             |                    核心排查方向与解决方案                    |                                                              |
| :------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|  API 版本不兼容，第三方工具失效  | v29 版本最低 API 版本要求 1.44，升级 Watchtower、Portainer 等工具至最新稳定版 |                                                              |
|       拉取镜像超时 / 失败        | 检查镜像加速配置是否生效；使用 DaoCloud 前缀替换方案拉取海外镜像；检查宿主机 DNS 与网络连通性 |                                                              |
|        容器启动后立即退出        | 核心原因：容器内无前台进程运行，CMD/ENTRYPOINT 命令执行完成后退出；解决方案：修改启动命令为前台运行，如 nginx 用`nginx -g "daemon off;"` |                                                              |
|     端口映射失败 / 端口冲突      |                      用 `netstat -tlnp                       | grep 端口号 ` 查看宿主机端口是否被占用，更换主机端口；确认容器内服务确实监听了映射的端口 |
|          容器内数据丢失          | 容器是无状态的，必须通过命名卷 / 绑定挂载持久化数据，容器删除后内部数据会全部丢失 |                                                              |
|          容器间无法通信          | 确认容器加入同一个自定义桥接网络，通过容器名 / 服务名访问，不要使用[localhost](https://localhost)或容器 IP（容器重启 IP 会变化） |                                                              |
|       Docker 服务启动失败        | 用`journalctl -u docker`查看服务日志；检查`daemon.json`配置文件语法是否正确；确认端口未被占用 |                                                              |
| 权限被拒绝，无法执行 docker 命令 | 检查当前用户是否加入 docker 组，执行`groups`命令查看；重新执行`sudo usermod -aG docker $USER && newgrp docker` |                                                              |
|           磁盘空间不足           | 用`docker system df`查看 Docker 磁盘占用；执行`docker system prune`清理无用资源；检查容器日志是否未配置轮转，占用大量磁盘 |                                                              |
|           容器频繁重启           | 用`docker logs 容器名`查看应用退出原因；检查健康检查配置是否合理；确认资源限制是否过小，导致应用被 OOM 杀死 |                                                              |

------

## 十二、学习路径建议

1. **入门阶段**：掌握`run/pull/ps/exec/logs/stop/rm`等基础命令，理解镜像与容器的核心区别，能独立运行常用中间件容器
2. **进阶阶段**：学习 Dockerfile 编写、多阶段构建、Docker Compose 多容器编排，掌握数据卷与网络配置，能独立部署完整的前后端应用
3. **生产实践阶段**：掌握镜像优化、安全加固、资源限制、健康检查、日志监控，能完成生产环境的标准化部署与运维
4. **生态扩展阶段**：学习 Kubernetes 容器编排、Docker Scout 镜像安全扫描、BuildKit 高级构建、CI/CD 流水线集成，适配企业级微服务架构