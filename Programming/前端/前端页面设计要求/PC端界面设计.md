# PC端界面设计

## 基础原则

PC端设计基于键鼠交互，以**高效、清晰、一致、适配**为核心，打造符合桌面习惯的专业界面。

---

### 一、核心设计原则

- **效率优先**：缩短操作路径，聚焦高频任务。
- **信息层级**：大屏下严格区分核心、次要、辅助信息。
- **全链路一致**：视觉、交互、控件全局统一，降低学习成本。
- **键鼠适配**：完善hover、快捷键、焦点导航，不照搬移动端逻辑。
- **容错可控**：支持撤销、中断，危险操作需二次确认。
- **可访问性**：满足对比度、键盘操作、读屏兼容标准。
- **兼容适配**：覆盖主流分辨率、系统、浏览器。

---

### 二、界面核心模块

标准架构：**导航区 → 操作区 → 内容区 → 状态栏**。

#### （一）全局导航系统

| 导航类型 | 适用场景               | 核心规范                        |
| -------- | ---------------------- | ------------------------------- |
| 顶部导航 | 功能≤8个的中轻量产品   | 高48-64px，常驻吸顶，菜单≤3级   |
| 侧边导航 | 功能≥8个的复杂专业系统 | 宽200/240px，支持收起，层级≤4级 |
| 组合导航 | 超大多业务线平台       | 顶部切业务域，侧边随域切换      |
| 标签页   | 平级内容切换           | 数量2-7个，超出则滚动/折叠      |

#### （二）内容展示区

- **布局**：基于12/24列栅格，最小宽1200px，最大内容宽1920px。
- **卡片**：圆角4/8px，内边距16/24px，同页风格一致。
- **图表**：同页类型≤4种，配色统一，支持hover下钻。
- **表格**：表头吸顶，行高40-56px，支持排序/筛选/分页。
- **编辑区**：宽度600-800px，正文14px，行高1.5-1.6倍。

#### （三）操作控件区

**按钮规范**（最核心控件）：

| 类型     | 样式     | 约束               |
| -------- | -------- | ------------------ |
| 主按钮   | 主色填充 | 同页≤1个           |
| 次按钮   | 描边     | 同模块≤3个         |
| 文字按钮 | 无背景   | 禁止与主按钮并列   |
| 危险按钮 | 红色填充 | 必须搭配二次确认   |
| 禁用按钮 | 灰色低透 | 保留形态，禁止隐藏 |

- 尺寸：高32/36/40px，最小宽80px。
- 状态：必须包含hover、点击、禁用三态反馈。
- **操作栏**：全局操作≤5个，超出收入「更多」菜单。

#### （四）弹窗与抽屉

| 组件 | 适用               | 尺寸            | 约束                   |
| ---- | ------------------ | --------------- | ---------------------- |
| 弹窗 | 轻量操作、二次确认 | 宽360/520/720px | 禁止嵌套弹窗           |
| 抽屉 | 长表单、详情查看   | 宽320/480/640px | 右侧滑出，禁止嵌套抽屉 |

#### （五）状态栏

- **面包屑**：内容区顶部，格式「首页 > 一级 > 二级 > 当前」。
- **全局状态栏**：底部通栏，高24/32px，字号≤12px。
- **页面状态提示**：右上角临时反馈，3-5s自动消失。

---

### 三、基础视觉设计系统

#### （一）栅格系统

- 列数：12列（中轻量） / 24列（企业级）。
- 参数：水槽16/24px，边距24/32px，最小宽1200px。

#### （二）色彩系统

| 色系   | 作用                                | 约束                       |
| ------ | ----------------------------------- | -------------------------- |
| 主色   | 品牌色，用于主按钮/选中态           | 占比≤10%                   |
| 辅助色 | 次要信息/标签/图表                  | 占比≤20%                   |
| 功能色 | 成功(绿)/警告(橙)/危险(红)/信息(蓝) | 全产品固定语义             |
| 中性色 | 背景/文字/边框                      | 占比≥70%，文字对比度≥4.5:1 |

#### （三）字体系统

以14px正文为基准的标准层级：

| 层级     | 字号    | 行高    | 字重    | 场景      |
| -------- | ------- | ------- | ------- | --------- |
| 一级标题 | 20/24px | 1.4     | 600/700 | 页面标题  |
| 二级标题 | 18px    | 1.4     | 600     | 卡片标题  |
| 三级标题 | 16px    | 1.5     | 500/600 | 模块标题  |
| 正文     | 14px    | 1.5-1.6 | 400     | 正文/按钮 |
| 辅助文字 | 12px    | 1.4     | 400     | 备注/提示 |
| 超小文字 | 10px    | 1.3     | 400     | 标签/角标 |

- 约束：同页字号≤6种，中文用系统默认字体。

#### （四）图标与间距

- **图标**：线性/面性统一，尺寸16/20/24/32px，点击热区≥24px。
- **间距**：遵循8px基准，所有间距为8的倍数。

---

### 四、PC端专属交互规范

#### （一）鼠标交互

- **点击**：热区≥24px，左键确认，双击仅用于编辑/打开。
- **Hover**：所有可点元素必设反馈（≤150ms），可配tooltip。
- **右键**：仅用于专业工具，选项≤8个。
- **滚轮/拖拽**：滚动条≥8px，拖拽时提供占位提示。

#### （二）键盘交互

- **焦点导航**：Tab/Shift+Tab顺序切换，焦点边框高亮。
- **快捷键**：支持系统通用（Ctrl+C/V/Z/S、ESC、Enter）。
- **输入框**：方向键移动光标，Home/End跳转首尾。

#### （三）操作反馈

- **即时**：hover/点击/输入实时响应。
- **状态**：加载超3s提供进度/取消入口。
- **结果**：成功/失败明确提示（Toast/弹窗）。
- **异常**：说明原因及解决方案，输入错误就近提示。

---

### 五、适配与兼容规范

- **分辨率**：适配1366×768~2560×1440，最小宽1200px。
- **适配模式**：流式/固定居中/响应式。
- **兼容性**：Windows 10+ / macOS 10.15+，Chrome/Edge/Firefox/Safari主流浏览器近2年版本。
- **缩放**：支持100%-150%缩放，无模糊重叠。

---

### 六、特殊场景设计

#### （一）状态页

| 状态   | 构成                  | 要求                     |
| ------ | --------------------- | ------------------------ |
| 空状态 | 缺省图+标题+说明+按钮 | 告知原因并提供解决方案   |
| 加载   | 全屏/局部/操作加载    | 骨架屏结构与真实内容一致 |
| 报错   | 错误码+说明+操作入口  | 文案通俗，避免术语       |

#### （二）表单规范

- **标签**：顶部对齐优先，必填标*号。
- **输入框**：高度统一，宽度匹配内容。
- **校验**：失焦实时校验，提交时定位首个错误项。
- **排布**：长表单单列，短表单可双列，间距24px。

#### （三）暗黑模式

- 背景用深灰非纯黑，文字对比度≥4.5:1。
- 边框/分割线用低透明白，主色/功能色语义一致。
- 支持跟随系统自动切换。

---

### 七、设计验收与交付

#### （一）验收标准

1. 视觉一致：无自定义样式。
2. 交互规范：状态反馈完整，快捷键正常。
3. 适配完整：主流环境无错乱。
4. 可访问性：对比度达标，键盘全操作。
5. 业务匹配：高频触达便捷，路径最短。

#### （二）交付物

1. 完整界面设计稿（含流程、适配标注）。
2. 设计规范文档（色彩、字体、组件等）。
3. 切图（2x/3x）与标注。
4. 交互说明文档（状态变化、快捷键等）。

## 一、主流CSS框架PC端实现

### 1. Bootstrap 5.3+（响应式CSS框架）

基于原生CSS/JS，支持暗黑模式、RTL、CSS变量定制，适配PC端全分辨率。以下为PC端深度定制实现（含4K/8K扩展、组件封装、工程化实践）。

#### （1）全局断点与主题系统扩展

扩展2K/4K/8K断点，通过CSS @layer规范样式优先级，CSS变量统一管理设计规范，支持一键切换明暗主题：

```css
@layer bootstrap, utilities, business;

@layer bootstrap {
  :root, :root[data-bs-theme="light"] {
    --bs-breakpoint-2k: 2560px;
    --bs-breakpoint-4k: 3840px;
    --bs-breakpoint-8k: 7680px;
    --bs-container-max-width-2k: 1920px;
    --bs-container-max-width-4k: 2560px;
    --bs-container-max-width-8k: 3840px;
    --bs-gutter-x: 1.5rem;
    --bs-gutter-y: 1.5rem;
    --bs-font-size-base: 1rem;
    --bs-btn-padding-x: 1rem;
    --bs-btn-padding-y: 0.5rem;
    --bs-border-radius: 4px;
  }

  :root[data-bs-theme="dark"] {
    --bs-body-bg: #121212;
    --bs-body-color: #e8e8e8;
    --bs-border-color: #2d2d2d;
    --bs-card-bg: #1e1e1e;
  }

  @media (min-width: 2560px) {
    :root { --bs-font-size-base: 1.0625rem; }
    .container-2k { max-width: var(--bs-container-max-width-2k); margin: 0 auto; }
    .col-2k-2 { flex: 0 0 auto; width: 16.666667%; }
    .col-2k-3 { flex: 0 0 auto; width: 25%; }
    .col-2k-4 { flex: 0 0 auto; width: 33.333333%; }
    .col-2k-6 { flex: 0 0 auto; width: 50%; }
  }

  @media (min-width: 3840px) {
    :root { --bs-font-size-base: 1.125rem; }
    .container-4k { max-width: var(--bs-container-max-width-4k); margin: 0 auto; }
    .col-4k-3 { flex: 0 0 auto; width: 25%; }
    .col-4k-4 { flex: 0 0 auto; width: 33.333333%; }
    .col-4k-6 { flex: 0 0 auto; width: 50%; }
  }

  @media (min-width: 7680px) {
    :root { --bs-font-size-base: 1.25rem; }
    .container-8k { max-width: var(--bs-container-max-width-8k); margin: 0 auto; }
  }
}

@layer utilities {
  .pc-content-max { max-width: 1920px; margin: 0 auto; }
  .pc-unselectable { user-select: none; -webkit-user-select: none; }
  .pc-hover-lift { transition: transform 0.2s ease, box-shadow 0.2s ease; will-change: transform, box-shadow; }
  .pc-hover-lift:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
}
```

#### （2）PC端经典布局：侧边栏+主内容+顶部导航

结合Offcanvas+Collapse组件，实现大屏固定侧边栏、中小屏折叠抽屉、二级菜单折叠的PC端全适配布局：

```html
<link href="https://cdn.bootcdn.net/ajax/libs/bootstrap/5.3.3/css/bootstrap.min.css" rel="stylesheet">
<script src="https://cdn.bootcdn.net/ajax/libs/bootstrap/5.3.3/js/bootstrap.bundle.min.js"></script>

<div class="container-2k container-4k container-8k vh-100 overflow-hidden">
  <div class="row h-100 g-0">
    <aside class="col-lg-2 d-none d-lg-block bg-body-tertiary h-100 border-end overflow-auto">
      <div class="p-3 border-bottom"><h5 class="mb-0 fw-bold">企业后台系统</h5></div>
      <ul class="nav flex-column py-2">
        <li class="nav-item"><a class="nav-link active d-flex align-items-center gap-2" href="#"><i class="bi bi-house"></i><span>首页</span></a></li>
        <li class="nav-item">
          <a class="nav-link d-flex align-items-center justify-content-between gap-2" data-bs-toggle="collapse" href="#menuData">
            <span class="d-flex align-items-center gap-2"><i class="bi bi-bar-chart"></i>数据管理</span><i class="bi bi-chevron-down small"></i>
          </a>
          <div class="collapse" id="menuData">
            <ul class="nav flex-column ps-3 py-1">
              <li class="nav-item"><a class="nav-link small" href="#">数据看板</a></li>
              <li class="nav-item"><a class="nav-link small" href="#">数据导出</a></li>
            </ul>
          </div>
        </li>
        <li class="nav-item"><a class="nav-link d-flex align-items-center gap-2" href="#"><i class="bi bi-gear"></i><span>系统设置</span></a></li>
      </ul>
    </aside>

    <main class="col-lg-10 col-12 h-100 d-flex flex-column overflow-hidden">
      <header class="py-3 px-4 border-bottom d-flex align-items-center justify-content-between flex-shrink-0">
        <button class="btn btn-light d-lg-none" data-bs-toggle="offcanvas" data-bs-target="#offcanvasSidebar"><i class="bi bi-list"></i></button>
        <div class="d-flex align-items-center gap-3 ms-auto">
          <div class="input-group w-auto">
            <input type="text" class="form-control" placeholder="全局搜索"><button class="btn btn-outline-secondary"><i class="bi bi-search"></i></button>
          </div>
          <div class="dropdown">
            <button class="btn btn-link text-body dropdown-toggle" data-bs-toggle="dropdown">
              <img src="https://picsum.photos/32/32" class="rounded-circle" width="32" height="32"><span class="ms-2 d-none d-md-inline">管理员</span>
            </button>
            <ul class="dropdown-menu dropdown-menu-end">
              <li><a class="dropdown-item" href="#">个人中心</a></li><li><hr class="dropdown-divider"></li><li><a class="dropdown-item text-danger" href="#">退出登录</a></li>
            </ul>
          </div>
        </div>
      </header>

      <div class="flex-1 overflow-auto p-4">
        <div class="row row-cols-1 row-cols-md-2 row-cols-lg-4 row-cols-2k-6 g-4 mb-4">
          <div class="col"><div class="card h-100 border-0 shadow-sm pc-hover-lift"><div class="card-body"><h6 class="text-muted mb-2">总访问量</h6><h3 class="fw-bold mb-0">128,560</h3><span class="text-success small">+12.5% 环比</span></div></div></div>
          <div class="col"><div class="card h-100 border-0 shadow-sm pc-hover-lift"><div class="card-body"><h6 class="text-muted mb-2">订单数</h6><h3 class="fw-bold mb-0">3,241</h3><span class="text-success small">+8.2% 环比</span></div></div></div>
        </div>

        <div class="card border-0 shadow-sm">
          <div class="card-header bg-transparent d-flex align-items-center justify-content-between"><h6 class="mb-0 fw-bold">订单列表</h6><button class="btn btn-primary btn-sm">导出数据</button></div>
          <div class="card-body p-0">
            <div class="table-pc-wrapper">
              <table class="table table-pc table-striped table-hover mb-0 align-middle">
                <thead><tr><th class="fixed-left" width="80">序号</th><th width="120">订单编号</th><th width="150">用户名称</th><th width="120">订单金额</th><th width="120">支付状态</th><th width="150">创建时间</th><th width="150">更新时间</th><th width="180">收货地址</th><th class="fixed-right" width="120">操作</th></tr></thead>
                <tbody><tr><td class="fixed-left">1</td><td>ORD20250408001</td><td>张三</td><td>¥ 2599.00</td><td><span class="badge bg-success">已支付</span></td><td>2025-04-08 10:20</td><td>2025-04-08 10:30</td><td>北京市朝阳区XX街道XX小区</td><td class="fixed-right"><button class="btn btn-sm btn-outline-primary me-1">查看</button><button class="btn btn-sm btn-outline-secondary">编辑</button></td></tr></tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</div>

<div class="offcanvas offcanvas-start" id="offcanvasSidebar">
  <div class="offcanvas-header border-bottom"><h5 class="offcanvas-title fw-bold">企业后台系统</h5><button class="btn-close" data-bs-dismiss="offcanvas"></button></div>
  <div class="offcanvas-body p-0"><ul class="nav flex-column py-2">...</ul></div>
</div>

<style>
@layer utilities {
  .table-pc-wrapper { overflow-x: auto; max-height: 600px; position: relative; }
  .table-pc thead { position: sticky; top: 0; background: var(--bs-body-bg); z-index: 10; box-shadow: 0 1px 0 var(--bs-border-color); }
  .table-pc .fixed-left { position: sticky; left: 0; background: var(--bs-body-bg); z-index: 5; }
  .table-pc .fixed-right { position: sticky; right: 0; background: var(--bs-body-bg); z-index: 5; }
  .table-pc thead .fixed-left, .table-pc thead .fixed-right { z-index: 15; }
}
</style>
<link rel="stylesheet" href="https://cdn.bootcdn.net/ajax/libs/bootstrap-icons/1.11.3/font/bootstrap-icons.min.css">
```

#### （3）Bootstrap PC端进阶能力

1. Utility API自定义工具类；2. 原生表单组件增强；3. 模态框/抽屉层级管理；4. RTL布局原生支持。

---

### 2. Tailwind CSS 3.4+（原子化CSS框架）

无预设样式，JIT按需生成，支持CSS变量、Container Queries、暗黑模式，企业级PC端定制化首选。

#### （1）PC端全场景配置（tailwind.config.js）

```javascript
module.exports = {
  content: ["./src/**/*.{html,js,ts,vue,jsx,tsx}"],
  darkMode: "class",
  theme: {
    screens: { sm: "640px", md: "768px", lg: "1024px", xl: "1280px", "2xl": "1920px", "2k": "2560px", "4k": "3840px", "8k": "7680px" },
    extend: {
      fontSize: { "pc-xs": "0.75rem", "pc-sm": "0.875rem", "pc-base": "1rem", "pc-lg": "1.125rem", "pc-xl": "1.25rem", "pc-2xl": "1.5rem", "pc-3xl": "1.75rem" },
      spacing: { "pc-1": "0.25rem", "pc-2": "0.5rem", "pc-3": "0.75rem", "pc-4": "1rem", "pc-5": "1.25rem", "pc-6": "1.5rem", "pc-8": "2rem", "pc-10": "2.5rem", "pc-12": "3rem" },
      borderRadius: { "pc-sm": "2px", "pc": "4px", "pc-md": "6px", "pc-lg": "8px" },
      boxShadow: { "pc-sm": "0 1px 2px 0 rgba(0,0,0,0.05)", "pc": "0 1px 3px 0 rgba(0,0,0,0.1), 0 1px 2px -1px rgba(0,0,0,0.1)", "pc-md": "0 4px 6px -1px rgba(0,0,0,0.1), 0 2px 4px -2px rgba(0,0,0,0.1)", "pc-lg": "0 10px 15px -3px rgba(0,0,0,0.1), 0 4px 6px -4px rgba(0,0,0,0.1)" },
      colors: { primary: { 50: "#f0f7ff", 100: "#e0efff", 200: "#bae0ff", 300: "#7cc3ff", 400: "#36a0ff", 500: "#0d84ff", 600: "#0066ff", 700: "#0052d9", 800: "#0044b3", 900: "#00388f", 950: "#00235c" } }
    }
  },
  plugins: [
    require("@tailwindcss/forms"), require("@tailwindcss/container-queries"), require("@tailwindcss/aspect-ratio"), require("@tailwindcss/typography"),
    function ({ addUtilities }) {
      addUtilities({
        ".pc-sticky-header": { position: "sticky", top: "0", zIndex: "10", backgroundColor: "inherit" },
        ".pc-unselectable": { "-webkit-user-select": "none", "-moz-user-select": "none", "user-select": "none" },
        ".pc-hover-lift": { transition: "transform 0.2s ease, box-shadow 0.2s ease", willChange: "transform, box-shadow" },
        ".pc-hover-lift:hover": { transform: "translateY(-2px)" },
        ".pc-content-max": { maxWidth: "1920px", marginLeft: "auto", marginRight: "auto" },
        ".pc-scrollbar": { scrollbarWidth: "thin", scrollbarColor: "rgba(0,0,0,0.2) transparent" },
        ".pc-scrollbar::-webkit-scrollbar": { width: "6px", height: "6px" },
        ".pc-scrollbar::-webkit-scrollbar-thumb": { backgroundColor: "rgba(0,0,0,0.2)", borderRadius: "3px" },
        ".pc-scrollbar::-webkit-scrollbar-track": { backgroundColor: "transparent" }
      });
    }
  ]
};
```

#### （2）PC端经典布局：侧边栏固定+主内容自适应（原子化实现）

```html
<!DOCTYPE html>
<html lang="zh-CN" class="pc-scrollbar">
<head><meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0"><title>PC端Tailwind布局</title><script src="https://cdn.tailwindcss.com"></script><script src="https://cdn.jsdelivr.net/npm/feather-icons@4.29.0/dist/feather.min.js"></script><script>tailwind.config = { /* 配置内容 */ }</script></head>
<body class="bg-gray-50 text-gray-900 dark:bg-gray-950 dark:text-gray-100">
  <div class="flex h-screen overflow-hidden">
    <aside class="hidden lg:flex flex-col w-60 2xl:w-70 2k:w-70 4k:w-70 flex-shrink-0 bg-white dark:bg-gray-900 border-r border-gray-200 dark:border-gray-800">
      <div class="h-16 flex items-center px-pc-4 border-b"><h1 class="text-pc-xl font-bold text-primary-600 dark:text-primary-500">企业管理系统</h1></div>
      <nav class="flex-1 overflow-auto py-pc-2 pc-scrollbar">
        <ul class="space-y-pc-1 px-pc-2">
          <li><a href="#" class="flex items-center gap-pc-3 px-pc-3 py-pc-2 rounded-pc bg-primary-50 dark:bg-primary-900/20 text-primary-600"><i data-feather="home" class="w-5 h-5"></i><span>首页</span></a></li>
          <li><button class="w-full flex items-center justify-between gap-pc-3 px-pc-3 py-pc-2 rounded-pc hover:bg-gray-100" onclick="this.nextElementSibling.classList.toggle('hidden')"><span class="flex items-center gap-pc-3"><i data-feather="bar-chart-2" class="w-5 h-5"></i><span>数据管理</span></span><i data-feather="chevron-down" class="w-4 h-4"></i></button><ul class="mt-pc-1 ml-pc-6 space-y-pc-1 hidden"><li><a href="#" class="block px-pc-3 py-pc-2 rounded-pc text-pc-sm hover:bg-gray-100">数据看板</a></li><li><a href="#" class="block px-pc-3 py-pc-2 rounded-pc text-pc-sm hover:bg-gray-100">数据导出</a></li></ul></li>
          <li><a href="#" class="flex items-center gap-pc-3 px-pc-3 py-pc-2 rounded-pc hover:bg-gray-100"><i data-feather="settings" class="w-5 h-5"></i><span>系统设置</span></a></li>
        </ul>
      </nav>
      <div class="p-pc-3 border-t"><div class="flex items-center gap-pc-3"><img src="https://picsum.photos/40/40" class="rounded-full w-10 h-10"><div><p class="font-medium text-pc-sm truncate">管理员</p><p class="text-gray-500 text-xs truncate">超级管理员</p></div></div></div>
    </aside>
    <main class="flex-1 flex flex-col overflow-hidden">
      <header class="h-16 flex items-center justify-between px-pc-4 border-b bg-white dark:bg-gray-900 flex-shrink-0">
        <button class="lg:hidden p-pc-2 rounded-pc hover:bg-gray-100" onclick="document.getElementById('mobile-sidebar').classList.toggle('hidden')"><i data-feather="menu" class="w-5 h-5"></i></button>
        <div class="flex items-center gap-pc-4 ml-auto">
          <div class="hidden md:flex items-center rounded-pc border bg-gray-50 dark:bg-gray-800 px-pc-3 py-pc-2 w-64"><i data-feather="search" class="w-4 h-4 text-gray-400 mr-pc-2"></i><input type="text" placeholder="全局搜索" class="bg-transparent border-none outline-none w-full text-pc-sm"></div>
          <button class="p-pc-2 rounded-pc hover:bg-gray-100" onclick="document.documentElement.classList.toggle('dark')"><i data-feather="moon" class="w-5 h-5 dark:hidden"></i><i data-feather="sun" class="w-5 h-5 hidden dark:block"></i></button>
          <button class="p-pc-2 rounded-pc hover:bg-gray-100 relative"><i data-feather="bell" class="w-5 h-5"></i><span class="absolute top-1 right-1 w-2 h-2 bg-red-500 rounded-full"></span></button>
        </div>
      </header>
      <div class="flex-1 overflow-auto p-pc-4 2xl:p-pc-6 pc-scrollbar 4k:max-w-[2560px] 4k:mx-auto w-full">
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 2xl:grid-cols-5 2k:grid-cols-6 gap-pc-4 mb-pc-6">
          <div class="bg-white dark:bg-gray-900 rounded-pc-lg shadow-pc-sm p-pc-4 pc-hover-lift"><p class="text-gray-500 text-pc-sm mb-pc-2">总访问量</p><h3 class="text-pc-3xl font-bold mb-pc-1">128,560</h3><span class="text-green-500 text-pc-sm">+12.5% 环比</span></div>
          <div class="bg-white dark:bg-gray-900 rounded-pc-lg shadow-pc-sm p-pc-4 pc-hover-lift"><p class="text-gray-500 text-pc-sm mb-pc-2">订单数</p><h3 class="text-pc-3xl font-bold mb-pc-1">3,241</h3><span class="text-green-500 text-pc-sm">+8.2% 环比</span></div>
          <div class="bg-white dark:bg-gray-900 rounded-pc-lg shadow-pc-sm p-pc-4 pc-hover-lift"><p class="text-gray-500 text-pc-sm mb-pc-2">成交额</p><h3 class="text-pc-3xl font-bold mb-pc-1">¥ 586,230</h3><span class="text-green-500 text-pc-sm">+15.8% 环比</span></div>
          <div class="bg-white dark:bg-gray-900 rounded-pc-lg shadow-pc-sm p-pc-4 pc-hover-lift"><p class="text-gray-500 text-pc-sm mb-pc-2">用户数</p><h3 class="text-pc-3xl font-bold mb-pc-1">24,689</h3><span class="text-green-500 text-pc-sm">+5.3% 环比</span></div>
        </div>
        <div class="bg-white dark:bg-gray-900 rounded-pc-lg shadow-pc-sm overflow-hidden">
          <div class="p-pc-4 border-b flex items-center justify-between"><h6 class="font-bold text-pc-lg">订单列表</h6><button class="bg-primary-600 hover:bg-primary-700 text-white px-pc-4 py-pc-2 rounded-pc text-pc-sm font-medium">导出数据</button></div>
          <div class="overflow-x-auto pc-scrollbar">
            <table class="w-full text-left">
              <thead class="bg-gray-50 dark:bg-gray-800"><tr><th class="pc-sticky-header left-0 z-20 px-pc-4 py-pc-3 text-pc-sm border-b w-20">序号</th><th class="pc-sticky-header px-pc-4 py-pc-3 text-pc-sm border-b w-40">订单编号</th><th class="pc-sticky-header px-pc-4 py-pc-3 text-pc-sm border-b w-32">用户名称</th><th class="pc-sticky-header px-pc-4 py-pc-3 text-pc-sm border-b w-32">订单金额</th><th class="pc-sticky-header px-pc-4 py-pc-3 text-pc-sm border-b w-28">支付状态</th><th class="pc-sticky-header px-pc-4 py-pc-3 text-pc-sm border-b w-40">创建时间</th><th class="pc-sticky-header px-pc-4 py-pc-3 text-pc-sm border-b w-40">更新时间</th><th class="pc-sticky-header px-pc-4 py-pc-3 text-pc-sm border-b min-w-[200px]">收货地址</th><th class="pc-sticky-header right-0 z-20 px-pc-4 py-pc-3 text-pc-sm border-b w-32 bg-gray-50 dark:bg-gray-800">操作</th></tr></thead>
              <tbody class="divide-y divide-gray-200 dark:divide-gray-800"><tr class="hover:bg-gray-50"><td class="sticky left-0 bg-inherit px-pc-4 py-pc-3 text-pc-sm">1</td><td class="px-pc-4 py-pc-3 text-pc-sm font-medium">ORD20250408001</td><td class="px-pc-4 py-pc-3 text-pc-sm">张三</td><td class="px-pc-4 py-pc-3 text-pc-sm font-medium">¥ 2599.00</td><td class="px-pc-4 py-pc-3 text-pc-sm"><span class="bg-green-100 text-green-700 dark:bg-green-900/30 dark:text-green-400 px-2 py-1 rounded-full text-xs">已支付</span></td><td class="px-pc-4 py-pc-3 text-pc-sm text-gray-500">2025-04-08 10:20</td><td class="px-pc-4 py-pc-3 text-pc-sm text-gray-500">2025-04-08 10:30</td><td class="px-pc-4 py-pc-3 text-pc-sm text-gray-500">北京市朝阳区XX街道XX小区1号楼1单元101室</td><td class="sticky right-0 bg-inherit px-pc-4 py-pc-3 text-pc-sm flex gap-2"><button class="text-primary-600 hover:text-primary-700">查看</button><button class="text-gray-600 hover:text-gray-900">编辑</button></td></tr></tbody>
            </table>
          </div>
        </div>
      </div>
    </main>
  </div>
  <div id="mobile-sidebar" class="fixed inset-0 z-50 bg-black/50 hidden lg:hidden" onclick="this.classList.add('hidden')"><aside class="absolute top-0 left-0 h-full w-60 bg-white dark:bg-gray-900 overflow-auto" onclick="event.stopPropagation()"><!-- 与大屏侧边栏内容一致 --></aside></div>
  <script>feather.replace()</script>
</body>
</html>
```

#### （3）Tailwind生态PC端进阶推荐

1. UnoCSS：更轻量即时原子化引擎；2. 官方插件生态：@tailwindcss/forms、@tailwindcss/container-queries；3. 组件封装：@apply封装高频组件；4. 设计系统同步：Figma Tokens自动生成配置。

---

### 3. 其他主流PC端CSS框架

| 框架            | 最新版本 | 核心优势                                | PC端适用场景                   |
| --------------- | -------- | --------------------------------------- | ------------------------------ |
| Bulma 1.0+      | 1.0.2    | 纯CSS无JS依赖、Flexbox驱动、语义化类名  | 快速搭建官网、营销页、简单后台 |
| UIkit 3.20+     | 3.21.13  | 轻量高性能、组件丰富、动画流畅、RTL支持 | 交互复杂的官网、产品页、中后台 |
| Pure.css        | 3.0.0    | 体积4KB、模块化引入、无依赖             | 轻量级页面、极简项目           |
| Foundation 6.8+ | 6.8.1    | 企业级成熟、栅格强大、无障碍原生支持    | 大型企业级、金融/政务类项目    |

---

## 二、主流前端组件框架PC端实现

PC端企业级开发以Vue3、React18为核心，以下为2025年最新稳定版PC端实现，含无渲染组件库、跨框架方案。

### 1. Vue3生态PC端组件库

#### （1）Element Plus 2.8+（Vue3国民级组件库）

基于Vue3组合式API，TypeScript支持，原生虚拟滚动、暗黑模式、国际化。

**核心升级：PC端全局配置+布局封装**

```vue
<template>
  <el-config-provider :locale="zhCn" :breakpoints="pcBreakpoints" :theme="pcTheme">
    <el-container class="h-screen overflow-hidden">
      <el-aside :width="isCollapse ? '64px' : '240px'" class="border-r bg-white dark:bg-gray-900">
        <div class="h-16 flex items-center justify-center border-b"><h1 v-if="!isCollapse" class="text-xl font-bold text-primary">企业管理系统</h1><el-icon v-else size="24" color="var(--el-color-primary)"><Menu /></el-icon></div>
        <el-menu :default-active="route.path" :collapse="isCollapse" :unique-opened="true" router class="h-[calc(100vh-64px)] border-none">
          <el-menu-item index="/"><el-icon><House /></el-icon><template #title>首页</template></el-menu-item>
          <el-sub-menu index="/data"><template #title><el-icon><DataAnalysis /></el-icon><span>数据管理</span></template><el-menu-item index="/data/dashboard">数据看板</el-menu-item><el-menu-item index="/data/export">数据导出</el-menu-item></el-sub-menu>
          <el-menu-item index="/system"><el-icon><Setting /></el-icon><template #title>系统设置</template></el-menu-item>
        </el-menu>
      </el-aside>
      <el-container>
        <el-header class="h-16 flex items-center justify-between px-4 border-b bg-white dark:bg-gray-900">
          <el-button :icon="isCollapse ? Expand : Fold" circle @click="isCollapse = !isCollapse" />
          <div class="flex items-center gap-4 ml-auto">
            <el-input placeholder="全局搜索" clearable style="width: 240px"><template #prefix><el-icon><Search /></el-icon></template></el-input>
            <el-button :icon="isDark ? Sunny : Moon" circle @click="toggleDark" />
            <el-dropdown trigger="click"><div class="flex items-center gap-2 cursor-pointer"><el-avatar :size="32" src="https://picsum.photos/32/32" /><span class="hidden md:inline">管理员</span></div><template #dropdown><el-dropdown-menu><el-dropdown-item>个人中心</el-dropdown-item><el-dropdown-item divided>退出登录</el-dropdown-item></el-dropdown-menu></template></el-dropdown>
          </div>
        </el-header>
        <el-main class="p-4 overflow-auto bg-gray-50 dark:bg-gray-950"><router-view v-slot="{ Component }"><transition name="fade-slide" mode="out-in"><component :is="Component" /></transition></router-view></el-main>
      </el-container>
    </el-container>
  </el-config-provider>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'
import { useDark, useToggle } from '@vueuse/core'
import zhCn from 'element-plus/dist/locale/zh-cn.mjs'
import { House, DataAnalysis, Setting, Menu, Fold, Expand, Search, Moon, Sunny } from '@element-plus/icons-vue'

const route = useRoute()
const isCollapse = ref(false)
const isDark = useDark()
const toggleDark = useToggle(isDark)
const pcBreakpoints = { xs:0, sm:576, md:768, lg:992, xl:1200, xxl:1920, '2k':2560, '4k':3840 }
const pcTheme = computed(() => ({ colorPrimary: '#0d84ff', fontSize: 14, borderRadius: '4px', buttonSize: 'default', table: { headerBgColor: isDark.value ? '#1f2937' : '#f9fafb', rowHoverBgColor: isDark.value ? '#374151' : '#f3f4f6' } }))
</script>

<style scoped>
.fade-slide-enter-active, .fade-slide-leave-active { transition: all 0.3s ease; }
.fade-slide-enter-from { opacity: 0; transform: translateX(10px); }
.fade-slide-leave-to { opacity: 0; transform: translateX(-10px); }
</style>
```

**ElTable虚拟滚动实现**

```vue
<template>
  <el-table :data="tableData" border height="600" v-loading="loading" :virtualized="true" :row-key="(row) => row.id" :scroll="{ x: 1500, y: 600 }">
    <el-table-column prop="id" label="ID" fixed="left" width="80" />
    <el-table-column prop="orderNo" label="订单编号" width="180" sortable />
    <el-table-column prop="userName" label="用户名称" width="120" />
    <el-table-column prop="amount" label="订单金额" width="120" sortable />
    <el-table-column prop="payStatus" label="支付状态" width="100"><template #default="{ row }"><el-tag :type="row.payStatus === 1 ? 'success' : 'warning'">{{ row.payStatus === 1 ? '已支付' : '待支付' }}</el-tag></template></el-table-column>
    <el-table-column prop="createTime" label="创建时间" width="180" />
    <el-table-column prop="address" label="收货地址" min-width="200" show-overflow-tooltip />
    <el-table-column label="操作" fixed="right" width="150"><template #default><el-button type="primary" size="small" link>查看</el-button><el-button type="default" size="small" link>编辑</el-button></template></el-table-column>
  </el-table>
</template>

<script setup>
import { ref, onMounted } from 'vue'
const loading = ref(false)
const tableData = ref([])
const mockData = () => { const data = []; for (let i=1; i<=100000; i++) data.push({ id:i, orderNo:`ORD${String(i).padStart(8,'0')}`, userName:`用户${i}`, amount:(Math.random()*10000).toFixed(2), payStatus:Math.random()>0.3?1:0, createTime:new Date(Date.now()-Math.random()*30*24*60*60*1000).toLocaleString(), address:`北京市朝阳区XX街道XX小区${i}号楼` }); return data }
onMounted(() => { loading.value=true; setTimeout(()=>{ tableData.value=mockData(); loading.value=false },500) })
</script>
```

#### （2）Vue3其他PC端组件库

1. Naive UI 2.40+：全量TypeScript，主题定制极致灵活；2. Arco Design Vue 2.60+：字节出品，企业级设计系统；3. TDesign Vue Next 1.8+：腾讯出品，跨端一致设计；4. Vuetify 3.6+：谷歌Material Design，国际化支持。

---

### 2. React18生态PC端组件库

#### （1）Ant Design 5.20+（React企业级首选）

基于React18并发模式，CSS-in-JS动态主题，原生虚拟滚动、无障碍、国际化。

**核心升级：PC端全局配置+布局封装**

```jsx
import { useState, useMemo } from 'react'
import { Layout, Menu, Button, Input, Dropdown, Avatar, ConfigProvider, theme } from 'antd'
import { MenuFoldOutlined, MenuUnfoldOutlined, HomeOutlined, BarChartOutlined, SettingOutlined, SearchOutlined, MoonOutlined, SunOutlined, UserOutlined } from '@ant-design/icons'
import { Outlet, useLocation, useNavigate } from 'react-router-dom'
import zhCN from 'antd/locale/zh_CN'
import './PcLayout.css'

const { Header, Sider, Content } = Layout

const PcLayout = () => {
  const [collapsed, setCollapsed] = useState(false)
  const [darkMode, setDarkMode] = useState(false)
  const location = useLocation()
  const navigate = useNavigate()
  const menuItems = [
    { key: '/', icon: <HomeOutlined />, label: '首页' },
    { key: '/data', icon: <BarChartOutlined />, label: '数据管理', children: [{ key: '/data/dashboard', label: '数据看板' }, { key: '/data/export', label: '数据导出' }] },
    { key: '/system', icon: <SettingOutlined />, label: '系统设置' },
  ]
  const antdTheme = useMemo(() => ({
    token: { colorPrimary: '#165DFF', fontSize: 14, borderRadius: 4, paddingSM: 12, paddingMD: 16 },
    algorithm: darkMode ? theme.darkAlgorithm : theme.defaultAlgorithm,
    screen: { xs:0, sm:576, md:768, lg:992, xl:1200, xxl:1920, '2k':2560, '4k':3840 },
    components: { Table: { headerBg: darkMode ? '#1f2937' : '#fafafa', rowHoverBg: darkMode ? '#374151' : '#f5f5f5' }, Menu: { darkItemBg: '#1f2937' } }
  }), [darkMode])
  const userMenuItems = [{ key: 'profile', label: '个人中心' }, { type: 'divider' }, { key: 'logout', label: '退出登录' }]

  return (
    <ConfigProvider locale={zhCN} theme={antdTheme}>
      <Layout style={{ height: '100vh', overflow: 'hidden' }}>
        <Sider trigger={null} collapsible collapsed={collapsed} width={240} theme={darkMode ? 'dark' : 'light'} className="pc-sider">
          <div className={`logo h-16 flex items-center justify-center border-b ${collapsed ? 'px-0' : 'px-4'}`}>{!collapsed ? <h1 className="text-xl font-bold text-primary">企业管理系统</h1> : <span className="text-2xl text-primary">企</span>}</div>
          <Menu mode="inline" selectedKeys={[location.pathname]} openKeys={['/data']} items={menuItems} onClick={({ key }) => navigate(key)} style={{ height: 'calc(100vh - 64px)' }} inlineCollapsed={collapsed} />
        </Sider>
        <Layout>
          <Header className="pc-header flex items-center justify-between px-4">
            <Button type="text" icon={collapsed ? <MenuUnfoldOutlined /> : <MenuFoldOutlined />} onClick={() => setCollapsed(!collapsed)} style={{ fontSize: '16px', width: 64, height: 64 }} />
            <div className="flex items-center gap-4 ml-auto">
              <Input placeholder="全局搜索" prefix={<SearchOutlined />} allowClear style={{ width: 240 }} />
              <Button type="text" icon={darkMode ? <SunOutlined /> : <MoonOutlined />} onClick={() => setDarkMode(!darkMode)} style={{ fontSize: '16px', width: 40, height: 40 }} />
              <Dropdown menu={{ items: userMenuItems }} trigger={['click']}><div className="flex items-center gap-2 cursor-pointer"><Avatar size={32} icon={<UserOutlined />} src="https://picsum.photos/32/32" /><span className="hidden md:inline">管理员</span></div></Dropdown>
            </div>
          </Header>
          <Content className="pc-content"><Outlet /></Content>
        </Layout>
      </Layout>
    </ConfigProvider>
  )
}
export default PcLayout
```

**Ant Design Table虚拟滚动+远程分页**

```jsx
import { useState, useEffect, useCallback } from 'react'
import { Table, Tag, Button, Space, Spin } from 'antd'

const OrderTable = () => {
  const [loading, setLoading] = useState(false)
  const [dataSource, setDataSource] = useState([])
  const [pagination, setPagination] = useState({ current:1, pageSize:20, total:0, showSizeChanger:true, pageSizeOptions:['10','20','50','100'], showTotal:(total)=>`共 ${total} 条数据` })
  const columns = [
    { title:'序号', dataIndex:'id', fixed:'left', width:80 }, { title:'订单编号', dataIndex:'orderNo', width:180, sorter:true }, { title:'用户名称', dataIndex:'userName', width:120 },
    { title:'订单金额', dataIndex:'amount', width:120, sorter:true }, { title:'支付状态', dataIndex:'payStatus', width:100, render:(status)=><Tag color={status===1?'success':'warning'}>{status===1?'已支付':'待支付'}</Tag>, filters:[{text:'已支付',value:1},{text:'待支付',value:0}] },
    { title:'创建时间', dataIndex:'createTime', width:180 }, { title:'收货地址', dataIndex:'address', minWidth:200, ellipsis:true }, { title:'操作', fixed:'right', width:150, render:()=><Space><Button type="link" size="small">查看</Button><Button type="link" size="small">编辑</Button></Space> }
  ]
  const fetchData = useCallback((page, pageSize, filters, sorter) => { setLoading(true); setTimeout(()=>{ const total=10000; const data=[]; for(let i=(page-1)*pageSize+1; i<=Math.min(page*pageSize,total); i++) data.push({ key:i, id:i, orderNo:`ORD${String(i).padStart(8,'0')}`, userName:`用户${i}`, amount:(Math.random()*10000).toFixed(2), payStatus:Math.random()>0.3?1:0, createTime:new Date(Date.now()-Math.random()*30*24*60*60*1000).toLocaleString(), address:`北京市朝阳区XX街道XX小区${i}号楼` }); setDataSource(data); setPagination(prev=>({...prev, current:page, pageSize, total})); setLoading(false) },500) },[])
  const handleTableChange = (pagination, filters, sorter) => fetchData(pagination.current, pagination.pageSize, filters, sorter)
  useEffect(()=>{ fetchData(pagination.current, pagination.pageSize, {}, {}) },[fetchData, pagination.current, pagination.pageSize])
  return (<div className="bg-white rounded-lg shadow-sm"><div className="p-4 border-b flex items-center justify-between"><h3 className="text-lg font-bold mb-0">订单列表</h3><Button type="primary">导出数据</Button></div><Spin spinning={loading}><Table columns={columns} dataSource={dataSource} pagination={pagination} onChange={handleTableChange} bordered={false} scroll={{ x:1500, y:600 }} virtual rowKey="key" /></Spin></div>)
}
export default OrderTable
```

#### （2）React其他PC端组件库

1. Shadcn UI + Radix UI：无渲染组件，100%定制化；2. Semi Design 2.50+：抖音出品，A11y无障碍；3. MUI 5.15+：全球最流行React UI库；4. Arco Design React 2.60+：字节出品，跨团队协作友好。

---

### 3. 跨框架PC端组件方案

1. Shoelace：Web Components跨框架；2. Fast UI：字节跨框架Web Components；3. PrimeNG/PrimeReact/PrimeVue：一套设计规范多框架实现。

---

## 三、PC端性能与多屏深度优化

基于原生Web API+框架最新特性，从多屏适配、资源加载、渲染性能、交互体验、大数据处理五大维度优化。

### 1. 多屏渲染优化

#### （1）组件级自适应：CSS Container Queries

```css
.pc-card-container { container-type: inline-size; container-name: cardContainer; }
@container cardContainer (min-width: 280px) { .card-content { flex-direction: row; } .card-title { font-size: 1.125rem; } }
@container cardContainer (min-width: 360px) { .card-content { gap: 1.5rem; } .card-title { font-size: 1.25rem; } }
@container cardContainer (min-width: 480px) { .card-extra { display: block; } }
```

#### （2）高DPI/超宽屏资源适配

1. 矢量图标优先；2. 多分辨率图片：`<img src="./card-1x.avif" srcset="./card-1x.avif 1x, ./card-2x.avif 2x, ./card-3x.avif 3x">`；3. 系统缩放适配：`window.devicePixelRatio`动态调整；4. 线性自适应单位：`clamp()`。

```javascript
function adaptSystemScale() {
  const scale = window.devicePixelRatio || 1
  const html = document.documentElement
  if (scale !== 1 && navigator.userAgent.includes('Windows')) {
    html.style.setProperty('--system-scale', 1/scale)
    html.style.transform = `scale(var(--system-scale))`
    html.style.transformOrigin = 'top left'
    html.style.width = `${scale * 100}%`
    html.style.height = `${scale * 100}vh`
  } else { /* 清除样式 */ }
}
adaptSystemScale()
window.addEventListener('resize', debounce(adaptSystemScale, 300))
```

```css
.pc-title { font-size: clamp(1.25rem, 2vw, 2rem); }
.pc-content { width: min(90vw, max(1200px, 100%)); max-width: 1920px; margin: 0 auto; }
.pc-card { width: clamp(280px, 15vw, 360px); }
```

#### （3）页面切换动画：View Transitions API

```javascript
// 路由切换
router.afterEach((to, from) => { if (!document.startViewTransition) return; document.startViewTransition(() => { /* DOM更新 */ }) })
// 暗黑模式切换
function toggleDark() { if (!document.startViewTransition) { document.documentElement.classList.toggle('dark'); return } const transition = document.startViewTransition(() => { document.documentElement.classList.toggle('dark') }); transition.ready.then(() => { document.documentElement.animate({ clipPath: ['circle(0% at 50% 50%)', 'circle(150% at 50% 50%)'] }, { duration: 500, pseudoElement: '::view-transition-new(root)' }) }) }
```

---

### 2. 资源加载极致优化

#### （1）资源加载优先级策略

```html
<link rel="preload" href="/css/main.css" as="style">
<link rel="preload" href="/fonts/Inter-Regular.woff2" as="font" type="font/woff2" crossorigin>
<link rel="preload" href="/images/hero-banner.avif" as="image">
<link rel="modulepreload" href="/js/main.js">
<link rel="prefetch" href="/css/data.css" as="style">
<link rel="prerender" href="/dashboard">
```

#### （2）代码分包与懒加载

**Vue3+Vite分包**：
```javascript
// vite.config.js
export default defineConfig({
  build: { rollupOptions: { output: { manualChunks: { 'vue-vendor': ['vue','vue-router','pinia'], 'ui-vendor': ['element-plus'], 'utils-vendor': ['axios','dayjs','@vueuse/core'] } } } }
})
// 路由懒加载
const routes = [{ path:'/', component: ()=>import('@/views/Home.vue') }]
```

**React18+Vite分包**：
```javascript
export default defineConfig({
  build: { rollupOptions: { output: { manualChunks: { 'react-vendor': ['react','react-dom','react-router-dom'], 'ui-vendor': ['antd'], 'utils-vendor': ['axios','dayjs'] } } } }
})
// 路由懒加载+Suspense
const Home = lazy(() => import('@/views/Home'))
```

#### （3）字体与图片优化

```css
@font-face { font-family:'Inter PC'; src:url('./fonts/Inter-Regular.woff2') format('woff2'); font-display:swap; unicode-range:U+0020-007E,U+4E00-9FFF; }
```
```html
<img src="./images/1.avif" loading="lazy" width="280" height="160">
```

---

### 3. 渲染与交互性能优化

#### （1）CSS渲染性能优化

```css
.pc-card { transition: transform 0.2s ease, box-shadow 0.2s ease; will-change: transform, box-shadow; }
.pc-card:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
```

#### （2）JS渲染性能优化

防抖/节流：
```javascript
export function debounce(fn, delay=200) { let timer; return function(...args){ clearTimeout(timer); timer=setTimeout(()=>fn.apply(this,args),delay) } }
export function throttle(fn, interval=100) { let lastTime=0; return function(...args){ const now=Date.now(); if(now-lastTime>=interval){ lastTime=now; fn.apply(this,args) } } }
```

框架优化：Vue3使用`v-memo`/`shallowRef`；React18使用`useDeferredValue`/`useTransition`/`useMemo`。

#### （3）键鼠交互优化

```css
@media (hover: hover) and (pointer: fine) { .pc-btn:hover { background: #165DFF; } }
.pc-btn:focus-visible { outline: 2px solid #165DFF; outline-offset: 2px; }
```

快捷键：
```javascript
document.addEventListener('keydown', (e) => {
  if (e.key === 'Escape') { /* 关闭弹窗 */ }
  if (e.ctrlKey && e.key === 's') { e.preventDefault(); document.dispatchEvent(new CustomEvent('pc:save')) }
  if (e.ctrlKey && e.key === 'f') { e.preventDefault(); document.querySelector('.pc-search-input')?.focus() }
})
```

---

### 4. 大数据量渲染优化

#### （1）虚拟滚动核心原理

```vue
<template>
  <div class="virtual-list" ref="containerRef" @scroll="handleScroll">
    <div class="virtual-list-phantom" :style="{ height: totalHeight + 'px' }"></div>
    <div class="virtual-list-content" :style="{ transform: `translateY(${offsetY}px)` }">
      <div v-for="item in visibleData" :key="item.id" :style="{ height: itemHeight + 'px' }">{{ item.content }}</div>
    </div>
  </div>
</template>
<script setup>
const itemHeight=60, preloadCount=5
const scrollTop=ref(0), totalData=ref([...Array(100000)].map((_,i)=>({id:i+1, content:`列表项 ${i+1}`})))
const totalHeight=computed(()=>totalData.value.length*itemHeight)
const startIndex=computed(()=>Math.max(0,Math.floor(scrollTop.value/itemHeight)-preloadCount))
const endIndex=computed(()=>Math.min(totalData.value.length-1,Math.floor((scrollTop.value+containerRef.value?.clientHeight)/itemHeight)+preloadCount))
const visibleData=computed(()=>totalData.value.slice(startIndex.value,endIndex.value+1))
const offsetY=computed(()=>startIndex.value*itemHeight)
const handleScroll=()=>{ scrollTop.value=containerRef.value.scrollTop }
</script>
```

#### （2）Web Workers离线计算

```javascript
// worker.js
self.onmessage = (e) => { const { data, type, params } = e.data; let result=[]; if(type==='sort') result=data.sort((a,b)=>params.order==='ascend'?a[params.key]-b[params.key]:b[params.key]-a[params.key]); if(type==='filter') result=data.filter(item=>Object.values(item).some(val=>String(val).includes(params.keyword))); if(type==='statistics') result={ total:data.length, sum:data.reduce((acc,cur)=>acc+cur.amount,0), avg:data.reduce((acc,cur)=>acc+cur.amount,0)/data.length }; self.postMessage(result) }
// 主线程
const worker = new Worker('./worker.js')
function calculateData(data, type, params) { return new Promise(resolve=>{ worker.onmessage=e=>resolve(e.data); worker.postMessage({data,type,params}) }) }
```

---

## 四、PC端开发核心场景最佳实践

### 1. 暗黑模式与主题系统

```css
:root { --pc-bg-primary:#fff; --pc-text-primary:#303133; }
:root[data-theme="dark"] { --pc-bg-primary:#121212; --pc-text-primary:#e8e8e8; }
body { background: var(--pc-bg-secondary); color: var(--pc-text-primary); transition: background 0.3s, color 0.3s; }
```

```javascript
export function useTheme() {
  const currentTheme = ref(localStorage.getItem('pc-theme') || 'light')
  const initTheme = () => { /* 本地存储或系统主题 */ document.documentElement.setAttribute('data-theme', currentTheme.value) }
  const toggleTheme = (theme) => { const newTheme = theme || (currentTheme.value==='light'?'dark':'light'); currentTheme.value=newTheme; document.documentElement.setAttribute('data-theme', newTheme); localStorage.setItem('pc-theme', newTheme) }
  return { currentTheme, initTheme, toggleTheme }
}
```

### 2. PC端无障碍（a11y）适配

1. 语义化HTML；2. ARIA属性：`role`/`aria-label`/`aria-expanded`；3. 色彩对比度≥4.5:1；4. 键盘导航与焦点可见。

### 3. PC端浮层层级管理规范

| 浮层类型      | z-index | 适用场景              |
| ------------- | ------- | --------------------- |
| 下拉菜单/气泡 | 1000    | 下拉选择器、Tooltip   |
| 固定导航/吸顶 | 1020    | 顶部导航、侧边栏      |
| 遮罩层        | 1040    | 弹窗背景遮罩          |
| 弹窗/抽屉     | 1050    | 对话框、抽屉          |
| 全局通知      | 1080    | Message、Notification |
| 全局加载      | 1100    | 页面加载遮罩          |

---

## 五、PC端开发核心注意事项

### 1. 框架与工程化规范
- CSS框架优先变量覆盖；组件库按需引入；ESLint+Prettier+TypeScript；开启gzip/brotli压缩。

### 2. 适配与设计规范
- 断点：1366×768/1920×1080/2560×1440/3840×2160；内容最大宽度2560px；热区≥24×24px；正文字号≥12px。

### 3. 交互与体验规范
- 键鼠双路径；不可逆操作二次确认；超过300ms显示加载；大数据表格支持排序/筛选/分页。

### 4. 兼容性与安全规范
- 浏览器：Chrome 100+、Edge 100+、Firefox 100+、Safari 15+；系统：Windows 10/11、macOS 12+；XSS过滤、CSRF Token、权限控制；官网做好SEO。