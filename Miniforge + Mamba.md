Miniforge 是 Conda 的轻量级开源替代，**内置 Mamba 包管理器**（比 conda 快 5-10 倍），默认使用 conda-forge 源，完全免费无商业限制。

- **清华大学镜像站（首选）**：[https://mirrors.tuna.tsinghua.edu.cn/github-release/conda-forge/miniforge/LatestRelease/](https://link.wtturl.cn/?target=https%3A%2F%2Fmirrors.tuna.tsinghua.edu.cn%2Fgithub-release%2Fconda-forge%2Fminiforge%2FLatestRelease%2F&scene=im&aid=497858&lang=zh "autolink")
- 南京大学镜像站：[https://mirror.nju.edu.cn/github-release/conda-forge/miniforge/LatestRelease/](https://link.wtturl.cn/?target=https%3A%2F%2Fmirror.nju.edu.cn%2Fgithub-release%2Fconda-forge%2Fminiforge%2FLatestRelease%2F&scene=im&aid=497858&lang=zh "autolink")

| 术语                      | 说明                                           |     |     |     |
| ----------------------- | -------------------------------------------- | --- | --- | --- |
| **环境 (environment)**    | 独立的 Python 及依赖空间，每个环境互不干扰                    |     |     |     |
| **base 环境**             | Miniforge 安装时自带的根环境，通常只用来管理 conda/mamba 本身   |     |     |     |
| **包缓存 (package cache)** | 下载的 `.conda` / `.tar.bz2` 包存放处，不同环境共享，避免重复下载 |     |     |     |
| **channel**             | 包的来源仓库，`conda-forge` 是默认且最全的社区频道             |     | 术语  | 说明  |
| **环境 (environment)**    | 独立的 Python 及依赖空间，每个环境互不干扰                    |     |     |     |
| **base 环境**             | Miniforge 安装时自带的根环境，通常只用来管理 conda/mamba 本身   |     |     |     |
| **包缓存 (package cache)** | 下载的 `.conda` / `.tar.bz2` 包存放处，不同环境共享，避免重复下载 |     |     |     |
| **channel**             | 包的来源仓库，`conda-forge` 是默认且最全的社区频道             |     |     |     |
|                         |                                              |     |     |     |
|                         |                                              |     |     |     |
## 下载与安装 Miniforge
Miniforge 是 conda-forge 社区维护的轻量级 conda 发行版，默认已内置 **mamba 求解器**（`conda-libmamba-solver`），速度快且资源占用低。
**下载 Miniforge**  
访问 [Miniforge GitHub Release](https://github.com/conda-forge/miniforge/releases/latest)，下载 `Miniforge3-Windows-x86_64.exe`（Intel/AMD 64位）。  
或者用 winget（Windows 11/10 自带）：
```bash
winget install CondaForge.Miniforge3
```
## 配置
### 一：安装软件配置
+ Create shortcuts (supported packages only)
	创建开始菜单快捷方式（仅支持的程序）
+  Add installation to my PATH environment variable
	把 Miniforge 路径添加到系统 PATH 环境变量
+ Register Miniforge3 as my default Python 3.13
	把 Miniforge3 内置python注册为系统默认的 Python
+ Clear the package cache upon completion
	安装完成后清空安装缓存包
 打开 **新的** 命令提示符（CMD）或 PowerShell
激活**Conda 初始化 PowerShell**，让 PowerShell 支持 Conda 的环境激活/切换功能。
```powershell
# 用完整路径运行 conda 初始化脚本
%LOCALAPPDATA%\Programs\Miniforge3\Scripts\conda.exe init powershell
```
- 输入以下命令验证安装：
```bash
conda --version
mamba --version
conda config --set auto_activate true
conda config --set default_activation_env maintest
conda config --set env_prompt "({name}) "
```
### 二：配置自定义路径
#### 2.1：conda配置
```bash
# 设置包缓存目录（下载的 .conda/.tar.bz2 包存放处）
conda config --add pkgs_dirs D:\tmp\conda\condaPkgdir
# 设置虚拟环境默认创建目录（命名环境如 conda create -n myenv）
conda config --add envs_dirs D:\tmp\conda\CondaVenv
# 设置 conda 使用 mamba 求解器（Miniforge 通常已默认，若无则执行）
conda config --set solver libmamba
# 配置 conda-forge 主源 
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
# 禁用默认源（避免混合源导致的问题）安装包时，只从最高优先级的源下载，不会混合多个源
conda config --set channel_priority strict 
# 删除默认官方源（`defaults` 是 Anaconda 官方源，在国外）
conda config --remove channels defaults
```
查看结果：
```bash
# 查看当前所有配置来源
mamba info
conda config --show pkgs_dirs
conda config --show envs_dirs
conda config --show-sources
# 验证镜像配置：
conda config --show channels
```
#### 2.2：python配置
+ PIP_CACHE_DIR：pip缓存存放位置环境变量
+ PYTHONPYCACHEPREFIX：python字节码文件配置环境变量
+ PYTHONUSERBASE：用户全局安装包环境变量pip install --user 时的安装前缀（用户级包目录）
#### 2.3：镜像源配置教程
```bash
# 设置全局镜像别名
conda config --set channel_alias https://mirrors.tuna.tsinghua.edu.cn/anaconda
# 配置自定义通道
conda config --set custom_channels.conda-forge https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
# 启用严格的通道优先级（避免混合源导致的依赖冲突）
conda config --set channel_priority strict
# 禁用默认的Anaconda源（Miniforge不需要，且商用有风险）
conda config --remove channels defaults
# 显示下载通道URL（方便调试）
conda config --set show_channel_urls true
# 禁用base环境自动激活（最佳实践，避免污染全局环境）
conda config --set auto_activate_base false
```
更新镜像源
```cmd
mamba update -n base conda mamba --all -y
```
### 三：使用与维护
#### 虚拟环境管理
```bash
# 创建空环境（不预装任何包） 
mamba create -n 环境名 --no-default-packages -y
# 创建新环境（指定Python版本，推荐3.11/3.12）
mamba create -n 环境名 python=3.12 -y
# 激活环境
mamba activate 环境名
# 退出当前环境
mamba deactivate
# 查看所有环境（确认都在D:\tmp\CondaVenv下）
mamba env list
# 删除环境（彻底删除，不可恢复）
mamba env remove -n 环境名 -y
# 重命名环境
mamba rename -n 旧环境名 新环境名
# 复制环境
mamba create -n 新环境名 --clone 旧环境名
# 在自定义位置创建（不受默认目录影响）
mamba create -p D:\myproject\env python=3.12
 # 激活环境
mamba activate myenv 
# 也可用路径激活 
mamba activate D:\myproject\env   
# 退出当前环境，回到 base
mamba deactivate 
# 显示所有环境路径  
mamba env list              
# 或
mamba info --envs   

# 导出到 yml 文件（可保存至 D:\tmp\CondaVenv 备份）
mamba env export -n ds > D:\tmp\CondaVenv\ds_backup.ym
# 从 yml 重建环境
mamba env create -f D:\tmp\CondaVenv\ds_backup.yml
# 导出仅含用户明确安装的包（不含依赖）
mamba env export --from-history -n ds > ds_light.yml  
# 更新现有环境以匹配yaml文件 
mamba env update -f environment.yaml --prune  
# 更新conda和mamba到最新版本 
mamba update conda mamba --all -y  
```
#### 包管理
```bash
# 指定版本
mamba install python=3.10.8
# 安装包
mamba install 包名 -y
# 安装指定版本的包
mamba install 包名=1.2.3 -y
# 安装多个包
mamba install numpy pandas matplotlib -y
# 更新包
mamba update 包名 -y
# 更新环境中所有包
mamba update --all -y
# 卸载包
mamba remove 包名 -y
# 查看已安装的包
mamba list
# 在指定环境安装（无需激活）
mamba install -n myenv pandas

# 搜索包
mamba search 包名

# 查看包详细信息
mamba search 包名 --info
# 从指定通道安装包（如pytorch）
mamba install -c pytorch pytorch torchvision torchaudio -y

# 安装本地包文件
mamba install ./package.tar.bz2 -y

# 安装pip包（仅当conda-forge没有时使用）
pip install 包名

# 锁定包版本（防止意外更新）
mamba install 包名=1.2.3 --freeze-installed -y
```
#### 维护
```bash
# 查看缓存大小 
mamba clean --info
# 清理不再被任何环境使用的包缓存（释放磁盘空间）
conda clean --all
# 删除某个环境
conda remove -n py310 --all
# 清理未使用的包缓存 
mamba clean -p -y
# 修复依赖问题 
mamba install --repair -y 

# 强制重新安装所有包 
mamba install --force-reinstall --all -y
# 仅清理解压后的临时包文件（较安全，可腾出大量空间）
conda clean --tarballs
mamba update --all -y   # 更新所有包修复依赖


conda config --show-sources  # 显示所有配置文件及内容
conda config --get channels  # 查看 channel 优先级
conda info --pkgs-dirs       # 查看包缓存目录
conda info --envs-dirs       # 查看环境搜索目录


# 添加 channel（放在最前面优先级最高）
conda config --add channels conda-forge
# 移除 channel
conda config --remove channels defaults
# 设置/添加路径
conda config --add pkgs_dirs D:\tmp\condaPkgdir2   # 新增第二个缓存目录
conda config --add envs_dirs D:\projects\conda_envs # 添加备用环境目录
```
#### 高级
离线安装包
在有网络的机器上下载包：
```bash
mamba install 包名 --download-only
# `condaPkgdir`里面
```
在离线机器上安装：
```bash
mamba install 包名 --offline -y
```
### 临时使用其他镜像源
```
# 临时使用中科大镜像源安装包
mamba install 包名 -c https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/ -y
```
### 查看包依赖关系
```
# 查看包的依赖
mamba repoquery depends 包名

# 查看哪些包依赖于该包
mamba repoquery whoneeds 包名
```
依赖处理
```bash
mamba install 包名 --strict-channel-priority -y
```
重置
```bash
# 如果 base 坏了，可以强制重装 mamba/conda
conda install --revision 0   # 回滚到初始状态（慎用）
# 更稳妥的方式：
mamba update -n base conda mamba
```