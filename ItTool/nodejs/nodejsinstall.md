# nodejs便携版安装使用教程
这个是一个便携式的nodejs程序，目的是为了方便将开发环境带到其他的计算机里面运行
## 安装步骤
### 一：将目录设置到环境变量
#### nodejs
```bash
cmd
:: cmd
set "NODE_DIR=%cd%"
:: 获取当前目录
npm config set prefix "%NODE_DIR%\node_global"
npm config set cache "%NODE_DIR%\node_cache"
:: 配置 npm
npm config get prefix
npm config get cache
:: 验证，如果输出的路径与配置路径一致，说明路径配置成功。
```
powershell
```powershell
# 获取当前目录
$nodeDir = Get-Location

# 配置 npm
npm config set prefix "$nodeDir\node_global"
npm config set cache "$nodeDir\node_cache"
npm config get prefix
npm config get cache
# 验证，如果输出的路径与配置路径一致，说明路径配置成功。
```
#### 系统环境变量
1. 新建系统变量
右键点击桌面上的此电脑（或“计算机”），选择 属性 ，点击 高级系统设置，然后选择 环境变量
在 系统变量 区域，点击 新建，输入以下内容，然后点击确定。
```
变量名: NODE_PATH
变量值:当前文件夹目录\node_modules 
```
2. 更新系统变量 

在 系统变量 区域，选择 Path，点击 编辑,点击 新建，输入 `%NODE_PATH%`
```bash
临时配置系统变量
`set PATH=%PATH%;%NODE_DIR%`
永久配置系统变量
`setx /M PATH "%PATH%;%NODE_DIR%"`
```
然后依次点击确定关闭所有窗口。
3. 验证
完成环境变量配置后，重新打开 cmd ，输入以下命令验证：
```cmd
echo %NODE_PATH%
```
#### 用户变量 
在 用户变量 区域，选择 Path 变量，点击 编辑
将默认的 `C:\Users\你的用户名\AppData\Roaming\npm`更改为`当前文件夹目录\node_global`

### start脚本模板
```bash
@echo off
:: 关闭回显，设置NODE_DIR为当前脚本所在目录（%cd%表示当前工作目录）
echo.
chcp 65001 >nul 
:: 解决中文乱码问题
echo ==========程序开始============
:: ~dp0 表示批处理文件所在的驱动器和路径，是获取脚本目录的标准写法
set "NODE_DIR=%~dp0"
:: 移除路径末尾的反斜杠（避免路径拼接时出现双反斜杠）
set "NODE_DIR=%NODE_DIR:~0,-1%"
:: 获取脚本所在的真实目录（而非当前工作目录）
echo.
echo "验证node程序的根NODE_DIR路径"
echo.
echo node path is %NODE_DIR%
:: 临时添加Node.js目录到PATH（当前窗口有效，优先使用当前目录的Node）
set path=%PATH%;%NODE_DIR%;%NODE_DIR%\node_global;%NODE_DIR%\node_modules
echo hello
:: 配置npm全局目录和缓存目录
call npm config set prefix "%NODE_DIR%\node_global" >nul 
echo 验证npm全局安装包目录设置结果：
call npm config get prefix
echo.
call npm config set cache "%NODE_DIR%\node_cache" >nul 
echo 验证npm缓存目录设置结果：
call npm config get cache
echo 验证`node`版本
call node -v
echo 验证`npm`版本
call npm -v
echo.
pause
cls
```


### 二：验证
1. 全局安装 i5ting-doc 模块（以管理员身份运行 CMD）：
```cmd
npm install -g i5ting-doc
```
安装成功后，node_global 文件夹下会生成 node_modules 目录。
2. 输入以下命令验证安装路径：
```cmd
npm root -g
```
若输出路径为 node_global\node_modules，说明配置成功。
## 使用教程
### 配置镜像
1. 安装 nrm 
```bash
npm install -g nrm
```
2. 查看所有可用源
```bash
nrm ls
```
会显示：
+ npm —— 官方源（慢）
+ taobao —— 淘宝源（快，推荐）
+ yarn
+ tencent
+ 等等
3. 一键切换到淘宝镜像
```bash
nrm use taobao
```
4. 切回官方源（发布包时用）
```bash
nrm use npm
```
5. 测试哪个源速度最快
```bash
nrm test
```
6. 永久切换淘宝源
```bash
npm config set registry https://registry.npmmirror.com/
```
6. 永久切回官方
```bash
npm config set registry https://registry.npmjs.org/
```
8. 查看当前用的是什么源
```bash
npm config get registry
```
### pnpm
1. 安装
方法 1：用 npm 安装（最简单）
```bash
npm install -g pnpm
```
方法 2：官方一键安装（无环境变量问题）
```powershell
iwr https://get.pnpm.io/install.ps1 -useb | iex
```
2. 使用
```bash
pnpm install 包名      # 安装依赖（等同于 npm install）
pnpm add 包名 -g      # 全局安装（等同于 npm i -g）
pnpm dev              # 运行项目
pnpm run 脚本名        # 运行脚本
```
3. 设置全局包安装目录
设置环境变量
```bash
pnpm config set store-dir "D:\pnpm\.pnpm-store"
pnpm config set global-dir "D:\pnpm\global"
pnpm config set global-bin-dir "D:\pnpm\global\bin"
```
初始化
```bash
pnpm setup
```
还原默认
```bash
pnpm config delete store-dir
pnpm config delete global-dir
pnpm config delete global-bin-dir
pnpm setup
```
## node版本管理工具
### fnm
尽管如此当前node版本可能仍然不符合你的使用
powershell
```powershell
iwr https://fnm.vercel.app/install.ps1 -useb | iex
```
Mac / Linux
```bash
curl -fsSL https://fnm.vercel.app/install | bash
```
### 常用命令
```bash
fnm install 20       # 安装 Node 20 LTS（最新稳定版）
fnm use 20           # 使用 Node 20
fnm default 20       # 设置默认版本
fnm ls               # 查看已安装版本
fnm current          # 查看当前版本
fnm uninstall 18      # 卸载旧版本
```
### 项目版本切换
在项目根目录新建 .node-version 文件：
```text
20
```
