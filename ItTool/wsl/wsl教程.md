# wsl教程
## WSL2
### 安装前准备

**检查 Windows 版本**

- Win10：版本≥2004（内部版本 19041+），按`Win+R`输入`winver`查看。
- Win11：无版本限制。

**开启 CPU 虚拟化（必做）**

- 重启电脑，按品牌快捷键进入 BIOS（联想 F2、戴尔 F12、华硕 Del、惠普 F10）。
- 找到`Intel VT-x`（Intel）/`AMD-V`（AMD），设为`Enabled`，F10 保存重启。

### 一键安装

1. 以**管理员身份**打开 PowerShell / 终端。
2. 执行一键安装命令：

```powershell
wsl --install
```

3. 命令自动完成：启用 WSL + 虚拟机平台、安装 Linux 内核、设 WSL2 为默认、安装 Ubuntu（默认）
4. 安装完成后**重启电脑**Microsoft Learn。
5. 重启后自动启动 Ubuntu，按提示设置**Linux 用户名 + 密码**（密码输入不显示，正常输入即可）。
6. 安装指定发行版

+ \# 查看可安装的发行版 
  + `wsl --list --online`
+ \# 安装指定发行版（如Debian）
  + `wsl --install -d Debian`

### 常用 WSL 命令与基础操作

#### 常用 WSL 命令

| 命令                             | 作用                          |
| -------------------------------- | ----------------------------- |
| `wsl`                            | 启动默认发行版                |
| `wsl -d <发行版名>`              | 启动指定发行版                |
| `wsl --list --verbose`           | 查看所有发行版（含 WSL 版本） |
| `wsl --set-version <发行版名> 2` | 将发行版转为 WSL2             |
| `wsl --shutdown`                 | 关闭所有 WSL 发行版           |
| `wsl --unregister <发行版名>`    | 卸载发行版（删除数据）        |
| `wsl --status`                   | 查看 WSL 状态与版本           |

#### WSL 内基础操作

##### 文件互访

- Windows 访问 WSL：文件资源管理器地址栏输入`\\wsl$`，即可查看所有发行版文件Microsoft Learn。
- WSL 访问 Windows：Windows 盘挂载在`/mnt/<盘符>`，

##### 打开文件管理器（WSL 内）

```bash
# 打开当前目录的Windows文件管理器
explorer.exe .
```

### 管理wsl发行版

核心原理：WSL2 的发行版本质是一个 `ext4.vhdx` 虚拟磁盘文件，通过微软官方标准的**导出→注销→导入**流程，即可将虚拟文件放到非系统盘，全程无兼容性风险，操作简单可控。

>所有操作均在**管理员身份的 PowerShell**中执行，目标路径建议纯英文、无空格、无特殊字符（例：`D:\WSL\Ubuntu`，禁止中文路径）

#### 已安装的 WSL 发行版，迁移到其他盘符（最常用）

适合已经把发行版装在 C 盘，想迁移到 D/E/F 等非系统盘释放 C 盘空间的情况。

1. **关闭所有 WSL 实例，确保数据一致性**

```powershell
# 关闭所有运行中的WSL发行版
wsl --shutdown
# 验证是否全部停止，STATE列应全部显示Stopped
wsl -l -v
```

2. 导出当前发行版为备份 tar 包

先通过 `wsl -l -v` 确认要迁移的发行版名称（例：`Ubuntu`），执行导出命令：

```powershell
# 命令格式：wsl --export <发行版名称> <备份tar包完整路径>
# 示例：将Ubuntu导出到D盘WSL文件夹，备份文件名为ubuntu_backup.tar
wsl --export Ubuntu D:\WSL\ubuntu_backup.tar
```

+ 等待导出完成，tar 包大小和发行版实际占用空间一致，无报错即为成功；
+ 提前创建目标文件夹（例：`D:\WSL`），避免路径不存在报错。

3. 注销原 C 盘的发行版（释放 C 盘空间）

必须确认备份 tar 包已成功生成后再执行，注销后原发行版数据会被彻底删除，无法恢复

```powershell
# 命令格式：wsl --unregister <发行版名称>
wsl --unregister Ubuntu
```

⚠️  必须确认备份 tar 包已成功生成后再执行，注销后原发行版数据会被彻底删除，无法恢复

```powershell
# 命令格式：wsl --unregister <发行版名称>
wsl --unregister Ubuntu
```

执行后，`wsl -l -v` 将看不到该发行版，原 C 盘占用空间会直接释放。

4. 导入发行版到目标盘符的自定义路径

```powershell
# 命令格式：wsl --import <新发行版名称> <目标安装路径> <备份tar包路径> --version 2
# 示例：将Ubuntu导入到D:\WSL\Ubuntu文件夹，强制指定为WSL2版本
wsl --import Ubuntu D:\WSL\Ubuntu D:\WSL\ubuntu_backup.tar --version 2
```

+ 新发行版名称可和原名称一致，也可自定义；

+ `--version 2` 必须加上，避免自动降级为 WSL1；

+ 导入完成后，会在目标路径自动生成 `ext4.vhdx` 虚拟磁盘文件，这就是 WSL 的核心文件。
5. 修复默认用户（迁移后默认变成 root 的关键修复）

迁移后启动 WSL 会默认以 root 用户登录，需恢复之前的普通用户，操作如下：

+ 先启动 WSL：`wsl -d Ubuntu`
+ 在 WSL 内执行以下命令，创建 / 修改 wsl.conf 配置文件，替换`你的用户名`为你之前设置的 Linux 用户名：

```bash
sudo tee /etc/wsl.conf << EOF
[user]
default=lk
EOF
```

+ 退出 WSL，执行 `wsl --shutdown` 重启实例，再次启动就会默认用你指定的普通用户登录。

5. 验证迁移成功

```powershell
wsl -l -v
```

#### 全新安装发行版，直接安装到非系统盘

适合还没安装任何发行版，想直接装到非系统盘，完全不占用 C 盘空间的情况。

1. 下载官方发行版离线镜像包

+ 先查看微软官方支持的可安装发行版列表：

```powershell
wsl -l -o
```

+ 打开微软官方 WSL 发行版下载页，下载你需要的版本（例：Ubuntu、Debian），得到后缀为 `.appx` 的安装包：

+ 官方下载地址：https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#downloading-distributions

2. 提取系统根文件包

+ 将下载的 `.appx` 文件后缀改为 `.zip`，用解压软件解压到临时文件夹；
+ 解压后，在文件夹里找到 `install.tar.gz`（部分发行版叫`rootfs.tar.gz`），这就是系统根文件系统包，复制到目标路径（例：`D:\WSL\install.tar.gz`）。

3. 直接导入安装到目标盘符

```powershell
# 命令格式：wsl --import <发行版名称> <目标安装路径> <rootfs包路径> --version 2
# 示例：安装Ubuntu到D:\WSL\Ubuntu路径
wsl --import Ubuntu D:\WSL\Ubuntu D:\WSL\install.tar.gz --version 2
```

4. 初始化设置用户名和密码

+ 启动 WSL：`wsl -d Ubuntu`
+ 此时默认是 root 用户，创建你的普通用户并赋予 sudo 权限，替换`你的用户名`为自定义名称：

```bash
# 创建普通用户
adduser 你的用户名
# 赋予sudo管理员权限
usermod -aG sudo 你的用户名
```

按照场景一的步骤 5，设置`/etc/wsl.conf`指定默认用户，重启 WSL 即可正常使用。

#### 关键补充 & 常见问

**路径规范**：每个发行版必须单独指定一个**空文件夹**，禁止多个发行版共用同一路径；路径不要用中文、空格、特殊字符，避免启动失败。

**默认发行版设置**：迁移 / 安装后，如需设为默认启动的发行版，执行：

```powershell
wsl --set-default Ubuntu
```

**虚拟磁盘优化**：导入后的 vhdx 文件是动态扩容的，占用空间随使用增长，如需压缩回收空闲空间，执行`wsl --shutdown`后，用`diskpart`工具压缩 vhdx 文件即可。

**启动失败排查**：检查是否加了`--version 2`、BIOS 虚拟化是否开启、路径是否合规、是否用管理员权限执行命令。

### wsl网络管理


#### 一

```bash
# 第一步：获取WSL Ubuntu的IP地址（纯PowerShell语法，无需awk）
$wslIp = (wsl -d Ubuntu -e hostname -I).Trim().Split(' ')[0]
# 第二步：配置端口转发
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=18080 connectaddress=$wslIp connectport=8080
```
###### 验证规则是否生效：
```powershell
netsh interface portproxy show all
```
输出中若能看到 `18080` 对应 `WSL的IP:8080`，说明配置成功。
如果不想用 PowerShell，可手动获取 WSL IP 后填到命令里：
防火墙放行
```
# 新建入站规则，允许 18080 端口 TCP 连接
New-NetFirewallRule -DisplayName "WSL-CodeServer-18080" -Direction Inbound -Protocol TCP -LocalPort 18080 -Action Allow -Profile Private,Public
```
#### 二
##### 步骤 1：查 WSL Ubuntu 的 IP
在 WSL Ubuntu 终端执行：
```bash
hostname -I  # 输出类似 172.17.0.2 （取第一个IP即可）
```
###### 步骤 2：在 Windows 管理员 CMD 中执行转发（替换成你的 WSL IP）
```cmd
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=18080 connectaddress=172.17.0.2 connectport=8080
```
##### 补充：若需删除转发规则（可选）
```powershell
netsh interface portproxy delete v4tov4 listenaddress=0.0.0.0 listenport=18080
```
##### 最终验证访问
1. 确保 WSL 里的 code-server 已启动（绑定`0.0.0.0:8080`）；
2. 在浏览器访问 `http://你的Windows内网IP:18080`（如`http://192.168.1.100:18080`）；
3. 输入 code-server 的密码即可正常访问。

### 防火墙

现在wsl可以使用镜像模式，不需要端口转发，所以只用在windows放行相应端口就可以了

### 1. 核心：单端口放行（你的 18080 端口）

直接复制执行，一键创建全场景生效的放行规则：

```
New-NetFirewallRule -DisplayName "WSL pydoc3 18080" -Direction Inbound -LocalPort 18080 -Protocol TCP -Action Allow -Profile Any -InterfaceType Any
```

参数解释（按需修改）：

- `-DisplayName`：规则名称，必须唯一，方便后续管理
- `-Direction Inbound`：入站规则（外部访问本机），出站规则改为`Outbound`
- `-LocalPort`：要放行的本地端口
- `-Protocol`：协议，TCP/UDP，按需修改
- `-Action Allow`：允许连接
- `-Profile Any`：适配所有网络场景（专用 / 公网 / 域）
- `-InterfaceType Any`：适配所有网卡（有线 / 无线 / 虚拟网卡）

### 2. 批量端口放行

一次性放行多个常用端口，适合 WSL 开发场景：

```
# 放行Web、开发、数据库常用端口
New-NetFirewallRule -DisplayName "WSL 开发常用端口合集" -Direction Inbound -LocalPort 80,443,3306,5000,5432,8080,18080 -Protocol TCP -Action Allow -Profile Any
```

### 3. 常用管理命令

```
# 查看已创建的规则（按名称筛选）
Get-NetFirewallRule -DisplayName "*WSL*"

# 删除指定规则（替换为你的规则名称）
Remove-NetFirewallRule -DisplayName "WSL pydoc3 18080"

# 临时关闭所有防火墙（仅测试排查用，不要长期开启）
Set-NetFirewallProfile -All -Enabled False

# 重新开启所有防火墙
Set-NetFirewallProfile -All -Enabled True
```

### 4. CMD 兼容命令（netsh 方式，适配老系统）

管理员 CMD 执行：

```
:: 放行18080 TCP端口
netsh advfirewall firewall add rule name="WSL pydoc3 18080" dir=in action=allow protocol=TCP localport=18080 profile=any

:: 查看所有放行规则
netsh advfirewall firewall show rule name=all

:: 删除指定规则
netsh advfirewall firewall delete rule name="WSL pydoc3 18080"
```

------

## 三、WSL 场景专属注意事项（避坑核心）

1. **镜像模式（你当前使用的模式）**：**绝对不需要`netsh interface portproxy`端口转发**，只需要 2 个条件即可访问：
   - WSL 内服务监听`0.0.0.0`（而非 127.0.0.1）
   - Windows 防火墙放行对应端口
   - 额外添加端口转发会导致端口冲突、访问失败，必须先清空所有多余的转发规则。
2. **NAT 模式（WSL 默认模式）**：才需要「端口转发 + 防火墙放行」两步操作，镜像模式无需此步骤。
3. **第三方安全软件拦截**：360 安全卫士、腾讯电脑管家、卡巴斯基等杀毒软件，会覆盖 Windows Defender 防火墙，必须在对应软件的「防火墙 / 网络防护」中单独放行端口，或临时关闭第三方防火墙测试。
4. **VPN / 代理干扰**：Clash、Shadowsocks、公司 VPN 等会篡改路由表，导致局域网 IP 访问异常，测试时请先关闭所有 VPN / 代理软件。

------

## 四、验证规则是否生效

### 1. 本机验证

Windows CMD/PowerShell 执行，测试端口是否通：

```
# 方法1：curl测试（推荐，Windows 10/11原生支持）
curl http://127.0.0.1:18080
curl http://192.168.1.3:18080

# 方法2：telnet测试
telnet 192.168.1.3 18080
```

返回 HTML 内容 / 黑屏无报错，说明端口放行成功；提示超时 / 拒绝连接，说明规则未生效。

### 2. 局域网验证

用同一 WiFi / 局域网下的手机、另一台电脑，打开浏览器访问`http://192.168.1.3:18080`，能正常打开页面，说明全链路配置成功。