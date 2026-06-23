# Act Runner

## 基础

本文介绍 Gitea Actions 的执行器：**act runner**。

### 运行要求

支持两种模式：

1. **Docker 内运行**（推荐）
2. **宿主机直接运行**

- 推荐在 Docker 中执行任务，需先安装 Docker 并确保守护进程正常运行。
- 其他兼容 Docker API 的 OCI 容器引擎也可用，但未测试。
- 若只在宿主机直接执行任务，则无需 Docker。

---

### 安装方式

#### 1. 二进制安装

##### 下载

- 稳定版：从 [Release 页面](https://gitea.com/gitea/act_runner/releases) 下载
- 每日构建版：从下载页面获取

选择对应平台版本。

Unix 类系统授权并查看版本：

```
chmod +x act_runner
./act_runner --version
```

> 显示版本号即成功。

---

### 获取注册令牌

Runner 可在三个级别注册：

1. **实例级（全局）**：为实例下所有仓库执行任务
2. **组织级**：为组织下所有仓库执行任务
3. **仓库级**：只为当前仓库执行任务

> 即使仓库有自己的 runner，实例/组织级 runner 仍可用。

#### 令牌获取位置

- 实例级：`https://你的Gitea地址/-/admin/actions/runners`
- 组织级：`https://你的Gitea地址/组织名/settings/actions/runners`
- 仓库级：`https://你的Gitea地址/用户名/仓库名/settings/actions/runners`

也可用 Gitea 命令行生成：

```bash
gitea --config /etc/gitea/app.ini actions generate-runner-token
```

环境变量设置全局令牌：

```bash
GITEA_RUNNER_REGISTRATION_TOKEN
GITEA_RUNNER_REGISTRATION_TOKEN_FILE
```

---

### 配置文件（可选）

生成默认配置：

```bash
./act_runner generate-config > config.yaml
```

默认配置基本无需修改。

---

### 注册 Runner

必须先注册，让 runner 知道拉取任务的位置。

交互式注册：

```bash
./act_runner register
# 或指定配置文件
./act_runner --config config.yaml register
```

依次输入：

- Gitea 实例地址（http/https）
- 注册令牌
- Runner 名称（可选，默认主机名）
- Runner 标签（可选）

#### 非交互式注册

```bash
./act_runner register --no-interactive \
  --instance http://192.168.1.111:3000 \
  --token 你的令牌 \
  --name my-runner \
  --labels "ubuntu-latest:docker://node:16-bullseye"
```

注册后生成 `.runner` 文件，请勿手动编辑。

---

### 临时 Runner（Ephemeral）

安全增强模式：

- 每个 runner 只执行**一个任务**后销毁
- 执行后凭证自动失效
- 需要 act_runner ≥0.2.12

注册临时 runner：

```bash
./act_runner register --ephemeral
```

---

### 启动 Runner

#### 命令行启动

```bash
./act_runner daemon
# 或
./act_runner daemon --config config.yaml
```

#### systemd 服务（Linux）

创建 `/etc/systemd/system/act_runner.service`

```ini
[Unit]
Description=Gitea Actions Runner
After=network.target docker.service
Requires=docker.service

[Service]
User=你的用户名
ExecStart=/home/用户名/act_runner daemon
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

然后：

```bash
systemctl daemon-reload
systemctl enable --now act_runner
```

#### LaunchDaemon（macOS）

创建 plist 文件并加载：

```
sudo launchctl load ...
```

---

### 使用 Docker 镜像运行（最常用）

#### 拉取镜像

```bash
docker pull gitea/act_runner:latest
```

#### 一键注册+启动（推荐）

```bash
docker run -d \
  -e GITEA_INSTANCE_URL=http://192.168.1.111:3000 \
  -e GITEA_RUNNER_REGISTRATION_TOKEN=你的令牌 \
  -e GITEA_RUNNER_NAME=my-runner \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name my_act_runner \
  gitea/act_runner:latest
```

关键点：

- 必须挂载 `/var/run/docker.sock` 才能在容器内启动 Docker 任务
- 不挂载则只能在 runner 容器内直接运行（不推荐）

#### Docker Compose

```yaml
version: "3.8"
services:
  runner:
    image: gitea/act_runner:latest
    environment:
      GITEA_INSTANCE_URL: http://192.168.1.111:3000
      GITEA_RUNNER_REGISTRATION_TOKEN: 你的令牌
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

启动：`docker-compose up -d`

---

### 标签（Labels）作用

格式：

```
标签名:docker://镜像名
或
标签名:host
```

示例：

```
ubuntu-22.04:docker://node:16-bullseye
```

含义：

- workflow 中 `runs-on: ubuntu-22.04`
- 使用该 runner
- 在 `node:16-bullseye` 容器中执行任务

默认标签：

```
ubuntu-latest:docker://node:16-bullseye,
ubuntu-22.04:docker://node:16-bullseye,...
```

若直接在宿主机运行：

```
ubuntu-22.04:host
```

---

### 缓存配置（actions/cache）

若 workflow 使用缓存，需在 `config.yaml` 中配置：

```yaml
cache:
  enabled: true
  host: 192.168.x.x # 宿主机局域网IP
  port: 8088
```

并映射端口：

```bash
-p 8088:8088
```

### 验证 Runner 运行状态

访问 Gitea 的 Runner 管理页面（例如实例级：`https://你的Gitea地址/-/admin/actions/runners`），看到 Runner 状态为「在线」即成功。

---

### 核心注意事项

1. **Docker 依赖**：若 Runner 需执行 Docker 类型任务，必须确保 Docker 守护进程运行（`sudo systemctl start docker`），且二进制启动用户有权限（`sudo usermod -aG docker 用户名` 并重启终端）。
2. **令牌安全**：注册令牌勿泄露，`.runner` 文件需妥善保管。
3. **网络**：Gitea 实例地址必须能被 Runner 访问，防火墙放行对应端口（如 3000）。
4. **标签匹配**：workflow 中 `runs-on` 需与 Runner 注册时的标签一致。

---

### 常用运维命令

```bash
# 查看 systemd 日志
sudo journalctl -u act_runner -f

# 停止 / 重启 Runner（systemd）
sudo systemctl stop act_runner
sudo systemctl restart act_runner

# Docker 方式查看日志
docker logs -f my_act_runner
```

---

## 工作流配置示例解析

以下是一个完整的 Gitea Actions 工作流配置文件（`.gitea/workflows/hello.yaml`）的解析：

```yaml
name: Hello World Workflow # 工作流名称，显示在 Gitea Actions 页面

on: [push] # 触发条件：每次推送代码时执行

jobs:
  hello-job:
    name: Say Hello
    runs-on: ubuntu-latest # 必须与 Runner 注册的标签一致
    steps:
      - name: Checkout code
        uses: actions/checkout@v4 # 拉取仓库代码到 Runner
      - name: Print Hello
        run: echo "Hello from Gitea Actions! 🎉"
      - name: Show current directory
        run: pwd && ls -la # 显示当前目录及文件
```

#### 执行流程

1. 代码推送到仓库 → 触发工作流。
2. Gitea 查找标签为 `ubuntu-latest` 的在线 Runner。
3. Runner 依次执行：
   - 拉取代码；
   - 打印欢迎信息；
   - 显示目录内容。
4. 在 Gitea 仓库的「Actions」页面可查看每个步骤的日志和结果

### 一、工作流文件基础

#### 1. 文件存放与命名

工作流是 GitHub Actions 中的可配置自动化过程，由一个或多个 Job 组成，由特定事件触发。每个工作流都是一个独立的 YAML 配置文件，必须存放在仓库的 `.github/workflows` 目录下。文件名必须以 `.yml` 或 `.yaml` 结尾，可以包含多个 YAML 文件，每个文件对应一个独立的工作流。

#### 2. 目录结构示例

```yaml
项目根目录/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml        # 持续集成工作流
│   │   ├── deploy.yml    # 部署工作流
│   │   └── ...           # 其他工作流文件
│   ├── ISSUE_TEMPLATE/   # Issue模板（可选）
│   └── PULL_REQUEST_TEMPLATE/  # PR模板（可选）
├── src/                  # 源代码目录
├── tests/                # 测试目录
└── ...                   # 其他项目文件
```

---

### 🔧 二、核心语法详解

#### 1. `name` — 工作流名称

工作流的名称。GitHub 会在仓库的 Actions 选项卡下显示你定义的工作流名称。

```yaml
name: CI/CD Pipeline
```

#### 2. `run-name` — 工作流运行名称

工作流运行的名称，由工作流生成。GitHub 会在仓库的 Actions 选项卡的工作流运行列表中显示工作流运行名称。此值可以包含表达式，并可以引用 `github` 和 `inputs` 上下文。

```yaml
run-name: Deploy to $&#123;&#123; inputs.deploy_target &#125;&#125; by @$&#123;&#123; github.actor &#125;&#125;
```

如果省略 `run-name` 或仅包含空格，则运行名称将设置为工作流运行的特定于事件的信息。例如，对于由 `push` 或 `pull_request` 事件触发的工作流，它将设置为提交消息或拉取请求的标题。

#### 3. `on` — 触发器

`on` 是 GitHub Actions 的“启动器”——定义这个工作流应该在什么事件发生时启动。

##### 3.1 单事件触发

```yaml
on: push
```

##### 3.2 多事件触发

```yaml
on: [push, fork]
```

如果指定多个事件，只需要发生其中一个事件即可触发工作流。如果多个触发事件同时发生，则会触发多个工作流运行。

##### 3.3 带条件的事件过滤

可以组合多个事件，并利用 `branches`、`paths`、`tags` 等过滤器进行更精细的控制。

```yaml
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
    paths-ignore:
      - "docs/**"
```

##### 3.4 使用活动类型

某些事件具有活动类型，可以让你更好地控制工作流何时运行。使用 `on.<event_name>.types` 定义将触发工作流运行的事件活动类型。

```yaml
on:
  label:
    types:
      - created
      - edited
      - deleted
```

##### 3.5 常用事件类型

| 事件                | 说明                                   |
| :------------------ | :------------------------------------- |
| `push`              | 代码推送时触发                         |
| `pull_request`      | PR 相关事件                            |
| `workflow_dispatch` | 手动触发（需要在 GitHub 界面点击按钮） |
| `schedule`          | 定时触发（cron）                       |
| `release`           | 发布事件                               |
| `issues`            | Issue 事件                             |
| `watch`             | Star 事件                              |
| `fork`              | Fork 事件                              |

#### 4. `jobs` — 任务集合

Jobs 定义工作流中的任务组，可以有多个 Job 并行或顺序执行。

##### 4.1 基本结构

```yaml
jobs:
  build: # Job ID（自定义标识符）
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Hello World"
```

##### 4.2 `runs-on` — 运行环境

指定作业的运行器环境。常用值包括 `ubuntu-latest`、`windows-latest`、`macos-latest`，也可以指定版本如 `ubuntu-22.04`。

```yaml
runs-on: ubuntu-latest
```

##### 4.3 `needs` — 作业依赖

默认情况下，多个 Job 是并行执行的。使用 `needs:` 可以实现前后顺序。

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "构建中..."
  deploy:
    needs: build # 必须等 build 执行完
    runs-on: ubuntu-latest
    steps:
      - run: echo "部署中..."
```

#### 5. `steps` — 执行步骤

Steps 是构成 Job 的每一个步骤，按顺序执行。每个 Step 可以是一个 `run`（执行 shell 命令）或 `uses`（引用一个 Action）。

##### 5.1 两种步骤类型

```yaml
steps:
  - name: 使用已有Action
    uses: actions/checkout@v4 # 拉取代码

  - name: 执行shell命令
    run: echo "Hello, world!"
```

##### 5.2 `if` — 条件执行

使用 `if` 条件表达式，支持布尔逻辑，决定是否运行特定步骤。

```yaml
steps:
  - name: 仅在主分支运行
    run: echo "这是主分支"
    if: github.ref == 'refs/heads/main'
```

常用条件判断模式：

```yaml
# 基于分支
if: contains(github.ref, 'feature/')

# 基于事件类型
if: github.event_name == 'pull_request'

# 多条件组合
if: github.ref == 'refs/heads/main' && github.event_name == 'push'
```

##### 5.3 `with` — 输入参数

指明当前 Action 的输入参数，使用键/值对。

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: "20"
    cache: "npm"
```

---

### 🚀 三、高级语法与特性

#### 1. 矩阵策略（Matrix Strategy）

矩阵策略允许你定义一组变量组合，GitHub Actions 会为每个组合创建一个独立的作业运行。这让你可以在多个环境中高效运行相同的作业，无需重复工作流步骤。

##### 1.1 基本矩阵配置

```yaml
jobs:
  deploy:
    strategy:
      matrix:
        os: [ubuntu-latest, ubuntu-20.04, windows-latest]
        images: [hello-world, alpine]
    runs-on: $&#123;&#123; matrix.os &#125;&#125;
    steps:
      - name: Run image on $&#123;&#123; matrix.os &#125;&#125;
        run: docker run $&#123;&#123; matrix.images &#125;&#125;
```

GitHub Actions 会运行所有定义的组合（此处为 3 × 2 = 6 个作业），显著提高 CI/CD 吞吐量。

##### 1.2 排除特定组合（exclude）

使用 `exclude` 关键字来跳过某些不支持或不需要的组合。

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, ubuntu-20.04, windows-latest]
    images: [hello-world, alpine]
  exclude:
    - os: windows-latest
      images: alpine # 跳过 Windows + alpine 组合
```

排除不支持的组合有助于节省构建分钟数并避免可预测的失败。

##### 1.3 添加额外组合（include）

使用 `include` 来添加超出默认矩阵的自定义组合。

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, ubuntu-20.04, windows-latest]
    images: [hello-world, alpine]
  exclude:
    - os: windows-latest
      images: alpine
  include:
    - os: ubuntu-20.04
      images: amd64/alpine # 仅在 Ubuntu 20.04 上运行
```

##### 1.4 控制失败行为和并发

```yaml
strategy:
  fail-fast: false # 即使有失败也继续运行所有矩阵作业
  max-parallel: 2 # 限制同时运行的作业数为 2
  matrix:
    os: [ubuntu-latest, ubuntu-20.04, windows-latest]
    images: [hello-world, alpine]
```

| 选项           | 说明                                                                       |
| :------------- | :------------------------------------------------------------------------- |
| `matrix`       | 定义要组合的轴（如 OS、镜像）                                              |
| `exclude`      | 省略特定的组合                                                             |
| `include`      | 添加超出默认矩阵的自定义组合                                               |
| `fail-fast`    | `true`（默认）在第一次失败时取消所有作业；`false` 则无论失败都运行所有作业 |
| `max-parallel` | 限制同时运行的矩阵作业数量                                                 |

#### 2. 环境变量与 Secrets

GitHub Actions 支持读取环境变量和仓库的 Secret 值。

##### 2.1 使用环境变量

```yaml
env:
  GLOBAL_VAR: "全局值"

jobs:
  build:
    env:
      JOB_VAR: "作业值"
    steps:
      - name: 使用环境变量
        run: echo "$&#123;&#123; env.JOB_VAR &#125;&#125;"
```

环境变量的优先级为：步骤级 > 作业级 > 工作流级。

##### 2.2 使用 Secrets

```yaml
steps:
  - name: 使用Secret
    run: echo "$&#123;&#123; secrets.MY_SECRET_KEY &#125;&#125;"
```

#### 3. 上下文与表达式

上下文是预定义的对象，包含工作流运行、环境、事件、Secrets、作业、步骤等数据。在 GitHub Actions 中，可以使用 `$&#123;&#123; <expression> &#125;&#125;` 的方式来使用表达式。

##### 3.1 常用上下文

| 上下文    | 说明                   | 示例属性                                   |
| :-------- | :--------------------- | :----------------------------------------- |
| `github`  | 仓库、运行和事件详情   | `github.sha`、`github.event`、`github.ref` |
| `job`     | 作业级别的元数据       | `job.status`                               |
| `steps`   | 之前步骤的输出和状态   | `steps.step_id.outcome`                    |
| `runner`  | Runner 环境信息        | `runner.os`、`runner.arch`、`runner.name`  |
| `secrets` | 作业可用的加密 Secrets | `secrets.DOCKER_PASSWORD`                  |
| `env`     | 环境变量信息           | `env.VAR_NAME`                             |

##### 3.2 使用表达式

```yaml
steps:
  - name: 输出当前分支
    run: echo "当前分支是 $&#123;&#123; github.ref_name &#125;&#125;"

  - name: 仅当推送事件时运行
    if: github.event_name == 'push'
    run: echo "这是推送事件"
```

#### 4. 可复用工作流（Reusable Workflows）

可复用工作流可以避免在不同仓库中重复编写相同的工作流。其关键区别在于使用 `workflow_call` 触发器。

```yaml
# 可复用工作流定义（在模版仓库中）
on:
  workflow_call:
    inputs:
      env:
        required: true
        type: string
        default: "dev"
      working-directory:
        required: false
        type: string
        default: "."
```

在其他工作流中调用：

```yaml
jobs:
  call-reusable:
    uses: my-org/github-action-templates/.github/workflows/build.yml@main
    with:
      env: "production"
      working-directory: "./app"
```

**优点**包括提高开发效率、保持一致性、可维护性。

---

### 💡 四、实战示例与最佳实践

#### 1. 完整工作流示例：Node.js 项目的 CI/CD

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  workflow_dispatch: # 支持手动触发

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test

      - name: Build project
        run: npm run build

      - name: Upload artifacts
        uses: actions/upload-artifact@v4
        with:
          name: build-files
          path: dist/

  deploy:
    needs: build-and-test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v4
        with:
          name: build-files
          path: dist/

      - name: Deploy to Production
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: $&#123;&#123; secrets.AWS_ACCESS_KEY_ID &#125;&#125;
          aws-secret-access-key: $&#123;&#123; secrets.AWS_SECRET_ACCESS_KEY &#125;&#125;
          aws-region: us-east-1

      - name: Sync to S3
        run: aws s3 sync ./dist s3://my-app-bucket
```

#### 2. 多环境矩阵测试示例

```yaml
name: Multi-Environment Test

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [18, 20, 22]
      fail-fast: false
      max-parallel: 4
    runs-on: $&#123;&#123; matrix.os &#125;&#125;
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: $&#123;&#123; matrix.node-version &#125;&#125;
      - run: npm ci
      - run: npm test
```

#### 3. 最佳实践总结

| 实践                         | 说明                                               |
| :--------------------------- | :------------------------------------------------- |
| ✅ 使用矩阵策略              | 测试多个操作系统、语言版本或容器镜像，最大化并行度 |
| ✅ 使用缓存                  | 利用 `hashFiles()` 函数缓存依赖，加速构建          |
| ✅ 条件执行                  | 使用 `if` 表达式避免不必要的步骤执行               |
| ✅ 使用 Secrets 保护敏感信息 | 永远不要在日志中暴露 Secrets                       |
| ✅ 可复用工作流              | 将通用逻辑抽取为可复用工作流，保持 DRY 原则        |
| ✅ 上传构建产物              | 使用 `actions/upload-artifact` 在作业间共享数据    |
| ✅ 添加状态徽章              | 在 README 中显示工作流成功状态，提高项目透明度     |
| ✅ 避免冗余                  | 保持工作流文件简洁，避免重复代码                   |

---

### 🔍 五、快速参考表

| 字段                     | 必填 | 说明             |
| :----------------------- | :--- | :--------------- |
| `name`                   | 否   | 工作流名称       |
| `run-name`               | 否   | 工作流运行的名称 |
| `on`                     | 是   | 触发工作流的事件 |
| `jobs`                   | 是   | 定义作业集合     |
| `jobs.<job_id>.runs-on`  | 是   | 指定运行环境     |
| `jobs.<job_id>.steps`    | 是   | 定义执行步骤     |
| `jobs.<job_id>.needs`    | 否   | 设置作业依赖关系 |
| `jobs.<job_id>.if`       | 否   | 条件执行作业     |
| `jobs.<job_id>.strategy` | 否   | 矩阵策略配置     |
| `jobs.<job_id>.env`      | 否   | 作业级环境变量   |
| `steps[*].uses`          | 否\* | 引用 Action      |
| `steps[*].run`           | 否\* | 执行 shell 命令  |
| `steps[*].if`            | 否   | 条件执行步骤     |
| `steps[*].with`          | 否   | Action 输入参数  |

> \*每个步骤必须包含 `uses` 或 `run` 中的一个
