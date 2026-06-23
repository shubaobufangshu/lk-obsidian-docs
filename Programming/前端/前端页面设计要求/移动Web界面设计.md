# 移动Web界面设计

---

## 一、基础知识

### 1. 屏幕分辨率
- **物理分辨率**：屏幕实际像素点数（如iPhone 14 2532×1170px）。
- **逻辑分辨率**：CSS渲染单位，通常为物理分辨率的1/2（如Retina屏），网页布局以此为准。

### 2. 视口（Viewport）
控制移动端显示区域，标准配置：
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```
- `width=device-width`：视口宽度=设备逻辑宽度。
- `initial-scale=1.0`：初始缩放为1。
- `maximum-scale=1.0` / `user-scalable=no`：禁止缩放，稳定布局。

### 3. 二倍图
Retina屏物理像素为逻辑2倍，普通图模糊。用**2倍尺寸图**（如逻辑100×100px，图用200×200px），再通过CSS设逻辑尺寸，保证清晰。

---

## 二、适配方案

### （一）宽度适配（宽自适应，高固定/内容撑开）
#### 1. 百分比布局
子元素宽设百分比（如`width:50%`），基于父元素宽度自适应。
> 注意：父宽须明确；`padding/margin`百分比参照父宽。

#### 2. Flex布局
通过`display:flex`定义容器，控制子项排列。
- **容器属性**：
  - `flex-direction`：主轴方向（`row`水平/`column`垂直）。
  - `justify-content`：主轴对齐（`flex-start`左/`center`中/`space-between`两端）。
  - `align-items`：交叉轴对齐（`center`居中/`stretch`拉伸）。
- **子项属性**：
  - `flex:1`：均分剩余空间。
  - `align-self`：单独设对齐。

---

### （二）等比适配（宽高等比缩放）
#### 1. rem单位
相对根元素字号，`1rem = 1html字号`。常设`html`字号为视口宽的1/10（如375px→`font-size:37.5px`），元素用`rem`，随视口等比缩放。

##### 设置方式
- **媒体查询**：检测宽度动态设值：
  ```css
  @media (width:375px) { html{font-size:37.5px} }
  @media (width:414px) { html{font-size:41.4px} }
  ```
- **flexible.js**（淘宝开源）：自动检测并设置，无需手写查询：
  ```html
  <script src="https://cdn.bootcdn.net/ajax/libs/flexible.js/0.3.2/flexible.min.js"></script>
  ```

##### Less预处理
支持变量、运算、嵌套，需编译为CSS（如VS Code插件`Easy Less`）。
- **变量**：`@color:#333;` → `color:@color;`
- **运算**：`width:(100rem / 2);` 或 `width:100rem ./ 2;`
- **嵌套**：`.box{ .child{color:red} }` → `.box .child{...}`
- **导入**：`@import "common.less";`
- **导出**：首行`// out: ./css/style.css`；禁止：`// out: false`

#### 2. vw/vh单位
- `vw`：视口宽的1%（如375px→`1vw=3.75px`）。
- `vh`：视口高的1%。
- 优势：直接基于视口，无需JS。如`width:50vw;`（宽为视口一半）。

---

## 三、响应式网页
一套代码适配PC/移动，通过**媒体查询**按屏幕尺寸切换样式。

### 1. 媒体查询语法
```css
@media 关键字 媒体类型 and (媒体特性) {
  选择器 { css }
}
```
- **关键字**：`and`、`or`、`not`。
- **媒体类型**：`screen`、`print`、`speech`、`all`。
- **媒体特性**：
  - `max-width:600px`：≤600px生效。
  - `min-width:768px`：≥768px生效。
  - `orientation:portrait`竖屏 / `landscape`横屏。

示例：
```css
@media screen and (max-width:768px) {
  .box { width:100%; }
}
```

### 2. 外部CSS引入
按条件加载样式：
```html
<link rel="stylesheet" media="screen and (max-width:768px)" href="mobile.css">
```

---

## 四、Bootstrap（CSS框架）
基于HTML/CSS/JS的响应式框架，[Bootstrap中文网](sslocal://flow/file_open?url=https%3A%2F%2Fwww.bootcss.com%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)。

### 1. 栅格系统（核心）
容器12列，通过类名控列数。
| 断点 | 尺寸范围 | 类前缀    |
| ---- | -------- | --------- |
| xs   | <576px   | .col-     |
| sm   | ≥576px   | .col-sm-  |
| md   | ≥768px   | .col-md-  |
| lg   | ≥992px   | .col-lg-  |
| xl   | ≥1200px  | .col-xl-  |
| xxl  | ≥1400px  | .col-xxl- |

示例：
```html
<div class="container">
  <div class="row">
    <div class="col-sm-6 col-md-4">内容1</div>
    <div class="col-sm-6 col-md-4">内容2</div>
    <div class="col-sm-12 col-md-4">内容3</div>
  </div>
</div>
```

### 2. 全局样式
#### 按钮
- 基础：`.btn`
- 颜色：`.btn-primary`（蓝）、`.btn-success`（绿）、`.btn-warning`（黄）、`.btn-danger`（红）
- 尺寸：`.btn-sm`、`.btn-lg`
- 示例：`<button class="btn btn-primary btn-lg">按钮</button>`

#### 表格
- 基础：`.table`
- 样式：`.table-striped`（隔行变色）、`.table-bordered`（边框）、`.table-hover`（悬停高亮）
- 示例：`<table class="table table-striped table-hover">...</table>`

### 3. 组件
引入资源：
```html
<link href="https://cdn.bootcdn.net/ajax/libs/bootstrap/5.3.0/css/bootstrap.min.css" rel="stylesheet">
<script src="https://cdn.bootcdn.net/ajax/libs/bootstrap/5.3.0/js/bootstrap.bundle.min.js"></script>
```
常用组件（导航栏、卡片、模态框等）：复制结构，改内容即可。

### 4. 字体图标（Bootstrap Icons）
引入：
```html
<link rel="stylesheet" href="https://cdn.bootcdn.net/ajax/libs/bootstrap-icons/1.10.0/font/bootstrap-icons.css">
```
使用：`<i class="bi bi-house"></i>`（图标名如`bi-search`、`bi-heart`）。