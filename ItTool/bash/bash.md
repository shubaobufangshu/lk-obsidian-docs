# Bash 详细教程

Bash（Bourne Again SHell）是 Linux 和 macOS 系统上最常用的命令行解释器，也是写 shell 脚本的首选工具。本教程从零开始，系统讲解 Bash 的核心知识，并包含大量示例，帮助你深入掌握。

---

## 目录
1. [Bash 简介与环境](#1-bash-简介与环境)
2. [基本语法与命令执行](#2-基本语法与命令执行)
3. [变量](#3-变量)
4. [引号与字符串处理](#4-引号与字符串处理)
5. [输入输出重定向与管道](#5-输入输出重定向与管道)
6. [条件判断](#6-条件判断)
7. [循环](#7-循环)
8. [函数](#8-函数)
9. [数组](#9-数组)
10. [算术运算](#10-算术运算)
11. [脚本编写规范与调试](#11-脚本编写规范与调试)
12. [进阶主题与陷阱](#12-进阶主题与陷阱)

---

## 1. Bash 简介与环境

### 什么是 Bash
Bash 是 Unix/Linux 系统的默认 shell，既是一个交互式命令行环境，也是一种脚本语言。它兼容 sh（Bourne shell），并加入了许多增强功能。

### 查看当前 Shell
```bash
echo $SHELL          # 输出当前用户的默认 shell
echo $BASH_VERSION   # 输出 Bash 版本
```

### 脚本文件
Bash 脚本文件通常以 `.sh` 结尾，第一行指定解释器（shebang）：
```bash
#!/bin/bash
```
赋予执行权限后即可运行：
```bash
chmod +x script.sh
./script.sh
```
或者直接由 bash 解释执行：
```bash
bash script.sh
```

---

## 2. 基本语法与命令执行

### 命令分隔
- 一行写一条命令，或用分号 `;` 分隔多条命令。
```bash
echo Hello; echo World
```

### 命令退出状态
每个命令执行后返回退出码：`0` 表示成功，非 `0` 表示出错。
```bash
ls /nonexist
echo $?   # 打印上一条命令的退出码
```

### 命令组合
- `&&`：前一条成功才执行下一条。
- `||`：前一条失败才执行下一条。
```bash
mkdir dir && cd dir          # 创建目录成功才进入
grep "error" log.txt || echo "Not found"   # 找不到则提示
```

### 后台执行与作业控制
- 命令后加 `&` 放入后台。
```bash
sleep 10 &
jobs            # 查看后台作业
fg %1           # 将作业 1 调回前台
```

### 命令行自动补全与历史
- Tab 补全命令、文件名。
- `history` 查看历史记录，`!number` 执行历史命令。

---

## 3. 变量

### 定义变量
```bash
name="John"        # 等号两边不能有空格
age=25
```
注意：变量名区分大小写。

### 使用变量
```bash
echo $name
echo ${name}       # 推荐加花括号，避免歧义
```

### 只读变量
```bash
readonly PI=3.14
PI=3.15   # 报错
```

### 删除变量
```bash
unset name
```

### 环境变量与局部变量
- 普通变量只在当前 shell 有效。
- 使用 `export` 将变量传递给子进程。
```bash
export PATH=$PATH:/new/path
```

### 特殊变量
- `$0`：脚本名
- `$1 ~ $9`：位置参数（脚本或函数的参数）
- `$#`：参数个数
- `$@` 和 `$*`：所有参数列表（区别见后文）
- `$?`：上一条命令退出状态
- `$$`：当前 Shell 的 PID
- `$!`：最后一个后台命令的 PID

示例：
```bash
#!/bin/bash
echo "Script name: $0"
echo "First argument: $1"
echo "All arguments: $@"
echo "Number of arguments: $#"
```

### 命令替换
将命令的输出赋给变量。
```bash
now=$(date)          # 推荐语法
# 或使用反引号：now=`date`
echo "Current time: $now"
```

---

## 4. 引号与字符串处理

### 三种引号
- **单引号 `'`**：所有字符原样输出，不解析变量和转义符。
- **双引号 `"`**：解析变量、命令替换和转义字符。
- **反引号 `` ` ``**：命令替换（旧式），不推荐，用 `$()` 代替。

```bash
name=Alice
echo '$name'   # 输出 $name
echo "$name"   # 输出 Alice
```

### 字符串操作
获取长度、子串、替换等。
```bash
str="Hello World"
echo ${#str}            # 长度：11
echo ${str:6}           # 从索引6到结尾：World
echo ${str:0:5}         # 子串：Hello
echo ${str/World/Bash}  # 替换第一个：Hello Bash
echo ${str//o/0}        # 全部替换：Hell0 W0rld
```

### 特殊转义序列
在 `echo` 中使用 `-e` 启用反斜杠转义：
```bash
echo -e "Line1\nLine2\tTabbed"
```

---

## 5. 输入输出重定向与管道

### 标准文件描述符
- `0`：stdin（标准输入）
- `1`：stdout（标准输出）
- `2`：stderr（标准错误）

### 输出重定向
```bash
echo "log" > file.txt      # 覆盖写入
echo "append" >> file.txt  # 追加写入
ls /nonexist 2> error.log  # 错误重定向
ls /nonexist > out.txt 2>&1 # 标准输出和错误都写入同一个文件
```
`&>` 是 `> /path 2>&1` 的简写（bash 专用）。

### 输入重定向
```bash
wc -l < input.txt
```

### Here Document（此处文档）
```bash
cat << EOF > output.txt
Line 1
Line 2
EOF
```
`EOF` 可以是任意标识符。如果写成 `<< 'EOF'`，则内部不进行变量替换。

### 管道 `|`
将一个命令的输出作为另一个命令的输入。
```bash
ls -l | grep ".txt" | wc -l
```

### 命名管道（FIFO）及进程替换（进阶）
```bash
diff <(ls dir1) <(ls dir2)  # 比较两个目录列表
```

---

## 6. 条件判断

### `test` 命令与 `[ ]`
条件判断使用 `test` 命令，其等价形式为 `[ condition ]`。注意方括号内侧必须有空格。
```bash
if [ "$name" = "root" ]; then
    echo "Welcome admin"
fi
```

### 字符串比较
| 操作符 | 含义 |
|--------|------|
| `=`   | 字符串相等 |
| `!=`  | 字符串不等 |
| `-z`  | 字符串为空 |
| `-n`  | 字符串非空 |

```bash
str=""
if [ -z "$str" ]; then
    echo "String is empty"
fi
```

### 数字比较
| 操作符 | 含义 |
|--------|------|
| `-eq` | 等于 |
| `-ne` | 不等于 |
| `-lt` | 小于 |
| `-le` | 小于等于 |
| `-gt` | 大于 |
| `-ge` | 大于等于 |

```bash
num=10
if [ $num -gt 5 ]; then
    echo "Greater than 5"
fi
```
**注意**：数字比较必须使用这些操作符，`>` 在 `[ ]` 中会变成重定向。

### 文件测试
| 操作符 | 含义 |
|--------|------|
| `-e` | 文件存在 |
| `-f` | 普通文件 |
| `-d` | 目录 |
| `-r` | 可读 |
| `-w` | 可写 |
| `-x` | 可执行 |
| `-s` | 文件大小大于0 |

```bash
if [ -f /etc/passwd ]; then
    echo "passwd file exists"
fi
```

### 逻辑运算
- `!`：非
- `-a`：与（用在 `[ ]` 内）
- `-o`：或（用在 `[ ]` 内）
- `&&` 和 `||`：用于多个 `[ ]` 或命令之间。
```bash
if [ -f "$file" ] && [ -r "$file" ]; then
    echo "Readable file"
fi
```

### 双括号 `(( ))` 和 `[[ ]]` (推荐)
- `(( ))`：用于算术比较，支持 `>`, `<`, `&&` 等 C 风格语法。
```bash
if (( num > 5 )); then
    echo "Greater"
fi
```
- `[[ ]]`：字符串比较增强版，支持 `&&`, `||`, 正则匹配 `=~` 等，无需引号保护变量。
```bash
if [[ $name == root* ]]; then   # 通配符匹配
    echo "starts with root"
fi
if [[ $email =~ ^[a-z]+@.* ]]; then
    echo "Valid email pattern"
fi
```

### if-elif-else 结构
```bash
if [ $score -ge 90 ]; then
    grade="A"
elif [ $score -ge 80 ]; then
    grade="B"
else
    grade="C"
fi
```

### case 语句
```bash
read -p "Enter y/n: " answer
case $answer in
    [Yy] | [Yy][Ee][Ss])
        echo "Yes"
        ;;
    [Nn] | [Nn][Oo])
        echo "No"
        ;;
    *)
        echo "Unknown"
        ;;
esac
```

---

## 7. 循环

### for 循环
```bash
# 列表循环
for fruit in apple banana orange; do
    echo $fruit
done

# 数字序列 {起始..结束..步长}
for i in {1..5}; do
    echo "Number $i"
done

# C 风格
for (( i=0; i<5; i++ )); do
    echo $i
done
```

遍历数组或命令输出：
```bash
for file in $(ls); do
    echo "File: $file"
done
# 推荐：
for file in *; do
    [ -f "$file" ] && echo "$file"
done
```

### while 循环
```bash
count=1
while [ $count -le 5 ]; do
    echo $count
    ((count++))
done
```

读取文件每一行：
```bash
while IFS= read -r line; do
    echo "$line"
done < input.txt
```

### until 循环
直到条件为真时才退出。
```bash
count=1
until [ $count -gt 5 ]; do
    echo $count
    ((count++))
done
```

### 循环控制
- `break`：跳出整个循环。
- `continue`：跳过本次循环剩余部分。

### 循环和管道
循环中的变量作用域是子 shell 时（如 `cat file | while ...`），修改的变量在外部不可见。解决方法：使用重定向代替管道。
```bash
total=0
while read -r num; do
    ((total+=num))
done < numbers.txt
echo "Total: $total"
```

---

## 8. 函数

### 定义与调用
```bash
# 定义
greet() {
    echo "Hello, $1"
}
# 调用
greet "Alice"
```

或者：
```bash
function greet {
    echo "Hello, $1"
}
```

### 传递参数
函数内部使用 `$1`, `$2` … `$@`, `$#` 与脚本参数完全一样。
```bash
add() {
    echo $(($1 + $2))
}
result=$(add 3 5)
echo $result   # 8
```

### 返回值
- 函数使用 `return` 返回退出状态码（0-255），不能直接返回字符串。
- 获取函数输出通过命令替换 `$(function)`。
```bash
is_even() {
    if (( $1 % 2 == 0 )); then
        return 0
    else
        return 1
    fi
}
if is_even 4; then
    echo "Even"
fi
```

### 局部变量
函数内用 `local` 定义局部变量，否则默认为全局。
```bash
myfunc() {
    local var="I am local"
    echo $var
}
```

### 函数库
把函数放在一个文件中，在脚本里用 `source` 或 `.` 引入。
```bash
# mylib.sh
say_hello() { echo "Hello"; }

# script.sh
source ./mylib.sh
say_hello
```

---

## 9. 数组

Bash 支持一维数组，索引从 0 开始。

### 定义数组
```bash
# 方式一：括号+空格分隔
fruits=("apple" "banana" "orange")

# 方式二：逐个赋值
fruits[0]="apple"
fruits[1]="banana"
```

### 访问元素
```bash
echo ${fruits[0]}       # 单个元素
echo ${fruits[@]}       # 所有元素
echo ${#fruits[@]}      # 数组长度
echo ${!fruits[@]}      # 所有索引
```

### 遍历数组
```bash
for item in "${fruits[@]}"; do
    echo "$item"
done
```

**注意**：`${fruits[*]}` 和 `${fruits[@]}` 在双引号内行为不同：
- `"${fruits[@]}"` 将每个元素作为一个独立的单词。
- `"${fruits[*]}"` 将所有元素连接成一个单词（由 `IFS` 第一个字符分隔）。
因此遍历时建议用 `"${array[@]}"`。

### 追加元素
```bash
fruits+=("grape")
```

### 删除元素
```bash
unset fruits[1]       # 删除索引1，索引不连续
```

### 关联数组（Bash 4+）
需要先声明。
```bash
declare -A ages
ages[John]=25
ages[Alice]=30
echo ${ages[John]}
# 遍历
for key in "${!ages[@]}"; do
    echo "$key: ${ages[$key]}"
done
```

---

## 10. 算术运算

### 整数运算
- 使用 `$(( expression ))`。
```bash
a=5
b=3
echo $((a + b))
echo $((a * b))
echo $((a % b))
echo $((a++))
```
支持运算符：`+` `-` `*` `/` `%` `**`（幂） `++` `--`

### 浮点运算
Bash 本身不支持浮点数，需调用外部工具 `bc` 或 `awk`。
```bash
echo "scale=2; 5/3" | bc        # 1.66
echo "3.14 * 2.5" | bc
awk "BEGIN {print 5/3}"
```

### let 命令
`let` 可执行算术表达式，但很少用。
```bash
let "c = a + b"
```

---

## 11. 脚本编写规范与调试

### 推荐的开头
```bash
#!/bin/bash
set -euo pipefail
# -e：遇到错误立即退出
# -u：使用未定义变量时报错
# -o pipefail：管道中任意命令失败则整体失败
```
也可分开写：`set -e`, `set -u`, `set -o pipefail`。

### 调试
- `bash -x script.sh`：执行时打印每条命令及其参数（展开后）。
- 在脚本中局部启用：`set -x` 开启跟踪，`set +x` 关闭。
- 打印变量：`echo "DEBUG: var=$var" >&2`。

### 脚本参数处理
```bash
while getopts ":a:b:c" opt; do
  case $opt in
    a) arg_a="$OPTARG" ;;
    b) arg_b="$OPTARG" ;;
    c) flag_c=true ;;
    \?) echo "Invalid option" ;;
  esac
done
shift $((OPTIND-1))
```

### 包含外部文件
```bash
source /path/to/file
# 或
. /path/to/file
```

### 临时文件
使用 `mktemp` 安全创建临时文件。
```bash
tmpfile=$(mktemp /tmp/myscript.XXXXXX)
# 用完后删除
rm "$tmpfile"
```

---

## 12. 进阶主题与陷阱

### IFS（内部字段分隔符）
默认 IFS 为空格、制表、换行。修改 IFS 可改变单词分割行为。
```bash
data="a:b:c"
IFS=: read -r f1 f2 f3 <<< "$data"
echo $f1 $f2 $f3
```
`<<<` 是 here-string，将字符串送给命令的标准输入。

### 默认值、替代值
```bash
echo ${var:-default}    # 如果 var 为空或未定义，使用 default，不修改 var
echo ${var:=default}    # 如果为空，赋值为 default 并返回
echo ${var:+set}        # 如果 var 不为空，返回 set，否则空
echo ${var:?error}      # 如果为空，输出 error 到 stderr 并退出
```

### 字符串/数组切片和模式替换
前面基本介绍了，再补充：
```bash
var="picture.jpg"
echo ${var%.*}   # 删除最短匹配后缀 .* -> picture
echo ${var%%.*}  # 删除最长匹配后缀
echo ${var#*.}   # 删除最短匹配前缀 -> jpg
echo ${var##*.}  # 删除最长匹配前缀
```

### 子 Shell 与命令分组
- `(command1; command2)` 在子 shell 中运行，变量修改不影响父 shell。
- `{ command1; command2; }` 在当前 shell 运行，注意内部空格和最后分号。

```bash
x=10
( x=20 )          # 子shell
echo $x          # 仍是 10
{ x=30; }        # 当前shell
echo $x          # 30
```

### 陷阱（常见错误）
1. **变量未加引号**：`[ $var = "yes" ]`，如果 `$var` 为空会变成 `[ = "yes" ]`，错误。应写 `[ "$var" = "yes" ]`。
2. **`$@` 和 `$*` 不加引号**：会被单词分割，应使用 `"$@"` 保留参数完整性。
3. **`[` 和 `[[` 混用**：`[[` 是 bash 关键字，功能更强，但不具 POSIX 可移植性。
4. **管道中的变量**：管道左侧右侧运行在子 shell，变量修改不保留。解决方法用重定向或进程替换。
5. **使用 `set -e` 的隐形陷阱**：某些命令在条件中或管道中不会触发退出。需谨慎使用 `set -e`，更推荐显式错误处理。

### 信号处理 trap
脚本可以捕捉信号并执行清理。
```bash
trap 'echo "Interrupted"; exit' INT TERM
trap 'rm -f "$tmpfile"' EXIT   # 脚本退出时自动清理
```

### 正则表达式与文本处理
在 `[[ ]]` 中使用 `=~` 匹配正则，但更复杂的文本处理通常交给 `grep`, `sed`, `awk`。
```bash
if [[ "abc123" =~ ^[a-z]+[0-9]+$ ]]; then
    echo "Pattern matched"
fi
```

### 关联外部命令
Bash 本身不是万能的，常用工具组合：
- `find`：文件查找
- `xargs`：将输入转为参数
- `jq`：处理 JSON
- `sed`/`awk`：文本流编辑和格式化

示例：查找所有 `.txt` 文件并统计行数。
```bash
find . -name "*.txt" -print0 | xargs -0 wc -l
```

### 编写健壮脚本 checklist
- 使用 `#!/bin/bash` 和 `set -euo pipefail`
- 所有变量引用加双引号，除明确需要分词的情况
- 参数校验：检查 `$#` 和文件是否存在
- 为关键函数设置返回值并检查 `$?`
- 清理临时文件用 `trap`
- 避免使用 `eval`，防止注入攻击
- 路径包含空格用引号包围
- 使用 `printf` 代替 `echo` 输出特殊字符