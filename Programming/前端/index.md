> [!multi-column]
>> [!note] Web 基础 & 框架
>> - [HTML5+CSS3](Programming/前端/web页面元素及样式/html5+css3/html5+css3)
>> - [Vue3](Programming/前端/web页面元素及样式/Vue3/Vue3)
>> - [ElementPlus](Programming/前端/web页面元素及样式/ElementPlus/ElementPlus)
>> - [TailwindCSS](Programming/前端/web页面元素及样式/TailwindCSS/TailwindCSS)
>
>> [!abstract] Web 逻辑 & 工具
>> - [JavaScript](Programming/前端/web页面逻辑设计/JavaScript/JavaScript)
>> - [TypeScript](Programming/前端/web页面逻辑设计/typescript/typescript)
>> - [AJAX](Programming/前端/AJAX/AJAX)
>> - [Nodejs](Programming/前端/Nodejs/Nodejs)
>
>> [!example] 构建工具
>> - [Vite](Programming/前端/页面构建打包工具/vite/Vite)
>
>> [!todo] 设计要求
>> - [PC端界面设计](Programming/前端/前端页面设计要求/PC端界面设计)
>> - [移动Web界面设计](Programming/前端/前端页面设计要求/移动Web界面设计)
>
>> [!tip] 三维 & 桌面
>> - [Three.js](Programming/前端/thee/three)
>> - [PyQt6](Programming/前端/pyqt6/README)
## 快速导航

[[toc]]

## 页面布局基础

### 核心内容

涵盖 HTML 基础语法、CSS 核心布局方案（流式布局、弹性布局 Flex、网格布局 Grid、定位布局）、响应式设计、浏览器兼容性处理等核心知识点。

### 学习重点

- HTML 语义化标签的正确使用，避免纯样式标签嵌套；
- Flex/Grid 布局的场景化应用（如移动端适配、复杂栅格布局）；
- 响应式设计的核心原理（媒体查询、rem/vw 单位、适配方案）；
- 常见布局问题排查（垂直居中、边距塌陷、溢出隐藏）。

### 配套文档

[页面布局详细教程](./PC端界面设计.md)

## JavaScript 基础

### 核心内容

分为基础版（js.md）和进阶版（JavaScript.md），覆盖变量、数据类型、运算符、流程控制、函数、原型链、异步编程（回调/Promise/async-await）、DOM/BOM 操作等全维度知识点。

### 学习重点

- 基础版：掌握语法规范、变量作用域、数组/对象常用方法；
- 进阶版：深入理解原型链、闭包、异步编程、事件循环、内存管理；
- 结合实际案例理解 DOM 操作与事件委托，避免常见性能问题。

### 配套文档

- [JS 基础语法](./JavaScript.md)

## TypeScript 基础

### 核心内容

从 TS 环境搭建、基础类型、接口、泛型、类与继承，到 TS 与 Vue/React 框架的结合使用，覆盖前端类型化开发全流程。

### 学习重点

- 基础类型与高级类型（联合类型、交叉类型、类型断言）的灵活运用；
- 接口与类型别名的区别，泛型在工具函数中的应用；
- TS 编译配置（tsconfig.json）的核心参数调优；
- 框架结合场景：Vue3 + TS 组件封装、Props 类型定义。

### 配套文档

[TypeScript 完整教程](./typescript.md)

## AJAX 数据交互

### 核心内容

涵盖 AJAX 原生实现、Fetch API、Axios 封装、跨域问题解决、请求拦截/响应拦截、异常处理等前端数据交互核心知识点。

### 学习重点

- 原生 XMLHttpRequest 的工作原理，理解异步请求流程；
- Axios 封装统一请求函数，处理重复请求、请求取消、超时重试；
- 跨域解决方案（CORS、代理、JSONP）的适用场景与实现；
- 响应数据格式化、错误码统一处理，提升用户体验。

### 配套文档

[AJAX 实战教程](./AJAX.md)

## Node.js 后端基础

### 核心内容

Node.js 环境搭建、CommonJS/ESModule 模块化、文件操作、HTTP 服务搭建、中间件原理、npm/yarn/pnpm 包管理工具使用。

### 学习重点

- 模块化规范的差异与兼容方案，避免模块导入导出错误；
- 基于 Express/Koa 快速搭建简易后端服务，处理接口请求；
- 文件读写、流操作的性能优化，避免内存溢出；
- npm 私服、包版本管理、lock 文件的作用与原理。

### 配套文档

[Node.js 入门教程](../FullStack/Nodejs.md)

## 移动 Web 设计

### 核心内容

移动端适配方案、触摸事件、移动端常用组件（轮播、下拉刷新、上拉加载）、H5 新特性、移动端性能优化。

### 学习重点

- 视口（viewport）配置，适配不同尺寸移动端设备；
- 触摸事件与鼠标事件的区别，处理移动端点击穿透问题；
- H5 特性（Canvas、LocalStorage、Geolocation）的实战应用；
- 移动端性能优化：图片懒加载、资源压缩、减少重排重绘。

### 配套文档

[移动 Web 设计指南](./移动Web界面设计.md)

## Electron-Vue 跨端开发

### 核心内容

Electron 环境搭建、主进程/渲染进程通信、窗口管理、系统 API 调用、Vue 集成 Electron、打包发布流程。

### 学习重点

- 主进程与渲染进程的通信方式（IPC、共享数据）；
- 系统级功能实现（文件操作、托盘图标、快捷键、通知）；
- Vue 组件与 Electron API 的结合，处理跨端兼容问题；
- 打包优化（体积压缩、环境配置、多平台发布）。

### 配套文档

[Electron-Vue 实战教程](../Electron.md)

## 学习与开发建议

1. **循序渐进**：先掌握 HTML/CSS/JS 基础，再学习 TS/Node.js，最后拓展跨端开发；
2. **实战驱动**：每个知识点结合小案例练习，避免纯理论学习；
3. **规范优先**：遵循 ESLint/Prettier 代码规范，养成良好编码习惯；
4. **持续更新**：关注前端新特性（如 CSS 新属性、JS 新语法），定期迭代知识体系；
5. **问题排查**：遇到报错优先查阅官方文档，结合浏览器调试工具定位问题。

## 版本说明

本文档持续更新，所有子文档的内容会随前端技术生态迭代同步优化，重点关注 Vue3、React18、TS5.x、Electron 最新版本的适配。
