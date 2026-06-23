---
layout: 主页
date: 2026-05-16T08:00:00
banner_y: 0.0
title: 主页
banner: "[[lingkong.png]]"
---
--------

Dataview 是 Obsidian 最强大的第三方插件之一，它能将你的笔记库变成一个可查询的数据库，通过简单的语法自动提取笔记元数据并生成表格、列表、任务视图和日历视图。
## 核心配置详解
### 通用查询设置
| 配置项                                                  | 作用                 | 推荐设置                   |
| ---------------------------------------------------- | ------------------ | ---------------------- |
| **Enable inline queries**                            | 启用行内查询（`= 表达式`语法）  | ✅ 必须开启                 |
| **Enable JavaScript queries**                        | 启用 DataviewJS 高级查询 | ❌ 默认关闭（进阶用户按需开启，有安全风险） |
| **Enable inline JavaScript queries**                 | 启用行内 JS 查询         | ❌ 默认关闭（依赖上一项）          |
| **Enable inline field highlighting in reading view** | 阅读视图中高亮行内元数据       | ✅ 推荐开启                 |
| **Enable inline field highlighting in Live Preview** | 实时预览中高亮行内元数据       | ✅ 推荐开启                 |
| **DataviewJS keyword**                               | DataviewJS 代码块关键字  | 保持默认 `dataviewjs`      |
| **Inline query prefix**                              | 行内查询前缀             | 保持默认 `=`               |
| **JavaScript inline query prefix**                   | JS 行内查询前缀          | 保持默认 `$=`              |
| **Code block inline queries**                        | 允许在普通代码块中执行行内查询    | ✅ 推荐开启                 |
### 视图设置

| 配置项 | 作用 | 推荐设置 |
|--------|------|----------|
| **Display result count** | 在查询结果标题显示数量 | ✅ 推荐开启 |
| **Warn on empty result** | 查询无结果时显示警告 | ✅ 推荐开启（方便排查问题） |
| **Render null as** | 空值的显示内容 | 建议改为 `无`（更符合中文习惯） |
| **Automatic view refreshing** | 文件修改时自动刷新查询 | ✅ 推荐开启 |
| **Refresh interval** | 刷新间隔（毫秒） | 保持默认 `2500`（2.5秒，太频繁会影响性能） |
| **Date format** | 默认日期格式 | 强烈建议改为 `yyyy-MM-dd` |
| **Date + time format** | 默认日期时间格式 | 强烈建议改为 `yyyy-MM-dd HH:mm` |
### 表格与任务设置
| 配置项                                    | 作用                | 推荐设置                  |
| -------------------------------------- | ----------------- | --------------------- |
| **Primary column name**                | 表格第一列（文件链接）名称     | 保持默认 `File`           |
| **Grouped column name**                | 分组查询时的分组列名称       | 保持默认 `Group`          |
| **Automatic task completion tracking** | 勾选任务时自动添加完成日期     | ❌ 默认关闭（如需自动记录完成时间可开启） |
| **Use emoji shorthand for completion** | 使用 emoji 表示任务完成状态 | ❌ 默认关闭                |
| **Recursive sub-task completion**      | 勾选父任务时自动完成所有子任务   | ✅ 推荐开启（如果使用子任务）       |
## 核心概念：元数据
Dataview 的工作原理是**提取笔记中的元数据**，然后根据元数据进行查询。元数据有两种写法：
写在笔记最开头，用 `---` 包裹，是最规范的元数据写法：
```yaml
---
title: Dataview 使用教程
tags: [Obsidian, 插件, 教程]
date: 2026-05-16
author: 豆包
status: 已完成
priority: 高
category: 技术笔记
---
```
### 2. 行内元数据（推荐用于局部元数据）
写在笔记正文中，格式为 `字段名:: 值`，适合在内容中添加零散的元数据：
```
这是一篇关于 Dataview 的详细教程。
创建时间:: 2026-05-16
阅读时长:: 15分钟
相关链接:: [[Obsidian 基础入门]], [[Templater 插件教程]]
```
## 基础查询语法
Dataview 查询写在代码块中，代码块语言指定为 `dataview`。基本结构如下：
```
···dataview
查询类型 字段1, 字段2 AS "别名", ...
FROM 来源范围
WHERE 过滤条件
SORT 排序字段 排序方式
LIMIT 结果数量
GROUP BY 分组字段
···
```
### 四种基本查询类型
#### TABLE 表格查询
以表格形式显示多个字段的信息：
```
···dataview
// 列出所有技术笔记，显示标题、日期、状态和优先级
TABLE title, date, status, priority
FROM #技术笔记
WHERE status != "已废弃"
SORT date DESC
LIMIT 10
···
```
#### LIST 列表查询
只显示文件链接，适合简单的列表展示：
```
···dataview
// 列出所有待办的项目笔记
LIST
FROM #项目
WHERE status = "待办"
SORT priority DESC
···
```
#### TASK 任务查询
专门用于查询笔记中的任务（`- [ ] 任务内容`）：
```
···
// 列出所有未完成的任务
TASK
WHERE !completed
SORT created ASC
···
```
#### CALENDAR 日历查询
按日期在日历视图中显示笔记：
```
···dataview
// 按创建日期显示所有日记
CALENDAR file.cday
FROM "日记"
···
```
### 常用内置字段
Dataview 会为每个文件自动生成以下内置字段，无需手动添加：
- `file.name`：文件名
- `file.path`：文件完整路径
- `file.folder`：文件所在文件夹
- `file.link`：指向该文件的内部链接
- `file.size`：文件大小
- `file.ctime`：文件创建时间
- `file.mtime`：文件最后修改时间
- `file.cday`：文件创建日期（仅日期）
- `file.mday`：文件最后修改日期（仅日期）
- `file.tags`：文件的所有标签
- `file.outlinks`：文件中的所有外链
- `file.inlinks`：所有指向该文件的链接
- `file.tasks`：文件中的所有任务
### 常用运算符与函数
- **比较运算符**：`=`、`!=`、`>`、`<`、`>=`、`<=`
- **逻辑运算符**：`AND`、`OR`、`NOT`
- **字符串函数**：`contains(字符串, 子串)`、`startswith()`、`endswith()`
- **日期函数**：`date()`、`today()`、`now()`、`dur(时间间隔)`
- **数组函数**：`length(数组)`、`contains(数组, 元素)`
#### 实用查询示例
最近修改的笔记
```
···dataview
TABLE file.mtime AS "最后修改时间", file.size AS "文件大小"
FROM "笔记"
SORT file.mtime DESC
LIMIT 15
···
```
#### 今天到期的任务
```
···dataview
TASK
WHERE due = date(today) AND !completed
···
```
#### 按状态分组的项目
```
···dataview
TABLE rows.file.link AS "项目列表"
FROM #项目
GROUP BY status AS "项目状态"
SORT status ASC
···
```
#### 包含特定关键词的笔记
```
···dataview
LIST
WHERE contains(file.name, "Dataview") OR contains(file.content, "Dataview")
···dataview
```
#### 行内查询（直接写在正文中）
- 当前日期：`= date(today)`
- 今天的待办任务数量：`= length(filter(file.tasks, t => t.due = date(today) AND !t.completed))`
- 所有技术笔记数量：`= length(#技术笔记)`

## 进阶用法
### DataviewJS 高级查询
当普通查询无法满足需求时，可以使用 JavaScript 编写更复杂的查询。需要先在配置中开启 `Enable JavaScript queries`。

示例：列出所有包含图片的笔记
```dataviewjs
dv.table(["笔记名称", "图片数量"],
  dv.pages("笔记")
    .filter(p => p.file.outlinks.some(l => 
      l.path.endsWith(".png") || l.path.endsWith(".jpg") || l.path.endsWith(".gif")
    ))
    .map(p => [
      p.file.link,
      p.file.outlinks.filter(l => 
        l.path.endsWith(".png") || l.path.endsWith(".jpg") || l.path.endsWith(".gif")
      ).length
    ])
)
```
### 结合 Templater 自动添加元数据
使用 Templater 插件创建笔记模板，自动添加统一格式的元数据：
```yaml
---
title: 在团队中部署 Obsidian
date: 2026-05-Sa
author: 你的名字
status: 草稿
tags: []
---
```
## 常见问题排查
1. **查询不显示结果**
   - 检查元数据格式：YAML 必须用 `---` 包裹，行内元数据必须是两个冒号 `::`
   - 检查字段名拼写是否正确（区分大小写）
   - 检查查询范围是否正确（`FROM` 后面的文件夹或标签是否存在）
   - 点击查询结果右上角的刷新按钮手动刷新

2. **查询不自动刷新**
   - 确认已开启 `Automatic view refreshing`
   - 检查刷新间隔是否设置过长
   - 重启 Obsidian

3. **性能问题**
   - 尽量缩小查询范围，指定具体的文件夹或标签，避免查询整个库
   - 减少单个页面中的查询数量
   - 避免使用过于复杂的 DataviewJS 查询