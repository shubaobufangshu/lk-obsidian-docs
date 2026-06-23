`# 用户环境全量 [Environment]::GetEnvironmentVariables('User') | Format-List`

`#用户环境全量 [Environment]::GetEnvironmentVariables('Machine') | Format-List`
`# 全部环境变量Get-ChildItem Env: | Format-List`
---
## 第1章：认识 PowerShell

### 1.1 它是什么？
Windows PowerShell 是微软开发的命令行壳程序和脚本环境，它建立在 .NET Framework 之上。与传统的 CMD 不同，PowerShell 处理的是**对象**而非纯文本，这让它拥有极强的数据处理与自动化能力。

- **Shell**：交互式命令行环境，你可以输入命令并立即看到结果。
- **脚本语言**：可以将命令写入 `.ps1` 文件，实现复杂的自动化任务。
- **面向对象**：命令的输出是结构化的 .NET 对象，可以直接访问对象的属性、方法，无需复杂的文本解析。

本教程针对 Windows 自带的 **Windows PowerShell 5.1**（绝大多数 Windows 10/11 及服务器版本内置的版本）。

### 1.2 如何启动
有多种方式打开：
- 按 `Win + X`，选择“Windows PowerShell”或“Windows PowerShell (管理员)”。
- 在开始菜单或搜索框输入 `powershell` 回车。
- 在资源管理器地址栏输入 `powershell` 并回车，可直接在当前路径打开。
- 使用 **Windows PowerShell ISE**：一个集成脚本环境，适合编写调试脚本（搜索 ISE 即可打开）。对于更现代的开发，推荐使用 [Visual Studio Code](https://code.visualstudio.com/) 并安装 PowerShell 扩展。

管理员权限会决定某些系统级操作（如安装模块、修改注册表）是否被允许。
### 1.3 Profile文件
如同linux一样PowerShell在启动时候会加载 Profile文件
```powershwll
# 不存在则自动创建配置文件 
New-Item $PROFILE -ItemType File -Force
# 简单别名 
Set-Alias ll Get-ChildItem Set-Alias np notepad 
# 带参数/多命令→先用函数再别名 
function gs { git status } Set-Alias s gs
# 即时生效
. $PROFILE
```
Windows PowerShell 5.1：
+ `当前用户・当前终端:$HOME\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`
+ `全终端:$HOME\Documents\WindowsPowerShell\Profile.ps1`
+ `全用户:$PSHOME\Microsoft.PowerShell_profile.ps1`
PowerShell7
+ `当前用户・当前终端:$HOME\Documents\PowerShell\Microsoft.PowerShell_profile.ps1`
+ `全终端:$HOME\Documents\WindowsPowerShell\Profile.ps1`
+ `全用户:$PSHOME\Microsoft.PowerShell_profile.ps1`
---

## 第2章：基本概念——万物皆Cmdlet

### 2.1 Cmdlet（命令）
PowerShell 原生命令称为 Cmdlet，采用 **动词-名词** 的命名规范，中间用连字符连接，例如 `Get-Process`、`Stop-Service`。这种命名一看便知其功能。
- 动词：Get（获取）、Set（设置）、New（新建）、Remove（删除）、Start（启动）、Stop（停止）等。
- 名词：Process、Service、Item、ChildItem 等，通常为单数形式。

### 2.2 参数
Cmdlet 支持多种参数，大部分参数以 `-` 开头，参数名与 .NET 类似，不区分大小写。
```powershell
# -Name 参数指定要获取的服务名称
Get-Service -Name Spooler

# -ComputerName 参数可以对远程计算机执行命令
Get-Process -ComputerName Server01
```
参数值可以是字符串、数字，甚至整个对象。一些开关参数（如 `-Force`）不需要值，出现即生效。

### 2.3 别名
为了照顾习惯 CMD/Linux 的用户，PowerShell 内置大量别名。
- `dir`  → `Get-ChildItem`
- `cd`   → `Set-Location`
- `cls`  → `Clear-Host`
- `copy` → `Copy-Item`
- `del`  → `Remove-Item`
- `type` → `Get-Content`

你可以查看所有别名：`Get-Alias`。

### 2.4 获取帮助——最重要的命令
掌握 `Get-Help` 是自学的关键。任何时候都可以这样使用：
```powershell
# 基本帮助
Get-Help Get-Process

# 详细帮助（包含参数说明和示例）
Get-Help Get-Process -Detailed

# 完整帮助（包含所有技术信息）
Get-Help Get-Process -Full

# 仅查看示例
Get-Help Get-Process -Examples

# 在线帮助（在浏览器中打开最新文档）
Get-Help Get-Process -Online
```
如果遇到“Get-Help 无法在此计算机上找到...”的提示，请以管理员身份运行一次 `Update-Help` 来下载完整帮助文件（可能需要一点时间）。

---

## 第3章：文件系统与基础操作

PowerShell 具有“提供程序”的概念，文件系统、注册表、环境变量等都可以像磁盘驱动器一样访问。

### 3.1 导航
```powershell
# 查看当前位置
Get-Location

# 切换目录（支持相对路径和绝对路径）
Set-Location C:\Windows
cd D:\Documents

# 列出当前目录下的文件和文件夹
Get-ChildItem
# 简写
dir
ls

# 只显示文件夹
Get-ChildItem -Directory

# 只显示文件
Get-ChildItem -File

# 递归显示所有子目录内容
Get-ChildItem -Recurse

# 筛选名字（支持通配符 * ?）
Get-ChildItem *.txt
Get-ChildItem -Filter *.log
```

### 3.2 创建、复制、移动、删除
```powershell
# 创建新目录
New-Item -Path C:\Temp -ItemType Directory
# 创建空文件
New-Item -Path C:\Temp\test.txt -ItemType File

# 复制文件
Copy-Item C:\Temp\test.txt D:\Backup\test_copy.txt
# 复制整个目录（含子内容）
Copy-Item C:\Temp D:\Backup -Recurse

# 移动文件（等同于剪切）
Move-Item D:\Backup\test_copy.txt C:\Temp\moved.txt

# 删除文件
Remove-Item C:\Temp\moved.txt
# 删除包含内容的文件夹，使用 -Recurse -Force
Remove-Item C:\Temp -Recurse -Force
```
**注意**：PowerShell 删除文件默认是永久删除，不经过回收站，请谨慎操作。

### 3.3 查看和编辑文件内容
```powershell
# 查看文本文件全部内容
Get-Content C:\Windows\System32\drivers\etc\hosts

# 查看前10行
Get-Content .\log.txt -TotalCount 10

# 将字符串输出到文件（覆盖）
"Hello PowerShell" | Out-File C:\Temp\hello.txt
# 或使用 Set-Content（更适用于纯文本）
Set-Content C:\Temp\hello.txt "Hello PowerShell"

# 追加内容
"Append line" | Add-Content C:\Temp\hello.txt
```

---

## 第4章：管道与对象——PowerShell的灵魂

### 4.1 管道传递对象
在 CMD 中，管道传递的是纯文本。而 PowerShell 的管道（`|`）传递的是**完整的 .NET 对象**。这意味着你可以直接访问输出的属性并调用其方法。

```powershell
# 获取所有进程，并把它们“喂”给 Stop-Process
Get-Process notepad | Stop-Process
```
当左侧命令产生的对象具有右侧命令参数所需的属性时，绑定会自动发生。例如 `Get-Service -Name Spooler | Stop-Service`，`Get-Service` 输出的服务对象的 `Name` 属性会自动绑定给 `Stop-Service` 的 `-Name` 参数。

### 4.2 探索对象：Get-Member
想知道某个命令输出对象的类型和所有属性、方法？使用 `Get-Member`（别名 `gm`）。
```powershell
Get-Process | Get-Member
# 结果会列出 TypeName，以及属性(Property)和方法(Method)
```

### 4.3 选择和过滤对象
#### Select-Object：选择对象的部分属性，或取前/后几个
```powershell
# 只显示进程的 Name 和 CPU 时间
Get-Process | Select-Object Name, CPU

# 显示前5条记录
Get-Process | Select-Object -First 5

# 选择最后10条
Get-ChildItem | Select-Object -Last 10

# 展开单个属性，返回属性值本身的集合
Get-Process | Select-Object -ExpandProperty Name
```

#### Where-Object：根据条件过滤对象（别名 `?`）
```powershell
# 获取所有 CPU 时间大于 10 的进程
Get-Process | Where-Object { $_.CPU -gt 10 }
# 简化写法：
Get-Process | Where-Object CPU -gt 10

# $_ 表示当前管道中的对象
# 获取所有名字以 "a" 开头的进程
Get-Process | Where-Object { $_.Name -like 'a*' }
```

#### ForEach-Object：对每个对象执行操作（别名 `%`）
```powershell
# 停止所有名为 "notepad" 的进程（另一种方式）
Get-Process notepad | ForEach-Object { $_.Kill() }

# 取每个文件名及其长度，构建自定义字符串
Get-ChildItem | ForEach-Object { "File: $($_.Name), Size: $($_.Length)" }

# 使用成员调用语法（PowerShell 3.0+）
Get-Process notepad | ForEach-Object Kill
```

#### Sort-Object：排序
```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
```

#### Group-Object：分组统计
```powershell
Get-ChildItem | Group-Object Extension | Sort-Object Count -Descending
```

---

## 第5章：格式化输出与导出

数据不仅要在屏幕上看，还需要输出为报表。

```powershell
# 格式化为列表（显示所有属性，适合属性较多的对象）
Get-Service | Format-List *

# 格式化为表格（默认）
Get-Process | Format-Table Name, Id, CPU -AutoSize

# 导出为 CSV 文件
Get-Process | Select-Object Name, Id, CPU | Export-Csv C:\proc.csv -NoTypeInformation

# 从 CSV 导入
$data = Import-Csv C:\proc.csv

# 转换为 HTML 报告
Get-Service | ConvertTo-Html | Out-File C:\services.html

# 使用 Out-GridView 弹出筛选窗口（Windows 下非常好用）
Get-Process | Out-GridView -PassThru
```
注意：`Format-*` 命令用于屏幕显示，会改变对象类型，切勿在需要进一步处理数据的管道中使用它们，只应该放在管道的末端。

---

## 第6章：变量、数组和哈希表

### 6.1 变量
变量以 `$` 开头，弱类型，不区分大小写。
```powershell
$name = "PowerShell"
$number = 100
$today = Get-Date

# 获取变量类型
$today.GetType()

# 环境变量通过 $env: 驱动器访问
$env:PATH
$env:COMPUTERNAME

# 内置自动变量
$PSVersionTable   # PowerShell 版本信息
$_                # 当前管道对象
$PROFILE          # 当前用户的配置文件路径
$HOME             # 用户主目录
$pwd              # 当前工作目录
```

### 6.2 字符串
```powershell
# 单引号字面量：内容就是字面意思，不展开变量
$value = 'The path is $HOME'
# 输出：The path is $HOME

# 双引号可展开变量和子表达式
$value = "The path is $HOME"
# 输出：The path is C:\Users\YourName

# 子表达式 $( )
$value = "Date: $(Get-Date -Format 'yyyy-MM-dd')"

# 多行字符串（@' '@ 或 @" "@）
$multiline = @"
Line1
Line2
Variable: $name
"@
```

### 6.3 数组
```powershell
# 创建数组
$arr = 1, 2, 3, "PowerShell"
$arr = @()  # 空数组

# 访问元素（索引从0开始）
$arr[0]
$arr[-1]    # 最后一个元素
$arr[1..3]  # 范围切片

# 添加元素（数组是固定大小，加法会创建新数组）
$arr = $arr + "new item"
# 更高效的方式：使用 ArrayList 或 List
$list = [System.Collections.ArrayList]@()
$list.Add("item1") | Out-Null

# 遍历数组
$arr | ForEach-Object { "Item: $_" }
```

### 6.4 哈希表（关联数组）
```powershell
$hash = @{
    Name    = "PowerShell"
    Version = 5.1
    Enabled = $true
}

# 访问
$hash["Name"]
$hash.Version

# 添加/修改
$hash["NewKey"] = "some value"

# 作为参数（splatting）
$params = @{
    Name = "Spooler"
    ComputerName = "Server01"
}
Get-Service @params
```

---

## 第7章：运算符

### 7.1 比较运算符
与大多数语言不同，PowerShell 不使用 `==` 或 `>`，而是用字母形式，**默认不区分大小写**。
- 相等：`-eq`  不等：`-ne`
- 大于：`-gt`  大于等于：`-ge`
- 小于：`-lt`  小于等于：`-le`
- 字符串比较：`"abc" -eq "ABC"` 结果为 `True`。要区分大小写，前缀加 `c`：`-ceq`、`-cne`、`-clike` 等。

### 7.2 模式匹配
- **`-like` / `-notlike`**：支持通配符 `*`（任意字符）和 `?`（单个字符）。
  ```powershell
  "PowerShell" -like "*shell"  # True
  "PowerShell" -like "P?wer*"   # True
  ```
- **`-match` / `-notmatch`**：使用正则表达式。
  ```powershell
  "abc123" -match "\d+"  # True，匹配数字
  # 自动填充 $Matches 变量
  if ("Version: 5.1" -match "Version: (\d+\.\d+)") {
      $Matches[1]   # 输出 5.1
  }
  ```
- **`-replace`**：正则替换运算符。
  ```powershell
  "file.txt" -replace "\.txt$", ".log"  # 结果：file.log
  ```

### 7.3 包含运算符
```powershell
$arr = 1, 2, 3
2 -in $arr     # True
$arr -contains 2  # True
```
对于字符串，检查子串：
```powershell
"PowerShell" -contains "Shell"  # False! -contains 用于集合。
"PowerShell" -like "*Shell*"    # 这才是子串检查。
```

---

## 第8章：流程控制

### 8.1 条件语句
```powershell
$score = 85
if ($score -ge 90) {
    "优秀"
} elseif ($score -ge 80) {
    "良好"
} else {
    "其他"
}

# Switch 语句
$color = "red"
switch ($color) {
    "red"   { "Stop" }
    "yellow"{ "Wait" }
    default { "Go" }
}
```

### 8.2 循环
- **For**：
  ```powershell
  for ($i = 0; $i -lt 10; $i++) { Write-Host $i }
  ```
- **ForEach**（遍历集合）：
  ```powershell
  foreach ($item in Get-Process) {
      if ($item.WorkingSet64 -gt 100MB) {
          Write-Host $item.Name
      }
  }
  ```
- **While**：
  ```powershell
  $i = 0
  while ($i -lt 3) { $i++; $i }
  ```
- **Do...While/Until**：
  ```powershell
  do { $i--; $i } while ($i -gt 0)
  do { $i++; $i } until ($i -ge 5)
  ```
可以使用 `break` 跳出循环，`continue` 跳过当前迭代。

---

## 第9章：函数与脚本

### 9.1 简单函数
```powershell
function Get-Greeting {
    param(
        [string]$Name = "World"
    )
    "Hello, $Name!"
}
Get-Greeting -Name "User"
```
推荐使用 `param` 块定义参数，并且可以为参数指定类型、默认值。

### 9.2 高级函数（像 Cmdlet 一样工作）
添加 `[CmdletBinding()]` 可解锁公用参数：`-Verbose`、`-Debug`、`-ErrorAction` 等。
```powershell
function Invoke-Something {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory=$true)]
        [string]$Path
    )
    Write-Verbose "正在处理路径：$Path"
    # 具体操作
}
```
可以添加注释帮助（基于注释的帮助），只要在函数体内加入特定注释块，就可以被 `Get-Help` 识别：
```powershell
<#
.SYNOPSIS
    问候用户。
.DESCRIPTION
    返回一句问候语。
.PARAMETER Name
    要问候的人的名字。
.EXAMPLE
    Get-Greeting -Name "Alice"
#>
```

### 9.3 脚本文件 `.ps1`
将函数和命令写入 `.ps1` 文件，就可以像命令一样调用。
```powershell
# MyScript.ps1
param($Folder)
Get-ChildItem $Folder -Recurse | Measure-Object Length -Sum
```
执行脚本前，你可能需要修改执行策略：
```powershell
# 查看当前执行策略
Get-ExecutionPolicy

# 设置为允许本地脚本和远程签名脚本运行（推荐）
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```
运行脚本：
```powershell
# 必须使用完整路径或 .\ 前缀
.\MyScript.ps1 -Folder C:\Temp
# 或直接运行并传递参数
powershell.exe -File "C:\Scripts\MyScript.ps1" -Folder C:\Temp
```

### 9.4 作用域
函数内部变量默认只存在于函数内。可以通过 `$script:`、`$global:` 修饰符或 `Set-Variable -Scope` 指定作用域，但要慎用全局变量。

---

## 第10章：错误处理

### 10.1 终止与非终止错误
- 非终止错误：大多数 Cmdlet 产生的错误，只写入错误流但不停止脚本执行。
- 终止错误：严重的错误或遇到 `throw` 语句，会立即停止。
可以通 `-ErrorAction` 公用参数控制行为：
```powershell
# 静默忽略错误
Get-ChildItem "不存在的路径" -ErrorAction SilentlyContinue

# 将错误当作终止错误
Get-ChildItem "不存在路径" -ErrorAction Stop

# 整个脚本级别设置
$ErrorActionPreference = "Stop"
```
### 10.2 Try/Catch/Finally
只能捕获**终止错误**，因此经常配合 `-ErrorAction Stop` 使用。
```powershell
try {
    Get-Content "C:\nofile.txt" -ErrorAction Stop
} catch {
    Write-Warning "捕获错误：$($_.Exception.Message)"
} finally {
    Write-Host "无论如何都会执行"
}
```
`$Error` 自动变量是一个错误记录数组，记录所有错误详情。

---

## 第11章：模块管理

模块是组织 PowerShell 功能的包，可以包含函数、Cmdlet、变量等。
```powershell
# 列出已加载的模块
Get-Module

# 列出系统上所有可用模块
Get-Module -ListAvailable

# 导入模块
Import-Module ActiveDirectory

# 从 PowerShell Gallery 安装模块（需要管理员，可能需要先安装 NuGet 提供程序）
Install-Module -Name PSWindowsUpdate -Force -Scope CurrentUser

# 搜索模块
Find-Module -Name *Print*
```
如果要创建自己的模块，将 `.psm1` 文件放在 `$env:PSModulePath` 包含的目录下，命名文件夹与模块文件一致即可。

---

## 第12章：远程管理（PowerShell Remoting）

这是运维自动化的利器，基于 WinRM 服务。

### 12.1 启用远程
在目标计算机上以管理员身份运行：
```powershell
Enable-PSRemoting -Force
# 如果是在工作组环境，还需要配置 TrustedHosts
Set-Item WSMan:\localhost\Client\TrustedHosts -Value "IP_or_ComputerName" -Force
```

### 12.2 一对一交互会话
```powershell
Enter-PSSession -ComputerName Server01
# 现在你就在远程计算机上，输入 exit 退出
```

### 12.3 一对多命令执行
```powershell
# 在单台或多台远程计算机上执行脚本块
Invoke-Command -ComputerName Server01, Server02 -ScriptBlock { Get-Service BITS }

# 使用已建立的会话（复用连接）
$session = New-PSSession -ComputerName Server01
Invoke-Command -Session $session -ScriptBlock { Get-Process }
Remove-PSSession $session

# 把本地变量传递到远程
$localVar = "data"
Invoke-Command -ComputerName Server01 -ScriptBlock { $using:localVar }
```
Windows 10 默认限制，必须修改 WinRM 或在域环境下才能方便使用远程。

---

## 第13章：实用技巧与最佳实践

1. **Tab 补全**：输入命令或参数的部分字母后按 Tab 键，PowerShell 会帮你自动补全。Ctrl+Space 可列出所有选项（ISE 或 VSCode 中）。
2. **历史记录**：按 F7 弹出历史窗口（控制台），或使用 `Get-History`、`Invoke-History`。
3. **Transcript 记录**：`Start-Transcript` 开始将会话输出记录到文件，`Stop-Transcript` 停止。适合审计。
4. **使用凭据**：`$cred = Get-Credential` 会弹出安全登录框，然后可将 `-Credential $cred` 传递给支持的命令。
5. **计算属性**：Select-Object 时动态创建列。
   ```powershell
   Get-ChildItem | Select-Object Name, @{Name="Size(KB)"; Expression={[math]::Round($_.Length/1KB,2)}}
   ```
6. **避免使用别名和简写参数名编写正式脚本**，提升可读性。但在交互式使用时随意。
7. **永远不要直接使用 `Format-*` 命令的输出进行下一步数据处理**，它只应在管道最末端用于显示。
8. **使用 VSCode 配合 PowerShell 扩展**，有强大的 IntelliSense、调试、语法检查功能，是推荐的脚本开发环境。

---

## 第14章：常用 Cmdlet 速查

| 类别 | Cmdlet | 说明 |
|------|--------|------|
| 文件系统 | Get-ChildItem | 列出目录内容 |
| | Copy-Item | 复制 |
| | Move-Item | 移动/重命名 |
| | Remove-Item | 删除 |
| 内容 | Get-Content | 读取内容 |
| | Set-Content | 写入内容 |
| | Out-File | 输出到文件 |
| 系统管理 | Get-Process | 获取进程 |
| | Stop-Process | 终止进程 |
| | Get-Service | 获取服务 |
| | Start/Stop/Restart-Service | 服务控制 |
| | Get-EventLog / Get-WinEvent | 查看事件日志 |
| 网络 | Test-Connection | ping |
| | Test-NetConnection | 端口和路由测试 |
| | Resolve-DnsName | DNS解析 |
| 对象处理 | Select-Object | 选择属性 |
| | Where-Object | 过滤 |
| | ForEach-Object | 循环处理 |
| | Sort-Object | 排序 |
| 模块化 | Import-Module | 导入模块 |
| | Install-Module | 安装模块 |

---
