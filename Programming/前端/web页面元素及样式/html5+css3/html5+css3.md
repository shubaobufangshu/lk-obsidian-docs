# html5*css3

## 1. HTML基础语法

### 1.1 基本骨架

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>标题</title>
    <!-- 引入css -->
    <link type="text/css" rel="stylesheet" href="url">
    <style>
        /* 定义css */
    </style>
</head>
<body>
    <script>
        // 定义JavaScript
    </script>
    <!-- 引入JavaScript -->
    <script src="url" type="text/javascript" charset="utf-8"></script>
</body>
</html>
```

- `<!DOCTYPE html>`：HTML5文档类型声明，告知浏览器解析模式。
- `<html lang="en">`：根标签，文档以此开始和结束，指示浏览器用HTML解析内容。
- `<head>`：页面头部信息，包含`<meta>`、`<title>`、`<link>`。`<meta>`定义元信息（作者、关键词、刷新频率、文档关系等）；`<title>`定义标题；`<link>`引用外部文件。
- `<style>`：定义CSS样式。
- `<body>`：主体标签，与`<head>`并列，一个HTML文件仅一个`<body>`，页面内容（文本、图片、音视频等）均置于其中。
- `<script>`：定义或引入JavaScript代码。
- `<!-- -->`：注释。

### 1.2 标签

- **双标签**：`<标签>内容</标签>`
- **单标签**：`<标签>`

#### 1.2.1 标题标签

`<hn align="对齐方式">文本</hn>`，n=1~6，数字越小字体越大。对齐方式：`left`、`center`、`right`。

#### 1.2.2 文本修饰标签

- 加粗：`<b>`、`<strong>`
- 斜体：`<i>`、`<em>`
- 删除线：`<s>`、`<del>`
- 下划线：`<u>`、`<ins>`
- 大小号：`<big>`、`<small>`
- 上下标：`<sup>`、`<sub>`

#### 1.2.3 空标签

`<hr/>`（水平分割线）、`<br/>`（换行）、`<img/>`（图片）、`<input>`（表单控件）

#### 1.2.4 块级与行级标签

- **块级标签**：`<hn>`、`<div>`、`<ul>`、`<ol>`、`<li>`
  - `<div>`：容器
- **行级标签**：`<b>`、`<strong>`、`<i>`、`<em>`、`<s>`、`<u>`、`<del>`、`<ins>`、`<span>`
  - `<span>`：文本容器
- **`display`属性**：
  - `inline`：行级
  - `block`：块级
  - `inline-block`：行内块
  - `none`：隐藏

#### 1.2.5 列表标签

- **有序列表** `<ol>`
  - 属性：`reversed="reversed"`（降序）、`start="数字"`（起始值）、`type="1/A/a/I/i"`（标记类型）
  - 结构：`<ol><li>内容</li>...</ol>`
- **无序列表** `<ul>`
  - 属性 `type`：`disc`（实心圆）、`square`（方块）、`circle`（空心圆）、`none`
  - 结构：`<ul><li>内容</li>...</ul>`
- **自定义列表** `<dl>`
  - 结构：`<dl><dt>标题</dt><dd>解释</dd>...</dl>`

#### 1.2.6 超链接 `<a>`

- **属性**：
  - `href="url"`
  - `target`：`_blank`（新窗口）、`_self`（当前页）、`_parent`（在iframe中同_self）、`_top`（同_self）
  - `title`：提示文本
- **内部跳转**：`<a href="#n">起始</a>` → `<a name="n">目的地</a>`

#### 1.2.7 图片标签 `<img>`

- **属性**：`alt`（描述）、`src`（url）、`align`（排列方式）、`border`（边框）、`height/width`（高宽）

#### 1.2.8 表格

**常用标签**：
`<table>`、`<tr>`（行）、`<td>`（单元格）、`<caption>`（标题）、`<th>`（表头）、
`<thead>`/`<tbody>`/`<tfoot>`（结构分组）、`<colgroup>`/`<col>`（列组合与属性）

**表格属性**：

- `width` / `height`：表格宽高
- `border`：边框粗细（数字）
- `cellpadding`：单元格内容与边框的距离
- `cellspacing`：单元格之间的间距
- `align`：表格水平对齐（`center`/`left`/`right`）
- `bgcolor`：背景色
- `background`：背景图片
- `valign`：单元格内容垂直对齐（`top`/`bottom`/`middle`）

**行标签 `<tr>` 属性**：

- `align`：行内水平对齐
- `height`：行高
- `valign`：行内垂直对齐
- `bgcolor` / `background`：行背景色/背景图

**单元格标签 `<td>` 属性**：

- `align` / `valign`：单元格内对齐
- `width` / `height`：单元格宽高
- `bgcolor` / `background`：单元格背景
- `rowspan`：合并同一列的多行
- `colspan`：合并同一行的多列

**合并单元格**：

- 行合并：`<td rowspan="n">`
- 列合并：`<td colspan="n">`

#### 1.2.9 表单

**`<form>` 属性**：

- `action="url"`（处理地址）
- `method="get/post"`（post更安全、数据量大）
- `name`（表单名称）
- `autocomplete="on/off"`（自动完成）
- `novalidate`（关闭验证）

**`<input>` 标签**：

- `type`：
  - 输入框：`text`（单行）、`password`（密码）
  - 选择框：`radio`（单选框，同`name`一组，`checked`默认）、`checkbox`（复选框，`checked`默认）、`file`（文件上传）、日期时间类型（`date`、`month`、`week`、`time`、`datetime`、`datetime-local`）
  - 按钮：`submit`（提交）、`reset`（重置）
  - 其他：`email`（验证邮箱）、`range`（滑动条，配合`max/min/step`）、`tel`（电话）、`number`（数字）、`url`、`search`、`color`（颜色选择器）
- 通用属性：`maxlength`（最大字符）、`value`（默认文本）、`size`（宽度）、`required`（必填）、`max/min`（数值范围）、`readonly`（只读）、`disabled`（禁用且不提交）

**`<textarea>`**（多行文本）：`cols`（每行字符数）、`rows`（行数）

**`<select>`**（下拉框）：

- 属性：`size`（可见选项数）、`multiple`（多选）
- 结构：`<select><option selected>选项</option>...</select>`

**`<datalist>`**：为 `<input>` 提供自动完成

#### 1.2.10 音频标签 `<audio>`

- `autoplay`：页面加载后自动播放
- `loop`：循环播放
- `preload`：预加载策略（`auto`/`meta`/`none`）
- `controls`：显示播放控件
- `muted`：静音输出
- `src`：音频文件URL

#### 1.2.11 视频标签 `<video>`

- 与 `<audio>` 共有属性相同（`autoplay`、`loop`、`preload`、`controls`、`muted`、`src`）
- `height` / `width`：播放器尺寸（像素）

#### 语义布局标签

- `<header>`：页头
- `<footer>`：页脚
- `<nav>`：导航链接
- `<section>`：内容分块（非容器）
- `<aside>`：附属内容
- `<figure>`：独立流内容
- `<figcaption>`：为 `<figure>` 添加标题
- `<article>`：日志、评论等，内部可用 `<section>` 划分

### 1.3 Emmet 写法

- 类：`标签名.类名`
- ID：`标签名#id名`
- 同阶：`标签+标签`
- 父子：`标签>标签`
- 多个：`标签*数字`
- 带内容：`标签{内容}`

## 2. CSS 层叠样式表

### 2.1 CSS 基础语法与使用

- **语法**：`选择器 { 属性: 属性值; }`
  - 选择器：指定要渲染的对象
  - 声明：`属性: 值`，定义渲染方式

- **使用方式**：
  1. **行内样式**：`<标签名 style="属性1:值1; 属性2:值2;">内容</标签名>`
  2. **内部样式**：`<style>` 标签内定义 CSS
  3. **外部样式**：`<link type="text/css" rel="stylesheet" href="url">`

### 2.2 CSS 选择器

#### 标签选择器
`标签名 { 属性: 值; }` —— 选中页面中所有该标签

#### ID 选择器
`#id值 { 属性: 值; }` —— 选中对应 id 的标签

#### 类选择器
`.class值 { 属性: 值; }` —— 选中对应 class 的标签

#### 后代选择器
`外层标签 内层标签 { 属性: 值; }` —— 选中外层内的所有内层标签

#### 伪类选择器

| 伪类                   | 作用                              | 示例                           |
| ---------------------- | --------------------------------- | ------------------------------ |
| `:root`                | 选中页面根元素（html）            | `:root {}`                     |
| `:not(标签)`           | 排除指定标签后的所有元素          | `:not(div) {}`                 |
| `:only-child`          | 选中父元素中唯一的子元素          | `父:only-child {}`             |
| `:first-child`         | 父元素的第一个子元素              | `父:first-child {}`            |
| `:last-child`          | 父元素的最后一个子元素            | `父:last-child {}`             |
| `:nth-child(n)`        | 父元素的正数第 n 个子元素         | `父:nth-child(2n+1) {}`        |
| `:nth-last-child(n)`   | 父元素的倒数第 n 个子元素         | `父:nth-last-child(2) {}`      |
| `:nth-of-type(n)`      | 父元素内同类型子元素的正数第 n 个 | `父 子:nth-of-type(2) {}`      |
| `:nth-last-of-type(n)` | 父元素内同类型子元素的倒数第 n 个 | `父 子:nth-last-of-type(1) {}` |
| `:empty`               | 没有子元素（包括文本节点）的元素  | `父:empty {}`                  |
| `:target`              | 匹配 URL 中 `#` 标识符对应的元素  | `:target {}`                   |

#### 伪元素选择器

| 伪元素     | 作用                                       |
| ---------- | ------------------------------------------ |
| `::before` | 在元素内容前插入新内容（需配合 `content`） |
| `::after`  | 在元素内容后插入新内容（需配合 `content`） |

#### 链接伪类选择器（按顺序：L-V-H-A）

| 伪类        | 作用           |
| ----------- | -------------- |
| `a:link`    | 未访问链接     |
| `a:visited` | 已访问链接     |
| `a:hover`   | 鼠标悬停       |
| `a:active`  | 鼠标按下未松开 |

#### 组合选择器
`选择器1, 选择器2 { 属性: 值; }` —— 同时选中多个选择器匹配的元素

### 2.3 CSS 选择器优先级权重

| 选择器类型                         | 权重值              |
| ---------------------------------- | ------------------- |
| 元素选择器 / 伪元素选择器          | 1                   |
| 类选择器 / 伪类选择器 / 属性选择器 | 10                  |
| ID 选择器                          | 100                 |
| 内联样式                           | 1000                |
| `!important`                       | 10000（最高优先级） |

### 2.4 CSS 文本样式规则

| 属性              | 作用           | 取值说明                                                     |
| :---------------- | :------------- | :----------------------------------------------------------- |
| `font-family`     | 字体族         | 多字体用逗号隔开；英文在前，中文在后；中文字体及含特殊字符的字体加引号；优先使用系统默认字体 |
| `font-size`       | 字体大小       | 关键字（`xx-small`~`xx-large`）或像素（px），默认 `medium`   |
| `font-weight`     | 字体粗细       | 关键字（`normal`=400、`lighter`、`bold`=700、`bolder`）或数值（100~900） |
| `color`           | 字体颜色       | 关键字（red/blue/green）、十六进制（#ffffff）、rgb（rgb(255,0,0)） |
| `font-style`      | 字体样式       | `normal`（默认）、`italic`（斜体）、`oblique`（倾斜）        |
| `text-decoration` | 文字修饰       | `none`（无）、`underline`（下划线）、`line-through`（删除线）、`overline`（顶划线） |
| `text-indent`     | 首行缩进       | 像素值                                                       |
| `text-align`      | 水平对齐       | `left`、`center`、`right`                                    |
| `text-transform`  | 字母大小写转换 | `none`、`uppercase`（全大写）、`lowercase`（全小写）、`capitalize`（首字母大写） |
| `line-height`     | 行高           | 像素值                                                       |
| `word-spacing`    | 单词间距       | 像素值（正/负）                                              |
| `letter-spacing`  | 字符间距       | 像素值（正/负）                                              |

------

### 2.5 CSS 样式

#### 尺寸与盒模型

| 属性       | 作用         | 取值                                                         |
| :--------- | :----------- | :----------------------------------------------------------- |
| `width`    | 元素宽度     | 关键字（`auto`）、px/cm、百分比                              |
| `height`   | 元素高度     | 同上                                                         |
| `overflow` | 内容溢出处理 | `visible`（溢出可见）、`hidden`（裁剪隐藏）、`scroll`（强制滚动条）、`auto`（需要时滚动条）、`inherit`（继承父级） |

#### 列表样式

| 属性              | 作用               | 示例                 |
| :---------------- | :----------------- | :------------------- |
| `list-style`      | 一次性设置列表属性 | `list-style: none;`  |
| `list-style-type` | 列表标记类型       | `none`（取消小圆点） |

#### 背景样式

| 属性                    | 作用         | 取值说明                                                     |
| :---------------------- | :----------- | :----------------------------------------------------------- |
| `background-color`      | 背景颜色     | 颜色名、十六进制、rgb、`transparent`（透明）                 |
| `background-image`      | 背景图片     | `url("路径")`                                                |
| `background-repeat`     | 背景重复方式 | `no-repeat`（不重复）、`repeat`（默认，平铺）、`repeat-x`（水平重复）、`repeat-y`（垂直重复）、`space`（均匀分布）、`round`（缩放后填满） |
| `background-size`       | 背景尺寸     | `auto`（原始大小）、`length`（数值）、`percentage`（百分比）、`cover`（覆盖容器）、`contain`（完整显示） |
| `background-position`   | 背景位置     | 关键字（`center`/`top left`等）、数值（`20px 30px`）、百分比（`50% 50%`） |
| `background-attachment` | 背景滚动方式 | `scroll`（随页面滚动）、`fixed`（相对视口固定）、`local`（随元素滚动） |

### 2.6 盒子模型

#### 宽高
- `width` / `height`：像素值（px/cm/pt）、百分比、`auto`（浏览器自动计算）

#### 内边距 `padding`
- 取值：像素、百分比、`auto`
- 简写规则（上、右、下、左）：
  - `padding: a b c d;` → 上 a、右 b、下 c、左 d
  - `padding: a b c;` → 上 a、左右 b、下 c
  - `padding: a b;` → 上下 a、左右 b
  - `padding: a;` → 四边均为 a

#### 外边距 `margin`
- 取值同 `padding`，可为负值
- 简写规则同上

#### 边框 `border`

| 属性           | 作用                              | 取值                                                         |
| -------------- | --------------------------------- | ------------------------------------------------------------ |
| `border-color` | 边框颜色（需配合 `border-style`） | 颜色名、rgb、十六进制；简写规则同 `padding`                  |
| `border-style` | 边框样式                          | `none`、`hidden`、`dotted`（点）、`dashed`（虚）、`solid`（实）、`double`（双线）、`groove`/`ridge`/`inset`/`outset`（3D效果） |
| `border-width` | 边框宽度                          | `medium`（默认）、`thin`、`thick`、像素值                    |

#### CSS 轮廓 `outline`
- `outline`：简写（宽度、样式、颜色）
- `outline-color` / `outline-style` / `outline-width`：同边框对应属性

#### 元素溢出 `overflow`
- `hidden`：溢出隐藏
- `scroll`：始终显示滚动条
- `auto`：溢出时显示滚动条

#### 圆角效果 `border-radius`
- 像素值 / 百分比（可设1~4个值）
- 胶囊：高度一半 / 50%
- 正圆：正方形边长一半

#### 阴影 `box-shadow`
- 语法：`box-shadow: x偏移 y偏移 模糊半径 扩散半径 颜色 [inset]`

---

### 2.7 盒子位置

#### 元素类型

| 类型   | 特点                               |
| ------ | ---------------------------------- |
| 块级   | 独占一行，宽默认100%               |
| 行内   | 可并排，宽高由内容撑开，不可设宽高 |
| 行内块 | 可并排，可设宽高                   |

#### 文档流
- 正常文档流：块级独占一行，行内从左向右排列
- 脱离文档流：元素“漂浮”于其他元素之上（如浮动、绝对定位）

#### 浮动 `float`
- `float: left` / `right` / `none`
- **清除浮动**：
  - `clear: left` / `right` / `both`
  - 父级添加 `overflow: hidden` 或使用伪元素：
    ```css
    .父级::after {
        content: "";
        display: block;
        clear: both;
    }
    ```

---

### 2.8 定位

| 定位方式 | `position` 值 | 说明                                       |
| -------- | ------------- | ------------------------------------------ |
| 静态定位 | `static`      | 默认，按文档流排列                         |
| 相对定位 | `relative`    | 相对于自身原位置偏移，不脱离文档流         |
| 绝对定位 | `absolute`    | 相对于最近的非 `static` 父元素，脱离文档流 |
| 固定定位 | `fixed`       | 相对于浏览器视口固定，脱离文档流           |

- 偏移属性：`top`、`right`、`bottom`、`left`

#### Flex 布局（父级设置 `display: flex`）

| 属性              | 作用             | 常用值                                                       |
| ----------------- | ---------------- | ------------------------------------------------------------ |
| `justify-content` | 主轴对齐         | `flex-start`、`flex-end`、`center`、`space-between`、`space-around` |
| `align-items`     | 侧轴对齐         | `stretch`、`center`、`flex-start`、`flex-end`                |
| `align-self`      | 单个子项侧轴对齐 | 同上                                                         |
| `flex-direction`  | 主轴方向         | `row`（左→右）、`column`（上→下）、`row-reverse`、`column-reverse` |
| `flex`            | 子项伸缩比例     | 数字（如 `1` 占剩余空间比例）                                |
| `flex-wrap`       | 换行             | `wrap`（换行）、`nowrap`（不换）                             |
| `align-content`   | 多行侧轴对齐     | 同 `justify-content`                                         |
| `z-index`         | 堆叠层级         | 数字（默认0，越大越靠上）                                    |

#### Grid 布局（父级设置 `display: grid`）

**显式网格**：
- `grid-template-columns`：列宽（如 `100px 1fr`、`repeat(3, 1fr)`）
- `grid-template-rows`：行高
- `grid-template-areas`：区域命名

**隐式网格**：
- `grid-auto-rows` / `grid-auto-columns`：自动生成的行高/列宽
- `minmax(min, max)`：尺寸范围

**对齐与间隙**：
- `justify-content` / `align-content`：网格整体对齐
- `gap`：网格线间隙

**子项定位**：
- `grid-column: 1 / 3`：从第1列到第3列前（或 `span 2` 跨2列）
- `grid-row: 1 / 4`：从第1行到第4行前
- `grid-area: 行起 / 列起 / 行终 / 列终`

**区域命名法**：
```css
父级 {
    grid-template-areas:
        "header header"
        "sidebar main";
}
子项 { grid-area: header; }
```

---

### 2.9 CSS 精灵

将多个小图标合并为一张大图，减少 HTTP 请求。

**实现步骤**：
1. 创建盒子（宽高等于小图标尺寸）
2. 设置背景图为精灵图
3. 通过 `background-position` 移动背景图（负值，取小图标左上角坐标）

**示例**：
```css
.icon {
    width: 32px;
    height: 32px;
    background-image: url(sprite.png);
    background-position: -64px -128px;
}
```

### 2.9 CSS 精灵

将多个小图标合并为一张大图，减少 HTTP 请求。

**实现步骤**：
1. 创建盒子（宽高等于小图标尺寸）
2. 设置背景图为精灵图
3. 通过 `background-position` 移动背景图（负值，取小图标左上角坐标）

**示例**：
```css
.icon {
    width: 32px;
    height: 32px;
    background-image: url(sprite.png);
    background-position: -64px -128px;
}
```

---

### 2.10 CSS 字体图标

**资源网站**：https://www.iconfont.cn/

**使用流程**：
1. 上传 SVG 矢量图片（去除颜色）或从官方素材库选择图标
2. 加入购物车 → 添加至项目 → 下载至本地
3. 解压后包含文件：
   - `iconfont.css`：样式文件
   - `iconfont.ttf` / `.woff` / `.woff2`：字体文件
   - `demoindex.html` / `demoindex.css`：示例
4. 在 HTML 中引入 CSS：
   ```html
   <link rel="stylesheet" href="iconfont.css">
   ```
5. 使用字体图标：
   ```html
   <span class="iconfont icon-xx"></span>
   ```

---

### 2.11 CSS 修饰属性

| 属性             | 作用         | 常用值                                                       |
| ---------------- | ------------ | ------------------------------------------------------------ |
| `vertical-align` | 垂直对齐     | `baseline`（基线）、`top`、`middle`、`bottom`                |
| `transition`     | 过渡动画     | `属性 时间`（如 `all 0.3s`），加在元素本身，配合 `:hover`    |
| `opacity`        | 透明度       | 0（透明）~ 1（不透明）                                       |
| `cursor`         | 鼠标悬停样式 | `default`（箭头）、`pointer`（小手）、`text`（工字）、`move`（十字） |

**元素显示与隐藏对比**：

| 方法                 | 占空间 | 可交互           | 支持动画 | 恢复显示              |
| -------------------- | ------ | ---------------- | -------- | --------------------- |
| `display: none`      | 否     | 否               | 否       | `display: block`      |
| `visibility: hidden` | 是     | 否               | 否       | `visibility: visible` |
| `opacity: 0`         | 是     | 是（需配合禁用） | 是       | `opacity: 1`          |

---

### 3. 网站资源

**Favicon 图标**（浏览器标签页图标）：
```html
<link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
```

---

### 4. 平面转换 `transform`

| 函数                                  | 作用                     | 示例                                       |
| ------------------------------------- | ------------------------ | ------------------------------------------ |
| `translate(x, y)`                     | 平移（px / %，可为负）   | `translate(50px, -20px)`                   |
| `rotate(角度deg)`                     | 旋转（可为负）           | `rotate(45deg)`                            |
| `transform-origin`                    | 变换原点（方位/像素）    | `transform-origin: left top;`              |
| `scale(x, y)`                         | 缩放（>1 放大，<1 缩小） | `scale(1.5, 0.8)`                          |
| `skew(角度deg)`                       | 倾斜（可为负）           | `skew(10deg)`                              |
| `background-image: linear-gradient()` | 线性渐变                 | `to right, red 0%, blue 100%`              |
| `background-image: radial-gradient()` | 径向渐变                 | `circle at center, red, blue`              |
| 多重转换                              | 先平移后旋转             | `transform: translate(10px) rotate(30deg)` |

**线性渐变示例**：
```css
/* 方向写法 */
background-image: linear-gradient(to right, red, blue);
/* 角度写法 */
background-image: linear-gradient(45deg, red 30%, blue 70%);
```

**径向渐变示例**：
```css
background-image: radial-gradient(100px at center, red, blue);
```

---

### 5. 空间转换（3D）

**透视距离**（近大远小，添加给父级）：
```css
perspective: 800px;  /* 常用 800~1200px */
```

| 函数                      | 作用                    | 示例                                     |
| ------------------------- | ----------------------- | ---------------------------------------- |
| `translate3d(x, y, z)`    | 3D 平移（px / %）       | `translate3d(10px, 20px, -50px)`         |
| `rotateZ(角度)`           | 绕 Z 轴旋转（平面效果） | `rotateZ(45deg)`                         |
| `rotateX(角度)`           | 绕 X 轴旋转（上下翻转） | `rotateX(60deg)`                         |
| `rotateY(角度)`           | 绕 Y 轴旋转（左右翻转） | `rotateY(60deg)`                         |
| `rotate3d(x, y, z, 角度)` | 绕任意轴旋转            | `rotate3d(1, 1, 0, 45deg)`               |
| `scale3d(x, y, z)`        | 3D 缩放                 | `scale3d(1.5, 1.5, 1)`                   |
| `transform-origin`        | 3D 变换原点             | 同平面转换                               |
| `transform-style`（父级） | 子级是否处于 3D 空间    | `flat`（平面）、`preserve-3d`（3D 空间） |

**注意**：`skew` 和渐变函数仅适用于平面转换，不适用于空间转换。

---

## 6. 动画 `animation`（多状态过渡）

### 6.1 定义动画

使用 `@keyframes` 定义动画关键帧：

```css
/* 两状态简写 */
@keyframes 动画名 {
    from { 属性: 值; }
    to   { 属性: 值; }
}

/* 多状态百分比 */
@keyframes 动画名 {
    0%   { 属性: 值; }
    10%  { 属性: 值; }
    ...
    100% { 属性: 值; }
}
```

### 6.2 使用动画

`animation` 简写属性（顺序不限，但时长和延迟注意区分）：

```css
animation: 动画名 持续时间 缓动函数 延迟时间 播放次数 方向 填充模式;
```

**各参数说明**：

| 参数      | 取值                           | 说明                                              |
| --------- | ------------------------------ | ------------------------------------------------- |
| 动画名    | `@keyframes` 定义的名字        | 必填                                              |
| 持续时间  | `0.5s`、`2s`                   | 必填，单位 s/ms                                   |
| 缓动函数  | `linear`、`ease`、`steps(n)`   | `steps()` 配合精灵图实现逐帧动画                  |
| 延迟时间  | `0s`、`1s`                     | 可选，默认 0s                                     |
| 播放次数  | `1`、`3`、`infinite`           | `infinite` 无限循环                               |
| 方向      | `normal`、`alternate`          | `alternate` 来回反向播放                          |
| 填充模式  | `forwards`、`backwards`        | `forwards` 停在最后状态；`backwards` 停在开始状态 |
| 暂停/播放 | `animation-play-state: paused` | 常用于 `:hover` 时暂停动画                        |

**示例**：

```css
/* 定义 */
@keyframes slide {
    from { transform: translateX(0); }
    to   { transform: translateX(100px); }
}

/* 使用 */
.box {
    animation: slide 2s linear 0.5s infinite alternate forwards;
}

/* 悬停暂停 */
.box:hover {
    animation-play-state: paused;
}
```
