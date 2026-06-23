
## 一、基础准备（必看）

1. **手机开启开发者选项 + USB调试**
   设置 → 关于手机 → 连续点击版本号，开启开发者选项
   开发者选项 → 打开【USB调试】
2. **连接方式**
   - USB直连（最稳定）
   - WiFi连接（同局域网下）

---

## 二、设备连接与基础命令

### 1. 查看已连接设备

```bash
adb devices
```

- 显示 `device` 代表连接成功
- 显示 `offline` 代表连接失败

  ### 2. 重启ADB服务（解决连接异常）

  ```bash
  adb kill-server
  adb start-server
  ```

  ### 3. WiFi无线连接ADB

  ```bash
  # 1. 先USB连接，开启TCP端口
  adb tcpip 5555
  # 2. 查看手机IP，然后执行
  adb connect 手机IP:5555
  # 3. 断开WiFi连接
  adb disconnect 手机IP:5555
  ```

  ### 4. 查看ADB版本

  ```bash
  adb version
  ```

---

## 三、设备信息查询

```bash
# 查看手机系统版本
adb shell getprop ro.build.version.release
# 查看手机型号
adb shell getprop ro.product.model
# 查看手机品牌
adb shell getprop ro.product.brand
# 查看CPU架构
adb shell getprop ro.product.cpu.abi
# 查看设备序列号
adb get-serialno
# 查看电池信息
adb shell dumpsys battery
# 查看屏幕分辨率
adb shell wm size
# 查看屏幕密度
adb shell wm density
```

---

## 四、文件传输（电脑 ↔ 手机）

### 1. 电脑 → 手机（推送文件）

```bash
adb push 电脑文件路径 手机文件夹路径
```

示例：

```bash
adb push C:\test.apk /sdcard/
```

### 2. 手机 → 电脑（拉取文件）

```bash
adb pull 手机文件路径 电脑保存路径
```

示例：

```bash
adb pull /sdcard/test.apk C:\
```

---

## 五、应用管理（安装/卸载/查看）

### 1. 安装APK

```bash
adb install 电脑APK路径
```

- 覆盖安装（保留数据）

  ```bash
  adb install -r 路径
  ```

- 安装到SD卡

  ```bash
  adb install -s 路径
  ```

  ### 2. 卸载应用

  ```bash
  adb uninstall 包名
  ```

  保留数据卸载：

  ```bash
  adb uninstall -k 包名
  ```

  ### 3. 查看手机所有应用包名

  ```bash
  adb shell pm list packages
  ```

  过滤系统应用：

  ```bash
  adb shell pm list packages -s
  ```

  过滤第三方应用：

  ```bash
  adb shell pm list packages -3
  ```

  ### 4. 查看应用安装路径

  ```bash
  adb shell pm path 包名
  ```

  ### 5. 清除应用数据与缓存

  ```bash
  adb shell pm clear 包名
  ```

---

## 六、手机操控命令

### 1. 重启手机

```bash
adb reboot
```

### 2. 重启到Recovery模式

```bash
adb reboot recovery
```

### 3. 重启到Bootloader（线刷模式）

```bash
adb reboot bootloader
```

### 4. 截取屏幕（保存到电脑）

```bash
adb exec-out screencap -p > C:\screen.png
```

### 5. 录制屏幕（保存到手机）

```bash
adb shell screenrecord /sdcard/record.mp4
```

## 按 `Ctrl+C` 停止录制

## 七、Shell命令（进入手机系统）

### 1. 进入手机终端

```bash
adb shell
```

进入后可直接执行Linux命令：`ls` `cd` `rm` `mkdir` 等

### 2. 不进入Shell直接执行命令

```bash
adb shell 命令
```

示例：

```bash
adb shell ls /sdcard/
```

---

## 八、日志查看（调试必备）

### 1. 实时打印所有日志

```bash
adb logcat
```

### 2. 保存日志到电脑

```bash
adb logcat > C:\log.txt
```

### 3. 清空旧日志

```bash
adb logcat -c
```

### 4. 只查看指定应用日志

```bash
adb logcat --pid=$(adb shell pidof -s 包名)
```

---

## 九、实用高级命令

### 1. 模拟点击屏幕

```bash
adb shell input tap x坐标 y坐标
```

### 2. 模拟滑动

```bash
adb shell input swipe 起始x 起始y 结束x 结束y 时长(毫秒)
```

示例：上滑

```bash
adb shell input swipe 500 1000 500 500 200
```

### 3. 模拟输入文字

```bash
adb shell input text "需要输入的内容"
```

### 4. 模拟按键

```bash
adb shell input keyevent 键值
```

常用键值：

- 3 → HOME键
- 4 → 返回键
- 5 → 拨号
- 6 → 挂断
- 24 → 音量+
- 25 → 音量-
- 26 → 电源键
- 82 → 菜单键

  ### 5. 启动应用

  ```bash
  adb shell am start -n 包名/Activity名
  ```

  ### 6. 强制停止应用

  ```bash
  adb shell am force-stop 包名
  ```

---

## 十、多设备管理

如果同时连接多台设备，必须加 `-s 设备序列号` 指定设备

```bash
adb -s 序列号 devices
adb -s 序列号 install test.apk
```

---

# 你现在可以直接测试的命令

在你当前打开的CMD窗口里直接输入：

```bash
adb devices
```

## 能看到设备就代表环境完全正常！

### 总结

1. 你当前的**ADB环境已经配置完成**，直接在这个CMD窗口用命令即可
2. 最常用：`adb devices` `adb install` `adb push/pull` `adb logcat`
3. 连接问题优先用：`adb kill-server && adb start-server`
   需要我给你做**常用命令速查表**，或者教你**批量执行ADB命令**吗？
