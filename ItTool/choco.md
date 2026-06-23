
## 安装
### 设置安装环境
```cmd
# 设置Chocolatey自身的安装路径，例如D:\Chocolatey
$env:ChocolateyInstall = 'D:\Chocolatey'

# 检查环境变量是否设置成功
$env:ChocolateyInstall
```
%ChocolateyInstall%最好后续在系统变量里面设置
### 安装
```cmd
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
- **命令解释**：这条命令做了三件事：
    - `Set-ExecutionPolicy Bypass -Scope Process -Force`：临时允许在当前 PowerShell 会话中运行脚本，不会永久修改系统安全策略[](https://computingforgeeks.com/manage-applications-on-windows-using-chocolatey-software-management-tool/)。
    - `[System.Net.ServicePointManager]::SecurityProtocol ...`：强制使用 TLS 1.2 安全协议，确保下载安全[](https://computingforgeeks.com/manage-applications-on-windows-using-chocolatey-software-management-tool/)。
    - `iex ...`：从官网下载并执行安装脚本[](https://computingforgeeks.com/manage-applications-on-windows-using-chocolatey-software-management-tool/)。
### 验证
```cmd
choco --version
```
## 使用
|命令|作用|常用参数|
|---|---|---|
|`choco -v`|查看 Choco 版本|无|
|`choco help`|查看全部帮助文档|无|
|`choco search 软件名`|搜索软件包|`--all` 显示全部版本|
|`choco install 包名`|安装软件|`-y` 静默同意、`--notsilent`弹出图形安装界面|
|`choco uninstall 包名`|卸载软件|`-y` 直接确认卸载|
|`choco upgrade 包名`|更新指定软件|`all` 更新全部已装软件|
|`choco list`|列出本地已安装软件|`--local` 仅本地、`--outdated` 查看可更新软件|
|`choco info 包名`|查看软件详细信息|无|
|`choco pin add -n=包名`|锁定版本禁止更新|`pin remove` 解除锁定|
|`choco clean`|清理下载缓存安装包|无|
|`choco config list`|查看 Choco 配置|无|
|`choco config set 键=值`|修改配置|可设置源、安装目录|
|`choco source list`|查看软件源|无|
|`choco source add`|添加自定义软件源|多用于国内镜像加速|
|`choco feature list`|查看功能开关|无|
|`choco feature enable -n=xxx`|开启功能|`allowGlobalConfirmation` 全局默认同意|
|`choco upgrade all -y`|一键更新所有软件|全系统软件批量升级|
|`choco install 包1 包2 -y`|批量安装多个软件|空格分隔包名|
### 图形化安装
图形化安装软件
```cmd
choco install git --notsilent 
```
## 配置
### choco config
| 配置项 | 你的当前值 | 作用说明 | 为空时的默认行为 |
|:---|:---|:---|:---|
| `cacheLocation` | (空) | 指定下载的安装包缓存目录。 | 使用系统临时文件夹 (`$env:TEMP`)。 |
| `commandExecutionTimeoutSeconds` | `2700` | 安装脚本的最大执行秒数，超时会终止。 | 已设置，2700秒（45分钟）是个很宽松的值。 |
| `containsLegacyPackageInstalls` | `true` | 标记系统中是否存在用旧版 Chocolatey（0.9.8 以下）安装的包。 | 只是一个只读状态标记，**无需修改**。 |
| `defaultPushSource` | (空) | `choco push` 推送包时的默认目标源。 | 无默认推送源，推送时必须手动指定。 |
| `defaultTemplateName` | (空) | `choco new` 创建新包时使用的模板名。 | 使用内置的默认模板。 |
| `proxy` | (空) | 代理服务器地址。 | 不使用代理，直接连接网络。 |
| `proxyBypassList` | (空) | 不走代理的地址列表（逗号分隔）。 | 无额外绕过规则。 |
| `proxyBypassOnLocal` | `true` | 本地地址是否绕过代理。 | 是，本地连接不走代理。 |
| `proxyPassword` | (空) | 代理密码（加密存储）。 | 无。 |
| `proxyUser` | (空) | 代理用户名。 | 无。 |
| `upgradeAllExceptions` | (空) | 运行 `choco upgrade all` 时要**跳过**的包名列表。 | 升级所有包，不跳过任何。 |
| `webRequestTimeoutSeconds` | `30` | 网络请求的超时秒数。 | 已设置为30秒，较为合理。 |
### 换源
#### 切换国内镜像源（加速下载）

Chocolatey 的默认源在国外，国内访问可能很慢。你可以将其替换为国内镜像源（如清华大学开源软件镜像站），从而显著提升下载速度和稳定性[](https://www.php.cn/faq/2244465.html)。
```powershell
# 查看当前使用的源
choco source list
# 移除默认源
choco source remove -n chocolatey
# 添加清华大学镜像源
choco source add -n tuna -s https://mirrors.tuna.tsinghua.edu.cn/chocolatey/
# 设置新源为优先使用
choco source priority --name=tuna --priority=0
```
## 软件推荐
### 🎮 个人游戏与工具
*   **steam** (`steam`)：PC最大的数字游戏平台，海量游戏库。
*   **epicgameslauncher** (`epicgameslauncher`)：Epic游戏商城客户端，每周有免费游戏领取。
*   **ubisoft-connect** (`ubisoft-connect`)：育碧游戏平台，包含《刺客信条》、《孤岛惊魂》等大作。
*   **goggalaxy** (`goggalaxy`)：GOG平台客户端，主打无加密（DRM-Free）经典游戏，是怀旧游戏天堂。
*   **minecraft** (`minecraft-launcher`)：沙盒游戏《我的世界》官方启动器，创造无限可能。
*   **nvidia-geforce-now** (`nvidia-geforce-now`)：Nvidia云游戏平台，在低配电脑上也能畅玩3A大作。
*   **origin** (`origin`)：EA游戏平台，包含《FIFA》、《战地》、《模拟人生》等热门系列。
*   **bluestacks** (`bluestacks`)：Android模拟器，让你在电脑上畅玩手机游戏。
*   **wechat** (`wechat`)：经典的休闲小游戏平台（没错，PC版微信里也能玩小游戏！）。
*   **discord** (`discord`)：游戏玩家首选的语音和文字聊天社区。

### 👾 复古游戏与模拟器
*   **retroarch** (`retroarch`)：终极复古模拟器前端，支持NES, SNES, PS1, PSP等几乎所有老游戏机。
*   **dolphin** (`dolphin-emulator`)：优秀的GameCube和Wii模拟器，支持高清画质增强。
*   **pcsx2** (`pcsx2`)：PlayStation 2模拟器，兼容性极高，游戏库庞大。
*   **ppsspp** (`ppsspp`)：PSP模拟器，画质和便携性极佳。
*   **cemu** (`cemu`)：Wii U模拟器，性能出色，是《塞尔达传说：旷野之息》的首选模拟器。
*   **yuzu** (`yuzu`)：Nintendo Switch模拟器（因法律原因当前状态为停更）。
*   **dosbox** (`dosbox`)：DOS系统模拟器，重温《仙剑奇侠传》、《Doom》等经典DOS游戏。
*   **scummvm** (`scummvm`)：经典点击冒险游戏模拟器，如《猴岛的秘密》、《极速天龙》。

### 🎬 多媒体与创意工具
*   **vlc** (`vlc`)：万能播放器，几乎能播放所有音视频格式。
*   **handbrake** (`handbrake`)：强大的开源视频转码压缩工具。
*   **obs-studio** (`obs-studio`)：免费开源的直播推流和视频录制软件。
*   **gimp** (`gimp`)：功能可媲美PS的自由开源图像处理软件。
*   **blender** (`blender`)：自由开源的3D创作套件，支持建模、动画、渲染等全流程。
*   **audacity** (`audacity`)：强大的多轨音频编辑和录音软件。
*   **spotify** (`spotify`)：全球流行的正版流媒体音乐服务平台。
*   **kodi** (`kodi`)：开源家庭影院/媒体中心软件，强大的媒体库管理功能。

### ✨ 酷炫命令行工具 (Terminal Toys)
*   **cmatrix** (`cmatrix`)：在命令行里模拟《黑客帝国》的经典数字雨，装X神器。
*   **neofetch** (`neofetch`)：用ASCII艺术在终端漂亮地显示系统信息。
*   **figlet** (`figlet`)：将任意文本转换为大号ASCII艺术字横幅。
*   **cowsay** (`cowsay`)：生成一头用ASCII字符画成的牛，让它说出你指定的文字。
*   **lolcat** (`lolcat`)：给任何文本或命令输出加上彩虹渐变色，让终端充满基情。
*   **fortune** (`fortune`)：每次随机显示一条名言警句、笑话或有趣的格言。
*   **htop** (`htop`)：更现代美观的交互式进程查看器，比系统自带的好看不少。
*   **bat** (`bat`)：带语法高亮和Git行号提示的cat替代品，查看代码必备。
* 