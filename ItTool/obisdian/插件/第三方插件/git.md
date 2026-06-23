---
layout: 主页
date: 2026-05-16T08:00:00
banner_y: 0.0
title: 主页
banner: "[[lingkong.png]]"
---
--------

Obsidian Git 是最常用的 Obsidian 笔记版本控制与多设备同步工具，基于 Git 实现完整的版本历史追踪、异地同步和数据备份。

> 前置条件
> - Git 客户端[Git 官方网站](https://git-scm.com/downloads)
>> - 添加环境变量
> - 配置 Git 全局身份
> - 创建远程 Git 仓库
>>- `git config --global user.name "你的用户名"`
>>- `git config --global user.email "你的邮箱地址"`
> - 初始化本地 Obsidian 仓库
>>- `git init`
>>- ` git remote add origin 你的远程仓库地址`
>>- `git remote -v`

## 详细配置说明
### Automatic（自动同步模块）

| 选项                                                    | 作用                                                                                                     | 推荐设置                                |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------------------------ | ----------------------------------- |
| Split timers for automatic commit and sync            | 分开提交和推送的计时器。开启后可分别设置自动提交间隔和自动推送间隔；关闭则两者使用同一个间隔                                                         | ❌ 关闭（个人备份无需分开）                      |
| Auto commit-and-sync interval (minutes)               | 自动提交并同步的间隔，单位分钟。默认 0 表示完全禁用自动同步                                                                        | ✅ 设置为 **5-15 分钟**（不要太短，否则会产生大量提交记录） |
| Auto commit-and-sync after stopping file edits        | 停止编辑文件后才开始计时自动同步。可以避免在你编辑过程中自动提交                                                                       | ✅ 开启                                |
| Auto commit-and-sync after latest commit              | 手动提交后重置自动同步计时器。减少手动提交后的重复自动提交                                                                          | ✅ 开启                                |
| Auto push interval (minutes)                          | 自动推送间隔，仅在开启 Split timers 时有效                                                                           | ❌ 保持 0（禁用）                          |
| Auto pull interval (minutes)                          | 自动拉取远程修改的间隔，单位分钟                                                                                       | ✅ 多设备使用设置为 **15-30 分钟**；单设备使用保持 0   |
| Auto commit-and-sync only staged files                | 只自动提交已暂存的文件。关闭则自动提交所有修改的文件                                                                             | ❌ 关闭（无需手动暂存，所有修改自动备份）               |
| Specify custom commit message on auto commit-and-sync | 自动提交时弹出窗口让你输入自定义信息                                                                                     | ❌ 关闭（自动提交用默认信息即可）                   |
| Commit message on auto commit-and-sync                | 自动提交的默认信息模板。支持占位符：<br>`{{date}}` 日期<br>`{{hostname}}` 主机名<br>`{{numFiles}}` 修改文件数<br>`{{files}}` 修改文件名 | ✅ 保持你的设置：`vault backup: {{date}}`   |
### Commit message（提交信息模块）

| 选项 | 作用 | 推荐设置 |
|------|------|----------|
| Commit message on manual commit | 手动提交的默认信息模板 | ✅ 也设置为 `vault backup: {{date}}`，或留空让自己输入 |
| Commit message script | 使用 Shell 脚本生成提交信息 | ❌ 保持空（高级功能，一般不需要） |
| {{date}} placeholder format | 日期占位符的格式，使用 Moment.js 语法 | ✅ 保持你的设置：`YYYY-MM-DD HH:mm:ss`（精确到秒，方便追溯） |
| {{hostname}} placeholder replacement | 自定义主机名。多设备使用时可以在这里设置不同的名字（如"Desktop"、"Laptop"），方便区分提交来源 | ✅ 多设备使用建议设置；单设备保持空 |
| Preview commit message | 点击 Preview 按钮可以预览最终的提交信息效果 | 按需使用 |
| List filenames affected by commit in the commit body | 在提交信息的正文里列出所有修改的文件名 | ✅ 开启（在历史记录中可以快速看到这次提交改了什么） |
###  Pull（拉取模块）

| 选项                          | 作用                                                                                | 推荐设置                                 |
| --------------------------- | --------------------------------------------------------------------------------- | ------------------------------------ |
| Merge strategy              | 合并远程修改的策略：<br>- Merge：合并（默认，最安全）<br>- Rebase：变基<br>- Fast-forward only：只快进        | ✅ 保持默认 **Merge**                     |
| Merge strategy on conflicts | 冲突时的自动解决策略：<br>- None：不自动解决（Git 默认）<br>- Ours：保留本地修改，忽略远程<br>- Theirs：保留远程修改，忽略本地 | ✅ 保持默认 **None**（绝对不要自动解决冲突，否则可能丢失数据） |
| Pull on startup             | 启动 Obsidian 时自动拉取远程最新修改                                                           | ✅ 开启（多设备使用必备）                        |
###  Commit-and-sync（提交并同步模块）

| 选项 | 作用 | 推荐设置 |
|------|------|----------|
| Push on commit-and-sync | 提交并同步时执行推送操作。开启后流程是：提交 → 拉取 → 推送；关闭则只提交和拉取 | ✅ 开启（你的截图中已经开启） |
| Pull on commit-and-sync | 提交前先拉取远程修改。这是 Git 的最佳实践，可以提前发现冲突 | ✅ 开启（你的截图中已经开启） |
### Hunk management（块管理模块）
Hunk 指文件中一组连续的修改行。这部分控制在编辑器中直接查看和管理修改的功能。

| 选项 | 作用 | 推荐设置 |
|------|------|----------|
| Signs | 在编辑器行号旁边显示修改标记：<br>- 绿色：新增行<br>- 黄色：修改行<br>- 红色：删除行 | ✅ 开启（非常实用，直观看到修改位置） |
| Hunk commands | 添加块操作命令。可以通过命令面板暂存、重置、预览单个修改块 | ✅ 开启 |
| Status bar with summary of line changes | 在状态栏显示新增、修改、删除的行数统计 | ✅ 开启 |
### Line author information（行作者信息模块）

| 选项 | 作用 | 推荐设置 |
|------|------|----------|
| Show commit authoring information next to each line | 在每行代码旁边显示提交作者和时间 | ❌ 个人使用关闭（影响编辑器美观）；多人协作开启 |
### History view（历史视图模块）

| 选项 | 作用 | 推荐设置 |
|------|------|----------|
| Show Author | 在历史记录中显示提交作者 | ✅ 开启 |
| Show Date | 在历史记录中显示提交日期 | ✅ 开启 |
### Source control view（源码控制视图模块）

| 选项                                                        | 作用                    | 推荐设置            |
| --------------------------------------------------------- | --------------------- | --------------- |
| Automatically refresh source control view on file changes | 文件修改时自动刷新 Git 面板的修改列表 | ✅ 开启（你的截图中已经开启） |
| Source control view refresh interval                      | 刷新间隔，单位毫秒             | ✅ 保持默认 7000（7秒） |
###  Miscellaneous（杂项模块）

| 选项 | 作用 | 推荐设置 |
|------|------|----------|
| Diff view style | 差异对比视图风格：<br>- Split：分屏对比（左右显示修改前后）<br>- Inline：行内对比 | ✅ 保持默认 **Split**（更清晰） |
| Disable informative notifications | 禁用"提交成功"、"拉取成功"等信息通知 | ❌ 关闭（需要知道操作是否成功） |
| Disable error notifications | 禁用错误通知 | ❌ 绝对不要开启（出错了都不知道） |
| Hide notifications for no changes | 没有修改时隐藏通知（如自动提交时没有内容可提交） | ✅ 开启（避免不必要的打扰） |
| Show status bar | 显示 Git 状态栏 | ✅ 开启（你的截图中已经开启） |
| File menu integration | 在文件右键菜单中添加"暂存"、"取消暂存"、"添加到.gitignore"选项 | ✅ 开启（你的截图中已经开启） |
| Show branch status bar | 在状态栏显示当前分支名 | ✅ 开启（你的截图中已经开启） |
| Show the count of modified files in the status bar | 在状态栏显示未提交的修改文件数 | ✅ 开启 |
### Commit author（提交作者模块）

| 选项 | 作用 | 推荐设置 |
|------|------|----------|
| Author name for commit | 本仓库专用的提交作者名，会覆盖全局 Git 配置 | ✅ 保持空（使用全局配置即可） |
| Author email for commit | 本仓库专用的提交邮箱 | ✅ 保持空（使用全局配置即可） |
### Advanced（高级设置模块）
这些设置一般不需要修改，只有在遇到特定问题时才需要调整。

| 选项                                            | 作用                                           | 推荐设置                                                                 |
| --------------------------------------------- | -------------------------------------------- | -------------------------------------------------------------------- |
| Update submodules                             | 自动更新 Git 子模块                                 | ❌ 关闭（个人使用一般不需要子模块）                                                   |
| Custom Git binary path                        | 自定义 Git 可执行文件的路径。如果 Obsidian 找不到 Git 时使用     | ✅ 保持默认 `git`；找不到时填写完整路径，如 Windows：`C:\Program Files\Git\bin\git.exe` |
| Additional environment variables              | 额外的环境变量                                      | ❌ 保持空                                                                |
| Additional PATH environment variable paths    | 额外的 PATH 路径                                  | ❌ 保持空                                                                |
| Custom base path (Git repository path)        | 自定义 Git 仓库的基础路径。如果 Git 仓库不在 Obsidian 库根目录时使用 | ❌ 保持空                                                                |
| Custom Git directory path (Instead of '.git') | 自定义 Git 目录的名称                                | ❌ 保持默认 `.git`                                                        |
| Disable on this device                        | 在当前设备上禁用 Git 插件                              | ❌ 关闭                                                                 |
## 基础操作
### 第一次手动同步验证
配置完成后，建议先进行一次手动同步，验证所有设置是否正确：
1. 点击 Obsidian 左侧边栏的 **Git 图标**（像两个分支的图标）
2. 你会看到 **Changes** 列表显示所有未提交的修改（你的截图中显示有 53 个修改文件）
3. 点击顶部的 **对勾按钮**（Commit all changes）
4. 在弹出的输入框中确认提交信息，点击对勾提交
5. 提交完成后，点击顶部的 **同步按钮**（两个箭头循环的图标），或者直接使用 **Commit and sync all changes** 命令一步完成
6. 同步完成后，刷新你的 GitHub/Gitee 仓库页面，确认文件已经成功上传
###日常使用指南
### 基本操作
- **手动提交同步**：使用命令面板（`Ctrl+P`）搜索 `Git: Commit and sync all changes`
- **查看修改**：在 Git 面板点击文件名，可以查看该文件的修改前后对比
- **暂存/取消暂存**：点击文件旁边的 **+** 号暂存该文件，点击 **-** 号取消暂存
- **拉取远程修改**：点击 Git 面板顶部的 **向下箭头** 按钮，或使用命令 `Git: Pull`
- **查看历史记录**：点击 Git 面板顶部的 **时钟图标**，或使用命令 `Git: View history`
### 解决冲突
当多设备同时编辑同一个文件时，可能会出现冲突。Git 会在冲突文件中标记出冲突部分：
```
<<<<<<< HEAD
这是你本地的修改内容
=======
这是远程仓库的修改内容
>>>>>>> origin/main
```
解决步骤：
1. 打开冲突的文件
2. 手动编辑文件，保留你想要的内容，删除 `<<<<<<<`、`=======`、`>>>>>>>` 这些冲突标记
3. 保存文件，回到 Git 面板
4. 暂存该文件，然后提交并同步
### 撤销修改
- 撤销未暂存的修改：在 Git 面板点击文件旁边的 **撤销箭头** 图标
- 撤销已提交的修改：在历史记录中右键点击要撤销的提交，选择 **Revert commit**