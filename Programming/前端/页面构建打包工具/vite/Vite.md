# Vite
## 一、基础认知与环境搭建
### 1.1 核心认知
Vite 是由Vue.js作者尤雨溪开发的**新一代前端构建工具**，基于原生ES Module（ESM）和Rollup，实现了**极速开发体验**和**优化的生产构建**。
- 核心优势：开发服务器秒启动、热更新（HMR）极速响应、原生ESM支持、开箱即用的TypeScript/Vue/React支持、基于Rollup的生产构建、强大的插件生态
- 适用场景：Vue/React/Svelte/Preact等框架项目、原生JS/TS项目、静态站点
- 版本要求：推荐使用Vite最新LTS版本，配套Node.js 18+ LTS版本

### 1.2 前置知识要求
- HTML/CSS/JavaScript 基础语法
- Node.js 基础（模块、npm/yarn/pnpm使用）
- 前端框架基础（Vue/React任选其一，也可使用原生JS）

### 1.3 环境搭建
#### 步骤1：安装Node.js
1. 访问 [Node.js官网](sslocal://flow/file_open?url=https%3A%2F%2Fnodejs.org%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=) 下载**LTS版本**（推荐20.x+），默认安装即可
2. 验证安装：
```bash
node -v  # 输出版本号即成功
npm -v   # 输出版本号即成功
```

#### 步骤2：配置国内镜像（加速依赖安装，国内用户必做）
```bash
npm config set registry https://registry.npmmirror.com
```

#### 步骤3：包管理器选型（三选一，推荐pnpm）
- npm：Node.js自带，无需额外安装
- yarn：`npm install -g yarn`
- pnpm（推荐）：`npm install -g pnpm`

## 二、快速上手：第一个Vite项目
### 2.1 项目初始化
使用官方脚手架创建项目，支持多种框架：
```bash
# npm 6.x
npm create vite@latest my-vite-app --template vue

# npm 7+（需额外双横线）
npm create vite@latest my-vite-app -- --template vue

# yarn
yarn create vite my-vite-app --template vue

# pnpm
pnpm create vite my-vite-app --template vue
```

#### 可选模板（--template 参数）
| 模板         | 说明                |
| ------------ | ------------------- |
| `vanilla`    | 原生JavaScript      |
| `vanilla-ts` | 原生TypeScript      |
| `vue`        | Vue 3 + JavaScript  |
| `vue-ts`     | Vue 3 + TypeScript  |
| `react`      | React + JavaScript  |
| `react-ts`   | React + TypeScript  |
| `svelte`     | Svelte + JavaScript |
| `svelte-ts`  | Svelte + TypeScript |
| `preact`     | Preact + JavaScript |
| `preact-ts`  | Preact + TypeScript |

### 2.2 项目结构解析
以Vue 3 + TypeScript模板为例，核心结构如下：
```
my-vite-app/
├── node_modules/       # 依赖包目录
├── public/             # 静态资源目录（不经过构建，直接复制到输出目录）
│   └── favicon.ico     # 网站图标
├── src/                # 源代码目录
│   ├── assets/         # 资源文件（经过构建处理，如图片、CSS）
│   │   └── logo.svg
│   ├── components/     # 组件目录
│   │   └── HelloWorld.vue
│   ├── App.vue         # 根组件
│   ├── main.ts         # 入口文件
│   └── style.css       # 全局样式
├── index.html          # HTML入口文件（Vite的核心，必须在根目录）
├── package.json        # 项目配置与依赖管理
├── tsconfig.json       # TypeScript配置
├── vite.config.ts      # Vite配置文件
└── README.md
```

### 2.3 核心文件说明
#### 1. index.html（HTML入口）
Vite将`index.html`视为项目入口，而非JS文件，必须放在根目录：
```html
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite App</title>
  </head>
  <body>
    <!-- 挂载点 -->
    <div id="app"></div>
    <!-- 引入入口JS文件，使用原生ESM -->
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

#### 2. vite.config.ts（Vite配置）
Vite的核心配置文件，支持TypeScript：
```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// defineConfig 提供类型提示
export default defineConfig({
  // 插件配置
  plugins: [vue()],
  // 开发服务器配置
  server: {
    port: 3000,
    open: true
  }
})
```

#### 3. package.json（脚本配置）
```json
{
  "name": "my-vite-app",
  "private": true,
  "version": "0.0.0",
  "type": "module", // 必须，启用ESM模块
  "scripts": {
    "dev": "vite",           // 启动开发服务器
    "build": "vue-tsc && vite build", // 构建生产版本
    "preview": "vite preview" // 预览生产构建
  },
  "dependencies": {
    "vue": "^3.4.0"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^5.0.0",
    "typescript": "^5.3.0",
    "vite": "^5.4.0",
    "vue-tsc": "^2.0.0"
  }
}
```

### 2.4 启动开发服务器
```bash
# 进入项目目录
cd my-vite-app

# 安装依赖
npm install

# 启动开发服务器
npm run dev
```
开发服务器默认在 `http://localhost:5173` 启动，支持热更新（HMR），修改代码后页面会自动刷新。

### 2.5 构建生产版本
```bash
# 构建生产版本，输出到 dist 目录
npm run build

# 预览生产构建
npm run preview
```

## 三、核心概念（重中之重）
### 3.1 极速开发的核心原理
Vite 解决了传统打包工具（Webpack）开发时的痛点，核心在于：
1. **开发服务器基于原生ESM**：无需打包整个应用，浏览器直接请求模块，实现秒启动
2. **依赖预构建**：使用esbuild预构建node_modules中的依赖，将CommonJS/UMD转换为ESM，提升加载速度
3. **按需编译**：只编译当前请求的模块，而非整个应用
4. **极速HMR**：HMR速度与模块数量无关，只更新修改的模块

### 3.2 依赖预构建
Vite 启动时会自动预构建依赖，核心作用：
- 将CommonJS/UMD模块转换为ESM
- 合并多个小模块为一个，减少HTTP请求
- 缓存预构建结果，下次启动直接使用

**手动触发预构建**：
```bash
# 删除缓存并重新预构建
rm -rf node_modules/.vite
npm run dev
```

### 3.3 热更新（HMR）
Vite 提供开箱即用的HMR，不同框架的HMR支持：
- Vue 3：通过`@vitejs/plugin-vue`自动支持
- React：通过`@vitejs/plugin-react`自动支持
- Svelte：通过`@sveltejs/vite-plugin-svelte`自动支持
- 原生JS：需手动实现HMR API

**HMR API示例（原生JS）**：
```javascript
if (import.meta.hot) {
  import.meta.hot.accept((newModule) => {
    // 模块更新时的处理逻辑
    console.log('模块已更新', newModule);
  });
}
```

## 四、常用配置详解
### 4.1 基础配置
```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path'

export default defineConfig({
  // 项目根目录，默认 process.cwd()
  root: './',
  
  // 开发或生产环境服务的公共基础路径，默认 '/'
  // 生产环境部署到子路径时需修改，如 '/my-app/'
  base: '/',
  
  // 插件配置
  plugins: [vue()],
  
  // 模式配置，默认 'development'（开发）和 'production'（生产）
  mode: 'development',
  
  // 路径别名配置
  resolve: {
    alias: {
      // @ 指向 src 目录
      '@': path.resolve(__dirname, './src'),
      // 自定义别名
      '@components': path.resolve(__dirname, './src/components')
    }
  }
})
```

### 4.2 开发服务器配置
```typescript
export default defineConfig({
  server: {
    // 服务器端口，默认 5173
    port: 3000,
    
    // 启动时自动打开浏览器
    open: true,
    
    // 监听所有地址，默认 localhost
    host: '0.0.0.0',
    
    // 端口被占用时自动退出，默认 false（自动尝试下一个可用端口）
    strictPort: false,
    
    // 代理配置（解决跨域问题）
    proxy: {
      // 将 /api 开头的请求代理到目标服务器
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true, // 改变请求头中的 Origin
        rewrite: (path) => path.replace(/^\/api/, '') // 重写路径
      }
    },
    
    // CORS配置
    cors: true,
    
    // 强制依赖预构建
    force: false
  }
})
```

### 4.3 构建配置
```typescript
export default defineConfig({
  build: {
    // 输出目录，默认 'dist'
    outDir: 'dist',
    
    // 静态资源输出目录，默认 'assets'
    assetsDir: 'assets',
    
    // 小于此大小的资源内联为 base64，默认 4096（4KB）
    assetsInlineLimit: 4096,
    
    // 构建目标浏览器，默认 'modules'（支持ESM的现代浏览器）
    target: 'modules',
    
    // 是否生成 source map，默认 false
    sourcemap: false,
    
    // 是否压缩代码，默认 'esbuild'（更快），可选 'terser'（更彻底）
    minify: 'esbuild',
    
    // 代码分割配置
    rollupOptions: {
      output: {
        // 手动分割代码
        manualChunks: {
          // 将 Vue 相关库单独打包
          'vue-vendor': ['vue', 'vue-router', 'pinia'],
          // 将 UI 库单独打包
          'ui-vendor': ['element-plus']
        }
      }
    },
    
    // 是否清空输出目录，默认 true
    emptyOutDir: true,
    
    // 构建后是否生成 manifest.json，默认 false
    manifest: false,
    
    // 构建后是否生成 ssr manifest，默认 false
    ssrManifest: false
  }
})
```

### 4.4 CSS 配置
```typescript
export default defineConfig({
  css: {
    // CSS 预处理器配置
    preprocessorOptions: {
      // SCSS 配置
      scss: {
        // 全局变量注入
        additionalData: `@import "@/styles/variables.scss";`
      },
      // Less 配置
      less: {
        javascriptEnabled: true
      }
    },
    
    // CSS Modules 配置
    modules: {
      // 类名生成规则
      localsConvention: 'camelCaseOnly',
      // 开发环境类名格式
      generateScopedName: '[name]__[local]___[hash:base64:5]'
    },
    
    // 是否开启 CSS 代码分割，默认 true
    codeSplit: true
  }
})
```

## 五、开发技巧与最佳实践
### 5.1 静态资源处理
#### 1. 引入资源
```typescript
// 引入图片
import logo from './assets/logo.png'

// 在模板中使用
<img src={logo} alt="logo" />
```

#### 2. public 目录
- `public` 目录下的资源不经过构建，直接复制到输出目录根路径
- 引用时使用绝对路径，如 `/favicon.ico`
- 适用场景：不需要处理的静态资源（如robots.txt、manifest.json）

#### 3. 特殊资源引入
```typescript
// 显式 URL 引入（添加 ?url 后缀）
import logoUrl from './assets/logo.png?url'

// 显式 raw 引入（添加 ?raw 后缀，获取文件内容字符串）
import shaderContent from './shader.glsl?raw'

// 显式 worker 引入（添加 ?worker 或 ?sharedworker 后缀）
import MyWorker from './worker.ts?worker'
const worker = new MyWorker()
```

### 5.2 环境变量
Vite 内置环境变量支持，通过 `.env` 文件配置：

#### 1. 环境变量文件
```
.env                # 所有环境加载
.env.local          # 所有环境加载，git 忽略
.env.[mode]         # 指定模式加载，如 .env.development、.env.production
.env.[mode].local   # 指定模式加载，git 忽略
```

#### 2. 定义环境变量
只有以 `VITE_` 开头的变量才会暴露给客户端代码：
```env
# .env.development
VITE_API_BASE_URL=http://localhost:8080/api
VITE_APP_TITLE=开发环境

# .env.production
VITE_API_BASE_URL=https://api.example.com
VITE_APP_TITLE=生产环境
```

#### 3. 使用环境变量
```typescript
// 在代码中使用
console.log(import.meta.env.VITE_API_BASE_URL)
console.log(import.meta.env.VITE_APP_TITLE)

// 内置环境变量
import.meta.env.MODE        // 当前模式（development/production）
import.meta.env.BASE_URL    // 配置的 base 路径
import.meta.env.PROD        // 是否生产环境
import.meta.env.DEV         // 是否开发环境
import.meta.env.SSR         // 是否服务端渲染
```

#### 4. TypeScript 类型提示
在 `src/vite-env.d.ts` 中添加类型定义：
```typescript
/// <reference types="vite/client" />

interface ImportMetaEnv {
  readonly VITE_API_BASE_URL: string
  readonly VITE_APP_TITLE: string
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
```

### 5.3 CSS 预处理器使用
Vite 开箱即用支持 CSS 预处理器，只需安装对应依赖：
```bash
# SCSS/Sass
npm install -D sass

# Less
npm install -D less

# Stylus
npm install -D stylus
```

使用示例：
```vue
<template>
  <div class="box">Hello</div>
</template>

<style lang="scss">
$primary-color: #42b883;

.box {
  color: $primary-color;
  &:hover {
    opacity: 0.8;
  }
}
</style>
```

### 5.4 TypeScript 支持
Vite 原生支持 TypeScript，无需额外配置：
- `.ts` 文件直接引入
- Vue 3 单文件组件中使用 `<script lang="ts">`
- React 中使用 `.tsx` 文件

**推荐配置**：
```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*.ts", "src/**/*.tsx", "src/**/*.vue"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

## 六、构建优化与性能提升
### 6.1 代码分割
通过 `rollupOptions.manualChunks` 手动分割代码，减少单个文件大小：
```typescript
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          // 框架核心
          'vue-core': ['vue', 'vue-router', 'pinia'],
          // UI 库
          'ui-lib': ['element-plus'],
          // 工具库
          'utils': ['lodash-es', 'dayjs']
        }
      }
    }
  }
})
```

### 6.2 Tree Shaking
Vite 内置 Tree Shaking，自动移除未使用的代码：
- 确保使用 ES Module（`import/export`），避免 CommonJS
- 配置 `sideEffects` 在 `package.json` 中：
```json
{
  "sideEffects": [
    "*.css",
    "*.scss"
  ]
}
```

### 6.3 压缩优化
```typescript
export default defineConfig({
  build: {
    // 使用 esbuild 压缩（更快，推荐）
    minify: 'esbuild',
    // esbuild 压缩选项
    esbuild: {
      // 生产环境移除 console 和 debugger
      drop: ['console', 'debugger']
    }
  }
})
```

### 6.4 预加载与预获取
在 `index.html` 中使用 `<link rel="modulepreload">` 预加载关键模块：
```html
<link rel="modulepreload" href="/src/main.ts">
```

### 6.5 分析包体积
使用 `rollup-plugin-visualizer` 分析包体积，优化依赖：
```bash
npm install -D rollup-plugin-visualizer
```

```typescript
import { defineConfig } from 'vite'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    visualizer({
      open: true, // 构建后自动打开分析报告
      filename: 'dist/stats.html'
    })
  ]
})
```

## 七、常用插件推荐
### 7.1 框架插件
| 插件                           | 功能           |
| ------------------------------ | -------------- |
| `@vitejs/plugin-vue`           | Vue 3 支持     |
| `@vitejs/plugin-vue-jsx`       | Vue 3 JSX 支持 |
| `@vitejs/plugin-react`         | React 支持     |
| `@sveltejs/vite-plugin-svelte` | Svelte 支持    |
| `@preact/preset-vite`          | Preact 支持    |

### 7.2 功能插件
| 插件                       | 功能               |
| -------------------------- | ------------------ |
| `vite-plugin-pages`        | 基于文件系统的路由 |
| `vite-plugin-layouts`      | 布局系统           |
| `vite-plugin-pwa`          | PWA 支持           |
| `vite-plugin-svg-icons`    | SVG 图标管理       |
| `vite-plugin-compression`  | gzip/brotli 压缩   |
| `vite-plugin-imagemin`     | 图片压缩           |
| `rollup-plugin-visualizer` | 包体积分析         |

### 7.3 插件使用示例
```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { createSvgIconsPlugin } from 'vite-plugin-svg-icons'
import path from 'path'

export default defineConfig({
  plugins: [
    vue(),
    // SVG 图标插件
    createSvgIconsPlugin({
      iconDirs: [path.resolve(process.cwd(), 'src/assets/icons')],
      symbolId: 'icon-[dir]-[name]'
    })
  ]
})
```

## 八、部署与常见问题
### 8.1 部署
1. 构建生产版本：
```bash
npm run build
```
2. 将 `dist` 目录部署到任意静态服务器（Nginx、Apache、Vercel、Netlify等）

**Nginx 配置示例**：
```nginx
server {
  listen 80;
  server_name example.com;
  root /path/to/dist;
  index index.html;

  location / {
    try_files $uri $uri/ /index.html;
  }

  # 配置 gzip 压缩
  gzip on;
  gzip_vary on;
  gzip_min_length 1024;
  gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss application/json application/javascript;
}
```

### 8.2 常见问题排查
#### 1. 开发服务器启动慢
- 检查是否有大量依赖未预构建，删除 `node_modules/.vite` 重新启动
- 检查是否有循环依赖
- 使用 `pnpm` 替代 npm/yarn，提升依赖安装速度

#### 2. 构建后页面空白
- 检查 `base` 配置是否正确，部署到子路径时需修改
- 检查路由模式是否为 `history`，需配置服务器重定向到 `index.html`
- 打开浏览器控制台，查看是否有报错

#### 3. 热更新不生效
- 检查文件是否在 `src` 目录下
- 检查是否使用了正确的框架插件
- 检查 `vite.config.ts` 中的 `server.hmr` 配置是否正确
- 尝试重启开发服务器

#### 4. 依赖预构建失败
- 检查 `node_modules` 是否完整，删除后重新安装
- 检查是否有不兼容的依赖
- 配置 `optimizeDeps` 手动指定需要预构建的依赖：
```typescript
export default defineConfig({
  optimizeDeps: {
    include: ['vue', 'vue-router'],
    exclude: ['some-large-package']
  }
})
```

## 九、进阶内容与生态
### 9.1 服务端渲染（SSR）
Vite 支持 SSR，官方提供了 Vue 和 React 的 SSR 模板：
```bash
# Vue SSR
npm create vite@latest my-vue-ssr-app -- --template vue-ssr

# React SSR
npm create vite@latest my-react-ssr-app -- --template react-ssr
```

### 9.2 静态站点生成（SSG）
使用 `vite-ssg` 实现静态站点生成：
```bash
npm install -D vite-ssg
```

### 9.3 官方学习资源
1. [Vite 官方文档](sslocal://flow/file_open?url=https%3A%2F%2Fcn.vitejs.dev%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：最权威、最新的开发文档
2. [Vite GitHub](sslocal://flow/file_open?url=https%3A%2F%2Fgithub.com%2Fvitejs%2Fvite&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：源码与 issue
3. [Awesome Vite](sslocal://flow/file_open?url=https%3A%2F%2Fgithub.com%2Fvitejs%2Fawesome-vite&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：精选插件与资源
