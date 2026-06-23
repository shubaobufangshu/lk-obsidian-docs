这是一份全面的 Windows 命令提示符（CMD）详细教程，从零基础开始，逐步深入，包括基础概念、文件管理、系统维护、网络诊断、批处理脚本编写等内容。

---

## 一、什么是 CMD？
CMD（命令提示符）是 Windows 系统中的命令行解释器，用来执行输入的命令。它起源于 DOS 系统，至今仍是系统管理、故障排查和自动化的强大工具。

## 二、如何打开 CMD
- 按 `Win + R`，输入 `cmd`，回车。
- 在开始菜单搜索“cmd”或“命令提示符”。
- 在资源管理器地址栏直接输入 `cmd` 并回车，会在当前目录打开命令窗口。
- 以**管理员身份运行**：右键点击“命令提示符”，选择“以管理员身份运行”（许多系统级命令需要管理员权限）。

## 三、基本操作与技巧
1. **命令帮助**：`命令名 /?`  
   例如：`dir /?` 会列出 dir 命令所有参数。
2. **自动补全**：输入前几个字母后按 `Tab` 键，可自动补全文件名或目录名。
3. **历史命令**：按 `↑` `↓` 方向键浏览之前执行过的命令。按 `F7` 可弹出历史列表。
4. **复制/粘贴**：
   - 在窗口标题栏右键 → 编辑 → 标记，用鼠标选中文本后按回车复制。
   - 粘贴可直接按右键（或开启快速编辑模式：标题栏右键 → 属性 → 选项 → 勾选“快速编辑模式”）。
5. **清屏**：`cls`
6. **终止命令**：`Ctrl + C`
7. **命令行参数分隔符**：空格。如果路径含空格，需用英文双引号括起来，如 `"C:\Program Files"`。

---

## 四、文件和目录管理

### 1. 查看目录内容 `dir`
```cmd
dir                  # 列出当前目录文件和子目录
dir /a               # 显示所有文件（含隐藏、系统文件）
dir /s               # 递归显示子目录中的文件
dir /b               # 简洁模式（仅文件名）
dir /o:n             # 按名称排序（/o:d 按日期，/o:s 按大小）
dir /p               # 分页显示
```
**通配符**：`*` 匹配任意字符序列，`?` 匹配单个字符。  
`dir *.txt` 列出所有 txt 文件。

### 2. 切换目录 `cd`（Change Directory）
```cmd
cd C:\Windows        # 进入指定目录
cd ..                # 返回上级目录
cd \                 # 直接回到当前驱动器的根目录
cd /d D:\Backup      # 同时切换驱动器和目录
```
不同盘符间切换需加 `/d`，或先输盘符如 `D:` 再 `cd 路径`。

### 3. 创建目录 `md` 或 `mkdir`
```cmd
md newfolder         # 创建一个目录
md "my data"         # 含空格需加引号
md dir1\dir2\dir3    # 可一次性创建多级目录（需启用命令扩展）
```

### 4. 删除目录 `rd` 或 `rmdir`
```cmd
rd oldfolder         # 删除空目录
rd /s oldfolder      # 删除目录及所有子目录和文件（提示确认）
rd /s /q oldfolder   # 安静模式，不提示直接删除，使用需谨慎
```

### 5. 复制文件 `copy`
```cmd
copy a.txt b.txt               # 复制文件
copy a.txt D:\backup\          # 复制到指定目录
copy *.txt D:\backup\          # 批量复制
copy a.txt + b.txt c.txt       # 合并文件内容并写入新文件
```
`copy` 适用于单个或少量文件，不能复制目录结构。

### 6. 复制目录和文件 `xcopy` 和 `robocopy`
```cmd
xcopy 源 目标 /s /e /i
# /s 复制子目录（除空目录），/e 复制空目录，/i 若目标不存在则假定为目录
robocopy 源 目标 /MIR
# 更强大的同步工具，/MIR 镜像目录（完全同步，慎用）
```
`robocopy` 支持断点续传、多线程（`/MT`）、只复制更改等，建议大型备份使用。

### 7. 移动文件 `move`
```cmd
move a.txt D:\backup\        # 移动文件
move oldname.txt newname.txt # 重命名文件（相当于移动）
move folder1 folder2         # 重命名目录（同盘符下）
```

### 8. 删除文件 `del` 或 `erase`
```cmd
del a.txt            # 删除文件
del *.tmp            # 删除所有临时文件
del /f /q file.txt   # /f 强制删除只读文件，/q 安静模式不提示
del /s *.log         # 递归删除子目录下所有log文件
```

### 9. 重命名 `ren` 或 `rename`
```cmd
ren old.txt new.txt
ren *.txt *.bak      # 批量修改扩展名
```

### 10. 查看文件内容 `type`
```cmd
type file.txt        # 显示文件内容
type file.txt | more # 分页显示
```

---

## 五、重定向与管道

- **标准输出重定向** `>`  
  `dir > list.txt` 将输出写入文件（覆盖原内容）
- **追加输出** `>>`  
  `echo 新的行 >> list.txt`
- **标准输入重定向** `<`  
  `sort < input.txt` 从文件读取内容进行排序
- **管道** `|`  
  将前一个命令的输出作为后一个命令的输入  
  `dir | find "txt"` 从 dir 结果中查找包含 txt 的行  
  `tasklist | findstr "chrome"` 查找进程名包含 chrome 的进程

- **错误重定向** `2>`  
  `dir xxx 2> error.txt` 将错误信息写入文件。  
  合并输出：`command > log.txt 2>&1`

---

## 六、环境变量
1. **查看变量值**：`echo %变量名%`  
   如 `echo %USERNAME%`、`echo %PATH%`
2. **设置变量**（仅当前窗口有效）：
   ```cmd
   set MYVAR=Hello
   echo %MYVAR%
   ```
3. **常用系统变量**：
   - `%USERPROFILE%` 用户目录（如 `C:\Users\你的用户名`）
   - `%SYSTEMROOT%` 系统目录（`C:\Windows`）
   - `%TEMP%` 临时文件夹
   - `%PATH%` 可执行文件搜索路径
4. **永久设置环境变量**：  
   使用 `setx` 命令或通过“系统属性→环境变量”图形界面。
   ```cmd
   setx MYVAR "永久值"
   ```
   注意：`setx` 修改的变量对**新打开**的 CMD 生效，当前窗口不变。

---

## 七、系统信息与管理

### 1. 系统信息
```cmd
systeminfo           # 详细系统信息（OS版本、安装日期、内存、网卡等）
ver                  # Windows 版本
winver               # 弹窗显示版本信息
wmic cpu get name    # CPU 名称（需安装WMIC）
```

### 2. 进程管理
```cmd
tasklist                     # 列出所有进程
tasklist /svc                # 显示进程承载的服务
tasklist /fi "imagename eq notepad.exe"   # 按名称过滤
taskkill /im notepad.exe /f  # 强制结束进程（/f 强制，/t 终止进程树）
taskkill /pid 1234           # 按PID结束进程
```

### 3. 磁盘操作
```cmd
chkdsk C: /f          # 检查C盘并修复错误（可能需要重启）
chkdsk C: /r          # 查找坏扇区并恢复可读信息
format D: /fs:ntfs    # 格式化D盘为NTFS（**会清除所有数据！**）
diskpart              # 进入磁盘分区工具（list disk, select disk, clean等）
```
> 注意：`diskpart` 和 `format` 属于高风险命令，操作前务必确认盘符。

### 4. 日期和时间
```cmd
date                 # 显示并等待修改日期
time                 # 显示并修改时间
date /t              # 仅显示日期
time /t              # 仅显示时间
```

---

## 八、网络相关命令

### 1. IP配置 `ipconfig`
```cmd
ipconfig                     # 基本信息
ipconfig /all                # 详细配置（MAC地址、DNS等）
ipconfig /release            # 释放IP（DHCP）
ipconfig /renew              # 重新获取IP
ipconfig /flushdns           # 清除DNS缓存
```

### 2. 连通性测试 `ping`
```cmd
ping 8.8.8.8                 # 测试网络连通
ping -t 192.168.1.1          # 持续ping，Ctrl+C停止
ping -n 10 baidu.com         # 发送10个包
ping -l 1500 192.168.1.1    # 指定数据包大小（字节）
```

### 3. 路由追踪 `tracert`
```cmd
tracert baidu.com            # 跟踪数据包路径
tracert -d baidu.com         # 不将IP解析为域名，加快速度
```

### 4. DNS查询 `nslookup`
```cmd
nslookup baidu.com           # 查询默认DNS服务器解析
nslookup baidu.com 8.8.8.8  # 指定DNS服务器查询
```

### 5. 网络统计 `netstat`
```cmd
netstat -ano                 # 显示所有连接和监听端口，并显示PID
netstat -b                   # 显示涉及的可执行程序（需管理员权限）
netstat -r                   # 显示路由表
```

### 6. ARP表管理 `arp`
```cmd
arp -a                       # 显示ARP缓存表
```

---

## 九、实用命令汇集

| 命令 | 说明 |
|------|------|
| `cls` | 清屏 |
| `title MyConsole` | 设置窗口标题 |
| `color 0a` | 设置文字颜色（0背景黑，a前景绿色） |
| `tree` | 图形化显示目录结构 |
| `tree /f` | 显示包含文件 |
| `fc file1.txt file2.txt` | 比较文件差异 |
| `shutdown /s /t 60` | 60秒后关机 |
| `shutdown /r /t 0` | 立即重启 |
| `shutdown /a` | 取消定时关机 |
| `assoc .txt` | 查看.txt扩展名关联 |
| `assoc .txt=txtfile` | 更改关联 |
| `ftype txtfile` | 查看类型定义 |
| `where cmd` | 查找命令位置 |
| `powercfg /batteryreport` | 生成电池报告（笔记本） |
| `driverquery` | 列出驱动程序 |

---

## 十、批处理（BAT/CMD脚本）基础
将命令写入 `.bat` 或 `.cmd` 文件，双击即可自动执行。

### 1. 基础指令
```batch
@echo off          # 关闭命令回显（@表示本行也不显示）
rem 这是注释        # 注释内容（也可用 ::）
echo Hello World   # 打印一行
pause              # 暂停，提示“请按任意键继续...”
exit               # 退出脚本
```

### 2. 变量与用户输入
```batch
@echo off
set /p name=请输入你的名字：
echo 你好, %name%!
pause
```

### 3. 条件判断 `if`
```batch
if exist "C:\test.txt" (
    echo 文件存在
) else (
    echo 文件不存在
)

if "%name%"=="admin" echo 欢迎管理员
```
比较时注意变量加引号，防止空值语法错误。

### 4. 循环 `for`
遍历文件：
```batch
for %%f in (*.txt) do echo %%f
```
在命令行中直接执行（非批处理）变量只用单个 `%`，如 `%f`。

计数循环：
```batch
for /l %%i in (1,1,10) do echo %%i
```
这将在 1 到 10 之间循环。

### 5. 调用与跳转
- `goto`：跳转到标签
  ```batch
  :start
  echo 循环中...
  goto start
  ```
- `call`：调用另一个批处理或本文件的子过程
  ```batch
  call :subfunc 参数1
  goto :eof
  :subfunc
  echo 参数是 %1
  goto :eof
  ```

### 6. 命令行参数
脚本文件 `test.bat` 内容：
```batch
@echo off
echo 第一个参数：%1
echo 所有参数：%*
```
执行 `test.bat hello world` 输出 `hello world`。

### 7. 错误级别 `%errorlevel%`
许多命令执行后会设置错误码（0通常表示成功）。
```batch
ping -n 1 192.168.1.1 >nul
if %errorlevel% equ 0 (
    echo 网络连通
) else (
    echo 无法连通
)
```

---

## 十一、高级命令与技巧

### 1. 服务管理 `net` 和 `sc`
```cmd
net start 服务名      # 启动服务
net stop 服务名       # 停止服务
sc query 服务名       # 查询服务状态
sc config 服务名 start= auto   # 设为自动启动（注意等号后有空格）
```

### 2. 计划任务 `schtasks`
```cmd
schtasks /create /tn "MyTask" /tr "notepad.exe" /sc daily /st 09:00
```
创建每天9点运行记事本的任务。查看：`schtasks /query`，删除：`schtasks /delete /tn "MyTask" /f`

### 3. 注册表操作 `reg`
```cmd
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer
reg add "HKCU\Software\MyApp" /v Setting /t REG_DWORD /d 1 /f
reg delete "HKCU\Software\MyApp" /f
```
操作注册表前强烈建议备份。

### 4. 使用管道组合强大功能
```cmd
tasklist | find /i "chrome" > chrome_pids.txt
for /f "tokens=2" %i in ('tasklist ^| find "chrome"') do @echo %i
```
在批处理中 `%i` 需写成 `%%i`。

### 5. 快速获取文件列表全路径
```cmd
dir /b /s *.mp3 > songs.txt
```

---

## 十二、常见问题与注意事项

1. **权限不足**  
   如果遇到“拒绝访问”，请以管理员身份运行 CMD。

2. **路径含空格**  
   务必加双引号，否则会被拆分成多个参数。

3. **文件名/目录名不区分大小写**  
   Windows 下文件系统默认不区分，但某些命令如 `findstr` 的 `/I` 参数有意义。

4. **Unicode 支持**  
   默认 CMD 使用 ANSI 代码页（中文系统通常为 GBK）。切换为 UTF-8 代码页：`chcp 65001`。但兼容性可能不佳，建议用 `type` 查看 UTF-8 文件前先设置代码页。

5. **快速编辑模式导致程序暂停**  
   如果在运行脚本时不小心点击命令行窗口，可能进入“选择”模式导致程序暂停，按回车恢复。

6. **换行符转换**  
   从 Linux 系统来的文本文件换行符不同，某些命令可能异常，可用 `type` 或 `more` 查看，或用工具转换。

---

## 十三、进一步学习
- 每个命令都自带帮助：`命令 /?`
- 官方文档：[Microsoft Docs - Windows 命令](https://docs.microsoft.com/zh-cn/windows-server/administration/windows-commands/windows-commands)
- 练习编写批处理，实现日常重复任务的自动化。

这份教程覆盖了 CMD 的绝大多数常用命令和概念，从入门到脚本编写，建议边看边动手实践。遇到命令不确定时，多用 `/?` 查询官方解释，慢慢就能成为命令行高手。