# Tkinter 完整精炼教程
Tkinter 是 Python 官方内置的跨平台 GUI 库，无需额外安装，轻量易用，适合快速开发桌面应用。本教程从基础到进阶全覆盖，所有代码可直接运行。

## 一、基础入门
### 1.1 环境准备
Python 3.x 自带 Tkinter，标准导入方式：
```python
import tkinter as tk  # 核心模块
from tkinter import ttk  # 现代化美化组件集（推荐优先使用）
```

验证安装：
```python
import tkinter
tkinter._test()
```

### 1.2 程序基础结构
所有 Tkinter 程序的固定框架：
```python
import tkinter as tk

# 1. 创建主窗口（根容器）
root = tk.Tk()

# 2. 窗口配置
root.title("第一个程序")

# 3. 添加组件
label = tk.Label(root, text="Hello Tkinter!")
label.pack()  # 布局渲染

# 4. 启动事件循环（必须，阻塞运行，监听所有用户操作）
root.mainloop()
```

### 1.3 主窗口常用配置
```python
root = tk.Tk()

# 窗口大小与位置：宽x高+左偏移+上偏移
root.geometry("400x300+200+100")

# 禁止调整窗口大小
root.resizable(width=False, height=False)

# 背景颜色
root.configure(bg="#f0f0f0")

# 窗口居中通用方法
def center_window(win, w, h):
    sw = win.winfo_screenwidth()
    sh = win.winfo_screenheight()
    x = (sw - w) // 2
    y = (sh - h) // 2
    win.geometry(f"{w}x{h}+{x}+{y}")

center_window(root, 400, 300)
```

## 二、布局管理器（核心）
同一父容器内**只能使用一种布局方式**，不可混用。

### 2.1 pack 布局
按顺序堆叠排列，适合简单垂直/水平布局，上手最快。

**核心参数**：
- `side`：停靠方向 `tk.TOP`(默认)/`tk.BOTTOM`/`tk.LEFT`/`tk.RIGHT`
- `fill`：填充方向 `tk.NONE`/`tk.X`/`tk.Y`/`tk.BOTH`
- `expand`：是否占用剩余空间 `True`/`False`
- `padx/pady`：组件外部间距；`ipadx/ipady`：组件内部间距
- `anchor`：对齐方式，用方位缩写 `n/s/e/w/center`

示例：
```python
tk.Button(root, text="按钮1").pack(side=tk.LEFT, padx=5)
tk.Button(root, text="按钮2").pack(side=tk.LEFT, padx=5)
tk.Button(root, text="按钮3").pack(fill=tk.X, pady=10)
```

### 2.2 grid 布局（最推荐）
表格网格布局，精准控制行列，适合复杂表单类界面。

**核心参数**：
- `row` / `column`：行号/列号（从0开始）
- `sticky`：单元格内对齐方式 `n/s/e/w`，`nsew` 代表撑满单元格
- `padx/pady`：单元格外边距
- `rowspan` / `columnspan`：跨行/跨列数

**缩放适配**：用 `rowconfigure` / `columnconfigure` 设置权重，控制窗口拉伸时的比例。

示例：登录表单
```python
tk.Label(root, text="用户名:").grid(row=0, column=0, padx=5, pady=10, sticky="e")
tk.Entry(root).grid(row=0, column=1, padx=5, pady=10, sticky="ew")

tk.Label(root, text="密  码:").grid(row=1, column=0, padx=5, pady=10, sticky="e")
tk.Entry(root, show="*").grid(row=1, column=1, padx=5, pady=10, sticky="ew")

tk.Button(root, text="登录").grid(row=2, column=0, columnspan=2, pady=10)

# 让第二列随窗口拉伸
root.columnconfigure(1, weight=1)
```

### 2.3 place 布局
绝对/相对定位，自由度最高但适配性差，仅适合固定布局场景。

**核心参数**：
- `x/y`：绝对像素坐标
- `relx/rely`：相对坐标（0~1，相对于父容器宽高比例）
- `width/height`：绝对宽高；`relwidth/relheight`：相对宽高

示例：
```python
tk.Button(root, text="绝对定位").place(x=50, y=50, width=100, height=30)
tk.Button(root, text="居中").place(relx=0.5, rely=0.5, anchor="center")
```

## 三、常用组件详解
所有组件第一个参数为**父容器**，后续为关键字参数。优先使用 `ttk` 组件，样式更统一美观。

### 3.1 基础交互组件
#### Label 标签
显示文本或图片，不可编辑。
**核心参数**：
- `text`：静态文本；`textvariable`：绑定可变变量，动态更新
- `font`：字体格式 `("字体名", 字号, 样式)`，如 `("微软雅黑", 12, "bold")`
- `bg/fg`：背景色/前景色；`image`：显示图片

> 注意：`PhotoImage` 加载的图片必须保持引用，否则会被垃圾回收导致图片不显示，需设为全局变量或组件属性。

```python
tk.Label(root, text="普通标签", fg="red", font=("微软雅黑", 12)).pack()
```

#### Button 按钮
点击触发回调。
**核心参数**：
- `command`：点击回调函数（无参）
- `state`：状态 `tk.NORMAL`(正常)/`tk.DISABLED`(禁用)

```python
def click():
    print("被点击")

tk.Button(root, text="点击我", command=click, width=10).pack(pady=5)
```

#### Entry 单行输入框
单行文本输入，适合账号、密码等短文本。
**核心参数**：
- `show`：掩码字符，密码框设为 `"*"`
- `textvariable`：绑定可变变量

**常用方法**：
- `.get()`：获取输入内容
- `.insert(tk.END, 文本)`：末尾插入内容
- `.delete(0, tk.END)`：清空输入框

```python
entry = tk.Entry(root)
entry.pack()
entry.insert(0, "默认内容")
```

#### Text 多行文本框
支持多行文本、富文本格式，适合大段内容编辑。
**索引规则**：格式为 `"行号.列号"`，如 `"1.0"` 代表第1行第0列（开头），`tk.END` 代表末尾。

**常用方法**：
- `.insert("1.0", 文本)`：开头插入
- `.get("1.0", tk.END)`：获取全部文本
- `.delete("1.0", tk.END)`：清空所有内容
- `.tag_config()`：配置文本样式标签，实现局部字体、颜色设置

```python
text = tk.Text(root, height=5, width=30)
text.pack()
text.insert("1.0", "第一行\n第二行")
```

### 3.2 选择类组件
#### Checkbutton 复选框
多选开关，绑定 `IntVar`/`BooleanVar`，选中为1，未选为0。
```python
var1 = tk.IntVar()
tk.Checkbutton(root, text="选项A", variable=var1).pack(anchor="w")
```

#### Radiobutton 单选框
一组选项互斥，同一组绑定同一个变量，通过 `value` 区分选项。
```python
gender = tk.StringVar(value="男")
tk.Radiobutton(root, text="男", variable=gender, value="男").pack(anchor="w")
tk.Radiobutton(root, text="女", variable=gender, value="女").pack(anchor="w")
```

#### ttk.Combobox 下拉框
下拉选择，支持输入。
```python
combo = ttk.Combobox(root, values=["选项1", "选项2", "选项3"])
combo.pack()
combo.current(0)  # 默认选中第一个
# 获取值：combo.get()
```

### 3.3 容器组件
用于分组嵌套，实现复杂布局。

#### Frame 框架
基础透明容器，用于布局拆分。
```python
# 左右分栏
left = tk.Frame(root, bg="gray", width=200)
left.pack(side=tk.LEFT, fill=tk.Y)
right = tk.Frame(root, bg="white")
right.pack(side=tk.RIGHT, fill=tk.BOTH, expand=True)
```

#### LabelFrame 带标签框架
带标题边框的容器，用于分组显示。
```python
lf = tk.LabelFrame(root, text="用户信息", padx=10, pady=10)
lf.pack(padx=10, pady=10, fill="x")
tk.Label(lf, text="姓名:").pack(side=tk.LEFT)
```

#### ttk.Notebook 标签页
多标签切换容器。
```python
notebook = ttk.Notebook(root)
notebook.pack(fill=tk.BOTH, expand=True)

tab1 = tk.Frame(notebook)
tab2 = tk.Frame(notebook)
notebook.add(tab1, text="标签1")
notebook.add(tab2, text="标签2")
```

### 3.4 其他常用组件
#### Scale 滑块
数值调节控件。
```python
scale = tk.Scale(root, from_=0, to=100, orient=tk.HORIZONTAL)
scale.pack()
```

#### ttk.Progressbar 进度条
**两种模式**：`determinate` 确定进度；`indeterminate` 不确定加载动画。
```python
pb = ttk.Progressbar(root, length=200, mode="determinate")
pb.pack()
pb["value"] = 50  # 设置进度
```

#### Listbox 列表框
列表选择控件。
```python
lb = tk.Listbox(root)
lb.pack()
for item in ["条目1", "条目2", "条目3"]:
    lb.insert(tk.END, item)
```

#### ttk.Treeview 树形表格
实现树形结构和表格展示，类似 Excel。
```python
columns = ("id", "name", "age")
tree = ttk.Treeview(root, columns=columns, show="headings")
# 设置表头
tree.heading("id", text="ID")
tree.heading("name", text="姓名")
tree.heading("age", text="年龄")
# 插入数据
tree.insert("", tk.END, values=(1, "张三", 20))
tree.pack(fill=tk.BOTH, expand=True)
```

## 四、事件与绑定
### 4.1 command 简单回调
按钮、菜单等组件的 `command` 参数，仅支持点击事件，无法直接传参。如需传参用 `lambda` 包装：
```python
def show_msg(msg):
    print(msg)

tk.Button(root, text="点击", command=lambda: show_msg("你好")).pack()
```

### 4.2 bind 通用事件绑定
可绑定任意鼠标、键盘、窗口事件，语法：`组件.bind("<事件类型>", 回调函数)`，回调自动接收**事件对象**参数。

```python
def on_click(event):
    print(f"点击坐标：{event.x}, {event.y}")

root.bind("<Button-1>", on_click)  # 绑定左键点击
```

### 4.3 常见事件类型
| 事件格式 | 说明 |
|---------|------|
| `<Button-1>` | 鼠标左键按下（2中键，3右键） |
| `<B1-Motion>` | 鼠标左键拖动 |
| `<Enter>` / `<Leave>` | 鼠标移入/移出组件 |
| `<KeyPress-a>` | 按下键盘 a 键 |
| `<Return>` | 按下回车键 |
| `<Configure>` | 窗口大小改变 |

### 4.4 事件对象常用属性
- `event.x` / `event.y`：鼠标相对于组件的坐标
- `event.keysym`：按下的按键名称
- `event.widget`：触发事件的组件实例

## 五、可变变量（双向绑定）
Tkinter 提供特殊变量类，实现**数据与UI的双向同步**：修改变量时界面自动更新，界面输入变化时变量自动同步。

### 5.1 变量类型
- `tk.StringVar()`：字符串
- `tk.IntVar()`：整数
- `tk.DoubleVar()`：浮点数
- `tk.BooleanVar()`：布尔值

> 注意：可变变量必须在创建主窗口 `Tk()` 之后实例化。

### 5.2 使用示例
```python
text_var = tk.StringVar(value="初始文本")

# 绑定到输入框和标签
tk.Entry(root, textvariable=text_var).pack()
tk.Label(root, textvariable=text_var).pack()

# 修改变量，两个组件自动更新
text_var.set("新文本")
# 获取变量值
print(text_var.get())
```

## 六、进阶功能
### 6.1 标准对话框
#### 消息框
```python
from tkinter import messagebox

messagebox.showinfo("提示", "操作成功")
messagebox.showerror("错误", "出错了")
# 确认框，返回 True/False
result = messagebox.askyesno("确认", "确定删除？")
```

#### 文件对话框
```python
from tkinter import filedialog

# 打开文件，返回文件路径
path = filedialog.askopenfilename(filetypes=[("文本文件", "*.txt")])
# 保存文件
path = filedialog.asksaveasfilename(defaultextension=".txt")
# 选择文件夹
dir_path = filedialog.askdirectory()
```

#### 颜色选择框
```python
from tkinter import colorchooser
color = colorchooser.askcolor()  # 返回 (RGB元组, 十六进制色值)
```

### 6.2 菜单系统
#### 顶部菜单栏
```python
menubar = tk.Menu(root)

# 文件菜单
file_menu = tk.Menu(menubar, tearoff=0)
file_menu.add_command(label="打开", command=lambda: print("打开"))
file_menu.add_separator()
file_menu.add_command(label="退出", command=root.quit)

menubar.add_cascade(label="文件", menu=file_menu)
root.config(menu=menubar)
```

#### 右键弹出菜单
```python
popup = tk.Menu(root, tearoff=0)
popup.add_command(label="复制")

def show_popup(event):
    popup.post(event.x_root, event.y_root)

root.bind("<Button-3>", show_popup)
```

### 6.3 Canvas 画布绘图
用于绘制图形、自定义控件、显示图片。坐标系统：左上角为(0,0)，x向右、y向下递增。

```python
canvas = tk.Canvas(root, width=400, height=300, bg="white")
canvas.pack()

canvas.create_line(0, 0, 400, 300, fill="red", width=2)  # 直线
canvas.create_rectangle(50, 50, 150, 150, fill="blue")  # 矩形
canvas.create_oval(200, 50, 300, 150, fill="green")     # 椭圆/圆
canvas.create_text(200, 200, text="画布文本")            # 文本
```

### 6.4 多线程防卡顿
Tkinter 是**单线程模型**，UI 操作必须在主线程执行。耗时任务会阻塞界面，需放在子线程中运行，UI 更新通过 `after` 方法投递到主线程。

```python
import threading
import time

def long_task():
    for i in range(5):
        time.sleep(1)
        # 通过after将UI更新投递到主线程
        root.after(0, lambda v=i: label.config(text=f"进度: {v+1}/5"))

def start():
    threading.Thread(target=long_task, daemon=True).start()

label = tk.Label(root, text="未开始")
label.pack(pady=10)
tk.Button(root, text="开始任务", command=start).pack()
```

## 七、综合实战：简易记事本
```python
import tkinter as tk
from tkinter import filedialog, messagebox

class Notepad:
    def __init__(self, root):
        self.root = root
        self.root.title("简易记事本")
        self.root.geometry("600x400")
        self.current_file = None
        
        self._create_menu()
        self._create_text()
        self._create_statusbar()
    
    def _create_menu(self):
        menubar = tk.Menu(self.root)
        file_menu = tk.Menu(menubar, tearoff=0)
        file_menu.add_command(label="新建", command=self.new_file)
        file_menu.add_command(label="打开", command=self.open_file)
        file_menu.add_command(label="保存", command=self.save_file)
        file_menu.add_separator()
        file_menu.add_command(label="退出", command=self.root.quit)
        menubar.add_cascade(label="文件", menu=file_menu)
        self.root.config(menu=menubar)
    
    def _create_text(self):
        self.text = tk.Text(self.root, undo=True)
        self.text.pack(fill=tk.BOTH, expand=True)
    
    def _create_statusbar(self):
        self.status = tk.Label(self.root, text="就绪", anchor="w", bd=1, relief="sunken")
        self.status.pack(side=tk.BOTTOM, fill=tk.X)
    
    def new_file(self):
        self.text.delete("1.0", tk.END)
        self.current_file = None
    
    def open_file(self):
        path = filedialog.askopenfilename(filetypes=[("文本文件", "*.txt")])
        if not path:
            return
        with open(path, "r", encoding="utf-8") as f:
            self.text.delete("1.0", tk.END)
            self.text.insert("1.0", f.read())
        self.current_file = path
    
    def save_file(self):
        if not self.current_file:
            path = filedialog.asksaveasfilename(defaultextension=".txt")
            if not path:
                return
            self.current_file = path
        with open(self.current_file, "w", encoding="utf-8") as f:
            f.write(self.text.get("1.0", tk.END))
        messagebox.showinfo("提示", "保存成功")

if __name__ == "__main__":
    root = tk.Tk()
    app = Notepad(root)
    root.mainloop()
```
## 八、主题美化
Tkinter 原生样式偏老旧，可通过原生主题切换、自定义样式、第三方主题库三种方式美化，推荐优先使用 `ttkbootstrap` 实现现代化界面。

### 8.1 原生 ttk 主题切换
ttk 自带多套跨平台主题，无需额外安装，可快速切换整体风格。

```python
from tkinter import ttk

style = ttk.Style()
# 查看当前系统可用主题
print(style.theme_names())
# 切换主题，推荐跨平台通用的 clam 主题
style.theme_use("clam")
```

**常见主题说明**：
- `clam`：跨平台通用，质感优于默认，是自定义样式的基础
- `vista`：Windows 系统专属，适配原生系统风格
- `aqua`：macOS 系统专属
- `alt` / `default` / `classic`：经典样式，兼容性最强

### 8.2 ttkbootstrap 现代化主题（推荐）
基于 Bootstrap 设计风格的第三方主题库，开箱即用，支持明暗主题、多种配色，是目前 Tkinter 美化的主流方案。

#### 安装
```bash
pip install ttkbootstrap
```

#### 基础使用
```python
import ttkbootstrap as ttk
from ttkbootstrap.constants import *

# 创建窗口并指定主题，如暗黑主题 darkly
root = ttk.Window(themename="darkly")
root.geometry("400x300")
root.title("美化示例")

# 组件用法和标准 ttk 一致，新增 bootstyle 参数控制样式
ttk.Label(root, text="登录系统", font=("微软雅黑", 16)).pack(pady=20)
ttk.Entry(root, width=30).pack(pady=5)
ttk.Button(root, text="登录", bootstyle=PRIMARY, width=20).pack(pady=15)
ttk.Button(root, text="取消", bootstyle=(SECONDARY, OUTLINE), width=20).pack()

root.mainloop()
```

**常用配置**：
- **主题名**：亮色主题 `cosmo`/`flatly`/`litera`；暗色主题 `darkly`/`cyborg`/`superhero`
- **样式类型**：`PRIMARY`/`SUCCESS`/`DANGER`/`WARNING`/`INFO` 等配色
- **轮廓样式**：搭配 `OUTLINE` 实现线框效果，如 `(PRIMARY, OUTLINE)`

### 8.3 自定义 ttk 组件样式
无需第三方库，通过 `ttk.Style` 可精细化定制组件外观，支持全局样式和自定义命名样式。

#### 基础自定义
```python
style = ttk.Style()
# 格式：样式名.组件类型，组件类型必须正确（TButton/TLabel/TEntry等）
style.configure(
    "Accent.TButton",
    foreground="white",
    background="#2563eb",
    font=("微软雅黑", 10, "bold"),
    padding=8
)

# 使用自定义样式
ttk.Button(root, text="自定义按钮", style="Accent.TButton").pack()
```

#### 状态交互样式
通过 `map` 方法配置悬停、按下、禁用等不同状态的样式：
```python
style.map(
    "Accent.TButton",
    background=[("active", "#1d4ed8"), ("pressed", "#1e40af"), ("disabled", "#9ca3af")],
    foreground=[("disabled", "#e5e7eb")]
)
```

## 九、打包为 EXE 可执行文件
使用 `PyInstaller` 将 Python 脚本打包为独立 exe 文件，无需 Python 环境即可在 Windows 运行。

### 9.1 环境准备
```bash
pip install pyinstaller
```

### 9.2 基础打包命令
Tkinter GUI 程序推荐使用以下基础命令：
```bash
pyinstaller --onefile --windowed --clean --name "我的程序" main.py
```

**核心参数说明**：
| 参数 | 作用 |
|------|------|
| `--onefile` | 打包为单个 exe 文件，便于分发 |
| `--windowed` / `--noconsole` | 隐藏控制台黑窗口，GUI 程序必须添加 |
| `--name xxx` | 指定生成的 exe 文件名 |
| `--icon=app.ico` | 设置程序图标（必须为 ico 格式） |
| `--clean` | 打包前清理临时缓存文件 |

执行完成后，生成的 exe 文件位于项目目录的 `dist` 文件夹内。

### 9.3 体积优化方案
1. **纯净虚拟环境打包**
   在仅安装项目依赖的虚拟环境中打包，避免将多余的第三方库打包进 exe，是减体积最有效的方式。

2. **排除无用模块**
   通过 `--exclude-module` 排除不需要的内置模块：
   ```bash
   pyinstaller --onefile --windowed --exclude-module tkinter.test --exclude-module unittest main.py
   ```

3. **UPX 压缩**
   下载 UPX 压缩工具，打包时指定路径，可压缩 30% 左右体积：
   ```bash
   pyinstaller --onefile --windowed --upx-dir "upx工具目录" main.py
   ```

### 9.4 常见问题与解决方案
#### 1. 外部资源路径失效
图片、配置文件等外部资源，打包后运行时会解压到临时目录，直接用相对路径会找不到文件。需封装路径适配函数：
```python
import os
import sys

def resource_path(relative_path):
    """适配开发环境与打包后的资源路径"""
    if hasattr(sys, "_MEIPASS"):
        # 打包后运行：资源解压到临时目录 _MEIPASS
        base_path = sys._MEIPASS
    else:
        # 开发环境：当前脚本目录
        base_path = os.path.abspath(".")
    return os.path.join(base_path, relative_path)
```

使用示例：
```python
# 加载图片
img = tk.PhotoImage(file=resource_path("logo.png"))
```

打包时需同步添加资源文件：
```bash
# Windows 用分号分隔，Linux/macOS 用冒号
pyinstaller --onefile --windowed --add-data "logo.png;." main.py
```

#### 2. 杀毒软件误报
单文件打包的 exe 易被杀毒软件误报，可去掉 `--onefile` 参数，以目录形式分发，误报概率大幅降低。

#### 3. 中文乱码
确保代码文件保存为 UTF-8 编码；若仍有乱码，可在代码开头显式设置系统默认编码。

