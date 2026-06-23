# Zsh 完全指南：从入门到精通

Zsh（Z Shell）是一款强大的 Unix Shell，它几乎兼容 Bash，但提供了更智能的补全、更灵活的通配、主题化的提示符以及丰富的插件生态。本教程将带你从安装开始，逐步深入，让你彻底掌握 Zsh 的使用与配置。

---
## 目录
1. [什么是 Zsh？为什么要用？](#1-什么是-zsh为什么要用)
2. [安装 Zsh](#2-安装-zsh)
3. [将 Zsh 设为默认 Shell](#3-将-zsh-设为默认-shell)
4. [第一次启动与配置文件](#4-第一次启动与配置文件)
5. [命令行编辑与键绑定](#5-命令行编辑与键绑定)
6. [超级补全系统](#6-超级补全系统)
7. [强大的通配符与 Globbing](#7-强大的通配符与-globbing)
8. [历史记录管理](#8-历史记录管理)
9. [别名与函数](#9-别名与函数)
10. [提示符与主题](#10-提示符与主题)
11. [插件与框架（Oh My Zsh）](#11-插件与框架oh-my-zsh)
12. [高级插件推荐](#12-高级插件推荐)
13. [进阶技巧与钩子](#13-进阶技巧与钩子)
14. [常见问题与排错](#14-常见问题与排错)
---
## 1. 什么是 Zsh？为什么要用？
Zsh 是一款为交互式使用设计的 Shell，也是脚本语言解释器。它在 1990 年由 Paul Falstad 创建，现在由社区维护。
**为什么选择 Zsh？**

- **惊人的补全**：不仅能补全命令和文件，还能补全命令选项、进程 ID、Git 分支、包管理器参数等。
- **强大的 Globbing**：可以用 `**/*.txt` 递归匹配所有目录下的 txt 文件，比 Bash 的 `find` 方便太多。
- **更好的历史管理**：跨会话共享历史、按时间排序、去除重复命令。
- **可定制的提示符**：可以显示 Git 状态、Python 虚拟环境、错误码等，且支持右侧提示符。
- **拼写纠正**：不小心打错目录名时，Zsh 能自动纠正。
- **路径展开**：按 `cd /u/l/b` 再按 Tab，可以展开成 `/usr/local/bin`。
- **插件和主题生态**：通过 Oh My Zsh 等框架，几行配置就能获得语法高亮、自动建议等增强功能。
macOS 自 2019 年起已将默认 Shell 从 Bash 换为 Zsh，各大 Linux 发行版也提供官方包。
---
## 2. 安装 Zsh
### macOS
macOS Catalina 及以上已经自带 Zsh，无需安装。若版本较老或想安装最新版，可通过 Homebrew：
```bash
brew install zsh
```
### Linux (Debian/Ubuntu)
```bash
sudo apt update
sudo apt install zsh
```
### Linux (Fedora/RHEL)
```bash
sudo dnf install zsh
```
### Windows (WSL 或 Git Bash)
WSL 中的 Linux 发行版可直接安装，与上述方法相同。若使用 Git Bash，则无法直接运行 Zsh，建议在 WSL 下使用。
安装后确认版本：
```bash
zsh --version
```
---
## 3. 将 Zsh 设为默认 Shell

使用 `chsh`（change shell）命令：
```bash
chsh -s $(which zsh)
```
输入用户密码后，下次登录时就会进入 Zsh。立即在当前终端体验可手动输入 `zsh`。

> 注意：有些系统需要将 `$(which zsh)` 的路径（通常是 `/usr/bin/zsh` 或 `/usr/local/bin/zsh`）添加到 `/etc/shells` 文件中才能通过 `chsh` 切换。可用 `cat /etc/shells` 检查。
---
## 4. 第一次启动与配置文件
第一次运行 Zsh 时，如果没有 `~/.zshrc` 文件，会进入一个配置向导。建议手动创建配置文件，便于完全掌控。
**Zsh 的配置文件加载顺序：**
- 登录 shell：`.zshenv` → `.zprofile` → `.zshrc` → `.zlogin`
- 交互式非登录 shell：`.zshenv` → `.zshrc`
- 脚本执行：`.zshenv` → `.zshrc` （若设置了 `POSIX_ALIAS` 等会影响，通常只读 `.zshenv`）
日常配置 99% 都在 `~/.zshrc` 中完成。
创建一个最基本的 `~/.zshrc`：
```bash
# 历史记录设置
HISTSIZE=50000
SAVEHIST=50000
HISTFILE=~/.zsh_history
setopt SHARE_HISTORY
setopt HIST_IGNORE_DUPS
setopt HIST_FIND_NO_DUPS

# 基础补全
autoload -Uz compinit && compinit

# 别名
alias ll='ls -lh'
alias la='ls -lAh'
alias grep='grep --color=auto'

# 路径
export PATH="$HOME/bin:$PATH"
```
---
## 5. 命令行编辑与键绑定
Zsh 默认使用 Emacs 风格的键绑定，但也支持 vi 模式。
### Emacs 模式（默认）
常用快捷键：
- `Ctrl + A`：行首
- `Ctrl + E`：行尾
- `Ctrl + W`：删除前一个单词
- `Alt + D`：删除后一个单词
- `Ctrl + U`：删除整行
- `Ctrl + K`：删除从光标到行尾
- `Ctrl + Y`：粘贴刚才删除的内容（撤销删除）
### Vi 模式
在 `~/.zshrc` 中添加：
```bash
bindkey -v
```
按 `Esc` 进入命令模式，可使用 `h/j/k/l` 移动，`/` 搜索历史等。
### 自定义快捷键
使用 `bindkey` 命令。例如把 `Ctrl + R` 改为增强的历史搜索（需配合插件）：
```bash
bindkey '^R' history-incremental-search-backward
```
---
## 6. 超级补全系统

Zsh 的补全系统是其核心亮点。只需启用：
```bash
autoload -Uz compinit && compinit
```
### 常用的补全选项（可放在 compinit 之后）
```bash
zstyle ':completion:*' menu select       # 用菜单显示补全，可用箭头选择
zstyle ':completion:*' matcher-list 'm:{a-z}={A-Z}' # 不区分大小写
setopt COMPLETE_IN_WORD                  # 允许光标在单词中间补全
setopt ALWAYS_TO_END                     # 补全后光标移到末尾
```
### 补全示例
- 命令选项补全：`ls -<Tab>` 会列出 `-l`, `-a`, `-h` 等。
- 进程 ID 补全：`kill <Tab>` 会列出当前运行的所有进程名和 PID。
- 远程主机补全：`ssh <Tab>` 会从 `~/.ssh/config` 和 `known_hosts` 中读取主机名。
- Git 分支补全：`git checkout <Tab>` 列出所有分支。
- 包管理器补全：`brew install <Tab>` 列出可安装的包。
### 路径展开（强力技巧）
输入 `cd /u/l/b` 按 Tab，Zsh 会将其补全为 `/usr/local/bin`，只要路径首字母序列唯一对应。这被称为“灵活补全”。

---
## 7. 强大的通配符与 Globbing
Zsh 支持比 Bash 更丰富的文件名生成模式。

| 通配符/标志        | 功能                                       | 示例                              |
|-------------------|--------------------------------------------|-----------------------------------|
| `**`              | 递归匹配目录                               | `ls **/*.txt`                     |
| `*.(txt\|md)`    | 匹配扩展名为 txt 或 md 的文件              | `ls *.(txt\|md)`                  |
| `<1-100>`         | 数字范围                                   | `ls file<1-100>.txt`              |
| `(.)` (全局限定符) | 只匹配普通文件                             | `ls *(.)`                         |
| `(/)`              | 只匹配目录                                 | `ls *(/)`                         |
| `(@)`              | 只匹配符号链接                             | `ls *(@)`                         |
| `(Lm+10)`          | 文件大小大于 10MB                          | `ls *(Lm+10)`                     |
| `(m-7)`            | 最近 7 天内修改过的文件                    | `ls *(m-7)`                       |

使用 `setopt EXTENDED_GLOB` 启用扩展通配（Oh My Zsh 默认启用）。
例子：删除当前目录下所有不以 `.txt` 结尾的文件：
```bash
rm ^*.txt
```
这比 `find` 和 `grep -v` 组合直观得多。
---
## 8. 历史记录管理

Zsh 的历史功能远比 Bash 强大。推荐配置：
```bash
HISTSIZE=100000                   # 内存中记录条数
SAVEHIST=100000                   # 文件保存条数
HISTFILE=~/.zsh_history           # 历史文件路径
setopt SHARE_HISTORY             # 多个终端实时共享历史
setopt HIST_IGNORE_DUPS          # 连续重复命令不记录
setopt HIST_FIND_NO_DUPS         # 搜索时去重
setopt HIST_IGNORE_SPACE         # 以空格开头的命令不记录
setopt INC_APPEND_HISTORY_TIME   # 每条命令立即追加并带时间戳
```
### 历史搜索
- `Ctrl + R`：增量搜索历史（默认行为类似 Bash）。
- 安装 `zsh-history-substring-search` 或 `zsh-autosuggestions` 后，可以用上下箭头按已输入内容搜索历史。
- 命令 `fc -l 1` 列出所有历史；`history` 通常也映射为 `fc -l`。
---
## 9. 别名与函数

### 别名
对于常用命令的短替代：
```bash
alias g='git'
alias gst='git status'
alias gaa='git add -A'
alias gc='git commit'
alias gl='git pull'
alias gp='git push'
alias ...='cd ../..'
alias ....='cd ../../..'
```
避免覆盖已有命令：`alias ls='ls --color=auto'` 等。
**全局别名**（在管道中也可展开）：
```bash
alias -g G='| grep'
alias -g L='| less'
alias -g H='| head'
alias -g T='| tail'
```
使用：`ps aux G python` 会展开为 `ps aux | grep python`。
**后缀别名**：根据文件后缀自动打开程序：
```bash
alias -s html='vim'
alias -s rb='ruby'
alias -s py='python'
alias -s md='typora'
```
在命令行输入 `test.md` 回车，就会用 Typora 打开。
### 函数
复杂逻辑用函数。函数可以放入 `~/.zshrc` 或 `~/.oh-my-zsh/custom/` 等位置。

示例：创建目录并进入
```bash
mkcd() { mkdir -p "$1" && cd "$1"; }
```
示例：提取压缩文件（根据后缀自动判断）
```bash
extract() {
  if [ -f $1 ] ; then
    case $1 in
      *.tar.bz2) tar xjf $1     ;;
      *.tar.gz)  tar xzf $1     ;;
      *.bz2)     bunzip2 $1     ;;
      *.rar)     unrar e $1     ;;
      *.gz)      gunzip $1      ;;
      *.tar)     tar xf $1      ;;
      *.tbz2)    tar xjf $1     ;;
      *.tgz)     tar xzf $1     ;;
      *.zip)     unzip $1       ;;
      *.Z)       uncompress $1  ;;
      *.7z)      7z x $1        ;;
      *)         echo "'$1' cannot be extracted via extract()" ;;
    esac
  else
    echo "'$1' is not a valid file"
  fi
}
```
---
## 10. 提示符与主题
Zsh 的提示符可以极度定制，但直接写很复杂，通常借助框架如 Oh My Zsh 应用主题。
### 手动简单定制
提示符变量：
- `PROMPT` 或 `PS1`：左侧主提示符
- `RPROMPT` 或 `RPS1`：右侧提示符
- `PROMPT2`：续行提示符

常用转义序列：
- `%n`：用户名
- `%m`：主机名（短）
- `%~`：当前目录（`~` 缩写）
- `%d`：完整目录路径
- `%#`：普通用户显示 `%`，root 显示 `#`
- `%F{color}...%f`：设置前景色，如 `%F{red}hello%f`
- `%B...%b`：粗体
- `%T`：当前时间 24 小时制
- `%*`：当前时间带秒
- `%(?. .%F{red}%?%f)`：上一条命令退出码不为 0 时显示红色错误码

示例：
```bash
PROMPT='%F{cyan}%n@%m%f %F{yellow}%~%f %# '
RPROMPT='[%*]'
```
效果：`user@host ~ % `   右侧显示当前时间。

### 使用 Oh My Zsh 主题
Oh My Zsh 提供了上百个主题，可直接使用。详见第 11 节。

---

## 11. 插件与框架（Oh My Zsh）

**Oh My Zsh** 是最流行的 Zsh 配置管理框架，简化了插件和主题的安装。

### 安装 Oh My Zsh
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
或使用 wget。脚本会将现有 `.zshrc` 备份，并创建新的 `.zshrc`。

### 目录结构
- 插件：`~/.oh-my-zsh/plugins/`
- 主题：`~/.oh-my-zsh/themes/`
- 自定义目录：`~/.oh-my-zsh/custom/`，放置自己的插件和主题。

### 启用插件
编辑 `~/.zshrc`，找到 `plugins=` 行：
```bash
plugins=(
  git
  docker
  docker-compose
  kubectl
  colored-man-pages
  zsh-autosuggestions
  zsh-syntax-highlighting
)
```
每个名字对应一个插件目录，大部分是内置的。要安装外部插件（如上后两个），需先克隆到 `$ZSH_CUSTOM/plugins/`：
```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### 切换主题
修改 `ZSH_THEME` 变量：
```bash
ZSH_THEME="agnoster"  # 需安装 Powerline 字体
```
常用主题：`robbyrussell`（默认）、`agnoster`、`powerlevel10k`（需单独安装）。

> 主题通常需要安装字体，如 [Nerd Fonts](https://www.nerdfonts.com/) 或 Powerline 字体，否则可能出现乱码。

---

## 12. 高级插件推荐

### zsh-autosuggestions
根据历史记录和补全，在命令行灰色提示建议，按 `→` 或 `End` 接受。
```bash
# 安装后启用插件即可，无需额外配置
```
可自定义颜色：
```bash
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE="fg=#666666"
```

### zsh-syntax-highlighting
为输入的命令提供即时语法高亮：有效命令显示绿色，无效显示红色等。
安装后在插件列表里加入即可。

### z (zsh-z 或 zoxide)
智能目录跳转：`z project` 会根据历史跳转到最匹配的目录。比 `cd` 省很多时间。
Oh My Zsh 内置 `z` 插件，只需在插件列表添加 `z`。

### fzf 与 fzf-tab
`fzf` 是一个通用模糊查找工具，搭配 `fzf-tab` 插件可以让 Tab 补全的菜单变成可交互模糊搜索。

### Powerlevel10k 主题
`powerlevel10k` 是一个快速、功能丰富的主题，初次运行时会有配置向导，按需显示 Git 状态、执行时间、Python 环境等。
安装：克隆仓库到 `$ZSH_CUSTOM/themes/powerlevel10k`，设置 `ZSH_THEME="powerlevel10k/powerlevel10k"`，重启终端运行 `p10k configure`。

---

## 13. 进阶技巧与钩子

### 钩子函数
Zsh 支持在特定事件触发时执行自定义函数，例如进入目录时自动设置环境变量、命令执行前打印时间等。

常用钩子（通过定义函数并添加到数组实现）：
- `chpwd`：目录改变时
- `precmd`：每次显示提示符前
- `preexec`：每条命令执行前

示例：进入项目目录时自动激活 Python 虚拟环境
```bash
autoload -Uz add-zsh-hook
auto_venv() {
  if [[ -f .venv/bin/activate ]]; then
    source .venv/bin/activate
  fi
}
add-zsh-hook chpwd auto_venv
auto_venv  # 对当前目录也执行一次
```

### 在文件中插入单引号/全局引用
Zsh 的 `quote-line` 小部件：按 `Alt + '` 可给当前命令行添上单引号，保护特殊字符。

### 键盘宏录制
按 `Ctrl + X (` 开始录制，`Ctrl + X )` 停止。之后 `Ctrl + X e` 执行宏。

### 小部件与 ZLE
Zsh 行编辑器（ZLE）是可编程的，你可以定义自己的编辑功能。例如，按 `Ctrl + O` 在命令末尾添加 `| less`：
```bash
add-less-pipe() {
  LBUFFER+=" | less"
  zle redisplay
}
zle -N add-less-pipe
bindkey '^O' add-less-pipe
```

---

## 14. 常见问题与排错

### 乱码或字体问题
- 使用 Oh My Zsh 的 `agnoster`、`powerlevel10k` 等主题时，务必安装 Powerline 字体或 Nerd Font。
- iTerm2、Terminal、VS Code 终端等均需设置字体为已安装的带图标字体。

### 补全不生效
- 检查 `~/.zshrc` 是否有 `autoload -Uz compinit && compinit`。
- 确认插件名拼写正确，且已安装。
- 运行 `rm -f ~/.zcompdump; compinit` 重建补全缓存。

### 速度慢
- 启动慢：可能是 Oh My Zsh 加载了太多插件。用 `zsh -xv` 调试启动过程，去掉不必要的插件，或考虑换用更轻量的 `zinit` 或 `zi`。
- 建议使用 `powerlevel10k` 即时提示，它本身优化了速度。
- 将 `compinit` 懒加载或将不常用工具的补全设为按需加载。

### 从 Bash 迁移
- Zsh 高度兼容 Bash 脚本，基本无需修改 `.bashrc` 中的别名和函数，可直接复制到 `~/.zshrc`。
- 数组索引：Bash 从 0 开始，Zsh 默认从 1 开始。如需兼容，在脚本开头添加 `setopt KSH_ARRAYS`。
- `source` 命令两者通用。

### 卸载 Oh My Zsh
运行 `uninstall_oh_my_zsh` 命令（在终端里输入即可），它会清理文件并恢复备份的 `.zshrc`。

---

## 结语

Zsh 将命令行体验提升到了新的高度。一旦习惯了它的智能补全、快速跳转和即时建议，回到 Bash 会感到诸多不便。

本教程覆盖了从零基础到高级定制的方方面面。建议你一步步地：
1. 安装 Zsh
2. 掌握基本设置和历史管理
3. 安装 Oh My Zsh
4. 按需添加插件
5. 挑选一个心仪的主题（推荐 powerlevel10k）
6. 逐步把自己的工作流优化

记得经常查看 [Oh My Zsh 官方 wiki](https://github.com/ohmyzsh/ohmyzsh/wiki) 和 [Zsh 手册](https://zsh.sourceforge.io/Doc/)