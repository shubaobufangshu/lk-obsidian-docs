# Nginx 全流程下载使用详细教程（2026 最新稳定版）

本教程覆盖**Windows/Linux/macOS 全平台**，从下载安装、基础操作、配置详解到实战场景、问题排查，新手可直接照着一步步操作，生产环境也可直接参考规范配置。

## 一、前置说明：版本选择与官方渠道

### 1. 核心版本区分

表格







| 版本类型                   | 适用场景                         | 推荐指数 |
| -------------------------- | -------------------------------- | -------- |
| Stable version（稳定版）   | 生产环境、新手入门、线上业务     | ⭐⭐⭐⭐⭐    |
| Mainline version（主线版） | 测试环境、尝鲜最新功能、二次开发 | ⭐⭐⭐      |
| Legacy versions（历史版）  | 仅老项目兼容适配，不推荐新项目   | ⭐        |

### 2. 官方地址

- 官网首页：https://nginx.org/
- 官方下载页：https://nginx.org/en/download.html
- 官方文档：https://nginx.org/en/docs/

## 二、分平台全流程安装教程

### （一）Windows 系统（本地测试 / 开发环境）

#### 1. 下载安装包

1. 打开官方下载页，找到 **Stable version** 稳定版，下载 `nginx/Windows-xxx.zip` 压缩包
2. 避坑提醒：**不要下载到含中文、空格的路径**，不要放在 C 盘 Program Files 目录（避免权限问题），推荐解压到 `D:\nginx\` 根目录

#### 2. 基础启动与验证

1. 启动方式（推荐第二种，可查看报错）

   - 方式 1：直接双击解压目录下的 `nginx.exe`（黑框一闪而过为正常，后台静默运行）

   - 方式 2：按下

     ```
     Win+R
     ```

     输入

     ```
     cmd
     ```

     打开命令提示符，执行以下命令进入目录并启动

     cmd

     

     

     

     

     

     ```
     # 进入nginx根目录（替换为你的解压路径）
     D:
     cd D:\nginx
     # 启动nginx
     start nginx
     ```

     

   

2. 验证是否启动成功

   

   打开浏览器，访问 

   ```
   http://localhost
   ```

    或 

   ```
   http://127.0.0.1
   ```

   ，页面出现 

   Welcome to nginx!

    即为安装启动成功。

#### 3. Windows 核心常用命令

必须在 nginx 根目录的 cmd 窗口执行，配置环境变量后可全局执行

cmd











```
# 1. 检查配置文件语法是否正确（修改配置后必执行）
nginx -t

# 2. 平滑重载配置（修改配置后不重启服务生效，生产环境推荐）
nginx -s reload

# 3. 优雅停止服务（处理完所有请求后再关闭）
nginx -s quit

# 4. 强制快速停止服务
nginx -s stop

# 5. 查看nginx版本
nginx -v

# 6. 查看nginx版本+编译模块信息
nginx -V
```

#### 4. 进阶配置（可选）

1. 配置全局环境变量

   

   - 右键「此电脑」→「属性」→「高级系统设置」→「环境变量」
   - 找到系统变量中的`Path`，点击编辑，新增你的 nginx 根目录路径（如`D:\nginx`）
   - 一路点击确定，重启 cmd 窗口，即可在任意目录执行 nginx 命令

   

2. 配置 Windows 开机自启

   推荐使用`nssm`工具将 nginx 注册为系统服务，避免每次开机手动启动：

   

   1. 下载 nssm（https://nssm.cc/download），解压后将对应系统位数的`nssm.exe`放到 nginx 根目录
   2. 以管理员身份打开 cmd，进入 nginx 目录，执行`nssm install nginx`
   3. 在弹出的窗口中，Path 选择 nginx.exe 路径，点击 Install 安装
   4. 执行`services.msc`打开服务面板，找到 nginx 服务，设置启动类型为「自动」，即可开机自启

   

------

### （二）Linux 系统（生产环境首选）

Linux 系统提供两种安装方式：**包管理器安装（新手 / 生产环境首选）**、源码编译安装（自定义模块 / 高级用户使用）

#### 方式 1：包管理器安装（推荐）

优势：自动处理依赖、一键安装更新、自带 systemd 服务、配置规范、维护成本低

##### ① CentOS/RHEL 系列（7/8/9，含 Rocky Linux、AlmaLinux）

1. 前置准备：安装基础工具，配置官方 yum 源（系统默认源版本过旧，优先用官方稳定版源）

   bash

   

   运行

   

   

   

   

   ```
   # 1. 安装基础工具
   sudo yum install -y yum-utils
   # 或CentOS8+用dnf
   sudo dnf install -y yum-utils
   
   # 2. 创建官方yum源配置文件
   sudo tee /etc/yum.repos.d/nginx.repo <<-'EOF'
   [nginx-stable]
   name=nginx stable repo
   baseurl=https://nginx.org/packages/centos/$releasever/$basearch/
   gpgcheck=1
   enabled=1
   gpgkey=https://nginx.org/keys/nginx_signing.key
   module_hotfixes=true
   
   [nginx-mainline]
   name=nginx mainline repo
   baseurl=https://nginx.org/packages/mainline/centos/$releasever/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=https://nginx.org/keys/nginx_signing.key
   module_hotfixes=true
   EOF
   ```

   

2. 安装 Nginx

   bash

   

   运行

   

   

   

   

   ```
   sudo yum install -y nginx
   # 或CentOS8+用dnf
   sudo dnf install -y nginx
   ```

   

3. 启动服务 + 配置开机自启

   bash

   

   运行

   

   

   

   

   ```
   # 启动nginx
   sudo systemctl start nginx
   # 设置开机自启
   sudo systemctl enable nginx
   # 查看运行状态
   sudo systemctl status nginx
   ```

   

4. 防火墙放行端口（必做，否则外网无法访问）

   bash

   

   运行

   

   

   

   

   ```
   # 放行80（HTTP）、443（HTTPS）端口
   sudo firewall-cmd --permanent --add-port=80/tcp
   sudo firewall-cmd --permanent --add-port=443/tcp
   # 重载防火墙规则
   sudo firewall-cmd --reload
   ```

   

5. 验证安装

   

   浏览器访问服务器

   公网 IP 地址

   ，出现

   ```
   Welcome to nginx!
   ```

   即为成功。

##### ② Ubuntu/Debian 系列

1. 前置准备：安装依赖，配置官方 APT 源，确保安装最新稳定版

   bash

   

   运行

   

   

   

   

   ```
   # 1. 更新软件包索引，安装基础依赖
   sudo apt update && sudo apt upgrade -y
   sudo apt install -y curl gnupg2 ca-certificates lsb-release ubuntu-keyring
   
   # 2. 导入官方GPG签名密钥
   curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg > /dev/null
   
   # 3. 添加官方稳定版源
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] http://nginx.org/packages/ubuntu $(lsb_release -cs) nginx" | sudo tee /etc/apt/sources.list.d/nginx.list
   
   # 4. 设置源优先级，避免系统旧版覆盖官方源
   echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900" | sudo tee /etc/apt/preferences.d/99nginx
   ```

   

2. 安装 Nginx

   bash

   

   运行

   

   

   

   

   ```
   sudo apt update
   sudo apt install -y nginx
   ```

   

3. 启动服务 + 配置开机自启

   bash

   

   运行

   

   

   

   

   ```
   sudo systemctl start nginx
   sudo systemctl enable nginx
   sudo systemctl status nginx
   ```

   

4. 防火墙放行端口

   bash

   

   运行

   

   

   

   

   ```
   # 方式1：直接放行nginx全量规则（推荐）
   sudo ufw allow 'Nginx Full'
   # 方式2：单独放行端口
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   # 重载防火墙
   sudo ufw reload
   ```

   

5. 验证安装：浏览器访问服务器公网 IP，出现欢迎页即为成功。

#### 方式 2：源码编译安装（高级用户 / 自定义模块使用）

优势：可自由选择版本、自定义编译模块、指定安装路径，灵活性极高

1. 安装编译依赖

   bash

   

   运行

   

   

   

   

   ```
   # CentOS/RHEL系列
   sudo yum install -y gcc gcc-c++ make pcre pcre-devel zlib zlib-devel openssl openssl-devel
   
   # Ubuntu/Debian系列
   sudo apt install -y gcc g++ make libpcre3 libpcre3-dev zlib1g zlib1g-dev libssl-dev
   ```

   

2. 下载最新稳定版源码包（可替换为官网最新版本号）

   bash

   

   运行

   

   

   

   

   ```
   # 进入/usr/local/src目录
   cd /usr/local/src
   # 下载源码包（官网最新稳定版1.26.0，可自行替换）
   sudo wget https://nginx.org/download/nginx-1.26.0.tar.gz
   # 解压
   sudo tar -zxvf nginx-1.26.0.tar.gz
   # 进入解压目录
   cd nginx-1.26.0
   ```

   

3. 配置编译参数（核心步骤，可自定义模块）

   bash

   

   运行

   

   

   

   

   ```
   # 基础配置（入门推荐）
   sudo ./configure \
   --prefix=/usr/local/nginx \
   --user=nginx \
   --group=nginx \
   --with-http_ssl_module \
   --with-http_gzip_static_module \
   --with-http_stub_status_module
   ```

   

   核心参数说明：

   - `--prefix`：指定 nginx 安装根目录
   - `--user/--group`：指定运行 nginx 的用户和用户组
   - `--with-http_ssl_module`：开启 HTTPS SSL 模块（必开）
   - 执行后无报错，即可进入编译步骤

   

4. 编译与安装

   bash

   

   运行

   

   

   

   

   ```
   # 编译（根据服务器配置，耗时1-5分钟）
   sudo make
   # 安装
   sudo make install
   ```

   

5. 创建运行用户，配置环境变量

   bash

   

   运行

   

   

   

   

   ```
   # 创建nginx运行用户（不允许登录）
   sudo useradd -s /sbin/nologin nginx
   # 配置环境变量
   echo "export PATH=$PATH:/usr/local/nginx/sbin" | sudo tee /etc/profile.d/nginx.sh
   # 生效环境变量
   source /etc/profile
   ```

   

6. 配置 systemd 服务（实现开机自启、系统管理）

   bash

   

   运行

   

   

   

   

   ```
   # 创建服务文件
   sudo tee /usr/lib/systemd/system/nginx.service <<-'EOF'
   [Unit]
   Description=nginx - high performance web server
   Documentation=https://nginx.org/en/docs/
   After=network-online.target remote-fs.target nss-lookup.target
   Wants=network-online.target
   
   [Service]
   Type=forking
   PIDFile=/usr/local/nginx/logs/nginx.pid
   ExecStart=/usr/local/nginx/sbin/nginx
   ExecReload=/usr/local/nginx/sbin/nginx -s reload
   ExecStop=/usr/local/nginx/sbin/nginx -s quit
   PrivateTmp=true
   
   [Install]
   WantedBy=multi-user.target
   EOF
   ```

   

7. 启动服务 + 开机自启

   bash

   

   运行

   

   

   

   

   ```
   # 重载systemd配置
   sudo systemctl daemon-reload
   # 启动nginx
   sudo systemctl start nginx
   # 设置开机自启
   sudo systemctl enable nginx
   # 查看状态
   sudo systemctl status nginx
   ```

   

8. 防火墙放行端口（同包管理器安装步骤），浏览器访问公网 IP 验证即可。

------

### （三）macOS 系统

1. 前提：已安装 Homebrew（未安装可执行 `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`）

2. 安装 Nginx

   bash

   

   运行

   

   

   

   

   ```
   brew install nginx
   ```

   

3. 启动服务 + 开机自启

   bash

   

   运行

   

   

   

   

   ```
   # 启动并设置开机自启
   brew services start nginx
   # 仅临时启动
   nginx
   ```

   

4. 验证安装：浏览器访问 `http://localhost:8080`，出现欢迎页即为成功

5. 核心命令同 Linux 系统，配置文件默认路径：`/usr/local/etc/nginx/nginx.conf`

------

## 三、Nginx 核心运维命令汇总

表格







| 功能                | 命令                                         | 说明                                       |
| ------------------- | -------------------------------------------- | ------------------------------------------ |
| 检查配置文件语法    | `nginx -t`                                   | 修改配置后必执行，提前发现语法错误         |
| 查看版本号          | `nginx -v`                                   | 仅显示版本号                               |
| 查看版本 + 编译模块 | `nginx -V`                                   | 查看编译时安装的模块                       |
| 启动服务            | `systemctl start nginx`                      | Linux 系统（包管理器安装）                 |
| 停止服务（优雅）    | `nginx -s quit` / `systemctl stop nginx`     | 处理完所有请求后关闭，生产环境推荐         |
| 停止服务（强制）    | `nginx -s stop`                              | 立即终止进程，紧急情况使用                 |
| 平滑重载配置        | `nginx -s reload` / `systemctl reload nginx` | 不重启服务生效新配置，不中断业务，核心命令 |
| 查看服务状态        | `systemctl status nginx`                     | Linux 系统查看运行状态、日志、报错         |
| 重启服务            | `systemctl restart nginx`                    | 重启服务，会中断业务，非必要不使用         |

------

## 四、Nginx 核心配置文件详解

### 1. 配置文件默认路径

表格







| 安装方式       | 主配置文件路径                     | 子配置文件目录                  | 网站默认根目录          |
| -------------- | ---------------------------------- | ------------------------------- | ----------------------- |
| Linux 包管理器 | `/etc/nginx/nginx.conf`            | `/etc/nginx/conf.d/`            | `/usr/share/nginx/html` |
| Linux 源码编译 | `/usr/local/nginx/conf/nginx.conf` | `/usr/local/nginx/conf/conf.d/` | `/usr/local/nginx/html` |
| Windows        | `解压目录/conf/nginx.conf`         | `解压目录/conf/conf.d/`         | `解压目录/html`         |

> 最佳实践：每个网站单独创建一个`.conf`配置文件，放在`conf.d`目录下，主配置文件通过`include`引入，避免单文件过大、管理混乱。

### 2. 配置文件核心层级结构

Nginx 配置采用**区块化分层结构**，核心层级为：`全局块 → events块 → http块 → server块 → location块`

nginx











```
# 1. 全局块：影响Nginx全局的配置
user nginx;                 # 运行Nginx的用户
worker_processes auto;      # 工作进程数，auto自动匹配CPU核心数（最优配置）
error_log /var/log/nginx/error.log warn; # 错误日志路径+级别
pid /var/run/nginx.pid;     # 进程PID文件路径

# 2. events块：网络连接相关配置，影响Nginx与用户的连接性能
events {
    use epoll;               # Linux使用epoll事件驱动模型（高性能核心）
    worker_connections 10240; # 每个worker进程的最大连接数，生产环境调大到10240+
    multi_accept on;         # 允许一次接收多个连接
}

# 3. http块：HTTP协议全局配置，可包含多个server块
http {
    include       /etc/nginx/mime.types; # 引入文件类型映射表
    default_type  application/octet-stream; # 默认文件类型

    # 日志格式定义
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main; # 访问日志路径

    # 核心性能优化
    sendfile on;                # 开启高效文件传输模式，静态资源必开
    tcp_nopush on;              # 减少网络包数量，提升传输效率
    tcp_nodelay on;             # 降低小数据包延迟
    keepalive_timeout 65;       # 长连接保持超时时间

    # Gzip压缩配置
    gzip on;                    # 开启gzip压缩，减小文件体积，提升加载速度
    gzip_vary on;
    gzip_min_length 1024;      # 仅压缩大于1KB的文件
    gzip_types text/plain text/css application/json application/javascript text/xml;

    # 引入子配置文件（conf.d目录下所有.conf文件）
    include /etc/nginx/conf.d/*.conf;
}
```

### 3. 核心子配置块详解

#### ① server 块（虚拟主机）

一个`server`块对应一个网站 / 服务，核心配置如下：

nginx











```
server {
    listen       80;                 # 监听的端口，HTTP默认80，HTTPS默认443
    server_name  example.com www.example.com; # 你的域名/服务器IP
    root   /usr/share/nginx/html;    # 网站根目录
    index  index.html index.htm;     # 默认首页文件
    error_page  404 /404.html;       # 404错误页
    error_page  500 502 503 504 /50x.html; # 5xx错误页

    # location块：匹配URL路径，处理具体请求
    location / {
        # 根路径请求处理
    }
}
```

#### ② location 块（URL 匹配）

location 是 Nginx 最核心的功能，用于匹配请求的 URI，执行不同的处理逻辑，核心匹配规则：

表格







| 匹配符 | 格式                 | 匹配规则                         | 优先级                 |      |
| ------ | -------------------- | -------------------------------- | ---------------------- | :--: |
| `=`    | `location = /test`   | 精确匹配，路径完全一致才生效     | 最高                   |      |
| `^~`   | `location ^~ /test/` | 前缀匹配，匹配成功后不再正则匹配 | 次高                   |      |
| `~`    | `location ~ .(jpg    | png)$`                           | 正则匹配，区分大小写   |  中  |
| `~*`   | `location ~* .(jpg   | png)$`                           | 正则匹配，不区分大小写 |  中  |
| 无符号 | `location /test`     | 普通前缀匹配                     | 最低                   |      |

------

## 五、实战常用场景配置（可直接复制使用）

### 场景 1：静态网站部署

创建`/etc/nginx/conf.d/website.conf`，写入以下配置：

nginx











```
server {
    listen       80;
    server_name  your-domain.com www.your-domain.com; # 替换为你的域名/IP
    root   /var/www/website; # 替换为你的网站文件存放目录
    index  index.html index.htm;

    # 开启防盗链（可选）
    valid_referers none blocked server_names *.your-domain.com;
    if ($invalid_referer) {
        return 403;
    }

    # 静态资源缓存
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 30d; # 缓存30天
        add_header Cache-Control "public, no-transform";
    }

    location / {
        try_files $uri $uri/ /index.html; # 适配SPA单页应用（Vue/React）
    }

    # 错误页配置
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
}
```

配置完成后，执行`nginx -t`检查语法，无误后执行`nginx -s reload`重载生效。

### 场景 2：反向代理（前后端分离 / Java/Node 项目）

将域名请求转发到后端服务，示例：将`api.your-domain.com`的请求转发到本地 8080 端口的后端服务

nginx











```
server {
    listen       80;
    server_name  api.your-domain.com; # 替换为你的API域名

    location / {
        # 后端服务地址，替换为你的服务IP+端口
        proxy_pass http://127.0.0.1:8080;
        # 核心请求头配置，传递真实客户端信息
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        # 超时配置
        proxy_connect_timeout 30s;
        proxy_read_timeout 60s;
        proxy_send_timeout 60s;
    }
}
```

### 场景 3：PHP 站点部署（Nginx + PHP-FPM）

nginx











```
server {
    listen       80;
    server_name  php.your-domain.com;
    root   /var/www/php;
    index  index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # 处理PHP请求
    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000; # PHP-FPM监听地址，也可用unix socket
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

### 场景 4：负载均衡

将流量分发到多台后端服务器，提升可用性和性能

nginx











```
# 1. 定义上游服务器组
upstream web_servers {
    # 轮询策略（默认）：按顺序依次分发
    server 192.168.1.101:8080 weight=2; # weight权重，数值越大，分发的请求越多
    server 192.168.1.102:8080 weight=1;
    server 192.168.1.103:8080 backup; # backup备用服务器，其他节点宕机时启用
    # ip_hash; # 可选：同一客户端IP固定访问同一台服务器，解决session问题
}

# 2. 虚拟主机配置
server {
    listen       80;
    server_name  load.your-domain.com;

    location / {
        proxy_pass http://web_servers; # 转发到上游服务器组
        # 代理头配置同反向代理
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

------

## 六、常见问题排查

### 1. 启动失败：端口 80 被占用

- 报错提示：`bind() to 0.0.0.0:80 failed (98: Address already in use)`

- 解决方案：

  1. 查看端口占用：`netstat -tulpn | grep 80` 或 `ss -tulpn | grep 80`
  2. 终止占用进程：`kill -9 进程PID`
  3. 或修改 nginx 监听端口，将`listen 80`改为其他未占用端口

  

### 2. 浏览器访问 403 Forbidden

- 常见原因：

  1. 网站根目录文件权限不足：执行`chown -R nginx:nginx 网站目录`、`chmod -R 755 网站目录`
  2. SELinux 未关闭：临时关闭`setenforce 0`，永久关闭修改`/etc/selinux/config`
  3. index 首页文件不存在，或 root 路径配置错误
  4. 开启了防盗链，referer 不在白名单内

  

### 3. 浏览器访问 404 Not Found

- 常见原因：

  1. `root`/`alias`路径配置错误，文件实际不存在
  2. location 匹配规则错误，导致请求未找到对应文件
  3. SPA 单页应用未配置`try_files $uri $uri/ /index.html;`

  

### 4. 502 Bad Gateway

- 常见原因：

  1. 反向代理的后端服务未启动、端口错误、服务宕机
  2. PHP-FPM 服务未运行，`fastcgi_pass`地址配置错误
  3. 后端服务端口被防火墙拦截，无法访问

  

### 5. 504 Gateway Timeout

- 常见原因：后端服务响应超时，接口处理时间过长
- 解决方案：调大`proxy_read_timeout`、`fastcgi_read_timeout`超时时间，同时优化后端服务性能

### 6. 配置修改后不生效

- 解决方案：

  1. 先执行`nginx -t`检查配置文件是否有语法错误
  2. 执行`nginx -s reload`平滑重载配置，不要用 restart
  3. 确认修改的配置文件是否被主配置文件`include`引入
  4. 清理浏览器缓存，或用无痕模式测试

  

------

## 七、生产环境安全与优化建议

1. **隐藏 Nginx 版本号**：在 http 块中添加`server_tokens off;`，避免泄露版本信息被针对性攻击
2. **配置 HTTPS**：使用 Let's Encrypt 免费 SSL 证书，强制 HTTP 跳转 HTTPS，开启 TLS1.2 + 协议
3. **权限最小化**：使用普通用户运行 worker 进程，禁止 root 用户直接运行，网站目录权限严格控制
4. **限制请求速率**：配置`limit_req_zone`限制单 IP 请求速率，防 CC 攻击
5. **访问控制**：通过`allow`/`deny`配置 IP 黑白名单，限制后台管理页面仅内网 IP 访问
6. **定期更新**：及时更新 Nginx 稳定版，修复安全漏洞
7. **日志管理**：配置日志切割，避免日志文件过大占满磁盘，定期分析访问日志和错误日志