# PowerShell 别名（Alias）全解文档

## 一、概述
别名（Alias）是 PowerShell 为简化命令输入、兼容 CMD 与 Linux Shell 使用习惯而提供的命令快捷方式。每一个别名最终都会映射到一个真实的 PowerShell Cmdlet 或可执行程序，执行逻辑、参数与管道行为与原命令完全一致。

以下为 `Get-Alias` 返回的所有别名分类详解，其中包含**系统内置别名**与 Conda 环境注入的**扩展别名**。

---

## 二、文件与目录操作
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| cd / chdir / sl | `Set-Location` | 切换当前工作目录，兼容 CMD、Linux 的 `cd` 使用习惯 |
| dir / ls / gci | `Get-ChildItem` | 列出当前目录下的文件、子目录及其属性；`dir` 兼容 CMD，`ls` 兼容 Linux |
| copy / cp / cpi | `Copy-Item` | 复制文件或目录，支持通配符、递归复制与跨驱动器操作 |
| move / mv / mi | `Move-Item` | 移动文件或目录，也可用于同路径下重命名 |
| del / erase / rm / rmdir / rd / ri | `Remove-Item` | 删除文件、目录或注册表项；兼容 CMD 与 Linux 删除命令习惯 |
| ren / rni | `Rename-Item` | 重命名文件或目录，支持批量通配符重命名 |
| gi | `Get-Item` | 获取指定文件、目录、注册表项等对象本身，用于读取元数据与属性 |
| ni | `New-Item` | 新建文件、目录、注册表项等，通过 `-ItemType` 指定创建类型 |
| si | `Set-Item` | 设置指定项的值，可用于修改文件内容、变量值、注册表键值等 |
| pushd | `Push-Location` | 将当前目录压入目录栈，并切换到目标目录 |
| popd | `Pop-Location` | 从目录栈弹出最近一次保存的目录，回到上一个工作路径 |
| pwd / gl | `Get-Location` | 获取当前工作目录的完整绝对路径 |
| mount / ndr | `New-PSDrive` | 创建 PowerShell 逻辑驱动器，可映射网络共享、注册表、证书存储等 |
| rdr | `Remove-PSDrive` | 删除已创建的 PowerShell 逻辑驱动器 |
| gdr | `Get-PSDrive` | 列出当前所有 PowerShell 驱动器（含盘符、注册表、环境变量等） |
| cvpa | `Convert-Path` | 将 PowerShell 路径转换为操作系统原生的文件系统路径 |
| rvpa | `Resolve-Path` | 解析路径中的通配符与相对路径，返回完整的绝对路径 |
| md | `mkdir` | 创建新目录，是 `New-Item -ItemType Directory` 的简化函数 |

---

## 三、内容读写与属性管理
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| cat / type / gc | `Get-Content` | 读取文本文件内容并逐行输出；`cat` 兼容 Linux，`type` 兼容 CMD |
| ac | `Add-Content` | 向文件末尾追加内容，不会覆盖原有内容 |
| sc | `Set-Content` | 写入/覆盖文件内容，会清空原有内容后写入新数据 |
| clc | `Clear-Content` | 清空文件内容，但保留文件本身 |
| cli | `Clear-Item` | 清空指定项的内容（如文件、变量、别名） |
| clp | `Clear-ItemProperty` | 清空文件、注册表项等对象的属性值 |
| gp | `Get-ItemProperty` | 获取项的所有属性（如文件版本、大小，注册表键值） |
| gpv | `Get-ItemPropertyValue` | 直接获取项指定属性的值，比 `Get-ItemProperty` 更简洁 |
| sp | `Set-ItemProperty` | 设置项的指定属性值 |
| rp | `Remove-ItemProperty` | 删除项的指定属性 |
| mp | `Move-ItemProperty` | 在不同项之间移动属性及其值 |
| rnp | `Rename-ItemProperty` | 重命名项的属性名称 |
| cpp | `Copy-ItemProperty` | 将一个项的属性及其值复制到另一个项 |
| sls | `Select-String` | 在文本或文件中搜索匹配字符串，类似 Linux 的 `grep`，支持正则表达式 |
| fhx | `Format-Hex` | 将文件或二进制数据以「十六进制+ASCII」形式展示，用于二进制分析 |
| tee | `Tee-Object` | 将输出同时发送到控制台和文件，类似 Linux 的 `tee` 命令 |
| CFS | `ConvertFrom-String` | 将非结构化文本解析转换为结构化的 PowerShell 对象 |
| epcsv | `Export-Csv` | 将 PowerShell 对象集合导出为 CSV 格式文件 |
| ipcsv | `Import-Csv` | 读取 CSV 文件，将其转换为 PowerShell 对象集合 |

---

## 四、对象筛选与集合处理
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| `%` / foreach | `ForEach-Object` | 遍历集合中的每一个对象并执行脚本块，是管道中最常用的循环命令 |
| `?` / where | `Where-Object` | 根据条件筛选管道中的对象，只保留符合条件的结果 |
| select | `Select-Object` | 选择对象的指定属性，或截取前N个/后N个对象，支持去重、扩展属性 |
| sort | `Sort-Object` | 按指定属性对对象集合进行升序/降序排序 |
| group | `Group-Object` | 按指定属性对对象进行分组，返回分组统计结果 |
| compare / diff | `Compare-Object` | 对比两组对象的差异，标识出各自独有的项 |
| gu | `Get-Unique` | 从已排序的集合中提取唯一项，去除重复对象 |
| measure | `Measure-Object` | 统计对象数量、数值的和/平均值/最大值/最小值等 |
| gm | `Get-Member` | 查看对象的类型名称、所有属性和方法，是调试与学习的核心命令 |

---

## 五、进程与作业管理
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| ps / gps | `Get-Process` | 获取当前系统所有运行进程的信息（PID、内存占用、CPU使用率等） |
| kill / spps | `Stop-Process` | 强制停止指定 PID 或名称的进程 |
| saps / start | `Start-Process` | 启动新进程，可运行程序、打开文件/URL，支持指定运行参数与权限 |
| sajb | `Start-Job` | 在后台启动一个 PowerShell 作业，不阻塞前台命令执行 |
| spjb | `Stop-Job` | 停止正在运行的后台作业 |
| gjb | `Get-Job` | 列出所有后台作业及其状态（运行中、已完成、失败等） |
| rcjb | `Receive-Job` | 获取后台作业的执行输出结果 |
| rjb | `Remove-Job` | 删除已完成或停止的后台作业 |
| sujb | `Suspend-Job` | 挂起（暂停）正在运行的后台作业 |
| rujb | `Resume-Job` | 恢复被挂起的后台作业 |
| wjb | `Wait-Job` | 阻塞当前线程，直到指定的后台作业执行完成 |

---

## 六、系统服务管理
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| gsv | `Get-Service` | 获取系统所有服务的状态、名称、启动类型等信息 |
| sasv | `Start-Service` | 启动指定的系统服务 |
| spsv | `Stop-Service` | 停止指定的系统服务 |

---

## 七、格式化与输出控制
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| ft | `Format-Table` | 将输出格式化为表格形式，可自定义显示列与列宽 |
| fl | `Format-List` | 将输出格式化为列表形式，每行显示一个属性，适合查看详细信息 |
| fw | `Format-Wide` | 将输出格式化为多列宽列表，适合仅展示单个属性的场景 |
| fc | `Format-Custom` | 使用自定义视图格式化输出，适配特殊对象类型 |
| echo / write | `Write-Output` | 将对象写入输出管道，是 PowerShell 默认的输出命令 |
| oh | `Out-Host` | 将输出直接发送到控制台主机，强制立即显示 |
| lp | `Out-Printer` | 将输出内容发送到打印机进行打印 |
| ogv | `Out-GridView` | 弹出图形化表格窗口展示输出，支持筛选、排序、复制 |
| clear / cls | `Clear-Host` | 清空控制台屏幕的所有内容，兼容 CMD 的 `cls` 习惯 |

---

## 八、命令历史与远程会话
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| h / history / ghy | `Get-History` | 查看当前会话中执行过的所有命令历史 |
| r / ihy | `Invoke-History` | 重新执行历史记录中的指定命令 |
| clhy | `Clear-History` | 清空当前会话的命令历史记录 |
| nsn | `New-PSSession` | 创建一个 PowerShell 远程会话，用于批量远程管理 |
| gsn | `Get-PSSession` | 列出当前所有已创建的 PowerShell 会话 |
| cnsn | `Connect-PSSession` | 重新连接到已断开的远程会话 |
| dnsn | `Disconnect-PSSession` | 断开当前远程会话（会话保留在远程端） |
| rsn | `Remove-PSSession` | 关闭并删除指定的 PowerShell 会话 |
| rcsn | `Receive-PSSession` | 接收断开会话中累积的输出结果 |
| icm | `Invoke-Command` | 在本地或远程计算机上执行脚本块，是批量远程执行的核心命令 |
| etsn | `Enter-PSSession` | 进入交互式远程会话，像操作本地机器一样操作远程主机 |
| exsn | `Exit-PSSession` | 退出交互式远程会话，回到本地会话 |
| epsn | `Export-PSSession` | 将远程会话中的命令导出为本地模块 |
| ipsn | `Import-PSSession` | 导入远程会话中的命令到本地会话，实现透明远程调用 |
| npssc | `New-PSSessionConfigurationFile` | 创建 PowerShell 会话配置文件，用于自定义远程会话环境 |

---

## 九、别名、变量与模块
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| gal | `Get-Alias` | 获取当前会话中所有别名的列表与映射关系 |
| sal / nal | `Set-Alias` / `New-Alias` | 创建新别名或修改已有别名的指向 |
| epal | `Export-Alias` | 将当前所有别名导出到文件，便于后续导入复用 |
| ipal | `Import-Alias` | 从文件中导入别名配置 |
| gv | `Get-Variable` | 获取当前会话中的变量及其值 |
| set / sv | `Set-Variable` | 创建新变量或修改变量的值、作用域、选项 |
| nv | `New-Variable` | 创建新变量，可设置只读、常量等高级选项 |
| rv | `Remove-Variable` | 删除指定的变量 |
| clv | `Clear-Variable` | 清空变量的值，但保留变量本身 |
| gmo | `Get-Module` | 列出已导入的 PowerShell 模块，或查看系统可用模块 |
| ipmo | `Import-Module` | 导入指定的 PowerShell 模块，加载其中的命令与函数 |
| rmo | `Remove-Module` | 从当前会话中移除已导入的模块 |
| nmo | `New-Module` | 在内存中创建一个动态模块，无需实体文件 |
| asnp | `Add-PSSnapin` | 加载传统 PowerShell 管理单元（早期扩展方式） |
| gsnp | `Get-PSSnapin` | 列出已加载的 PowerShell 管理单元 |
| rsnp | `Remove-PSSnapin` | 卸载已加载的 PowerShell 管理单元 |

---

## 十、网络请求与脚本执行
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| curl / wget / iwr | `Invoke-WebRequest` | 发送 HTTP/HTTPS/FTP 请求，获取网页、文件或接口响应；兼容 Linux 命令习惯 |
| irm | `Invoke-RestMethod` | 专门用于调用 REST API，自动解析 JSON/XML 响应为 PowerShell 对象 |
| iex | `Invoke-Expression` | 将字符串作为 PowerShell 命令执行，常用于动态执行代码 |
| ii | `Invoke-Item` | 用系统默认程序打开指定文件、目录或 URL |
| sleep | `Start-Sleep` | 暂停脚本/命令执行指定的秒数或毫秒 |
| man | `help` | 查看命令的帮助文档，包含语法、参数、示例与说明 |
| ise | `powershell_ise.exe` | 启动 PowerShell ISE 图形化脚本编辑器 |
| shcm | `Show-Command` | 弹出图形化界面，可视化填写命令参数，适合新手使用 |

---

## 十一、系统信息与调试工具
| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| gcm | `Get-Command` | 查找系统中所有可用命令（Cmdlet、函数、别名、外部程序等） |
| gcs | `Get-PSCallStack` | 获取当前脚本的调用堆栈，用于定位错误与调试 |
| dbp | `Disable-PSBreakpoint` | 禁用指定断点，命中时不中断执行 |
| ebp | `Enable-PSBreakpoint` | 启用已禁用的断点 |
| gbp | `Get-PSBreakpoint` | 列出当前所有设置的断点 |
| sbp | `Set-PSBreakpoint` | 设置行断点、变量断点或命令断点 |
| rbp | `Remove-PSBreakpoint` | 删除指定的断点 |
| trcm | `Trace-Command` | 跟踪指定命令的执行过程，输出详细的调试信息 |
| gcb | `Get-Clipboard` | 获取系统剪贴板中的文本内容 |
| scb | `Set-Clipboard` | 将文本内容写入系统剪贴板 |
| gin | `Get-ComputerInfo` | 获取完整的计算机系统信息（硬件、操作系统、网络配置等） |
| gtz | `Get-TimeZone` | 获取当前系统的时区设置 |
| stz | `Set-TimeZone` | 修改系统的时区设置 |
| gwmi | `Get-WmiObject` | 通过 WMI 获取系统信息（旧版接口，推荐使用 CIM 命令替代） |
| iwmi | `Invoke-WmiMethod` | 调用 WMI 类的方法 |
| swmi | `Set-WmiInstance` | 创建或更新 WMI 类的实例 |
| rwmi | `Remove-WmiObject` | 删除 WMI 实例 |

---

## 十二、Conda 环境专属别名
由 Conda 模块注入，仅在安装并初始化 Conda 后可用，用于简化 Conda 环境操作。

| 别名 | 对应原命令 | 功能说明 |
|------|------------|----------|
| conda | `Invoke-Conda` | Conda 命令入口，代理所有 conda 子命令 |
| etenv | `Enter-CondaEnvironment` | 激活指定的 Conda 虚拟环境 |
| exenv | `Exit-CondaEnvironment` | 退出当前 Conda 环境，回到 base 基础环境 |
| genv | `Get-CondaEnvironment` | 列出系统中所有已创建的 Conda 环境及其状态 |

---

## 三、补充说明
1. **别名本质是快捷方式**：所有别名最终都会映射到真实的 Cmdlet，参数、管道行为与原命令完全一致，无功能差异。
2. **作用域限制**：默认创建的别名仅在当前会话有效，关闭 PowerShell 后失效；如需永久生效，需写入配置文件 `$PROFILE`。
3. **命令优先级**：当别名、函数、Cmdlet 同名时，PowerShell 执行优先级为：别名 > 函数 > Cmdlet > 外部程序。
4. **WMI 命令说明**：`gwmi` 等 WMI 系列别名属于兼容旧版的命令；PowerShell 3.0 之后推荐使用 CIM 命令（如 `Get-CimInstance`），功能更强、更安全且支持远程操作。

需要我补充**常用别名的实战示例**，帮你快速上手使用吗？