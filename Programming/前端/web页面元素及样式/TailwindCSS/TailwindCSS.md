## 认识 Tailwind CSS
### 1.1 什么是 Tailwind CSS
Tailwind CSS 是一个**工具优先（Utility-First）** 的原子化 CSS 框架，它预定义了大量单功能的 CSS 工具类，你可以直接在 HTML 标签中组合这些类名，快速实现页面样式，无需编写大量自定义 CSS。
和传统 CSS/SCSS 方案相比，它的核心差异在于：
- 传统方案：先给元素命名类名，再在 CSS 文件中编写该类的样式
- Tailwind 方案：直接在 HTML 中使用预定义的工具类，无需离开标签即可完成样式开发
### 1.2 v4 版本核心优势与重大变更
v4 版本是 Tailwind 发展史上的重大重构，核心基于 Rust 编写的 **Oxide 引擎**，带来了质的飞跃：
| 特性            | v3 版本 | v4 版本 | 提升幅度 |
| --------------- | ------- | ------- | -------- |
| 全量构建速度    | ~378ms  | ~100ms  | 3.8倍    |
| 增量构建速度    | ~44ms   | ~5ms    | 8.8倍    |
| 生产环境CSS体积 | ~24KB   | ~18KB   | 缩减25%  |
| 内存占用        | ~180MB  | ~90MB   | 降低50%  |

**核心语法与架构变更（重点）**：
1.  **CSS 优先配置**：废弃了 `tailwind.config.js` 为主的配置方式，改为在 CSS 文件中通过 `@theme` 等原生 CSS 指令完成配置
2.  **引入方式更新**：移除 `@tailwind base/components/utilities` 指令，改为原生 `@import "tailwindcss";` 引入
3.  **内置核心能力**：原生支持容器查询、CSS 嵌套、`@import` 导入，无需额外插件
4.  **语法命名对齐原生CSS**：如渐变类名从 `bg-gradient-to-*` 改为 `bg-linear-to-*`，贴合 CSS `linear-gradient` 语法
5.  **默认行为优化**：边框默认继承文本颜色、占位符默认半透明继承文本色、`ring` 默认宽度从 3px 改为 1px
### 1.3 前置环境要求
- Node.js 版本：18.0 及以上（推荐 LTS 版本）
- 浏览器支持：现代浏览器（Safari 16.4+、Chrome 111+、Firefox 128+），旧版浏览器建议继续使用 v3.4 版本
- 编辑器推荐：VS Code + Tailwind CSS IntelliSense 插件（提供智能提示、语法高亮与自动补全）
## 环境搭建
我们提供4种主流场景的搭建方案，你可以根据自己的项目类型选择。

### 2.1 方案1：CDN 快速原型开发
适合快速 demo、原型验证，无需构建工具，直接在 HTML 中引入即可：
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>Tailwind CDN 示例</title>
  <!-- 引入 Tailwind CSS v4 CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- 自定义主题（可选） -->
  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            primary: '#165DFF',
          },
        },
      }
    }
  </script>
</head>
<body>
  <h1 class="text-3xl font-bold text-primary text-center mt-10">
    Hello Tailwind CSS v4!
  </h1>
</body>
</html>
```
> 注意：CDN 方式不适合生产环境，无法使用完整的高级特性，仅推荐用于原型开发。

### 2.2 方案2：Vite 项目集成（推荐，全框架通用）
Vite 是目前前端最主流的构建工具，Tailwind v4 提供了官方专属插件，配置极简。

#### 步骤1：创建 Vite 项目（已有项目可跳过）
```bash
# 创建 Vite 项目（支持 Vue/React/Preact/Svelte 等）
npm create vite@latest tailwind-demo
# 进入项目目录
cd tailwind-demo
# 安装基础依赖
npm install
```

#### 步骤2：安装 Tailwind 依赖
```bash
# 安装 Tailwind CSS 核心与 Vite 插件
npm install -D tailwindcss @tailwindcss/vite 
```

#### 步骤3：配置 Vite
修改项目根目录的 `vite.config.js` / `vite.config.ts`，引入 Tailwind 插件：
```javascript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue' // React 项目改为 react 插件
import tailwindcss from '@tailwindcss/vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue(), tailwindcss()], // 加入 tailwindcss 插件
})
```

#### 步骤4：引入 Tailwind 入口 CSS
在项目全局 CSS 文件（通常是 `src/style.css` / `src/index.css`）中，写入以下内容：
```css
/* 核心：引入 Tailwind CSS v4，替代 v3 的 @tailwind 指令 */
@import "tailwindcss";

/* 自定义主题（后续会详细讲解） */
@theme {
  --color-primary: #165DFF;
  --font-family-sans: 'Inter', system-ui, sans-serif;
}
```

#### 步骤5：验证是否生效
修改 `src/App.vue` / `src/App.jsx`，写入测试代码：
```vue
<template>
  <div class="min-h-screen bg-gray-50 flex items-center justify-center">
    <div class="text-center">
      <h1 class="text-4xl font-bold text-primary mb-4">
        Tailwind CSS v4 + Vite 配置成功！
      </h1>
      <p class="text-gray-600 text-lg mb-6">原子化CSS开发，从未如此高效</p>
      <button class="bg-primary hover:bg-primary/90 text-white px-6 py-3 rounded-lg font-medium transition-all">
        开始使用
      </button>
    </div>
  </div>
</template>
```

启动项目 `npm run dev`，浏览器中即可看到样式生效，配置完成。

### 2.3 方案3：Webpack 项目集成
v4.2.0 版本新增了官方 Webpack 插件，无需手动配置 PostCSS，极简集成：
```bash
# 安装依赖
npm install -D tailwindcss @tailwindcss/webpack
```

修改 `webpack.config.js` 配置：
```javascript
const TailwindPlugin = require('@tailwindcss/webpack');

module.exports = {
  // 其他webpack配置...
  plugins: [
    new TailwindPlugin({
      // 可选：配置入口CSS文件，默认 ./src/index.css
      entry: './src/style.css',
    }),
  ],
};
```

在入口 CSS 文件中写入 `@import "tailwindcss";`，即可正常使用，和 Vite 方案完全一致。

### 2.4 方案4：原生 HTML/CLI 方式
适合纯静态 HTML 项目，使用官方 CLI 工具构建：
1.  初始化项目
    ```bash
    # 初始化package.json
    npm init -y
    # 安装依赖
    npm install -D tailwindcss
    ```
2.  创建入口 CSS 文件 `src/input.css`
    ```css
    @import "tailwindcss";
    ```
3.  执行构建命令
    ```bash
    # 开发模式：监听文件变化，实时构建
    npx @tailwindcss/cli -i ./src/input.css -o ./dist/output.css --watch
    
    # 生产模式：压缩优化
    npx @tailwindcss/cli -i ./src/input.css -o ./dist/output.css --minify
    ```
4.  在 HTML 中引入构建后的 CSS 文件即可使用。
### 2.5核心配置文件详解

| 配置项       | 作用                | 核心规则                                      |
| --------- | ----------------- | ----------------------------------------- |
| content   | 模板文件扫描路径，用于摇树优化   | 必须覆盖所有包含Tailwind类名的文件，否则生产环境会丢失样式         |
| theme     | 主题配置，用于定制设计系统     | extend内为扩展默认主题，直接写为覆盖默认主题                 |
| plugins   | 插件注册，扩展Tailwind能力 | 支持官方插件、第三方插件、自定义插件                        |
| darkMode  | 深色模式配置            | 可选值：`media`（跟随系统）/ `class`（手动切换），默认关闭     |
| prefix    | 类名前缀              | 用于避免与其他UI库类名冲突，如 `tw-`，生成类名为 `tw-flex`    |
| important | 全局!important      | 可选值：`true` / 选择器（如`#app`），提升样式优先级，解决UI库冲突 |
#### postcss.config.js 作用
PostCSS 是 Tailwind 的运行依赖，用于解析和转换CSS，默认配置已满足绝大多数场景，无需额外修改：
```javascript
export default {
  plugins: {
    tailwindcss: {},
    autoprefixer: {}, // 自动添加浏览器前缀，兼容不同浏览器
  },
}
```
## 核心语法与基础规则
### 3.1 Utility-First 核心思想
Tailwind 的核心是**原子化工具类**，每个类名对应一条唯一的 CSS 规则，通过组合类名实现复杂样式。

我们用一个按钮示例，对比传统 CSS 和 Tailwind 的写法差异：

#### 传统 CSS 写法
```html
<!-- HTML -->
<button class="btn-primary">点击按钮</button>

<!-- 单独的CSS文件 -->
<style>
.btn-primary {
  background-color: #165DFF;
  color: white;
  font-weight: 500;
  padding: 12px 24px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  transition: background-color 0.2s;
}
.btn-primary:hover {
  background-color: #0e42c9;
}
</style>
```

#### Tailwind 写法
```html
<!-- 无需单独CSS，直接在标签内完成所有样式 -->
<button class="bg-[#165DFF] hover:bg-[#0e42c9] text-white font-medium px-6 py-3 rounded-lg border-none cursor-pointer transition-colors">
  点击按钮
</button>
```

可以看到，Tailwind 让样式和结构绑定在一起，无需切换文件，无需思考类名命名，开发效率大幅提升。

### 3.2 类名命名规则
Tailwind 的类名遵循**语义化简写**规则，记忆成本极低，核心规律如下：
1.  **属性简写**：CSS 属性名的缩写，如 `width` → `w`，`padding` → `p`，`margin` → `m`，`background` → `bg`
2.  **值映射**：属性值直接跟在简写后，如 `width: 100%` → `w-full`，`padding: 16px` → `p-4`
3.  **方向指定**：通过后缀指定方向，如 `padding-top` → `pt`，`margin-left` → `ml`，`border-right` → `border-r`
4.  **状态变体**：通过 `前缀:` 给类名添加状态限定，如 `hover:bg-blue-600`（仅鼠标悬停时生效）
Tailwind 默认使用 `rem` 作为核心单位，基准为 `1rem = 16px`

|类名后缀|对应 rem 值|对应 px 值|
|:--|:--|:--|
|0|0rem|0px|
|1|0.25rem|4px|
|2|0.5rem|8px|
|3|0.75rem|12px|
|4|1rem|16px|
|6|1.5rem|24px|
|8|2rem|32px|
|10|2.5rem|40px|
**常用简写对照表**

|CSS 属性|Tailwind 简写|常用示例|
|:--|:--|:--|
|width|w|w-1/2、w-full、w-screen|
|height|h|h-10、h-full、h-screen|
|padding|p|p-4、px-2、py-3、pt-1|
|margin|m|m-4、mx-auto、my-2、mb-4|
|background|bg|bg-white、bg-primary、bg-opacity-50|
|text|text|text-lg、text-center、text-gray-700|
|font|font|font-bold、font-medium、font-sans|
|border|border|border、border-gray-200、border-r-2|
|rounded|rounded|rounded-lg、rounded-full、rounded-t-md|
|display|直接写属性|block、inline-block、flex、grid|

### 3.3 核心指令详解
Tailwind v4 提供了多个原生 CSS 指令，用于配置、扩展和定制能力，这是 v4 版本的核心语法，必须掌握。

#### 1. @import "tailwindcss";
**核心入口指令**，替代 v3 版本的 `@tailwind base; @tailwind components; @tailwind utilities;`，必须放在全局 CSS 文件的最顶部，用于引入 Tailwind 的所有核心样式、工具类与能力。

#### 2. @theme 主题配置指令
v4 版本最核心的变更，**替代了 v3 的 tailwind.config.js 主题配置**，所有设计令牌（颜色、间距、字体、圆角、阴影等）都在 `@theme` 块中定义，自动生成对应的工具类。

**基础用法示例**：
```css
@import "tailwindcss";

@theme {
  /* 自定义颜色：自动生成 bg-*、text-*、border-* 等工具类 */
  --color-primary: #165DFF;
  --color-primary-light: #e8f3ff;
  --color-success: #00b42a;
  --color-danger: #ff4d4f;

  /* 自定义间距：自动生成 w-*、h-*、p-*、m-* 等工具类 */
  --spacing-18: 4.5rem; /* 72px */
  --spacing-128: 32rem; /* 512px */

  /* 自定义字体 */
  --font-family-sans: 'Inter', 'Noto Sans SC', system-ui, sans-serif;
  --font-family-mono: 'Fira Code', monospace;

  /* 自定义圆角 */
  --radius-xl: 12px;
  --radius-2xl: 16px;

  /* 自定义断点（响应式） */
  --breakpoint-2xl: 1536px;
}
```

定义完成后，你可以直接在 HTML 中使用 `bg-primary`、`p-18`、`rounded-2xl`、`2xl:grid-cols-4` 等类名，Tailwind 会自动生成对应的样式。

#### 3. @utility 自定义工具类指令
v4 新增指令，用于在 CSS 中直接定义自定义工具类，无需编写 JavaScript 插件，支持 CSS 嵌套、变体与响应式。

**语法示例**：
```css
@import "tailwindcss";

/* 自定义内容可见性工具类 */
@utility content-auto {
  content-visibility: auto;
}

/* 自定义文本渐变工具类，支持嵌套 */
@utility text-gradient {
  background-clip: text;
  -webkit-background-clip: text;
  color: transparent;
  
  /* 支持嵌套，自动生成 hover:text-gradient 变体 */
  &:hover {
    opacity: 0.8;
  }
}

/* 自定义卡片阴影工具类 */
@utility card-shadow {
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}
```

定义完成后，直接在 HTML 中使用 `content-auto`、`text-gradient`、`card-shadow` 即可，和原生工具类完全一致，支持 `hover:`、`dark:`、响应式前缀等所有变体。

#### 4. @source 内容扫描指令
用于手动指定 Tailwind 需要扫描的文件路径，告诉 Tailwind 哪些文件中使用了工具类，避免生产环境打包无用样式。

v4 版本默认会自动扫描项目中的 HTML、Vue、JSX、TSX 等文件，大多数场景无需手动配置，仅特殊场景需要手动指定：
```css
@import "tailwindcss";

/* 手动指定扫描路径 */
@source "../components/**/*.{html,jsx,vue}";
@source "./pages/**/*.html";
```

#### 5. @apply 样式复用指令
用于在 CSS 中把多个 Tailwind 工具类的样式，合并到一个自定义选择器中，适合复用重复的样式组合。

**示例**：
```css
@import "tailwindcss";

/* 自定义按钮类，复用多个工具类 */
.btn-primary {
  @apply bg-primary hover:bg-primary/90 text-white font-medium px-6 py-3 rounded-lg transition-all;
}

/* 自定义卡片类 */
.card-base {
  @apply bg-white rounded-xl card-shadow p-6 border border-gray-100;
}
```

使用时直接在 HTML 中写入 `class="btn-primary"` 即可，和原生类名效果一致。

> 注意：@apply 适合简单的样式复用，复杂组件推荐使用框架组件（Vue/React组件）封装，更利于维护。

#### 6. @layer 层级指令
用于指定样式的层级优先级，可选值：`base`、`components`、`utilities`，优先级从低到高，`utilities` 层级最高，可以覆盖前面的样式。

**示例**：
```css
@import "tailwindcss";

/* base层级：全局基础样式，默认优先级最低 */
@layer base {
  body {
    @apply font-sans text-gray-800 bg-gray-50;
  }
  a {
    @apply text-primary hover:underline;
  }
}

/* components层级：自定义组件样式 */
@layer components {
  .btn-secondary {
    @apply bg-white border border-gray-200 text-gray-700 px-6 py-3 rounded-lg hover:bg-gray-50 transition-all;
  }
}

/* utilities层级：自定义工具类，优先级最高 */
@layer utilities {
  .text-shadow {
    text-shadow: 0 2px 4px rgba(0,0,0,0.1);
  }
}
```

### 3.4 任意值语法（方括号语法）
当 Tailwind 预设的工具类无法满足你的需求时，无需编写自定义 CSS，直接使用**方括号语法**，写入任意 CSS 值，Tailwind 会自动生成对应的工具类。

**基础用法示例**：
```html
<!-- 自定义宽度 -->
<div class="w-[200px]"></div>
<!-- 自定义颜色 -->
<p class="text-[#123456] bg-[rgb(240,248,255)]"></p>
<!-- 自定义定位 -->
<div class="top-[10vh] left-[50%] -translate-x-1/2 fixed"></div>
<!-- 自定义CSS属性 -->
<div class="backdrop-blur-[10px] content-visibility-auto"></div>
<!-- 带CSS变量 -->
<div class="bg-[color:var(--my-custom-color)]"></div>
```

任意值语法支持所有 CSS 属性，甚至可以写入复杂的 CSS 函数，完全覆盖自定义 CSS 的能力，无需离开 HTML 即可实现任何样式。

### 3.5 变体前缀：状态与条件样式
变体是 Tailwind 的核心能力之一，通过 `前缀:` 语法，给工具类添加状态、响应式、主题等条件限定，仅当条件满足时样式才生效。

#### 1. 交互状态变体
最常用的变体，用于处理用户交互状态，和 CSS 伪类一一对应：

| 变体前缀    | 对应CSS伪类 | 说明                    |
| ----------- | ----------- | ----------------------- |
| `hover:`    | `:hover`    | 鼠标悬停时生效          |
| `focus:`    | `:focus`    | 元素获得焦点时生效      |
| `active:`   | `:active`   | 元素被点击激活时生效    |
| `disabled:` | `:disabled` | 元素禁用时生效          |
| `visited:`  | `:visited`  | 链接被访问后生效        |
| `checked:`  | `:checked`  | 单选/复选框被选中时生效 |

**示例**：
```html
<button class="bg-blue-500 hover:bg-blue-600 active:bg-blue-700 focus:ring-2 focus:ring-blue-300 disabled:opacity-50 disabled:cursor-not-allowed text-white px-4 py-2 rounded">
  交互按钮
</button>
```

#### 2. 结构伪类变体
用于匹配元素的结构位置，对应 CSS 结构伪类：

| 变体前缀 | 对应CSS伪类        | 说明               |
| -------- | ------------------ | ------------------ |
| `first:` | `:first-child`     | 第一个子元素生效   |
| `last:`  | `:last-child`      | 最后一个子元素生效 |
| `even:`  | `:nth-child(even)` | 偶数子元素生效     |
| `odd:`   | `:nth-child(odd)`  | 奇数子元素生效     |
| `empty:` | `:empty`           | 空元素生效         |

**示例**：
```html
<ul class="list-none">
  <li class="py-2 border-b border-gray-200 last:border-b-0">列表项1</li>
  <li class="py-2 border-b border-gray-200 last:border-b-0">列表项2</li>
  <li class="py-2 border-b border-gray-200 last:border-b-0">列表项3</li>
</ul>
```

#### 3. 分组变体 `group-*`
用于实现父元素 hover 时，子元素样式变化，非常适合卡片、列表等场景。

**用法**：
1.  给父元素添加 `group` 类名
2.  给子元素添加 `group-hover:`、`group-focus:` 等前缀

**示例**：
```html
<div class="group bg-white p-6 rounded-xl border border-gray-200 hover:border-primary transition-all">
  <h3 class="text-xl font-bold group-hover:text-primary transition-colors">卡片标题</h3>
  <p class="text-gray-600 mt-2">鼠标悬停父卡片，标题会变色</p>
</div>
```

#### 4. 变体叠加
Tailwind 支持多个变体前缀叠加，优先级从右到左，实现更复杂的条件样式。

**示例**：
```html
<!-- 仅在大屏下，鼠标悬停时生效 -->
<button class="lg:hover:bg-blue-600"></button>

<!-- 仅在深色模式下，鼠标悬停时生效 -->
<div class="dark:hover:bg-zinc-800"></div>

<!-- 仅在大屏下，偶数项，鼠标悬停时生效 -->
<div class="lg:even:hover:bg-gray-50"></div>
```

## 核心工具类全解
这一部分我们分类讲解 Tailwind 最常用的工具类，覆盖90%的开发场景，配合示例快速上手。

### 4.1 布局类工具类
布局是页面开发的基础，Tailwind 提供了完整的 Flex、Grid、定位、容器等布局工具类。

#### 1. 显示与隐藏
| 类名           | CSS属性                 | 说明               |
| -------------- | ----------------------- | ------------------ |
| `block`        | `display: block`        | 块级元素           |
| `inline-block` | `display: inline-block` | 行内块元素         |
| `inline`       | `display: inline`       | 行内元素           |
| `flex`         | `display: flex`         | 弹性布局           |
| `inline-flex`  | `display: inline-flex`  | 行内弹性布局       |
| `grid`         | `display: grid`         | 网格布局           |
| `inline-grid`  | `display: inline-grid`  | 行内网格布局       |
| `hidden`       | `display: none`         | 隐藏元素           |
| `visible`      | `visibility: visible`   | 可见               |
| `invisible`    | `visibility: hidden`    | 不可见（保留占位） |

#### 2. Flex 弹性布局
| 分类       | 常用类名            | 说明                         |
| ---------- | ------------------- | ---------------------------- |
| 主轴方向   | `flex-row`          | 水平排列（默认）             |
|            | `flex-col`          | 垂直排列                     |
|            | `flex-row-reverse`  | 水平反向排列                 |
|            | `flex-col-reverse`  | 垂直反向排列                 |
| 换行       | `flex-wrap`         | 自动换行                     |
|            | `flex-nowrap`       | 不换行（默认）               |
|            | `flex-wrap-reverse` | 反向换行                     |
| 主轴对齐   | `justify-start`     | 主轴起点对齐（默认）         |
|            | `justify-center`    | 主轴居中对齐                 |
|            | `justify-end`       | 主轴终点对齐                 |
|            | `justify-between`   | 两端对齐，间距均分           |
|            | `justify-around`    | 元素两侧间距相等             |
|            | `justify-evenly`    | 元素之间间距完全相等         |
| 交叉轴对齐 | `items-start`       | 交叉轴起点对齐               |
|            | `items-center`      | 交叉轴居中对齐               |
|            | `items-end`         | 交叉轴终点对齐               |
|            | `items-baseline`    | 基线对齐                     |
|            | `items-stretch`     | 拉伸填充（默认）             |
| 单独对齐   | `self-auto`         | 继承父级（默认）             |
|            | `self-center`       | 单独居中对齐                 |
|            | `self-start`        | 单独起点对齐                 |
|            | `self-end`          | 单独终点对齐                 |
| 弹性占比   | `flex-1`            | `flex: 1 1 0%`，占满剩余空间 |
|            | `flex-auto`         | `flex: 1 1 auto`，自动适配   |
|            | `flex-none`         | `flex: none`，不伸缩         |

**Flex 居中示例（最常用）**：
```html
<!-- 水平垂直居中 -->
<div class="flex items-center justify-center min-h-screen">
  <div>我在页面正中间</div>
</div>

<!-- 两端对齐导航栏 -->
<nav class="flex items-center justify-between px-6 py-4 border-b">
  <div class="logo">LOGO</div>
  <ul class="flex items-center gap-6">
    <li>首页</li>
    <li>产品</li>
    <li>关于我们</li>
  </ul>
</nav>
```

#### 3. Grid 网格布局
| 分类   | 常用类名                       | 说明             |
| ------ | ------------------------------ | ---------------- |
| 列数   | `grid-cols-1` ~ `grid-cols-12` | 网格列数，1-12列 |
| 行数   | `grid-rows-1` ~ `grid-rows-6`  | 网格行数，1-6行  |
| 列跨度 | `col-span-1` ~ `col-span-12`   | 元素跨列数       |
|        | `col-span-full`                | 跨满所有列       |
| 行跨度 | `row-span-1` ~ `row-span-6`    | 元素跨行数       |
|        | `row-span-full`                | 跨满所有行       |
| 间距   | `gap-*`                        | 行列统一间距     |
|        | `gap-x-*`                      | 列间距           |
|        | `gap-y-*`                      | 行间距           |

**Grid 响应式卡片示例**：
```html
<!-- 移动端1列，平板2列，桌面4列，间距6 -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
  <div class="bg-white p-4 rounded-lg">卡片1</div>
  <div class="bg-white p-4 rounded-lg">卡片2</div>
  <div class="bg-white p-4 rounded-lg">卡片3</div>
  <div class="bg-white p-4 rounded-lg">卡片4</div>
</div>
```

#### 4. 定位布局
| 类名                                        | CSS属性                         | 说明                     |
| ------------------------------------------- | ------------------------------- | ------------------------ |
| `static`                                    | `position: static`              | 静态定位（默认）         |
| `relative`                                  | `position: relative`            | 相对定位                 |
| `absolute`                                  | `position: absolute`            | 绝对定位                 |
| `fixed`                                     | `position: fixed`               | 固定定位                 |
| `sticky`                                    | `position: sticky`              | 粘性定位                 |
| `inset-0`                                   | `top:0;right:0;bottom:0;left:0` | 填满父容器               |
| `top-*` / `right-*` / `bottom-*` / `left-*` | 对应方向定位值                  | 定位偏移量               |
| `z-*`                                       | `z-index: *`                    | 层级，z-0 ~ z-50，z-auto |

**绝对定位居中示例**：
```html
<div class="relative w-full h-96 bg-gray-100">
  <!-- 相对于父容器水平垂直居中 -->
  <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-white p-6 rounded-lg">
    绝对定位居中
  </div>
</div>
```

#### 5. 容器类 `container`
`container` 类用于固定页面最大宽度，居中对齐，适配不同屏幕，是页面布局的基础：
```html
<!-- 页面容器，自动适配断点，居中对齐，带左右内边距 -->
<div class="container mx-auto px-4">
  <!-- 页面内容 -->
</div>
```

### 4.2 盒模型工具类
盒模型是 CSS 的核心，Tailwind 提供了完整的宽高、内边距、外边距、边框、圆角等工具类。

#### 1. 宽高尺寸
| 类名                  | 说明                                       |
| --------------------- | ------------------------------------------ |
| `w-*`                 | 宽度，w-0 ~ w-96，步长4px（w-4=16px）      |
| `w-1/2` ~ `w-12/12`   | 百分比宽度，如w-1/2=50%，w-1/3=33.333%     |
| `w-full`              | 宽度100%                                   |
| `w-screen`            | 宽度100vw，视口宽度                        |
| `w-auto`              | 宽度自动                                   |
| `min-w-*`             | 最小宽度                                   |
| `max-w-*`             | 最大宽度，常用max-w-screen-xl、max-w-3xl等 |
| `h-*`                 | 高度，和宽度规则一致                       |
| `h-full`              | 高度100%                                   |
| `h-screen`            | 高度100vh，视口高度                        |
| `min-h-screen`        | 最小高度100vh，常用在页面根容器            |
| `min-h-*` / `max-h-*` | 最小/最大高度                              |

#### 2. 内边距 padding
| 类名   | 说明                       |
| ------ | -------------------------- |
| `p-*`  | 四个方向内边距，p-0 ~ p-96 |
| `px-*` | 水平方向（左右）内边距     |
| `py-*` | 垂直方向（上下）内边距     |
| `pt-*` | 上内边距                   |
| `pr-*` | 右内边距                   |
| `pb-*` | 下内边距                   |
| `pl-*` | 左内边距                   |

> 尺寸规则：Tailwind 默认 1 单位 = 0.25rem = 4px，如 `p-4` = 16px，`p-6` = 24px，以此类推。

#### 3. 外边距 margin
| 类名                              | 说明                                               |
| --------------------------------- | -------------------------------------------------- |
| `m-*`                             | 四个方向外边距，m-0 ~ m-96                         |
| `mx-*`                            | 水平方向（左右）外边距，`mx-auto` 常用实现水平居中 |
| `my-*`                            | 垂直方向（上下）外边距                             |
| `mt-*` / `mr-*` / `mb-*` / `ml-*` | 对应方向外边距                                     |
| `-m-*` / `-mt-*` 等               | 负外边距，如 `-mt-4` = margin-top: -16px           |

#### 4. 边框与圆角
| 分类     | 常用类名                                                     | 说明                                             |
| -------- | ------------------------------------------------------------ | ------------------------------------------------ |
| 边框宽度 | `border`                                                     | 1px 边框，默认全方向                             |
|          | `border-0` ~ `border-8`                                      | 边框宽度                                         |
|          | `border-t-*` / `border-r-*` / `border-b-*` / `border-l-*`    | 单方向边框宽度                                   |
| 边框颜色 | `border-*`                                                   | 边框颜色，如 `border-gray-200`、`border-primary` |
| 边框样式 | `border-solid`                                               | 实线（默认）                                     |
|          | `border-dashed`                                              | 虚线                                             |
|          | `border-dotted`                                              | 点线                                             |
|          | `border-none`                                                | 无边框                                           |
| 圆角     | `rounded-none`                                               | 无圆角                                           |
|          | `rounded-sm`                                                 | 2px 圆角                                         |
|          | `rounded`                                                    | 4px 圆角                                         |
|          | `rounded-md`                                                 | 6px 圆角                                         |
|          | `rounded-lg`                                                 | 8px 圆角                                         |
|          | `rounded-xl`                                                 | 12px 圆角                                        |
|          | `rounded-2xl`                                                | 16px 圆角                                        |
|          | `rounded-full`                                               | 9999px 圆角，圆形/胶囊形状                       |
|          | `rounded-t-*` / `rounded-r-*` / `rounded-b-*` / `rounded-l-*` | 单方向圆角                                       |

**示例**：
```html
<!-- 卡片盒模型示例 -->
<div class="w-full max-w-sm mx-auto bg-white border border-gray-200 rounded-xl p-6 mb-6">
  <h3 class="text-xl font-bold mb-2">卡片标题</h3>
  <p class="text-gray-600 mb-4">卡片内容，带内边距、边框和圆角</p>
  <button class="w-full bg-blue-500 hover:bg-blue-600 text-white py-2 px-4 rounded-lg border-none">
    按钮
  </button>
</div>
```

### 4.3 排版工具类
排版是页面可读性的核心，Tailwind 提供了字体、字号、字重、行高、对齐、文本装饰等完整的排版工具类。

#### 1. 字体与字号
| 类名                    | CSS属性                   | 说明               |
| ----------------------- | ------------------------- | ------------------ |
| `font-sans`             | `font-family: 无衬线字体` | 无衬线字体（默认） |
| `font-serif`            | `font-family: 衬线字体`   | 衬线字体           |
| `font-mono`             | `font-family: 等宽字体`   | 等宽字体           |
| `text-xs`               | `font-size: 0.75rem`      | 12px 字号          |
| `text-sm`               | `font-size: 0.875rem`     | 14px 字号          |
| `text-base`             | `font-size: 1rem`         | 16px 字号（默认）  |
| `text-lg`               | `font-size: 1.125rem`     | 18px 字号          |
| `text-xl`               | `font-size: 1.25rem`      | 20px 字号          |
| `text-2xl` ~ `text-9xl` | 对应字号                  | 24px ~ 144px 字号  |
| `font-thin`             | `font-weight: 100`        | 细体               |
| `font-light`            | `font-weight: 300`        | 轻体               |
| `font-normal`           | `font-weight: 400`        | 常规（默认）       |
| `font-medium`           | `font-weight: 500`        | 中等               |
| `font-semibold`         | `font-weight: 600`        | 半粗体             |
| `font-bold`             | `font-weight: 700`        | 粗体               |
| `font-black`            | `font-weight: 900`        | 黑体               |

#### 2. 文本样式
| 类名                      | CSS属性                           | 说明                          |
| ----------------------- | ------------------------------- | --------------------------- |
| `text-left`             | `text-align: left`              | 左对齐（默认）                     |
| `text-center`           | `text-align: center`            | 居中对齐                        |
| `text-right`            | `text-align: right`             | 右对齐                         |
| `text-justify`          | `text-align: justify`           | 两端对齐                        |
| `leading-tight`         | `line-height: 1.25`             | 紧凑行高                        |
| `leading-normal`        | `line-height: 1.5`              | 常规行高（默认）                    |
| `leading-relaxed`       | `line-height: 1.75`             | 宽松行高                        |
| `leading-loose`         | `line-height: 2`                | 超宽松行高                       |
| `tracking-tight`        | `letter-spacing: -0.025em`      | 紧凑字间距                       |
| `tracking-normal`       | `letter-spacing: 0em`           | 常规字间距                       |
| `tracking-wide`         | `letter-spacing: 0.025em`       | 宽松字间距                       |
| `text-white` / `text-*` | `color: *`                      | 文本颜色                        |
| `opacity-*`             | `opacity: *`                    | 透明度，0-100                   |
| `underline`             | `text-decoration: underline`    | 下划线                         |
| `line-through`          | `text-decoration: line-through` | 删除线                         |
| `no-underline`          | `text-decoration: none`         | 无装饰线                        |
| `uppercase`             | `text-transform: uppercase`     | 全大写                         |
| `lowercase`             | `text-transform: lowercase`     | 全小写                         |
| `capitalize`            | `text-transform: capitalize`    | 首字母大写                       |
| `normal-case`           | `text-transform: none`          | 无转换                         |
| `truncate`              | 文本溢出省略号                         | 单行文本溢出显示...                 |
| `line-clamp-*`          | 多行文本溢出省略                        | line-clamp-1 ~ line-clamp-6 |

**排版示例**：
```html
<article class="max-w-2xl mx-auto px-4 py-10">
  <h1 class="text-4xl font-bold text-gray-900 leading-tight mb-4">
    Tailwind CSS 排版教程
  </h1>
  <p class="text-gray-500 text-sm mb-6">发布于 2026-04-25 · 5分钟阅读</p>
  <p class="text-lg text-gray-700 leading-relaxed mb-4">
    Tailwind CSS 是一个工具优先的原子化 CSS 框架，它让你无需离开 HTML 即可快速构建现代化的页面。
  </p>
  <p class="text-gray-600 leading-relaxed mb-6">
    原子化 CSS 的核心思想是将样式拆分为最小的原子单元，每个类名对应一条 CSS 规则，通过组合这些类名实现复杂的样式效果，大幅提升开发效率。
  </p>
  <!-- 多行文本溢出 -->
  <p class="text-gray-600 line-clamp-2 border-l-4 border-primary pl-4 italic">
    这是一段多行文本，超过2行就会自动显示省略号，非常适合卡片摘要、简介等场景，无需编写复杂的CSS，直接使用line-clamp类名即可实现。
  </p>
</article>
```

### 4.4 颜色与背景工具类
Tailwind 提供了完整的颜色系统，默认包含 22 种主色，每种主色从 50（最浅）到 950（最深）分为 10 个色阶，完全满足日常开发需求。

#### 1. 颜色使用规则
Tailwind 的颜色类名遵循 `属性-色名-色阶` 的规则，例如：
- `bg-blue-500`：背景色，蓝色，500色阶
- `text-red-600`：文本色，红色，600色阶
- `border-green-400`：边框色，绿色，400色阶
- `ring-indigo-300`：轮廓环色，靛蓝色，300色阶

**常用色名**：`gray`/`zinc`/`neutral`/`stone`（中性灰）、`red`/`orange`/`amber`/`yellow`/`lime`/`green`/`emerald`/`teal`/`cyan`/`sky`/`blue`/`indigo`/`violet`/`purple`/`fuchsia`/`pink`/`rose`

#### 2. 背景相关工具类
| 类名                                                       | CSS属性                           | 说明                                |
| -------------------------------------------------------- | ------------------------------- | --------------------------------- |
| `bg-*`                                                   | `background-color: *`           | 背景色                               |
| `bg-opacity-*`                                           | `background-opacity: *`         | 背景透明度，0-100                       |
| `bg-transparent`                                         | `background: transparent`       | 透明背景                              |
| `bg-linear-to-*`                                         | `background: linear-gradient()` | 线性渐变（v4 新增，替代v3的bg-gradient-to-*） |
| `bg-linear-*`                                            | 线性渐变角度                          | 如 `bg-linear-45` 45度渐变            |
| `from-*`                                                 | 渐变起始色                           | 渐变第一个颜色                           |
| `via-*`                                                  | 渐变中间色                           | 渐变中间颜色                            |
| `to-*`                                                   | 渐变结束色                           | 渐变最后一个颜色                          |
| `bg-cover`                                               | `background-size: cover`        | 背景图覆盖容器                           |
| `bg-contain`                                             | `background-size: contain`      | 背景图完整包含                           |
| `bg-center`                                              | `background-position: center`   | 背景图居中                             |
| `bg-top` / `bg-bottom` / `bg-left` / `bg-right`          | 背景图位置                           | 对应方向定位                            |
| `bg-no-repeat`                                           | `background-repeat: no-repeat`  | 背景图不重复                            |
| `bg-fixed`                                               | `background-attachment: fixed`  | 背景图固定                             |
| border-gray-200 / border-primary-500                     | border-color                    | 边框颜色<br>                          |
| shadow-sm / shadow / shadow-md / shadow-lg / shadow-none | box-shadow                      | 阴影                                |

**渐变示例（v4 最新语法）**：
```html
<!-- 从左到右的蓝紫渐变 -->
<div class="w-full h-40 bg-linear-to-r from-blue-500 to-purple-600 rounded-xl"></div>

<!-- 45度角三色渐变 -->
<div class="w-full h-40 bg-linear-45 from-indigo-500 via-purple-500 to-pink-500 rounded-xl"></div>

<!-- 文本渐变 -->
<h2 class="text-3xl font-bold bg-linear-to-r from-blue-500 to-purple-600 bg-clip-text text-transparent">
  这是一段渐变文本
</h2>
```

### 4.5 交互与动画工具类
Tailwind 提供了完整的交互、过渡、动画工具类，轻松实现页面动效与交互反馈。

#### 1. 光标与交互
| 类名                  | CSS属性                | 说明                      |
| --------------------- | ---------------------- | ------------------------- |
| `cursor-pointer`      | `cursor: pointer`      | 手型光标（按钮/链接常用） |
| `cursor-not-allowed`  | `cursor: not-allowed`  | 禁用光标                  |
| `cursor-text`         | `cursor: text`         | 文本光标                  |
| `cursor-move`         | `cursor: move`         | 移动光标                  |
| `pointer-events-none` | `pointer-events: none` | 禁用所有鼠标事件          |
| `pointer-events-auto` | `pointer-events: auto` | 启用鼠标事件              |
| `select-none`         | `user-select: none`    | 禁止文本选中              |
| `select-text`         | `user-select: text`    | 允许文本选中              |
| `select-all`          | `user-select: all`     | 点击全选文本              |

#### 2. 过渡动画
| 类名                            | CSS属性                                   | 说明                    |
| ------------------------------- | ----------------------------------------- | ----------------------- |
| `transition-all`                | `transition: all 0.2s`                    | 所有属性过渡            |
| `transition-colors`             | `transition: color,background-color...`   | 颜色相关过渡            |
| `transition-opacity`            | `transition: opacity`                     | 透明度过渡              |
| `transition-transform`          | `transition: transform`                   | 变换过渡                |
| `duration-75` ~ `duration-1000` | `transition-duration: *`                  | 过渡时长，75ms ~ 1000ms |
| `ease-linear`                   | `transition-timing-function: linear`      | 线性缓动                |
| `ease-in`                       | `transition-timing-function: ease-in`     | 先慢后快                |
| `ease-out`                      | `transition-timing-function: ease-out`    | 先快后慢                |
| `ease-in-out`                   | `transition-timing-function: ease-in-out` | 缓入缓出                |

#### 3. 变换属性
| 类名                                    | CSS属性                | 说明                            |
| --------------------------------------- | ---------------------- | ------------------------------- |
| `scale-*`                               | `transform: scale(*)`  | 缩放，scale-0 ~ scale-150       |
| `scale-x-*` / `scale-y-*`               | 单方向缩放             | 水平/垂直缩放                   |
| `rotate-*`                              | `transform: rotate(*)` | 旋转，rotate-0 ~ rotate-360     |
| `-rotate-*`                             | 反向旋转               | 如 -rotate-45 逆时针旋转45度    |
| `translate-x-*` / `translate-y-*`       | 位移                   | 水平/垂直位移                   |
| `-translate-x-1/2` / `-translate-y-1/2` | 负位移                 | 常用居中定位                    |
| `skew-x-*` / `skew-y-*`                 | 倾斜                   | 水平/垂直倾斜                   |
| `transform-origin-*`                    | `transform-origin: *`  | 变换原点，如center、top、bottom |

#### 4. 内置动画
Tailwind 内置了常用的 CSS 动画，直接使用类名即可：

| 类名             | 动画效果                     |
| ---------------- | ---------------------------- |
| `animate-none`   | 无动画                       |
| `animate-spin`   | 无限旋转动画，常用加载图标   |
| `animate-ping`   | 脉冲扩散动画，常用通知红点   |
| `animate-pulse`  | 呼吸淡入淡出动画，常用骨架屏 |
| `animate-bounce` | 弹跳动画，常用下拉提示       |

**动画示例**：
```html
<!-- 按钮悬停缩放+过渡 -->
<button class="bg-blue-500 hover:bg-blue-600 hover:scale-105 active:scale-95 text-white px-6 py-3 rounded-lg transition-all duration-300">
  悬停缩放按钮
</button>

<!-- 加载旋转图标 -->
<div class="animate-spin w-6 h-6 border-2 border-blue-500 border-t-transparent rounded-full"></div>

<!-- 通知红点 -->
<div class="relative">
  <svg class="w-6 h-6 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9"></path>
  </svg>
  <span class="absolute -top-1 -right-1 w-3 h-3 bg-red-500 rounded-full animate-ping"></span>
  <span class="absolute -top-1 -right-1 w-3 h-3 bg-red-500 rounded-full"></span>
</div>
```
#### 状态变体


Tailwind 支持给所有功能类添加**状态前缀**，实现不同交互状态下的样式变化，格式为 `状态:类名`。
##### 常用基础状态

|状态前缀|触发条件|示例|
|---|---|---|
|hover|鼠标悬浮|`hover:bg-blue-600 hover:text-white`|
|focus|获得焦点|`focus:border-blue-500 focus:outline-none`|
|active|元素激活（点击时）|`active:bg-blue-700 active:scale-95`|
|disabled|元素禁用|`disabled:bg-gray-300 disabled:cursor-not-allowed`|
|visited|链接已访问|`visited:text-purple-600`|
##### 高级状态变体

|状态前缀|作用|示例|
|---|---|---|
|group-hover|父容器添加 `group` 类，子元素跟随父容器悬浮变化|`<div class="group"><span class="group-hover:text-white"></span></div>`|
|peer-*|兄弟元素状态联动，兄弟元素添加 `peer` 类，控制当前元素样式|`<input class="peer" /><span class="peer-invalid:text-red-500">请输入正确内容</span>`|
|first/last|第一个/最后一个子元素|`first:mt-0 last:mb-0`|
|odd/even|奇偶子元素|`odd:bg-white even:bg-gray-50`|

## 高级特性详解
### 5.1 响应式设计
Tailwind 内置了移动端优先的响应式断点系统，通过断点前缀，轻松实现不同屏幕尺寸的样式适配。

#### 1. 默认断点系统
| 断点前缀 | 最小宽度 | 适配设备                 |
| -------- | -------- | ------------------------ |
| 无前缀   | 0        | 所有设备（默认，移动端） |
| `sm:`    | 640px    | 手机横屏/小平板          |
| `md:`    | 768px    | 平板/大手机              |
| `lg:`    | 1024px   | 笔记本/小桌面            |
| `xl:`    | 1280px   | 桌面显示器               |
| `2xl:`   | 1536px   | 大桌面显示器             |

#### 2. 响应式使用规则
Tailwind 的响应式是**增量式**的，断点前缀的样式会覆盖更小屏幕的样式，更大屏幕的样式会继承更小屏幕的样式，遵循移动端优先原则。

**核心规则**：不带前缀的类名是所有屏幕的默认样式，带断点前缀的类名仅在**大于等于该断点宽度**时生效。

**示例**：
```html
<!-- 
  移动端：1列
  sm(≥640px)：2列
  md(≥768px)：3列
  lg(≥1024px)：4列
  xl(≥1280px)：6列
-->
<div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-6 gap-4">
  <div class="bg-gray-100 p-4 rounded">响应式卡片</div>
  <div class="bg-gray-100 p-4 rounded">响应式卡片</div>
  <div class="bg-gray-100 p-4 rounded">响应式卡片</div>
  <div class="bg-gray-100 p-4 rounded">响应式卡片</div>
  <div class="bg-gray-100 p-4 rounded">响应式卡片</div>
  <div class="bg-gray-100 p-4 rounded">响应式卡片</div>
</div>

<!-- 响应式文本大小 -->
<h1 class="text-2xl sm:text-3xl md:text-4xl lg:text-5xl font-bold text-center">
  响应式标题
</h1>

<!-- 响应式显示隐藏：移动端隐藏，桌面端显示 -->
<div class="hidden lg:block">
  仅在大屏显示的内容
</div>

<!-- 响应式显示隐藏：桌面端隐藏，移动端显示 -->
<div class="block lg:hidden">
  仅在移动端显示的内容
</div>
```

#### 3. 自定义断点
在 `@theme` 块中可以自定义断点，覆盖或扩展默认断点：
```css
@import "tailwindcss";

@theme {
  /* 自定义断点 */
  --breakpoint-sm: 640px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
  --breakpoint-xl: 1280px;
  --breakpoint-2xl: 1536px;
  /* 新增自定义断点 */
  --breakpoint-3xl: 1920px;
}
```

定义后即可使用 `3xl:grid-cols-8` 等自定义断点前缀。

### 5.2 容器查询（v4 内置）
v4 版本原生支持容器查询，无需额外插件，实现了**基于父容器宽度**的样式适配，弥补了媒体查询只能基于视口宽度的不足，非常适合组件化开发。

#### 容器查询使用步骤
1.  给父容器添加 `@container` 类名，将其定义为查询容器
2.  给子元素使用 `@<breakpoint>:` 前缀，基于父容器宽度应用样式
3.  支持 `@sm:`、`@md:`、`@lg:` 等所有默认断点，也支持 `@max-md:` 最大宽度匹配

**示例**：
```html
<!-- 定义查询容器 -->
<div class="card @container w-full max-w-md mx-auto bg-white border border-gray-200 rounded-xl p-6">
  <!-- 
    当父容器宽度 ≥768px(md)时，变为水平布局
    当父容器宽度 <768px时，保持垂直布局
  -->
  <div class="flex flex-col @md:flex-row gap-4 items-center">
    <img src="https://picsum.photos/100" class="w-24 h-24 rounded-full object-cover" alt="头像">
    <div>
      <h3 class="text-xl font-bold @md:text-2xl">用户名</h3>
      <p class="text-gray-600 @md:text-lg">用户简介，容器宽度变化时，布局和字号会自动适配</p>
    </div>
  </div>
</div>
```

#### 命名容器
当页面有多个嵌套的查询容器时，可以给容器命名，子元素指定基于哪个容器进行查询，避免样式冲突：
```css
@import "tailwindcss";

@theme {
  /* 定义命名容器 */
  --container-sidebar: sidebar;
  --container-card: card;
}
```

使用时：
```html
<!-- 侧边栏容器，命名为sidebar -->
<aside class="@container/sidebar w-64 bg-gray-50 h-screen">
  <!-- 卡片容器，命名为card -->
  <div class="@container/card m-4 bg-white p-4 rounded-lg">
    <!-- 基于sidebar容器适配 -->
    <h3 class="@lg/sidebar:text-xl font-bold">标题</h3>
    <!-- 基于card容器适配 -->
    <p class="@md/card:text-sm text-gray-600">内容</p>
  </div>
</aside>
```

### 5.3 深色模式
Tailwind 内置了完善的深色模式支持，通过 `dark:` 前缀，轻松实现深色主题样式适配。

#### 1. 深色模式配置
v4 版本默认支持基于系统主题的深色模式，无需额外配置，直接使用 `dark:` 前缀即可。

如果需要手动切换深色模式（用户手动点击切换按钮），需要在 `@theme` 中配置深色模式选择器：
```css
@import "tailwindcss";

@theme {
  /* 配置深色模式基于class类名切换 */
  --dark-mode-selector: '.dark';
}
```

#### 2. 深色模式使用
**基于系统主题（默认）**：
```html
<!-- 浅色模式：白色背景，黑色文本 -->
<!-- 深色模式：黑色背景，白色文本 -->
<div class="bg-white dark:bg-zinc-900 text-gray-900 dark:text-white min-h-screen">
  <h1 class="text-3xl font-bold mb-4">深色模式示例</h1>
  <p class="text-gray-600 dark:text-gray-300">
    这段文本在浅色模式是深灰色，深色模式是浅灰色
  </p>
  <button class="bg-blue-500 hover:bg-blue-600 dark:bg-blue-700 dark:hover:bg-blue-600 text-white px-4 py-2 rounded-lg">
    按钮
  </button>
</div>
```

**手动切换模式**：
1.  给页面根元素（html）添加 `dark` 类名，即可启用深色模式，移除则恢复浅色模式
2.  可以通过 JavaScript 实现切换按钮，示例代码：
```html
<button id="theme-toggle" class="p-2 rounded-lg bg-gray-100 dark:bg-zinc-800">
  切换主题
</button>

<script>
  const toggleBtn = document.getElementById('theme-toggle');
  const html = document.documentElement;

  // 初始化：优先读取本地存储，否则读取系统主题
  if (localStorage.theme === 'dark' || (!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
    html.classList.add('dark');
  } else {
    html.classList.remove('dark');
  }

  // 切换主题
  toggleBtn.addEventListener('click', () => {
    html.classList.toggle('dark');
    // 保存到本地存储
    localStorage.theme = html.classList.contains('dark') ? 'dark' : 'light';
  });
</script>
```

### 5.4 主题深度定制
通过 `@theme` 块，你可以完全定制 Tailwind 的设计系统，打造符合项目需求的主题。

#### 1. 可定制的核心设计令牌
| 令牌前缀             | 说明       | 示例                                                  |
| -------------------- | ---------- | ----------------------------------------------------- |
| `--color-*`          | 颜色系统   | `--color-primary: #165DFF;`                           |
| `--spacing-*`        | 间距系统   | `--spacing-18: 4.5rem;`                               |
| `--font-family-*`    | 字体家族   | `--font-family-sans: 'Inter', sans-serif;`            |
| `--font-size-*`      | 字体大小   | `--font-size-2xl: 1.5rem;`                            |
| `--font-weight-*`    | 字重       | `--font-weight-medium: 500;`                          |
| `--line-height-*`    | 行高       | `--line-height-relaxed: 1.75;`                        |
| `--letter-spacing-*` | 字间距     | `--letter-spacing-wide: 0.025em;`                     |
| `--radius-*`         | 圆角       | `--radius-2xl: 16px;`                                 |
| `--border-width-*`   | 边框宽度   | `--border-width-4: 4px;`                              |
| `--shadow-*`         | 阴影       | `--shadow-card: 0 4px 20px rgba(0,0,0,0.08);`         |
| `--breakpoint-*`     | 响应式断点 | `--breakpoint-3xl: 1920px;`                           |
| `--z-index-*`        | 层级       | `--z-index-dropdown: 1000;`                           |
| `--opacity-*`        | 透明度     | `--opacity-80: 0.8;`                                  |
| `--duration-*`       | 动画时长   | `--duration-500: 500ms;`                              |
| `--easing-*`         | 缓动函数   | `--easing-bounce: cubic-bezier(0.34, 1.56, 0.64, 1);` |

#### 2. 完整主题定制示例
```css
@import "tailwindcss";

@theme {
  /* 深色模式选择器 */
  --dark-mode-selector: '.dark';

  /* 自定义品牌色 */
  --color-primary: #165DFF;
  --color-primary-light: #e8f3ff;
  --color-primary-dark: #0e42c9;
  --color-secondary: #722ed1;
  --color-success: #00b42a;
  --color-warning: #ff7d00;
  --color-danger: #ff4d4f;

  /* 自定义中性色 */
  --color-gray-50: #f5f7fa;
  --color-gray-100: #e4e7ed;
  --color-gray-200: #dcdfe6;
  --color-gray-300: #c0c4cc;
  --color-gray-400: #909399;
  --color-gray-500: #606266;
  --color-gray-600: #303133;
  --color-gray-700: #262626;
  --color-gray-800: #1d1d1d;
  --color-gray-900: #141414;

  /* 自定义字体 */
  --font-family-sans: 'Inter', 'Noto Sans SC', 'PingFang SC', 'Microsoft YaHei', system-ui, sans-serif;
  --font-family-mono: 'Fira Code', 'Consolas', monospace;

  /* 自定义圆角 */
  --radius-sm: 4px;
  --radius-md: 6px;
  --radius-lg: 8px;
  --radius-xl: 12px;
  --radius-2xl: 16px;
  --radius-full: 9999px;

  /* 自定义阴影 */
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-base: 0 1px 4px 0 rgba(0, 0, 0, 0.08);
  --shadow-md: 0 4px 12px 0 rgba(0, 0, 0, 0.08);
  --shadow-lg: 0 8px 24px 0 rgba(0, 0, 0, 0.1);
  --shadow-card: 0 4px 20px 0 rgba(0, 0, 0, 0.06);

  /* 自定义断点 */
  --breakpoint-xs: 480px;
  --breakpoint-sm: 640px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
  --breakpoint-xl: 1280px;
  --breakpoint-2xl: 1536px;
}
```

### 5.5任意值语法

当内置类无法满足特殊需求时，使用**方括号语法** `[值]` 直接写入自定义CSS值，无需编写自定义CSS。

#### 核心用法

```html
<!-- 自定义尺寸 -->
<div class="w-[300px] h-[200px] mt-[17px]"></div>

<!-- 自定义颜色 -->
<div class="bg-[#165DFF] text-[rgb(255,100,50)]"></div>

<!-- 自定义CSS属性 -->
<div class="[font-size:14px] [line-height:1.8]"></div>

<!-- 带单位的计算值 -->
<div class="w-[calc(100%-20px)] h-[100vh-80px]"></div>

<!-- CSS变量 -->
<div class="bg-[var(--theme-color)]"></div>

<!-- 状态+任意值组合 -->
<div class="hover:bg-[rgba(22,93,255,0.8)]"></div>
```

## 最佳实践与性能优化
### 6.1 组件复用最佳实践
Tailwind 中复用样式有3种主流方案，适用于不同场景，按推荐优先级排序如下：

#### 1. 框架组件封装（最高推荐）
在 Vue/React 等框架中，将重复的 UI 封装为组件，这是最灵活、最易维护的复用方式，完全保留 Tailwind 的灵活性。

**React 组件示例**：
```jsx
// Button.jsx 按钮组件
export default function Button({ 
  children, 
  type = 'primary', 
  size = 'md',
  disabled = false,
  onClick 
}) {
  // 基础样式
  const baseClass = 'font-medium rounded-lg transition-all';
  
  // 类型样式
  const typeClass = {
    primary: 'bg-primary hover:bg-primary/90 text-white',
    secondary: 'bg-white border border-gray-200 hover:bg-gray-50 text-gray-700',
    danger: 'bg-danger hover:bg-danger/90 text-white',
  }[type];
  
  // 尺寸样式
  const sizeClass = {
    sm: 'px-3 py-1.5 text-sm',
    md: 'px-6 py-3 text-base',
    lg: 'px-8 py-4 text-lg',
  }[size];

  // 禁用样式
  const disabledClass = disabled ? 'opacity-50 cursor-not-allowed' : '';

  return (
    <button 
      className={`${baseClass} ${typeClass} ${sizeClass} ${disabledClass}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}
```

使用时：
```jsx
import Button from './Button';

// 直接使用组件，无需重复写样式
<Button type="primary" size="md">主要按钮</Button>
<Button type="secondary" size="sm">次要按钮</Button>
<Button type="danger" disabled>禁用按钮</Button>
```

#### 2. @apply 抽取复用样式
适合简单的、无逻辑的样式复用，比如全局通用的按钮、卡片样式，在 CSS 文件中通过 `@apply` 抽取。

**示例**：
```css
@import "tailwindcss";

@layer components {
  /* 通用按钮样式 */
  .btn-primary {
    @apply bg-primary hover:bg-primary/90 text-white font-medium px-6 py-3 rounded-lg transition-all;
  }

  .btn-secondary {
    @apply bg-white border border-gray-200 hover:bg-gray-50 text-gray-700 font-medium px-6 py-3 rounded-lg transition-all;
  }

  /* 通用卡片样式 */
  .card-base {
    @apply bg-white rounded-xl shadow-card p-6 border border-gray-100;
  }
}
```

使用时直接在 HTML 中添加类名即可。

#### 3. 循环批量生成（适合列表/重复元素）
在模板中通过循环生成重复元素，避免重复写类名，适合列表、表格、选项卡等场景。

**Vue 示例**：
```vue
<template>
  <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
    <!-- 循环生成卡片，无需重复写类名 -->
    <div v-for="item in cardList" :key="item.id" class="card-base">
      <h3 class="text-xl font-bold mb-2">{{ item.title }}</h3>
      <p class="text-gray-600">{{ item.desc }}</p>
    </div>
  </div>
</template>

<script setup>
const cardList = [
  { id: 1, title: '卡片1', desc: '卡片内容1' },
  { id: 2, title: '卡片2', desc: '卡片内容2' },
  { id: 3, title: '卡片3', desc: '卡片内容3' },
];
</script>
```

### 6.2 性能优化
Tailwind v4 本身已经做了极致的性能优化，生产环境只会打包项目中实际使用到的工具类，CSS 体积通常在 20KB 以内，配合以下优化，可达到最佳性能。

#### 1. 生产环境构建优化
- 确保构建时使用 `--minify` 参数压缩 CSS，去除无用代码、空格和注释
- 开启构建工具的 CSS 压缩，如 Vite 生产构建会自动压缩，无需额外配置
- 避免在生产环境使用 CDN 方式，CDN 会引入完整的 Tailwind 代码，体积较大

#### 2. 精准控制内容扫描
通过 `@source` 指令精准指定需要扫描的文件，避免扫描无关文件，提升构建速度，同时避免打包无用样式：
```css
@import "tailwindcss";

/* 仅扫描实际使用到Tailwind的文件 */
@source "./src/**/*.{html,jsx,tsx,vue}";
/* 排除不需要扫描的文件 */
@source "!./node_modules/**/*";
@source "!./dist/**/*";
```

#### 3. 避免滥用 @apply 和自定义 CSS
- 尽量使用原生工具类，减少自定义 CSS 和 @apply 的使用，避免增加 CSS 体积
- 自定义工具类优先使用 `@utility` 指令，会自动适配 JIT 引擎，仅打包使用到的类
- 避免在循环中生成动态类名，如 `w-${width}`，Tailwind 无法扫描到动态类名，不会生成对应的样式，必须使用完整的类名

**错误写法（动态类名）**：
```vue
<!-- 错误：Tailwind无法识别动态拼接的类名 -->
<div class="w-{{ width }}"></div>
<div :class="`w-${width}`"></div>
```

**正确写法**：
```vue
<!-- 正确：使用完整的类名映射 -->
<div :class="{
  'w-1/2': width === 'half',
  'w-full': width === 'full',
  'w-screen': width === 'screen'
}"></div>

<!-- 或使用任意值语法 -->
<div :style="{ width: width + 'px' }" class="..."></div>
```

#### 4. 开启 CSS 代码分割
在 Vite/Webpack 等构建工具中，开启 CSS 代码分割，按需加载 CSS，提升页面首屏加载速度。

### 6.3 开发效率提升技巧
1.  **安装 VS Code 插件**：Tailwind CSS IntelliSense，提供智能补全、语法高亮、 hover 提示、自动格式化，是开发必备
2.  **使用 Prettier 插件**：`prettier-plugin-tailwindcss`，自动对 Tailwind 类名进行排序，统一代码规范，提升可读性
3.  **使用官方组件库**：Tailwind UI（官方）、shadcn/ui、daisyUI 等，基于 Tailwind 封装的组件，开箱即用
4.  **使用 Tailwind Play**：https://play.tailwindcss.com/ 官方在线 playground，无需搭建环境，快速调试样式
5.  **收藏官方速查表**：https://tailwindcss.com/docs/cheat-sheet 完整的类名速查表，开发时快速查阅




### 6.2 常用第三方插件

- `daisyui`：基于Tailwind的组件库，提供大量开箱即用的UI组件
- `tailwindcss-animate`：内置常用动画，扩展动画类名
- `@headlessui/tailwindcss`：配合Headless UI无样式组件库使用的变体插件
- `tailwind-scrollbar`：自定义滚动条样式
官方核心插件

Tailwind 官方提供高频场景专用插件，开箱即用： 

| 插件名称 | 功能 | 安装方式 |
| ----------- | ------------- | -------------- | 
| @tailwindcss/forms | 美化表单元素，提供统一的表单样式 | `npm install -D @tailwindcss/forms` |
| @tailwindcss/typography | 富文本排版样式，给MD/HTML富文本添加统一美观的排版 | `npm install -D @tailwindcss/typography` | 
| @tailwindcss/aspect-ratio | 宽高比控制，替代原生aspect-ratio，兼容低版本浏览器 | `npm install -D @tailwindcss/aspect-ratio` | 
| @tailwindcss/line-clamp | 多行文本溢出省略，兼容低版本浏览器 | `npm install -D @tailwindcss/line-clamp` |

**插件使用**：安装后在 `tailwind.config.js` 中注册

```javascript
module.exports = {
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}
```

### 6.3 自定义插件开发

通过插件可以扩展Tailwind的能力，包括自定义功能类、变体、主题等，基础示例：

```javascript
// tailwind.config.js
const plugin = require('tailwindcss/plugin')

module.exports = {
  plugins: [
    plugin(function({ addUtilities, theme }) {
      // 自定义功能类
      addUtilities({
        '.content-hidden': {
          'content-visibility': 'hidden',
        },
        '.text-gradient': {
          'background-clip': 'text',
          '-webkit-background-clip': 'text',
          'color': 'transparent',
        }
      })
    })
  ],
}
```

---
## 与其他UI库兼容
当与Element Plus、Ant Design等UI库配合使用时，解决类名冲突、优先级问题：
1. 配置前缀：给Tailwind添加类名前缀，避免冲突
```javascript
module.exports = {
prefix: 'tw-',
}
````

2. 配置important：提升Tailwind样式优先级，覆盖UI库默认样式
```javascript
module.exports = {
important: '#app', // 推荐使用根选择器，而非true，避免过度使用!important
}
```
    
## 官方学习资源
- Tailwind CSS 官方文档：https://tailwindcss.com/docs
- Tailwind CSS 中文文档：https://tailwindcss.zhcndoc.com
- Tailwind Play 在线调试：https://play.tailwindcss.com
- 官方组件库 Tailwind UI：https://tailwindui.com

## 命令表格

---

### 类名命名规则对照表
#### 表1-1 核心命名规则
| 规则类型   | 核心说明                                       | 示例                                                   | 对应CSS效果                                                  |
| ---------- | ---------------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 属性简写   | CSS属性名语义化缩写，是类名的核心前缀          | `w`、`p`、`m`、`bg`、`text`                            | `width`、`padding`、`margin`、`background`、`color/font` 相关属性 |
| 值映射     | 属性值直接跟在简写后，完成类名组合             | `w-full`、`p-4`、`text-lg`                             | `width:100%`、`padding:16px`、`font-size:1.125rem`           |
| 方向指定   | 后缀指定属性生效的方向，适配盒模型规则         | `pt`、`ml`、`border-r`、`rounded-t`                    | `padding-top`、`margin-left`、`border-right`、`border-top-radius` |
| 状态变体   | 「前缀:」格式限定样式生效的条件/状态，支持叠加 | `hover:bg-blue-600`、`md:text-2xl`、`dark:bg-zinc-900` | 仅鼠标悬停/≥768px视口/深色模式下，对应样式生效               |
| 任意值语法 | 方括号`[]`写入自定义CSS值，覆盖预设能力        | `w-[200px]`、`text-[#123456]`、`top-[10vh]`            | 生成对应自定义值的CSS属性，无需额外写自定义样式              |

#### 表1-2 常用CSS属性简写对照表
| CSS原生属性           | Tailwind简写  | 完整类名示例                                |
| ----------------- | ----------- | ------------------------------------- |
| width / height    | w / h       | w-full、h-screen、w-1/2                 |
| padding           | p           | p-4、px-6、py-3、pt-2                    |
| margin            | m           | m-auto、mx-4、my-6、mb-8                 |
| background        | bg          | bg-white、bg-primary、bg-linear-to-r    |
| color / font-size | text        | text-gray-700、text-lg、text-center     |
| font-weight       | font        | font-bold、font-medium、font-normal     |
| border            | border      | border、border-gray-200、border-r-2     |
| border-radius     | rounded     | rounded-lg、rounded-full、rounded-t-md  |
| display           | 原生属性名直接作为类名 | block、flex、grid、hidden、inline-block   |
| position          | 原生属性名直接作为类名 | static、relative、absolute、fixed、sticky |

---

### v4版本核心指令详解表
| 指令名称                 | 核心作用                                                     | 语法示例                                                     | v4版本变更说明                                               | 使用注意事项                                                 |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `@import "tailwindcss";` | 框架核心入口，引入所有基础样式、工具类与核心能力             | `@import "tailwindcss";`                                     | 完全替代v3版本的`@tailwind base/components/utilities` 三条指令 | 必须放在全局CSS文件的最顶部，否则会导致样式优先级异常        |
| `@theme`                 | 主题配置核心指令，定义全局设计令牌，自动生成对应工具类       | `@theme { --color-primary: #165DFF; --breakpoint-3xl: 1920px; }` | v4新增核心指令，替代v3版本`tailwind.config.js`中的主题配置   | 所有自定义设计令牌必须以`--`开头，遵循CSS变量命名规范，支持颜色、间距、断点、字体等全量设计系统定制 |
| `@utility`               | 自定义工具类指令，在CSS中直接定义原生工具类，支持嵌套与变体  | `@utility text-gradient { background-clip: text; color: transparent; }` | v4新增指令，无需编写JS插件即可自定义工具类，替代v3的插件式自定义工具类 | 定义的工具类和原生工具类能力完全一致，支持`hover:`、`dark:`、响应式前缀等所有变体 |
| `@source`                | 内容扫描指令，手动指定Tailwind需要扫描的文件路径，控制样式打包范围 | `@source "./src/**/*.{html,vue,jsx}";`                       | v4新增指令，替代v3版本`tailwind.config.js`中的`content`配置项 | 支持通配符匹配，支持`!`排除路径，精准扫描可大幅提升构建速度，减少无用样式打包 |
| `@apply`                 | 样式复用指令，将多个Tailwind工具类合并到自定义选择器中       | `.btn-primary { @apply bg-primary hover:bg-primary/90 text-white px-6 py-3 rounded-lg; }` | 语法兼容v3，优先级规则优化，配合`@layer`使用效果更佳         | 适合简单无逻辑的样式复用，复杂组件优先使用框架组件（Vue/React）封装，避免滥用导致维护成本上升 |
| `@layer`                 | 层级优先级控制指令，指定样式所属层级，控制样式覆盖规则       | `@layer base { body { @apply font-sans bg-gray-50; } }`      | 语法兼容v3，层级优先级固定为：`base` < `components` < `utilities` | `utilities`层级优先级最高，可覆盖前面所有层级的样式，自定义工具类建议放在该层级 |

---

### 核心工具类速查表
#### 表3-1 布局类工具类
| 功能分类     | 常用类名                                             | 对应CSS属性                         | 功能说明                      |
| -------- | ------------------------------------------------ | ------------------------------- | ------------------------- |
| 显示与隐藏    | `block`/`inline-block`/`inline`                  | 对应原生display属性                   | 控制元素的盒类型                  |
|          | `flex`/`inline-flex`/`grid`/`inline-grid`        | 对应原生display属性                   | 开启弹性/网格布局                 |
|          | `hidden`/`visible`/`invisible`                   | `display:none`/`visibility`     | 控制元素显示/隐藏，`invisible`保留占位 |
| Flex弹性布局 | `flex-row`/`flex-col`                            | `flex-direction`                | 控制主轴方向                    |
|          | `flex-wrap`/`flex-nowrap`                        | `flex-wrap`                     | 控制元素是否换行                  |
|          | `justify-start/center/end/between/around/evenly` | `justify-content`               | 控制主轴对齐方式                  |
|          | `items-start/center/end/baseline/stretch`        | `align-items`                   | 控制交叉轴对齐方式                 |
|          | `self-auto/center/start/end`                     | `align-self`                    | 控制单个元素的交叉轴对齐              |
|          | `flex-1`/`flex-auto`/`flex-none`                 | `flex`                          | 控制元素的弹性伸缩占比               |
| Grid网格布局 | `grid-cols-1~12`                                 | `grid-template-columns`         | 定义网格列数                    |
|          | `grid-rows-1~6`                                  | `grid-template-rows`            | 定义网格行数                    |
|          | `col-span-1~12`/`col-span-full`                  | `grid-column`                   | 控制元素跨列数                   |
|          | `row-span-1~6`/`row-span-full`                   | `grid-row`                      | 控制元素跨行数                   |
|          | `gap-*`/`gap-x-*`/`gap-y-*`                      | `gap`                           | 控制网格行列间距                  |
| 定位布局     | `static`/`relative`/`absolute`/`fixed`/`sticky`  | 对应原生position属性                  | 控制元素定位类型                  |
|          | `inset-0`                                        | `top:0;right:0;bottom:0;left:0` | 填满父级定位容器                  |
|          | `top-*`/`right-*`/`bottom-*`/`left-*`            | 对应方向定位属性                        | 控制定位偏移量                   |
|          | `z-0~50`/`z-auto`                                | `z-index`                       | 控制元素层级                    |
| 容器适配     | `container`                                      | 响应式max-width + margin:auto      | 固定页面最大宽度，居中适配不同屏幕         |

#### 表3-2 盒模型类工具类
| 功能分类 | 常用类名                          | 对应CSS属性                               | 功能说明                                    |
| -------- | --------------------------------- | ----------------------------------------- | ------------------------------------------- |
| 宽高尺寸 | `w-0~96`/`w-1/2~12/12`            | `width`                                   | 固定宽度/百分比宽度                         |
|          | `w-full`/`w-screen`/`w-auto`      | `width:100%/100vw/auto`                   | 全屏宽度/自适应宽度                         |
|          | `min-w-*`/`max-w-*`               | `min-width`/`max-width`                   | 最小/最大宽度控制                           |
|          | `h-0~96`/`h-full`/`h-screen`      | `height`                                  | 高度控制，规则同宽度                        |
|          | `min-h-screen`                    | `min-height:100vh`                        | 最小高度铺满视口，页面根容器常用            |
| 内边距   | `p-*`                             | `padding`                                 | 四个方向统一内边距                          |
|          | `px-*`/`py-*`                     | `padding-left/right`/`padding-top/bottom` | 水平/垂直方向内边距                         |
|          | `pt-*`/`pr-*`/`pb-*`/`pl-*`       | 对应单方向padding属性                     | 单方向内边距控制                            |
| 外边距   | `m-*`                             | `margin`                                  | 四个方向统一外边距                          |
|          | `mx-auto`/`mx-*`/`my-*`           | `margin-left/right`/`margin-top/bottom`   | 水平居中/水平/垂直外边距                    |
|          | `mt-*`/`mr-*`/`mb-*`/`ml-*`       | 对应单方向margin属性                      | 单方向外边距控制                            |
|          | `-m-*`/`-mt-*`等                  | 负margin值                                | 负外边距偏移控制                            |
| 边框     | `border`/`border-0~8`             | `border-width`                            | 边框宽度控制                                |
|          | `border-t/r/b/l-*`                | 对应单方向border-width                    | 单方向边框宽度                              |
|          | `border-*`                        | `border-color`                            | 边框颜色控制，如`border-gray-200`           |
|          | `border-solid/dashed/dotted/none` | `border-style`                            | 边框样式控制                                |
| 圆角     | `rounded-sm/md/lg/xl/2xl/full`    | `border-radius`                           | 全方向圆角控制，`rounded-full`为圆形/胶囊型 |
|          | `rounded-t/r/b/l-*`               | 对应单方向border-radius                   | 单方向圆角控制                              |

#### 表3-3 排版类工具类
| 功能分类 | 常用类名                                            | 对应CSS属性          | 功能说明               |
| -------- | --------------------------------------------------- | -------------------- | ---------------------- |
| 字体配置 | `font-sans`/`font-serif`/`font-mono`                | `font-family`        | 控制字体家族           |
|          | `text-xs/sm/base/lg/xl/2xl~9xl`                     | `font-size`          | 控制字体大小           |
|          | `font-thin/light/normal/medium/semibold/bold/black` | `font-weight`        | 控制字体粗细           |
| 文本样式 | `text-left/center/right/justify`                    | `text-align`         | 控制文本对齐方式       |
|          | `leading-tight/normal/relaxed/loose`                | `line-height`        | 控制行高               |
|          | `tracking-tight/normal/wide`                        | `letter-spacing`     | 控制字间距             |
|          | `text-*`                                            | `color`              | 控制文本颜色           |
|          | `opacity-0~100`                                     | `opacity`            | 控制元素透明度         |
| 文本装饰 | `underline`/`line-through`/`no-underline`           | `text-decoration`    | 控制文本装饰线         |
|          | `uppercase`/`lowercase`/`capitalize`/`normal-case`  | `text-transform`     | 控制文本大小写转换     |
| 文本溢出 | `truncate`                                          | 单行文本溢出省略     | 单行文本超出显示省略号 |
|          | `line-clamp-1~6`                                    | `-webkit-line-clamp` | 多行文本超出显示省略号 |

#### 表3-4 颜色与背景类工具类
| 功能分类           | 常用类名                          | 对应CSS属性                     | 功能说明                                                     |
| ------------------ | --------------------------------- | ------------------------------- | ------------------------------------------------------------ |
| 颜色系统           | `[属性]-[色名]-[色阶]`            | 对应颜色属性                    | 通用规则：如`bg-blue-500`、`text-red-600`，色阶50(最浅)-950(最深) |
| 背景色             | `bg-*`                            | `background-color`              | 控制背景颜色                                                 |
|                    | `bg-opacity-*`                    | `background-opacity`            | 控制背景透明度                                               |
|                    | `bg-transparent`                  | `background: transparent`       | 透明背景                                                     |
| 背景渐变（v4最新） | `bg-linear-to-*`                  | `background: linear-gradient()` | 线性渐变，替代v3的`bg-gradient-to-*`                         |
|                    | `bg-linear-*`                     | 渐变角度控制                    | 如`bg-linear-45`为45度渐变                                   |
|                    | `from-*`/`via-*`/`to-*`           | 渐变色值                        | 控制渐变的起始/中间/结束颜色                                 |
| 背景图控制         | `bg-cover`/`bg-contain`           | `background-size`               | 控制背景图尺寸                                               |
|                    | `bg-center/top/bottom/left/right` | `background-position`           | 控制背景图位置                                               |
|                    | `bg-no-repeat`                    | `background-repeat: no-repeat`  | 背景图不重复                                                 |
|                    | `bg-fixed`                        | `background-attachment: fixed`  | 背景图固定不随滚动条移动                                     |

#### 表3-5 交互与动画类工具类
| 功能分类   | 常用类名                                     | 对应CSS属性                  | 功能说明                 |
| ---------- | -------------------------------------------- | ---------------------------- | ------------------------ |
| 光标与交互 | `cursor-pointer`/`not-allowed`/`text`/`move` | `cursor`                     | 控制鼠标光标样式         |
|            | `pointer-events-none/auto`                   | `pointer-events`             | 控制元素是否响应鼠标事件 |
|            | `select-none/text/all`                       | `user-select`                | 控制文本是否可被选中     |
| 过渡动画   | `transition-all/colors/opacity/transform`    | `transition`                 | 控制过渡属性范围         |
|            | `duration-75~1000`                           | `transition-duration`        | 控制过渡时长（单位ms）   |
|            | `ease-linear/in/out/in-out`                  | `transition-timing-function` | 控制过渡缓动曲线         |
| 变换属性   | `scale-0~150`/`scale-x/y-*`                  | `transform: scale()`         | 控制元素缩放             |
|            | `rotate-0~360`/`-rotate-*`                   | `transform: rotate()`        | 控制元素旋转             |
|            | `translate-x/y-*`/`-translate-x/y-*`         | `transform: translate()`     | 控制元素位移             |
|            | `skew-x/y-*`                                 | `transform: skew()`          | 控制元素倾斜             |
|            | `transform-origin-*`                         | `transform-origin`           | 控制变换的原点           |
| 内置动画   | `animate-spin`                               | 无限旋转动画                 | 常用加载图标             |
|            | `animate-ping`                               | 脉冲扩散动画                 | 常用通知红点             |
|            | `animate-pulse`                              | 呼吸淡入淡出动画             | 常用骨架屏               |
|            | `animate-bounce`                             | 弹跳动画                     | 常用下拉/提示动效        |
|            | `animate-none`                               | 清除动画                     | 禁用元素动画             |

---

### 高级特性详解表
| 特性名称   | 核心能力                                  | 使用规则                                                                                                              | v4专属特性                             | 实战示例                                                                                                          |
| ------ | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| 响应式设计  | 基于视口宽度的移动端优先适配，一套代码适配全尺寸设备            | 1. 无前缀类名为移动端默认样式<br>2. 断点前缀样式仅在≥对应宽度时生效<br>3. 断点样式增量覆盖，大屏继承小屏样式                                                   | 支持通过`@theme`自定义断点，兼容旧版断点规则         | `<div class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-6"></div>`<br>移动端1列，平板3列，桌面4列                 |
| 容器查询   | 基于父容器宽度的样式适配，弥补媒体查询仅适配视口的不足，完美适配组件化开发 | 1. 父容器添加`@container`类名定义为查询容器<br>2. 子元素使用`@<breakpoint>:`前缀，基于父容器宽度生效<br>3. 支持`@max-<breakpoint>:`反向匹配            | v4原生内置，无需额外插件，支持命名容器避免嵌套冲突         | `<div class="card @container"><div class="flex flex-col @md:flex-row"></div></div>`<br>父容器宽度≥768px时，子元素变为水平布局 |
| 深色模式   | 一键适配深色主题，支持系统主题跟随与手动切换两种模式            | 1. 默认基于系统`prefers-color-scheme`匹配<br>2. 手动切换需在`@theme`配置`--dark-mode-selector: '.dark'`<br>3. 使用`dark:`前缀定义深色模式样式 | 配置方式简化，支持嵌套变体，优先级规则优化              | `<div class="bg-white dark:bg-zinc-900 text-gray-900 dark:text-white"></div>`<br>浅色模式白底黑字，深色模式黑底白字            |
| 主题深度定制 | 全量定制设计系统，打造符合项目品牌的专属主题                | 1. 在`@theme`块中通过CSS变量定义设计令牌<br>2. 支持覆盖/扩展默认的颜色、间距、字体、圆角、断点等所有配置<br>3. 定义后自动生成对应工具类，无需额外配置                         | v4核心特性，完全基于原生CSS，替代旧版JS配置文件，构建速度更快 | `@theme { --color-primary: #165DFF; --radius-xl: 12px; --breakpoint-3xl: 1920px; }`<br>自定义品牌色、圆角、超大屏断点        |
| 分组变体   | 父元素状态触发子元素样式变化，实现卡片/列表的联动交互           | 1. 父元素添加`group`类名<br>2. 子元素添加`group-hover:`/`group-focus:`等前缀<br>3. 支持嵌套分组与命名分组，避免冲突                              | 支持自定义分组命名，适配复杂嵌套场景，变体叠加规则优化        | `<div class="group"><h3 class="group-hover:text-primary"></h3></div>`<br>鼠标悬停父卡片时，子标题变色                       |
| 变体叠加   | 多条件组合限定样式生效场景，实现复杂的条件样式               | 1. 多个变体前缀叠加，优先级从右到左<br>2. 支持响应式、状态、深色模式、容器查询等所有变体组合<br>3. 无叠加层数限制                                                 | v4优化了变体叠加的优先级计算，构建性能大幅提升           | `<button class="lg:dark:hover:bg-blue-700"></button>`<br>仅在大屏+深色模式+鼠标悬停时，样式生效                                 |
附录

### 官方资源

- Tailwind CSS 官方文档：[https://tailwindcss.com/docs](https://tailwindcss.com/docs)
- Tailwind UI 官方组件库：[https://tailwindui.com](https://tailwindui.com)
- Tailwind Play 在线调试：[https://play.tailwindcss.com](https://play.tailwindcss.com)
