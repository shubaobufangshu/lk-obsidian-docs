Python CLI 程序设计详细教程
# 一、CLI 基础认知
## 1.1 什么是 CLI 程序
CLI（Command Line Interface，命令行界面）程序是通过终端/命令行接收用户指令、执行逻辑并输出结果的程序，无需图形界面，具备**启动快、资源占用低、可脚本自动化、适合服务器运维、批量任务处理**等优势，是后端开发、运维自动化、数据处理、工具开发的核心场景。
Python 开发 CLI 程序门槛极低，支持原生标准库、轻量化三方库多种方案，可快速实现从简单脚本到企业级命令行工具的开发。
## 1.2 核心开发场景
- 运维工具：文件批量处理、日志分析、服务启停、环境配置
- 开发工具：代码生成、打包编译、接口测试、数据迁移
- 自动化脚本：定时任务、批量数据清洗、报表生成
- 跨平台工具：Windows/Linux/Mac 通用轻量工具
## 1.3 环境准备
基于 Python3.8+ 版本开发，所有示例兼容主流新版本，提前配置好系统环境变量，可直接在终端执行 python / python3 命令。
# 二、零基础原生 CLI 实现（无第三方库）
无需安装任何依赖，通过 Python 内置模块快速实现基础命令行交互，适合极简小型工具。
## 2.1 sys.argv 命令行参数获取
**sys.argv** 是 Python 原生参数列表，用于接收终端传入的位置参数，列表第一个值为脚本文件名，后续为用户传入参数，所有参数默认字符串类型。
### 示例代码（sys_cli.py）
```python
import sys

def main():
    # 获取参数列表
    args = sys.argv
    print(f"脚本名称：{args[0]}")
    print(f"传入参数：{args[1:]}")

    # 简单逻辑：数字求和
    if len(args) > 2:
        try:
            num1 = int(args[1])
            num2 = int(args[2])
            print(f"求和结果：{num1 + num2}")
        except ValueError:
            print("参数错误：请传入整数数字")

if __name__ == "__main__":
    main()
```
### 终端执行命令与效果
```Plain
# 基础调用
python sys_cli.py 10 20
# 输出
脚本名称：sys_cli.py
传入参数：['10', '20']
求和结果：30
```
## 2.2 原生交互式输入 input
适用于需要动态交互的 CLI 场景，运行程序后等待用户输入内容，实现问答式交互。
```python
def main():
    name = input("请输入你的姓名：")
    age = input("请输入你的年龄：")
    print(f"您好 {name}，今年 {age} 岁")

if __name__ == "__main__":
    main()
```
## 2.3 原生方案优缺点
✅ 优点：零依赖、启动极速、部署简单
❌ 缺点：无参数校验、无默认帮助文档、不支持可选参数/子命令、复杂场景代码冗余
**适用场景**：超简单一次性脚本、参数固定的极简工具
# 三、标准库 argparse 企业级基础 CLI 开发
**argparse** 是 Python 内置标准库，无需额外安装，是官方推荐的标准 CLI 开发方案，支持**参数校验、默认值、帮助文档、位置参数、可选参数、子命令**，兼顾简洁性与功能性，是中小型 CLI 工具首选。
## 3.1 核心核心概念
- 位置参数：必传参数，按顺序传入，无参数名
- 可选参数：选传参数，以 `-` / `--` 开头，支持默认值
- 参数类型：自动类型转换（int/float/bool），自动校验参数合法性
- 帮助文档：自动生成 `-h/--help` 帮助信息
## 3.2 完整基础示例
```python
import argparse

def main():
    # 1. 创建解析器，设置程序描述
    parser = argparse.ArgumentParser(description="Python argparse 示例：简易计算工具")
    
    # 2. 添加位置参数（必传）
    parser.add_argument("num1", type=float, help="第一个计算数字")
    parser.add_argument("num2", type=float, help="第二个计算数字")
    
    # 3. 添加可选参数（选传，带默认值）
    parser.add_argument("-o", "--operate", type=str, default="add", 
                        choices=["add", "sub", "mul", "div"],
                        help="运算方式：add加法、sub减法、mul乘法、div除法（默认add）")
    
    # 4. 解析终端参数
    args = parser.parse_args()

    # 5. 业务逻辑
    res = 0
    if args.operate == "add":
        res = args.num1 + args.num2
    elif args.operate == "sub":
        res = args.num1 - args.num2
    elif args.operate == "mul":
        res = args.num1 * args.num2
    elif args.operate == "div":
        if args.num2 == 0:
            print("错误：除数不能为0")
            return
        res = args.num1 / args.num2
    
    print(f"计算结果：{res}")

if __name__ == "__main__":
    main()
```
## 3.3 常用执行命令
```Plain
# 查看自动生成帮助文档
python cli_argparse.py -h

# 默认加法运算
python cli_argparse.py 10 5

# 指定乘法运算
python cli_argparse.py 10 5 -o mul
```

## 3.4 高级功能：子命令开发
子命令是复杂 CLI 核心能力，可实现 `git add` / `docker run` 式多级命令，适配多功能工具。
```python
import argparse

def main():
    parser = argparse.ArgumentParser(description="文件处理CLI工具")
    # 创建子命令容器
    subparsers = parser.add_subparsers(dest="command", help="子命令功能")

    # 子命令1：创建文件
    create_parser = subparsers.add_parser("create", help="创建新文件")
    create_parser.add_argument("filename", type=str, help="文件名称")

    # 子命令2：删除文件
    delete_parser = subparsers.add_parser("delete", help="删除文件")
    delete_parser.add_argument("filename", type=str, help="文件名称")

    args = parser.parse_args()

    # 子命令逻辑分发
    if args.command == "create":
        with open(args.filename, "w", encoding="utf-8") as f:
            f.write("")
        print(f"文件 {args.filename} 创建成功")
    elif args.command == "delete":
        import os
        if os.path.exists(args.filename):
            os.remove(args.filename)
            print(f"文件 {args.filename} 删除成功")
        else:
            print(f"文件 {args.filename} 不存在")

if __name__ == "__main__":
    main()
```
### 子命令执行示例
```Plain
python cli_sub.py create test.txt
python cli_sub.py delete test.txt
```
# 四、主流三方 CLI 库实战（高效开发）
原生 argparse 语法繁琐，工业级开发常用 **Fire、Click、Typer** 三大库，语法更简洁、功能更强大、开发效率更高。
## 4.1 Google Fire：一行代码生成 CLI（极简首选）
Fire 是谷歌开源轻量化库，核心优势：**无需手动解析参数，一行代码将任意Python对象（函数、类）转为CLI工具**，零学习成本。
### 安装依赖
```Plain
pip install fire
```
### 完整示例
```python
import fire

# 定义普通函数
def calc(num1: float, num2: float, operate: str = "add"):
    """
    简易计算工具
    :param num1: 数字1
    :param num2: 数字2
    :param operate: 运算方式 add/sub/mul/div
    """
    if operate == "add":
        return num1 + num2
    elif operate == "sub":
        return num1 - num2
    elif operate == "mul":
        return num1 * num2
    elif operate == "div":
        return num1 / num2 if num2 != 0 else "除数不能为0"

# 核心：一行生成CLI
if __name__ == "__main__":
    fire.Fire(calc)
```
### 执行命令
```Plain
python cli_fire.py 10 20
python cli_fire.py 10 20 --operate mul
# 自动帮助文档
python cli_fire.py --help
```
## 4.2 Click：企业级标准 CLI 库（最常用）
Click 是 Flask 团队开发的专业 CLI 库，语法优雅、装饰器驱动，原生支持**彩色输出、交互式输入、进度条、命令组、参数校验**，是Python CLI 工业级标准。
### 安装依赖
```Plain
pip install click
```
### 完整示例（命令组+参数+交互）
```python
import click

# 创建命令组（支持多子命令）
@click.group()
def cli():
    """Click 多功能文件处理 CLI 工具"""
    pass

# 子命令1：创建文件
@cli.command("create")
@click.option("--name", "-n", required=True, help="文件名称")
@click.option("--content", "-c", default="", help="文件初始内容")
def create(name, content):
    """创建新文件"""
    with open(name, "w", encoding="utf-8") as f:
        f.write(content)
    click.secho(f"✅ 文件 {name} 创建成功", fg="green")

# 子命令2：交互式输入
@cli.command("input")
def interactive():
    """交互式问答输入"""
    name = click.prompt("请输入用户名")
    pwd = click.prompt("请输入密码", hide_input=True)
    click.secho(f"用户名：{name}，密码已隐藏", fg="blue")

if __name__ == "__main__":
    cli()
```
### 执行命令
```Plain
python cli_click.py create -n demo.txt -c "hello python cli"
python cli_click.py input
```
## 4.3 Typer：现代化类型提示 CLI 库（未来趋势）
Typer 基于 Click 开发，完全兼容 Click 功能，核心优势：**依托 Python 类型注解自动解析参数**，代码更简洁、类型校验更严格，适合现代 Python 项目。
### 安装依赖
```Plain
pip install typer
```
### 完整示例
```python
import typer

app = typer.Typer(help="Typer 现代化CLI工具")

@app.command("calc")
def calc(
    num1: float, 
    num2: float, 
    operate: str = typer.Option("add", help="运算方式：add/sub/mul/div")
):
    """带类型校验的计算工具"""
    match operate:
        case "add": res = num1 + num2
        case "sub": res = num1 - num2
        case "mul": res = num1 * num2
        case "div": res = num1 / num2 if num2 != 0 else "除数错误"
        case _: res = "不支持的运算方式"
    typer.echo(f"计算结果：{res}")

if __name__ == "__main__":
    app()
```
# 五、CLI 高阶功能开发
## 5.1 终端彩色输出
通过 Click/Typer 原生能力实现彩色日志输出，区分成功、错误、提示信息，提升交互体验。
```python
import click
click.secho("成功！", fg="green", bold=True)
click.secho("警告！", fg="yellow")
click.secho("错误！", fg="red", bold=True)
```
## 5.2 进度条展示
适配文件下载、批量处理等耗时操作，提升 CLI 交互质感。
```python
import click
import time

# 模拟批量任务
with click.progressbar(range(100), label="任务处理中") as bar:
    for i in bar:
        time.sleep(0.02)
```
## 5.3 配置文件加载
支持 json/yaml/ini 配置文件，避免重复传参，适配复杂项目。需安装依赖：`pip install pyyaml`
```python
import yaml

def load_config(config_path: str = "config.yaml"):
    """加载yaml配置文件"""
    with open(config_path, "r", encoding="utf-8") as f:
        return yaml.safe_load(f)

# 结合argparse使用
if __name__ == "__main__":
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument("--config", default="config.yaml", help="配置文件路径")
    args = parser.parse_args()
    config = load_config(args.config)
    print("加载配置：", config)
```
## 5.4 异常捕获与友好报错
屏蔽原生报错堆栈，输出简洁易懂的错误提示，提升工具稳定性。
```python
import click

def safe_run(func):
    """异常捕获装饰器"""
    def wrapper(*args, **kwargs):
        try:
            return func(*args, **kwargs)
        except Exception as e:
            click.secho(f"执行失败：{str(e)}", fg="red")
            return None
    return wrapper
```
# 六、工程化实战：完整多功能 CLI 工具
整合上述知识点，开发一款**文件批量处理CLI工具**，支持文件创建、批量重命名、文件删除、配置加载，基于 Click 实现工业级规范。
```python
import click
import os
import yaml

# 加载全局配置
def load_config():
    if os.path.exists("cli_config.yaml"):
        with open("cli_config.yaml", "r", encoding="utf-8") as f:
            return yaml.safe_load(f)
    return {}

# 主命令组
@click.group()
def file_tool():
    """Python 批量文件处理 CLI 工具"""
    pass

# 1. 创建文件
@file_tool.command("create")
@click.option("--name", "-n", required=True, help="文件名")
@click.option("--content", "-c", default="", help="文件内容")
def create_file(name, content):
    try:
        with open(name, "w", encoding="utf-8") as f:
            f.write(content)
        click.secho(f"✅ 文件 {name} 创建成功", fg="green")
    except Exception as e:
        click.secho(f"❌ 创建失败：{e}", fg="red")

# 2. 批量重命名
@file_tool.command("rename")
@click.option("--path", "-p", default="./", help="文件路径")
@click.option("--prefix", "-pre", default="", help="重命名前缀")
def batch_rename(path, prefix):
    if not os.path.exists(path):
        click.secho("❌ 路径不存在", fg="red")
        return
    count = 0
    for idx, file in enumerate(os.listdir(path)):
        old_path = os.path.join(path, file)
        if os.path.isfile(old_path):
            new_name = f"{prefix}_{idx+1}_{file}"
            new_path = os.path.join(path, new_name)
            os.rename(old_path, new_path)
            count += 1
    click.secho(f"✅ 批量重命名完成，共处理 {count} 个文件", fg="green")

if __name__ == "__main__":
    file_tool()
```
# 七、PyInstaller 打包部署工业级教程（依赖+配置专项精讲）
PyInstaller 是 Python 跨平台打包主流工具，可将脚本、依赖库、配置文件、静态资源打包为**单文件可执行程序**，支持 Windows/Linux/Mac 全平台，无需目标设备安装 Python 环境。本章重点攻克两大核心难点：**依赖缺失/冗余问题**、**配置文件打包失效/路径报错问题**，覆盖零基础使用到工程级优化全流程。
## 7.1 基础安装与核心原理
### 7.1.1 安装与版本校验
支持 Python3.7+，兼容本教程所有 CLI 项目，安装命令：
```Plain
pip install pyinstaller
# 校验安装成功
pyinstaller --version
```
### 7.1.2 打包核心原理
PyInstaller 通过**静态代码分析**扫描脚本依赖模块、封装 Python 解释器、整合项目资源，最终生成独立可执行文件。打包过程生成3个核心目录/文件：
- **build/**：打包临时缓存文件，可直接删除
- **dist/**：最终输出目录，存放可执行程序（核心输出）
- **.spec** 配置文件：打包工程配置文件，用于自定义依赖、资源、打包规则（复杂项目必备）
## 7.2 核心打包参数详解（精炼实用）
摒弃冗余参数，整理工业级高频参数，适配所有 CLI 工具打包场景：
```Plain
# 基础必用参数
-F, --onefile    打包为单个独立可执行文件（CLI工具首选，分发便捷）
-D, --onedir     打包为文件夹模式（启动更快，适合大型项目）
-n, --name       指定生成程序名称，默认沿用脚本名
-c, --console    保留终端控制台（CLI工具强制开启，默认开启）
-w, --window     关闭终端控制台（仅GUI工具使用，CLI禁止）

# 进阶核心参数（解决依赖/配置问题）
-p, --paths      指定自定义依赖模块搜索路径
--hidden-import  手动补充静态扫描遗漏的隐藏依赖
--collect-all    完整收集指定包所有子模块（解决动态导入报错）
--add-data       打包配置文件/静态资源（核心！解决配置丢失问题）
--distpath       指定程序输出目录，默认dist/
--specpath       指定spec配置文件生成目录
```
## 7.3 重点一：依赖深度处理（根治打包报错）
PyInstaller 静态分析存在缺陷，无法识别**动态导入、隐式子模块、第三方包内嵌依赖**，易出现 ModuleNotFoundError，以下是分层级解决方案。
### 7.3.1 依赖问题核心场景
- 常规第三方库（click/typer/fire）：可自动扫描识别，无需手动处理
- 动态导入模块、子模块（如 openpyxl、pandas 子依赖）：静态扫描遗漏，需手动补全
- 自定义本地模块、非标准路径依赖：需指定搜索路径
### 7.3.2 分层解决方案（从简到繁）
#### 1. 补充隐藏依赖（--hidden-import）
适用于单个缺失子模块，精准补充依赖，不冗余打包：
```Plain
# 格式：--hidden-import=模块名
pyinstaller -F main.py --hidden-import=yaml --hidden-import=click._compat
```
#### 2. 批量收集全量依赖（--collect-all）
适用于复杂第三方库（数据分析、运维工具），完整收集包内所有子模块，彻底解决依赖缺失：
```Plain
pyinstaller -F main.py --collect-all=pandas --collect-all=pyyaml
```
#### 3. 指定自定义依赖路径（-p）
适用于项目存在本地自定义模块、第三方私有库场景：
```Plain
# 追加自定义模块搜索路径
pyinstaller -F main.py -p ./libs -p ./modules
```
### 7.3.3 纯净环境打包（极致精简依赖）
默认打包会带入系统全部第三方库，导致程序体积臃肿、版本冲突。工业级最佳实践：**虚拟环境隔离打包**，仅安装项目必需依赖：
```Plain
# 1. 创建虚拟环境
python -m venv build_env
# 2. 激活环境
# Windows
build_env\Scripts\activate
# Linux/Mac
source build_env/bin/activate
# 3. 仅安装项目所需依赖
pip install click pyyaml typer fire
# 4. 执行打包（无冗余依赖）
pyinstaller -F main.py
```
## 7.4 重点二：配置文件完整处理方案（核心重难点）
打包后**相对路径失效、配置文件找不到、读取为空**是最高频问题，根源是：打包后程序运行在系统临时目录（`sys._MEIPASS`），原始项目路径失效。本节提供两种工业级解决方案，适配不同业务场景。
### 7.4.1 通用路径兼容函数（必写基础代码）
适配开发环境 & 打包后环境，自动识别资源路径，彻底解决路径报错，所有带配置的项目必须引入：
```python
import os
import sys

def get_resource_path(relative_path: str) -> str:
    """
    兼容开发环境/打包环境的资源路径获取函数
    :param relative_path: 配置文件相对路径
    :return: 绝对路径
    """
    if hasattr(sys, "_MEIPASS"):
        # 打包后：读取临时解压目录资源
        base_path = sys._MEIPASS
    else:
        # 开发时：读取当前项目目录资源
        base_path = os.path.abspath(".")
    return os.path.join(base_path, relative_path)

# 调用示例：加载yaml配置文件
config_path = get_resource_path("cli_config.yaml")
print(config_path)
```
### 7.4.2 方案一：配置文件内嵌打包（适合固定配置）
将配置文件直接打包进可执行程序，用户不可修改，适合固定参数、默认配置场景。通过 `--add-data` 参数实现，跨平台语法有差异：
#### Windows 打包命令
```Plain
# 格式：--add-data "源路径;目标路径"
pyinstaller -F main.py --add-data "cli_config.yaml;."
```
#### Linux/Mac 打包命令
```Plain
# 格式：--add-data "源路径:目标路径"
pyinstaller -F main.py --add-data "cli_config.yaml:."
```
参数说明：`.` 代表打包后放置在程序根目录，与代码读取路径对应。
### 7.4.3 方案二：配置文件外置独立（适合可修改配置）
将配置文件与可执行程序分离，用户可自由修改配置、无需重新打包，是**运维工具、商用CLI工具首选方案**。
实现逻辑：不打包配置文件，程序优先读取**可执行程序同级目录**的配置文件，无配置则加载默认内置配置。无需复杂打包参数，仅需优化路径读取逻辑。
### 7.4.4 高阶：spec 文件统一托管配置与依赖
多配置、多依赖的复杂项目，推荐使用自动生成的 `.spec` 文件固化打包规则，无需每次输入冗长命令：
1. 首次执行任意打包命令，生成 spec 文件
2. 编辑 spec 文件，添加资源、依赖配置
3. 后续直接通过 spec 打包，规则永久生效
```Plain
# 通过spec文件打包（优先读取配置）
pyinstaller main.spec
```
## 7.5 完整生产级打包命令（CLI工具专用）
整合依赖补全、配置打包、参数优化，适配本教程所有文件处理、计算类CLI工具：
```Plain
# Windows 生产打包命令
pyinstaller -F -c -n file_tool main.py ^
--hidden-import=yaml ^
--collect-all=click ^
--add-data "cli_config.yaml;."

# 参数释义
# -F：单文件打包 -c：保留控制台（CLI必备） -n：自定义程序名
# 补全yaml、click隐藏依赖
# 内嵌配置文件，打包后正常读取
```
## 7.6 打包优化：体积压缩与性能提升
- **虚拟环境精简**：仅安装项目必需依赖，杜绝全局冗余包
- **UPX 压缩**：安装 UPX 工具，自动压缩程序体积，最高压缩50%体积
- **剔除无用资源**：打包前删除缓存、日志、无用依赖文件
打包完成后，`dist` 目录下生成最终可执行程序：Windows 为 `.exe` 文件，Linux/Mac 为二进制可执行文件，可直接在无 Python 环境的设备运行，配置文件、依赖均正常加载。
## 7.7 系统全局命令部署
将打包后的程序配置为系统全局命令，实现任意终端直接调用，媲美系统原生 CLI 工具。
### Windows 部署
将 exe 文件所在目录添加到系统 **环境变量 PATH**，重启终端即可全局调用。
### Linux/Mac 部署
将二进制程序移动到系统全局目录，赋予执行权限：
```Plain
sudo mv file_tool /usr/local/bin/
sudo chmod +x /usr/local/bin/file_tool
# 任意终端直接调用
file_tool --help
```
## 7.8 高频报错与避坑指南
- **配置文件读取为空**：未使用 `sys._MEIPASS` 路径适配，必须引入通用资源路径函数
- **模块缺失报错**：动态子模块未收集，使用 `--collect-all` 批量收集依赖
- **程序体积过大**：未使用虚拟环境，全局依赖冗余，优先隔离环境打包
- **跨平台配置失效**：`--add-data` 分隔符混用，Windows 用 `;`，Linux/Mac 用 `:`
- **CLI 程序无输出**：错误使用 `-w` 关闭控制台，CLI 工具必须保留 `-c` 参数
# 八、库选型与最佳实践
## 8.1 三大主流库选型方案
- **极简脚本、快速原型**：优先 Fire（零配置、一行代码生效）
- **企业级工具、功能复杂**：优先 Click（生态成熟、功能全面、稳定性高）
- **现代化项目、类型严格**：优先 Typer（类型注解驱动、开发效率最高）
- **零依赖需求、轻量化场景**：使用原生 argparse

## 8.2 开发最佳实践
1. **参数规范**：必传参数强制校验，可选参数设置合理默认值，增加参数说明
2. **交互体验**：使用彩色输出、进度条、友好报错，避免原生堆栈信息暴露
3. **配置解耦**：固定配置写入 yaml/json 文件，不硬编码在代码中
4. **异常处理**：所有IO、运算逻辑增加异常捕获，保证程序健壮性
5. **模块化开发**：复杂工具拆分命令模块、工具函数、配置模块，避免代码堆砌
6. **文档完善**：利用库原生能力生成帮助文档，标注命令、参数、使用示例
## 8.3 常见避坑指南
- 避免使用 sys.argv 开发复杂 CLI，参数管理混乱、维护成本极高
- 打包时排除无关依赖，减小程序体积
- 跨平台开发注意路径兼容（使用 os.path / pathlib）
- CLI 工具禁止使用阻塞式弹窗，全程终端交互
# 九、教程总结
本教程覆盖 Python CLI 开发全链路：从原生零依赖实现、标准库基础开发、主流三方库高效实战，到高阶功能、工程化项目、打包部署与行业最佳实践，适配**新手入门、项目开发、企业级落地**全场景。
核心核心：简单场景轻量化实现，复杂场景优先 Click/Typer 模块化开发，严格遵循参数校验、异常处理、配置解耦的工程化规范，即可开发出专业、稳定、易用的 Python 命令行工具。