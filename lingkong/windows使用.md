---
layout: 主页
date: 2026-05-16T08:00:00
banner_y: 0.0
title: 主页
banner: "[[lingkong.png]]"
---

--------
windows10强开hyper-v
```powershell
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
pause
```
**保存并运行**：将文件另存为 `.bat` 后缀（如 `HyperV_Install.bat`），右键点击该文件，选择“**以管理员身份运行**”
```powershell
bcdedit /set hypervisorlaunchtype auto #打开hyper-v
bcdedit /set hypervisorlaunchtype off #关闭hyper-v

```