# Ventoy 制作 Kali Linux To Go 详细教程（基于 vtoyboot 插件）

本教程完全适配的工具：**Kali Linux Everything 镜像**、**VirtualBox 7.2.8**、**vtoyboot 1.0.36**，严格遵循官方文档要求，确保同时支持 Legacy BIOS 和 UEFI 双模式启动。

## 一、前置准备与核心注意事项

### 必备资源确认

- 已下载：`kali-linux-everything-xxx-amd64.iso`（建议 2024.x 及以上版本）
- 已安装：VirtualBox 7.2.8（Windows 版）
- 已下载：`vtoyboot-1.0.36.tar.gz`（从 [GitHub Releases](https://github.com/ventoy/vtoyboot/releases) 获取）
- 硬件：容量≥**80GB** 的 Ventoy U 盘（Kali Everything 安装后约占 45GB，预留 35GB 后续使用空间）

### 绝对不能违反的核心规则

1. 虚拟硬盘**必须是固定大小**，不支持动态扩展
2. 安装系统时 VirtualBox**必须启用 EFI 模式**（否则无法双模式启动）
3. 安装完成后**不能创建虚拟机快照**，否则 vDisk 无法识别
4. 最终文件**必须添加 `.vtoy` 后缀**才能被 Ventoy 识别
5. Kali Linux 必须额外安装 `grub-pc-bin` 包（官方明确要求）

## 二、步骤 1：创建固定大小的虚拟硬盘（VirtualBox 7.2.8）

1. 打开 VirtualBox，点击**新建**
2. 虚拟机名称：`Kali-ToGo`（任意），类型：`Linux`，版本：``Debian (64-bit)`或`Oracle Linux (64-bit)``，点击**下一步**
3. 内存大小：建议分配**4096MB（4GB）以上**，处理器：2核以上，点击**下一步**
4. 虚拟硬盘：选择**现在创建虚拟硬盘**，点击**创建**
5. 虚拟硬盘文件类型：选择 **VHD（虚拟硬盘）** 或 **VDI（VirtualBox 磁盘映像）**（两者均可，推荐 VHD 兼容性更好）
6. 存储在物理硬盘上：**必须选择「固定大小(F)」**预先分配全部空间(Fixed size)**”，点击**下一步**
7. **关键！启用EFI并挂载ISO**：
   - 选中你创建好的虚拟机，点击“设置(Settings)”。
   - 进入“系统(System)” -> “主板(Motherboard)”选项卡，**勾选“启用EFI (只针对某些操作系统) (Enable EFI)”**。这是实现UEFI启动的关键。
   - 进入“存储(Storage)”选项卡，在“控制器：IDE”下点击“没有盘片(Empty)”，然后在右侧属性面板点击光盘图标，选择“选择一个虚拟光盘文件...”，加载你准备好的`kali-linux-everything.iso`镜像。
8. 文件位置和大小：
   - 位置：选择剩余空间充足的本地磁盘（如 `D:\VM\Kali-ToGo.vhd`）
   - 大小：**至少 80GB**（Kali Everything 完整安装约 45GB）
9. 点击**创建**，等待虚拟硬盘生成（固定大小创建时间较长，80GB 约 5-10 分钟）

## 三、步骤 2：配置 VirtualBox 为 UEFI 启动模式（关键！）

> ⚠️ 官方强调三次：必须启用 EFI，否则生成的 vDisk 只能在 UEFI 模式下启动，无法兼容 Legacy BIOS

1. 选中刚创建的 `Kali-ToGo` 虚拟机，点击**设置**
2. 左侧选择**系统**→**主板**：
   - 勾选 **启用 EFI（只针对某些操作系统）**
   - 启动顺序：将「光驱」拖动到「硬盘」上方
   - 芯片组保持默认 `PIIX3` 即可
3. 左侧选择**存储**：
   - 点击「控制器:SATA」右侧的 **+ 图标**→**添加光驱**
   - 选择你下载的 `kali-linux-everything-xxx-amd64.iso`，点击**选择**
4. 其他设置保持默认，点击**确定**保存

## 四、步骤 3：安装 Kali Linux 到虚拟硬盘

1. 选中 `Kali-ToGo` 虚拟机，点击**启动**
2. 进入 Kali 安装界面，选择 **Graphical Install**（图形化安装）
3. 按照向导完成语言、地区、键盘布局设置
4. 主机名、域名：保持默认或自定义即可
5. 设置 root 密码和普通用户账户（建议记住密码，后续需要）
6. 磁盘分区（最关键步骤）：
   - 选择 **使用整个磁盘**（不要手动分区，避免兼容性问题）
   - 选择唯一的虚拟硬盘（`/dev/sda`）
   - 分区方案：选择 **将所有文件放在同一个分区中**
   - 确认分区设置，选择 **结束分区设定并将修改写入磁盘**
   - 勾选 **是**，确认写入磁盘
7. 软件选择：保持默认勾选（Everything 镜像已包含所有工具），点击**继续**
8. 安装 GRUB 引导器：
   - 选择 **是**，将 GRUB 安装到主引导记录
   - 选择 `/dev/sda`（虚拟硬盘）作为安装位置
9. 等待安装完成（Everything 镜像安装时间较长，约 30-60 分钟）
10. 安装完成后，点击**继续**，虚拟机会自动重启

## 五、步骤 4：安装 grub-pc-bin 包（Kali 专属要求）

> ⚠️ 官方文档明确标注：Kali Linux 需要安装此包才能支持 Legacy BIOS 模式启动。如果只需要 UEFI 启动，可跳过此步骤。

1. 重启后进入 Kali 系统，登录你创建的用户账户

2. 挂载 Kali 安装 ISO（虚拟机中自动挂载，如未挂载，点击 VirtualBox 顶部「设备」→「分配光驱」→ 选择 Kali ISO）

3. 打开终端，执行以下命令安装 `grub-pc-bin`：
   
   ```bash
   # 切换到 ISO 的 grub2 包目录
   cd /media/$USER/Kali-Linux-*/pool/main/g/grub2/
   # cd /media/cdrom/pool/main/g/grub2/
   # 安装 grub-pc-bin 包（文件名可能略有不同，按 Tab 补全）
   sudo dpkg -i grub-pc-bin_*.deb
   ```
   
   *(注：`grub-pc-bin`包名可能会变，可用`ls /media/cdrom/pool/main/g/grub2/`查看准确文件名)*。

4. 等待安装完成，无报错即可

## 六、步骤 5：执行 vtoyboot 脚本（核心适配步骤）

1. **执行vtoyboot脚本**：
   
   - **挂载vtoyboot镜像**：在VirtualBox菜单栏，点击“设备”->“分配光驱”，弹出Kali ISO，然后加载你下载好的`vtoyboot-1.0.36.iso`。
   
   - **运行脚本**：在Kali虚拟机中打开终端，执行以下命令：
     
     ```bash
     # 复制文件到桌面
     cp -r /media/cdrom/vtoyboot-1.0.36 ~/Desktop/
     cd ~/Desktop/vtoyboot-1.0.36
     # 执行脚本
     sudo bash ./vtoyboot.sh
     ```
   
   - 看到提示“`vtoyboot has finished successfully`”即为成功。
   
   

2. 将你下载的 `vtoyboot-1.0.36.tar.gz` 传输到 Kali 系统中：
- 方法 1：使用 VirtualBox 共享文件夹
- 方法 2：将压缩包拷贝到 U 盘，插入虚拟机后挂载
1. 打开终端，解压并执行脚本：
   
   ```bash
   # 解压压缩包（假设文件在 ~/Downloads 目录）
   cd ~/Downloads
   tar -xzf vtoyboot-1.0.36.tar.gz
   
   # 进入解压后的目录
   cd vtoyboot-1.0.36
   
   # 以 root 权限执行脚本（必须）
   sudo bash vtoyboot.sh
   ```

2. 脚本执行过程中会自动检测系统并生成适配的 initramfs，等待完成（约 1-2 分钟）

3. 脚本执行完成后，**必须执行关机命令**，不能重启：
   
   ```bash
   sudo poweroff
   ```

## 七、步骤 6：部署到 Ventoy U 盘并启动

1. 关闭 VirtualBox，找到你创建的虚拟硬盘文件（如 `D:\VM\Kali-ToGo.vhd`）
2. 重命名文件，**添加 `.vtoy` 后缀**（推荐保留原后缀，格式为 `xxx.vhd.vtoy`）：
   - 示例：`Kali-Everything-2026.1.vhd.vtoy`
3. 将重命名后的 `.vtoy` 文件拷贝到 Ventoy U 盘的**任意目录**（确保路径唯一，不要在多个分区有同名文件）
4. 重启电脑，从 Ventoy U 盘启动
5. 在 Ventoy 启动菜单中，你会看到 `Kali-Everything-2026.1.vhd.vtoy` 选项，选择它即可启动 Kali Linux To Go

## 八、后续维护与常见问题

### 系统升级后处理

- 普通软件安装/升级：无任何限制，正常操作即可
- 内核升级、驱动更新或执行 `apt full-upgrade` 系统大升级后：
  1. 启动进入 Kali To Go 系统
  2. 重新执行一次 `sudo bash vtoyboot.sh`
  3. 执行 `sudo poweroff` 关机，确保下次正常启动

### 启动本地硬盘上的 vDisk（可选）

如果不想把大文件放在 U 盘，可以将 `.vtoy` 文件放在本地硬盘，通过 Ventoy 自定义菜单启动：

1. 在 Ventoy U 盘根目录创建 `ventoy` 文件夹，新建 `ventoy_grub.cfg` 文件
2. 添加以下内容（修改 `my_vdisk_path` 为实际路径）：
   
   ```grub
   menuentry "启动本地硬盘 Kali To Go" {
       set my_vdisk_path="/VHD/Kali-Everything-2026.1.vhd.vtoy"
       if search -n -s vdiskhd -f "$my_vdisk_path"; then
           vtoyboot_common_func "($vdiskhd)$my_vdisk_path"
       else
           echo "$my_vdisk_path not found"
       fi
   }
   ```
3. 保存后重启，Ventoy 菜单会显示该选项

### 常见问题排查

1. **Ventoy 看不到 .vtoy 文件**：确认后缀名正确（不是 `.vtoy.txt`），文件完整拷贝
2. **启动时报错找不到磁盘**：重新执行 vtoyboot 脚本，确保虚拟硬盘是固定大小且无快照
3. **Legacy BIOS 模式无法启动**：确认已安装 `grub-pc-bin` 包，且安装系统时启用了 EFI
4. **系统启动后黑屏**：增加 VirtualBox 安装时的内存分配（至少 4GB），检查显卡驱动

需要我帮你生成一份可直接复制的 `ventoy_grub.cfg` 本地硬盘启动配置，以及 vtoyboot 执行失败的排查清单吗？

