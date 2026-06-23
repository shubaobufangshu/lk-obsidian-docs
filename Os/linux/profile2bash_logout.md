# 用户级
```bash
# ~/.profile: 登录Shell执行的配置文件
# 如果存在 ~/.bash_profile 或 ~/.bash_login，bash 不会读取此文件
```
注释说明：**仅登录 Shell（SSH/开机登录）执行**，优先级低于 `.bash_profile`、`.bash_login`。

```bash
# 默认 umask 在 /etc/profile 设置
# SSH 登录的 umask 需安装 libpam-umask
#umask 022
```
默认注释，未启用权限掩码配置，无实际作用。

```bash
# 如果运行的是 bash
if [ -n "$BASH_VERSION" ]; then
    # 存在 ~/.bashrc 就加载它
    if [ -f "$HOME/.bashrc" ]; then
        . "$HOME/.bashrc"
    fi
fi
```
**核心作用**：登录Shell执行 `.profile` 时，**自动加载 ~/.bashrc**，让别名、环境变量、终端配置在 SSH 下也生效。

```bash
# 若 ~/bin 目录存在，加入系统 PATH
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi
```
将用户私有脚本目录 `~/bin` 加入环境变量，可直接运行目录内的命令。

```bash
# 若 ~/.local/bin 目录存在，加入系统 PATH
if [ -d "$HOME/.local/bin" ] ; then
    PATH="$HOME/.local/bin:$PATH"
fi
```
将用户常用工具目录 `~/.local/bin` 加入环境变量（pip 安装的命令默认在此）。

```bash
. "$HOME/.cargo/env"
```
加载 Rust 包管理器 Cargo 环境，使 `cargo`/`rustc` 命令全局可用。

---


```bash
# ~/.bash_logout：登录Shell退出时由bash执行
```
**作用**：仅当**登录 Shell（SSH/控制台登录）退出时**自动运行。

```bash
# 退出控制台时清屏，保护隐私
```
注释说明用途。

```bash
if [ "$SHLVL" = 1 ]; then
```
**SHLVL=1** 表示当前是**最外层登录Shell**，只在此时执行。

```bash
[ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
```
**作用**：如果`clear_console`命令存在，就**安静清屏**，退出后不留命令痕迹，保护隐私。

---
 极简总结
**登录Shell退出 → 自动清屏 → 不留历史痕迹**
普通终端关闭不执行，仅SSH/控制台登录退出时生效。

# 系统级
## /etc/profile 逐行中文注释+详细解释
```bash
# /etc/profile：适用于 Bourne shell (sh(1))
# 以及所有兼容 Bourne 的 shell（bash、ksh、ash 等）的**系统全局配置文件**
# 所有用户登录系统时，都会执行此文件
```

```bash
# 判断是否为交互式shell（存在命令提示符PS1变量即为交互式）
# 非交互式shell（脚本执行）不执行后续配置
if [ "${PS1-}" ]; then

  # 判断当前shell是bash，且不是以/bin/sh兼容模式运行
  if [ "${BASH-}" ] && [ "$BASH" != "/bin/sh" ]; then
    # 注释说明：/etc/bash.bashrc 已配置默认命令提示符
    # 若系统存在该文件，就加载它（继承bash的完整配置）
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi

  # 当前shell不是bash（如sh、ash等）
  else
    # 判断用户UID：root用户UID=0
    if [ "$(id -u)" -eq 0 ]; then
      # root用户命令提示符设为 #
      PS1='# '
    else
      # 普通用户命令提示符设为 $
      PS1='$ '
    fi
  fi
fi
```

```bash
# 判断系统存在 /etc/profile.d 目录
if [ -d /etc/profile.d ]; then
  # 遍历该目录下所有符合命名规则的.sh脚本
  for i in $(run-parts --list --regex '^[a-zA-Z0-9_][a-zA-Z0-9._-]*\.sh$' /etc/profile.d); do
    # 若脚本文件可读，则执行加载该脚本
    if [ -r $i ]; then
      . $i
    fi
  done
  # 销毁循环变量i，避免污染系统环境
  unset i
fi
```

---

## 核心功能总结（无废话）
1. **执行时机**：用户**登录系统**时执行一次，全局生效
2. **交互式判断**：仅对手动打开的终端生效，不影响脚本
3. **shell 区分处理**
   - bash  shell：加载 `/etc/bash.bashrc` 完整配置
   - 其他shell：仅设置极简提示符 `#`/`$`
1. **加载系统环境**：自动执行 `/etc/profile.d/` 下所有系统脚本，配置环境变量、软件运行环境
   ### /etc/profile.d 脚本说明（按文件）
2. **01-locale-fix.sh**：修复系统区域/语言编码，解决乱码
3. **70-systemd-shell-extra.sh**：systemd 相关终端扩展配置
4. **80-systemd-osc-context.sh**：终端 OSC 转义序列、上下文适配
5. **Z97-byobu.sh**：为 byobu 终端分屏工具加载环境
6. **Z99-cloud-locale-test.sh**：云环境语言编码检测
7. **Z99-cloudinit-warnings.sh**：云初始化服务告警提示
8. **apps-bin-path.sh**：追加应用程序二进制目录到 PATH
9. **bash_completion.sh**：启用 Bash 命令自动补全
10. **gawk.sh / gawk.csh**：分别为 bash、csh 配置 gawk 环境
11. **update-motd.sh**：更新登录提示信息（每日消息）

### 补充规则
文件名数字前缀控制**加载顺序**，数字越小越先执行；后缀区分 Shell 类型，登录 Shell 会自动加载对应可执行脚本。