# Tauri 全面开发文档（教述式）
本文档以**循序渐进的教学式讲解**为核心，覆盖 Tauri 从入门环境搭建、核心开发、进阶优化到打包分发的全流程，基于最新稳定的 Tauri 2.x 版本编写，同时兼容 1.x 核心用法，适配 Windows/macOS/Linux 全平台，兼顾桌面端与移动端开发。

## 一、Tauri 基础认知
### 1.1 什么是 Tauri
Tauri 是一款开源的**跨平台桌面/移动端应用开发框架**，允许开发者使用 HTML/CSS/JavaScript/TypeScript 等 Web 技术构建应用 UI，搭配 Rust 语言实现后端原生能力，最终可打包生成 Windows、macOS、Linux、Android、iOS 多平台的原生应用。

其核心设计理念是**轻量、高性能、强安全**，是 Electron 等传统 Web 桌面框架的主流替代方案。

### 1.2 核心架构
Tauri 采用严格的前后端分离双层架构，这是其性能与安全优势的核心来源：
- **前端层（WebView）**：应用的 UI 界面，完全使用 Web 技术开发，支持 Vue、React、Svelte、原生 HTML 等所有前端技术栈。代码运行在系统原生的 WebView 引擎中（Windows 用 WebView2、macOS 用 WKWebView、Linux 用 WebKitGTK），而非嵌入完整 Chromium，极致压缩应用体积。
- **后端层（Rust Core）**：应用的原生能力核心，基于 Rust 语言开发，负责与操作系统底层交互（文件操作、系统权限、进程管理、网络请求等）。前端通过 Tauri 封装的 IPC 通信机制与后端交互，实现 Web 界面与原生能力的无缝结合。

### 1.3 核心优势（与 Electron 对比）
| 特性       | Tauri                                                | Electron                                       |
| ---------- | ---------------------------------------------------- | ---------------------------------------------- |
| 应用体积   | 极小，Release 包通常仅几 MB，最小可到 600KB          | 庞大，基础包体积 50MB+                         |
| 内存占用   | 极低，同等应用仅为 Electron 的 1/10 ~ 1/5            | 高，默认占用数百 MB 内存                       |
| 启动速度   | 毫秒级，接近原生应用启动速度                         | 秒级，通常 2~5 秒启动                          |
| 安全性     | 强，默认沙箱隔离、细粒度权限控制，Rust 天然内存安全  | 弱，需开发者手动配置安全策略，存在内存安全风险 |
| 跨平台支持 | 桌面端（Windows/macOS/Linux）+ 移动端（Android/iOS） | 仅支持桌面端                                   |
| 运行时依赖 | 依赖系统自带 WebView，无需额外嵌入                   | 嵌入完整 Chromium 与 Node.js，无系统依赖       |

### 1.4 版本说明
- 本文档基于 **Tauri 2.x** 编写，这是当前最新稳定版本，核心重构为插件化架构，新增移动端全平台支持，是官方推荐的生产环境版本。
- 若你使用 Tauri 1.x，核心的命令、事件、基础开发逻辑完全兼容，仅插件、移动端、部分配置项存在差异，可通过官方 `tauri migrate` 命令一键迁移至 2.x。

## 二、开发环境全平台搭建
Tauri 环境分为**基础工具链**和**系统级依赖**两部分，以下为全平台保姆级搭建步骤，所有步骤完成后需重启终端/电脑生效。

### 2.1 通用基础工具链
#### 1. 安装 Node.js
Tauri 前端开发依赖 Node.js 环境，推荐安装 **LTS 18 及以上稳定版本**，自带 npm/yarn/pnpm 包管理器。
- 下载地址：https://nodejs.org/
- 安装完成后，打开终端验证安装：
```bash
node --version
npm --version
```

#### 2. 安装 Rust 工具链
Rust 是 Tauri 后端的核心语言，必须安装官方 rustup 工具链。
- **Windows 系统**：
  1. 访问 https://rustup.rs/ 下载 `rustup-init.exe`
  2. 运行安装程序，全程选择默认选项即可
- **macOS/Linux 系统**：
  打开终端执行以下命令：
  ```bash
  curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
  ```
- 安装完成后，重启终端，验证安装：
```bash
rustc --version
cargo --version
```
> 提示：cargo 是 Rust 的包管理器，等同于前端的 npm，后续 Rust 依赖均通过 cargo 管理。

### 2.2 各平台系统级依赖
#### 1. Windows 系统
1. **安装 Microsoft C++ 生成工具**
   - 下载 Visual Studio 安装程序：https://visualstudio.microsoft.com/zh-hans/visual-cpp-build-tools/
   - 安装时必须勾选 **使用C++的桌面开发**  workload，其他保持默认即可。
2. **安装 WebView2 运行时**
   - Windows 11 已默认内置，Windows 10 需手动安装
   - 下载地址：https://developer.microsoft.com/zh-cn/microsoft-edge/webview2/
   - 推荐下载「常青版独立安装程序」安装即可。

#### 2. macOS 系统
只需安装 Xcode 命令行工具，打开终端执行：
```bash
xcode-select --install
```
执行后按弹窗提示完成安装，无需完整安装 Xcode。

#### 3. Linux 系统（以 Debian/Ubuntu 为例）
打开终端执行以下命令，安装系统依赖库：
```bash
sudo apt update
sudo apt install libwebkit2gtk-4.1-dev build-essential curl wget file \
libxdo-dev libssl-dev libayatana-appindicator3-dev librsvg2-dev
```
其他 Linux 发行版（Fedora/Arch 等）依赖可参考官方文档：https://tauri.app/zh-cn/start/prerequisites/

### 2.3 环境验证
所有步骤完成后，重启终端，执行以下命令，无报错即环境搭建成功：
```bash
rustc --version && cargo --version && node --version
```

## 三、项目创建与结构解析
### 3.1 使用官方脚手架创建项目
Tauri 官方提供 `create-tauri-app` 脚手架，支持所有主流前端框架，一键生成标准化项目结构，是新手入门的首选方式。

#### 1. 执行创建命令
在你想要存放项目的文件夹中，打开终端，执行以下任一命令（推荐使用 pnpm/npm）：
```bash
# npm 方式
npm create tauri-app@latest

# pnpm 方式
pnpm create tauri-app@latest

# yarn 方式
yarn create tauri-app
```

#### 2. 交互式配置
执行命令后，终端会出现交互式选项，按提示完成配置即可：
1. 输入项目名称（如 `my-tauri-app`）
2. 输入前端包标识符（反向域名格式，如 `com.example.myapp`）
3. 选择包管理器（npm/pnpm/yarn/bun）
4. 选择前端模板（原生 HTML/Vue/React/Svelte/Angular 等）
5. 选择是否使用 TypeScript（推荐开启）

配置完成后，脚手架会自动生成项目结构，并提示后续操作命令。

#### 3. 初始化项目依赖
进入项目根目录，安装前端依赖：
```bash
cd my-tauri-app
npm install
```

### 3.2 项目核心结构详解
初始化完成后，项目的标准目录结构如下，每个文件/文件夹的作用都有明确分工，新手需重点掌握：
```
my-tauri-app/
├── src/                 # 前端源代码目录（你的UI代码全在这里）
│   ├── assets/          # 前端静态资源（图片、样式、字体等）
│   ├── main.ts/js       # 前端入口文件
│   └── index.html       # 前端HTML入口
├── public/              # 全局静态资源（不会被前端打包器编译）
├── src-tauri/           # Tauri Rust 后端核心目录（重中之重）
│   ├── src/             # Rust 源代码目录
│   │   ├── main.rs      # Rust 应用入口文件，命令注册、应用初始化都在这里
│   │   └── lib.rs       # 可选，用于拆分命令、逻辑模块
│   ├── Cargo.toml       # Rust 项目配置文件，管理Rust依赖、项目元信息
│   ├── tauri.conf.json  # Tauri 全局核心配置文件（窗口、权限、构建、安全等）
│   ├── icons/           # 应用全平台图标文件
│   └── target/          # Rust 编译产物目录（自动生成，无需修改）
├── package.json         # 前端项目配置文件，管理前端依赖、脚本命令
└── tsconfig.json        # TypeScript 配置（若选择TS模板）
```

#### 核心文件重点说明
1. **`src-tauri/src/main.rs`**：Rust 后端的入口核心，应用启动、命令注册、插件初始化、生命周期配置都在此文件中完成。
2. **`src-tauri/tauri.conf.json`**：Tauri 应用的全局配置文件，窗口大小、标题、图标、安全策略、API 权限、构建命令等所有应用级配置都在此管理。
3. **`src-tauri/Cargo.toml`**：Rust 项目的依赖管理文件，所有后端用到的 Rust 第三方库都需在此声明，等同于前端的 `package.json`。
4. **`src/`**：前端开发的主目录，和你平时开发 Web 项目完全一致，无额外学习成本。

## 四、快速上手：Hello World 实战
本节通过完整的实战步骤，带你实现第一个 Tauri 应用，掌握「前端调用 Rust 后端」的核心开发流程。

### 4.1 步骤1：启动开发环境
进入项目根目录，执行以下命令，启动 Tauri 开发环境：
```bash
npm run tauri dev
```
- 首次执行会下载并编译 Rust 依赖，耗时 1~3 分钟，后续启动会秒开（依赖已缓存）。
- 启动成功后，会自动弹出应用窗口，显示脚手架默认的欢迎页面。
- 开发环境默认开启**热重载**：修改前端代码，窗口实时刷新；修改 Rust 代码，应用自动重新编译并重启。

### 4.2 步骤2：编写 Rust 后端命令
我们先在 Rust 端定义一个可被前端调用的命令，实现「接收前端传入的名字，返回问候语」的功能。

1. 打开 `src-tauri/src/main.rs` 文件，修改代码如下：
```rust
// 预导入Tauri核心依赖
#![cfg_attr(not(debug_assertions), windows_subsystem = "windows")]

// 1. 定义一个前端可调用的命令函数，用#[tauri::command]宏标记
#[tauri::command]
fn say_hello(name: String) -> String {
    // 后端业务逻辑：这里可以写任何Rust原生能力
    println!("前端调用了say_hello命令，传入参数：{}", name);
    // 返回结果给前端
    format!("Hello, {}! 欢迎来到 Tauri 开发世界！", name)
}

// 2. 应用入口函数
fn main() {
    tauri::Builder::default()
        // 3. 注册我们定义的命令，多个命令用逗号分隔
        .invoke_handler(tauri::generate_handler![say_hello])
        // 运行应用
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

2. 保存文件，开发环境会自动重新编译 Rust 代码，无需手动重启。

### 4.3 步骤3：前端调用 Rust 命令
前端通过 Tauri 提供的 `invoke` 函数，即可调用我们在 Rust 端定义的 `say_hello` 命令，实现前后端通信。

1. 打开前端入口文件 `src/index.html`，修改代码如下：
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tauri Hello World</title>
</head>
<body>
    <div style="width: 100vw; height: 100vh; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 20px;">
        <h1>Tauri 入门实战</h1>
        <input type="text" id="nameInput" placeholder="请输入你的名字" style="padding: 8px 12px; width: 300px; font-size: 16px;">
        <button id="helloBtn" style="padding: 8px 20px; font-size: 16px; cursor: pointer;">发送问候</button>
        <p id="resultText" style="font-size: 18px; color: #2c3e50;"></p>
    </div>

    <script type="module">
        // 从Tauri JS API导入invoke函数，用于调用Rust命令
        import { invoke } from '@tauri-apps/api/core'

        // 获取DOM元素
        const nameInput = document.getElementById('nameInput');
        const helloBtn = document.getElementById('helloBtn');
        const resultText = document.getElementById('resultText');

        // 按钮点击事件
        helloBtn.addEventListener('click', async () => {
            const name = nameInput.value.trim();
            if (!name) {
                alert('请输入你的名字');
                return;
            }

            try {
                // 核心：调用Rust端的say_hello命令，传入参数
                // 第一个参数是命令名（和Rust端函数名完全一致）
                // 第二个参数是对象，包含传入的所有参数
                const result = await invoke('say_hello', { name });
                // 将返回结果渲染到页面
                resultText.textContent = result;
            } catch (error) {
                console.error('调用Rust命令失败', error);
                resultText.textContent = '调用失败，请重试';
            }
        });
    </script>
</body>
</html>
```

2. 保存文件，应用窗口会自动热重载，刷新页面内容。

### 4.4 步骤4：测试功能
1. 在应用窗口的输入框中输入你的名字，点击「发送问候」按钮。
2. 页面会立即显示 Rust 后端返回的问候语，同时终端会打印出前端传入的参数。
3. 至此，你已经完成了第一个 Tauri 应用，掌握了 Tauri 最核心的「前端调用 Rust」开发模式。

## 五、核心概念：前后端双向通信
前后端通信是 Tauri 开发的核心，框架提供了两种核心通信机制：**Commands 命令系统**（请求-响应模式，前端主动调用后端）和 **Events 事件系统**（发布-订阅模式，双向通信），覆盖所有业务场景。

### 5.1 Commands 命令系统（核心）
Commands 是 Tauri 最常用的通信方式，采用「前端请求-后端响应」的模式，支持类型安全、参数传递、异步执行、错误处理，适合绝大多数业务场景。

#### 1. 命令的基础定义与注册
- 定义命令：在 Rust 函数上添加 `#[tauri::command]` 宏，即可将普通 Rust 函数标记为可被前端调用的命令。
- 注册命令：在 `main.rs` 的 `invoke_handler` 中，通过 `tauri::generate_handler![]` 宏注册命令，多个命令用逗号分隔。
- 命名规则：前端调用的命令名必须和 Rust 函数名完全一致，区分大小写。

#### 2. 进阶用法：带返回值与错误处理
命令支持返回 `Result` 类型，实现规范的错误处理，前端可通过 `try/catch` 捕获错误，这是生产环境的标准写法。

Rust 端代码示例：
```rust
use tauri::command;

// 定义带错误处理的命令，返回Result类型
#[command]
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        // 返回错误，前端会捕获到该异常
        return Err("除数不能为0".to_string());
    }
    // 返回成功结果
    Ok(a / b)
}

// 注册命令
fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![divide])
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

前端调用代码示例：
```javascript
import { invoke } from '@tauri-apps/api/core';

// 调用带错误处理的命令
const calculateDivide = async (a, b) => {
    try {
        const result = await invoke('divide', { a, b });
        console.log('计算结果：', result);
        return result;
    } catch (error) {
        console.error('计算失败：', error);
        alert(error);
    }
};

// 测试
calculateDivide(10, 2); // 成功，返回5
calculateDivide(10, 0); // 失败，抛出异常
```

#### 3. 异步命令
对于耗时操作（如文件读写、网络请求、复杂计算），命令支持异步执行，避免阻塞应用主线程。

Rust 端异步命令示例：
```rust
use tauri::command;
use std::time::Duration;

// 定义异步命令，添加async关键字
#[command]
async fn async_task(seconds: u64) -> Result<String, String> {
    // 模拟耗时操作
    tokio::time::sleep(Duration::from_secs(seconds)).await;
    Ok(format!("异步任务执行完成，耗时{}秒", seconds))
}

// 注册命令，和同步命令完全一致
fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![async_task])
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```
> 提示：Tauri 内置 tokio 异步运行时，可直接在异步命令中使用 async/await 语法。

前端调用异步命令，和同步命令完全一致，无需额外修改：
```javascript
import { invoke } from '@tauri-apps/api/core';

const runAsyncTask = async () => {
    console.log('开始执行异步任务');
    const result = await invoke('async_task', { seconds: 3 });
    console.log(result); // 3秒后打印执行结果
};
```

#### 4. 命令中访问应用上下文
在命令中，可通过参数注入的方式获取 `AppHandle`（应用句柄）、`State`（全局状态）、`WebviewWindow`（窗口对象）等核心上下文，无需手动创建。

示例：在命令中获取应用句柄，实现窗口操作
```rust
use tauri::{command, AppHandle, WebviewWindow};

#[command]
fn set_window_title(app_handle: AppHandle, title: String) {
    // 获取当前窗口
    if let Some(window) = app_handle.get_webview_window("main") {
        // 修改窗口标题
        window.set_title(&title).unwrap();
    }
}
```

### 5.2 Events 事件系统
事件系统采用「发布-订阅」模式，支持**双向通信**：既可以前端监听、Rust 主动发送事件，也可以 Rust 监听、前端发送事件，适合流式数据传输、状态同步、通知推送等场景。

#### 1. Rust 端发送事件，前端监听
这是最常用的场景，用于 Rust 后端主动向前端推送数据（如下载进度、实时日志、系统通知等）。

##### 步骤1：Rust 端发送事件
通过 `AppHandle` 的 `emit` 方法发送事件，支持全局事件（所有窗口接收）和指定窗口事件。
```rust
use tauri::{AppHandle, Emitter, command};

// 定义一个模拟下载的命令，执行过程中向前端发送进度事件
#[command]
async fn download_file(app_handle: AppHandle, url: String) -> Result<(), String> {
    // 发送下载开始事件
    app_handle.emit("download-start", &url).unwrap();

    // 模拟下载进度
    for progress in [20, 40, 60, 80, 100] {
        tokio::time::sleep(tokio::time::Duration::from_millis(500)).await;
        // 发送下载进度事件，携带进度数据
        app_handle.emit("download-progress", progress).unwrap();
    }

    // 发送下载完成事件
    app_handle.emit("download-finish", &url).unwrap();
    Ok(())
}

fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![download_file])
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

##### 步骤2：前端监听事件
通过 `listen` 函数监听 Rust 端发送的事件，接收事件数据。
```javascript
import { invoke } from '@tauri-apps/api/core';
import { listen } from '@tauri-apps/api/event';

// 页面加载时，注册事件监听
window.addEventListener('DOMContentLoaded', async () => {
    // 监听下载开始事件
    await listen('download-start', (event) => {
        console.log('下载开始：', event.payload);
    });

    // 监听下载进度事件
    await listen('download-progress', (event) => {
        console.log('下载进度：', event.payload + '%');
        // 可实时更新页面进度条
    });

    // 监听下载完成事件
    await listen('download-finish', (event) => {
        console.log('下载完成：', event.payload);
        alert('文件下载完成！');
    });
});

// 触发下载
const startDownload = async () => {
    await invoke('download_file', { url: 'https://example.com/file.zip' });
};
```

#### 2. 前端发送事件，Rust 监听
前端也可主动发送事件，Rust 端通过 `listen` 方法监听，实现前端向 Rust 端的非请求式通信。

前端发送事件示例：
```javascript
import { emit } from '@tauri-apps/api/event';

// 前端发送事件，携带数据
const sendUserAction = async () => {
    await emit('user-click', { button: 'submit', time: Date.now() });
};
```

Rust 端监听事件示例：
```rust
use tauri::Builder;

fn main() {
    Builder::default()
        .setup(|app| {
            // 监听前端发送的user-click事件
            let _id = app.listen("user-click", |event| {
                println!("收到前端事件，数据：{:?}", event.payload());
            });
            Ok(())
        })
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

### 5.3 全局状态管理
对于多命令、多模块共享的数据，Tauri 提供了 `State` 全局状态管理机制，支持线程安全的全局数据共享，替代单例模式，是生产环境的最佳实践。

#### 1. 定义与注册全局状态
```rust
use tauri::{command, State, Manager};
use std::sync::Mutex;

// 1. 定义全局状态结构体
#[derive(Default)]
struct AppState {
    // 用Mutex实现线程安全的可变性
    visit_count: Mutex<u32>,
}

// 2. 定义命令，通过State参数注入全局状态
#[command]
fn add_visit_count(state: State<AppState>) -> u32 {
    // 获取锁，修改状态
    let mut count = state.visit_count.lock().unwrap();
    *count += 1;
    *count
}

fn main() {
    tauri::Builder::default()
        // 3. 注册全局状态，应用启动时初始化
        .manage(AppState::default())
        .invoke_handler(tauri::generate_handler![add_visit_count])
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

#### 2. 前端调用
```javascript
import { invoke } from '@tauri-apps/api/core';

// 每次调用，全局计数+1
const getCount = async () => {
    const count = await invoke('add_visit_count');
    console.log('当前访问次数：', count);
};
```

## 六、前端开发指南
Tauri 对前端开发**零侵入**，你可以完全按照 Web 开发的习惯编写 UI 代码，同时 Tauri 提供了完善的 JS API，封装了窗口、对话框、文件系统、网络等原生能力，无需手动编写 Rust 命令即可快速调用。

### 6.1 Tauri JS API 安装与引入
- 若你使用 `create-tauri-app` 脚手架创建项目，JS API 已默认安装，无需额外操作。
- 手动安装命令：
```bash
npm install @tauri-apps/api
```

- 引入方式：采用 ESModule 按需引入，示例：
```javascript
// 核心通信API
import { invoke } from '@tauri-apps/api/core';
// 窗口管理API
import { getCurrentWindow } from '@tauri-apps/api/window';
// 对话框API
import { message, open } from '@tauri-apps/api/dialog';
// 文件系统API
import { readTextFile, writeTextFile } from '@tauri-apps/api/fs';
```

### 6.2 核心前端 API 常用场景
#### 1. 窗口管理
通过 `window` 模块，可实现窗口的最大化、最小化、关闭、置顶、修改大小等所有原生窗口操作，无需编写 Rust 代码。

示例：基础窗口操作
```javascript
import { getCurrentWindow } from '@tauri-apps/api/window';

// 获取当前窗口实例
const appWindow = getCurrentWindow();

// 窗口最小化
const minimizeWindow = () => appWindow.minimize();
// 窗口最大化/还原
const toggleMaximize = () => appWindow.toggleMaximize();
// 关闭窗口
const closeWindow = () => appWindow.close();
// 设置窗口置顶
const setAlwaysOnTop = (isTop) => appWindow.setAlwaysOnTop(isTop);
// 设置窗口大小
const resizeWindow = (width, height) => appWindow.setSize({ width, height });
```

#### 2. 系统对话框
通过 `dialog` 模块，可快速调用系统原生的消息提示、文件选择、文件夹选择对话框，替代浏览器的 `alert/confirm`，体验更原生。

示例：文件选择对话框
```javascript
import { open, message } from '@tauri-apps/api/dialog';

// 打开文件选择对话框
const selectFile = async () => {
    const selected = await open({
        multiple: false,
        filters: [
            { name: '文本文件', extensions: ['txt'] },
            { name: '图片文件', extensions: ['png', 'jpg', 'jpeg'] }
        ]
    });

    if (selected) {
        console.log('选择的文件路径：', selected);
        await message('文件选择成功！', { title: '提示', type: 'info' });
    }
};
```

#### 3. 文件系统操作
通过 `fs` 模块，可实现文件的读写、创建、删除、遍历等操作，直接访问本地文件系统，突破浏览器的沙箱限制。

> 重要提示：使用 fs 等原生能力 API，必须先在 `tauri.conf.json` 中配置对应的权限，否则会调用失败。

示例：读写文本文件
```javascript
import { readTextFile, writeTextFile } from '@tauri-apps/api/fs';
import { documentDir } from '@tauri-apps/api/path';

// 写入文件
const saveFile = async () => {
    const docPath = await documentDir();
    const filePath = `${docPath}/tauri-test.txt`;
    
    await writeTextFile(filePath, 'Hello Tauri! 这是写入的文本内容');
    console.log('文件写入成功');
};

// 读取文件
const loadFile = async () => {
    const docPath = await documentDir();
    const filePath = `${docPath}/tauri-test.txt`;
    
    const content = await readTextFile(filePath);
    console.log('文件内容：', content);
};
```

#### 4. 其他常用 API
| 模块                           | 核心功能                                   |
| ------------------------------ | ------------------------------------------ |
| `@tauri-apps/api/shell`        | 打开外部链接、执行系统命令、打开默认应用   |
| `@tauri-apps/api/notification` | 发送系统桌面通知                           |
| `@tauri-apps/api/clipboard`    | 读写系统剪贴板                             |
| `@tauri-apps/api/http`         | 发送 HTTP/HTTPS 请求，突破浏览器 CORS 限制 |
| `@tauri-apps/api/updater`      | 应用自动更新功能                           |

### 6.3 前端安全策略
Tauri 内置了严格的安全机制，开发时必须遵守以下核心规则，避免安全风险：
1. **最小权限原则**：仅在 `tauri.conf.json` 中开启应用必需的 API 权限，禁止开启全量权限。
2. **CSP 内容安全策略**：默认开启严格的 CSP 策略，禁止执行内联脚本、eval 代码，禁止加载不安全的远程资源，生产环境切勿关闭。
3. **禁止前端执行危险操作**：不要在前端硬编码文件路径、系统命令、密钥等敏感信息，所有危险操作应放在 Rust 后端执行，前端仅负责触发命令。
4. **输入校验**：所有从前端传入 Rust 命令的参数，必须在 Rust 端做严格的校验和过滤，避免命令注入、路径遍历等安全漏洞。

## 七、Rust 后端开发指南
Rust 后端是 Tauri 应用的核心，负责实现所有原生能力、复杂业务逻辑、性能敏感型操作，前端仅负责 UI 渲染，这是 Tauri 高性能、高安全性的核心保障。

### 7.1 Rust 项目依赖管理
Tauri 的 Rust 依赖通过 `src-tauri/Cargo.toml` 文件管理，格式如下：
```toml
[package]
name = "my-tauri-app"
version = "0.1.0"
edition = "2021"

[dependencies]
# Tauri核心依赖，版本和脚手架保持一致
tauri = { version = "2.0", features = [] }
# serde 用于序列化/反序列化，前后端数据传输必备
serde = { version = "1.0", features = ["derive"] }
# 你可以添加任何Rust第三方库，比如：
# 系统信息获取
sysinfo = "0.30"
# 网络请求
reqwest = { version = "0.12", features = ["json"] }
# 异步运行时（Tauri已内置，无需额外添加）
# tokio = { version = "1.0", features = ["full"] }
```

添加依赖后，执行 `tauri dev` 或 `cargo build` 会自动下载并编译依赖，无需手动安装。

### 7.2 应用生命周期管理
Tauri 提供了完整的应用生命周期钩子，可在应用启动、窗口创建、应用退出等关键节点执行自定义逻辑，核心通过 `setup` 钩子实现，这是应用初始化的核心入口。

示例：应用生命周期钩子
```rust
use tauri::Builder;

fn main() {
    Builder::default()
        // setup 钩子：应用初始化完成，窗口创建前执行，仅执行一次
        .setup(|app| {
            println!("应用启动完成，应用ID：{}", app.package_info().name);
            
            // 在这里执行初始化逻辑：
            // 1. 读取配置文件
            // 2. 初始化数据库连接
            // 3. 启动后台服务
            // 4. 注册全局事件监听
            
            Ok(())
        })
        // 应用即将退出时执行
        .on_window_event(|window, event| {
            match event {
                // 窗口关闭事件
                tauri::WindowEvent::CloseRequested { api, .. } => {
                    println!("窗口即将关闭");
                    // 可在这里阻止窗口关闭，实现退出确认逻辑
                    // api.prevent_close();
                }
                _ => {}
            }
        })
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

### 7.3 多窗口开发
Tauri 支持多窗口应用开发，可通过 Rust 后端动态创建、管理多个窗口，每个窗口都是独立的 WebView 实例，可通过事件系统实现窗口间通信。

示例：动态创建新窗口
```rust
use tauri::{command, AppHandle, WebviewWindowBuilder};

#[command]
fn create_new_window(app_handle: AppHandle) {
    // 构建新窗口
    let new_window = WebviewWindowBuilder::new(
        &app_handle,
        "second-window", // 窗口唯一标签，不可重复
        tauri::WebviewUrl::App("second.html".into()) // 窗口加载的前端页面
    )
    .title("第二个窗口") // 窗口标题
    .inner_size(800.0, 600.0) // 窗口大小
    .resizable(true) // 是否可调整大小
    .build() // 创建窗口
    .unwrap();

    println!("新窗口创建成功，标签：{}", new_window.label());
}

// 注册命令
fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![create_new_window])
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

前端调用创建窗口命令：
```javascript
import { invoke } from '@tauri-apps/api/core';

const openNewWindow = async () => {
    await invoke('create_new_window');
};
```

### 7.4 Rust 后端开发最佳实践
1. **模块拆分**：不要把所有命令都写在 `main.rs` 中，可按业务模块拆分到不同的 Rust 文件中，通过 `mod` 关键字引入，提升代码可维护性。
2. **错误处理规范化**：不要滥用 `unwrap()`，生产环境应使用自定义错误类型，配合 `thiserror` 库实现规范的错误处理，避免应用崩溃。
3. **耗时操作异步化**：所有 IO 操作、网络请求、复杂计算都应使用异步命令，避免阻塞主线程导致应用卡顿。
4. **状态管理集中化**：多模块共享的数据使用 `State` 管理，不要使用全局静态变量，保证线程安全。
5. **日志输出**：使用 `log` 库配合 `tauri-plugin-log` 插件实现规范的日志输出，替代 `println!`，方便生产环境调试与排错。

## 八、插件系统与能力扩展
Tauri 2.x 采用**插件化架构**，将所有非核心能力都拆分为独立插件，开发者可按需引入，实现极致的包体积优化，同时支持自定义插件扩展能力。

### 8.1 官方常用插件
Tauri 官方提供了大量成熟的插件，覆盖绝大多数业务场景，无需手动编写底层 Rust 代码，以下是最常用的官方插件：

| 插件名                      | 核心功能                             |
| --------------------------- | ------------------------------------ |
| `tauri-plugin-fs`           | 文件系统操作，替代 1.x 内置 fs API   |
| `tauri-plugin-dialog`       | 系统对话框，替代 1.x 内置 dialog API |
| `tauri-plugin-shell`        | 系统 shell 操作、打开外部链接        |
| `tauri-plugin-http`         | 网络请求，突破 CORS 限制             |
| `tauri-plugin-notification` | 系统通知                             |
| `tauri-plugin-updater`      | 应用自动更新                         |
| `tauri-plugin-log`          | 跨平台日志系统                       |
| `tauri-plugin-store`        | 本地键值对存储，替代 localStorage    |
| `tauri-plugin-opener`       | 用默认应用打开文件/链接              |

### 8.2 插件安装与使用（以 fs 插件为例）
#### 1. 安装插件
分为 Rust 端和前端端两部分安装：

1. **Rust 端安装**：在 `src-tauri/Cargo.toml` 中添加插件依赖
```toml
[dependencies]
tauri-plugin-fs = "2.0"
```

2. **前端端安装**：在项目根目录执行
```bash
npm install @tauri-apps/plugin-fs
```

#### 2. 注册插件
在 `main.rs` 中注册插件，应用启动时加载插件：
```rust
fn main() {
    tauri::Builder::default()
        // 注册fs插件
        .plugin(tauri_plugin_fs::init())
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

#### 3. 前端使用插件 API
和内置 API 用法完全一致，按需引入即可：
```javascript
import { readTextFile, writeTextFile } from '@tauri-apps/plugin-fs';

// 读写文件
const testFs = async () => {
    await writeTextFile('test.txt', '插件测试内容');
    const content = await readTextFile('test.txt');
    console.log(content);
};
```

#### 4. 配置插件权限
大多数插件都需要在 `tauri.conf.json` 中配置权限，才能正常使用，例如 fs 插件：
```json
{
  "plugins": {
    "fs": {
      "scope": ["$DOCUMENT/*", "$DESKTOP/*"]
    }
  },
  "permissions": [
    "fs:allow-read-text-file",
    "fs:allow-write-text-file"
  ]
}
```

### 8.3 自定义插件开发
当官方插件无法满足业务需求时，你可以开发自定义插件，将通用的 Rust 逻辑和前端 API 封装为独立插件，实现复用。

自定义插件的创建命令：
```bash
npx @tauri-apps/cli plugin new my-plugin
```
执行后会生成标准化的插件项目结构，包含 Rust 后端代码、前端 API 封装、权限配置等，详细开发教程可参考官方插件开发文档：https://tauri.app/zh-cn/develop/plugins/

## 九、核心配置文件详解
`src-tauri/tauri.conf.json` 是 Tauri 应用的全局核心配置文件，所有应用级配置都在此管理，以下是核心配置项的详细讲解，新手需重点掌握。

### 9.1 基础配置结构
```json
{
  "$schema": "../node_modules/@tauri-apps/cli/schema.json",
  // 应用基础信息
  "productName": "my-tauri-app",
  "version": "0.1.0",
  "identifier": "com.example.myapp",
  // 构建配置
  "build": {
    "frontendDist": "../dist",
    "beforeDevCommand": "npm run dev",
    "beforeBuildCommand": "npm run build",
    "devUrl": "http://localhost:5173"
  },
  // 应用配置
  "app": {
    "windows": [
      {
        "label": "main",
        "title": "我的Tauri应用",
        "width": 1200,
        "height": 800,
        "resizable": true,
        "fullscreen": false,
        "center": true
      }
    ],
    "security": {
      "csp": "default-src 'self'; img-src 'self' asset: https://asset.localhost"
    }
  },
  // 插件配置
  "plugins": {},
  // 权限配置
  "permissions": []
}
```

### 9.2 核心配置项详解
| 配置项                     | 作用             | 关键说明                                                     |
| -------------------------- | ---------------- | ------------------------------------------------------------ |
| `identifier`               | 应用唯一标识符   | 反向域名格式，如 `com.example.myapp`，是应用的唯一标识，打包、代码签名、移动端开发必须配置，不可随意修改 |
| `productName`              | 应用名称         | 最终生成的可执行文件名称、窗口默认标题、安装包显示名称       |
| `version`                  | 应用版本号       | 语义化版本格式，如 `1.0.0`，用于自动更新、版本管理           |
| `build.frontendDist`       | 前端构建产物目录 | Tauri 打包时会将该目录的前端文件嵌入到应用中，必须和前端打包器的输出目录一致 |
| `build.beforeDevCommand`   | 开发前执行的命令 | 启动 `tauri dev` 时会先执行该命令，通常是前端开发服务器启动命令，如 `npm run dev` |
| `build.beforeBuildCommand` | 构建前执行的命令 | 执行 `tauri build` 时会先执行该命令，通常是前端打包命令，如 `npm run build` |
| `build.devUrl`             | 开发服务器地址   | 开发环境下 Tauri 加载的前端地址，必须和前端开发服务器地址一致 |
| `app.windows`              | 窗口配置数组     | 配置应用的主窗口，可配置多个窗口，每个窗口的 `label` 必须唯一，是窗口的唯一标识 |
| `app.security.csp`         | 内容安全策略     | 控制前端资源的加载、脚本执行规则，生产环境必须保持严格配置，切勿关闭 |
| `permissions`              | 全局权限配置     | 配置应用允许使用的 API 权限，遵循最小权限原则，仅开启必需的权限 |
| `plugins`                  | 插件配置         | 各个插件的专属配置项，如 fs 插件的路径范围、updater 插件的更新地址等 |

## 十、调试与排错
### 10.1 开发调试工具
#### 1. 前端 DevTools
Tauri 开发环境默认集成了 Chromium 开发者工具，和浏览器调试完全一致：
- 打开方式：在应用窗口中按 `F12` 或 `Ctrl+Shift+I`（Windows/Linux）、`Cmd+Opt+I`（macOS）。
- 功能支持：完全支持元素审查、Console 控制台、网络请求、断点调试、性能分析等所有浏览器 DevTools 功能。
- 生产环境默认禁用 DevTools，避免用户打开修改应用。

#### 2. Rust 后端调试
- **日志打印**：开发环境可使用 `println!` 宏打印变量、执行流程，输出会显示在终端中，是最常用的调试方式。
- **断点调试**：可使用 VS Code 配合 `rust-analyzer` 插件、CLion 等 IDE 实现 Rust 代码的断点调试，查看变量、调用栈等。
- **错误日志**：Rust 代码编译错误会直接显示在终端中，包含详细的错误位置、原因、修复建议，新手可根据提示快速修复。

### 10.2 常见问题与解决方案
| 常见问题                                  | 核心原因                                                     | 解决方案                                                     |
| ----------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `tauri dev` 启动失败，提示 Rust 编译错误  | 1. Rust 环境未正确安装；2. Cargo.toml 依赖配置错误；3. Rust 代码语法错误 | 1. 重新执行 `rustc --version` 验证环境；2. 检查终端的编译错误提示，修复代码/依赖配置；3. 执行 `cargo clean` 清理缓存后重试 |
| 前端调用 invoke 提示「command not found」 | 1. Rust 命令未在 `generate_handler` 中注册；2. 前端调用的命令名和 Rust 函数名不一致（区分大小写） | 1. 检查命令是否已注册；2. 核对命令名拼写，确保完全一致       |
| 调用 API 提示「permission denied」        | 未在 `tauri.conf.json` 中配置对应的 API 权限                 | 1. 查看官方文档，找到该 API 对应的权限项；2. 在 `permissions` 数组中添加该权限；3. 重启开发环境生效 |
| 打包后应用打开白屏                        | 1. 前端打包产物路径配置错误；2. 前端路由使用了 history 模式；3. 前端代码中使用了浏览器专属 API | 1. 核对 `frontendDist` 配置是否正确；2. 前端路由改为 hash 模式；3. 区分开发环境和生产环境，避免在 Tauri 中使用浏览器专属 API |
| 跨平台打包失败                            | Tauri 不支持交叉编译，无法在 Windows 上打包 macOS 应用，反之亦然 | 1. 在对应系统上执行打包命令；2. 使用 GitHub Actions 等 CI/CD 工具实现多平台自动化构建 |

## 十一、打包、构建与分发
### 11.1 应用构建打包
当应用开发完成后，使用 `tauri build` 命令即可构建生产环境的应用安装包，命令如下：
```bash
# 生产环境构建命令
npm run tauri build
```

#### 构建说明
1. 执行命令后，会先执行 `beforeBuildCommand` 配置的前端打包命令，生成前端构建产物。
2. 然后编译 Rust 代码，生成 release 优化版本的二进制文件。
3. 最后将前端产物、二进制文件、资源文件打包，生成对应平台的安装包/可执行文件。
4. 首次构建会编译所有 Rust 依赖，耗时较长，后续构建会快很多。

#### 构建产物位置
构建完成后，产物会生成在 `src-tauri/target/release/` 目录下，不同平台的产物格式不同：
- **Windows**：生成 `.exe` 可执行文件和 `.msi` 安装包，位于 `src-tauri/target/release/bundle/msi/`
- **macOS**：生成 `.app` 应用 bundle 和 `.dmg` 磁盘镜像，位于 `src-tauri/target/release/bundle/dmg/`
- **Linux**：生成 `.deb` Debian 安装包和 `.AppImage` 便携包，位于 `src-tauri/target/release/bundle/`

### 11.2 跨平台打包注意事项
Tauri **不支持交叉编译**，即无法在一个系统上直接打包其他平台的应用，必须在对应平台执行构建命令：
- Windows 应用只能在 Windows 系统上构建
- macOS 应用只能在 macOS 系统上构建
- Linux 应用只能在 Linux 系统上构建

若需实现一次代码，多平台打包，推荐使用 GitHub Actions、GitLab CI 等 CI/CD 工具，在云端分别在不同系统上执行构建命令，生成全平台安装包。

### 11.3 代码签名与公证
生产环境分发应用时，必须进行代码签名，否则会被系统拦截，提示「未知发布者」，甚至无法安装运行。
- **Windows**：需要购买代码签名证书，对 `.exe` 和 `.msi` 文件进行签名，可提交到 Microsoft Store 分发。
- **macOS**：需要 Apple 开发者账号，对应用进行代码签名和公证，否则 macOS 会阻止应用运行，无法分发到 App Store。
- **Linux**：无需强制代码签名，可通过 GPG 对安装包进行签名，提升安全性。

详细的代码签名教程可参考官方文档：https://tauri.app/zh-cn/distribute/signing/

### 11.4 自动更新
Tauri 官方提供了 `tauri-plugin-updater` 插件，开箱即用实现应用的自动更新功能，无需手动开发更新逻辑，核心步骤：
1. 安装并注册 updater 插件
2. 在 `tauri.conf.json` 中配置更新服务器地址、公钥
3. 前端通过插件 API 检查更新、下载更新、安装更新
4. 发布新版本时，生成更新包和签名，上传到更新服务器

详细教程可参考官方自动更新文档：https://tauri.app/zh-cn/distribute/updater/

### 11.5 分发渠道
构建完成的应用，可通过以下渠道分发：
1. **官方网站直接下载**：将安装包上传到你的服务器/对象存储，提供下载链接，适合独立开发者。
2. **应用商店**：Windows 可提交到 Microsoft Store，macOS 可提交到 App Store，Linux 可提交到 Flathub、Snap Store。
3. **第三方分发平台**：可通过 CrabNebula Cloud、GitHub Releases 等平台分发，自带更新统计、版本管理功能。

## 十二、进阶开发与最佳实践
### 12.1 移动端开发
Tauri 2.x 全面支持 Android 和 iOS 移动端开发，一套代码可同时构建桌面端和移动端应用，核心开发逻辑和桌面端完全一致，仅需额外配置移动端环境和权限。

#### 移动端开发环境搭建
1. **Android 开发**：安装 Android Studio、Android SDK、NDK，配置环境变量，详细步骤参考官方文档。
2. **iOS 开发**：必须使用 macOS 系统，安装 Xcode，配置 iOS 开发证书，详细步骤参考官方文档。

#### 移动端开发命令
```bash
# Android 开发环境启动
npm run tauri android dev

# iOS 开发环境启动
npm run tauri ios dev

# Android 构建打包
npm run tauri android build

# iOS 构建打包
npm run tauri ios build
```
> 提示：移动端的窗口、权限、生命周期等配置，和桌面端略有差异，需参考移动端专属文档。

### 12.2 性能优化最佳实践
1. **前端性能优化**：和 Web 应用优化完全一致，使用生产环境构建、代码分割、懒加载、图片压缩，减少前端包体积，提升渲染速度。
2. **Rust 性能优化**：
   - 耗时操作使用异步执行，避免阻塞主线程
   - 大数据传输使用通道（Channel）替代事件，减少序列化开销
   - 发布构建时配置 Cargo 优化参数，极致压缩二进制体积
   - 避免频繁的前后端通信，批量处理数据，减少 IPC 调用次数
3. **包体积优化**：
   - 按需引入插件，不使用的插件不要注册
   - 开启 Rust 编译优化，移除调试符号
   - 使用 `upx` 对二进制文件进行压缩，进一步减小体积
   - 前端使用 Tree Shaking 移除未使用的代码

### 12.3 安全最佳实践
1. **严格遵循最小权限原则**：仅开启应用必需的 API 权限和文件系统路径范围，禁止使用通配符开启全量权限。
2. **前端不可信原则**：所有从前端传入的参数，必须在 Rust 端做严格的校验和过滤，禁止直接使用前端传入的路径、命令、SQL 语句等敏感内容。
3. **敏感操作后端化**：所有文件操作、系统命令、网络请求、密钥管理等敏感操作，必须放在 Rust 后端实现，前端仅负责触发，禁止在前端实现核心业务逻辑。
4. **保持 CSP 策略开启**：生产环境切勿关闭或放宽 CSP 策略，避免 XSS 攻击。
5. **定期更新依赖**：定期更新 Tauri 版本、Rust 依赖、前端依赖，修复安全漏洞。

### 12.4 CI/CD 自动化构建
推荐使用 GitHub Actions 实现 Tauri 应用的自动化构建、代码签名、发布全流程，一次配置，永久生效。官方提供了成熟的 `tauri-action` 动作，可快速配置全平台自动化构建，示例配置可参考官方文档：https://tauri.app/zh-cn/distribute/pipelines/github/

---

## Tauri 核心模块

---

### 一、IPC 通信（深度进阶）
IPC（进程间通信）是 Tauri 前端 WebView 与 Rust 后端通信的**唯一核心通道**，前文已讲解基础的 Commands 命令与 Events 事件，这里深入讲解生产级进阶用法、安全规范与 2.x 新增特性。

#### 1.1 核心模式回顾（对应后端开发思维）
| Tauri IPC 模式    | 对应后端开发模型                    | 核心适用场景                                       |
| ----------------- | ----------------------------------- | -------------------------------------------------- |
| Commands 命令系统 | HTTP 请求-响应模型（同步/异步接口） | 前端主动触发后端逻辑、获取数据、执行操作（最常用） |
| Events 事件系统   | 发布-订阅消息队列（MQ）             | 后端主动向前端推送数据、多窗口广播、状态同步       |
| Channel 流式通道  | gRPC 流式传输/WebSocket 双向流      | 大文件传输、实时日志、进度推送等高频流式数据场景   |

#### 1.2 Commands 命令系统进阶
##### 1.2.1 命令的核心依赖注入
Rust 端的命令函数支持**编译期自动注入上下文对象**，无需手动传递，这是生产级开发的核心能力，支持注入的对象包括：
- `AppHandle`：应用全局句柄，可操作窗口、托盘、插件、全局状态
- `WebviewWindow`：当前调用命令的窗口实例，可精准操作当前窗口
- `State<T>`：全局注册的状态对象，用于多命令共享数据
- `WindowEvent`：窗口事件对象（事件相关命令）

**实战示例**：带上下文注入的命令
```rust
// src-tauri/src/main.rs
use tauri::{command, AppHandle, WebviewWindow, State, Manager};
use std::sync::Mutex;

// 全局状态
#[derive(Default)]
pub struct AppState {
    visit_count: Mutex<u32>,
}

// 注入多个上下文对象的命令
#[command]
pub async fn update_window_title(
    app_handle: AppHandle,       // 应用全局句柄
    window: WebviewWindow,        // 当前调用命令的窗口
    state: State<'_, AppState>,   // 全局状态
    new_title: String,             // 前端传入的业务参数
) -> Result<u32, String> {
    // 1. 修改当前窗口标题
    window.set_title(&new_title).map_err(|e| e.to_string())?;
    
    // 2. 更新全局访问计数
    let mut count = state.visit_count.lock().unwrap();
    *count += 1;
    
    // 3. 通过AppHandle获取所有窗口，广播标题更新事件
    app_handle.emit("title-updated", &new_title).map_err(|e| e.to_string())?;

    Ok(*count)
}

fn main() {
    tauri::Builder::default()
        .manage(AppState::default()) // 注册全局状态
        .invoke_handler(tauri::generate_handler![update_window_title])
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

前端调用：
```typescript
// 前端代码
import { invoke } from '@tauri-apps/api/core';

const changeTitle = async () => {
  try {
    const count = await invoke('update_window_title', {
      newTitle: '新的窗口标题'
    });
    console.log('更新成功，当前访问次数：', count);
  } catch (error) {
    console.error('更新失败：', error);
  }
};
```

##### 1.2.2 命令的权限控制
Tauri 2.x 支持为命令配置细粒度权限，禁止未授权的前端页面调用命令，生产环境必须开启：
1. 在 `src-tauri/capabilities/main.json` 中配置命令权限：
```json
{
  "identifier": "main",
  "description": "主窗口权限",
  "windows": ["main"],
  "permissions": [
    // 允许调用指定的命令
    "core:allow-invoke",
    {
      "identifier": "core:allow-invoke",
      "allow": ["update_window_title", "say_hello"], // 仅允许调用的命令列表
      "deny": ["*"] // 禁止其他所有命令
    }
  ]
}
```
2. 多窗口场景下，可为不同窗口配置不同的命令权限，实现权限隔离。

##### 1.2.3 异步命令的取消
对于耗时的异步命令，Tauri 支持前端取消执行，避免无效的资源占用：
```rust
// Rust 端：支持取消的异步命令
#[command]
pub async fn long_time_task(#[tauri::command] cancel: tauri::CancellationToken) -> Result<String, String> {
    for i in 0..10 {
        // 检查是否收到取消信号
        if cancel.is_cancelled() {
            return Err("任务已被取消".to_string());
        }
        tokio::time::sleep(tokio::time::Duration::from_secs(1)).await;
        println!("任务进度：{}0%", i+1);
    }
    Ok("任务执行完成".to_string())
}
```

前端取消任务：
```typescript
import { invoke } from '@tauri-apps/api/core';
import { CancellationToken } from '@tauri-apps/api/core';

// 创建取消令牌
const token = new CancellationToken();

// 启动任务
const runTask = async () => {
  try {
    const result = await invoke('long_time_task', {}, { cancel: token });
    console.log(result);
  } catch (error) {
    console.error(error);
  }
};

// 取消任务
const cancelTask = () => {
  token.cancel();
  console.log('已发送取消信号');
};
```

#### 1.3 Events 事件系统进阶
##### 1.3.1 精准事件投递
事件支持**全局广播**、**指定窗口投递**、**指定窗口排除**，避免无效的事件分发：
```rust
// Rust 端事件投递
use tauri::{Emitter, AppHandle};

#[command]
pub async fn send_event_to_target(app_handle: AppHandle) -> Result<(), String> {
    // 1. 全局广播：所有窗口都能收到
    app_handle.emit("global-event", "全局广播消息").unwrap();

    // 2. 定向投递：只给指定标签的窗口发送
    app_handle.emit_to("second-window", "target-event", "仅第二个窗口能收到").unwrap();

    // 3. 排除投递：除了指定窗口，其他窗口都能收到
    app_handle.emit_others("exclude-event", "除了主窗口都能收到", "main").unwrap();

    Ok(())
}
```

##### 1.3.2 事件监听的生命周期管理
前端监听事件时，必须手动管理监听的销毁，避免内存泄漏：
```typescript
import { listen } from '@tauri-apps/api/event';
import { onUnmounted } from 'vue'; // React 用 useEffect 清理

// 页面挂载时注册监听
let unlisten: () => void;
onMounted(async () => {
  unlisten = await listen('global-event', (event) => {
    console.log('收到事件：', event.payload);
  });
});

// 页面卸载时销毁监听，避免内存泄漏
onUnmounted(() => {
  unlisten?.();
});
```

#### 1.4 Channel 流式通道（2.x 新增）
Channel 是 Tauri 2.x 新增的高性能流式 IPC 通道，专为高频、大数据量的双向流式传输设计，性能远高于事件系统，适合实时日志、大文件传输、进度推送等场景。

**实战示例：前端向后端发送流式数据**

1. Rust 端定义接收通道的命令：
```rust
// src-tauri/src/main.rs
use tauri::{command, ipc::Channel};

#[command]
pub async fn upload_file_stream(channel: Channel<Vec<u8>>) -> Result<String, String> {
    let mut file_data = Vec::new();
    
    // 持续接收前端发送的流式数据
    while let Some(chunk) = channel.recv().await {
        if chunk.is_empty() {
            // 空包表示传输结束
            break;
        }
        file_data.extend_from_slice(&chunk);
        println!("收到分片，大小：{}字节", chunk.len());
    }

    Ok(format!("文件传输完成，总大小：{}字节", file_data.len()))
}

// 注册命令
fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![upload_file_stream])
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

2. 前端发送流式数据：
```typescript
import { invoke } from '@tauri-apps/api/core';
import { Channel } from '@tauri-apps/api/core';

// 创建通道
const channel = new Channel<Uint8Array>();

// 流式上传文件
const uploadFile = async (file: File) => {
  // 先把通道传给后端
  const uploadPromise = invoke('upload_file_stream', { channel });

  // 分片读取文件，通过通道流式发送
  const chunkSize = 1024 * 1024; // 1MB 分片
  const fileReader = new FileReader();
  let offset = 0;

  const readChunk = () => {
    const slice = file.slice(offset, offset + chunkSize);
    fileReader.readAsArrayBuffer(slice);
  };

  fileReader.onload = (e) => {
    const chunk = new Uint8Array(e.target?.result as ArrayBuffer);
    channel.send(chunk); // 发送分片
    offset += chunk.length;

    if (offset < file.size) {
      readChunk(); // 继续读取下一个分片
    } else {
      channel.send(new Uint8Array()); // 发送空包表示结束
    }
  };

  readChunk();

  // 等待后端处理完成
  const result = await uploadPromise;
  console.log(result);
};
```

#### 1.5 IPC 安全最佳实践（后端开发者必看）
1. **输入绝对校验**：所有从前端传入的参数，必须在 Rust 端做严格校验，禁止直接使用前端传入的路径、命令、SQL 语句，杜绝路径遍历、命令注入漏洞。
2. **最小权限原则**：仅开放业务必需的命令权限，禁止使用 `allow: ["*"]` 全量开放命令。
3. **敏感操作后端化**：文件读写、系统命令、数据库操作等敏感逻辑，必须全部放在 Rust 端实现，前端仅负责触发命令，禁止在前端硬编码敏感信息。
4. **禁止前端执行系统命令**：绝对不要开放「前端传入命令，后端直接执行」的接口，必须在后端白名单限制可执行的命令。
5. **CSP 策略加固**：在 `tauri.conf.json` 中严格配置 CSP，禁止内联脚本、eval 执行，避免 XSS 攻击导致的 IPC 权限泄露。

---

### 二、窗口管理
窗口是桌面应用的核心载体，Tauri 支持**静态配置**和**动态创建**两种窗口管理方式，支持多窗口、无边框、透明窗口、自定义标题栏等高级特性，完全覆盖桌面端所有窗口需求。

#### 2.1 窗口的静态配置（主窗口）
主窗口的基础配置在 `src-tauri/tauri.conf.json` 中完成，应用启动时自动创建，是最常用的配置方式：
```json
{
  "app": {
    "windows": [
      {
        "label": "main", // 窗口唯一标签，全局不可重复，核心标识
        "title": "我的Tauri应用", // 窗口标题
        "width": 1280, // 窗口宽度
        "height": 800, // 窗口高度
        "minWidth": 800, // 最小宽度
        "minHeight": 600, // 最小高度
        "x": null, // 窗口初始x坐标，null为居中
        "y": null, // 窗口初始y坐标，null为居中
        "resizable": true, // 是否允许调整窗口大小
        "fullscreen": false, // 是否全屏启动
        "focus": true, // 启动时是否聚焦
        "center": true, // 启动时是否居中
        "alwaysOnTop": false, // 是否置顶
        "decorations": true, // 是否显示系统标题栏（边框、关闭/最小化按钮）
        "transparent": false, // 窗口是否透明
        "visible": true, // 启动时是否显示窗口
        "icon": "icons/icon.png", // 窗口图标
        "skipTaskbar": false, // 是否隐藏任务栏图标
        "theme": "Auto" // 窗口主题：Light/Dark/Auto
      }
    ]
  }
}
```

#### 2.2 窗口的动态创建（多窗口）
通过 Rust 端代码可动态创建任意数量的子窗口，支持完全自定义配置，适合多窗口应用场景。

**实战示例：动态创建子窗口**

1. Rust 端创建窗口的命令：
```rust
// src-tauri/src/main.rs
use tauri::{command, AppHandle, WebviewWindowBuilder, WebviewUrl};
use tauri::window::WindowEvent;

#[command]
pub async fn create_child_window(app_handle: AppHandle) -> Result<(), String> {
    // 检查窗口是否已存在，避免重复创建
    if app_handle.get_webview_window("child").is_some() {
        return Err("子窗口已存在".to_string());
    }

    // 构建并创建子窗口
    let child_window = WebviewWindowBuilder::new(
        &app_handle,
        "child", // 窗口唯一标签，不可重复
        WebviewUrl::App("child.html".into()) // 窗口加载的前端页面
    )
    .title("子窗口")
    .inner_size(800.0, 600.0)
    .min_inner_size(600.0, 400.0)
    .center(true)
    .decorations(true)
    .always_on_top(false)
    .build()
    .map_err(|e| e.to_string())?;

    // 监听子窗口的事件
    child_window.on_window_event(move |event| {
        match event {
            WindowEvent::CloseRequested { api, .. } => {
                println!("子窗口即将关闭");
                // 可阻止窗口关闭：api.prevent_close();
            }
            WindowEvent::Focused(focused) => {
                println!("子窗口聚焦状态：{}", focused);
            }
            _ => {}
        }
    });

    Ok(())
}

// 注册命令
fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![create_child_window])
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

2. 前端调用创建窗口：
```typescript
import { invoke } from '@tauri-apps/api/core';

const openChildWindow = async () => {
  try {
    await invoke('create_child_window');
    console.log('子窗口创建成功');
  } catch (error) {
    console.error('创建失败：', error);
  }
};
```

#### 2.3 窗口的核心操作 API
Tauri 提供了前端和 Rust 端两套窗口操作 API，简单操作可直接用前端 API，复杂逻辑建议放在 Rust 端实现。

##### 前端窗口 API 常用操作
```typescript
import { getCurrentWindow, getAllWindows } from '@tauri-apps/api/window';

// 获取当前窗口实例
const currentWindow = getCurrentWindow();

// 基础窗口操作
await currentWindow.minimize(); // 最小化
await currentWindow.maximize(); // 最大化
await currentWindow.unmaximize(); // 还原最大化
await currentWindow.toggleMaximize(); // 切换最大化/还原
await currentWindow.close(); // 关闭窗口
await currentWindow.setAlwaysOnTop(true); // 置顶窗口
await currentWindow.setFocus(); // 聚焦窗口
await currentWindow.setVisible(false); // 隐藏窗口
await currentWindow.setSize({ width: 1000, height: 700 }); // 修改窗口大小
await currentWindow.setTitle('新标题'); // 修改窗口标题

// 获取所有打开的窗口
const allWindows = await getAllWindows();
console.log('所有窗口：', allWindows);

// 监听窗口事件
const unlisten = await currentWindow.listen('tauri://resize', () => {
  console.log('窗口大小发生变化');
});
```

##### Rust 端窗口 API 常用操作
```rust
use tauri::{AppHandle, Manager};

// 通过AppHandle获取窗口
pub fn window_operations(app_handle: &AppHandle) {
    // 获取指定标签的窗口
    if let Some(window) = app_handle.get_webview_window("main") {
        // 基础操作
        let _ = window.minimize();
        let _ = window.maximize();
        let _ = window.set_always_on_top(true);
        let _ = window.set_title("Rust端修改的标题");
        
        // 获取窗口状态
        let is_maximized = window.is_maximized().unwrap_or(false);
        let size = window.inner_size().unwrap();
        println!("窗口大小：{}x{}", size.width, size.height);
    }
}
```

#### 2.4 高级特性：无边框窗口与自定义标题栏
桌面应用开发中，为了实现个性化的 UI 设计，通常会关闭系统默认标题栏（`decorations: false`），自己实现自定义标题栏，这是 Tauri 开发的高频需求。

##### 完整实现步骤
1. 关闭系统标题栏，在 `tauri.conf.json` 中修改主窗口配置：
```json
{
  "app": {
    "windows": [
      {
        "label": "main",
        "decorations": false, // 关闭系统标题栏
        "transparent": false,
        "resizable": true
      }
    ]
  }
}
```

2. 前端实现自定义标题栏，核心是通过 `data-tauri-drag-region` 属性标记可拖拽区域：
```html
<!-- 自定义标题栏 -->
<div style="width: 100%; height: 40px; background: #2c3e50; display: flex; align-items: center; padding: 0 10px;">
  <!-- 拖拽区域：添加 data-tauri-drag-region 属性，即可实现窗口拖拽 -->
  <div data-tauri-drag-region style="flex: 1; height: 100%; display: flex; align-items: center; color: white;">
    我的应用
  </div>
  <!-- 窗口控制按钮 -->
  <div style="display: flex; gap: 10px;">
    <button id="minBtn" style="color: white; background: none; border: none; cursor: pointer;">—</button>
    <button id="maxBtn" style="color: white; background: none; border: none; cursor: pointer;">□</button>
    <button id="closeBtn" style="color: white; background: none; border: none; cursor: pointer;">×</button>
  </div>
</div>

<script type="module">
  import { getCurrentWindow } from '@tauri-apps/api/window';
  const appWindow = getCurrentWindow();

  // 绑定按钮事件
  document.getElementById('minBtn').addEventListener('click', () => appWindow.minimize());
  document.getElementById('maxBtn').addEventListener('click', () => appWindow.toggleMaximize());
  document.getElementById('closeBtn').addEventListener('click', () => appWindow.close());
</script>
```

##### 关键注意事项
- `data-tauri-drag-region` 标记的区域，默认会拦截鼠标点击事件，按钮、输入框等交互元素不要放在拖拽区域内。
- 无边框窗口在 Windows 上默认无法通过边缘拖拽缩放，需开启 `resizable: true`，Tauri 会自动处理边缘缩放。
- 多平台兼容：macOS 上建议保留窗口的圆角效果，Linux 上部分桌面环境对无边框窗口的支持存在差异。

#### 2.5 窗口管理最佳实践
1. **窗口标签唯一**：每个窗口的 `label` 必须全局唯一，这是窗口的核心标识，重复的标签会导致创建失败。
2. **窗口生命周期管理**：子窗口关闭时，要及时清理对应的事件监听、状态数据，避免内存泄漏。
3. **多窗口权限隔离**：不同业务的子窗口，通过 `capabilities` 配置独立的权限，避免子窗口越权调用敏感命令。
4. **避免重复创建窗口**：创建窗口前，先通过 `get_webview_window` 检查窗口是否已存在，避免重复创建。
5. **启动性能优化**：非核心窗口不要在应用启动时创建，采用懒加载方式，用户触发时再动态创建，提升启动速度。

---

### 三、系统托盘
系统托盘是桌面应用的必备能力，用于实现应用最小化到托盘、后台运行、快捷菜单操作等功能，Tauri 2.x 通过 `tauri-plugin-tray` 插件实现托盘能力，完全兼容 Windows/macOS/Linux 三大平台。

#### 3.1 插件安装与基础配置
1. 安装插件依赖：
   - Rust 端：在 `src-tauri/Cargo.toml` 中添加依赖
   ```toml
   [dependencies]
   tauri-plugin-tray = "2.0"
   ```
   - 前端端：安装 npm 包
   ```bash
   npm install @tauri-apps/plugin-tray
   ```

2. 注册插件：在 `src-tauri/src/main.rs` 中注册托盘插件
```rust
fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_tray::init()) // 注册托盘插件
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

3. 配置托盘图标：托盘图标必须是 `.ico`（Windows）、`.png`（macOS/Linux）格式，建议尺寸 32x32/64x64，放在 `src-tauri/icons/` 目录下。

#### 3.2 静态托盘配置（推荐）
在 `src-tauri/tauri.conf.json` 中配置托盘，应用启动时自动创建，无需编写 Rust 代码，适合固定菜单的场景：
```json
{
  "plugins": {
    "tray": {
      "icon": "icons/icon.ico", // 托盘图标，Windows用ico，macOS/Linux用png
      "iconAsTemplate": false, // macOS是否用模板图标（自动适配深色/浅色模式）
      "menu": [
        {
          "id": "show-main",
          "text": "打开主窗口",
          "enabled": true
        },
        {
          "id": "separator-1",
          "type": "separator" // 分隔线
        },
        {
          "id": "auto-start",
          "text": "开机自启",
          "checked": false,
          "type": "check" // 复选框菜单项
        },
        {
          "id": "separator-2",
          "type": "separator"
        },
        {
          "id": "quit",
          "text": "退出应用",
          "enabled": true
        }
      ],
      "tooltip": "我的Tauri应用" // 鼠标悬浮在托盘图标上的提示文本
    }
  },
  "permissions": [
    "tray:allow-show",
    "tray:allow-hide",
    "tray:allow-update-menu",
    "tray:allow-update-icon"
  ]
}
```

#### 3.3 托盘事件处理
通过监听托盘的菜单点击、图标点击事件，实现对应的业务逻辑，核心在 Rust 端处理事件。

**实战示例：完整的托盘事件处理**
```rust
// src-tauri/src/main.rs
use tauri::Manager;
use tauri_plugin_tray::TrayEvent;

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_tray::init())
        .setup(|app| {
            // 获取托盘实例
            let tray_handle = app.tray_handle();
            let app_handle = app.handle().clone();

            // 监听托盘所有事件
            tray_handle.on_tray_event(move |event| {
                match event {
                    // 菜单项点击事件
                    TrayEvent::MenuClicked { id } => {
                        match id.as_str() {
                            "show-main" => {
                                // 显示并聚焦主窗口
                                if let Some(window) = app_handle.get_webview_window("main") {
                                    let _ = window.show();
                                    let _ = window.set_focus();
                                    let _ = window.unminimize();
                                }
                            }
                            "auto-start" => {
                                // 切换开机自启状态，这里可实现开机自启逻辑
                                println!("开机自启状态切换");
                            }
                            "quit" => {
                                // 退出应用
                                app_handle.exit(0);
                            }
                            _ => {}
                        }
                    }
                    // 托盘图标左键点击事件
                    TrayEvent::LeftClick { .. } => {
                        println!("托盘图标左键点击");
                        // 左键点击显示/隐藏主窗口
                        if let Some(window) = app_handle.get_webview_window("main") {
                            if window.is_visible().unwrap_or(false) {
                                let _ = window.hide();
                            } else {
                                let _ = window.show();
                                let _ = window.set_focus();
                            }
                        }
                    }
                    // 托盘图标右键点击事件
                    TrayEvent::RightClick { .. } => {
                        println!("托盘图标右键点击");
                    }
                    _ => {}
                }
            });

            Ok(())
        })
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

#### 3.4 托盘的动态更新
支持运行时动态修改托盘图标、菜单、提示文本，适合根据应用状态更新托盘的场景。

##### Rust 端动态更新示例
```rust
use tauri::Manager;
use tauri_plugin_tray::{TrayHandle, MenuItem, MenuItemKind};

// 动态更新托盘菜单
pub fn update_tray_menu(tray_handle: &TrayHandle) {
    // 构建新的菜单
    let new_menu = vec![
        MenuItem {
            id: "show-main".to_string(),
            text: "打开主窗口".to_string(),
            enabled: true,
            kind: MenuItemKind::Standard,
            ..Default::default()
        },
        MenuItem {
            id: "separator".to_string(),
            kind: MenuItemKind::Separator,
            ..Default::default()
        },
        MenuItem {
            id: "running-status".to_string(),
            text: "服务运行中".to_string(),
            enabled: false,
            kind: MenuItemKind::Standard,
            ..Default::default()
        },
        MenuItem {
            id: "quit".to_string(),
            text: "退出应用".to_string(),
            enabled: true,
            kind: MenuItemKind::Standard,
            ..Default::default()
        },
    ];

    // 更新菜单
    tray_handle.set_menu(new_menu).unwrap();
}

// 动态更新托盘图标
pub fn update_tray_icon(tray_handle: &TrayHandle, icon_path: &str) {
    tray_handle.set_icon(icon_path).unwrap();
}

// 动态更新托盘提示文本
pub fn update_tray_tooltip(tray_handle: &TrayHandle, tooltip: &str) {
    tray_handle.set_tooltip(tooltip).unwrap();
}
```

##### 前端动态更新 API
```typescript
import { getTray } from '@tauri-apps/plugin-tray';

const tray = await getTray();
// 更新托盘提示文本
await tray.setTooltip('新的提示文本');
// 更新托盘图标
await tray.setIcon('icons/new-icon.ico');
```

#### 3.5 多平台兼容与最佳实践
1. **图标兼容**：
   - Windows：必须使用 `.ico` 格式图标，建议 32x32 尺寸
   - macOS：使用 64x64 的 PNG 图标，开启 `iconAsTemplate` 可自动适配深色/浅色模式
   - Linux：使用 PNG 图标，建议 48x48 尺寸
2. **行为兼容**：
   - Windows：左键点击默认弹出菜单，可通过事件自定义行为
   - macOS：左键点击默认触发点击事件，右键点击弹出菜单
   - Linux：不同桌面环境的托盘行为存在差异，建议做兼容性测试
3. **最佳实践**：
   - 应用关闭时，默认最小化到托盘，而非直接退出，提升用户体验
   - 托盘菜单不要超过 7 个选项，避免过于臃肿
   - 应用状态变化时，同步更新托盘图标/菜单，给用户明确的状态反馈
   - 必须提供「退出应用」的菜单项，避免用户无法关闭应用

---

### 四、文件系统
文件系统是桌面应用最核心的原生能力之一，Tauri 2.x 通过 `tauri-plugin-fs` 插件实现跨平台的文件/目录操作，相比浏览器的 File API，Tauri 可直接访问本地文件系统，无沙箱限制，同时通过**路径范围（Scope）** 严格控制访问权限，保证安全。

#### 4.1 插件安装与权限配置
1. 安装插件依赖：
   - Rust 端：`src-tauri/Cargo.toml`
   ```toml
   [dependencies]
   tauri-plugin-fs = "2.0"
   ```
   - 前端端：
   ```bash
   npm install @tauri-apps/plugin-fs
   ```

2. 注册插件：`src-tauri/src/main.rs`
```rust
fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_fs::init()) // 注册FS插件
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

3. **核心：路径范围 Scope 配置**（安全的核心）
Scope 是 Tauri 文件系统的安全机制，仅允许访问配置中明确允许的路径，禁止访问其他路径，生产环境必须遵循最小权限原则配置。

在 `src-tauri/tauri.conf.json` 中配置：
```json
{
  "plugins": {
    "fs": {
      "scope": [
        // 允许访问用户文档目录下的所有文件和子目录
        "$DOCUMENT/**",
        // 允许访问用户桌面目录
        "$DESKTOP/*",
        // 允许访问应用自身的数据目录
        "$APPDATA/**",
        // 允许访问指定的绝对路径（Windows示例）
        "C:\\Users\\*\\AppData\\Local\\my-app\\**",
        // 禁止访问的路径（优先级高于允许的路径）
        "!$DOCUMENT/secret/**"
      ]
    }
  },
  "permissions": [
    // 按需开启对应的API权限，禁止全量开启
    "fs:allow-read-text-file",
    "fs:allow-write-text-file",
    "fs:allow-read-binary-file",
    "fs:allow-write-binary-file",
    "fs:allow-create-dir",
    "fs:allow-read-dir",
    "fs:allow-remove",
    "fs:allow-rename",
    "fs:allow-metadata"
  ]
}
```

**常用路径变量**（跨平台兼容，无需硬编码绝对路径）：
| 变量名          | 对应路径                                   |
| --------------- | ------------------------------------------ |
| `$DOCUMENT`     | 用户文档目录                               |
| `$DESKTOP`      | 用户桌面目录                               |
| `$APPDATA`      | 应用专属数据目录（推荐存放应用配置、数据） |
| `$APPLOCALDATA` | 应用本地数据目录                           |
| `$CACHE`        | 应用缓存目录                               |
| `$HOME`         | 用户主目录                                 |
| `$PICTURE`      | 用户图片目录                               |
| `$VIDEO`        | 用户视频目录                               |
| `$AUDIO`        | 用户音频目录                               |
| `$DOWNLOAD`     | 用户下载目录                               |

#### 4.2 路径解析 API
跨平台开发中，绝对禁止硬编码路径，必须通过路径 API 解析系统标准路径，保证 Windows/macOS/Linux 兼容性。

##### 前端路径解析 API
```typescript
import { documentDir, appDataDir, homeDir, join } from '@tauri-apps/api/path';

// 获取系统标准路径
const docPath = await documentDir(); // 文档目录
const appDataPath = await appDataDir(); // 应用数据目录
const homePath = await homeDir(); // 用户主目录

// 拼接路径（跨平台兼容，自动处理/和\）
const configPath = await join(appDataPath, 'config', 'app.json');
console.log('配置文件路径：', configPath);
```

##### Rust 端路径解析 API
```rust
use tauri::Manager;
use tauri::path::BaseDirectory;

pub async fn get_config_path(app_handle: &tauri::AppHandle) -> String {
    // 解析应用数据目录下的config/app.json路径
    let path = app_handle.path()
        .resolve("config/app.json", BaseDirectory::AppData)
        .unwrap();
    path.to_string_lossy().to_string()
}
```

#### 4.3 核心文件操作 API
##### 4.3.1 文本文件读写（最常用）
```typescript
import { readTextFile, writeTextFile, exists } from '@tauri-apps/plugin-fs';
import { appDataDir, join } from '@tauri-apps/api/path';

// 写入文本文件
const saveConfig = async (config: object) => {
  // 获取配置文件路径
  const appDataPath = await appDataDir();
  const configPath = await join(appDataPath, 'app.json');
  
  // 写入文件，自动创建不存在的目录
  await writeTextFile(configPath, JSON.stringify(config, null, 2), {
    create: true, // 自动创建文件
    append: false // 覆盖写入，true为追加写入
  });
  console.log('配置保存成功');
};

// 读取文本文件
const loadConfig = async () => {
  const appDataPath = await appDataDir();
  const configPath = await join(appDataPath, 'app.json');
  
  // 检查文件是否存在
  const isExist = await exists(configPath);
  if (!isExist) {
    console.log('配置文件不存在');
    return null;
  }

  // 读取文件内容
  const content = await readTextFile(configPath);
  return JSON.parse(content);
};
```

##### 4.3.2 二进制文件读写（图片、视频、压缩包等）
```typescript
import { readBinaryFile, writeBinaryFile } from '@tauri-apps/plugin-fs';

// 写入二进制文件
const saveImage = async (imageData: Uint8Array, savePath: string) => {
  await writeBinaryFile(savePath, imageData);
  console.log('图片保存成功');
};

// 读取二进制文件
const loadImage = async (imagePath: string) => {
  const binaryData = await readBinaryFile(imagePath);
  // 转换为图片URL
  const blob = new Blob([binaryData]);
  const imageUrl = URL.createObjectURL(blob);
  return imageUrl;
};
```

##### 4.3.3 目录操作
```typescript
import { createDir, readDir, removeDir, exists } from '@tauri-apps/plugin-fs';
import { appDataDir, join } from '@tauri-apps/api/path';

// 创建目录，递归创建父目录
const createAppDir = async () => {
  const appDataPath = await appDataDir();
  const targetDir = await join(appDataPath, 'user', 'files');
  
  // 检查目录是否存在
  const isExist = await exists(targetDir);
  if (!isExist) {
    // 递归创建目录，类似mkdir -p
    await createDir(targetDir, { recursive: true });
    console.log('目录创建成功');
  }
};

// 读取目录内容
const listDir = async () => {
  const appDataPath = await appDataDir();
  const targetDir = await join(appDataPath, 'user');
  
  // 读取目录，递归读取子目录
  const entries = await readDir(targetDir, { recursive: true });
  console.log('目录内容：', entries);
  // 每个entry包含：name（文件名）、path（绝对路径）、isDirectory（是否目录）、isFile（是否文件）
};

// 删除目录
const deleteDir = async () => {
  const appDataPath = await appDataDir();
  const targetDir = await join(appDataPath, 'user');
  
  // 递归删除目录及所有内容，类似rm -rf
  await removeDir(targetDir, { recursive: true });
  console.log('目录删除成功');
};
```

##### 4.3.4 其他常用操作
```typescript
import { remove, rename, metadata, copyFile } from '@tauri-apps/plugin-fs';

// 删除文件/目录
await remove('path/to/file.txt');

// 重命名/移动文件/目录
await rename('path/old-name.txt', 'path/new-name.txt');

// 获取文件/目录元信息
const meta = await metadata('path/to/file.txt');
console.log('文件大小：', meta.size); // 字节数
console.log('创建时间：', meta.createdAt);
console.log('修改时间：', meta.modifiedAt);
console.log('是否是文件：', meta.isFile);
console.log('是否是目录：', meta.isDirectory);

// 复制文件
await copyFile('source/file.txt', 'target/file.txt');
```

##### 4.4 大文件流式读写
对于 GB 级的大文件，禁止一次性读取到内存中，必须使用流式读写，避免内存溢出。

**实战示例：大文件流式读取**
```typescript
import { open, readAll } from '@tauri-apps/plugin-fs';

// 流式读取大文件
const readLargeFile = async (filePath: string) => {
  // 打开文件，获取文件句柄
  const file = await open(filePath, { read: true });
  const chunkSize = 1024 * 1024; // 1MB 分片
  const buffer = new Uint8Array(chunkSize);

  try {
    let bytesRead;
    // 循环读取分片
    while ((bytesRead = await file.read(buffer)) > 0) {
      const chunk = buffer.slice(0, bytesRead);
      // 处理分片数据，比如计算哈希、上传、解析等
      console.log('读取分片大小：', bytesRead);
    }
    console.log('文件读取完成');
  } finally {
    // 关闭文件句柄
    await file.close();
  }
};
```

#### 4.5 Rust 端文件操作
对于敏感的文件操作，建议放在 Rust 端实现，避免前端越权访问，同时可以实现更复杂的业务逻辑。

**Rust 端文件操作示例**

```rust
use std::fs;
use std::path::PathBuf;
use tauri::{command, AppHandle, path::BaseDirectory};
use serde::{Serialize, Deserialize};
use thiserror::Error;

// 自定义错误类型
#[derive(Debug, Error)]
pub enum FsError {
    #[error("IO错误：{0}")]
    IoError(#[from] std::io::Error),
    #[error("JSON序列化错误：{0}")]
    JsonError(#[from] serde_json::Error),
    #[error("路径解析错误")]
    PathError,
}

// 配置结构体
#[derive(Debug, Serialize, Deserialize)]
pub struct AppConfig {
    pub theme: String,
    pub auto_start: bool,
    pub language: String,
}

// 读取配置文件
#[command]
pub async fn load_app_config(app_handle: AppHandle) -> Result<AppConfig, FsError> {
    // 解析配置文件路径
    let config_path = app_handle.path()
        .resolve("app.json", BaseDirectory::AppData)
        .map_err(|_| FsError::PathError)?;

    // 检查文件是否存在
    if !config_path.exists() {
        // 返回默认配置
        return Ok(AppConfig {
            theme: "auto".to_string(),
            auto_start: false,
            language: "zh-CN".to_string(),
        });
    }

    // 读取并解析配置文件
    let content = fs::read_to_string(config_path)?;
    let config = serde_json::from_str(&content)?;
    Ok(config)
}

// 保存配置文件
#[command]
pub async fn save_app_config(app_handle: AppHandle, config: AppConfig) -> Result<(), FsError> {
    let config_path = app_handle.path()
        .resolve("app.json", BaseDirectory::AppData)
        .map_err(|_| FsError::PathError)?;

    // 确保父目录存在
    if let Some(parent) = config_path.parent() {
        fs::create_dir_all(parent)?;
    }

    // 序列化并写入文件
    let content = serde_json::to_string_pretty(&config)?;
    fs::write(config_path, content)?;
    Ok(())
}
```

#### 4.6 文件系统安全最佳实践
1. **最小权限 Scope 配置**：仅开放业务必需的路径，禁止使用 `$HOME/**` 全量开放用户目录，禁止开放系统敏感路径。
2. **路径遍历防护**：所有前端传入的路径，必须在 Rust 端做规范化处理，禁止直接拼接路径，杜绝 `../` 路径遍历漏洞。
3. **敏感操作后端化**：配置文件、用户数据、业务文件的读写，必须放在 Rust 端实现，前端仅调用封装好的命令，禁止前端直接操作绝对路径。
4. **禁止硬编码路径**：所有路径必须通过系统路径 API 解析，保证跨平台兼容性，绝对禁止硬编码 Windows 的 `C:\` 或 macOS 的 `/Users/` 路径。
5. **大文件流式处理**：超过 100MB 的文件，必须使用流式读写，避免一次性加载到内存中导致应用崩溃。
6. **文件权限控制**：写入文件时，设置合理的文件权限，避免创建权限过高的文件。

---

### 五、原生通知
Tauri 通过 `tauri-plugin-notification` 插件实现跨平台的系统原生通知能力，支持 Windows 通知中心、macOS 通知中心、Linux libnotify，完全兼容系统原生样式，支持点击事件、通知动作、声音等高级特性。

#### 5.1 插件安装与权限配置
1. 安装插件依赖：
   - Rust 端：`src-tauri/Cargo.toml`
   ```toml
   [dependencies]
   tauri-plugin-notification = "2.0"
   ```
   - 前端端：
   ```bash
   npm install @tauri-apps/plugin-notification
   ```

2. 注册插件：`src-tauri/src/main.rs`
```rust
fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_notification::init()) // 注册通知插件
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

3. 权限配置：`src-tauri/tauri.conf.json`
```json
{
  "permissions": [
    "notification:allow-send",
    "notification:allow-request-permission",
    "notification:allow-is-permission-granted"
  ]
}
```

4. macOS 额外配置：在 `tauri.conf.json` 中添加应用配置，开启通知权限
```json
{
  "app": {
    "macOSPrivateApi": true
  }
}
```

#### 5.2 基础通知发送
发送通知前，必须先申请用户权限，只有权限授权通过后，才能正常发送通知。

**完整示例：基础通知发送**
```typescript
import { 
  sendNotification, 
  requestPermission, 
  isPermissionGranted,
  PermissionStatus
} from '@tauri-apps/plugin-notification';

// 发送通知的完整流程
const sendSystemNotification = async (title: string, body: string) => {
  // 1. 检查是否已授权
  let permissionGranted = await isPermissionGranted();
  
  // 2. 未授权则申请权限
  if (!permissionGranted) {
    const permission = await requestPermission();
    permissionGranted = permission === PermissionStatus.Granted;
  }

  // 3. 授权通过，发送通知
  if (permissionGranted) {
    await sendNotification({
      title: title, // 通知标题（必填）
      body: body, // 通知正文（必填）
      icon: 'icons/icon.png', // 通知图标，可选
      sound: 'default', // 通知声音，可选
      silent: false, // 是否静音，默认false
      // 通知ID，用于后续更新/关闭通知
      id: 1001
    });
    console.log('通知发送成功');
  } else {
    console.error('通知权限被拒绝');
  }
};

// 调用示例
sendSystemNotification('任务完成', '您的文件已成功下载完成！');
```

#### 5.3 通知事件监听
支持监听通知的点击事件，用户点击通知时，可执行对应的业务逻辑（如打开窗口、跳转到指定页面）。

**示例：监听通知点击事件**
```typescript
import { addEventListener } from '@tauri-apps/api/event';

// 应用启动时注册监听
const initNotificationListener = async () => {
  // 监听通知点击事件
  await addEventListener('notification:click', (event) => {
    console.log('通知被点击，通知ID：', event.payload.id);
    // 执行对应的业务逻辑
    const mainWindow = getCurrentWindow();
    mainWindow.show();
    mainWindow.setFocus();
  });

  // 监听通知关闭事件
  await addEventListener('notification:close', (event) => {
    console.log('通知被关闭，通知ID：', event.payload.id);
  });
};

// 页面加载时初始化
initNotificationListener();
```

#### 5.4 多平台兼容与最佳实践
1. **权限申请时机**：不要在应用启动时就申请通知权限，应该在用户第一次使用通知相关功能时申请，提升授权通过率。
2. **通知频率控制**：不要频繁发送无意义的通知，避免用户屏蔽应用的通知权限。
3. **平台兼容**：
   - Windows：需要应用安装后才能正常显示通知，开发环境下可能出现通知不显示的问题。
   - macOS：必须开启 `macOSPrivateApi: true`，否则无法监听通知点击事件。
   - Linux：依赖系统的 libnotify 库，部分精简系统可能不支持。
4. **通知内容规范**：通知标题简洁明了，正文控制在 2 行以内，避免过长内容被截断。
5. **业务场景适配**：仅在需要用户关注的场景发送通知，如任务完成、异常提醒、消息提醒，禁止滥用通知。

---

### 六、全局快捷键
全局快捷键（热键）是桌面应用的常用能力，支持在应用未聚焦、最小化到托盘的情况下，响应用户的键盘快捷键操作，Tauri 2.x 通过 `tauri-plugin-global-shortcut` 插件实现跨平台全局快捷键能力。

#### 6.1 插件安装与权限配置
1. 安装插件依赖：
   - Rust 端：`src-tauri/Cargo.toml`
   ```toml
   [dependencies]
   tauri-plugin-global-shortcut = "2.0"
   ```
   - 前端端：
   ```bash
   npm install @tauri-apps/plugin-global-shortcut
   ```

2. 注册插件：`src-tauri/src/main.rs`
```rust
fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_global_shortcut::init()) // 注册全局快捷键插件
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

3. 权限配置：`src-tauri/tauri.conf.json`
```json
{
  "permissions": [
    "global-shortcut:allow-register",
    "global-shortcut:allow-unregister",
    "global-shortcut:allow-is-registered"
  ]
}
```

#### 6.2 快捷键注册与事件监听
##### 6.2.1 前端 API 实现
```typescript
import { register, unregister, isRegistered } from '@tauri-apps/plugin-global-shortcut';

// 注册单个快捷键
const registerShortcut = async () => {
  // 快捷键格式：Ctrl+Shift+A，支持的修饰符：Ctrl/Alt/Shift/Meta，支持的按键：字母、数字、F1-F12、空格、回车等
  const shortcut = 'Ctrl+Shift+A';

  // 检查快捷键是否已注册
  const isReg = await isRegistered(shortcut);
  if (isReg) {
    console.log('快捷键已注册');
    return;
  }

  // 注册快捷键
  await register(shortcut, (event) => {
    console.log('快捷键被触发：', event.shortcut);
    // 执行业务逻辑：打开主窗口、截图、播放/暂停等
    const mainWindow = getCurrentWindow();
    mainWindow.show();
    mainWindow.setFocus();
  });

  console.log('快捷键注册成功');
};

// 注册多个快捷键
const registerMultiShortcuts = async () => {
  const shortcuts = ['Ctrl+Shift+S', 'Alt+F12', 'MediaPlayPause'];
  
  await register(shortcuts, (event) => {
    switch (event.shortcut) {
      case 'Ctrl+Shift+S':
        console.log('执行保存操作');
        break;
      case 'Alt+F12':
        console.log('打开开发者工具');
        break;
      case 'MediaPlayPause':
        console.log('播放/暂停');
        break;
    }
  });
};

// 注销快捷键
const unregisterShortcut = async (shortcut: string) => {
  await unregister(shortcut);
  console.log('快捷键已注销');
};

// 注销所有快捷键
const unregisterAllShortcuts = async () => {
  await unregisterAll();
  console.log('所有快捷键已注销');
};
```

##### 6.2.2 Rust 端实现
生产环境中，建议在 Rust 端注册全局快捷键，避免前端页面刷新导致快捷键失效。

```rust
// src-tauri/src/main.rs
use tauri::Manager;
use tauri_plugin_global_shortcut::GlobalShortcutExt;

fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_global_shortcut::init())
        .setup(|app| {
            // 获取全局快捷键管理器
            let shortcut_manager = app.global_shortcut();
            let app_handle = app.handle().clone();

            // 注册快捷键
            let shortcut = "Ctrl+Shift+A";
            shortcut_manager.on_shortcut(shortcut, move |_app, _shortcut| {
                println!("快捷键 Ctrl+Shift+A 被触发");
                // 显示主窗口
                if let Some(window) = app_handle.get_webview_window("main") {
                    let _ = window.show();
                    let _ = window.set_focus();
                }
            }).unwrap();

            println!("全局快捷键注册成功");
            Ok(())
        })
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

#### 6.3 多平台兼容与最佳实践
1. **快捷键规范**：
   - Windows/Linux：优先使用 `Ctrl` 作为主修饰符，避免使用 `Meta`（Win键）
   - macOS：优先使用 `Meta`（Command键）作为主修饰符，符合 macOS 用户习惯
   - 避免使用系统保留的快捷键，如 `Ctrl+Alt+Del`、`Ctrl+C`、`Ctrl+V` 等
2. **冲突处理**：注册快捷键前，先检查是否已注册，避免和其他应用的快捷键冲突；提供快捷键自定义功能，允许用户修改冲突的快捷键。
3. **生命周期管理**：应用退出时，自动注销所有注册的快捷键，避免残留；应用最小化到托盘时，保持快捷键生效，应用完全退出时注销。
4. **用户提示**：快捷键注册成功/失败时，给用户明确的提示；快捷键触发时，给用户对应的反馈。
5. **权限问题**：
   - Linux：部分桌面环境需要 root 权限才能注册全局快捷键
   - macOS：需要在「系统设置-隐私与安全性-辅助功能」中允许应用控制电脑，否则快捷键无法触发
   - Windows：无需额外权限，普通用户即可注册

---

### 七、自动更新
自动更新是桌面应用的核心能力，Tauri 官方提供了 `tauri-plugin-updater` 插件，开箱即用实现跨平台的应用自动更新，支持增量更新、进度回调、签名校验、静默更新等高级特性，完全覆盖生产级更新需求。

#### 7.1 核心原理
Tauri 自动更新的核心流程：
1. 应用启动时，向更新服务器请求 `update.json` 元数据，对比当前版本与最新版本
2. 若有新版本，下载对应的更新包，校验签名，确保更新包未被篡改
3. 校验通过后，安装更新，重启应用完成更新
4. 全程支持进度回调、断点续传、更新失败回滚

#### 7.2 插件安装与配置
1. 安装插件依赖：
   - Rust 端：`src-tauri/Cargo.toml`
   ```toml
   [dependencies]
   tauri-plugin-updater = "2.0"
   ```
   - 前端端：
   ```bash
   npm install @tauri-apps/plugin-updater
   ```

2. 注册插件：`src-tauri/src/main.rs`
```rust
fn main() {
    tauri::Builder::default()
        .plugin(tauri_plugin_updater::Builder::new().build()) // 注册更新插件
        .run(tauri::generate_context!())
        .expect("应用启动失败");
}
```

3. 核心配置：`src-tauri/tauri.conf.json`
```json
{
  "plugins": {
    "updater": {
      "endpoints": [
        // 更新服务器地址，返回update.json元数据，支持https
        "https://update.example.com/my-app/update.json"
      ],
      "pubkey": "你的公钥", // 签名公钥，用于校验更新包
      "windows": {
        "installMode": "passive" // Windows安装模式：passive/quiet/attended
      },
      "macOS": {
        "installerMode": "passive"
      }
    }
  },
  "permissions": [
    "updater:allow-check",
    "updater:allow-download",
    "updater:allow-install",
    "updater:allow-download-and-install"
  ]
}
```

#### 7.3 生成签名密钥对
更新包必须使用 Tauri 签名工具生成的密钥对进行签名，防止更新包被篡改，这是自动更新的安全核心。

1. 安装 Tauri CLI（已安装可跳过）：
```bash
npm install -g @tauri-apps/cli
```

2. 生成签名密钥对：
```bash
tauri signer generate
```
执行后会生成：
- 公钥（pubkey）：复制到 `tauri.conf.json` 的 `pubkey` 字段
- 私钥（private key）：妥善保存，不要泄露，用于签名更新包
- 同时会提示你设置私钥密码，务必记住

3. 配置环境变量：
打包时需要用到私钥，配置环境变量：
- Windows：`set TAURI_SIGNING_PRIVATE_KEY="你的私钥内容"`
- macOS/Linux：`export TAURI_SIGNING_PRIVATE_KEY="你的私钥内容"`
- 私钥密码：`export TAURI_SIGNING_PRIVATE_KEY_PASSWORD="你的私钥密码"`

#### 7.4 构建更新包与更新元数据
1. 执行构建命令，生成带签名的更新包：
```bash
npm run tauri build
```
构建完成后，更新包和签名文件会生成在：
- Windows：`src-tauri/target/release/bundle/msi/` 下的 `.msi` 安装包和 `.msi.sig` 签名文件
- macOS：`src-tauri/target/release/bundle/dmg/` 下的 `.dmg` 安装包和 `.tar.gz` 更新包、签名文件
- Linux：`src-tauri/target/release/bundle/appimage/` 下的 `.AppImage` 安装包和签名文件

2. 编写更新元数据 `update.json`：
将构建好的更新包上传到你的更新服务器，编写 `update.json` 文件，放在更新服务器的对应地址：
```json
{
  "version": "1.0.1", // 最新版本号，必须大于当前应用版本
  "notes": "1. 修复了已知bug\n2. 新增了XX功能\n3. 优化了性能", // 更新说明
  "pub_date": "2024-05-20T00:00:00Z", // 发布时间，ISO格式
  "platforms": {
    // Windows x64
    "windows-x86_64": {
      "signature": "更新包的签名内容，从.sig文件中复制",
      "url": "https://update.example.com/my-app/my-app_1.0.1_x64_en-US.msi"
    },
    // macOS aarch64（M系列芯片）
    "darwin-aarch64": {
      "signature": "更新包的签名内容",
      "url": "https://update.example.com/my-app/my-app_1.0.1_aarch64.dmg"
    },
    // macOS x86_64（Intel芯片）
    "darwin-x86_64": {
      "signature": "更新包的签名内容",
      "url": "https://update.example.com/my-app/my-app_1.0.1_x64.dmg"
    },
    // Linux x64
    "linux-x86_64": {
      "signature": "更新包的签名内容",
      "url": "https://update.example.com/my-app/my-app_1.0.1_amd64.AppImage"
    }
  }
}
```

#### 7.5 完整的更新流程实现
前端实现完整的更新检查、下载、安装流程，包含进度回调、错误处理：
```typescript
import { checkUpdate, installUpdate } from '@tauri-apps/plugin-updater';
import { relaunch } from '@tauri-apps/api/process';
import { message, confirm } from '@tauri-apps/api/dialog';

// 完整的自动更新流程
const checkForUpdate = async () => {
  try {
    // 1. 检查更新
    console.log('开始检查更新...');
    const update = await checkUpdate();

    // 2. 无新版本
    if (!update.available) {
      await message('当前已是最新版本', { title: '更新检查', type: 'info' });
      return;
    }

    // 3. 有新版本，询问用户是否更新
    const shouldUpdate = await confirm(
      `发现新版本 v${update.version}\n\n更新说明：\n${update.body}`,
      {
        title: '发现新版本',
        type: 'info',
        okLabel: '立即更新',
        cancelLabel: '稍后更新'
      }
    );

    if (!shouldUpdate) {
      return;
    }

    // 4. 下载并安装更新，监听下载进度
    await installUpdate((progressEvent) => {
      switch (progressEvent.event) {
        case 'Started':
          console.log('开始下载更新包，总大小：', progressEvent.data.contentLength);
          break;
        case 'Progress':
          // 下载进度，chunkLength是当前下载的字节数
          console.log('已下载：', progressEvent.data.chunkLength);
          break;
        case 'Finished':
          console.log('更新包下载完成，正在安装...');
          break;
      }
    });

    // 5. 安装完成，询问用户是否重启应用
    const shouldRelaunch = await confirm(
      '更新安装完成，需要重启应用才能生效，是否立即重启？',
      {
        title: '更新完成',
        type: 'info',
        okLabel: '立即重启',
        cancelLabel: '稍后重启'
      }
    );

    if (shouldRelaunch) {
      await relaunch(); // 重启应用
    }

  } catch (error) {
    console.error('更新失败：', error);
    await message(`更新失败：${error}`, { title: '更新错误', type: 'error' });
  }
};

// 应用启动时自动检查更新
checkForUpdate();
```

#### 7.6 进阶用法
1. **静默更新**：无需用户确认，后台自动下载安装更新，应用重启时生效，适合强制更新场景：
```typescript
const silentUpdate = async () => {
  const update = await checkUpdate();
  if (update.available) {
    await installUpdate();
    console.log('更新已在后台安装完成，下次重启生效');
  }
};
```

2. **强制更新**：低于最低版本的应用，必须更新才能使用：
```typescript
const forceUpdateCheck = async () => {
  const update = await checkUpdate();
  // 从update.body中解析最低版本号，判断是否需要强制更新
  const minVersion = '1.0.0';
  const currentVersion = await getVersion();
  
  if (compareVersion(currentVersion, minVersion) < 0) {
    await message('当前版本过低，必须更新才能继续使用', { title: '强制更新', type: 'error' });
    await installUpdate();
    await relaunch();
  }
};
```

#### 7.7 多平台兼容与最佳实践
1. **代码签名要求**：
   - Windows：更新包必须进行代码签名，否则会被 Windows Defender 拦截，无法安装
   - macOS：更新包必须进行代码签名和公证，否则 macOS 会阻止应用运行
   - Linux：无强制签名要求，但建议进行 GPG 签名
2. **更新服务器要求**：
   - 更新服务器必须使用 HTTPS 协议，禁止使用 HTTP
   - 服务器支持断点续传，提升大更新包的下载体验
   - 建议使用 CDN 加速更新包下载，提升用户体验
3. **版本号规范**：必须使用语义化版本号（SemVer），格式为 `主版本.次版本.补丁版本`，如 `1.0.1`，Tauri 会严格按照语义化版本对比版本号。
4. **灰度发布**：通过更新服务器的配置，实现灰度发布，先给部分用户推送新版本，验证无问题后全量发布。
5. **更新失败处理**：必须处理更新失败的场景，提供手动下载更新包的入口，避免用户无法更新。
6. **增量更新**：对于大体积应用，建议使用增量更新，仅下载变更的文件，减少下载体积，Tauri 2.x 原生支持增量更新。



## 官方资源与学习拓展

1. **Tauri 官方中文文档**：https://tauri.app/zh-cn/
2. **Tauri 官方 GitHub 仓库**：https://github.com/tauri-apps/tauri
3. **Awesome Tauri**：https://github.com/tauri-apps/awesome-tauri （包含大量优秀的开源项目、插件、教程）
4. **Tauri 官方 Discord 社区**：https://discord.com/invite/tauri （可直接和开发团队、社区开发者交流提问）