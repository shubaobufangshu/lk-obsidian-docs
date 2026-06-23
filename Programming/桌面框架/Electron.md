## 环境搭建（全平台通用）
 
#### 步骤1：安装Node.js

1. 访问 [Node.js官网](sslocal://flow/file_open?url=https%3A%2F%2Fnodejs.org%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=) 下载**LTS版本**（推荐20.x+），默认安装即可
    
2. 安装完成后，打开终端验证安装：
    
    ```bash
    node -v  # 输出版本号即成功
    npm -v   # 输出版本号即成功
    ```
    

#### 步骤2：配置国内镜像（加速依赖安装，国内用户必做）

```bash
# npm 镜像配置
npm config set registry https://registry.npmmirror.com
# Electron 镜像配置（解决下载慢/失败问题）
npm config set electron_mirror https://npmmirror.com/mirrors/electron/
```

#### 步骤3：包管理器选型（三选一，推荐pnpm）

- npm：Node.js自带，无需额外安装
    
- yarn：`npm install -g yarn`
    
- pnpm（推荐）：`npm install -g pnpm`
    
    > 注意：使用pnpm/yarn berry时，需在项目根目录配置 `.npmrc`：`node-linker=hoisted`，否则打包工具无法正常识别依赖
    

#### 步骤4：代码编辑器与插件

- 首选编辑器：VS Code
- 推荐插件：ESLint、Electron JS Snippets、Debugger for Chrome
## 一、Electron 核心概念
Electron 是由GitHub开发的跨平台桌面应用开发框架，基于**Chromium**（渲染UI）和**Node.js**（原生能力），使用HTML/CSS/JavaScript技术栈，一套代码可打包为Windows、macOS、Linux三大平台桌面应用。

Electron 基于 Chromium 和 Node.js，允许你使用前端技术构建跨平台桌面应用。核心概念如下：

| 概念                            | 作用                                  | 你的技术栈对应                              |
| ------------------------------- | ------------------------------------- | ------------------------------------------- |
| **主进程 (Main Process)**       | 应用入口，管理窗口、系统托盘、原生API | Node.js 环境                                |
| **渲染进程 (Renderer Process)** | 前端界面运行环境（Chromium）          | Vue 3 + Vite 环境                           |
| **预加载脚本 (Preload Script)** | 桥接主进程与渲染进程，安全暴露API     | 独立的上下文隔离环境                        |
| **进程间通信 (IPC)**            | 主进程与渲染进程的数据交换            | `ipcMain` / `ipcRenderer` / `contextBridge` |

---

## 二、项目初始化：Vite + Electron + Vue 3
我们将使用 `vite-plugin-electron` 插件快速搭建项目，这是目前最主流的 Electron + Vite 集成方案。

### 2.1 目录结构设计
```
electron-vue-app/
├── electron/                # Electron 核心代码
│   ├── main.ts             # 主进程入口
│   └── preload.ts          # 预加载脚本
├── src/                     # Vue 3 前端代码
│   ├── App.vue
│   └── main.ts
├── index.html               # HTML 入口
├── vite.config.ts           # Vite 配置
└── package.json
```

### 2.2 依赖安装
```bash
# 初始化项目
npm init -y

# 安装核心依赖
npm install -D vite vue@3 @vitejs/plugin-vue electron electron-builder vite-plugin-electron vite-plugin-electron-renderer
npm install element-plus @element-plus/icons-vue
```

### 2.3 Vite 配置文件 (`vite.config.ts`)
```typescript
// 导入 Node.js 官方内置的 path 核心模块
// path 模块专门用于处理文件路径，解决不同操作系统（Windows/Mac/Linux）路径格式不兼容的问题
import { resolve } from 'path'

// 从 electron-vite 库中导入 defineConfig 配置函数
// 作用：为配置文件提供 TypeScript 类型提示和校验，让配置项书写更规范、避免拼写错误，实现「类型安全」的配置
import { defineConfig } from 'electron-vite'

// 导入 Vite 官方适配 Vue3 的插件
// 核心功能：编译 .vue 单文件组件（解析模板<template>、脚本<script>、样式<style>），是 Vue3 项目的必备插件
import vue from '@vitejs/plugin-vue'

// 导入 unplugin 生态的 AutoImport 自动导入插件
// 作用：无需手动在代码中 import API（如 Vue 的 ref/reactive、Element Plus 的 ElMessage 等），插件会自动按需导入
import AutoImport from 'unplugin-auto-import/vite'

// 导入 unplugin 生态的 Components 组件自动注册插件
// 作用：无需手动 import 组件、无需 app.component 注册组件，插件会自动识别并注册 Vue 组件
import Components from 'unplugin-vue-components/vite'

// 导入 Element Plus 官方解析器
// 作用：配合上面两个自动导入插件，精准识别 Element Plus 的组件和 API，自动导入对应的代码和样式文件
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

// 导出 electron-vite 配置（Electron+Vite 项目的固定写法）
// defineConfig 包裹后，编辑器会提供完整的配置提示，是官方推荐的标准导出方式
export default defineConfig({
  // ==================== 主进程配置 (Main Process) ====================
  // Electron 架构核心：主进程是应用的「入口进程」，唯一的
  // 职责：管理应用生命周期、创建/销毁浏览器窗口、调用系统底层 API（文件、网络、托盘等）
  // 空对象 {}：表示使用 electron-vite 提供的**默认配置**（默认入口文件、输出目录、构建规则等）
  main: {},

  // ==================== 预加载脚本配置 (Preload Script) ====================
  // Electron 安全核心：预加载脚本是「渲染进程 ↔ 主进程」的通信桥梁
  // 作用：遵循 Electron 安全规范，不直接在渲染进程暴露 Node.js API，仅通过预加载暴露有限的通信方法
  // 空对象 {}：表示使用 electron-vite 提供的**默认配置**
  preload: {},

  // ==================== 渲染进程配置 (Renderer Process) ====================
  // 渲染进程：Electron 中负责「UI 渲染」的进程（等价于浏览器页面环境）
  // 本项目中，渲染进程就是 Vue3 + Element Plus 前端页面运行的环境
  renderer: {
    // Vite 核心配置：路径解析规则
    // 作用：自定义模块导入的路径解析方式，简化代码中路径的书写
    resolve: {
      // 路径别名配置：给长路径设置一个简短的别名，避免书写繁琐的相对路径
      alias: {
        // 配置别名 @renderer 映射到 项目根目录/src/renderer/src 绝对路径
        // resolve() 方法：将相对路径转换为**操作系统绝对路径**，避免路径报错
        // 用法：代码中直接写 import xxx from '@renderer/xxx'，无需写 ../../src/renderer/src/xxx
        '@renderer': resolve('src/renderer/src')
      }
    },

    // Vite 插件配置：所有扩展 Vite 功能的插件都在这里注册
    // 插件执行顺序：按数组顺序依次执行
    plugins: [
      // 注册 Vue3 插件，启用 .vue 文件编译能力
      vue(),
      // 注册 自动导入API 插件
      AutoImport({
        // 配置解析器：使用 Element Plus 官方解析器
        // 效果：自动导入 Vue 组合式API + Element Plus 所有 API（无需手动 import）
        resolvers: [ElementPlusResolver()]
      }),
      // 注册 自动注册组件 插件
      Components({
        // 配置解析器：使用 Element Plus 官方解析器
        // 效果：自动注册 Element Plus 所有组件（无需手动 import 组件、无需 app.use 注册）
        resolvers: [ElementPlusResolver()]
      })
    ],

    // Vite 开发服务器配置（仅开发环境生效，npm run dev 时生效）
    server: {
      // host: true → 监听所有网络地址（localhost + 本机局域网 IP）
      // 作用：允许同一局域网的手机/其他电脑访问开发服务，方便联调
      host: true,
      // 指定开发服务器的端口号为 5173（Vite 默认端口）
      port: 5173,
      // 配置开发服务器的**响应头**，解决浏览器/Electron 安全策略限制
      headers: {
        // 清空 内容安全策略(CSP) 头
        // 作用：Electron 渲染进程默认会严格限制资源加载，清空后避免 Element Plus/内联样式/脚本报错
        'Content-Security-Policy': '',
        // 兼容旧版浏览器的 CSP 响应头，同样清空避免限制
        'X-Content-Security-Policy': '',
        // 兼容 WebKit 内核（Electron 内置 Chrome 内核）的 CSP 响应头，清空限制
        'X-WebKit-CSP': ''
      }
    },

    // Vite 生产构建配置（仅打包环境生效，npm run build 时生效）
    build: {
      // 指定代码压缩工具：terser
      // Vite 默认压缩工具是 esbuild（更快），terser 压缩率更高，支持自定义删除 console/debugger
      minify: 'terser',
      // terser 压缩工具的详细配置项
      terserOptions: {
        // 代码压缩规则配置
        compress: {
          // 保留 console.log 语句，不删除（开发调试用，正式上线可改为 true 删除日志）
          drop_console: false,
          // 保留 debugger 语句，不删除（开发调试用，正式上线可改为 true 删除）
          drop_debugger: false
        }
      }
    }
  }
})

```

---

## 三、主进程开发：窗口管理与原生能力
主进程是 Electron 应用的“大脑”，负责创建窗口、管理生命周期和调用系统原生能力。

### 3.1 主进程入口 (`electron/main.ts`)
```typescript
// 1. 导入 Electron 核心模块
// app：管理应用生命周期
// shell：打开系统默认浏览器/文件
// BrowserWindow：创建桌面窗口
// ipcMain：主进程 <-> 渲染进程 通信
import { app, shell, BrowserWindow, ipcMain } from 'electron'

// 2. 导入 Node.js 路径处理工具
// 用于拼接文件路径，保证跨系统（Windows/macOS/Linux）路径正确
import { join } from 'path'

// 3. 导入 electron-vite 工具库
// electronApp：设置应用ID（Windows 任务栏图标用）
// optimizer：优化窗口快捷键（F12 开控制台、禁止生产环境刷新）
// is：判断当前环境（开发/生产）
import { electronApp, optimizer, is } from '@electron-toolkit/utils'

// 4. 导入应用图标（资源文件）
// ?asset 是 vite 语法，表示这是静态资源文件
import icon from '../../resources/icon.png?asset'
// process：Node.js 提供的全局对象，代表当前程序运行的进程
// env：environment 的缩写，即【环境变量】，用于存储系统/框架的配置开关
// ELECTRON_DISABLE_SECURITY_WARNINGS：
//    Electron 官方内置的固定环境变量名称
//    作用：控制是否关闭 Electron 开发环境的安全警告
//  'true'：将这个环境变量设置为开启状态 → 关闭所有安全警告提示
//关闭开发环境报警
// process.env.ELECTRON_DISABLE_SECURITY_WARNINGS = 'true'
// ==============================================
// 核心函数：创建应用主窗口
// ==============================================
let mainWindow = null
function createWindow() {
  // 创建一个浏览器窗口实例
  const mainWindow = new BrowserWindow({
    width: 1200, // 窗口默认宽度
    height: 900, // 窗口默认高度
    show: false, // 先不显示窗口（等加载完成再显示，避免白屏）
    autoHideMenuBar: true, // 自动隐藏顶部菜单栏（按 Alt 显示）
    // Linux 系统需要单独设置窗口图标
    ...(process.platform === 'linux' ? { icon } : {}),
    // 核心配置1：隐藏原生标题栏，保留系统窗口控制按钮
    titleBarStyle: 'hidden',
    // 核心配置2：自定义标题栏颜色与样式
    titleBarOverlay: {
      color: '#ffffff00', // 标题栏背景色，支持rgba/hex/hsl，可设置透明
      // symbolColor: '#ffffff', // 窗口控制按钮（最小化/关闭）的图标颜色（仅Windows/Linux生效）
      height: 20 // 标题栏高度，单位px，默认32px
    },
    // 网页（渲染进程）配置项
    webPreferences: {
      // 预加载脚本：在渲染进程运行前执行，用于安全暴露 API
      preload: join(__dirname, '../preload/index.js'),
      // 关闭沙箱模式（electron-vite 模板默认关闭，方便开发）
      sandbox: false,
      // 禁用自动填充功能，消除报错
      autofill: false,
      devTools: true // 确保开发者工具不被 CSP 限制
      // csp策略
      // additionalArguments: ['--csp=img-src self data: https://images.unsplash.com']
    }
  })

  // 监听：窗口准备就绪 → 显示窗口
  // 解决窗口刚创建时白屏闪烁问题
  mainWindow.on('ready-to-show', () => {
    mainWindow.show()
  })
  // 监听渲染进程的「键盘输入前」事件
  mainWindow.webContents.on('before-input-event', (event, input) => {
    // 判断：如果按下的按键 是 F12
    if (input.key === 'F12') {
      // 阻止默认行为（可选，但这里不需要）
      // event.preventDefault()

      // 判断：开发者工具 是否已经打开
      if (mainWindow.webContents.isDevToolsOpened()) {
        // 如果已经打开 → 关闭开发者工具
        mainWindow.webContents.closeDevTools()
      } else {
        // 如果没有打开 → 打开开发者工具
        // mode: 'detach' 表示：独立窗口打开（不嵌入应用窗口）
        mainWindow.webContents.openDevTools({ mode: 'detach' })
      }
    }
  })
  // 拦截渲染进程的「打开新窗口」行为
  // 改为：用系统默认浏览器打开外部链接
  mainWindow.webContents.setWindowOpenHandler((details) => {
    shell.openExternal(details.url) // 调用系统浏览器打开
    return { action: 'deny' } // 拒绝在应用内新建窗口
  })

  // ==============================================
  // 根据环境加载页面（开发热更新 / 生产加载本地文件）
  // ==============================================
  // 如果是【开发环境】：加载 Vite 启动的热更新服务地址
  if (is.dev && process.env['ELECTRON_RENDERER_URL']) {
    mainWindow.loadURL(process.env['ELECTRON_RENDERER_URL'])
  } else {
    // 如果是【生产环境】：加载打包好的本地 HTML 文件
    mainWindow.loadFile(join(__dirname, '../renderer/index.html'))
  }
}

// ==============================================
// 应用生命周期：Electron 初始化完成后执行
// ==============================================
app.whenReady().then(() => {
  // 设置 Windows 系统应用 ID（必须设置，否则任务栏图标异常）
  electronApp.setAppUserModelId('com.electron')

  // 监听：所有新窗口创建时
  // 自动优化快捷键：
  // 开发环境 F12 打开控制台
  // 生产环境 禁止 Ctrl+R 刷新页面
  app.on('browser-window-created', (_, window) => {
    optimizer.watchWindowShortcuts(window)
  })

  // ==============================================
  // IPC 通信测试（主进程监听渲染进程消息）
  // 渲染进程发 'ping' → 主进程打印 'pong'
  // ==============================================
  ipcMain.on('ping', () => console.log('pong'))

  // 执行：创建主窗口
  createWindow()

  // macOS 专属：点击 dock 图标，如果没有窗口则重新创建
  app.on('activate', function () {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

// ==============================================
// 所有窗口关闭时：退出应用（macOS 除外）
// ==============================================
app.on('window-all-closed', () => {
  // 非 macOS 系统（Windows / Linux）关闭所有窗口就退出应用
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

```

---

## 四、预加载脚本：安全的进程间通信桥梁
预加载脚本运行在**渲染进程的上下文隔离环境**中，是唯一可以同时访问 `window` 对象和 Node.js API 的地方。我们通过 `contextBridge` 安全暴露 API 给渲染进程。

### 4.1 预加载脚本 (`electron/preload.ts`)
```typescript
import { contextBridge, ipcRenderer } from 'electron'
import fs from 'fs/promises'

// 🔒 安全暴露 API 给渲染进程（window.electronAPI）
contextBridge.exposeInMainWorld('electronAPI', {
  // 1. 双向 IPC：渲染进程 → 主进程 → 渲染进程
  send: (channel: string, data: any) => {
    // 白名单验证：防止恶意调用
    const validChannels = ['toMain', 'save-file']
    if (validChannels.includes(channel)) {
      return ipcRenderer.invoke(channel, data)
    }
  },

  // 2. 单向监听：主进程 → 渲染进程
  on: (channel: string, callback: (...args: any[]) => void) => {
    const validChannels = ['fromMain']
    if (validChannels.includes(channel)) {
      ipcRenderer.on(channel, (event, ...args) => callback(...args))
    }
  },

  // 3. 封装文件操作 API
  openFile: async () => {
    const result = await ipcRenderer.invoke('open-file-dialog')
    if (!result.canceled && result.filePaths.length > 0) {
      const content = await fs.readFile(result.filePaths[0], 'utf-8')
      return { path: result.filePaths[0], content }
    }
    return null
  },

  saveFile: async (filePath: string, content: string) => {
    await fs.writeFile(filePath, content, 'utf-8')
    return true
  }
})

// 📝 TypeScript 类型声明（让 Vue 组件获得类型提示）
declare global {
  interface Window {
    electronAPI: {
      send: (channel: string, data: any) => Promise<any>
      on: (channel: string, callback: (...args: any[]) => void) => void
      openFile: () => Promise<{ path: string; content: string } | null>
      saveFile: (filePath: string, content: string) => Promise<boolean>
    }
  }
}
```

---

## 五、渲染进程开发：Vue 3 组件集成
在 Vue 3 组件中，我们可以直接通过 `window.electronAPI` 调用预加载脚本暴露的方法，实现与主进程的通信和原生能力调用。

### 5.1 Vue 3 入口文件 (`src/main.ts`)
```typescript
// 导入全局样式文件（项目通用的 CSS 样式）
import './assets/main.css'

// 从 Vue 核心库中导入「创建应用实例」的方法
import { createApp } from 'vue'
//  导入 Pinia
import { createPinia } from 'pinia'
// 导入持久化插件
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'
// 导入项目根组件（所有页面/组件都从这里开始嵌套）
import App from './App.vue'
//加载路由
import router from './router'
//引入ElementUI组件
import ElementPlus from 'element-plus'
//引入Element组件css
import 'element-plus/dist/index.css'
// Element Plus 暗黑模式 CSS 变量
import 'element-plus/theme-chalk/dark/css-vars.css'
// ==============================================
// 创建 Vue 应用实例，并挂载到页面的 #app 节点上
// 这是整个前端项目的启动入口
// 创建应用实例
const app = createApp(App)
// 创建 Pinia 实例
const pinia = createPinia()
// 给 Pinia 挂载持久化插件
pinia.use(piniaPluginPersistedstate)
// 挂载ElementUI
app.use(ElementPlus)
// 将 Pinia 挂载到 Vue 应用上
app.use(pinia)
// 注入路由
app.use(router)
// 挂载到 DOM 节点（项目启动）
app.mount('#app')

```

### 5.2 主组件 (`src/App.vue`)：文件操作 + IPC 通信
```vue
<template>
  <el-container class="app-container">
    <el-header class="header">
      <h1>助手 - Electron 实战</h1>
    </el-header>
    <el-main>
      <!-- 1. IPC 通信测试 -->
      <el-card class="card">
        <template #header><span>IPC 通信测试</span></template>
        <el-input v-model="message" placeholder="输入消息" style="width: 300px; margin-right: 10px;" />
        <el-button type="primary" @click="sendMessage">发送到主进程</el-button>
        <div style="margin-top: 10px;"><strong>主进程回复：</strong>{{ reply }}</div>
      </el-card>

      <!-- 2. 文件操作测试 -->
      <el-card class="card" style="margin-top: 20px;">
        <template #header><span>文件操作</span></template>
        <el-button type="success" @click="openFile">打开文件</el-button>
        <el-button type="warning" @click="saveFile" style="margin-left: 10px;" :disabled="!currentFile">保存文件</el-button>
        <el-input
          v-model="fileContent"
          type="textarea"
          :rows="10"
          placeholder="文件内容"
          style="margin-top: 10px;"
        />
        <div v-if="currentFile" style="margin-top: 10px;"><strong>当前文件：</strong>{{ currentFile }}</div>
      </el-card>
    </el-main>
  </el-container>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElMessage } from 'element-plus'

// ------------------------------
// 1. IPC 通信逻辑
// ------------------------------
const message = ref('')
const reply = ref('')

const sendMessage = async () => {
  if (!message.value) return ElMessage.warning('请输入消息')
  try {
    // 调用预加载脚本暴露的 API
    const res = await window.electronAPI.send('toMain', message.value)
    reply.value = res
    ElMessage.success('发送成功')
  } catch (err) {
    ElMessage.error('发送失败')
    console.error(err)
  }
}

// 监听主进程主动发送的消息
window.electronAPI.on('fromMain', (data) => {
  ElMessage.info(`收到主进程消息：${data}`)
})

// ------------------------------
// 2. 文件操作逻辑
// ------------------------------
const currentFile = ref<string | null>(null)
const fileContent = ref('')

const openFile = async () => {
  try {
    const res = await window.electronAPI.openFile()
    if (res) {
      currentFile.value = res.path
      fileContent.value = res.content
      ElMessage.success('文件打开成功')
    }
  } catch (err) {
    ElMessage.error('文件打开失败')
    console.error(err)
  }
}

const saveFile = async () => {
  if (!currentFile.value) return ElMessage.warning('请先打开文件')
  try {
    await window.electronAPI.saveFile(currentFile.value, fileContent.value)
    ElMessage.success('文件保存成功')
  } catch (err) {
    ElMessage.error('文件保存失败')
    console.error(err)
  }
}
</script>

<style scoped>
.app-container { height: 100vh; }
.header { background-color: #409eff; color: white; display: flex; align-items: center; }
.header h1 { margin: 0; font-size: 24px; }
.card { max-width: 800px; margin: 0 auto; }
</style>
```

---

## 六、高级功能：大模型流式输出实现
基于你之前关注的**流式输出（Streaming Response）**需求，我们在 Electron 中集成大模型 API（以 Ollama 本地部署 DeepSeek-R1 为例）。

### 6.1 在 `App.vue` 中添加流式输出功能
```vue
<template>
  <!-- ... 前面的内容 ... -->
  <!-- 3. 大模型流式输出 -->
  <el-card class="card" style="margin-top: 20px;">
    <template #header><span>大模型智能问答</span></template>
    <el-input v-model="prompt" placeholder="输入你的问题" style="width: 300px; margin-right: 10px;" />
    <el-button type="primary" @click="callLLM" :loading="loading">发送</el-button>
    <div style="margin-top: 10px; border: 1px solid #dcdfe6; padding: 10px; min-height: 150px;">
      <strong>回复：</strong>
      <span>{{ llmReply }}</span>
    </div>
  </el-card>
</template>

<script setup lang="ts">
// ... 前面的导入 ...

// ------------------------------
// 3. 大模型流式输出逻辑
// ------------------------------
const prompt = ref('')
const llmReply = ref('')
const loading = ref(false)

const callLLM = async () => {
  if (!prompt.value) return ElMessage.warning('请输入问题')
  loading.value = true
  llmReply.value = '' // 清空上次回复

  try {
    // 调用 Ollama 本地 API（替换为你的 API 地址）
    const response = await fetch('http://localhost:11434/api/generate', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'deepseek-r1',
        prompt: prompt.value,
        stream: true // 启用流式输出
      })
    })

    if (!response.ok) throw new Error('API 请求失败')

    // 处理流式响应
    const reader = response.body?.getReader()
    const decoder = new TextDecoder()

    if (reader) {
      while (true) {
        const { done, value } = await reader.read()
        if (done) break // 流结束

        // 解析流式数据（根据你的 API 格式调整）
        const chunk = decoder.decode(value, { stream: true })
        const lines = chunk.split('\n').filter(line => line.trim())

        for (const line of lines) {
          try {
            const data = JSON.parse(line)
            if (data.response) {
              llmReply.value += data.response // 逐字追加回复
            }
          } catch (e) { /* 忽略解析错误 */ }
        }
      }
    }
    ElMessage.success('请求完成')
  } catch (err) {
    ElMessage.error('请求失败')
    console.error(err)
  } finally {
    loading.value = false
  }
}
</script>
```

---

## 七、打包与发布：使用 electron-builder
最后，我们将应用打包为各平台的安装包（Windows `.exe`、macOS `.dmg`、Linux `.deb`）。

### 7.1 配置 `package.json`
```json
{
  "name": "smart-mine-assistant",
  "version": "1.0.0",
  "main": "dist-electron/main/main.js", // 主进程入口（编译后）
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "electron:build": "vite build && electron-builder" // 打包命令
  },
  "build": {
    "appId": "com.smartmine.assistant",
    "productName": "助手",
    "directories": { "output": "release" }, // 打包输出目录
    "files": [
      "dist/**/*",
      "dist-electron/**/*",
      "index.html"
    ],
    "win": {
      "target": "nsis", // Windows 安装包格式
      "icon": "assets/icon.ico" // 图标路径
    },
    "mac": {
      "target": "dmg",
      "icon": "assets/icon.icns"
    },
    "linux": {
      "target": "deb",
      "icon": "assets/icon.png"
    }
  }
}
```
在package.json中添加`build`字段，或创建单独的`electron-builder.json`配置文件，以下是开箱即用的完整配置：

```json
{
  "scripts": {
    "start": "electron .",
    "build:win": "electron-builder --win",  // 打包Windows
    "build:mac": "electron-builder --mac",  // 打包macOS
    "build:linux": "electron-builder --linux", // 打包Linux
    "build:all": "electron-builder -mwl"    // 全平台打包（仅macOS支持）
  },
  "build": {
    "appId": "com.yourcompany.yourapp",  // 应用唯一标识（必填）
    "productName": "你的应用名称",        // 应用名称（安装包、桌面快捷方式名称）
    "copyright": "Copyright © 2024 yourname", // 版权信息
    "directories": {
      "output": "dist/${version}" // 打包输出目录
    },
    "asar": true, // 启用asar压缩，保护源码（默认开启）
    "files": [ // 打包包含的文件，按需配置
      "main.js",
      "preload.js",
      "index.html",
      "assets/**/*",
      "node_modules/**/*"
    ],
    // 排除无用文件，减小包体积
    "files": [
      "**/*",
      "!**/*.map",
      "!**/node_modules/*/{CHANGELOG.md,README.md,README,readme.md,readme}",
      "!**/node_modules/*/{test,__tests__,tests,powered-test,example,examples}"
    ],
    // Windows平台配置
    "win": {
      "target": [
        {
          "target": "nsis", // 安装包格式
          "arch": ["x64"]   // 架构，x64/ia32/arm64
        }
      ],
      "icon": "assets/icon.ico", // 应用图标
      "signingHashAlgorithms": ["sha256"],
      "verifyUpdateCodeSignature": true
    },
    // Windows NSIS安装包配置
    "nsis": {
      "oneClick": false, // 取消一键安装，显示安装向导
      "allowToChangeInstallationDirectory": true, // 允许修改安装目录
      "createDesktopShortcut": true, // 创建桌面快捷方式
      "createStartMenuShortcut": true, // 创建开始菜单快捷方式
      "shortcutName": "你的应用名称",
      "installerIcon": "assets/icon.ico",
      "uninstallerIcon": "assets/icon.ico",
      "installerHeaderIcon": "assets/icon.ico"
    },
    // macOS平台配置
    "mac": {
      "target": [
        {
          "target": "dmg", // 磁盘映像格式
          "arch": ["x64", "arm64"] // 支持Intel和Apple Silicon
        }
      ],
      "icon": "assets/icon.icns",
      "category": "public.app-category.utilities", // 应用分类
      "hardenedRuntime": true, // 启用硬化运行时（公证必备）
      "gatekeeperAssess": false,
      "entitlements": "build/entitlements.mac.plist", // 权限文件
      "entitlementsInherit": "build/entitlements.mac.plist"
    },
    // macOS DMG配置
    "dmg": {
      "title": "你的应用名称",
      "icon": "assets/icon.icns",
      "contents": [
        { "x": 130, "y": 220 },
        { "x": 410, "y": 220, "type": "link", "path": "/Applications" }
      ]
    },
    // Linux平台配置
    "linux": {
      "target": "AppImage", // 通用Linux格式，无需安装即可运行
      "icon": "assets/icon.png",
      "category": "Utility"
    },
    // 自动更新配置（配合electron-updater使用）
    "publish": {
      "provider": "generic",
      "url": "https://your-update-server.com/updates/"
    }
  }
}
```

### 7.2 执行打包
```bash
# 1. 先构建前端和 Electron 代码
npm run build

# 2. 打包为当前平台的安装包
npm run electron:build
```

打包完成后，安装包将生成在 `release/` 目录下。
#### 跨平台打包注意事项

1. **Windows安装包**：只能在Windows系统或Wine环境下打包
2. **macOS安装包**：只能在macOS系统下打包，代码签名和公证必须在macOS系统完成
3. **Linux安装包**：可在Linux、macOS、Windows WSL环境下打包
4. **推荐方案**：使用GitHub Actions、GitLab CI等CI/CD工具，实现自动化全平台打包
#### 应用签名与上架

##### 1. Windows平台

- 必须购买代码签名证书（EV证书最佳），对安装包进行签名，避免Windows SmartScreen拦截
- 可上架Microsoft Store，需遵循微软应用商店规范

##### 2. macOS平台

- 必须申请Apple开发者账号，生成开发者证书，对应用进行签名
- macOS 10.15+ 必须对应用进行公证（Notarization），否则Gatekeeper会阻止应用运行
- 可上架Mac App Store，需遵循苹果审核规范

---
## 八、运行与调试
```bash
# 开发模式运行
npm run dev
```

调试与问题排查

### 8.1 渲染进程调试

和浏览器调试完全一致，使用Chromium DevTools：

1. 打开方式：窗口中按 `F12` 或 `Ctrl+Shift+I`（macOS为 `Cmd+Opt+I`）
2. 可调试内容：HTML/CSS、JavaScript断点、网络请求、本地存储、Console日志、性能分析

### 8.2 主进程断点调试（VS Code）

1. 在项目根目录创建 `.vscode/launch.json` 文件，配置如下：
    
    ```json
    {
    "version": "0.2.0",
    "configurations": [
    {
      "name": "调试Electron主进程",
      "type": "node",
      "request": "launch",
      "cwd": "${workspaceFolder}",
      "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron",
      "windows": {
        "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron.cmd"
      },
      "args": ["."],
      "outputCapture": "std",
      "console": "integratedTerminal"
    }
    ]
    }
    ```
    
2. 在main.js中打断点，按F5启动调试，即可实现主进程断点调试、变量查看、调用栈分析。
    

### 8.3 日志调试

1. 主进程日志：直接使用`console.log`，日志输出在启动终端中
    
2. 渲染进程日志：`console.log`输出在Chromium DevTools的Console面板中
    
3. 持久化日志：使用`electron-log`库，将日志写入本地文件，方便排查线上问题
    
    ```bash
    npm install electron-log
    ```
    
    ```javascript
    const log = require('electron-log');
    // 日志级别：error/warn/info/verbose/debug/silly
    log.info('应用启动');
    log.error('发生错误', error);
    // 日志文件默认路径
    // Windows: C:\Users\用户名\AppData\Roaming\应用名\logs
    // macOS: ~/Library/Logs/应用名
    // Linux: ~/.config/应用名/logs
    ```
    

### 8.4 高频问题排查

#### 1. 应用启动白屏

- 常见原因：页面路径错误、HTML文件不存在、JS语法报错、主进程崩溃、窗口未正确show
- 排查步骤：
    1. 打开DevTools，查看Console是否有报错
    2. 检查`loadFile/loadURL`的路径是否正确，使用绝对路径
    3. 监听窗口`did-fail-load`事件，查看页面加载失败原因
    4. 查看终端主进程日志，是否有崩溃报错

#### 2. IPC通信无响应

- 常见原因：事件名不匹配、preload未正确暴露API、主进程未监听事件、上下文隔离未开启导致API失效
- 排查步骤：
    1. 检查事件名大小写、拼写是否完全一致
    2. 检查preload是否正确使用`contextBridge`暴露API
    3. 检查主进程是否在窗口创建前就监听了IPC事件
    4. 打开DevTools和终端，查看是否有报错

#### 3. 打包后应用无法运行

- 常见原因：路径错误、依赖未打包、原生模块未重新编译、权限不足、平台兼容问题
- 排查步骤：
    1. 所有文件路径必须使用绝对路径，避免相对路径
    2. 检查package.json的dependencies和devDependencies，生产依赖必须放在dependencies
    3. 原生模块需使用`electron-builder install-app-deps`重新编译
    4. 关闭asar压缩，查看打包后的文件是否完整

#### 4. 应用包体积过大

- 优化方案：
    1. 精简打包配置，排除node_modules中的无用文件
    2. 前端代码进行treeshaking、压缩、分包
    3. 生产依赖仅保留必需的包，开发依赖全部放在devDependencies
    4. 使用7zip对安装包进行二次压缩

## 九、进阶功能与生态资源

### 9.1 自动更新

商用应用必备功能，使用`electron-updater`（electron-builder配套）实现，核心步骤：

1. 安装依赖
    
    ```bash
    npm install electron-updater
    ```
    
2. 主进程编写更新逻辑
    
```javascript
const { autoUpdater } = require('electron-updater');
const { dialog, ipcMain } = require('electron');
// 自动更新配置 autoUpdater.autoDownload = false; // 手动下载 autoUpdater.autoInstallOnAppQuit = true; // 退出时自动安装
// 应用启动后检查更新 app.whenReady().then(() => { autoUpdater.checkForUpdates(); });
// 监听更新事件 // 有可用更新 autoUpdater.on('update-available', (info) => { dialog.showMessageBox({ title: '更新提示', message: `发现新版本${info.version}，是否立即更新？`, buttons: ['立即更新', '稍后再说'] }).then((result) => { if (result.response === 0) { autoUpdater.downloadUpdate(); } }); });
// 无可用更新 autoUpdater.on('update-not-available', () => { console.log('当前已是最新版本'); });
// 更新下载进度 autoUpdater.on('download-progress', (progressObj) => { console.log(`更新进度：${progressObj.percent}%`); // 向渲染进程推送进度 mainWindow.webContents.send('update:progress', progressObj.percent); });
// 更新下载完成 autoUpdater.on('update-downloaded', () => { dialog.showMessageBox({ title: '更新完成', message: '新版本已下载完成，应用将重启并更新', buttons: ['立即重启'] }).then(() => { autoUpdater.quitAndInstall(); // 退出并安装更新 }); });
// 更新错误 autoUpdater.on('error', (error) => { console.error('更新失败：', error); });
```

### 9.2 常用第三方生态库

|库名|功能|
|---|---|
|electron-vite|Electron + Vite 构建工具，零配置支持Vue/React|
|electron-builder|全平台打包工具|
|electron-updater|自动更新方案|
|electron-store|持久化数据存储|
|electron-log|日志管理|
|better-sqlite3|SQLite数据库集成|
|@electron/remote|安全的渲染进程访问主进程对象方案|
|electron-updater|应用自动更新|
|screenshot-desktop|系统截图能力|

### 9.3 官方学习资源

1. [Electron 官方文档](sslocal://flow/file_open?url=https%3A%2F%2Fwww.electronjs.org%2Fzh%2Fdocs%2Flatest%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：最权威、最新的开发文档
2. [Electron Fiddle](sslocal://flow/file_open?url=https%3A%2F%2Fwww.electronjs.org%2Ffiddle&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：官方提供的Electron代码快速测试工具，无需搭建项目即可运行代码
3. [Electron 安全规范](sslocal://flow/file_open?url=https%3A%2F%2Fwww.electronjs.org%2Fzh%2Fdocs%2Flatest%2Ftutorial%2Fsecurity&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：官方安全清单，必须通读
4. [electron-builder 官方文档](sslocal://flow/file_open?url=https%3A%2F%2Fwww.electron.build%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：打包工具完整配置文档

### 9.4 开源项目参考

- [VS Code](sslocal://flow/file_open?url=https%3A%2F%2Fgithub.com%2Fmicrosoft%2Fvscode&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：微软开源的代码编辑器，最经典的Electron商用项目
- [Discord](sslocal://flow/file_open?url=https%3A%2F%2Fgithub.com%2Fdiscord&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：知名语音聊天应用
- [Figma](sslocal://flow/file_open?url=https%3A%2F%2Fwww.figma.com%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：UI设计工具
- [Notion](sslocal://flow/file_open?url=https%3A%2F%2Fwww.notion.so%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：笔记协作工具
- [Slack](sslocal://flow/file_open?url=https%3A%2F%2Fslack.com%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)：企业协作工具

## Electron 核心模块
---
### 一、窗口管理（BrowserWindow 核心模块）
#### 核心概述
窗口是Electron桌面应用的核心载体，所有界面都基于`BrowserWindow`类创建，**仅能在主进程中实例化与管理**，渲染进程只能通过IPC间接操作窗口。窗口管理的核心是：窗口生命周期控制、状态操作、自定义样式、多窗口管理。

#### 1. 窗口创建的安全基线（必选配置，禁止修改）
无论什么窗口，创建时必须保留以下安全配置，这是Electron应用的安全底线，上一期已强调，这里再次固化：
```typescript
// electron/main.ts 窗口基础配置
import { app, BrowserWindow } from 'electron'
import path from 'path'

// 全局窗口实例：必须声明在顶层，防止被JS垃圾回收（GC）导致窗口意外关闭
export let mainWindow: BrowserWindow | null = null

// 通用窗口安全配置（所有窗口都复用此配置）
const baseWindowConfig = {
  webPreferences: {
    contextIsolation: true, // 强制开启上下文隔离，绝对禁止关闭
    nodeIntegration: false, // 强制禁用渲染进程Node集成，绝对禁止开启
    nodeIntegrationInWorker: false,
    webSecurity: true, // 开启web安全，禁止跨域未授权请求
    allowRunningInsecureContent: false,
    sandbox: true
    preload: path.join(__dirname, '../preload/preload.js') // 唯一合法的桥接入口
  }
}

// 创建主窗口
function createMainWindow() {
  mainWindow = new BrowserWindow({
    // 基础尺寸
  width: 1200,
  height: 800,
  minWidth: 800,  // 最小宽度
  minHeight: 500, // 最小高度
  // 窗口位置
  x: 100,
  y: 100,
  center: true, // 居中显示
  // 窗口外观
  show: false, // 先隐藏，等页面加载完成再显示，避免白屏
  title: "我的应用",
  icon: path.join(__dirname, "assets/icon.png"),
  frame: true,  // 是否显示原生边框（false为无边框窗口）
  transparent: false, // 透明窗口
  resizable: true, // 是否可缩放
  movable: true, // 是否可拖动
  minimizable: true, // 是否可最小化
  maximizable: true, // 是否可最大化
  alwaysOnTop: false, // 是否置顶
  fullscreen: false, // 是否全屏
  autoHideMenuBar: true, // 自动隐藏菜单栏
  // 父子窗口与模态窗口
  parent: parentWindow, // 父窗口
  modal: true, // 模态窗口（阻塞父窗口操作）
  // 安全配置（固定不变）
    ...baseWindowConfig // 注入安全基线配置
  })

  // 开发/生产环境加载逻辑
  if (process.env.VITE_DEV_SERVER_URL) {
    mainWindow.loadURL(process.env.VITE_DEV_SERVER_URL)
    mainWindow.webContents.openDevTools() // 开发环境自动打开控制台
  } else {
    mainWindow.loadFile(path.join(__dirname, '../index.html'))
  }

  // 页面加载完成后再显示窗口，彻底解决启动白屏问题
  mainWindow.on('ready-to-show', () => {
    mainWindow?.show()
    mainWindow?.focus() // 聚焦窗口
  })

  // 窗口关闭时销毁实例，释放内存
  mainWindow.on('closed', () => {
    mainWindow = null
  })
}

// 应用就绪后创建窗口
app.whenReady().then(createMainWindow)

// 跨平台退出逻辑
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit() // macOS关闭所有窗口不退出应用
})

app.on('activate', () => {
  if (BrowserWindow.getAllWindows().length === 0) createMainWindow() // macOS点击Dock重建窗口
})
```

#### 2. 窗口常用状态操作（全场景覆盖）
以下是桌面应用99%会用到的窗口操作，全部通过IPC封装，渲染进程可安全调用，包含：最小化、最大化/还原、置顶、全屏、关闭、获取窗口状态。
#####  常用窗口操作

```javascript
// 窗口显示与隐藏
mainWindow.show();
mainWindow.hide();
// 窗口状态控制
mainWindow.minimize(); // 最小化
mainWindow.maximize(); // 最大化
mainWindow.restore();  // 恢复窗口
mainWindow.close();    // 关闭窗口
mainWindow.setAlwaysOnTop(true); // 窗口置顶
// 窗口内容操作
mainWindow.loadFile('index.html'); // 加载本地文件
mainWindow.loadURL('https://www.xxx.com'); // 加载远程URL
mainWindow.reload(); // 刷新页面
mainWindow.webContents.openDevTools(); // 打开开发者工具
```
##### 常用窗口事件
```javascript
// 窗口准备完成显示时触发（避免白屏）
mainWindow.once('ready-to-show', () => {
  mainWindow.show();
});
// 窗口关闭前触发
mainWindow.on('close', (event) => {
  // 可拦截关闭，比如弹窗确认
  event.preventDefault(); // 阻止默认关闭行为
  mainWindow.destroy(); // 强制销毁窗口
});
// 窗口获得/失去焦点
mainWindow.on('focus', () => console.log('窗口聚焦'));
mainWindow.on('blur', () => console.log('窗口失焦'));
// 窗口大小改变
mainWindow.on('resize', () => {
  const [width, height] = mainWindow.getSize();
  console.log('窗口尺寸：', width, height);
});

```

##### 步骤1：主进程注册IPC处理逻辑（electron/main.ts）
追加到之前的main.ts中即可：
```typescript
import { ipcMain } from 'electron'

// 1. 窗口最小化
ipcMain.handle('window:minimize', () => {
  if (!mainWindow || mainWindow.isDestroyed()) return false
  mainWindow.minimize()
  return true
})

// 2. 窗口最大化/还原切换
ipcMain.handle('window:toggle-maximize', () => {
  if (!mainWindow || mainWindow.isDestroyed()) return { isMaximized: false }
  if (mainWindow.isMaximized()) {
    mainWindow.unmaximize()
    return { isMaximized: false }
  } else {
    mainWindow.maximize()
    return { isMaximized: true }
  }
})

// 3. 获取窗口当前最大化状态
ipcMain.handle('window:get-maximize-status', () => {
  if (!mainWindow || mainWindow.isDestroyed()) return false
  return mainWindow.isMaximized()
})

// 4. 窗口置顶/取消置顶切换
ipcMain.handle('window:toggle-always-on-top', () => {
  if (!mainWindow || mainWindow.isDestroyed()) return { isOnTop: false }
  const isOnTop = mainWindow.isAlwaysOnTop()
  mainWindow.setAlwaysOnTop(!isOnTop)
  return { isOnTop: !isOnTop }
})

// 5. 全屏/退出全屏切换
ipcMain.handle('window:toggle-fullscreen', () => {
  if (!mainWindow || mainWindow.isDestroyed()) return { isFullscreen: false }
  const isFullscreen = mainWindow.isFullScreen()
  mainWindow.setFullScreen(!isFullscreen)
  return { isFullscreen: !isFullscreen }
})

// 6. 窗口关闭
ipcMain.handle('window:close', () => {
  if (!mainWindow || mainWindow.isDestroyed()) return false
  mainWindow.close()
  return true
})

// 7. 窗口居中显示
ipcMain.handle('window:center', () => {
  if (!mainWindow || mainWindow.isDestroyed()) return false
  mainWindow.center()
  return true
})
```

##### 步骤2：预加载脚本暴露API（electron/preload.ts）
追加到之前的`electronAPI`中，严格白名单校验：
```typescript
import { contextBridge, ipcRenderer } from 'electron'

contextBridge.exposeInMainWorld('electronAPI', {
  // 保留之前的IPC、file等API，追加窗口管理API
  window: {
    minimize: () => ipcRenderer.invoke('window:minimize'),
    toggleMaximize: () => ipcRenderer.invoke('window:toggle-maximize'),
    getMaximizeStatus: () => ipcRenderer.invoke('window:get-maximize-status'),
    toggleAlwaysOnTop: () => ipcRenderer.invoke('window:toggle-always-on-top'),
    toggleFullscreen: () => ipcRenderer.invoke('window:toggle-fullscreen'),
    close: () => ipcRenderer.invoke('window:close'),
    center: () => ipcRenderer.invoke('window:center')
  }
})

// 补充TypeScript类型声明
declare global {
  interface Window {
    electronAPI: {
      // 之前的类型保留，追加窗口类型
      window: {
        minimize: () => Promise<boolean>
        toggleMaximize: () => Promise<{ isMaximized: boolean }>
        getMaximizeStatus: () => Promise<boolean>
        toggleAlwaysOnTop: () => Promise<{ isOnTop: boolean }>
        toggleFullscreen: () => Promise<{ isFullscreen: boolean }>
        close: () => Promise<boolean>
        center: () => Promise<boolean>
      }
    }
  }
}
```

##### 步骤3：Vue 3 组件中调用（自定义标题栏示例）
最常用的场景是自定义无边框窗口的标题栏，直接复用以下代码即可：
```vue
<template>
  <!-- 自定义标题栏：无边框窗口专用 -->
  <div class="custom-titlebar">
    <!-- 拖拽区域：必须设置drag属性 -->
    <div class="titlebar-drag">
      <img src="/assets/icon.png" class="logo" />
      <span class="title">助手</span>
    </div>
    <!-- 窗口控制按钮：必须设置no-drag，否则无法点击 -->
    <div class="titlebar-controls">
      <el-button size="small" circle @click="handleMinimize" :icon="Minus" />
      <el-button size="small" circle @click="handleToggleMaximize" :icon="isMaximized ? Crop : FullScreen" />
      <el-button size="small" circle type="danger" @click="handleClose" :icon="Close" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { Minus, FullScreen, Crop, Close } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'

// 窗口状态
const isMaximized = ref(false)

// 窗口操作方法
const handleMinimize = async () => {
  await window.electronAPI.window.minimize()
}

const handleToggleMaximize = async () => {
  const res = await window.electronAPI.window.toggleMaximize()
  isMaximized.value = res.isMaximized
}

const handleClose = async () => {
  await window.electronAPI.window.close()
}

// 组件挂载时获取窗口初始状态
onMounted(async () => {
  isMaximized.value = await window.electronAPI.window.getMaximizeStatus()
})
</script>

<style scoped>
.custom-titlebar {
  width: 100%;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: #f5f5f5;
  border-bottom: 1px solid #e4e4e4;
  /* 禁止系统默认双击最大化，交给我们自己控制 */
  -webkit-user-select: none;
}

.titlebar-drag {
  width: 70%;
  height: 100%;
  display: flex;
  align-items: center;
  padding-left: 12px;
  gap: 8px;
  /* 核心：设置为可拖拽区域，无边框窗口必须加这个才能拖动 */
  -webkit-app-region: drag;
}

.titlebar-controls {
  display: flex;
  gap: 8px;
  padding-right: 12px;
  /* 核心：按钮区域禁止拖拽，否则无法点击 */
  -webkit-app-region: no-drag;
}

.logo {
  width: 24px;
  height: 24px;
}

.title {
  font-size: 14px;
  color: #333;
}
</style>
```

#### 3. 自定义无边框窗口（核心业务场景）
如果要使用上面的自定义标题栏，只需要在创建窗口时，给`BrowserWindow`添加`frame: false`配置即可：
```typescript
// electron/main.ts 创建窗口时
mainWindow = new BrowserWindow({
  frame: false, // 关闭系统默认边框，开启无边框窗口
  titleBarStyle: 'hidden', // macOS适配：隐藏标题栏，保留红绿灯按钮
  ...其他配置
})
```

#### 4. 最佳实践与高频踩坑指南
| 常见问题                    | 根本原因                     | 解决方案                                                     |
| --------------------------- | ---------------------------- | ------------------------------------------------------------ |
| 窗口打开瞬间白屏            | 窗口先显示，页面还没加载完成 | 配置`show: false`，监听`ready-to-show`事件后再`show()`       |
| 运行一段时间窗口意外消失    | 窗口实例被JS垃圾回收         | 必须把`BrowserWindow`实例声明为顶层全局变量，不要放在函数内部 |
| 无边框窗口无法拖动          | 没有设置可拖拽区域           | 给拖拽区域添加CSS：`-webkit-app-region: drag`                |
| 无边框窗口按钮无法点击      | 按钮在拖拽区域内             | 给按钮添加CSS：`-webkit-app-region: no-drag`                 |
| macOS窗口关闭后无法重新打开 | 没有监听`activate`事件       | 监听`app.on('activate')`，无窗口时重新创建                   |
| 窗口最大化后遮挡任务栏      | 没有设置最小宽高/适配系统    | 不要用`setBounds`强制铺满屏幕，用原生`maximize()`方法        |

---

### 二、系统托盘（Tray 模块）
#### 核心概述
系统托盘是桌面应用后台驻留、快捷操作的核心入口，对应Electron的`Tray`类，仅能在主进程中实例化，核心功能包括：托盘图标、右键菜单、点击事件、后台驻留。

#### 1. 托盘基础创建与核心配置
##### 步骤1：主进程完整实现（electron/main.ts）
```typescript
import { app, Tray, Menu, BrowserWindow, ipcMain } from 'electron'
import path from 'path'
import { mainWindow } from './main' // 导入上面创建的主窗口实例

// 全局托盘实例：必须顶层声明，防止被GC回收导致托盘消失
let tray: Tray | null = null

// 创建系统托盘
function createTray() {
  // 1. 图标路径处理：开发环境和生产环境兼容
  const iconPath = process.env.NODE_ENV === 'development'
    ? path.join(__dirname, '../../assets/tray-icon.png')
    : path.join(process.resourcesPath, 'assets/tray-icon.png')

  // 2. 实例化托盘
  tray = new Tray(iconPath)

  // 3. 设置托盘悬停提示
  tray.setToolTip('助手 - 运行中')

  // 4. 创建托盘右键菜单
  const trayMenu = Menu.buildFromTemplate([
    {
      label: '显示主窗口',
      click: () => {
        if (mainWindow) {
          mainWindow.show()
          mainWindow.focus()
        }
      }
    },
    {
      label: '隐藏主窗口',
      click: () => mainWindow?.hide()
    },
    { type: 'separator' }, // 分割线
    {
      label: '开机自启',
      type: 'checkbox',
      checked: app.getLoginItemSettings().openAtLogin,
      click: (menuItem) => {
        app.setLoginItemSettings({ openAtLogin: menuItem.checked })
      }
    },
    { type: 'separator' },
    {
      label: '退出应用',
      click: () => {
        app.quit()
      }
    }
  ])

  // 5. 绑定右键菜单
  tray.setContextMenu(trayMenu)

  // 6. 左键点击事件：Windows/Linux 左键点击显示/隐藏窗口，macOS默认触发右键菜单
  tray.on('click', () => {
    if (process.platform !== 'darwin') {
      if (mainWindow?.isVisible()) {
        mainWindow.hide()
      } else {
        mainWindow?.show()
        mainWindow?.focus()
      }
    }
  })

  // 7. 双击事件（仅Windows支持）
  tray.on('double-click', () => {
    mainWindow?.show()
    mainWindow?.focus()
  })
}

// 应用就绪后创建托盘
app.whenReady().then(() => {
  createTray()
})

// 应用退出前销毁托盘
app.on('before-quit', () => {
  tray?.destroy()
  tray = null
})
```

#### 2. 核心业务场景：最小化到托盘/关闭到托盘
桌面应用最常用的需求：点击关闭按钮不退出应用，而是最小化到托盘后台运行，实现如下：
```typescript
// electron/main.ts 创建主窗口时，追加窗口关闭拦截逻辑
mainWindow.on('close', (event) => {
  // 如果是用户主动退出应用，不拦截
  if (app.isQuiting) {
    mainWindow = null
    return
  }

  // 拦截关闭事件，隐藏窗口，最小化到托盘
  event.preventDefault()
  mainWindow?.hide()

  // Windows系统：显示气泡提示
  if (process.platform === 'win32') {
    tray?.displayBalloon({
      iconType: 'info',
      title: '助手',
      content: '应用已最小化到系统托盘，后台运行中'
    })
  }
})

// 重写app.quit方法，标记退出状态
app.on('before-quit', () => {
  app.isQuiting = true
})

// 补充TypeScript类型声明
declare global {
  namespace NodeJS {
    interface Global {
      isQuiting: boolean
    }
  }
}
```

#### 3. 托盘高级功能
##### （1）托盘图标闪烁（仅Windows支持）
```typescript
// 托盘闪烁函数
let flashInterval: NodeJS.Timeout | null = null
function startTrayFlash(interval = 500) {
  if (!tray || flashInterval) return
  const normalIcon = path.join(__dirname, '../../assets/tray-icon.png')
  const emptyIcon = path.join(__dirname, '../../assets/empty-icon.png') // 透明图标
  let isShow = true

  flashInterval = setInterval(() => {
    tray?.setImage(isShow ? normalIcon : emptyIcon)
    isShow = !isShow
  }, interval)
}

// 停止闪烁
function stopTrayFlash() {
  if (flashInterval) {
    clearInterval(flashInterval)
    flashInterval = null
    tray?.setImage(path.join(__dirname, '../../assets/tray-icon.png'))
  }
}

// 示例：收到告警消息时触发闪烁
ipcMain.on('tray:start-flash', () => startTrayFlash())
ipcMain.on('tray:stop-flash', () => stopTrayFlash())
```

##### （2）托盘消息角标（macOS支持，Windows部分支持）
```typescript
// macOS：Dock栏角标
app.setBadgeCount(10) // 设置未读消息数
app.setBadgeCount(0) // 清空角标

// Windows：托盘图标叠加角标（Win10+支持）
tray?.setOverlayIcon(path.join(__dirname, '../../assets/badge.png'), '10条未读消息')
```

#### 4. 最佳实践与高频踩坑指南
| 常见问题               | 根本原因                         | 解决方案                                                     |
| ---------------------- | -------------------------------- | ------------------------------------------------------------ |
| 运行一段时间托盘消失   | 托盘实例被GC回收                 | 必须把`Tray`实例声明为顶层全局变量                           |
| 打包后托盘图标不显示   | 图标路径错误，生产环境找不到文件 | 生产环境用`process.resourcesPath`拼接路径，打包时把assets文件夹复制到resources目录 |
| 托盘图标模糊           | 图标尺寸不符合系统要求           | Windows用16x16/32x32px的ico格式，macOS用18x18/36x36px的png格式 |
| 右键菜单不显示         | 实例化托盘后没有立即设置菜单     | 实例化`Tray`后必须同步调用`setContextMenu`                   |
| 点击退出应用后进程残留 | 窗口关闭事件被拦截，没有释放实例 | 退出时标记`isQuiting`状态，拦截逻辑里放行退出                |

---

### 三、快捷键模块
#### 核心概述
Electron的快捷键分为两类：
1. **应用内快捷键**：仅当应用聚焦时生效，绑定在应用菜单上，用`Menu`模块的`accelerator`属性实现
2. **全局快捷键**：应用在后台、未聚焦时也能触发，用`globalShortcut`模块实现，仅能在主进程中注册

#### 1. 应用内快捷键（菜单绑定）
应用内快捷键是最安全、兼容性最好的方案，不会和系统快捷键冲突，同时会在菜单上显示快捷键提示，符合桌面应用交互规范。

##### 主进程完整实现（electron/main.ts）
```typescript
import { app, Menu, BrowserWindow, ipcMain } from 'electron'
import { mainWindow } from './main'

// 创建应用菜单
function createAppMenu() {
  const menuTemplate = [
    // macOS专用应用菜单
    ...(process.platform === 'darwin' ? [{
      label: app.name,
      submenu: [
        { label: '关于', role: 'about' },
        { type: 'separator' },
        { label: '服务', role: 'services' },
        { type: 'separator' },
        { label: '隐藏', role: 'hide' },
        { label: '隐藏其他', role: 'hideOthers' },
        { label: '显示全部', role: 'unhide' },
        { type: 'separator' },
        { label: '退出', role: 'quit' }
      ]
    }] : []),
    // 文件菜单
    {
      label: '文件',
      submenu: [
        {
          label: '新建文件',
          accelerator: 'CmdOrCtrl+N', // 跨平台快捷键：Cmd(macOS)/Ctrl(Windows/Linux) + N
          click: () => {
            // 点击菜单/按快捷键时，给渲染进程发消息
            mainWindow?.webContents.send('menu:new-file')
          }
        },
        {
          label: '打开文件',
          accelerator: 'CmdOrCtrl+O',
          click: () => mainWindow?.webContents.send('menu:open-file')
        },
        {
          label: '保存文件',
          accelerator: 'CmdOrCtrl+S',
          click: () => mainWindow?.webContents.send('menu:save-file')
        },
        { type: 'separator' },
        {
          label: '打印',
          accelerator: 'CmdOrCtrl+P',
          click: () => mainWindow?.webContents.print()
        }
      ]
    },
    // 编辑菜单
    {
      label: '编辑',
      submenu: [
        { label: '撤销', accelerator: 'CmdOrCtrl+Z', role: 'undo' },
        { label: '重做', accelerator: 'Shift+CmdOrCtrl+Z', role: 'redo' },
        { type: 'separator' },
        { label: '剪切', accelerator: 'CmdOrCtrl+X', role: 'cut' },
        { label: '复制', accelerator: 'CmdOrCtrl+C', role: 'copy' },
        { label: '粘贴', accelerator: 'CmdOrCtrl+V', role: 'paste' },
        { label: '全选', accelerator: 'CmdOrCtrl+A', role: 'selectAll' }
      ]
    },
    // 视图菜单
    {
      label: '视图',
      submenu: [
        { label: '刷新', accelerator: 'F5', role: 'reload' },
        { label: '强制刷新', accelerator: 'Ctrl+F5', role: 'forceReload' },
        { label: '开发者工具', accelerator: 'F12', role: 'toggleDevTools' },
        { type: 'separator' },
        { label: '实际大小', accelerator: 'CmdOrCtrl+0', role: 'resetZoom' },
        { label: '放大', accelerator: 'CmdOrCtrl+Plus', role: 'zoomIn' },
        { label: '缩小', accelerator: 'CmdOrCtrl+-', role: 'zoomOut' },
        { type: 'separator' },
        { label: '全屏', accelerator: 'F11', role: 'togglefullscreen' }
      ]
    }
  ]

  // 构建菜单并设置为应用菜单
  const appMenu = Menu.buildFromTemplate(menuTemplate as any)
  Menu.setApplicationMenu(appMenu)
}

// 应用就绪后创建菜单
app.whenReady().then(createAppMenu)
```

##### 渲染进程监听菜单快捷键事件（Vue组件）
```typescript
// src/App.vue
import { onMounted, onUnmounted } from 'vue'
import { ElMessage } from 'element-plus'

onMounted(() => {
  // 监听菜单快捷键事件
  window.electronAPI.on('menu:new-file', () => {
    ElMessage.info('触发新建文件快捷键')
    // 执行你的新建文件逻辑
  })

  window.electronAPI.on('menu:open-file', () => {
    ElMessage.info('触发打开文件快捷键')
  })

  window.electronAPI.on('menu:save-file', () => {
    ElMessage.info('触发保存文件快捷键')
  })
})
```

#### 2. 全局系统快捷键
全局快捷键即使应用在后台、未聚焦时也能触发，适合截图、语音唤醒、快速打开窗口等场景。

##### 主进程完整实现（electron/main.ts）
```typescript
import { app, globalShortcut, ipcMain } from 'electron'
import { mainWindow } from './main'

// 应用就绪后注册全局快捷键
app.whenReady().then(() => {
  // 1. 注册全局快捷键：Ctrl+Alt+O 快速打开/隐藏主窗口
  const registerSuccess = globalShortcut.register('Ctrl+Alt+O', () => {
    if (mainWindow?.isVisible()) {
      mainWindow.hide()
    } else {
      mainWindow?.show()
      mainWindow?.focus()
    }
  })

  // 2. 注册全局截图快捷键：Ctrl+Alt+A
  globalShortcut.register('Ctrl+Alt+A', () => {
    mainWindow?.webContents.send('shortcut:capture-screen')
  })

  // 校验快捷键是否注册成功
  if (!registerSuccess) {
    console.error('全局快捷键注册失败，可能被其他应用占用')
  }
})

// 应用即将退出时，注销所有全局快捷键（必须做，否则会残留占用）
app.on('will-quit', () => {
  globalShortcut.unregisterAll()
})

// 应用失去焦点时注销，获得焦点时重新注册（可选，按需开启）
app.on('browser-window-blur', () => {
  // globalShortcut.unregisterAll()
})

app.on('browser-window-focus', () => {
  // 重新注册快捷键
})
```

#### 3. 最佳实践与高频踩坑指南
| 常见问题                 | 根本原因                  | 解决方案                                                     |
| ------------------------ | ------------------------- | ------------------------------------------------------------ |
| 全局快捷键注册失败       | 快捷键被系统/其他应用占用 | 注册后校验返回值，给用户提示更换快捷键                       |
| 关闭应用后快捷键依然生效 | 没有注销全局快捷键        | 必须在`app.on('will-quit')`中调用`unregisterAll()`           |
| macOS无法注册全局快捷键  | 没有开启辅助功能权限      | macOS需要在「系统设置→隐私与安全性→辅助功能」中允许你的应用  |
| 输入框输入时触发快捷键   | 没有做聚焦判断            | 渲染进程监听事件时，判断是否有输入框聚焦，聚焦时不执行逻辑   |
| 快捷键不生效             | 菜单被隐藏/自定义菜单覆盖 | 自定义无边框窗口时，不要用`Menu.setApplicationMenu(null)`清空菜单，否则快捷键失效 |

---

### 四、原生通知（Notification 模块）
#### 核心概述
Electron的`Notification`模块基于系统原生通知中心实现，比HTML5的Notification兼容性更好、权限更高，支持点击事件、按钮、图标等高级配置，可在主进程和渲染进程（通过IPC）中调用。

#### 1. 通知权限申请与基础实现
##### 步骤1：主进程通知实现（electron/main.ts）
```typescript
import { app, Notification, ipcMain } from 'electron'
import path from 'path'

// 校验系统是否支持通知
function isNotificationSupported() {
  if (!Notification.isSupported()) {
    console.error('当前系统不支持原生通知')
    return false
  }
  return true
}

// 通用通知发送函数
export function sendNotification(options: {
  title: string
  body: string
  icon?: string
  silent?: boolean
  urgency?: 'low' | 'normal' | 'critical' // Linux专用
}) {
  if (!isNotificationSupported()) return

  const notification = new Notification({
    title: options.title,
    body: options.body,
    icon: options.icon || path.join(__dirname, '../../assets/notification-icon.png'),
    silent: options.silent || false,
    urgency: options.urgency || 'normal'
  })

  // 通知点击事件
  notification.on('click', () => {
    console.log('用户点击了通知')
    // 点击通知显示主窗口
    if (mainWindow) {
      mainWindow.show()
      mainWindow.focus()
    }
    // 给渲染进程发送点击事件
    mainWindow?.webContents.send('notification:click', options)
  })

  // 通知显示事件
  notification.on('show', () => {
    console.log('通知已显示')
  })

  // 通知关闭事件
  notification.on('close', () => {
    console.log('通知被关闭')
  })

  // 显示通知
  notification.show()
  return notification
}

// 注册IPC：渲染进程调用发送通知
ipcMain.handle('notification:send', async (event, options) => {
  const notification = sendNotification(options)
  return !!notification
})

// 示例：应用启动时发送通知
app.whenReady().then(() => {
  sendNotification({
    title: '助手',
    body: '应用已启动，正在后台运行中',
    silent: true
  })
})
```

##### 步骤2：预加载脚本暴露API（electron/preload.ts）
```typescript
contextBridge.exposeInMainWorld('electronAPI', {
  // 保留之前的API，追加通知API
  notification: {
    send: (options: { title: string; body: string; icon?: string; silent?: boolean }) => 
      ipcRenderer.invoke('notification:send', options)
  }
})

// 类型声明
declare global {
  interface Window {
    electronAPI: {
      notification: {
        send: (options: { title: string; body: string; icon?: string; silent?: boolean }) => Promise<boolean>
      }
    }
  }
}
```

##### 步骤3：Vue组件中调用
```vue
<template>
  <el-button type="primary" @click="handleSendNotification">发送系统通知</el-button>
</template>

<script setup lang="ts">
import { ElMessage } from 'element-plus'

const handleSendNotification = async () => {
  const success = await window.electronAPI.notification.send({
    title: '设备告警',
    body: '井下通风设备温度异常，请及时处理！',
    silent: false
  })

  if (success) {
    ElMessage.success('通知发送成功')
  } else {
    ElMessage.error('通知发送失败，系统不支持')
  }
}
</script>
```

#### 2. 高级配置与跨平台适配
| 平台    | 支持的高级特性             | 注意事项                                   |
| ------- | -------------------------- | ------------------------------------------ |
| Windows | 按钮、输入框、音频、进度条 | 仅Win10+支持，需要应用在通知中心的白名单中 |
| macOS   | 附件、按钮、分类、延时发送 | 需要申请通知权限，打包后需要代码签名       |
| Linux   | 紧急程度、图标             | 仅支持libnotify的桌面环境（GNOME、KDE）    |

##### Windows高级通知示例（带按钮）
```typescript
// Windows 10+ 带按钮的通知
const notification = new Notification({
  title: '设备告警',
  body: '井下通风设备温度异常',
  actions: [
    { type: 'button', text: '查看详情' },
    { type: 'button', text: '忽略告警' }
  ]
})

// 监听按钮点击
notification.on('action', (event, index) => {
  if (index === 0) {
    // 查看详情
    mainWindow?.show()
    mainWindow?.webContents.send('notification:view-detail')
  } else {
    // 忽略告警
    console.log('用户忽略了告警')
  }
})
```

#### 3. 最佳实践与高频踩坑指南
| 常见问题              | 根本原因               | 解决方案                                                  |
| --------------------- | ---------------------- | --------------------------------------------------------- |
| 通知不显示            | 系统不支持/权限被禁用  | 先调用`Notification.isSupported()`校验，macOS申请通知权限 |
| macOS打包后通知不显示 | 应用没有代码签名       | macOS发布必须经过苹果公证，否则通知权限会被禁用           |
| Windows通知不显示     | 应用没有在通知中心启用 | 提示用户在「系统设置→通知→找到你的应用」开启通知权限      |
| 通知点击事件不生效    | 通知实例被GC回收       | 把通知实例声明为全局变量，防止被回收                      |
| 重复发送大量通知      | 没有做节流限制         | 同一类型通知设置防抖，避免频繁发送打扰用户                |

---

### 五、文件系统模块
#### 核心概述
Electron的文件系统能力基于Node.js的`fs`模块实现，**所有文件操作必须放在主进程中执行**，渲染进程只能通过IPC间接调用，绝对禁止直接在渲染进程中使用`fs`模块。核心重点是：安全防护、路径兼容、打包适配。

#### 1. 系统对话框（文件/文件夹选择、保存）
Electron的`dialog`模块提供了系统原生的文件选择对话框，是文件操作的入口，必须在主进程中调用。

##### 步骤1：主进程IPC实现（electron/main.ts）
```typescript
import { ipcMain, dialog, app } from 'electron'
import fs from 'fs/promises'
import path from 'path'

// 1. 打开文件选择对话框
ipcMain.handle('dialog:open-file', async (event, options = {}) => {
  const { filters = [], multiSelections = false } = options
  const result = await dialog.showOpenDialog(mainWindow!, {
    title: '选择文件',
    properties: multiSelections ? ['openFile', 'multiSelections'] : ['openFile'],
    filters: filters.length ? filters : [{ name: '所有文件', extensions: ['*'] }],
    defaultPath: app.getPath('documents') // 默认打开文档目录
  })
  return result
})

// 2. 打开文件夹选择对话框
ipcMain.handle('dialog:open-folder', async () => {
  const result = await dialog.showOpenDialog(mainWindow!, {
    title: '选择文件夹',
    properties: ['openDirectory'],
    defaultPath: app.getPath('documents')
  })
  return result
})

// 3. 打开文件保存对话框
ipcMain.handle('dialog:save-file', async (event, options = {}) => {
  const { filters = [], defaultPath = '' } = options
  const result = await dialog.showSaveDialog(mainWindow!, {
    title: '保存文件',
    filters: filters.length ? filters : [{ name: '所有文件', extensions: ['*'] }],
    defaultPath: defaultPath || app.getPath('documents')
  })
  return result
})

// 4. 消息对话框（确认、提示）
ipcMain.handle('dialog:message-box', async (event, options = {}) => {
  const { type = 'info', title = '提示', message = '', buttons = ['确定', '取消'] } = options
  const result = await dialog.showMessageBox(mainWindow!, {
    type,
    title,
    message,
    buttons
  })
  return result.response // 返回点击的按钮索引
})
```

##### 步骤2：预加载脚本暴露API（electron/preload.ts）
```typescript
contextBridge.exposeInMainWorld('electronAPI', {
  // 保留之前的API，追加对话框API
  dialog: {
    openFile: (options?: any) => ipcRenderer.invoke('dialog:open-file', options),
    openFolder: () => ipcRenderer.invoke('dialog:open-folder'),
    saveFile: (options?: any) => ipcRenderer.invoke('dialog:save-file', options),
    messageBox: (options?: any) => ipcRenderer.invoke('dialog:message-box', options)
  }
})
```

##### 步骤3：Vue组件调用示例
```vue
<template>
  <div>
    <el-button @click="handleOpenFile">选择文件</el-button>
    <el-button @click="handleSaveFile" style="margin-left: 10px;">保存文件</el-button>
    <el-button @click="handleOpenFolder" style="margin-left: 10px;">选择文件夹</el-button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElMessage } from 'element-plus'

const fileContent = ref('')
const currentFilePath = ref('')

// 选择并读取文件
const handleOpenFile = async () => {
  const result = await window.electronAPI.dialog.openFile({
    filters: [{ name: '文本文件', extensions: ['txt', 'md', 'json'] }]
  })

  if (result.canceled) return
  const filePath = result.filePaths[0]
  currentFilePath.value = filePath

  // 调用文件读取API（下面会实现）
  const content = await window.electronAPI.file.readFile(filePath)
  if (content.success) {
    fileContent.value = content.data
    ElMessage.success('文件读取成功')
  } else {
    ElMessage.error('文件读取失败：' + content.error)
  }
}

// 保存文件
const handleSaveFile = async () => {
  if (!currentFilePath.value) {
    // 没有打开的文件，打开保存对话框
    const result = await window.electronAPI.dialog.saveFile({
      filters: [{ name: '文本文件', extensions: ['txt'] }]
    })
    if (result.canceled) return
    currentFilePath.value = result.filePath
  }

  // 写入文件
  const res = await window.electronAPI.file.writeFile(currentFilePath.value, fileContent.value)
  if (res.success) {
    ElMessage.success('文件保存成功')
  } else {
    ElMessage.error('保存失败：' + res.error)
  }
}

// 选择文件夹
const handleOpenFolder = async () => {
  const result = await window.electronAPI.dialog.openFolder()
  if (!result.canceled) {
    ElMessage.success(`选择的文件夹：${result.filePaths[0]}`)
  }
}
</script>
```

#### 2. 文件读写与文件夹操作
##### 主进程完整实现（electron/main.ts）
```typescript
import { ipcMain, app } from 'electron'
import fs from 'fs/promises'
import fsSync from 'fs'
import path from 'path'

// 安全校验：禁止路径遍历攻击
function validatePath(filePath: string) {
  // 只允许操作用户目录下的文件，禁止访问系统目录
  const userPath = app.getPath('home')
  const resolvedPath = path.resolve(filePath)
  // 校验路径是否在用户目录下
  if (!resolvedPath.startsWith(userPath)) {
    throw new Error('非法路径：禁止访问系统目录')
  }
  return resolvedPath
}

// 1. 读取文件
ipcMain.handle('file:read-file', async (event, filePath: string, encoding: BufferEncoding = 'utf-8') => {
  try {
    const validPath = validatePath(filePath)
    const data = await fs.readFile(validPath, encoding)
    return { success: true, data }
  } catch (err) {
    return { success: false, error: (err as Error).message }
  }
})

// 2. 写入文件
ipcMain.handle('file:write-file', async (event, filePath: string, content: string | Buffer, encoding: BufferEncoding = 'utf-8') => {
  try {
    const validPath = validatePath(filePath)
    await fs.writeFile(validPath, content, encoding)
    return { success: true }
  } catch (err) {
    return { success: false, error: (err as Error).message }
  }
})

// 3. 检查文件/文件夹是否存在
ipcMain.handle('file:exists', async (event, filePath: string) => {
  try {
    const validPath = validatePath(filePath)
    const exists = fsSync.existsSync(validPath)
    return { success: true, exists }
  } catch (err) {
    return { success: false, error: (err as Error).message }
  }
})

// 4. 创建文件夹
ipcMain.handle('file:mkdir', async (event, dirPath: string) => {
  try {
    const validPath = validatePath(dirPath)
    await fs.mkdir(validPath, { recursive: true }) // recursive: 递归创建多级目录
    return { success: true }
  } catch (err) {
    return { success: false, error: (err as Error).message }
  }
})

// 5. 读取文件夹内容
ipcMain.handle('file:readdir', async (event, dirPath: string) => {
  try {
    const validPath = validatePath(dirPath)
    const files = await fs.readdir(validPath, { withFileTypes: true })
    // 格式化返回结果，区分文件和文件夹
    const fileList = files.map(file => ({
      name: file.name,
      isFile: file.isFile(),
      isDirectory: file.isDirectory(),
      path: path.join(validPath, file.name)
    }))
    return { success: true, data: fileList }
  } catch (err) {
    return { success: false, error: (err as Error).message }
  }
})

// 6. 删除文件/文件夹
ipcMain.handle('file:delete', async (event, targetPath: string) => {
  try {
    const validPath = validatePath(targetPath)
    const stat = await fs.stat(validPath)
    if (stat.isDirectory()) {
      await fs.rm(validPath, { recursive: true, force: true }) // 递归删除文件夹
    } else {
      await fs.unlink(validPath) // 删除文件
    }
    return { success: true }
  } catch (err) {
    return { success: false, error: (err as Error).message }
  }
})

// 7. 获取系统常用路径
ipcMain.handle('app:get-path', async (event, pathName: any) => {
  try {
    // 白名单校验，只允许获取指定路径
    const validPathNames = ['home', 'documents', 'desktop', 'temp', 'userData', 'downloads']
    if (!validPathNames.includes(pathName)) {
      throw new Error('非法路径名称')
    }
    const targetPath = app.getPath(pathName)
    return { success: true, path: targetPath }
  } catch (err) {
    return { success: false, error: (err as Error).message }
  }
})
```

#### 3. 最佳实践与高频踩坑指南
| 常见问题                         | 根本原因                               | 解决方案                                                     |
| -------------------------------- | -------------------------------------- | ------------------------------------------------------------ |
| 打包后文件写入失败               | 应用安装目录是只读的，asar归档无法写入 | 所有用户数据必须写入`app.getPath('userData')`目录，绝对不要写入应用安装目录 |
| 出现路径遍历安全漏洞             | 没有校验渲染进程传来的路径             | 必须实现`validatePath`函数，校验路径是否在允许的目录内，禁止访问系统目录 |
| 开发环境路径正常，打包后路径错误 | __dirname在打包后路径变化              | 生产环境用`app.getPath()`、`process.resourcesPath`拼接路径，不要用相对路径 |
| 大文件读取内存溢出               | 一次性读取整个大文件                   | 大文件用`fs.createReadStream`流式读取，不要用`readFile`一次性读取 |
| 跨平台路径分隔符错误             | Windows用\，macOS/Linux用/             | 永远用`path.join()`、`path.resolve()`拼接路径，不要手动写分隔符 |

---

### 六、自动更新（electron-updater 模块）
#### 核心概述
`electron-updater`是`electron-builder`官方配套的自动更新方案，是目前Electron生态最成熟、最易用的自动更新工具，支持全量更新、增量更新、进度监听、静默更新等功能，完全兼容Windows、macOS、Linux三大平台。

#### 1. 环境准备与依赖安装
```bash
# 安装自动更新依赖
npm install -D electron-updater
```

#### 2. package.json 配置（核心）
在之前的打包配置基础上，追加自动更新相关配置：
```json
{
  "name": "smart-mine-assistant",
  "version": "1.0.0",
  "main": "dist-electron/main/main.js",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "electron:build": "vite build && electron-builder",
    "electron:build-win": "vite build && electron-builder --win",
    "electron:build-mac": "vite build && electron-builder --mac"
  },
  "build": {
    "appId": "com.smartmine.assistant",
    "productName": "助手",
    "directories": {
      "output": "release"
    },
    "files": [
      "dist/**/*",
      "dist-electron/**/*",
      "index.html"
    ],
    // 自动更新核心配置
    "publish": [
      {
        "provider": "generic", // 通用静态文件服务器，支持nginx、oss、cdn等
        "url": "https://your-update-server.com/update/" // 你的更新服务器地址
      }
    ],
    "win": {
      "target": "nsis",
      "icon": "assets/icon.ico",
      "verifyUpdateCodeSignature": false // 开发阶段关闭签名校验，发布时必须开启
    },
    "mac": {
      "target": "dmg",
      "icon": "assets/icon.icns",
      "hardenedRuntime": true,
      "gatekeeperAssess": false
    },
    "nsis": {
      "oneClick": false, // 取消一键安装，允许用户选择安装目录
      "allowToChangeInstallationDirectory": true, // 允许修改安装目录
      "createDesktopShortcut": true, // 创建桌面快捷方式
      "createStartMenuShortcut": true, // 创建开始菜单快捷方式
      "shortcutName": "助手"
    }
  }
}
```

#### 3. 主进程自动更新全量实现（electron/main.ts）
```typescript
import { app, ipcMain, dialog } from 'electron'
import { autoUpdater } from 'electron-updater'
import { mainWindow } from './main'

// 自动更新配置
autoUpdater.autoDownload = false // 关闭自动下载，让用户手动确认后再下载
autoUpdater.autoInstallOnAppQuit = true // 应用退出时自动安装更新
autoUpdater.allowDowngrade = false // 禁止版本降级
autoUpdater.allowPrerelease = false // 禁止预发布版本

// 开发环境模拟更新（必须配置，否则开发环境会报错）
if (process.env.NODE_ENV === 'development') {
  autoUpdater.updateConfigPath = path.join(__dirname, '../../dev-app-update.yml')
  // 开发环境禁用签名校验
  autoUpdater.disableWebSecurity = true
}

// ------------------------------
// 自动更新事件监听
// ------------------------------
// 1. 检查更新时出错
autoUpdater.on('error', (err) => {
  console.error('更新出错：', err)
  mainWindow?.webContents.send('update:error', err.message)
})

// 2. 正在检查更新
autoUpdater.on('checking-for-update', () => {
  console.log('正在检查更新...')
  mainWindow?.webContents.send('update:checking')
})

// 3. 发现可用更新
autoUpdater.on('update-available', (updateInfo) => {
  console.log('发现新版本：', updateInfo.version)
  mainWindow?.webContents.send('update:available', updateInfo)

  // 弹出确认框，询问用户是否更新
  dialog.showMessageBox(mainWindow!, {
    type: 'info',
    title: '发现新版本',
    message: `最新版本：v${updateInfo.version}`,
    detail: updateInfo.releaseNotes || '修复了已知问题，优化了用户体验',
    buttons: ['立即更新', '稍后再说']
  }).then((res) => {
    if (res.response === 0) {
      autoUpdater.downloadUpdate() // 用户确认后开始下载
    }
  })
})

// 4. 没有可用更新
autoUpdater.on('update-not-available', (updateInfo) => {
  console.log('当前已是最新版本')
  mainWindow?.webContents.send('update:not-available', updateInfo)
})

// 5. 更新下载进度
autoUpdater.on('download-progress', (progressObj) => {
  console.log(`下载进度：${progressObj.percent.toFixed(2)}%`)
  mainWindow?.webContents.send('update:download-progress', progressObj)
})

// 6. 更新下载完成
autoUpdater.on('update-downloaded', (updateInfo) => {
  console.log('更新包下载完成')
  mainWindow?.webContents.send('update:downloaded', updateInfo)

  // 弹出确认框，询问用户是否立即安装
  dialog.showMessageBox(mainWindow!, {
    type: 'info',
    title: '更新下载完成',
    message: '新版本已下载完成，是否立即安装重启？',
    buttons: ['立即安装', '稍后重启']
  }).then((res) => {
    if (res.response === 0) {
      // 退出应用并安装更新
      autoUpdater.quitAndInstall(false, true)
    }
  })
})

// ------------------------------
// 注册IPC：渲染进程手动触发更新检查
// ------------------------------
ipcMain.handle('update:check', async () => {
  if (!app.isPackaged) {
    return { success: false, message: '开发环境不支持自动更新' }
  }
  autoUpdater.checkForUpdates()
  return { success: true }
})

// 应用就绪后，自动检查更新（可选）
app.whenReady().then(() => {
  if (app.isPackaged) {
    // 延迟3秒检查更新，避免影响应用启动速度
    setTimeout(() => {
      autoUpdater.checkForUpdates()
    }, 3000)
  }
})
```

#### 4. 渲染进程更新UI实现（Vue组件）
```vue
<template>
  <div class="update-panel">
    <el-button type="primary" @click="handleCheckUpdate" :loading="checking">检查更新</el-button>
    
    <!-- 下载进度条 -->
    <el-progress 
      v-if="downloading" 
      :percentage="downloadPercent" 
      :status="downloadStatus"
      style="margin-top: 20px;"
    />
    <div v-if="downloading" style="margin-top: 10px;">
      下载速度：{{ downloadSpeed }} | 已下载：{{ downloadedSize }} / {{ totalSize }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { ElMessage } from 'element-plus'

// 状态管理
const checking = ref(false)
const downloading = ref(false)
const downloadPercent = ref(0)
const downloadSpeed = ref('0 KB/s')
const downloadedSize = ref('0 MB')
const totalSize = ref('0 MB')
const downloadStatus = ref<'success' | 'exception' | 'active'>('active')

// 检查更新
const handleCheckUpdate = async () => {
  checking.value = true
  const res = await window.electronAPI.update.check()
  if (!res.success) {
    ElMessage.warning(res.message)
    checking.value = false
  }
}

// 监听更新事件
const unSubscribeList: (() => void)[] = []

onMounted(() => {
  // 正在检查更新
  unSubscribeList.push(
    window.electronAPI.on('update:checking', () => {
      checking.value = true
      ElMessage.info('正在检查更新...')
    })
  )

  // 发现新版本
  unSubscribeList.push(
    window.electronAPI.on('update:available', (info) => {
      checking.value = false
      ElMessage.info(`发现新版本 v${info.version}`)
    })
  )

  // 已是最新版本
  unSubscribeList.push(
    window.electronAPI.on('update:not-available', () => {
      checking.value = false
      ElMessage.success('当前已是最新版本')
    })
  )

  // 更新出错
  unSubscribeList.push(
    window.electronAPI.on('update:error', (err) => {
      checking.value = false
      downloading.value = false
      downloadStatus.value = 'exception'
      ElMessage.error('更新失败：' + err)
    })
  )

  // 下载进度
  unSubscribeList.push(
    window.electronAPI.on('update:download-progress', (progress) => {
      downloading.value = true
      downloadPercent.value = Math.floor(progress.percent)
      downloadSpeed.value = progress.bytesPerSecond > 1024 * 1024
        ? `${(progress.bytesPerSecond / 1024 / 1024).toFixed(2)} MB/s`
        : `${(progress.bytesPerSecond / 1024).toFixed(2)} KB/s`
      downloadedSize.value = `${(progress.transferred / 1024 / 1024).toFixed(2)} MB`
      totalSize.value = `${(progress.total / 1024 / 1024).toFixed(2)} MB`
    })
  )

  // 下载完成
  unSubscribeList.push(
    window.electronAPI.on('update:downloaded', () => {
      downloading.value = false
      downloadPercent.value = 100
      downloadStatus.value = 'success'
      ElMessage.success('更新包下载完成，即将安装重启')
    })
  )
})

// 组件卸载时取消所有监听
onUnmounted(() => {
  unSubscribeList.forEach(unSubscribe => unSubscribe())
})
</script>
```

#### 5. 更新服务极简搭建
1. 执行打包命令：`npm run electron:build-win`
2. 打包完成后，`release/`目录下会生成以下文件：
   - `助手 Setup 1.0.0.exe`（Windows安装包）
   - `latest.yml`（更新配置文件，核心！）
3. 把`release/`目录下的所有文件，上传到你的静态服务器（nginx、阿里云OSS、腾讯云COS等）的`update/`目录下
4. 确保你的`package.json`中的`publish.url`和上传的地址一致，比如`https://oss.xxx.com/update/`
5. 当你发布新版本时，只需要修改`package.json`中的`version`版本号，重新打包，把新的安装包和`latest.yml`上传到服务器即可，客户端会自动检测到新版本。

#### 6. 最佳实践与高频踩坑指南
| 常见问题                 | 根本原因                                   | 解决方案                                                     |
| ------------------------ | ------------------------------------------ | ------------------------------------------------------------ |
| 开发环境更新报错         | 开发环境不支持自动更新                     | 必须判断`app.isPackaged`，打包后才执行更新逻辑               |
| 检查更新时提示404        | 更新服务器地址错误，或latest.yml文件不存在 | 确保`publish.url`正确，文件上传完整，服务器开启了静态文件访问 |
| macOS更新失败            | 应用没有代码签名和公证                     | macOS发布必须经过苹果开发者账号签名和公证，否则无法更新      |
| Windows更新失败          | 安装包没有数字签名                         | Windows发布建议使用数字证书签名，否则会被系统拦截            |
| 下载更新包时提示校验失败 | 服务器上的文件和本地版本不匹配             | 每次更新必须重新打包，完整上传所有文件，不要手动修改latest.yml |
| 自动更新不生效           | 版本号没有修改                             | 新版本的version必须大于当前版本，遵循语义化版本规范          |



### IPC 进程间通信

---

#### 一、先搞懂核心前提：为什么IPC是Electron的命脉？
##### 1.1 为什么必须用IPC通信？
Electron的架构是**双进程隔离模型**，两个进程的运行环境、权限、能力完全隔离，无法直接互相调用变量/方法，必须通过IPC（Inter-Process Communication，进程间通信）作为唯一的桥梁：
- **主进程**：Node.js 全权限环境，能调用所有系统原生API、操作文件、管理窗口/托盘，是应用的“大脑”，但不能直接操作前端DOM。
- **渲染进程**：Chromium 浏览器环境，运行你的Vue 3代码，能操作DOM、渲染界面，但默认没有系统权限，也不能直接调用Node.js API。
- **预加载脚本（Preload）**：唯一的“安全桥”，运行在渲染进程的隔离上下文里，既能访问Node.js/electron API，又能接触window对象，是IPC通信的唯一安全入口。

##### 1.2 绝对不能碰的安全红线（先讲规则，再写代码）
Electron 12+ 版本默认开启了2项核心安全配置，**绝对不要为了开发方便关闭**，否则会出现致命的XSS安全漏洞（恶意代码可直接获取系统最高权限）：
```typescript
// 主进程创建窗口时，必须保留的配置
webPreferences: {
  contextIsolation: true, // 上下文隔离，开启后渲染进程无法直接访问Node.js
  nodeIntegration: false, // 禁用渲染进程的Node.js集成
  preload: path.join(__dirname, '../preload/preload.js') // 唯一合法的桥接入口
}
```
所有IPC通信，必须通过**预加载脚本 + contextBridge** 实现，禁止直接在Vue渲染进程中`import { ipcRenderer } from 'electron'`，这是新手最常见的错误。

---

#### 二、IPC通信的3种核心模式（全场景覆盖）
Electron的IPC通信只有3种核心模式，对应所有业务场景，我们逐个拆解，每个模式都讲清：**适用场景、完整代码实现、注意事项**，完全适配你的Vue 3项目。

##### 模式1：双向通信（渲染→主→渲染）【最常用，首推】
###### 核心API
- 渲染进程发起：`ipcRenderer.invoke(通道名, 参数)`
- 主进程响应：`ipcMain.handle(通道名, 异步处理函数)`
###### 适用场景
渲染进程需要**请求主进程执行操作，并拿到返回结果**，90%的业务场景都用这个模式，比如：
- 打开系统文件/文件夹选择框
- 读取/写入本地配置文件
- 调用系统原生API（获取硬件信息、电源状态）
- 代理网络请求（解决渲染进程跨域问题）
- 操作窗口状态（最大化、最小化、置顶）

###### 完整代码实现
步骤1：主进程注册处理函数（electron/main.ts）

```typescript
import { app, BrowserWindow, ipcMain, dialog } from 'electron'
import path from 'path'
import fs from 'fs/promises'

let mainWindow: BrowserWindow | null = null

// 1. 注册双向IPC处理函数：必须在app.whenReady()之前注册，避免渲染进程发起请求时还未注册
// 场景1：打开文件选择对话框，返回文件路径和内容
ipcMain.handle('file:open', async (event, filters = []) => {
  // event：事件对象，包含发起请求的渲染进程信息
  // filters：渲染进程传入的参数，必须做校验，防止恶意注入
  if (!Array.isArray(filters)) throw new Error('参数格式错误')

  const result = await dialog.showOpenDialog(mainWindow!, {
    properties: ['openFile'],
    filters: filters.length ? filters : [{ name: '所有文件', extensions: ['*'] }]
  })

  // 用户取消选择
  if (result.canceled) return { canceled: true, data: null }

  // 读取文件内容
  const filePath = result.filePaths[0]
  const content = await fs.readFile(filePath, 'utf-8')
  return { canceled: false, path: filePath, content }
})

// 场景2：写入文件，返回是否成功
ipcMain.handle('file:write', async (event, { filePath, content }) => {
  // 严格校验入参，防止路径遍历攻击
  if (!filePath || typeof filePath !== 'string') throw new Error('文件路径非法')
  if (content === undefined) throw new Error('文件内容不能为空')

  try {
    await fs.writeFile(filePath, content, 'utf-8')
    return { success: true }
  } catch (err) {
    return { success: false, error: (err as Error).message }
  }
})

// 窗口创建函数
function createWindow() {
  mainWindow = new BrowserWindow({
    width: 1200,
    height: 800,
    webPreferences: {
      contextIsolation: true,
      nodeIntegration: false,
      preload: path.join(__dirname, '../preload/preload.js')
    }
  })

  // 开发/生产环境加载逻辑
  if (process.env.VITE_DEV_SERVER_URL) {
    mainWindow.loadURL(process.env.VITE_DEV_SERVER_URL)
  } else {
    mainWindow.loadFile(path.join(__dirname, '../index.html'))
  }
}

app.whenReady().then(createWindow)
```

步骤2：预加载脚本安全暴露API（electron/preload.ts）

通过`contextBridge`做**白名单暴露**，只给渲染进程开放明确允许的方法，绝对不能直接把`ipcRenderer`整个暴露出去。
```typescript
import { contextBridge, ipcRenderer } from 'electron'

// 安全暴露给渲染进程的API，挂载到 window.electronAPI
contextBridge.exposeInMainWorld('electronAPI', {
  // 双向通信：文件操作
  file: {
    open: (filters: any[]) => ipcRenderer.invoke('file:open', filters),
    write: (params: { filePath: string; content: string }) => ipcRenderer.invoke('file:write', params)
  }
})

// TypeScript 类型声明，让Vue组件获得完整类型提示
declare global {
  interface Window {
    electronAPI: {
      file: {
        open: (filters: any[]) => Promise<{ canceled: boolean; path?: string; content?: string }>
        write: (params: { filePath: string; content: string }) => Promise<{ success: boolean; error?: string }>
      }
    }
  }
}
```

步骤3：Vue 3 组件中调用（src/App.vue）

```vue
<template>
  <div>
    <el-button type="primary" @click="handleOpenFile">打开文件</el-button>
    <el-button 
      type="success" 
      @click="handleSaveFile" 
      :disabled="!currentFile"
      style="margin-left: 10px;"
    >
      保存文件
    </el-button>
    <el-input
      v-model="fileContent"
      type="textarea"
      :rows="10"
      style="margin-top: 20px;"
      placeholder="文件内容"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { ElMessage } from 'element-plus'

// 状态管理
const currentFile = ref<string>('')
const fileContent = ref<string>('')

// 打开文件：调用IPC双向通信
const handleOpenFile = async () => {
  try {
    // 调用预加载脚本暴露的API
    const result = await window.electronAPI.file.open([
      { name: '文本文件', extensions: ['txt', 'md'] }
    ])

    if (result.canceled) return

    // 赋值渲染
    currentFile.value = result.path!
    fileContent.value = result.content!
    ElMessage.success('文件打开成功')
  } catch (err) {
    ElMessage.error('文件打开失败：' + (err as Error).message)
  }
}

// 保存文件：调用IPC双向通信
const handleSaveFile = async () => {
  if (!currentFile.value) return ElMessage.warning('请先打开文件')
  
  const result = await window.electronAPI.file.write({
    filePath: currentFile.value,
    content: fileContent.value
  })

  if (result.success) {
    ElMessage.success('文件保存成功')
  } else {
    ElMessage.error('保存失败：' + result.error)
  }
}
</script>
```

###### 核心优势
1. **Promise 原生支持**：完美适配Vue 3的async/await语法，避免回调地狱
2. **自动异常处理**：主进程抛出的错误会直接传递到渲染进程的catch中，无需手动处理
3. **单次响应**：一个invoke请求只会对应一个handle响应，不会出现重复监听的问题
4. **内存安全**：请求完成后自动销毁，不会造成内存泄漏

---

##### 模式2：单向推送（主→渲染）【主进程主动发消息】
###### 核心API
- 主进程推送：`mainWindow.webContents.send(通道名, 参数)`
- 渲染进程监听：`ipcRenderer.on(通道名, 回调函数)`
###### 适用场景
主进程**主动给渲染进程推送消息**，渲染进程被动接收，比如：
- 系统事件回调（托盘点击、USB设备插拔、电源状态变化、网络断开）
- 下载进度、文件上传进度实时推送
- 后台定时任务的结果推送（比如告警通知、数据同步）
- 跨窗口数据同步（主窗口给子窗口推送数据）

###### 完整代码实现
步骤1：主进程推送消息（electron/main.ts）

```typescript
import { app, BrowserWindow, ipcMain, Tray, Menu } from 'electron'
import path from 'path'

let mainWindow: BrowserWindow | null = null
let tray: Tray | null = null

// 注册主进程推送逻辑
app.whenReady().then(() => {
  createWindow()
  createTray()

  // 场景1：定时推送系统时间，每秒推送一次
  setInterval(() => {
    // 必须判断窗口是否存在、是否已经加载完成，否则消息会丢失
    if (mainWindow && !mainWindow.isDestroyed()) {
      mainWindow.webContents.send('system:time-update', new Date().toLocaleString())
    }
  }, 1000)

  // 场景2：托盘点击时，推送消息给渲染进程
  tray.on('click', () => {
    if (mainWindow) {
      mainWindow.show()
      mainWindow.webContents.send('tray:click', '用户点击了系统托盘')
    }
  })
})

// 创建系统托盘
function createTray() {
  const iconPath = path.join(__dirname, '../../assets/icon.png')
  tray = new Tray(iconPath)
  const contextMenu = Menu.buildFromTemplate([
    { label: '显示窗口', click: () => mainWindow?.show() },
    { label: '退出', click: () => app.quit() }
  ])
  tray.setContextMenu(contextMenu)
}

// 窗口创建函数（和之前一致，省略重复代码）
function createWindow() {
  mainWindow = new BrowserWindow({
    width: 1200,
    height: 800,
    webPreferences: {
      contextIsolation: true,
      nodeIntegration: false,
      preload: path.join(__dirname, '../preload/preload.js')
    }
  })
  if (process.env.VITE_DEV_SERVER_URL) {
    mainWindow.loadURL(process.env.VITE_DEV_SERVER_URL)
  } else {
    mainWindow.loadFile(path.join(__dirname, '../index.html'))
  }
}
```

步骤2：预加载脚本暴露监听方法（electron/preload.ts）

```typescript
import { contextBridge, ipcRenderer } from 'electron'

contextBridge.exposeInMainWorld('electronAPI', {
  // 之前的file API保留
  file: {
    open: (filters: any[]) => ipcRenderer.invoke('file:open', filters),
    write: (params: { filePath: string; content: string }) => ipcRenderer.invoke('file:write', params)
  },
  // 新增：主进程消息监听
  on: (channel: string, callback: (...args: any[]) => void) => {
    // 白名单校验，只允许监听指定通道，防止恶意监听
    const validChannels = ['system:time-update', 'tray:click']
    if (validChannels.includes(channel)) {
      // 封装回调，剥离event对象，只给渲染进程传递参数
      const subscription = (_event: any, ...args: any[]) => callback(...args)
      ipcRenderer.on(channel, subscription)

      // 返回取消监听的函数，防止内存泄漏
      return () => ipcRenderer.removeListener(channel, subscription)
    }
    throw new Error(`不允许监听通道：${channel}`)
  }
})

// 补充类型声明
declare global {
  interface Window {
    electronAPI: {
      file: {
        open: (filters: any[]) => Promise<{ canceled: boolean; path?: string; content?: string }>
        write: (params: { filePath: string; content: string }) => Promise<{ success: boolean; error?: string }>
      }
      on: (channel: string, callback: (...args: any[]) => void) => () => void
    }
  }
}
```

步骤3：Vue 3 组件中监听（src/App.vue）

**重点：必须在组件卸载时取消监听，否则会造成内存泄漏、重复触发回调**
```vue
<template>
  <div>
    <div>当前系统时间：{{ currentTime }}</div>
    <div>托盘事件通知：{{ trayNotice }}</div>
  </div>
</template>

<script setup lang="ts">
import { ref, onUnmounted } from 'vue'
import { ElNotification } from 'element-plus'

const currentTime = ref<string>('')
const trayNotice = ref<string>('')

// 监听系统时间更新
const unSubscribeTime = window.electronAPI.on('system:time-update', (time) => {
  currentTime.value = time
})

// 监听托盘点击事件
const unSubscribeTray = window.electronAPI.on('tray:click', (msg) => {
  trayNotice.value = msg
  ElNotification.info({ title: '托盘通知', message: msg })
})

// 【重中之重】组件卸载时，取消所有监听，释放内存
onUnmounted(() => {
  unSubscribeTime()
  unSubscribeTray()
})
</script>
```

---

##### 模式3：单向通知（渲染→主，无需返回）
###### 核心API
- 渲染进程发送：`ipcRenderer.send(通道名, 参数)`
- 主进程监听：`ipcMain.on(通道名, 回调函数)`
###### 适用场景
渲染进程**通知主进程执行操作，不需要返回结果**，比如：
- 窗口控制（最小化、关闭、置顶、全屏）
- 记录本地日志、上报埋点
- 触发系统通知、播放系统提示音
- 退出应用、重启应用

###### 极简代码实现
主进程监听（electron/main.ts）

```typescript
import { ipcMain, app } from 'electron'

// 窗口控制：最小化
ipcMain.on('window:minimize', (event) => {
  const win = BrowserWindow.fromWebContents(event.sender)
  win?.minimize()
})

// 窗口控制：关闭应用
ipcMain.on('window:close', () => {
  app.quit()
})

// 记录日志
ipcMain.on('log:info', (event, msg) => {
  console.log(`[渲染进程日志] ${new Date().toLocaleString()}: ${msg}`)
})
```

预加载脚本暴露（electron/preload.ts）

```typescript
contextBridge.exposeInMainWorld('electronAPI', {
  // 保留之前的API，新增窗口控制
  window: {
    minimize: () => ipcRenderer.send('window:minimize'),
    close: () => ipcRenderer.send('window:close')
  },
  log: {
    info: (msg: string) => ipcRenderer.send('log:info', msg)
  }
})
```

Vue组件调用

```vue
<template>
  <div class="window-control">
    <el-button @click="window.electronAPI.window.minimize()">最小化</el-button>
    <el-button type="danger" @click="window.electronAPI.window.close()">关闭应用</el-button>
  </div>
</template>
```

---

#### 三、IPC通信安全最佳实践（新手必看，避坑核心）
1. **永远开启上下文隔离，禁用渲染进程Node.js集成**
   这是Electron官方的首要安全建议，哪怕开发图方便，也绝对不要关闭，否则XSS漏洞会直接导致系统被控制。

2. **严格的通道白名单机制**
   预加载脚本中，必须对所有IPC通道做白名单校验，禁止使用通配符、禁止直接暴露`ipcRenderer.on/invoke/send`等原生方法，只暴露封装好的、明确的业务API。

3. **主进程必须校验所有入参**
   永远不要相信渲染进程传来的参数，所有入参必须做类型、格式、范围校验，防止路径遍历攻击、命令注入攻击。
   ❌ 错误示例：直接用渲染进程传来的路径执行`fs.readFile`
   ✅ 正确示例：校验路径是否在允许的目录内，校验文件后缀是否合法

4. **必须销毁监听，防止内存泄漏**
   所有`ipcRenderer.on`监听，必须在组件卸载时调用`removeListener`取消监听，否则会出现重复触发回调、内存持续上涨的问题。

5. **禁止在渲染进程处理敏感逻辑**
   所有涉及文件操作、系统API、密钥、数据库的敏感逻辑，必须放在主进程处理，渲染进程只负责发起请求和渲染结果，绝对不要把密钥、数据库连接放在Vue代码里。

---

#### 四、高频踩坑与解决方案
| 常见问题                                                     | 根本原因                                                  | 解决方案                                                     |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| 渲染进程报错 `ipcRenderer is undefined`                      | 关闭了上下文隔离，或直接在渲染进程import electron         | 必须通过preload+contextBridge暴露API，禁止在Vue中直接引入electron |
| 回调函数重复触发多次                                         | 组件多次挂载时，重复注册on监听，没有销毁                  | 组件卸载时必须调用removeListener取消监听，或使用ipcRenderer.once单次监听 |
| 主进程send消息，渲染进程收不到                               | 消息发送时，窗口还没创建/还没加载完成                     | 发送前判断`win && !win.isDestroyed()`，或监听`did-finish-load`事件后再发送 |
| ipcMain.handle报错 `Attempted to register a second handler for xxx` | 同一个通道注册了多次handle                                | 确保handle只在app.whenReady()之前注册一次，不要在循环/事件里重复注册 |
| 渲染进程invoke报错 `An object could not be cloned`           | 传递了无法序列化的参数（比如函数、DOM元素、循环引用对象） | IPC通信使用结构化克隆算法，只能传递可序列化的JSON数据，禁止传递函数、DOM |

---

#### 五、进阶优化：TypeScript全链路类型安全
针对你的TypeScript项目，我们可以给IPC通信做全链路类型约束，彻底避免写错通道名、传错参数的问题，开发体验拉满。

##### 步骤1：定义全局IPC类型文件（types/ipc.d.ts）
```typescript
// 定义所有IPC通道的类型约束
export interface IPCChannels {
  // 双向通信通道：[入参类型, 返回值类型]
  'file:open': [filters: { name: string; extensions: string[] }[], { canceled: boolean; path?: string; content?: string }]
  'file:write': [params: { filePath: string; content: string }, { success: boolean; error?: string }]
  // 单向推送通道：[参数类型]
  'system:time-update': [time: string]
  'tray:click': [msg: string]
  // 单向通知通道：[参数类型]
  'window:minimize': []
  'window:close': []
  'log:info': [msg: string]
}
```

##### 步骤2：预加载脚本类型约束
```typescript
import { contextBridge, ipcRenderer, IpcRendererEvent } from 'electron'
import type { IPCChannels } from '../types/ipc.d'

// 类型安全的invoke封装
const invoke = <T extends keyof IPCChannels>(
  channel: T,
  ...args: IPCChannels[T][0] extends never ? [] : [IPCChannels[T][0]]
): Promise<IPCChannels[T][1]> => {
  return ipcRenderer.invoke(channel, ...args)
}

// 类型安全的on封装
const on = <T extends keyof IPCChannels>(
  channel: T,
  callback: (args: IPCChannels[T][0]) => void
) => {
  const validChannels: (keyof IPCChannels)[] = ['system:time-update', 'tray:click']
  if (!validChannels.includes(channel)) throw new Error(`非法通道：${channel}`)

  const subscription = (_event: IpcRendererEvent, args: IPCChannels[T][0]) => callback(args)
  ipcRenderer.on(channel, subscription)
  return () => ipcRenderer.removeListener(channel, subscription)
}

// 类型安全的send封装
const send = <T extends keyof IPCChannels>(
  channel: T,
  ...args: IPCChannels[T][0] extends never ? [] : [IPCChannels[T][0]]
) => {
  ipcRenderer.send(channel, ...args)
}

// 暴露API
contextBridge.exposeInMainWorld('electronAPI', {
  invoke,
  on,
  send
})

// 全局类型声明
declare global {
  interface Window {
    electronAPI: {
      invoke: <T extends keyof IPCChannels>(
        channel: T,
        ...args: IPCChannels[T][0] extends never ? [] : [IPCChannels[T][0]]
      ) => Promise<IPCChannels[T][1]>
      on: <T extends keyof IPCChannels>(
        channel: T,
        callback: (args: IPCChannels[T][0]) => void
      ) => () => void
      send: <T extends keyof IPCChannels>(
        channel: T,
        ...args: IPCChannels[T][0] extends never ? [] : [IPCChannels[T][0]]
      ) => void
    }
  }
}
```

##### 效果
Vue组件中调用时，会自动提示通道名、参数类型、返回值类型，写错通道名、传错参数会直接报TS错误，彻底杜绝低级错误。
### 应用全局菜单（Menu）

Electron的Menu模块可创建原生应用菜单，支持Windows、macOS、Linux系统适配。

```javascript
const { app, Menu } = require('electron');

// 菜单模板
const template = [
  // macOS 应用菜单（必填，否则无菜单）
  ...(process.platform === 'darwin' ? [{
    label: app.name,
    submenu: [
      { role: 'about', label: '关于' },
      { type: 'separator' },
      { role: 'services', label: '服务' },
      { type: 'separator' },
      { role: 'hide', label: '隐藏' },
      { role: 'hideOthers', label: '隐藏其他' },
      { role: 'unhide', label: '显示全部' },
      { type: 'separator' },
      { role: 'quit', label: '退出' }
    ]
  }] : []),
  // 文件菜单
  {
    label: '文件',
    submenu: [
      {
        label: '新建文件',
        accelerator: 'CmdOrCtrl+N', // 快捷键
        click: () => console.log('点击新建文件')
      },
      { type: 'separator' },
      process.platform === 'darwin' 
        ? { role: 'close', label: '关闭' }
        : { role: 'quit', label: '退出' }
    ]
  },
  // 编辑菜单
  {
    label: '编辑',
    submenu: [
      { role: 'undo', label: '撤销' },
      { role: 'redo', label: '重做' },
      { type: 'separator' },
      { role: 'cut', label: '剪切' },
      { role: 'copy', label: '复制' },
      { role: 'paste', label: '粘贴' }
    ]
  },
  // 视图菜单
  {
    label: '视图',
    submenu: [
      { role: 'reload', label: '刷新' },
      { role: 'forceReload', label: '强制刷新' },
      { role: 'toggleDevTools', label: '开发者工具' },
      { type: 'separator' },
      { role: 'resetZoom', label: '重置缩放' },
      { role: 'zoomIn', label: '放大' },
      { role: 'zoomOut', label: '缩小' },
      { type: 'separator' },
      { role: 'togglefullscreen', label: '全屏' }
    ]
  }
];

// 构建菜单并设置为应用菜单
const menu = Menu.buildFromTemplate(template);
Menu.setApplicationMenu(menu);
```

#### 右键上下文菜单

```javascript
// 主进程中
const { Menu, ipcMain } = require('electron');

// 构建右键菜单模板
const contextMenuTemplate = [
  { role: 'copy', label: '复制' },
  { role: 'paste', label: '粘贴' },
  { type: 'separator' },
  {
    label: '自定义操作',
    click: () => console.log('执行自定义操作')
  }
];
const contextMenu = Menu.buildFromTemplate(contextMenuTemplate);

// 监听渲染进程的右键事件
ipcMain.on('context-menu:show', (event) => {
  // 在当前窗口弹出菜单
  contextMenu.popup({ window: BrowserWindow.fromWebContents(event.sender) });
});
```

```javascript
// preload.js 暴露方法
contextBridge.exposeInMainWorld('menuAPI', {
  showContextMenu: () => ipcRenderer.send('context-menu:show')
});
```

```html
<!-- 渲染进程调用 -->
<div oncontextmenu="window.menuAPI.showContextMenu(); return false;">
  右键点击此处弹出菜单
</div>
```
### 剪贴板操作（clipboard）

支持主进程和渲染进程（通过preload暴露）调用，可读写系统剪贴板的文本、图片、文件。

```javascript
// 主进程中
const { clipboard, ipcMain, nativeImage } = require('electron');

ipcMain.handle('clipboard:writeText', (event, text) => {
  clipboard.writeText(text);
  return true;
});

ipcMain.handle('clipboard:readText', () => {
  return clipboard.readText();
});

// 图片读写
ipcMain.handle('clipboard:writeImage', (event, imagePath) => {
  const image = nativeImage.createFromPath(imagePath);
  clipboard.writeImage(image);
  return true;
});

ipcMain.handle('clipboard:readImage', () => {
  const image = clipboard.readImage();
  return image.toDataURL(); // 返回base64图片
});
```

### 外部链接与文件打开（shell）

shell模块用于调用系统默认程序打开外部链接、文件、文件夹，避免直接在Electron窗口中打开外部链接带来的安全风险。

```javascript
const { shell, ipcMain } = require('electron');

// 1. 用系统默认浏览器打开外部链接
ipcMain.handle('shell:openExternal', async (event, url) => {
  // 安全校验：仅允许https协议
  if (!url.startsWith('https://')) throw new Error('仅支持https链接');
  await shell.openExternal(url);
  return true;
});

// 2. 用系统默认程序打开文件
ipcMain.handle('shell:openFile', async (event, filePath) => {
  await shell.openPath(filePath);
  return true;
});

// 3. 在资源管理器/访达中打开文件所在文件夹
ipcMain.handle('shell:showInFolder', async (event, filePath) => {
  shell.showItemInFolder(filePath);
  return true;
});

// 4. 放入回收站
ipcMain.handle('shell:trashItem', async (event, filePath) => {
  await shell.trashItem(filePath);
  return true;
});
```
### 本地数据存储

Electron应用常用的本地数据存储方案，覆盖不同业务场景：

|存储方案|适用场景|优势|限制|
|---|---|---|---|
|localStorage/sessionStorage|渲染进程小数据量存储（用户配置、UI状态）|原生API，无需依赖，使用简单|仅渲染进程可用，容量上限5MB，主进程无法访问，应用卸载后丢失|
|electron-store|全应用配置文件、持久化数据|支持主进程/渲染进程，API简单，支持加密，无容量限制|不适合大数据量复杂查询|
|IndexedDB|渲染进程大数据量、复杂查询存储|浏览器原生API，支持事务、索引，大容量存储|仅渲染进程可用，主进程无法直接访问|
|better-sqlite3|全应用大数据量、复杂关系型数据存储|完整SQLite能力，高性能，支持主进程/渲染进程|需要处理原生模块编译，跨平台适配|
|Node.js fs模块|自定义文件格式、大文件存储|完全自定义，无限制|需要自行处理文件读写、路径、并发问题|

#### 1. electron-store 推荐用法

最常用的持久化存储方案，安装与使用：

```bash
# 安装依赖
npm install electron-store
```

```javascript
// 主进程中使用
const Store = require('electron-store');
// 初始化存储实例
const store = new Store({
  name: 'config', // 文件名，默认config
  cwd: app.getPath('userData'), // 存储路径，默认应用数据目录
  encryptionKey: 'your-encryption-key', // 加密密钥，敏感数据推荐开启
  defaults: { // 默认配置
    window: {
      width: 1200,
      height: 800
    },
    userConfig: {
      theme: 'light',
      autoStart: false
    }
  }
});

// 常用操作
store.set('userConfig.theme', 'dark'); // 设置值
const theme = store.get('userConfig.theme'); // 获取值
store.delete('userConfig.autoStart'); // 删除值
store.has('userConfig.theme'); // 判断是否存在
store.clear(); // 清空所有数据
```

#### 2. 渲染进程使用 electron-store

两种方案：

1. 主进程通过IPC封装存储方法，渲染进程通过IPC调用（推荐，安全）
2. 渲染进程直接引入，需在preload中暴露，或关闭沙箱（不推荐，有安全风险）

## 内容安全策略（CSP）
内容安全策略（Content Security Policy，简称CSP）是W3C制定的前端安全标准，核心通过**白名单机制**限制浏览器加载、执行的资源与行为，是目前防御XSS跨站脚本攻击、点击劫持、恶意资源注入、数据泄露等前端安全威胁的最有效手段之一。

核心工作原理

Web服务器通过HTTP响应头（或HTML的meta标签）向浏览器传递CSP策略，浏览器接收到策略后，严格按照白名单规则校验所有资源加载、脚本执行、样式渲染、页面嵌入、网络请求等行为；凡是不符合规则的行为，会被浏览器直接拦截，并可选择向服务器发送违规报告。

### 两种核心运行模式
1.  **强制模式（Enforce Mode）**：对应HTTP头`Content-Security-Policy`，浏览器严格执行策略，拦截所有违规行为，是生产环境的标准部署模式。
2.  **报告模式（Report-Only Mode）**：对应HTTP头`Content-Security-Policy-Report-Only`，浏览器不拦截违规行为，仅发送违规报告，用于策略调试、验证和灰度上线，不会影响网站正常运行。

### CSP 核心语法与基础规则
#### 核心语法结构
CSP策略由**指令集**组成，单条策略的语法格式为：
```
[指令名称] [源值1] [源值2] [源值3];
```
- 完整的CSP策略由多条`指令+源值`的组合构成，不同指令之间用英文分号`;`分隔。
- 单条指令内，指令名称在前，多个源值之间用空格分隔。
- 指令名称和源值大小写不敏感，行业惯例统一使用小写。
- 未单独定义的获取源类指令，默认继承`default-src`的兜底规则。
- 同名称指令重复定义时，仅第一条生效，后续重复指令会被浏览器直接忽略。

#### 两种部署方式的语法规范
##### 1. HTTP响应头部署（推荐）
CSP的标准部署方式，支持所有CSP指令，无功能限制。
- 强制模式示例：
```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn.example.com;
```
- 报告模式示例：
```http
Content-Security-Policy-Report-Only: default-src 'self'; script-src 'self'; report-uri /csp-report-endpoint;
```

#### 2. HTML meta标签部署
通过HTML的`<meta>`标签在页面内嵌入策略，语法如下：
```html
<meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self';">
```
**强制遵守的关键限制**：
- meta标签仅支持部分CSP指令，**禁止使用**以下指令：`frame-ancestors`、`report-uri`、`report-to`、`block-all-mixed-content`、`upgrade-insecure-requests`。
- 必须放在HTML的`<head>`标签最顶部，越早加载越好，否则无法对早于meta标签加载的资源生效。
- 单页面内多个meta标签的CSP策略，执行**交集规则**，资源必须同时满足所有策略才会被放行。

#### 多条策略的合并规则
当浏览器接收到多个CSP策略（多个HTTP响应头、多个meta标签、响应头+meta标签组合），会执行**严格交集规则**：
- 所有策略的限制都会生效，资源必须同时满足所有策略的规则，才会被允许加载/执行。
- 多个策略叠加只会让限制更严格，不会放宽任何规则。
- 示例：第一个策略设置`script-src 'self'`，第二个策略设置`script-src https://cdn.example.com`，最终生效规则为`script-src 'none'`（无交集），所有脚本都会被拦截。

### CSP 标准指令全集与详细说明
#### 一、获取源指令（Fetch Directives）
获取源指令是CSP最核心的指令集，用于控制浏览器可加载的各类资源来源，未单独定义的获取源指令，默认继承`default-src`的规则。

##### 1. default-src
- **作用**：所有获取源指令的默认兜底指令，当某类资源的专属获取源指令未定义时，浏览器会使用该指令的规则。
- **语法**：`default-src <源值列表>;`
- **示例**：
```http
# 仅允许加载当前域名下的所有资源
default-src 'self';
```
- **注意事项**：仅对获取源指令生效，对文档指令、导航指令、报告指令等非获取源指令无兜底作用。

##### 2. script-src
- **作用**：控制JavaScript脚本的加载与执行来源，包括内联脚本、外部脚本、eval执行、动态脚本加载等，是防御XSS攻击的核心指令。
- **语法**：`script-src <源值列表>;`
- **示例**：
```http
# 允许当前域名、cdn.example.com的脚本，以及符合nonce值的内联脚本、信任脚本动态加载的子脚本
script-src 'self' https://cdn.example.com 'nonce-2726c7f26c' 'strict-dynamic';
```
- **关键说明**：
  - 允许内联脚本必须使用`nonce-*`、`hash-*`或`'unsafe-inline'`，其中`'unsafe-inline'`会大幅降低安全性，仅作为兜底方案。
  - 允许`eval()`、`new Function()`等动态代码执行，必须添加`'unsafe-eval'`，非必要严禁使用。
  - `'strict-dynamic'`会让信任的根脚本加载的子脚本自动获得信任，无需额外添加源白名单，是现代CSP的核心安全配置。

##### 3. style-src
- **作用**：控制CSS样式表的加载与执行来源，包括外部样式文件、内联样式、`<style>`标签。
- **语法**：`style-src <源值列表>;`
- **示例**：
```http
# 允许当前域名、谷歌字体CDN的样式，以及内联样式
style-src 'self' https://fonts.googleapis.com 'unsafe-inline';
```
- **注意事项**：
  - 主流前端框架（Vue/React/Angular）会生成内联样式，通常需要添加`'unsafe-inline'`，也可通过哈希/nonce方式替代。
  - 元素的`style`属性属于内联样式，受该指令控制。

##### 4. img-src
- **作用**：控制图片、favicon图标、SVG文件的加载来源。
- **语法**：`img-src <源值列表>;`
- **示例**：
```http
# 允许当前域名、data URI、图片CDN的图片资源
img-src 'self' data: https://img.example.com;
```
- **注意事项**：`data:`常用于小图片的base64编码，若页面使用base64图片，必须添加该源值。

##### 5. font-src
- **作用**：控制网页字体文件的加载来源。
- **语法**：`font-src <源值列表>;`
- **示例**：
```http
# 允许当前域名、谷歌字体静态CDN的字体文件
font-src 'self' https://fonts.gstatic.com;
```

##### 6. connect-src
- **作用**：控制网络请求的连接来源，包括Fetch API、XMLHttpRequest、WebSocket、EventSource、Navigator.sendBeacon()等。
- **语法**：`connect-src <源值列表>;`
- **示例**：
```http
# 允许当前域名、后端API接口、WebSocket连接、统计服务
connect-src 'self' https://api.example.com wss://ws.example.com https://www.google-analytics.com;
```
- **注意事项**：前后端分离的SPA应用，必须将后端API域名添加到该指令中。

##### 7. media-src
- **作用**：控制音频、视频等媒体资源的加载来源，包括``、`<video>`、`<track>`标签。
- **语法**：`media-src <源值列表>;`
- **示例**：
```http
# 仅允许当前域名和媒体CDN的音视频资源
media-src 'self' https://media.example.com;
```

##### 8. object-src
- **作用**：控制插件资源的加载来源，包括`<object>`、`<embed>`、`<applet>`标签，对应Flash、Java Applet、ActiveX等插件。
- **语法**：`object-src <源值列表>;`
- **最佳实践**：现代网页几乎不再使用此类插件，建议统一设置为`object-src 'none';`，彻底禁用插件，消除相关安全风险。
- **示例**：
```http
object-src 'none';
```

##### 9. frame-src
- **作用**：控制`<iframe>`标签加载的内嵌页面来源。
- **语法**：`frame-src <源值列表>;`
- **示例**：
```http
# 仅允许当前域名和支付平台的iframe嵌入
frame-src 'self' https://pay.example.com;
```
- **注意事项**：CSP3标准中，该指令替代了废弃的`child-src`指令，专门用于控制iframe资源。

##### 10. worker-src
- **作用**：控制Web Worker、Service Worker、Shared Worker脚本的加载来源。
- **语法**：`worker-src <源值列表>;`
- **示例**：
```http
# 仅允许当前域名的Worker脚本
worker-src 'self';
```
- **注意事项**：CSP3标准中，该指令替代了废弃的`child-src`指令，专门用于控制Worker资源。

##### 11. manifest-src
- **作用**：控制Web应用清单文件（manifest.json）的加载来源。
- **语法**：`manifest-src <源值列表>;`
- **示例**：
```http
manifest-src 'self';
```

##### 12. prefetch-src
- **作用**：控制预加载、预取资源的加载来源，包括`<link rel="prefetch">`、`<link rel="preload">`、`<link rel="prerender">`等。
- **语法**：`prefetch-src <源值列表>;`
- **示例**：
```http
prefetch-src 'self' https://cdn.example.com;
```

#### 二、文档指令（Document Directives）
文档指令用于控制当前文档的全局属性与行为，不继承`default-src`的规则。

##### 1. base-uri
- **作用**：控制`<base>`标签可以使用的URL来源，防止攻击者修改`<base>`标签，将页面内的相对路径资源重定向到恶意域名。
- **语法**：`base-uri <源值列表>;`
- **最佳实践**：建议设置为`base-uri 'self';`，仅允许当前域名，或`base-uri 'none';`彻底禁用`<base>`标签。
- **示例**：
```http
base-uri 'self';
```

##### 2. sandbox
- **作用**：为当前页面启用沙箱模式，限制页面的权限与行为，功能与iframe的`sandbox`属性一致。
- **语法**：`sandbox <可选的权限值列表>;`
- **可选权限值**：`allow-forms`（允许表单提交）、`allow-scripts`（允许脚本执行）、`allow-same-origin`（允许同域请求）、`allow-popups`（允许弹出窗口）、`allow-top-navigation`（允许导航到顶级窗口）等。
- **示例**：
```http
# 启用沙箱，仅允许表单提交和脚本执行
sandbox allow-forms allow-scripts;
```
- **注意事项**：若不添加任何权限值，会启用最严格的沙箱模式，禁用所有页面行为。

#### 三、导航指令（Navigation Directives）
导航指令用于控制页面的导航、跳转、嵌入行为，不继承`default-src`的规则。

##### 1. form-action
- **作用**：控制表单可以提交的目标URL地址，防止XSS攻击将表单数据提交到恶意域名。
- **语法**：`form-action <源值列表>;`
- **最佳实践**：建议设置为`form-action 'self';`，仅允许表单提交到当前域名，或添加可信的第三方支付/表单服务地址。
- **示例**：
```http
form-action 'self' https://pay.example.com;
```

##### 2. frame-ancestors
- **作用**：控制当前页面可以被哪些域名的页面通过`<iframe>`、`<frame>`、`<object>`、`<embed>`等标签嵌入，是防御点击劫持攻击的核心指令，替代了废弃的`X-Frame-Options`响应头。
- **语法**：`frame-ancestors <源值列表>;`
- **示例**：
```http
# 彻底禁止所有页面嵌入当前页面
frame-ancestors 'none';
# 仅允许当前域名嵌入
frame-ancestors 'self';
# 允许指定域名嵌入
frame-ancestors 'self' https://partner.example.com;
```
- **关键限制**：该指令**仅支持HTTP响应头部署**，meta标签中设置无效。
- **注意事项**：该指令不继承`default-src`的规则，若未定义，默认允许所有域名嵌入。

##### 3. navigate-to
- **作用**：控制当前页面可以导航（跳转）到的URL地址，包括`<a>`标签跳转、`window.location`跳转、表单提交跳转、`window.open`弹出窗口等。
- **语法**：`navigate-to <源值列表>;`
- **示例**：
```http
# 仅允许跳转到当前域名和可信的官方域名
navigate-to 'self' https://www.example.com;
```
- **注意事项**：该指令为CSP3新增指令，部分旧版浏览器不支持，生产环境需做好兼容性测试。

#### 四、报告指令（Reporting Directives）
报告指令用于配置CSP违规行为的报告接收地址，不继承`default-src`的规则。

##### 1. report-uri
- **作用**：指定接收CSP违规报告的URL地址，浏览器检测到违规行为时，会向该地址发送POST格式的JSON违规报告。
- **语法**：`report-uri <报告接收地址>;`
- **示例**：
```http
# 配置违规报告接收接口
report-uri /api/csp-report;
# 也可配置跨域的报告接收地址
report-uri https://report.example.com/csp-report;
```
- **注意事项**：
  - 该指令在CSP3标准中被标记为废弃，被`report-to`指令替代，但目前所有主流浏览器均保持兼容，是生产环境的主流方案。
  - 该指令**仅支持HTTP响应头部署**，meta标签中设置无效。

##### 2. report-to
- **作用**：CSP3标准新增的报告指令，通过`Reporting-Endpoints`响应头定义的报告组，配置违规报告的接收地址，支持批量报告、缓存配置等高级功能。
- **语法**：`report-to <报告组名称>;`
- **完整配置示例**：
```http
# 先定义报告端点
Reporting-Endpoints: csp-endpoint="https://report.example.com/csp-report"
# 再在CSP中引用报告组
Content-Security-Policy: default-src 'self'; report-to csp-endpoint;
```
- **注意事项**：
  - 该指令**仅支持HTTP响应头部署**，meta标签中设置无效。
  - 部分旧版浏览器不支持该指令，生产环境建议同时配置`report-uri`和`report-to`，实现全浏览器兼容。

#### 五、特殊功能指令
这类指令用于实现特定的安全功能，无额外源值配置，不继承`default-src`的规则。

##### 1. upgrade-insecure-requests
- **作用**：自动将页面内所有HTTP的资源请求升级为HTTPS请求，无需修改页面代码，解决HTTPS网站中的混合内容问题。
- **语法**：`upgrade-insecure-requests;`
- **示例**：
```http
Content-Security-Policy: upgrade-insecure-requests; default-src 'self';
```
- **注意事项**：
  - 该指令**仅支持HTTP响应头部署**，meta标签中设置无效。
  - 若升级后的HTTPS资源无法访问，浏览器会直接拦截该请求，不会回退到HTTP。

##### 2. block-all-mixed-content
- **作用**：彻底拦截HTTPS页面中所有的HTTP资源请求（混合内容），包括主动混合内容和被动混合内容。
- **语法**：`block-all-mixed-content;`
- **注意事项**：
  - 该指令在CSP3标准中被标记为废弃，推荐使用`upgrade-insecure-requests`替代。
  - 该指令**仅支持HTTP响应头部署**，meta标签中设置无效。

### CSP 源值（Source Expression）完整规范
源值是CSP指令的核心配置项，用于定义白名单的匹配规则，所有获取源指令、导航指令、文档指令均支持以下源值类型，按安全优先级从高到低排序。

#### 一、特殊关键字源值
所有特殊关键字必须用英文单引号`'`包裹，否则会被浏览器识别为域名。

##### 1. 'none'
- **作用**：彻底禁用该指令对应的所有资源/行为，不允许任何来源。
- **适用场景**：`object-src 'none'`、`frame-ancestors 'none'`等无需使用的资源类型。
- **示例**：
```http
object-src 'none';
```

##### 2. 'self'
- **作用**：匹配与当前文档**同协议、同域名、同端口**的来源，是最常用的基础源值。
- **注意事项**：
  - 不匹配不同子域名、不同端口、不同协议的来源。
  - 不匹配当前域名的上级域名或下级子域名。
- **示例**：
```http
default-src 'self';
```

##### 3. 'strict-dynamic'
- **作用**：CSP3新增的核心安全关键字，让通过`nonce-*`或`hash-*`信任的根脚本，其动态加载的子脚本自动获得信任，无需额外添加源白名单。
- **核心价值**：彻底解决大型项目中第三方脚本、动态脚本的白名单配置难题，同时大幅提升XSS防护能力。
- **注意事项**：
  - 仅在`script-src`指令中生效。
  - 必须配合`nonce-*`或`hash-*`使用，单独使用无效。
  - 兼容浏览器会忽略该关键字后的所有域名白名单、`'unsafe-inline'`，仅保留`nonce/hash`和`'strict-dynamic'`规则。
- **示例**：
```http
script-src 'nonce-2726c7f26c' 'strict-dynamic';
```

##### 4. 'unsafe-inline'
- **作用**：允许内联脚本、内联样式的执行，是CSP中最常用的降级方案，但会大幅降低XSS防护能力。
- **注意事项**：
  - 当`script-src`或`style-src`中配置了`nonce-*`或`hash-*`时，现代浏览器会自动忽略`'unsafe-inline'`，仅老版本浏览器会生效。
  - 非必要严禁使用，优先使用`nonce-*`或`hash-*`替代内联资源。
- **示例**：
```http
style-src 'self' 'unsafe-inline';
```

##### 5. 'unsafe-eval'
- **作用**：允许`eval()`、`new Function()`、`setTimeout(string)`、`setInterval(string)`等动态代码执行功能。
- **注意事项**：该关键字会彻底打开动态代码执行的大门，是XSS攻击的高风险入口，非必要严禁使用。
- **示例**：
```http
script-src 'self' 'unsafe-eval';
```

##### 6. 'unsafe-hashes'
- **作用**：允许通过哈希值信任事件处理函数（如`onclick`、`onload`）、javascript:URL等内联事件脚本，普通的内联`<script>`标签无需该关键字。
- **注意事项**：该关键字为CSP3新增，仅配合哈希值使用，单独使用无效。
- **示例**：
```http
script-src 'sha256-abc123def456' 'unsafe-hashes';
```

#### 二、加密验证源值
这类源值通过加密算法验证脚本/样式的完整性，是替代`'unsafe-inline'`的安全方案，仅在`script-src`和`style-src`指令中生效。

##### 1. nonce-* 随机数源值
- **语法**：`'nonce-<base64编码的随机字符串>'`
- **作用**：为每次HTTP请求生成唯一的随机数，只有携带对应`nonce`属性的内联脚本/外部脚本才会被执行。
- **核心规则**：
  - 随机数必须每次HTTP请求重新生成，**严禁复用**，否则会失去防XSS能力。
  - 随机数长度至少128位（16字节），使用安全的随机数生成器（如CSPRNG）生成，base64编码。
  - 内联脚本必须添加对应的`nonce`属性，与CSP策略中的值完全一致。
- **完整示例**：
  - HTTP响应头：
  ```http
  Content-Security-Policy: script-src 'nonce-OTdmOWI4ZTM4NTY1M2M4OTZh' 'strict-dynamic';
  ```
  - HTML页面内的脚本：
  ```html
  <script nonce="OTdmOWI4ZTM4NTY1M2M4OTZh">
    // 合法的内联脚本，会被执行
  </script>
  ```

##### 2. hash-* 哈希值源值
- **语法**：`'<hash算法>-<base64编码的哈希值>'`
- **支持的哈希算法**：`sha256`、`sha384`、`sha512`
- **作用**：计算脚本/样式内容的哈希值，只有内容哈希完全匹配的内联脚本/样式才会被执行。
- **核心规则**：
  - 哈希值计算的是脚本/样式的**完整内容**，包括空格、换行，不包括`<script>/<style>`标签本身。
  - 脚本内容发生任何修改，哈希值都会变化，必须重新计算。
  - 优先使用`sha256`算法，兼容性最好。
- **哈希值计算方法**：
  - 方法1：浏览器开发者工具，当CSP拦截了内联脚本时，Console面板会直接输出该脚本对应的正确哈希值。
  - 方法2：使用OpenSSL命令行计算，示例：
  ```bash
  # 计算alert(1)的sha256哈希值
  echo -n "alert(1)" | openssl dgst -sha256 -binary | openssl base64
  ```
- **示例**：
```http
# 允许内容哈希匹配的内联脚本执行
script-src 'sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4=';
```

#### 三、主机与地址源值
这类源值通过域名、IP、协议、端口定义白名单规则，是最基础的白名单配置方式。

##### 1. 完整域名源值
- **语法**：`https://www.example.com`
- **作用**：精确匹配指定协议、域名、端口的来源，端口省略时默认匹配协议对应的标准端口（HTTPS=443，HTTP=80）。
- **示例**：
```http
script-src https://cdn.example.com https://www.google-analytics.com;
```

##### 2. 通配符域名源值
- **语法**：`https://*.example.com`
- **作用**：匹配指定主域名下的所有子域名，包括多级子域名。
- **注意事项**：
  - `*.example.com` 匹配 `a.example.com`、`b.a.example.com`，**不匹配根域名`example.com`**。
  - 通配符仅能用于域名的前缀，不能用于中间或后缀，如`www.*.com`是非法的。
- **示例**：
```http
img-src https://*.example.com;
```

##### 3. 协议源值
- **语法**：`https:`、`http:`、`wss:`、`data:`
- **作用**：匹配所有使用指定协议的来源。
- **注意事项**：
  - `https:` 允许所有HTTPS来源，范围极广，非必要严禁使用。
  - `data:` 允许base64编码的资源，常用于图片、字体，仅在`img-src`、`font-src`中按需使用，严禁在`script-src`中使用。
- **示例**：
```http
img-src 'self' data:;
```

##### 4. IP地址源值
- **语法**：`192.168.1.1`、`https://10.0.0.0/8`
- **作用**：匹配指定的IP地址或IP段，仅用于内网环境。
- **示例**：
```http
default-src 'self' 192.168.1.0/24;
```

##### 5. 端口源值
- **语法**：`localhost:3000`、`https://example.com:8080`
- **作用**：匹配指定端口的来源，端口必须显式指定，`'self'`不会自动匹配非标准端口。
- **示例**：
```http
connect-src 'self' localhost:3000;
```

### 不同场景的完整CSP配置示例
以下示例均为生产环境可用的完整配置，附带详细注释，可直接根据场景修改使用。

#### 场景1：基础严格安全配置（通用静态站点）
适用于纯静态网站，无第三方资源，无动态脚本，安全性最高。
```http
Content-Security-Policy:
  # 兜底规则，仅允许当前域名
  default-src 'self';
  # 脚本仅允许当前域名，禁用eval和内联脚本
  script-src 'self';
  # 样式仅允许当前域名，禁用内联样式
  style-src 'self';
  # 图片允许当前域名和base64
  img-src 'self' data:;
  # 字体仅允许当前域名
  font-src 'self';
  # 网络请求仅允许当前域名
  connect-src 'self';
  # 音视频仅允许当前域名
  media-src 'self';
  # 彻底禁用插件
  object-src 'none';
  # 彻底禁用iframe嵌入
  frame-src 'none';
  # 彻底禁用Worker
  worker-src 'none';
  # 禁止base标签修改
  base-uri 'self';
  # 表单仅允许提交到当前域名
  form-action 'self';
  # 彻底禁止所有页面嵌入当前页面
  frame-ancestors 'none';
  # 自动升级HTTP请求为HTTPS
  upgrade-insecure-requests;
  # 违规报告接收地址
  report-uri /api/csp-report;
```

#### 场景2：SPA单页应用配置（Vue/React/Angular）
适用于前后端分离的SPA应用，包含前端框架、API接口、第三方统计等资源。
```http
Content-Security-Policy:
  # 兜底规则
  default-src 'self';
  # 脚本：允许当前域名、CDN、nonce验证的内联脚本、动态加载的信任脚本
  script-src 'self' https://cdn.example.com 'nonce-{每次请求生成的随机数}' 'strict-dynamic';
  # 样式：允许当前域名、CDN、内联样式（前端框架必需）
  style-src 'self' https://cdn.example.com 'unsafe-inline';
  # 图片：允许当前域名、CDN、base64、OSS存储
  img-src 'self' https://cdn.example.com https://oss.example.com data:;
  # 字体：允许当前域名、CDN
  font-src 'self' https://cdn.example.com;
  # 网络请求：允许当前域名、后端API、WebSocket、统计服务
  connect-src 'self' https://api.example.com wss://ws.example.com https://www.google-analytics.com;
  # 禁用插件
  object-src 'none';
  # 禁用iframe，若需嵌入第三方页面可添加对应域名
  frame-src 'none';
  # 禁用Worker，若需使用可改为'self'
  worker-src 'none';
  # base标签限制
  base-uri 'self';
  # 表单提交限制
  form-action 'self';
  # 仅允许当前域名嵌入
  frame-ancestors 'self';
  # 自动升级HTTP请求
  upgrade-insecure-requests;
  # 违规报告地址
  report-uri /api/csp-report;
```

#### 场景3：仅报告模式配置（调试/灰度上线）
适用于新策略上线前的调试，不会拦截任何资源，仅收集违规报告，验证策略无问题后再切换为强制模式。
```http
Content-Security-Policy-Report-Only:
  default-src 'self';
  script-src 'self' https://cdn.example.com 'nonce-{随机数}' 'strict-dynamic';
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https://*.example.com;
  font-src 'self';
  connect-src 'self' https://api.example.com;
  object-src 'none';
  base-uri 'self';
  form-action 'self';
  frame-ancestors 'none';
  upgrade-insecure-requests;
  report-uri /api/csp-report;
```

#### 场景4：兼容第三方资源的通用配置
适用于包含谷歌分析、百度统计、第三方字体、支付接口等常见第三方服务的网站。
```http
Content-Security-Policy:
  default-src 'self';
  # 脚本：允许当前域名、CDN、谷歌分析、百度统计、nonce+strict-dynamic
  script-src 'self' https://cdn.example.com https://www.google-analytics.com https://hm.baidu.com 'nonce-{随机数}' 'strict-dynamic';
  # 样式：允许当前域名、谷歌字体CDN、内联样式
  style-src 'self' https://fonts.googleapis.com 'unsafe-inline';
  # 图片：允许当前域名、CDN、base64、谷歌分析、百度统计
  img-src 'self' https://cdn.example.com data: https://www.google-analytics.com https://hm.baidu.com;
  # 字体：允许当前域名、谷歌字体静态CDN
  font-src 'self' https://fonts.gstatic.com;
  # 网络请求：允许当前域名、API、谷歌分析、百度统计
  connect-src 'self' https://api.example.com https://www.google-analytics.com https://hm.baidu.com;
  # 禁用插件
  object-src 'none';
  # iframe：允许支付宝、微信支付嵌入
  frame-src 'self' https://pay.weixin.qq.com https://mclient.alipay.com;
  # base标签限制
  base-uri 'self';
  # 表单：允许提交到支付平台
  form-action 'self' https://pay.weixin.qq.com https://mclient.alipay.com;
  # 禁止嵌入
  frame-ancestors 'none';
  # 自动升级HTTPS
  upgrade-insecure-requests;
  # 违规报告
  report-uri /api/csp-report;
```

### CSP 部署方法
#### 一、主流Web服务器的CSP配置
##### 1. Nginx 配置
在Nginx的配置文件（nginx.conf）或站点配置文件的`server`块中，添加`add_header`指令：
```nginx
server {
    listen 443 ssl;
    server_name www.example.com;

    # 强制模式CSP配置
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'nonce-$request_id' 'strict-dynamic'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'none'; upgrade-insecure-requests; report-uri /api/csp-report;" always;

    # 其他站点配置...
}
```
- 注意：必须添加`always`参数，确保非200状态码的响应也会携带CSP头。
- 示例中使用`$request_id`作为nonce值，Nginx会为每次请求生成唯一的随机ID，无需手动生成。

##### 2. Apache 配置
在Apache的配置文件（httpd.conf）或`.htaccess`文件中，使用`Header`指令：
```apache
# 启用header模块
Header always set Content-Security-Policy "default-src 'self'; script-src 'self' 'nonce-%{UNIQUE_ID}e' 'strict-dynamic'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'none'; upgrade-insecure-requests; report-uri /api/csp-report;"
```
- 注意：必须启用`mod_headers`模块，否则配置不生效。
- 示例中使用`%{UNIQUE_ID}e`作为nonce值，Apache会为每次请求生成唯一的随机ID。

##### 3. Tomcat 配置
在Tomcat的`conf/web.xml`文件中，添加过滤器配置，或在项目的`web.xml`中配置：
```xml
<filter>
    <filter-name>ContentSecurityPolicyFilter</filter-name>
    <filter-class>org.apache.catalina.filters.HttpHeaderSecurityFilter</filter-class>
    <init-param>
        <param-name>contentSecurityPolicy</param-name>
        <param-value>default-src 'self'; script-src 'self' 'nonce-{请求生成的随机数}' 'strict-dynamic'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'none'; upgrade-insecure-requests; report-uri /api/csp-report;</param-value>
    </init-param>
    <init-param>
        <param-name>addContentSecurityPolicyHeader</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>ContentSecurityPolicyFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

##### 4. Node.js/Express 配置
在Express项目中，通过中间件设置CSP响应头，推荐使用官方的`helmet`中间件，简化配置：
```javascript
// 安装helmet：npm install helmet
const express = require('express');
const helmet = require('helmet');
const app = express();

// 配置CSP
app.use(
  helmet.contentSecurityPolicy({
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", "https://cdn.example.com", "'strict-dynamic'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", "data:", "https://*.example.com"],
      fontSrc: ["'self'"],
      connectSrc: ["'self'", "https://api.example.com"],
      objectSrc: ["'none'"],
      baseUri: ["'self'"],
      formAction: ["'self'"],
      frameAncestors: ["'none'"],
      upgradeInsecureRequests: [],
      reportUri: "/api/csp-report"
    },
    useDefaults: false,
    reportOnly: false // 设为true时启用报告模式
  })
);

app.listen(3000);
```

#### 二、meta标签部署方法
仅适用于无服务器配置权限的场景，必须放在HTML的`<head>`标签最顶部，确保优先加载：
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <!-- CSP策略必须放在最顶部，越早加载越好 -->
  <meta http-equiv="Content-Security-Policy" content="
    default-src 'self';
    script-src 'self' https://cdn.example.com 'nonce-OTdmOWI4ZTM4NTY1M2M4OTZh' 'strict-dynamic';
    style-src 'self' 'unsafe-inline';
    img-src 'self' data:;
    object-src 'none';
    base-uri 'self';
    form-action 'self';
  ">
  <title>示例页面</title>
  <!-- 其他meta标签、资源引入 -->
</head>
<body>
  <!-- 页面内容 -->
</body>
</html>
```
- 再次强调：meta标签不支持`frame-ancestors`、`report-uri`、`upgrade-insecure-requests`等指令，生产环境优先使用HTTP响应头部署。

### CSP 调试与排错方法
#### 一、浏览器开发者工具调试
1.  **Console面板**：浏览器会在Console面板中输出所有CSP违规行为的详细日志，包括被拦截的资源地址、违反的CSP指令、建议的修复方案、违规代码的行列号。
2.  **Network面板**：查看HTTP响应头中是否正确携带了CSP头，以及被拦截资源的请求状态。
3.  **Application面板 > 存储 > 报告 > CSP违规报告**：查看浏览器缓存的所有CSP违规报告详情。

#### 二、CSP违规报告收集与解析
##### 1. 违规报告的标准格式
浏览器发送的CSP违规报告为POST请求，Body为JSON格式，标准结构如下：
```json
{
  "csp-report": {
    "document-uri": "https://www.example.com/page.html",
    "referrer": "https://www.google.com/",
    "violated-directive": "script-src 'self'",
    "effective-directive": "script-src",
    "original-policy": "default-src 'self'; script-src 'self'; report-uri /api/csp-report;",
    "blocked-uri": "https://malicious.com/evil.js",
    "line-number": 15,
    "column-number": 3,
    "source-file": "https://www.example.com/page.html",
    "status-code": 200
  }
}
```
- 核心字段说明：
  - `document-uri`：发生违规的页面地址
  - `violated-directive`：违反的具体CSP指令
  - `blocked-uri`：被拦截的资源地址
  - `line-number/column-number`：违规代码的位置

##### 2. 简单的报告接收接口示例（Node.js/Express）
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const fs = require('fs');
const app = express();

// 解析JSON请求体
app.use(bodyParser.json({ type: 'application/csp-report' }));

// CSP报告接收接口
app.post('/api/csp-report', (req, res) => {
  const report = req.body;
  // 打印报告到控制台
  console.log('CSP违规报告：', JSON.stringify(report, null, 2));
  // 保存报告到文件
  fs.appendFile('./csp-reports.log', JSON.stringify(report) + '\n', (err) => {
    if (err) console.error('保存报告失败：', err);
  });
  res.status(204).send();
});

app.listen(3000);
```

#### 三、常见报错与排错方案
| 报错信息                                                     | 问题原因                                       | 修复方案                                                     |
| ------------------------------------------------------------ | ---------------------------------------------- | ------------------------------------------------------------ |
| Refused to load the script 'xxx' because it violates the following Content Security Policy directive: "script-src 'self'" | 脚本地址不在script-src的白名单中               | 将脚本的域名添加到script-src指令中，或使用nonce/hash验证     |
| Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self'" | 内联脚本被拦截，未配置nonce/hash/unsafe-inline | 为内联脚本添加nonce属性，或配置对应的哈希值，非必要不使用'unsafe-inline' |
| Refused to apply inline style because it violates the following Content Security Policy directive: "style-src 'self'" | 内联样式被拦截                                 | 为style-src添加'unsafe-inline'，或使用哈希值验证             |
| Refused to frame 'xxx' because an ancestor violates the following Content Security Policy directive: "frame-ancestors 'none'" | 页面被iframe嵌入，违反了frame-ancestors规则    | 将嵌入页面的域名添加到frame-ancestors指令中                  |
| Refused to send form data to 'xxx' because it violates the following Content Security Policy directive: "form-action 'self'" | 表单提交地址不在白名单中                       | 将表单提交的域名添加到form-action指令中                      |
| Refused to connect to 'xxx' because it violates the following Content Security Policy directive: "connect-src 'self'" | 网络请求地址不在白名单中                       | 将API、WebSocket等地址添加到connect-src指令中                |

### CSP 常见坑与避坑指南
1.  **多个CSP头的交集陷阱**
    - 坑：同时设置了多个CSP响应头/meta标签，误以为规则会合并，实际是取交集，导致所有资源被拦截。
    - 避坑：生产环境仅设置一个CSP响应头，如需叠加规则，必须确保所有规则的交集符合预期。

2.  **'unsafe-inline'的无效配置**
    - 坑：在script-src中同时配置了nonce/hash和'unsafe-inline'，误以为内联脚本会被允许，实际现代浏览器会忽略'unsafe-inline'。
    - 避坑：使用nonce/hash时，无需添加'unsafe-inline'，仅在需要兼容IE11等老浏览器时添加。

3.  **nonce值复用的安全风险**
    - 坑：使用固定的nonce值，或复用nonce值，导致攻击者可以窃取nonce值，绕过CSP防护执行恶意脚本。
    - 避坑：每次HTTP请求必须重新生成唯一的nonce值，使用安全的随机数生成器，严禁硬编码固定nonce。

4.  **通配符的滥用导致安全降级**
    - 坑：为了省事，使用`script-src https:`、`script-src *.example.com`等宽泛的通配符规则，导致白名单范围过大，攻击者可以利用子域名的XSS漏洞绕过防护。
    - 避坑：遵循最小权限原则，仅添加必须的域名，严禁使用协议通配符、顶级域名通配符。

5.  **meta标签的功能限制**
    - 坑：在meta标签中配置了frame-ancestors、report-uri等指令，误以为会生效，实际浏览器会直接忽略。
    - 避坑：生产环境优先使用HTTP响应头部署，仅在无服务器权限时使用meta标签，并严格遵守其功能限制。

6.  **default-src的兜底误区**
    - 坑：误以为所有指令都会继承default-src的规则，实际只有获取源指令会继承，文档指令、导航指令、报告指令均不会继承。
    - 避坑：必须为base-uri、form-action、frame-ancestors等非获取源指令单独配置规则，不能依赖default-src兜底。

7.  **frame-ancestors与X-Frame-Options的冲突**
    - 坑：同时设置了frame-ancestors和X-Frame-Options，导致规则冲突，浏览器执行更严格的规则。
    - 避坑：CSP的frame-ancestors已完全替代X-Frame-Options，生产环境仅配置frame-ancestors即可，无需再设置X-Frame-Options。

### CSP 最佳实践与安全规范
1.  **优先使用报告模式灰度上线**
    新策略上线前，先使用`Content-Security-Policy-Report-Only`报告模式运行7-14天，收集并修复所有违规报告，确认无问题后再切换为强制模式，避免影响网站正常运行。

2.  **采用strict-dynamic+nonce/hash的现代安全配置**
    摒弃传统的域名白名单模式，优先使用`nonce-* + 'strict-dynamic'`的配置方案，彻底解决动态脚本、第三方脚本的配置难题，同时实现最强的XSS防护能力。

3.  **严格遵循最小权限原则**
    每个指令仅配置必须的源值，禁用所有不需要的资源类型：
    - `object-src 'none'`：彻底禁用插件
    - `base-uri 'self'`：限制base标签
    - `form-action 'self'`：限制表单提交
    - `frame-ancestors 'none'`：禁止页面嵌入
    非必要严禁使用`'unsafe-inline'`和`'unsafe-eval'`。

4.  **自动升级HTTP请求为HTTPS**
    所有HTTPS网站必须添加`upgrade-insecure-requests`指令，自动将页面内的HTTP资源升级为HTTPS，解决混合内容问题，提升网站安全性。

5.  **定期审计与更新CSP策略**
    每季度对CSP策略进行一次安全审计，清理不再使用的域名白名单，收紧过于宽松的规则，确保策略始终符合最小权限原则。网站新增第三方服务、功能模块时，必须先更新CSP策略，再上线功能。

6.  **配合其他安全响应头使用**
    CSP需配合以下安全响应头使用，构建完整的前端安全防护体系：
    - `Strict-Transport-Security (HSTS)`：强制浏览器使用HTTPS访问网站
    - `X-Content-Type-Options: nosniff`：禁用浏览器的MIME类型嗅探
    - `Referrer-Policy`：控制Referer信息的发送规则
    - `Cross-Origin-Opener-Policy`：隔离跨域窗口，防止跨源攻击

7.  **做好浏览器兼容降级**
    针对需要兼容旧版浏览器的场景，配置降级规则：
    - 同时配置`report-uri`和`report-to`，兼容所有浏览器的报告功能
    - 在nonce/hash配置后添加`'unsafe-inline'`，兼容不支持nonce/hash的老浏览器
    - 同时配置`frame-src`和`child-src`，兼容不支持CSP3的旧版浏览器

### CSP 浏览器兼容性说明
#### 核心版本支持
| 标准版本 | Chrome | Firefox | Edge | Safari | IE             |
| -------- | ------ | ------- | ---- | ------ | -------------- |
| CSP 1.0  | 25+    | 23+     | 12+  | 7+     | 11（部分支持） |
| CSP 2.0  | 40+    | 45+     | 79+  | 10+    | 不支持         |
| CSP 3.0  | 52+    | 52+     | 79+  | 15.4+  | 不支持         |

#### 关键指令兼容性说明
1.  **'strict-dynamic'**：Chrome 52+、Firefox 52+、Edge 79+、Safari 15.4+，IE全版本不支持。
2.  **frame-ancestors**：Chrome 40+、Firefox 33+、Edge 79+、Safari 10+，IE全版本不支持。
3.  **report-to**：Chrome 70+、Edge 79+、Firefox 113+，Safari全版本不支持，生产环境需同时配置`report-uri`。
4.  **navigate-to**：Chrome 80+、Edge 80+，Firefox、Safari全版本不支持，生产环境慎用。
5.  **nonce-* / hash-***：Chrome 28+、Firefox 23+、Edge 12+、Safari 10+，IE11仅支持哈希值，不支持nonce。

#### 兼容降级建议
- 面向国内用户的网站，优先兼容Chrome、Edge、Firefox、Safari的最新3个大版本，无需兼容IE。
- 面向政企用户的网站，如需兼容IE11，需添加`'unsafe-inline'`降级，同时避免使用CSP3新增指令。
- 所有生产环境配置，必须同时包含`report-uri`，确保全浏览器的违规报告收集能力。
