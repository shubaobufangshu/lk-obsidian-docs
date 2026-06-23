
---
## 一、Flow Launcher 是什么？

Flow Launcher 是一款 Windows 下的**快速启动与效率工具**，类似 macOS 的 Alfred 或 Spotlight。通过一个极简的搜索框，你可以：
**官方下载地址**：[https://flow-launcher.com/](https://link.wtturl.cn/?target=https%3A%2F%2Fflow-launcher.com%2F&scene=im&aid=497858&lang=zh "autolink") 或 GitHub Releases
- 快速启动应用程序
- 搜索文件（结合 Everything 几乎瞬间出结果）
- 即时计算、单位换算、颜色转换
- 一键执行系统命令
- 自定义网页搜索（Google、百度、B站等）
- 管理剪贴板历史、杀死进程、书签搜索……
- 通过丰富的**插件系统**扩展功能

它开源免费，占用资源小，完全可取代 Win 自带搜索和部分日常操作。

---
## 二、下载与安装

1. **下载**  
   前往官网：`https://www.flowlauncher.com`  
   或 GitHub Releases 页面：`https://github.com/Flow-Launcher/Flow.Launcher/releases`  
   选择最新的 `Flow-Launcher-vX.X.X.exe` 安装包，或便携版（Portable）压缩包。

2. **安装**  
   - 运行安装程序，选择安装路径。  
   - 建议勾选 **“开机自动启动”** 和 **“添加到系统 PATH”**。  
   - 安装完成后，任务栏会出现 Flow Launcher 的图标。

3. **更新**  
   在设置中可直接检查更新，或下载新版本覆盖安装（用户数据通常不受影响）。
### 2.1 必备初始配置

#### 2.1.1 集成 Everything 搜索引擎

Flow Launcher 本身不建索引，**必须配合 Everything 才能实现毫秒级文件搜索**：

1. 先安装 Everything：[https://www.voidtools.com/](https://link.wtturl.cn/?target=https%3A%2F%2Fwww.voidtools.com%2F&scene=im&aid=497858&lang=zh "autolink")
2. 打开 Flow Launcher 设置（Ctrl+I）
3. 进入 "插件"→"文件管理器"
4. 将 "索引搜索引擎" 和 "目录递归搜索引擎" 都改为 "Everything"
5. 填写 Everything.exe 的实际路径（如`C:\Program Files\Everything\Everything.exe`）

#### 1.2.2 基础设置优化

- **外观**：选择深色 / 浅色 / 毛玻璃主题，调整透明度和圆角
- **搜索**：添加常用优先目录，设置结果显示数量
- **热键**：检查并解决热键冲突，可设置双击 Alt 唤醒
---

## 三、首次启动与界面介绍

按下默认快捷键 **`Alt + Space`**，屏幕中央会弹出搜索框。界面包含：

- **输入区**：最核心的部分，输入任何内容。
- **结果列表**：实时显示匹配的程序、文件、命令、计算等。
- **预览面板**（可选）：在设置中开启后，可预览文件内容或图片。
- **上下文菜单**：对结果点击右键，或按 `Shift + Enter` 查看可执行的操作（以管理员身份运行、打开文件位置等）。

按 `Esc` 可以退出搜索框。

---

## 四、基础使用（无需任何配置）
### 基础快捷键大全
|快捷键|功能|
|---|---|
|Alt+Space|唤醒 / 隐藏主界面（默认）|
|Esc|关闭搜索框或返回上一级|
|↑/↓|在结果列表中上下选择|
|Enter|执行选中项|
|Ctrl+Enter|打开文件 / 文件夹所在位置|
|Ctrl+Shift+Enter|以管理员身份运行程序|
|Ctrl+C|复制选中项的完整路径|
|F1|切换预览面板（显示文件大图 / 详细信息）|
|Ctrl+I|快速打开设置|
|Alt + 数字|快速选择第 N 个结果|
|Tab|自动补全输入|
|F5|刷新所有插件数据|
### 1. 搜索并启动程序
直接输入软件名称，如 `chrome`、`微信`，回车即打开。  
支持拼音模糊匹配，输入 `kongzhimianban` 也能找到“控制面板”。
### 应用程序快速启动

这是 Flow Launcher 最基础也最常用的功能：

- 直接输入应用名称的**前几个字符**即可匹配（如`chr`→Chrome，`vs`→Visual Studio）
- 支持**拼音首字母搜索**中文应用（如`wx`→微信，`dd`→钉钉）
- 支持**模糊匹配**，即使输错几个字母也能找到正确结果
- 常用应用会自动排在前面，使用频率越高排序越靠前
### 2. 快速计算
直接输入数学表达式，无需前缀：
```
(100+50)*0.8
```
结果会实时显示，按回车会将结果复制到剪贴板；按 `Tab` 可选择更多操作（如仅复制数字）。

### 3. 运行系统命令
默认插件 `Shell` 允许执行 CMD 命令，前缀 `>`（可自定义）：
```
> ipconfig
> ping baidu.com -t
> control
```
输入 `> cmd` 会打开命令提示符窗口。
### 系统命令与设置
无需打开控制面板，直接输入命令即可控制系统：

- `shutdown`：30 秒后关机
- `restart -t 0`：立即重启
- `lock`：锁定电脑
- `sleep`：进入睡眠模式
- `display`：显示设置
- `network`：网络设置
- `sound`：声音设置
- `settings`：打开 Flow Launcher 自身设置
### 4. 打开文件夹或路径
输入完整路径，如 `C:\Users\你的用户名\Documents`，回车直接打开文件夹。  
环境变量也支持：`%appdata%`、`%userprofile%`。
### 文件与文件夹搜索

集成 Everything 后，能实现**毫秒级全硬盘文件搜索**：

- 直接输入文件名或关键词（如`报告.docx`）
- 使用**文件类型过滤**：`doc: 项目`（只搜索 Word 文档），`pdf: !recent`（最近修改的 PDF）
- 使用**路径限定**：`D:\work\`（只搜索 D 盘 work 目录）
- 使用**通配符**：`rep*.pdf`（所有以 rep 开头的 PDF 文件）
- 按`Ctrl+Enter`打开文件所在文件夹，按`Ctrl+C`复制文件路径
### 5. 浏览网页与搜索
直接输入网址 `https://www.bilibili.com` 回车打开。  
默认支持 Google 搜索，例如输入 `g 今天天气` 会用 Google 搜索“今天天气”。  
（若无 Google 访问，需自定义搜索引擎，见后文高级部分。）
### 网页搜索与浏览器书签

- **内置网页搜索**：输入`google 关键词`或`baidu 关键词`直接搜索
- **浏览器书签**：直接输入书签名称即可打开（支持 Chrome、Edge、Firefox）
- **直接访问网址**：输入完整网址（如`github.com`）直接在浏览器打开
### 6. 搜索文件（基础）
直接输入文件名，如 `报告.docx`，程序会搜索索引位置。  
**注意**：想要更快、更全的文件搜索，务必安装 **Everything 插件**（见后文）。

---

## 五、核心功能：插件系统
### 插件系统与扩展功能

Flow Launcher 的真正强大之处在于其**丰富的插件生态系统**，目前已有 200 + 社区插件。

#### 3.1 插件管理方法

**命令行方式**（最快捷）：

- `pm search 关键词`：搜索插件
- `pm install 插件名`：安装插件
- `pm list`：列出已安装插件
- `pm update`：更新所有插件
- `pm uninstall 插件名`：卸载插件 [17]

**图形界面方式**：

- 打开设置→插件→插件商店
- 浏览或搜索插件，点击安装即可
- 安装后无需重启，立即生效
Flow Launcher 的强大来自插件。所有插件都可以在**插件商店**中一键安装。
### 如何进入插件商店？
- 在搜索框输入 `settings` 并回车，打开设置界面 → 选择左侧 **“插件”** → 点击右上角 **“插件商店”**。
- 或直接在搜索框输入 `pm install` 进入插件管理命令行（适合高级用户）。

### 必装插件推荐

| 插件名称                   | 功能              | 关键词/用法                                           |
| ---------------------- | --------------- | ------------------------------------------------ |
| **Everything**         | 极速全盘文件搜索        | 直接输入文件名，如 `论文最终版.docx`；可使用筛选 `*.pdf`             |
| **System Commands**    | 关机、重启、锁屏、清空回收站等 | 输入 `shutdown`、`restart`、`lock`、`empty recycle` 等 |
| **Web Searches**       | 自定义搜索引擎         | 自定义热词，例如 `bd 关键词` 用百度搜索                          |
| **Clipboard History**  | 剪贴板历史管理         | 热词 `cb`，可查看/粘贴历史记录；建议设快捷键 `Ctrl+Shift+V`         |
| **Process Killer**     | 快速结束进程          | 输入 `kill 进程名`，如 `kill chrome`                    |
| **Calculator**         | 增强计算器（支持更多函数）   | 直接算式计算，支持 `sqrt(9)`, `sin(30 deg)`               |
| **Currency Converter** | 实时汇率转换          | `100 usd to cny`，`1 btc to usd`                  |
| **Translator**         | 快速翻译            | 配合热词如 `fy hello`（需配置翻译引擎）                        |
| **Color Converter**    | 颜色格式互转          | `#ff0000` 显示 RGB、HSL 等                           |
| **Browser Bookmarks**  | 搜索浏览器书签         | 支持 Chrome、Edge，输入书签名快速打开                         |
| **Windows Settings**   | 快速打开各种系统设置项     | 输入 `wifi`、`bluetooth`、`display` 等直接跳转            |
| **Program**            | 程序别名、添加自定义程序路径  | 默认内置，可给程序添加多个启动关键词                               |
| **Explorer**           | 文件管理快捷操作        | 复制路径、在此处打开终端、计算文件哈希等，右键文件结果可见                    |
必备插件推荐

| 插件名称                        | 功能                       | 关键词         | 示例                            |     |
| --------------------------- | ------------------------ | ----------- | ----------------------------- | --- |
| Clipboard History           | 剪贴板历史记录，支持文本搜索和快速粘贴      | `cb`        | `cb 密码`                       |     |
| Window Walker               | 快速切换和管理打开的窗口             | `win`       | `win Chrome`                  |     |
| Web Search Plus             | 增强版网页搜索，支持 20 + 搜索引擎     | `sc`        | `sc python 列表去重`              |     |
| Process Killer              | 一键结束进程                   | `kill`      | `kill notepad`                |     |
| IP Address                  | 快速查看本机 IP 和网络信息          | `ip`        | `ip`                          |     |
| Color Picker                | 屏幕取色器，支持多种颜色格式           | `color`     | `color`                       |     |
| Steam Games                 | 快速启动 Steam 游戏            | `steam`     | `steam 原神`                    |     |
| JWT Decoder                 | 解码 JWT 令牌                | `jwt`       | `jwt eyJhbGciOiJIUzI1NiIs...` |     |
| Translate                   | 在线翻译                     | `translate` | `translate hello 中文`          |     |
| Lively Wallpaper Controller | 控制 Lively Wallpaper 动态壁纸 | `lively`    | `lively 森林`                   |     |

#### 自定义搜索引擎

在 Web Search 插件设置中可以添加自己常用的搜索引擎：

1. 打开设置→插件→Web Search
2. 点击 "添加"
3. 填写名称、关键词和 URL（使用`{q}`代替搜索词）
4. 示例：
    
    - 知乎：关键词`zh`，URL`https://www.zhihu.com/search?q={q}`
    - GitHub：关键词`gh`，URL`https://github.com/search?q={q}`
    - 淘宝：关键词`tb`，URL`https://s.taobao.com/search?q={q}`

### 插件安装与配置示例：Everything
1. 从 [voidtools](https://www.voidtools.com) 下载并安装 **Everything**（建议安装服务版，开机启动）。
2. 在 Flow Launcher 插件商店安装 **Everything** 插件。
3. 安装后一般会自动检测 Everything 路径，如未检测到，进入设置 → 插件 → Everything → 指定 `Everything.exe` 位置。
4. 完成后，直接输入任意文件名即可秒出结果，并支持 `*.后缀` 过滤。

---
## 高级技巧与深度定制

### 4.1 搜索语法进阶

- **精确匹配**：使用双引号`"精确关键词"`
- **排除关键词**：使用减号`关键词 -排除词`
- **环境变量路径**：`%appdata%`直接打开应用数据文件夹
- **快速跳转文件夹**：使用`>`符号，如`>D:\work`直接打开该文件夹
- **执行 Shell 命令**：使用`!`前缀，如`!ipconfig`执行 ipconfig 命令 [12][17]

### 4.2 自定义命令与别名

可以为常用的复杂命令创建简单别名：

1. 打开设置→插件→Shell
2. 点击 "添加别名"
3. 填写别名和对应的命令
4. 示例：
    
    - 别名`cp`，命令`control`（打开控制面板）
    - 别名`dt`，命令`shutdown -s -t 3600`（1 小时后关机）
    - 别名`clean`，命令`cleanmgr`（打开磁盘清理）
    

### 4.3 界面与主题定制

Flow Launcher 支持深度界面定制：

- 内置多种主题，也可从社区下载第三方主题
- 可调整窗口大小、位置、透明度、圆角半径
- 可自定义字体、颜色、图标大小
- 可显示日期和时间在搜索窗口
- 高级用户可通过修改 XAML 文件创建自己的主题 [14]

### 4.4 性能优化技巧

- 只启用需要的插件，禁用不常用的插件
- 在文件管理器设置中排除不需要搜索的目录（如系统临时文件夹）
- 调整结果显示数量（建议 10-15 个）
- 定期更新 Flow Launcher 和插件到最新版本
- 确保 Everything 一直在后台运行 [14]
- 
## 六、设置详解（按需调校）

在搜索框输入 `settings` 回车，进入主设置界面。

### 通用
- **语言**：可选简体中文。
- **开机启动**：建议保持开启。
- **隐藏到通知区域**：关闭窗口后留在系统托盘。
- **离开焦点时自动隐藏**：点击其他地方自动关闭搜索框。

### 快捷键
- **显示/隐藏 Flow Launcher**：默认 `Alt + Space`，可改为 `Ctrl + Space`、`Win + Q` 等。
- 可为某些插件单独添加快捷键，例如给“剪贴板历史”设 `Ctrl+Shift+V`。

### 主题
- 内置浅色/深色主题，可直接切换。
- 在插件商店安装“主题”类插件，可获得更多样式，如 `Frosted Glass` 毛玻璃效果。
- 可自定义字体、字号、结果行高等。

### 插件（Plugin）设置
每个已安装插件的右侧有配置图标，可更改：
- **Action Keyword（激活关键词）**：比如把 `Web Searches` 中百度的关键词改成 `bd`，用法 `bd 搜索词`。
- **是否启用**：暂时不用的可禁用。
- 插件的详细参数（如浏览器书签的浏览器路径）。

### 文件搜索索引
在设置 → 插件 → **Everything** 或默认的**文件搜索**中，可排除特定文件夹，保护隐私。

---

## 七、高级使用技巧

### 1. 自定义网页搜索
以添加 **B站搜索** 为例：
1. 进入设置 → 插件 → **Web Searches** → 点击“添加”。
2. 填写：
   - **名称**：哔哩哔哩
   - **激活关键词**：`bili`（可随意）
   - **URL**：`https://search.bilibili.com/all?keyword={q}`
3. 保存后，输入 `bili 春日影` 即可在 B 站搜索。
   
此方法可添加任何带搜索的网站（GitHub、知乎、豆瓣等），只需找到其搜索 URL 并将关键词替换为 `{q}`。

### 2. 文件搜索过滤语法
- 直接 `*.pdf`：列出所有 PDF 文件。
- `*.pdf 论文`：搜索文件名含“论文”的 PDF。
- `folder: 项目`：搜索名含“项目”的文件夹（需 Everything 插件）。
- `size:>100mb`：搜索大于 100MB 的文件（Everything 语法直接生效）。

### 3. 快速打开特定文件夹
在程序插件中，可以添加一个**自定义程序路径**：  
设置 → 插件 → **Program** → 添加 → 程序路径填 `C:\MyProjects`，关键词设为 `proj`。  
以后输入 `proj` 就能打开该文件夹。

### 4. 剪贴板历史妙用
安装 **Clipboard History** 后：
- 在搜索框输入 `cb` 查看最近复制的文本/图片。
- 按回车粘贴到当前窗口。
- 可固定常用片段、清空历史。

### 5. 快捷命令汇总
- `> msconfig`：打开系统配置。
- `> control`：控制面板。
- `> appwiz.cpl`：程序和功能。
- `lock`：锁屏（需 System Commands 插件）。
- `sleep`：睡眠。
- `restart`：重启电脑。

### 6. 计算器进阶
直接输入：
- `=10^2` → 100
- `sin(45 deg)` → 0.707...
- `0b1010` → 十进制 10
- `0xFF` → 255
结果可复制，按 `Tab` 切换格式。

### 7. 临时数学/笔记（Quick Notes 插件）
部分插件支持直接创建便签，或使用 `> notepad` 快速打开记事本。

---

## 八、常见问题

**Q: 搜索不到某些文件？**  
A: 安装 Everything 并启用其插件。Windows 自带索引很慢且不全，Everything 是必备搭档。

**Q: 快捷键冲突怎么办？**  
A: 很多软件占用 `Alt+Space`（如 PowerToys Run），进设置改成 `Win+Q` 或 `Ctrl+Alt+Space`。

**Q: 如何卸载插件？**  
A: 设置 → 插件 → 点击插件右侧的“垃圾桶”图标。或输入 `pm remove 插件名`。

**Q: Flow Launcher 占用内存高吗？**  
A: 通常仅几十 MB，但安装过多插件或开启大量索引会有所增加，可禁用不用的插件。

**Q: 能导出/导入设置吗？**  
A: 设置、插件列表、自定义搜索引擎等都保存在 `%APPDATA%\FlowLauncher` 文件夹，备份该文件夹即可。

---