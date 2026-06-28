---
layout: 主页
date: 2026-05-16T08:00:00
banner_y: 0.0%
title: 主页
banner: "[[lingkong.png]]" 
---


[rime/weasel: 【小狼毫】Rime for Windows](https://github.com/rime/weasel)
Scoop 包管理器 + Rime 开源输入法 完整安装使用指南
## 一、Scoop 包管理器（Windows 最佳命令行软件管理器）
### 1. 安装前准备
按 `Win+X` 选择 **终端(管理员)** 打开 PowerShell，执行以下命令开启脚本执行权限：
```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
```
设置**执行策略**（权限）

### 2. 一键安装（国内加速版）
```powershell
irm https://mirror.nju.edu.cn/git/scoop-installer/raw/master/install.ps1 | iex
```

**自定义安装目录**（推荐安装到非系统盘）：
```powershell
irm https://mirror.nju.edu.cn/git/scoop-installer/raw/master/install.ps1 -outfile 'install.ps1'
.\install.ps1 -ScoopDir 'D:\Scoop' -ScoopGlobalDir 'D:\ScoopGlobal'
```

### 3. 配置国内镜像（必做，解决下载慢问题）
```powershell
# 删除默认官方源
scoop bucket rm main
scoop bucket rm extras

# 添加南京大学镜像（速度最快最稳定）
scoop bucket add main https://mirror.nju.edu.cn/git/scoop-main.git
scoop bucket add extras https://mirror.nju.edu.cn/git/scoop-extras.git
scoop bucket add versions https://mirror.nju.edu.cn/git/scoop-versions.git
scoop bucket add nerd-fonts https://mirror.nju.edu.cn/git/scoop-nerd-fonts.git

# 配置 Scoop 自身更新源
scoop config SCOOP_REPO https://mirror.nju.edu.cn/git/scoop.git
```

### 4. 安装下载加速工具
```powershell
scoop install git aria2
scoop config aria2-enabled true
scoop config aria2-max-connection-per-server 16
scoop config aria2-split 16
```

### 5. 基本使用命令
| 命令 | 功能 |
|------|------|
| `scoop search 软件名` | 搜索软件 |
| `scoop install 软件名` | 安装软件 |
| `scoop install -g 软件名` | 全局安装（所有用户可用） |
| `scoop uninstall 软件名` | 卸载软件 |
| `scoop update` | 更新 Scoop 自身和软件列表 |
| `scoop update *` | 更新所有已安装软件 |
| `scoop list` | 列出已安装软件 |
| `scoop info 软件名` | 查看软件详细信息 |

### 6. 常用软件一键安装
```powershell
# 开发工具
scoop install vscode nodejs-lts pnpm python go rustup openjdk17 docker-cli

# 系统工具
scoop install 7zip notepadplusplus ditto quicklook sumatrapdf bulk-crap-uninstaller

# 网络工具
scoop install wireshark nmap fiddler

# 终端工具
scoop install tabby lazygit bat exa fd ripgrep oh-my-posh
```

## 二、Rime 开源输入法（小狼毫 Weasel）
### 1. 安装方式（二选一）
#### 方式一：通过 Scoop 安装（推荐）
```powershell
scoop install weasel
```

#### 方式二：官方安装包安装
下载地址：https://github.com/rime/weasel/releases/latest

### 2. 初始设置
1.  安装完成后，按 `Win+空格` 切换到 **小狼毫输入法**
2.  右键点击任务栏上的 **"中"** 字图标，选择 **"输入法设定"**
3.  取消勾选默认的 **"朙月拼音"**，先不选择其他方案，点击 **"确定"**

### 3. 安装雾凇拼音方案（必装，开箱即用）
雾凇拼音是目前最完善的 Rime 拼音方案，包含海量词库、智能纠错、Emoji 支持等功能。

#### 方法一：使用官方东风破工具（最简单）
1.  右键点击任务栏 Rime 图标，选择 **"获取更多输入方案"**
2.  在弹出的命令行窗口中输入：
    ```
    iDvel/rime-ice:others/recipes/full
    ```
3.  等待安装完成，然后右键点击 Rime 图标，选择 **"重新部署"**

#### 方法二：使用 Git 安装（更新方便）
```powershell
git clone https://gitcode.com/GitHub_Trending/ri/rime-ice.git %APPDATA%\Rime --depth 1
```
然后右键点击 Rime 图标，选择 **"重新部署"**

### 4. 启用雾凇拼音
1.  右键点击任务栏 Rime 图标，选择 **"输入法设定"**
2.  勾选 **"雾凇拼音"**，点击 **"确定"**
3.  按 `Ctrl+~` 快捷键，选择 **"雾凇拼音"** 即可开始使用

### 5. 基础使用技巧
| 快捷键 | 功能 |
|--------|------|
| `Shift` | 中英文快速切换 |
| `Ctrl+~` | 打开方案选单（切换输入方案、简繁切换等） |
| `Ctrl+Shift+数字` | 将候选词固定到对应位置 |
| `Ctrl+Delete` | 删除用户词典中的错误词条 |
| `[` 和 `]` | 候选词翻页 |
| `/` | 输入特殊符号（如 `/fh` 输入符号，/em 输入 Emoji） |

### 6. 常用个性化配置
在 Rime 用户目录（`%APPDATA%\Rime`）中新建 `default.custom.yaml` 文件，粘贴以下内容：
```yaml
patch:
  # 候选词个数
  menu/page_size: 9
  
  # 横排显示候选词
  style/horizontal: true
  
  # 嵌入式候选窗（在光标位置显示）
  style/inline_preedit: true
  
  # 字体设置
  style/font_face: "Microsoft YaHei"
  style/font_point: 14
  
  # 中英文切换快捷键
  switcher/hotkeys:
    - Shift_L
    - Shift_R
  
  # 关闭自动大写
  ascii_composer/switch_key:
    Shift_L: commit_code
    Shift_R: commit_code
    Caps_Lock: noop
```

保存文件后，右键点击 Rime 图标，选择 **"重新部署"** 生效。

### 7. 皮肤设置
1.  下载皮肤：https://github.com/rime/weasel-themes
2.  将皮肤文件解压到 `%APPDATA%\Rime` 目录
3.  在 `default.custom.yaml` 中添加：
    ```yaml
    patch:
      style/color_scheme: 皮肤名称
    ```
4.  重新部署生效

## 三、常见问题解决
### Scoop 常见问题
- **下载失败**：检查网络连接，重新执行 `scoop update`，或更换镜像源
- **权限不足**：不要使用管理员权限运行 Scoop，Scoop 设计为用户级安装
- **环境变量不生效**：重启终端或电脑

### Rime 常见问题
- **重新部署卡住**：右键点击 Rime 图标，选择 **"退出算法服务"**，然后重新打开
- **词库不更新**：定期执行 `git pull` 在 `%APPDATA%\Rime` 目录更新雾凇拼音
- **在某些软件中无法输入**：重启该软件，或在 Rime 设置中开启 **"兼容模式"**

需要我帮你整理一份**常用 Rime 自定义配置**和**好看的皮肤包**下载链接吗？