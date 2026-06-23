---
layout: 主页
date: 2026-05-16T08:00:00
banner_y: 0.0
title: 主页
banner: "[[lingkong.png]]"
---
--------

# Obsidian Templater 插件完整配置说明文档
**版本**：v2.10.0+（2026年最新稳定版）  
**适用系统**：Windows/macOS/Linux/移动端  
**最后更新**：2026年5月16日

## 一、文档说明
本文档是Templater插件的完整配置参考手册，基于官方最新版本编写，包含所有核心设置项的功能解释、推荐配置、使用场景和注意事项。你可以对照Obsidian的设置界面逐行配置。

## 二、插件简介
Templater是Obsidian生态中最强大的模板插件，远超Obsidian自带的模板插件。它不仅支持简单的变量替换，还提供了数百个内置函数、JavaScript脚本执行、事件钩子等高级功能，可以实现高度自动化的笔记工作流。

**核心优势**：
- 支持复杂的变量和函数调用
- 可以执行JavaScript脚本
- 支持新建文件时自动触发模板
- 支持自定义用户函数和系统命令
- 与其他插件（每日笔记、日历等）完美兼容

## 三、基础配置
### 1. Template folder location（模板文件夹位置）
- **功能**：指定库中专门存放Templater模板文件的文件夹。只有放在这个文件夹里的`.md`文件才会被识别为可用模板。
- **推荐配置**：创建独立文件夹 `Templates/Templater`，不要与Obsidian自带模板插件的文件夹混用。
- **注意事项**：这是Templater的**必选设置**，不设置则插件无法正常工作。

### 2. Internal variables and functions（内部变量和函数）
- **功能**：Templater提供的预定义变量和函数库，是其核心能力所在。
- **使用方法**：点击`documentation`链接查看官方完整文档。
- **常用示例**：
  - `<% tp.file.title %>`：获取当前笔记标题
  - `<% tp.date.now("YYYY-MM-DD") %>`：获取当前日期
  - `<% tp.system.prompt("请输入内容") %>`：弹出输入框获取用户输入

### 3. Syntax highlighting on desktop（桌面端语法高亮）
- **功能**：在编辑模式下为Templater命令（`<% ... %>`包裹部分）添加语法高亮。
- **推荐配置**：**开启**（默认开启）
- **作用**：将Templater代码与普通文本区分开，大幅提升模板编写和调试效率。

### 4. Syntax highlighting on mobile（移动端语法高亮）
- **功能**：在移动端编辑模式下为Templater命令添加语法高亮。
- **推荐配置**：**关闭**（默认关闭）
- **注意事项**：官方明确警告此功能可能破坏移动端实时预览功能，导致笔记显示异常。

### 5. Automatic jump to cursor（自动跳转到光标）
- **功能**：插入模板后，自动将光标定位到模板中`<% tp.file.cursor() %>`标记的位置。
- **推荐配置**：**开启**
- **使用场景**：模板中设置光标位置后，插入模板即可直接开始输入内容，无需手动移动光标。

## 四、核心功能配置
### 1. Trigger Templater on new file creation（新建文件时自动触发模板）
- **功能**：监听"新建文件"事件，当新文件匹配设置的规则时，自动执行模板中的所有命令。
- **推荐配置**：**按需开启**
- **兼容性**：完美支持每日笔记、日历、复习、笔记重构等插件。
- **⚠️ 重要警告**：Templater可以执行脚本和系统命令，从未知来源获取的模板可能包含恶意代码。请确保所有自动触发的模板都是你自己编写的、安全的。
- **使用前提**：开启后必须在下方的"Folder templates"或"File regex templates"中设置触发规则。

### 2. Ignore folders on file creation（新建文件时忽略的文件夹）
- **功能**：在这些文件夹中新建文件时，不会触发Templater的自动模板处理逻辑。
- **推荐配置**：添加以下文件夹到忽略列表：
  - `Attachments`（附件文件夹）
  - `Assets`（资源文件夹）
  - `Excalidraw`（绘图文件夹）
  - `Scripts`（脚本文件夹）
- **注意事项**：此设置递归生效，子文件夹也会被自动忽略。

### 3. Folder templates（文件夹模板）
- **功能**：当在指定文件夹中新建**空文件**时，自动用指定模板填充该文件。
- **匹配规则**：优先匹配路径最深的文件夹规则。例如：给`A/B/C`和`A`都设置了模板，在`A/B/C`中新建文件时会使用`A/B/C`的模板。
- **全局模板**：给根目录`/`设置模板，将成为所有新建空文件的全局默认模板。

#### 3.1 Enable folder templates（启用文件夹模板）
- **功能**：开启后下方的文件夹模板规则才会生效。
- **注意事项**：与"File regex templates"**互斥**，开启一个会自动禁用另一个。
- **推荐配置**：大多数用户推荐使用文件夹模板，配置简单直观。

#### 3.2 配置方法
1. 左侧输入框填写文件夹路径（如`Notes/日记`）
2. 右侧输入框填写模板文件路径（如`Templates/Templater/日记模板.md`）
3. 点击加号添加规则
4. 可以拖动规则调整优先级（虽然文件夹模板按路径深度匹配，优先级调整影响不大）

### 4. File regex templates（文件正则模板）
- **功能**：当新建的**空文件**的路径/文件名匹配正则表达式时，自动填充对应的模板。
- **匹配规则**：从上到下按顺序匹配，**第一个匹配到的规则生效**，规则顺序非常重要。可以用`.*`作为最后一条兜底规则。
- **适用场景**：需要按文件名、扩展名等复杂规则触发模板的场景。

#### 4.1 Enable file regex templates（启用文件正则模板）
- **功能**：开启后下方的正则模板规则才会生效。
- **注意事项**：与"Folder templates"**互斥**。

#### 4.2 常用正则示例
| 正则表达式 | 匹配内容 |
|------------|----------|
| `^\d{4}-\d{2}-\d{2}.*` | 所有以日期开头的文件（如`2026-05-16 日记.md`） |
| `.*_会议记录\.md$` | 所有以`_会议记录.md`结尾的文件 |
| `^Notes\/工作\/.*` | `Notes/工作`文件夹下的所有文件 |
| `.*\.md$` | 所有Markdown文件 |

### 5. Template hotkeys（模板快捷键）
- **功能**：将特定模板文件绑定到全局快捷键，按下快捷键即可快速插入模板到当前笔记。
- **推荐配置**：为最常用的3-5个模板设置快捷键。
- **配置方法**：
1. 点击「Add new hotkey for template」
2. 输入模板文件路径（如`Templates/Templater/快速笔记.md`）
3. 点击新出现的按钮，按下想要设置的快捷键组合（如`Ctrl+Shift+N`）

## 五、高级功能配置
### 1. Startup templates（启动模板）
- **功能**：当Obsidian启动、Templater插件加载完成时，这些模板会被**自动执行一次**。
- **关键特性**：这些模板**不会向任何笔记输出内容**，仅用于执行脚本逻辑。
- **典型用途**：
  - 注册Obsidian事件钩子（文件打开/创建/删除时触发自定义逻辑）
  - 初始化全局变量和配置
  - 运行一次性启动脚本

#### 1.1 配置方法
1. 输入模板文件路径（如`Scripts/startup-init.md`）
2. 点击「Add new startup template」添加到列表

#### 1.2 示例脚本
```javascript
<%
// 注册文件创建钩子，新建文件时自动添加创建时间属性
app.vault.on("create", async (file) => {
  if (file.extension === "md") {
    await app.fileManager.processFrontMatter(file, (frontmatter) => {
      frontmatter["创建时间"] = tp.date.now("YYYY-MM-DD HH:mm:ss");
    });
  }
});
%>
```

### 2. User script functions（用户脚本函数）
- **功能**：允许你编写自定义JavaScript函数，在模板中通过`tp.user`调用。
- **作用**：将复杂逻辑封装成函数，避免在模板中重复编写代码，提升可维护性。

#### 2.1 Script files folder location（脚本文件夹位置）
- **功能**：指定一个文件夹，里面的所有`.js`文件都会被作为CommonJS模块加载。
- **推荐配置**：创建独立文件夹 `Scripts/UserFunctions`。

#### 2.2 User script intellisense（用户脚本智能提示）
- **功能**：设置编辑器中对用户脚本函数的智能提示方式。
- **推荐配置**：`Render method description, parameters list, and return`（显示最完整的信息）
- **选项说明**：
  - `Render method description, parameters list, and return`：显示函数描述、参数列表和返回值
  - `Render only method description`：只显示函数描述
  - `Render only parameters list`：只显示参数列表
  - `Render nothing`：不显示任何提示

#### 2.3 示例脚本
1. 在脚本文件夹中创建`utils.js`：
```javascript
module.exports = {
  // 格式化日期为 YYYY-MM-DD
  formatDate: (date) => {
    const d = new Date(date);
    return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
  },
  
  // 生成随机十六进制颜色
  randomColor: () => "#" + Math.floor(Math.random()*16777215).toString(16).padStart(6, '0'),
  
  // 获取本周一的日期
  getMonday: () => {
    const d = new Date();
    const day = d.getDay();
    const diff = d.getDate() - day + (day === 0 ? -6 : 1);
    return new Date(d.setDate(diff)).toISOString().split('T')[0];
  }
};
```

2. 在模板中调用：
```markdown
今天日期：<% tp.user.utils.formatDate(new Date()) %>
本周一：<% tp.user.utils.getMonday() %>
随机颜色：<% tp.user.utils.randomColor() %>
```

### 3. User system command functions（用户系统命令函数）
- **功能**：允许你创建链接到系统命令的用户函数，在模板中执行Shell命令。
- **推荐配置**：**保持关闭**
- **⚠️ 极高风险警告**：
  > 执行任意系统命令存在严重安全风险。从未知来源获取的模板如果包含恶意系统命令，可能会删除你的文件、窃取你的数据或对电脑造成永久性损害。
- **使用建议**：除非你非常清楚自己在做什么，并且只使用自己编写的、完全可控的脚本，否则绝对不要开启此功能。

## 六、快速入门配置步骤
1. 在你的Obsidian库中创建以下文件夹结构：
   ```
   ├── Templates/
   │   └── Templater/          # 存放Templater模板
   └── Scripts/
       ├── UserFunctions/      # 存放用户脚本函数
       └── Startup/            # 存放启动模板
   ```

2. 打开Templater设置：
   - 设置`Template folder location`为`Templates/Templater`
   - 设置`Script files folder location`为`Scripts/UserFunctions`
   - 开启`Automatic jump to cursor`
   - 关闭`Syntax highlighting on mobile`
   - 关闭`User system command functions`

3. 添加忽略文件夹：
   - 添加`Attachments`、`Assets`、`Scripts`到忽略列表

4. 配置文件夹模板（可选）：
   - 开启`Enable folder templates`
   - 添加规则：`Notes/日记` → `Templates/Templater/日记模板.md`
   - 添加规则：`Notes/读书笔记` → `Templates/Templater/读书笔记模板.md`

5. 设置模板快捷键（可选）：
   - 为`Templates/Templater/快速笔记.md`设置快捷键`Ctrl+Shift+N`

## 七、常用模板示例
### 日记模板
```markdown
---
日期: <% tp.date.now("YYYY-MM-DD") %>
星期: <% tp.date.now("dddd") %>
天气: 
心情: 
---

# <% tp.date.now("YYYY年MM月DD日 dddd") %>

## 今日待办
- [ ] 

## 今日记录

## 今日总结
```

### 快速笔记模板
```markdown
---
创建时间: <% tp.date.now("YYYY-MM-DD HH:mm:ss") %>
标签: 快速笔记
---

<% tp.file.cursor() %>
```

## 八、最佳实践
1. **统一管理模板**：所有模板都放在专门的文件夹中，按类型分类（如`日记`、`读书笔记`、`会议记录`）
2. **使用光标标记**：在模板中合适的位置添加`<% tp.file.cursor() %>`，提升使用效率
3. **优先使用文件夹模板**：大多数情况下，文件夹模板比正则模板更简单直观，不易出错
4. **封装重复逻辑**：将常用的逻辑封装成用户脚本函数，避免在多个模板中重复编写
5. **定期备份模板**：模板是你工作流的核心，定期备份模板文件夹
6. **谨慎使用高级功能**：启动模板和系统命令功能虽然强大，但也存在安全风险，使用时务必小心

## 九、安全注意事项
1. **永远不要运行来源不明的模板**：特别是包含JavaScript代码或系统命令的模板
2. **保持系统命令功能关闭**：除非你有明确的需求并且完全理解其风险
3. **限制自动触发范围**：不要为根目录设置全局模板，只给需要的文件夹设置模板
4. **定期检查自动触发规则**：确保没有意外添加的规则可能导致文件被意外修改

## 十、常见问题
### Q1：模板插入后变量没有被替换
A1：确保你使用的是Templater的命令（`<% ... %>`），而不是Obsidian自带模板的语法（`{{...}}`）。同时确保你是通过Templater的命令插入模板，而不是Obsidian自带的模板命令。

### Q2：新建文件时没有自动触发模板
A2：检查以下几点：
- 确保`Trigger Templater on new file creation`已开启
- 确保`Enable folder templates`或`Enable file regex templates`已开启
- 确保新建的文件是空文件（非空文件不会触发自动模板）
- 确保文件所在的文件夹不在忽略列表中

### Q3：用户脚本函数没有智能提示
A3：检查`User script intellisense`设置是否正确，并且脚本文件中正确导出了函数。如果仍然没有提示，可以点击设置中的刷新按钮重新加载脚本。