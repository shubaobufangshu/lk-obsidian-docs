# GitHub Actions本地调试工具act - 完整安装与使用指南

## 一、工具简介
act是一款用于本地运行GitHub Actions的开源工具，由Nektos公司开发。它允许开发者在无需将代码推送到GitHub的情况下，在本地调试和测试GitHub Actions工作流，大幅缩短开发周期。

### 核心特性
- 🚀 完全兼容GitHub Actions语法
- 🐳 基于Docker容器化运行
- 🔧 支持所有官方和社区Actions
- ⚡ 本地调试，避免远程CI等待
- 💾 缓存机制加速重复执行
- 🎯 支持矩阵构建、秘密变量等高级功能

## 二、安装方法

### 方法一：使用包管理器安装

#### 1. macOS（Homebrew）
```bash
brew install act
```

#### 2. Linux（Snap）
```bash
sudo snap install act --classic
```

#### 3. Windows（Chocolatey）
```powershell
choco install act-cli
```

### 方法二：脚本安装（跨平台）
```bash
# 安装最新版本
curl -s https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash

# 安装指定版本（推荐）
curl -s https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash -s v0.2.69
```

### 方法三：手动下载二进制文件
1. 访问GitHub Releases页面：https://github.com/nektos/act/releases
2. 下载对应平台的二进制文件
3. 解压后将act可执行文件添加到系统PATH

### 验证安装
```bash
act --version
# 输出版本信息表示安装成功
# act version 0.2.69
```

## 三、基本使用

### 1. 首次配置
首次运行act时，会提示选择Docker镜像大小：
```bash
act
```
- **Large镜像**：包含完整的GitHub Actions运行环境（推荐，约20GB）
- **Medium镜像**：基础环境，不含大部分工具链（约2GB）
- **Micro镜像**：最小化环境，仅包含必要组件（约500MB）

### 2. 基本命令

#### 列出所有工作流
```bash
act -l
```

#### 运行默认工作流
```bash
act
```

#### 运行指定工作流
```bash
act -W .github/workflows/build.yml
```

#### 运行指定事件触发的工作流
```bash
# 模拟push事件
act push

# 模拟pull_request事件
act pull_request

# 手动触发工作流
act workflow_dispatch
```

#### 运行指定Job
```bash
act -j build
```

#### 查看详细日志
```bash
act -v
```

#### 试运行不执行实际步骤
```bash
act -n
```

## 四、高级功能

### 1. 环境变量与秘密变量

#### 使用环境变量
```bash
act -e env_vars.json
```

env_vars.json示例：
```json
{
  "GITHUB_ENV_VAR": "value"
}
```

#### 使用秘密变量
```bash
# 从文件读取秘密
act --secret-file secrets.env

# 直接传递秘密
act -s GITHUB_TOKEN=your_token_here
```

secrets.env示例：
```env
GITHUB_TOKEN=ghp_abc123
AWS_ACCESS_KEY_ID=AKIAXYZ
```

### 2. 矩阵构建支持
act完全支持GitHub Actions的矩阵构建功能，会自动运行所有矩阵组合：
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16.x, 18.x, 20.x]
    steps:
    - uses: actions/checkout@v4
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
```

### 3. 自定义运行器镜像
```bash
# 使用自定义Docker镜像
act -P ubuntu-latest=my-custom-image:latest

# 永久配置自定义镜像
act --add-host ubuntu-latest=my-custom-image:latest
```

### 4. 缓存优化
act会自动缓存Docker镜像和构建产物，加速后续运行：
```bash
# 查看缓存
act --cache list

# 清理缓存
act --cache clean
```

## 五、常见问题与解决方案

### 1. Docker连接问题
```bash
# 确保Docker服务正在运行
sudo systemctl start docker

# 将当前用户添加到docker组
sudo usermod -aG docker $USER
# 重新登录生效
```

### 2. 工作流执行失败
```bash
# 查看详细错误日志
act -v

# 单独运行失败的Job
act -j failed_job_name -v
```

### 3. 镜像拉取过慢
```bash
# 使用国内镜像源
act --container-options "--registry-mirror=https://registry.docker-cn.com"
```

### 4. 内存不足问题
```bash
# 增加Docker内存限制
# 或使用micro镜像
act -P ubuntu-latest=nektos/act-environments-micro:latest
```

## 六、工作流最佳实践

### 1. 本地调试优先
- 在推送代码前，先用act本地验证工作流
- 避免频繁推送代码调试CI

### 2. 使用缓存加速
```yaml
steps:
- uses: actions/cache@v3
  with:
    path: |
      node_modules
      ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
    restore-keys: |
      ${{ runner.os }}-node-
```

### 3. 分层构建
将工作流拆分为多个Job，提高并行度：
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - run: npm test
  
  build:
    runs-on: ubuntu-latest
    needs: test  # 等待test Job完成
    steps:
    - uses: actions/checkout@v4
    - run: npm run build
```

## 七、与GitHub Actions的差异
虽然act尽可能兼容GitHub Actions，但仍存在一些细微差异：
1. 部分GitHub专属Actions可能无法在本地运行
2. 本地网络环境可能与GitHub托管运行器不同
3. 某些系统级功能可能有限制

## 八、贡献与支持
- GitHub仓库：https://github.com/nektos/act
- 文档站点：https://nektosact.com/
- 社区讨论：https://github.com/nektos/act/discussions
- 问题反馈：https://github.com/nektos/act/issues

## 九、版本更新
```bash
# 检查更新
act --version

# 使用脚本更新
curl -s https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash
```

---
**最后更新时间**：2026年6月1日
**文档版本**：v1.0

希望这份指南能帮助你充分利用act工具提升开发效率！如有疑问，欢迎在GitHub仓库提交issue或参与社区讨论。