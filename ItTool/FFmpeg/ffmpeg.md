# FFmpeg 下载配置使用全流程

FFmpeg 是一款开源跨平台的音视频处理工具，支持几乎所有主流格式的编码、解码、转码、剪辑、合并、滤镜等操作，是多媒体处理领域的事实标准。

---

## 一、下载 FFmpeg

### 1.1 官方下载渠道
官方网站：[ffmpeg.org](https://ffmpeg.org/) → 顶部导航 **Download**

FFmpeg 官方不直接提供 Windows 二进制安装包，而是推荐第三方构建版本。主流可靠来源有两个：
- **Gyan.dev**（推荐，更新稳定）
- **BtbN GitHub Releases**（版本更新快）

### 1.2 版本选择
Windows 平台提供两种主要构建包：

| 版本                  | 包含内容                           | 适用场景           |
| ------------------- | ------------------------------ | -------------- |
| **essentials**（精简版） | 主流编解码器（H.264、AAC、MP3 等）        | 日常转码、压缩、剪辑足够使用 |
| **full**（完整版）       | 全部编解码器（含 AV1、libx265、libvpx 等） | 专业处理、特殊格式需求    |

> 建议初学者直接下载 **full 完整版**，避免后续缺少编码器的问题。

### 1.3 下载步骤（Windows）
1. 打开 FFmpeg 官网下载页，点击 **Windows** 图标
2. 选择进入 **gyan.dev** 构建页面
3. 在 **Release Builds** 区域找到 `ffmpeg-release-full.7z` 或 `.zip` 下载
4. 等待下载完成（文件约 100MB+）

---

## 二、安装与环境配置

### 2.1 解压文件
1. 使用 7-Zip、WinRAR 等工具解压下载的压缩包
2. 将解压后的文件夹重命名为 `ffmpeg`（可选，方便记忆）
3. 移动到固定目录，例如：`C:\ffmpeg\` 或 `D:\Environment\ffmpeg\`

解压后目录结构：
```
ffmpeg/
├── bin/          # 可执行文件（核心）
│   ├── ffmpeg.exe   # 主处理程序
│   ├── ffprobe.exe  # 媒体信息分析工具
│   └── ffplay.exe   # 简易播放器
├── doc/          # 文档
├── presets/      # 预设文件
└── LICENSE       # 许可证
```

### 2.2 配置系统环境变量（关键步骤）
目的：在任意目录下都能通过命令行调用 ffmpeg。

**操作步骤：**
1. 复制 `bin` 文件夹的完整路径（例如 `C:\ffmpeg\bin`）
2. 右键「此电脑」→ **属性** → **高级系统设置**
3. 点击右下角 **环境变量**
4. 在「系统变量」区域找到 `Path`，双击编辑
5. 点击 **新建**，粘贴刚才复制的 `bin` 路径
6. 依次点击 **确定** 关闭所有窗口

> 注意：必须配置到**系统变量**的 Path 中，而非用户变量，否则部分程序可能无法调用。

### 2.3 验证安装是否成功
1. 按下 `Win + R`，输入 `cmd` 打开命令提示符
2. 输入以下命令并回车：
```bash
ffmpeg -version
```
3. 如果显示版本号、编译信息和编码器列表，说明安装配置成功

同样可以验证另外两个工具：
```bash
ffprobe -version
ffplay -version
```

---

## 三、FFmpeg 基础使用

### 3.1 命令基本语法
```bash
ffmpeg [全局参数] -i [输入文件] [编码/滤镜参数] [输出文件]
```

核心参数规则：
- `-i`：指定输入文件，可以有多个
- `-c:v` / `-vcodec`：指定视频编码器
- `-c:a` / `-acodec`：指定音频编码器
- `-c copy`：直接复制流，不重新编码（速度极快）
- `-vf`：视频滤镜（缩放、裁剪、水印等）
- `-ss`：起始时间点
- `-t`：持续时长

### 3.2 最常用命令速查

#### 查看媒体信息
```bash
ffprobe input.mp4
```

#### 格式转换（自动匹配编码器）
```bash
# MOV 转 MP4
ffmpeg -i input.mov output.mp4

# MKV 转 MP4
ffmpeg -i input.mkv output.mp4

# 无损封装转换（不重编码，秒级完成）
ffmpeg -i input.mkv -c copy output.mp4
```

#### 视频压缩（CRF 模式，推荐）
```bash
# H.264 编码，CRF=23（默认，值越小质量越高，18-28 常用）
ffmpeg -i input.mp4 -c:v libx264 -crf 23 -c:a aac output.mp4

# H.265 (HEVC) 编码，体积更小，CRF=28
ffmpeg -i input.mp4 -c:v libx265 -crf 28 -c:a aac output.mp4
```

#### 修改分辨率
```bash
# 宽度固定1280，高度等比例自动计算
ffmpeg -i input.mp4 -vf scale=1280:-1 output.mp4

# 强制指定 1920x1080
ffmpeg -i input.mp4 -s 1920x1080 output.mp4
```

#### 视频裁剪/截取
```bash
# 从第10秒开始，截取30秒（无损，速度快）
ffmpeg -ss 00:00:10 -t 30 -i input.mp4 -c copy output.mp4

# 从第10秒到第1分钟
ffmpeg -ss 00:00:10 -to 00:01:00 -i input.mp4 -c copy output.mp4
```
> 注意：`-ss` 放在 `-i` 前面是快速定位（基于关键帧），放在后面是精确逐帧但速度慢。

#### 提取音频
```bash
# 提取为 MP3
ffmpeg -i input.mp4 -vn -c:a libmp3lame -q:a 2 output.mp3

# 提取为 AAC
ffmpeg -i input.mp4 -vn -c:a copy output.aac
```
> `-vn` = video no，移除视频流

#### 去除声音（保留画面）
```bash
ffmpeg -i input.mp4 -an -c:v copy output.mp4
```
> `-an` = audio no，移除音频流

#### 截取封面/截图
```bash
# 截取第5秒的一帧作为图片
ffmpeg -i input.mp4 -ss 5 -vframes 1 cover.jpg

# 每隔10秒截取一张缩略图
ffmpeg -i input.mp4 -vf fps=1/10 thumb_%03d.jpg
```

#### 添加图片水印
```bash
# 左上角，距离边缘10像素
ffmpeg -i input.mp4 -i logo.png -filter_complex "overlay=10:10" output.mp4

# 右下角
ffmpeg -i input.mp4 -i logo.png -filter_complex "overlay=W-w-10:H-h-10" output.mp4
```

#### 视频合并
```bash
# 方法1：创建 filelist.txt 写入要合并的文件
# file 'part1.mp4'
# file 'part2.mp4'
ffmpeg -f concat -safe 0 -i filelist.txt -c copy output.mp4
```

#### 转 GIF
```bash
ffmpeg -i input.mp4 -vf "fps=10,scale=480:-1" output.gif
```

---

## 四、进阶使用技巧

### 4.1 编码速度与质量平衡
`-preset` 参数控制编码速度与压缩率：
- `ultrafast`：最快，压缩率最低
- `superfast` / `veryfast` / `faster` / `fast`
- `medium`：默认，均衡
- `slow` / `slower` / `veryslow`：最慢，压缩率最高

示例：
```bash
ffmpeg -i input.mp4 -c:v libx264 -preset veryfast -crf 23 output.mp4
```

### 4.2 批量处理（Windows）
批量转换当前目录下所有 avi 为 mp4：
```batch
for %%i in (*.avi) do ffmpeg -i "%%i" -c:v libx264 -crf 23 "%%~ni.mp4"
```

### 4.3 硬解码加速（NVIDIA GPU）
如果有 NVIDIA 显卡，可使用硬件编码大幅提速：
```bash
ffmpeg -i input.mp4 -c:v h264_nvenc -crf 23 output.mp4
```

---

## 五、Linux / macOS 安装

### Linux（Ubuntu/Debian）
```bash
sudo apt update
sudo apt install ffmpeg
```

### macOS（Homebrew）
```bash
brew install ffmpeg
```

---

## 六、常见问题

1. **提示"不是内部或外部命令"**
   → 环境变量配置错误，检查 Path 中是否正确添加了 bin 目录路径，配置后需**重新打开**命令行窗口。

2. **找不到编码器 libx264 / libx265**
   → 下载的是 essentials 精简版，换成 full 完整版即可。

3. **裁剪视频画面花屏/开头黑屏**
   → 使用 `-c copy` 无损裁剪时依赖关键帧，位置不精确；去掉 `-c copy` 重新编码可解决。

4. **转码速度很慢**
   → 正常现象，视频编码本身计算量大；可调整 `-preset` 为 faster 或使用 GPU 硬件加速。

---

需要我补充某个具体场景的详细命令（比如批量压缩、字幕烧录、直播推流），或者针对你的使用场景给出最优参数建议吗？