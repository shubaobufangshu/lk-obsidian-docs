---
layout: 主页
date: 2026-05-16T08:00:00
banner_y: 0.0
title: 主页
banner: "[[lingkong.png]]"
---
--------

Style Settings 是 Obsidian 最实用的第三方插件之一，它让你无需编写任何 CSS 代码，通过图形化界面就能深度自定义主题的外观和布局。
## 界面总览
| 界面元素 | 功能说明 |
|---------|---------|
| 顶部搜索框 | 快速搜索所有配置项，输入关键词即可定位（如搜索"image"找到所有图片相关设置） |
| 右上角 Import/Export | 导入/导出完整配置为 JSON 文件，用于备份或分享配置 |
| 配置分组 | 主题将所有设置按功能分类（如你截图中的三个核心布局分组） |
| 分组右侧循环箭头 | 重置该分组下所有设置为默认值 |
| 分组右侧向下箭头 | 复制该分组对应的所有 CSS 变量到剪贴板，用于手动编写 CSS 片段 |
## 核心模块详解
### Modular CSS Layout - Gallery Cards（画廊卡片布局）
#### General Image Settings（通用图片设置）
全局控制所有图片的基础样式：
- 图片圆角大小
- 图片阴影强度和颜色
- 图片边框粗细和颜色
- 图片默认最大宽度
- 点击图片放大的过渡效果
####  Image Gallery Settings（图片画廊设置）
**最常用功能**，一键创建响应式图片画廊，支持两种使用方式：

**方式1：Callout 块方式（推荐）**

```markdown
> [!gallery]
> ![[图片1.jpg]]
> ![[图片2.png]]
> ![[图片3.webp]]
> ![[图片4.jpg]]
```
将图片放在 `> [!gallery]` 块中，自动变成网格画廊布局。
**方式2：全局笔记方式**
在笔记的 YAML 头部添加：
```yaml
---
image-gallery: true
---
```
整个笔记中的所有图片都会自动变成画廊布局。
**可配置项**：
- 不同屏幕尺寸下每行显示的图片数量
- 图片之间的间距
- 图片卡片的固定高度
- 是否显示图片文件名作为标题
- 图片卡片的背景色和悬停效果
#### Image Float / Aside（图片浮动/侧边栏）
实现文字环绕图片的效果，使用方法极其简单：

```markdown
![[图片.jpg|left|250]] 这是左浮动的图片，文字会自动环绕在图片右侧。

![[图片.jpg|right|300]] 这是右浮动的图片，文字会自动环绕在图片左侧。
```
**可配置项**：
- 浮动图片的最大宽度限制
- 图片与文字之间的边距
- 浮动图片的阴影效果
- 小屏幕（手机）下是否自动取消浮动（避免文字挤压）
####  Image in List Settings（列表中的图片设置）
专门优化列表项中插入图片的显示效果：
- 列表中图片的最大高度
- 图片在列表项中的对齐方式（顶部/居中/底部）
- 是否自动调整列表行高以适应图片

#### Mermaid SVG Settings（Mermaid 图表设置）
统一设置所有 Mermaid 生成的图表样式：
- 图表的最大宽度
- 图表背景色
- 图表边框和阴影
- 图表的缩放行为
### Modular CSS Layout - Multi Column（多列布局）
无需手动编写 CSS，快速实现笔记的多列布局，是整理内容的利器。
##### Multi Column Callout Settings（多列 Callout 设置）
使用 `> [!multi-column]` 创建多列布局，每个子 Callout 就是一列：
```markdown
> [!multi-column]
>
> > [!note] 第一列
> > 这是第一列的内容，可以包含文字、列表、图片等
> > - 列表项1
> > - 列表项2
>
> > [!tip] 第二列
> > 这是第二列的内容
> > ![[图片.jpg|200]]
>
> > [!warning] 第三列
> > 这是第三列的内容
```

**可配置项**：
- 列与列之间的间距
- 是否显示列之间的分隔线
- 小屏幕下自动切换为单列的阈值
- 每列的最小宽度

##### Float Callout Settings（浮动 Callout 设置）
和浮动图片类似，实现 Callout 块的左右浮动：
```markdown
> [!info|left|350] 左浮动 Callout
> 这是左浮动的 Callout 内容，正文文字会环绕在右侧。
```

**可配置项**：
- 浮动 Callout 的最大宽度
- Callout 与正文之间的边距
- 浮动 Callout 的阴影和圆角
##### List Column（列表多列）
将普通的无序列表/有序列表转换为多列布局，支持两种使用方式：

**方式1：CSS 类方式（推荐）**
```markdown
- 列表项1
- 列表项2
- 列表项3
- 列表项4
- 列表项5
- 列表项6
{.two-column-list}
```
支持 `{.two-column-list}`、`{.three-column-list}`、`{.four-column-list}`。

**方式2：标签方式**
在列表上方添加标签：
```markdown
#mcl/list-column
- 列表项1
- 列表项2
- 列表项3
```

**可配置项**：
- 不同列数列表的列间距
- 列表项之间的垂直间距
- 小屏幕下自动切换为单列的阈值

##### List Grid and List Card（列表网格和列表卡片）
将列表转换为网格布局或卡片式布局，非常适合展示项目集合：
```markdown
#mcl/list-card
- 项目1
- 项目2
- 项目3
- 项目4
- 项目5
- 项目6
```

**可配置项**：
- 网格每行显示的卡片数量
- 卡片的大小和内边距
- 卡片的背景色和悬停效果
- 卡片的阴影和圆角

---

### Modular CSS Layout - Wide Views（宽视图布局）
解决 Obsidian 默认页面过窄的问题，灵活调整页面宽度和块级元素的显示方式。

#### Adjustable RLL Settings（可调整阅读线长度）
RLL = Reading Line Length（阅读线长度），即笔记内容的最大宽度。
- **前提条件**：必须在 Obsidian 设置 → 编辑器 → 启用"限制阅读线长度"
- 可分别设置桌面端、平板端、手机端的最大宽度
- 支持实时预览调整效果

#### Vault-wide toggle for Wide Blocks（全局宽块开关）
让特定类型的块自动突破阅读线长度限制，变成全宽显示：
- **前提条件**：同样需要启用"限制阅读线长度"
- 支持自动变宽的块类型：
  - 普通表格
  - Dataview 表格
  - Callout 块
  - 反向链接块
  - 代码块
  - 嵌入的笔记
- 可配置宽块的左右边距

#### Narrow Page Settings（窄页面设置）
与上面相反，将特定页面设置为窄页面，适合阅读长文本：
- **前提条件**：必须在 Obsidian 设置 → 编辑器 → 禁用"限制阅读线长度"
- 使用方法：在笔记 YAML 头部添加：
  ```yaml
  ---
  cssClass: narrow-page
  ---
  ```
- 可配置窄页面的最大宽度和左右边距
## 高级功能
### 导入导出配置
- **导出配置**：点击右上角 `Export` 按钮，将所有设置保存为 JSON 文件，作为备份
- **导入配置**：点击右上角 `Import` 按钮，选择之前导出的 JSON 文件，一键恢复配置
- **注意**：导入会覆盖当前所有设置，导入前建议先导出自己的配置作为备份
###  自定义 CSS 变量
如果 Style Settings 没有提供你想要的精细调整，可以：
1. 点击对应配置项右侧的向下箭头，复制 CSS 变量
2. 创建一个新的 CSS 片段（设置 → 外观 → CSS 片段 → 新建）
3. 粘贴变量并修改数值：
   ```css
   :root {
     --gallery-image-border-radius: 12px;
     --multi-column-gap: 20px;
   }
   ```
4. 启用该 CSS 片段，优先级高于 Style Settings
### 添加自定义配置项
你甚至可以在 CSS 片段中定义自己的 Style Settings 配置项：
```css
/* @settings
name: 我的自定义设置
id: my-custom-settings
settings:
  -
    id: my-accent-color
    title: 自定义强调色
    type: variable-color
    format: hex
    default: '#6366f1'
  -
    id: my-card-shadow
    title: 卡片阴影强度
    type: variable-number
    default: 0.1
    min: 0
    max: 1
    step: 0.05
*/

:root {
  --my-accent-color: #6366f1;
  --my-card-shadow: 0 4px 6px -1px rgba(0, 0, 0, var(--my-card-shadow));
}
```
保存并启用后，Style Settings 中会出现一个新的"我的自定义设置"分组。

