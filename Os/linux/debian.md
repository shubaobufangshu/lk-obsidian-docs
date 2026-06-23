# debian-lubancat

#### 检查并安装 NetworkManager

```bash
# 检查是否已安装
systemctl status NetworkManager
# 若未安装，执行以下命令安装
sudo apt update
sudo apt install network-manager
sudo systemctl enable --now NetworkManager
```

#### 扫描可用无线网络

```bash
sudo nmcli device wifi list
```

#### 连接到指定无线网络

```bash
sudo nmcli device wifi connect "SSID名称" password "密码" ifname wlan0
```

#### 验证连接状态

```bash
nmcli device status
# 或
ip addr show wlan0  # 替换为实际无线接口名称
```

#### 断开连接状态

```bash
# 断开wifi
sudo nmcli dev dis wlan0
# 删除wifi信息
sudo nmcli c delete uuid
```

### 传统配置文件方式（适用于无图形界面或自定义需求）

#### 查看无线接口名称

```bash
iwconfig  # 查找类似 wlan0 的接口名称
```

#### 2. 配置网络接口文件

编辑 `/etc/network/interfaces`：
bash

```bash
sudo nano /etc/network/interfaces
```

添加以下内容（动态获取 IP）：
ini

```ini
auto wlan0
iface wlan0 inet dhcp
wpa-ssid "SSID名称"
wpa-psk "密码"
```

若需静态 IP，修改为：
ini

```ini
auto wlan0
iface wlan0 inet static
address 192.168.1.100
netmask 255.255.255.0
gateway 192.168.1.1
wpa-ssid "SSID名称"
wpa-psk "密码"
dns-nameservers 8.8.8.8 8.8.4.4
```

#### 3. 启动无线连接

```bash
sudo ifup wlan0
```

## vnc

### 安装vnc服务

```bash
sudo apt install x11vnc
```

### 创建链接密码

```bash
x11vnc -storepasswd
```
