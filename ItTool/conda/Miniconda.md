# Miniconda 完整配置教程（2023 年 10 月 1 日）

### Miniconda 完整配置教程（新手友好·全平台通用）

配置核心围绕「**验证安装 → 配置国内镜像源（加速下载）→ 环境/包管理基础**」展开，以下是分步骤操作：

---

## 一、第一步：验证安装是否成功

安装完成后，先确认 Conda 能正常调用：

### 1. Windows 系统

打开 **开始菜单** → 找到并点击 **Anaconda Prompt (miniconda3)**（推荐用这个，避免环境变量问题），输入：

```Bash

conda --version
```

若显示版本号（如 `conda 26.1.1`），则安装成功。

### 2. macOS / Linux 系统

直接打开 **终端 (Terminal)**，输入：

```Bash

conda --version
```

若显示版本号则成功。

---

## 二、核心配置：换国内镜像源（必做，否则下载极慢）

官方源在国内访问慢，推荐换 **清华源** 或 **阿里源**，以下是**一键命令行配置法**（无需手动改文件）：

### 1. 生成基础配置文件

在终端/Anaconda Prompt 运行：

```Bash

conda config --set show_channel_urls yes
```

### 2. 配置清华源（推荐，稳定）

复制以下命令**全部一起运行**：

```Bash

conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
conda config --set show_channel_urls yes
```

### 3. （备选）配置阿里源

如果清华源慢，换成阿里源：

```Bash

conda config --add channels https://mirrors.aliyun.com/anaconda/pkgs/main
conda config --add channels https://mirrors.aliyun.com/anaconda/pkgs/r
conda config --add channels https://mirrors.aliyun.com/anaconda/pkgs/msys2
conda config --add channels https://mirrors.aliyun.com/anaconda/cloud/conda-forge
conda config --set show_channel_urls yes
```

### 4. 验证换源是否成功

运行：

```Bash

conda config --show channels
```

若输出里显示 `tsinghua.edu.cn` 或 `aliyun.com` 的链接，说明换源成功。

---

## 三、日常使用：环境与包管理（核心功能）

Conda 的核心优势是**隔离环境**，不同项目用不同环境，避免包冲突。

### 1. 环境管理命令

|功能|命令|示例|
|---|---|---|
|创建环境（指定 Python 版本）|`conda create -n 环境名 python=3.11 -y`|`conda create -n myai python=3.11 -y`|
|激活环境|`conda activate 环境名`|`conda activate myai`|
|退出当前环境|`conda deactivate`|-|
|查看所有环境|`conda env list`|-|
|删除环境（谨慎）|`conda remove -n 环境名 --all -y`|`conda remove -n myai --all -y`|
### 2. 包管理命令（需先激活环境）

|功能|命令|示例|
|---|---|---|
|安装包|`conda install 包名 -y` 或 `pip install 包名`|`conda install pandas -y`|
|安装指定版本|`conda install 包名=版本号 -y`|`conda install numpy=1.26 -y`|
|查看已安装包|`conda list`|-|
|搜索包|`conda search 包名`|`conda search torch`|
|删除包|`conda remove 包名 -y`|`conda remove pandas -y`|
|更新包|`conda update 包名 -y`|`conda update pandas -y`|
|更新 Conda 自身|`conda update conda -y`|-|
---

## 四、（进阶）配置 VSCode 关联 Conda 环境

如果你用 VSCode 写 Python，需要让 VSCode 识别 Conda 环境：

1. 在 VSCode 安装 **Python 扩展**（Microsoft 官方的）。

2. 打开项目文件夹，按 `Ctrl+Shift+P`（Windows）或 `Cmd+Shift+P`（Mac），输入 `Python: Select Interpreter`。

3. 在列表里选择你创建的 Conda 环境（如 `myai`）即可。

---

## 五、常见问题解决

1. **Windows 提示“conda 不是内部命令”**：

不要用系统自带的 CMD，用开始菜单里的 **Anaconda Prompt**。

1. **下载包仍然慢**：

尝试换另一个镜像源（清华→阿里），或检查网络连接。

1. **创建环境失败**：

检查 Python 版本是否存在（如 `python=3.9` 是存在的，`python=3.88` 不存在），或换个环境名试试。

# Miniconda 配置进 Windows 系统环境变量教程

### 把 Miniconda 配置进系统环境变量教程（Windows 专用·无 Python 冲突版）

因为你电脑上没有其他 Python，直接配置进环境变量**不会有冲突**，以后在任意 CMD/PowerShell 都能直接用 `conda` 和 `python` 命令。

---

## 一、第一步：找到 Miniconda 的安装路径

1. 打开文件资源管理器，进入以下路径（**替换“你的用户名”为你电脑的实际用户名**）：

    ```Plain Text
    
    C:\Users\你的用户名\miniconda3
    ```

    - 比如你的用户名是 `o'k`，路径就是 `C:\Users\o'k\miniconda3`

    - 如果你改了安装位置，就去你自定义的路径找。

2. 进入 `miniconda3` 文件夹，找到并**复制以下三个文件夹的完整路径**（后面要用）：

    - `Scripts`（最核心，必须加）

    - `Library\bin`（推荐加，避免某些库报错）

    - （可选）根目录 `miniconda3`

示例路径：

`    C:\Users\o'k\miniconda3\Scripts     C:\Users\o'k\miniconda3\Library\bin    `

---

## 二、第二步：打开系统环境变量设置

### 方法 1：通过“此电脑”打开（推荐）

1. 在桌面右键点击 **此电脑**（或“我的电脑”）→ 选择 **属性**。

2. 在弹出的窗口右侧/下方找到 **高级系统设置** 并点击。

3. 在弹出的“系统属性”窗口中，点击右下角的 **环境变量** 按钮。

### 方法 2：直接搜索（Win10/11）

1. 按键盘 `Win` 键，直接输入 **“环境变量”**。

2. 点击搜索结果里的 **“编辑系统环境变量”**。

---

## 三、第三步：编辑 Path 变量（核心步骤）

1. 在“环境变量”窗口的**上方用户变量**（只对你当前用户生效，推荐）或**下方系统变量**（对所有用户生效）里，找到名为 **`Path`** 的变量，选中它，点击 **编辑**。

2. 在弹出的“编辑环境变量”窗口中，点击右侧的 **新建**，然后**依次粘贴第一步复制的路径**：

    - 先粘贴：`C:\Users\你的用户名\miniconda3\Scripts`

    - 再点击 **新建**，粘贴：`C:\Users\你的用户名\miniconda3\Library\bin`

3. **（可选）调整顺序**：

点击刚添加的路径，用右侧的 **上移** 按钮把它们移到列表最顶部，确保系统优先调用 Miniconda 的 Python。

1. 点击所有窗口的 **确定** 保存（不要直接点叉，否则不生效）。

---

## 四、第四步：验证配置是否成功

1. **关闭所有已打开的 CMD/PowerShell 窗口**（必须重启终端才能读取新环境变量）。

2. 按 `Win+R`，输入 `cmd` 打开一个新的命令提示符。

3. 依次输入以下命令验证：

    ```Bash
    
    conda --version
    ```

    若显示版本号（如 `conda 26.1.1`），说明 Conda 配置成功。

`bash     python --version     `

若显示 Python 版本号（如 `Python 3.12.7`），说明 Python 配置成功。

`bash     where python     `

若输出的路径是你 Miniconda 的路径，说明系统默认使用的是 Miniconda 的 Python。

---

## 五、后续使用建议

配置完成后，你可以：

1. **直接在任意 CMD 里使用 Conda**，不用再打开“Anaconda Prompt”。

2. 创建一个常用的环境并设为默认（可选）：

    ```Bash
    
    # 创建一个叫 base_env 的环境（Python 3.12）
    conda create -n base_env python=3.12 -y
    # 激活它
    conda activate base_env
    ```

3. 记得先配置国内镜像源（参考上一条教程），否则下载包会很慢。

---

配置过程中如果找不到路径或保存后不生效，随时告诉我！
> （注：文档部分内容可能由 AI 生成）