---
layout: 主页
date: 2026-05-16T08:00:00
banner_y: 0.0
title: 主页
banner: "[[lingkong.png]]"
---
--------

# Obsidian MCL 
**版本**：v1.0 | **适用**：Obsidian 0.15.0+ | **依赖**：Style Settings 插件

---
## 安装
### 手动安装

1. **下载MCL文件**
   - 访问GitHub仓库：https://github.com/efemkay/obsidian-modular-css-layout
   - 点击"Code" → "Download ZIP"下载整个仓库
   - 解压ZIP文件

2. **复制CSS文件**
   - 打开Obsidian → 设置 → 外观 → CSS代码片段
   - 点击右侧的文件夹图标，打开snippets文件夹
   - 将解压后的三个核心CSS文件复制到该文件夹中

3. **启用片段**
   - 回到Obsidian的CSS代码片段设置
   - 刷新列表，然后启用三个MCL文件

### 必备插件：Style Settings

**Style Settings** 插件是使用MCL的必备工具，它可以让你通过可视化界面调整MCL的所有参数，而无需手动编辑CSS文件。

#### 安装方法

1. 打开Obsidian → 设置 → 社区插件 → 浏览
2. 搜索"Style Settings"
3. 点击"安装" → 启用

#### 基本使用

1. 安装并启用后，在设置中会出现"Style Settings"选项
2. 点击进入，你会看到"MCL Wide Views"、"MCL Multi Column"和"MCL Gallery Cards"三个配置部分
3. 每个部分都包含了对应的参数调整选项，如间距、宽度、颜色等
4. 修改参数后会实时预览效果，无需重启Obsidian
## 语法
### 📋 基础配置
#### 页面级全局配置（YAML Frontmatter）
```yaml
---
# 宽屏视图
cssClass: wide-page          # 整个页面宽屏显示
cssClass: wide-dataview     # 仅Dataview块宽屏
cssClass: wide-table        # 仅表格宽屏
cssClass: wide-callout      # 仅标注框宽屏
cssClass: wide-backlinks    # 仅反向链接宽屏
cssClass: narrow-page       # 页面变窄（适合长文本阅读）

# 画廊视图
cssClass: image-gallery     # 页面内所有图片自动排列为画廊
---
```
#### 全局启用
在Style Settings中：
- 打开"MCL Wide Views"部分
- 找到"Vault-wide settings"
- 勾选你想要全局启用的选项：
  - Vault-wide wide-page
  - Vault-wide wide-dataview
  - Vault-wide wide-table
  - Vault-wide wide-callout
  - Vault-wide wide-backlinks
#### 常用配置选项（Style Settings）
- **Reading Line Length**：调整阅读模式下的行长度
- **Wide Page Max Width**：宽屏页面的最大宽度
- **Wide Table Max Width**：宽屏表格的最大宽度
- **Wide Dataview Max Width**：宽屏Dataview的最大宽度
---

### 🖼️ 宽屏视图（Wide Views）
| 语法 | 效果 | 适用场景 |
|------|------|----------|
| `cssClass: wide-page` | 整个页面突破默认宽度限制 | 项目管理、数据表格、长文档 |
| `cssClass: wide-dataview` | 仅Dataview查询结果宽屏 | 数据库查询、任务列表 |
| `cssClass: wide-table` | 仅Markdown表格宽屏 | 数据对比、参数列表 |
| `cssClass: wide-callout` | 仅标注框宽屏 | 重要提示、信息汇总 |

---
### 📐 多列布局（Multi Column）⭐ 最常用
#### 1. 多列标注框（核心功能）
```markdown
> [!multi-column|width-300-400-300]
>
>> [!note] 第一列标题（300px）
>> 第一列内容（支持所有Markdown格式）
>
>> [!warning] 第二列标题（400px）
>> - 列表项1 
>> - 列表项2
>
>> [!success] 第三列标题（300px）
>> 第三列内容
>> ```python 
>> print("Hello World") 
>> ```
```
Obsidian 原生标准标注框（12种）
这是Obsidian核心自带的标注框，所有主题都原生支持，样式会随主题自动适配。

| 语法            | 别名                 | 默认图标 | 默认颜色 | 推荐用途            |
| ------------- | ------------------ | ---- | ---- | --------------- |
| `[!note]`     | -                  | 📝   | 蓝色   | 普通笔记、补充说明、基础知识  |
| `[!tip]`      | `hint`、`important` | 💡   | 绿色   | 技巧提示、最佳实践、优化建议  |
| `[!info]`     | -                  | ℹ️   | 蓝色   | 重要信息、参数说明、版本更新  |
| `[!success]`  | `check`、`done`     | ✅    | 绿色   | 完成状态、成功结果、验证通过  |
| `[!question]` | `help`、`faq`       | ❓    | 黄色   | 疑问、常见问题、待解决问题   |
| `[!warning]`  | `caution`          | ⚠️   | 黄色   | 警告、注意事项、潜在风险    |
| `[!danger]`   | `error`            | ❌    | 红色   | 严重错误、禁止操作、高危风险  |
| `[!todo]`     | -                  | 📌   | 蓝色   | 待办事项、任务清单、计划安排  |
| `[!cite]`     | `quote`            | 📑   | 灰色   | 引用内容、文献摘录、名言警句  |
| `[!example]`  | -                  | 📌   | 紫色   | 示例代码、演示案例、操作步骤  |
| `[!abstract]` | `summary`、`tldr`   | 📄   | 蓝色   | 摘要、总结、要点提炼      |
| `[!bug]`      | -                  | 🐛   | 红色   | 已知问题、bug报告、缺陷说明 |
 
MCL 扩展标注框类型（4种）
MCL在原生基础上新增了4种**容器型标注框**，专门用于实现特殊布局效果，不能单独作为普通标注框使用。

| 语法 | 核心功能 | 必须包含的内容 |
|------|----------|----------------|
| `[!multi-column]` | 多列布局容器 | 至少2个子标注框 |
| `[!gallery]` | 图片画廊容器 | 多个图片链接 |
| `[!card]` | 图片卡片容器 | 1张图片（可选标题） |
| `[!sidebar]` | 侧边栏容器 | 任意内容（配合浮动使用） |
**自定义列宽**：
```markdown
> [!multi-column|width-300-400-300]  # 三列分别为300px、400px、300px
> [!multi-column|width-50-50]        # 两列各占50%宽度
```

#### 2. 列表布局系列
| 语法                    | 效果           | 推荐列数 | 适用场景      |
| --------------------- | ------------ | ---- | --------- |
| `#mcl/list-column`    | 列表自动分栏（垂直排列） | 2-4列 | 长列表、分类目录  |
| `#mcl/list-column-3`  | 强制3列         | 固定列数 | 统一布局      |
| `#mcl/list-grid`      | 列表转为网格（水平排列） | 3-6列 | 图标、标签、短文本 |
| `#mcl/list-grid-wide` | 宽版网格         | 2-4列 | 中等长度内容    |
| `#mcl/list-card`      | 列表转为卡片式      | 2-3列 | 任务、项目、知识点 |
| `#mcl/list-card-wide` | 宽版卡片         | 1-2列 | 详细信息卡片    |

**示例**：
```markdown
#mcl/list-card
- 卡片1标题
  卡片1详细内容
  支持多行文本
- 卡片2标题
  卡片2详细内容
```

#### 3. 浮动元素
##### 浮动标注框
```markdown
> [!info|float-right-small] 标题
> 标注框内容，正文会自动环绕

> [!note|float-left-medium] 标题
> 标注框内容
```
**可用的浮动位置和大小**：
- `float-left-small`：左侧小尺寸
- `float-left-medium`：左侧中等尺寸
- `float-left-large`：左侧大尺寸
- `float-right-small`：右侧小尺寸
- `float-right-medium`：右侧中等尺寸
- `float-right-large`：右侧大尺寸
常用配置选项（Style Settings）
- **Minimum Sub-Callout Width**：子标注框的最小宽度
- **Multi Column Sub-Callout Gap**：子标注框之间的间距
- **Sub-Callout Margin Top**：子标注框的上边距
- **Floating Callout Width**：浮动标注框的宽度
- **Floating Callout Margin**：浮动标注框的外边距
##### 浮动图片
```markdown
![[图片.jpg|float-right-small]]
![[图片.png|float-left-large]]
```

**可用尺寸**：`small`(150px)、`medium`(250px)、`large`(350px)

---
### 🎨 画廊卡片（Gallery Cards）
#### 1. 图片画廊
```markdown
---
cssClass: image-gallery
---

> [!gallery]
> ![[图片1.jpg]]
> ![[图片2.jpg]]
> ![[图片3.jpg]]
> ![[图片4.jpg]]
```

#### 2. 图片卡片
```markdown

![[图片.jpg|card]]          # 单张图片卡片

> [!card] 图片标题
> ![[图片.jpg]]             # 带标题的图片卡片
```
 常用配置选项（Style Settings）
- **Gallery Image Gap**：画廊图片之间的间距
- **Gallery Image Border Radius**：画廊图片的圆角半径
- **Image Card Shadow**：图片卡片的阴影效果
- **Image Card Padding**：图片卡片的内边距
---
### 🔧 高级技巧
#### 嵌套布局
```markdown
> [!multi-column]
>
>> [!note] 左侧列
>> #mcl/list-card
>> - 卡片1
>>   内容
>> - 卡片2
>>   内容
>
>> [!warning] 右侧列
>> > [!multi-column|width-50-50]
>> >
>> >> [!info] 子列1
>> >> 内容
>> >
>> >> [!success] 子列2
>> >> 内容
```

#### 自定义参数（Style Settings）
| 模块    | 常用参数                         | 说明       |
| ----- | ---------------------------- | -------- |
| 多列标注框 | Minimum Sub-Callout Width    | 子标注框最小宽度 |
|       | Multi Column Sub-Callout Gap | 子标注框间距   |
| 列表卡片  | List Card Border Radius      | 卡片圆角     |
|       | List Card Shadow             | 卡片阴影     |
| 浮动元素  | Floating Callout Margin      | 浮动元素外边距  |
| 图片画廊  | Gallery Image Gap            | 图片间距     |
|       | Gallery Image Border Radius  | 图片圆角     |

---

### ⚠️ 常见问题与注意事项
1. **多列标注框必须有空行**：每个子标注框之间必须有一个只包含`>`的空行
2. **缩进正确**：子标注框必须比父标注框多一个`>`
3. **语法冲突**：避免在同一行使用多个MCL标签
4. **响应式**：MCL会自动在移动设备上转为单列布局
5. **主题兼容**：如遇显示问题，先尝试切换到默认主题排查

---

### 📌 快速参考卡片
```
# 最常用语法速记
1. 页面宽屏：cssClass: wide-page
2. 多列标注：> [!multi-column]
3. 卡片列表：#mcl/list-card
4. 网格列表：#mcl/list-grid
5. 浮动图片：![[图片|float-right-medium]]
6. 图片画廊：> [!gallery]
```
