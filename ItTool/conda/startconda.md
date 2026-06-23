## 配置国内镜像源（加速下载）
官方源在国内下载较慢，推荐换 清华源 或 阿里源。

1. 生成配置文件

在终端/CMD 运行：
```bash
conda config --set show_channel_urls yes
```
2. 编辑配置文件

- Windows：文件路径 C:\Users\你的用户名.condarc

- macOS/Linux：文件路径 ~/.condarc

用记事本/文本编辑器打开，替换为以下内容（清华源）：
```ini
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```
3. 验证换源

运行：
```bash
conda config --show channels
```
若显示清华源链接，则配置成功。


---
核心使用命令

1. 环境管理（最重要）

Conda 的核心优势是隔离环境，不同项目用不同环境，避免包冲突。

功能

命令

创建环境（指定 Python 版本）
```
conda create -n 环境名 python=3.11

激活环境

conda activate 环境名

退出当前环境

conda deactivate

查看所有环境

conda env list

删除环境（谨慎操作）

conda remove -n 环境名 --all
```
示例：创建一个叫 myai 的环境并激活：
```bash
conda create -n myai python=3.11 -y
conda activate myai
```

---
2. 包管理

功能

命令

安装包
```bash
conda install 包名 或 pip install 包名

安装指定版本

conda install numpy=1.26

查看已安装包

conda list

搜索包

conda search 包名

删除包

conda remove 包名

更新包

conda update 包名

更新 Conda 自身

conda update conda
```
示例：在 myai 环境中安装 pandas：
```bash
conda activate myai
conda install pandas -y
```

---
3. 环境导出与分享

将当前环境导出为配置文件，方便在其他电脑上复现：
```bash
# 导出环境
conda env export > environment.yml

# 从文件导入环境
conda env create -f environment.yml
```

---