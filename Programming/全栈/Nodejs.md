# Node.js 完全指南

npm/pnpm配置
```
npm config list
# JSON输出 
npm config list --json

pnpm config list 
# JSON输出 
pnpm config list --json
pnpm config get registry      # 镜像源
pnpm config get store-dir     # pnpm内容寻址存储目录
pnpm config get global-dir    # 全局安装目录
pnpm config get cache-dir     # 缓存目录
pnpm store path               # 快速查看store真实路径
```
## **nrm**
它是专门用来管理、切换 npm 全局镜像源的工具，能一键切换不同源（比如官方源、淘宝源），比手动改 `npm config` 更方便。

### 安装 nrm（全局安装）
```bash
npm install -g nrm
```

### nrm 的常用命令（核心功能）
- **查看所有可用的镜像源**：
  ```bash
  nrm ls
  ```
  执行后会列出常用源（比如 `npm` 对应官方源、`taobao` 对应淘宝镜像源（即 npmmirror））。

- **切换全局镜像源**（比如切换到淘宝源）：
  ```bash
  nrm use taobao
  ```

- **测试各源的访问速度**（选最快的源）：
  ```bash
  nrm test
  ```

- **查看当前使用的源**：
  ```bash
  nrm current
  ```

- **添加自定义源**：
  ```bash
  # 如果有私有 npm 源，可手动添加：
  nrm add 自定义源名称 源地址
  # 示例：nrm add myregistry https://registry.mycompany.com
  ```

- **删除自定义源**：
  ```bash
  nrm del 自定义源名称
  ```


## Node.js 基础

### 安装
Node.js 提供了多种安装方式，推荐使用官方安装包或版本管理器（如 nvm）以灵活切换版本。

#### 官方安装包（推荐新手）
1. 访问 [Node.js 官网](sslocal://flow/file_open?url=https%3A%2F%2Fnodejs.org%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)，下载 LTS（长期支持）版本（稳定可靠）或 Current（最新特性）版本。
2. 运行安装包，按提示完成安装（默认会同时安装 npm）。
3. 验证安装：打开终端，输入 `node -v` 和 `npm -v`，若显示版本号则安装成功。

#### 版本管理器 nvm（推荐开发者）
nvm 允许在同一台机器上安装和切换多个 Node.js 版本。
- **安装 nvm**：
  - Windows：下载 [nvm-windows](sslocal://flow/file_open?url=https%3A%2F%2Fgithub.com%2Fcoreybutler%2Fnvm-windows%2Freleases&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=) 安装包。
  - macOS/Linux：通过 curl 安装：`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash`。
- **使用 nvm**：
  ```bash
  nvm install 18.17.0  # 安装指定版本
  nvm use 18.17.0      # 切换到指定版本
  nvm ls                # 查看已安装的版本
  ```


### fs 模块（文件系统）
fs 模块用于操作文件和目录，提供同步和异步两种 API（异步 API 更推荐，避免阻塞事件循环）。

#### 导入 fs 模块
```javascript
const fs = require("fs");
```

#### 常用方法
- **读取文件**：
  ```javascript
  // 异步读取
  fs.readFile("文件路径", "utf-8", (err, data) => {
    if (err) return console.error("读取失败：", err);
    console.log("文件内容：", data);
  });
  
  // 同步读取（会阻塞，慎用）
  try {
    const data = fs.readFileSync("文件路径", "utf-8");
    console.log("文件内容：", data);
  } catch (err) {
    console.error("读取失败：", err);
  }
  ```

- **写入文件**（覆盖写入）：
  ```javascript
  fs.writeFile("文件路径", "写入内容", "utf-8", (err) => {
    if (err) return console.error("写入失败：", err);
    console.log("写入成功");
  });
  ```

- **追加内容**：
  ```javascript
  fs.appendFile("文件路径", "追加内容", "utf-8", (err) => {
    if (err) return console.error("追加失败：", err);
    console.log("追加成功");
  });
  ```

- **读取目录**：
  ```javascript
  fs.readdir("目录路径", (err, files) => {
    if (err) return console.error("读取目录失败：", err);
    console.log("目录内容：", files);
  });
  ```

- **创建目录**：
  ```javascript
  fs.mkdir("新目录路径", { recursive: true }, (err) => {
    if (err) return console.error("创建目录失败：", err);
    console.log("目录创建成功");
  });
  // recursive: true 表示递归创建父目录（如不存在）
  ```

- **删除文件/目录**：
  ```javascript
  // 删除文件
  fs.unlink("文件路径", (err) => {
    if (err) return console.error("删除文件失败：", err);
    console.log("文件删除成功");
  });
  
  // 删除目录（仅空目录）
  fs.rmdir("目录路径", (err) => {
    if (err) return console.error("删除目录失败：", err);
    console.log("目录删除成功");
  });
  
  // 递归删除非空目录（Node.js 14.14+）
  fs.rm("目录路径", { recursive: true, force: true }, (err) => {
    if (err) return console.error("删除目录失败：", err);
    console.log("目录删除成功");
  });
  ```


### path 模块（路径处理）
path 模块用于处理文件路径，解决不同操作系统的路径分隔符问题（Windows 用 `\`，macOS/Linux 用 `/`）。

#### 导入 path 模块
```javascript
const path = require("path");
```

#### 常用方法
- **路径拼接**：
  ```javascript
  const fullPath = path.join(__dirname, "文件夹", "文件.txt");
  // __dirname 是当前文件所在目录的绝对路径
  console.log(fullPath); // 输出类似：/Users/xxx/项目/文件夹/文件.txt
  ```

- **解析绝对路径**：
  ```javascript
  const absolutePath = path.resolve("文件夹", "文件.txt");
  // 以当前工作目录为基准，解析出绝对路径
  console.log(absolutePath);
  ```

- **获取文件名**：
  ```javascript
  const filename = path.basename("/path/to/file.txt");
  console.log(filename); // 输出：file.txt
  
  // 去掉扩展名
  const filenameWithoutExt = path.basename("/path/to/file.txt", ".txt");
  console.log(filenameWithoutExt); // 输出：file
  ```

- **获取扩展名**：
  ```javascript
  const ext = path.extname("/path/to/file.txt");
  console.log(ext); // 输出：.txt
  ```

- **解析路径信息**：
  ```javascript
  const pathInfo = path.parse("/path/to/file.txt");
  console.log(pathInfo);
  /* 输出：
  {
    root: '/',
    dir: '/path/to',
    base: 'file.txt',
    ext: '.txt',
    name: 'file'
  }
  */
  ```


### http 模块（创建 Web 服务器）
http 模块是 Node.js 内置的 HTTP 服务器/客户端工具，可用于构建基础的 Web 应用。

#### 创建简单的 Web 服务器
```javascript
const http = require("http");

// 创建服务器
const server = http.createServer((req, res) => {
  // req：请求对象（包含客户端请求信息）
  // res：响应对象（用于向客户端返回数据）

  // 设置响应头（解决中文乱码）
  res.setHeader("Content-Type", "text/html; charset=utf-8");

  // 根据请求 URL 返回不同内容
  if (req.url === "/") {
    res.end("<h1>首页</h1>");
  } else if (req.url === "/about") {
    res.end("<h1>关于我们</h1>");
  } else {
    res.statusCode = 404; // 设置 404 状态码
    res.end("<h1>404 页面不存在</h1>");
  }
});

// 启动服务器，监听 3000 端口
server.listen(3000, () => {
  console.log("服务器运行在 http://localhost:3000");
});
```

#### 处理 POST 请求数据
```javascript
const server = http.createServer((req, res) => {
  if (req.method === "POST" && req.url === "/submit") {
    let body = "";
    // 监听 data 事件，接收请求体数据
    req.on("data", (chunk) => {
      body += chunk;
    });
    // 监听 end 事件，数据接收完成后处理
    req.on("end", () => {
      console.log("接收到的数据：", body);
      res.end("数据提交成功");
    });
  }
});
```


### events 模块（事件驱动）
Node.js 是事件驱动的，events 模块是核心，几乎所有模块都继承自它。

#### 导入 events 模块并使用
```javascript
const EventEmitter = require("events");

// 创建事件发射器实例
const emitter = new EventEmitter();

// 监听事件
emitter.on("greet", (name) => {
  console.log(`你好，${name}！`);
});

// 触发事件
emitter.emit("greet", "张三"); // 输出：你好，张三！

// 只监听一次事件
emitter.once("onceGreet", () => {
  console.log("这个事件只会触发一次");
});
emitter.emit("onceGreet"); // 触发
emitter.emit("onceGreet"); // 不触发
```


### stream 模块（流处理）
stream 用于处理大文件（如视频、日志），避免一次性加载到内存导致溢出。

#### 常用流类型
- **可读流**（Readable）：读取数据（如 `fs.createReadStream`）。
- **可写流**（Writable）：写入数据（如 `fs.createWriteStream`）。
- **双工流**（Duplex）：既可读又可写（如 TCP socket）。
- **转换流**（Transform）：在读写过程中转换数据（如 `zlib.createGzip`）。

#### 示例：复制大文件
```javascript
const fs = require("fs");

// 创建可读流和可写流
const readStream = fs.createReadStream("大文件.txt");
const writeStream = fs.createWriteStream("大文件-副本.txt");

// 管道传输（自动处理流的结束和错误）
readStream.pipe(writeStream);

// 监听进度
readStream.on("data", (chunk) => {
  console.log(`读取了 ${chunk.length} 字节数据`);
});

readStream.on("end", () => {
  console.log("文件读取完成");
});

readStream.on("error", (err) => {
  console.error("读取错误：", err);
});
```


### querystring 处理查询字符串
用于解析和格式化 URL 查询字符串。

#### 使用示例
```javascript
const qs = require("querystring");

// 解析查询字符串
const queryStr = "name=张三&age=25";
const queryObj = qs.parse(queryStr);
console.log(queryObj); // 输出：{ name: '张三', age: '25' }

// 格式化对象为查询字符串
const newQueryStr = qs.stringify({ name: "李四", age: 30 });
console.log(newQueryStr); // 输出：name=李四&age=30
```


### Node.js 里面的模块的分类
#### 内置模块
Node.js 官方提供的模块，无需安装即可使用。
##### 加载内置模块
```javascript
const fs = require("fs");
```

#### 自定义模块
用户自己编写的模块。
##### 加载用户自定义模块
```javascript
const myModule = require("./自定义模块路径");
```

#### 第三方模块
由社区或第三方提供的模块，需通过 npm 安装。
##### 加载第三方模块
```javascript
const moment = require("moment");
```

#### module 对象
存储当前模块的相关信息。
```javascript
console.log(module);
```
输出示例：
```javascript
{
  id: '.',
  path: 'D:\\lk\\桌面\\lk\\html',
  exports: {}, // 向外共享成员
  filename: 'D:\\lk\\桌面\\lk\\html\\hello.js',
  loaded: false,
  children: [],
  paths: [
    'D:\\lk\\桌面\\lk\\html\\node_modules',
    'D:\\lk\\桌面\\lk\\node_modules',
    'D:\\lk\\桌面\\node_modules',
    'D:\\lk\\node_modules',
    'D:\\node_modules'
  ]
}
```

##### module.exports 向外共享成员
导入模块时，得到的成员就是 `module.exports` 指向的对象。
```javascript
module.exports = {
  a: 9,
  sayHello: function() {
    console.log("Hello!");
  }
};
```

##### exports 对象
Node.js 提供了 `exports` 对象，等同于 `module.exports`，但最终以 `module.exports` 为准。
```javascript
exports.name = "张三"; // 等价于 module.exports.name = "张三"
```


### 第三方模块（包）与 npm
第三方模块由社区提供，免费但需注意安全性。npm 是 Node.js 的包管理器，用于安装、管理和分享包。

#### npm 平台
- **www.npmjs.com**：JS 包共享平台，可搜索和查看包信息。
- **registry.npmjs.org**：JS 包下载服务器，npm 安装包时默认从这里下载。

#### 安装第三方包
```bash
npm install 包完整名称@版本号
# 或简写
npm i 包完整名称
```
安装后会在项目目录下生成：
- `node_modules`：存放安装的包，`require()` 时从这里导入。
- `package-lock.json`：记录包的具体版本、下载地址等信息，确保安装一致性。

#### 卸载包
```bash
npm uninstall 包完整名称
```

#### package.json
项目配置文件，用于记录项目信息和依赖。
- **生成命令**：
  ```bash
  npm init -y
  ```
- **剔除 `node_modules` 后恢复依赖**：
  ```bash
  npm install
  ```
- **示例 `package.json`**：
  ```json
  {
    "name": "html",
    "version": "1.0.0",
    "description": "",
    "main": "hello.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "dependencies": {
      "moment": "^2.30.1"
    },
    "devDependencies": {}
  }
  ```

#### 依赖分类
- **dependencies**：核心依赖，开发和上线都需要。
  ```bash
  npm i 包名
  ```
- **devDependencies**：开发依赖，仅开发时需要（如构建工具、测试库）。
  ```bash
  npm i 包名 -D
  ```

#### 切换下载源
```bash
# 获取当前下载源
npm config get registry

# 切换到淘宝源
npm config set registry=https://registry.npm.taobao.org

# 验证切换
npm config get registry
```


### 包的分类
#### 项目包
安装到项目的 `node_modules` 目录中的包。
- **开发依赖包**：记录在 `devDependencies`，仅开发使用。
- **核心依赖包**：记录在 `dependencies`，开发和上线都使用。

#### 全局包
安装到全局环境的包，可在命令行直接使用。
- **安装全局包**：
  ```bash
  npm i 包名 -g
  ```
- **卸载全局包**：
  ```bash
  npm uninstall 包名 -g
  ```
- **默认保存位置**：
  - Windows：`C:\Users\用户名\AppData\Roaming\npm\node_modules`
  - macOS/Linux：`/usr/local/lib/node_modules`


### 规范的包结构
一个规范的 npm 包应满足以下要求：
1. 以单独目录存在。
2. 顶级目录下必须有 `package.json`（包管理与配置文件）。
3. `package.json` 必须包含 `name`（包名）、`version`（版本号）、`main`（包入口）。
4. 包含 `README.md`（包说明文件）。

#### README.md 示例内容
- 安装方式
- 导入方式
- 使用方式
- 开源协议


### npm 发布包
#### 注册账号
访问 [www.npmjs.com](sslocal://flow/file_open?url=https%3A%2F%2Fwww.npmjs.com%2F&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=) 注册账号。

#### npm 终端登录
```bash
npm login
```
按提示输入用户名、密码和邮箱。

#### 发布包
终端切换到包根目录，运行：
```bash
npm publish
```

#### 删除发布包
```bash
npm unpublish 包名 --force
```
注意：只能删除发布 72 小时内的包。


### 模块包加载机制
- 模块加载后会被缓存，多次调用 `require()` 不会重复执行模块。
- 内置模块加载优先级最高。

#### 自定义模块加载机制
- 加载自定义模块时，路径必须以 `./` 或 `../` 开头，否则 Node.js 会将其视为内置模块或第三方模块。
- `require` 省略扩展名时的加载顺序：
  1. 按确切文件名加载。
  2. 补全 `.js` 后加载。
  3. 补全 `.json` 后加载。
  4. 补全 `.node` 后加载。
  5. 加载失败。

#### 第三方模块加载机制
- 逐级向上查找 `node_modules` 目录。
- 找到 `node_modules` 后，会先查看包的 `package.json` 中的 `main` 字段，然后加载对应文件；若没有 `main`，则默认加载 `index.js`。


## Express 是 Node.js 的 Web 框架
Express 是轻量级、灵活的 Node.js Web 框架，用于快速构建 Web 应用和 API。

### 安装 Express
```bash
npm i express@4.17.1
```

### 使用
```javascript
// 导入 express
const express = require("express");
// 创建 web 服务器
const app = express();
// 启动服务器，监听 3000 端口
app.listen(3000, () => {
  console.log("服务器运行在 http://localhost:3000");
});
```

### 监听请求
#### GET 请求
```javascript
app.get("客户端请求URL", (req, res) => {
  // req：客户端请求相关数据和属性
  // res：服务器响应对象
  res.send({ msg: "GET 请求成功" }); // 响应 JSON 对象
});
```

#### POST 请求
```javascript
app.post("客户端请求URL", (req, res) => {
  res.send({ msg: "POST 请求成功" });
});
```

#### 查询参数请求
```javascript
app.get("/api/user", (req, res) => {
  // req.query 可访问查询参数（URL 中 ? 后面的内容）
  const { name, age } = req.query;
  res.send({ name, age });
});
```

#### 动态参数请求
```javascript
app.get("/api/user/:id", (req, res) => {
  // req.params 可访问动态参数（URL 中 : 后面的内容）
  const { id } = req.params;
  res.send({ userId: id });
});
```

### 托管静态资源
```javascript
// 托管单个静态资源目录
app.use(express.static("public"));

// 托管多个静态资源目录（按顺序查找）
app.use(express.static("public1"));
app.use(express.static("public2"));

// 挂载路径前缀
app.use("/static", express.static("public"));
// 访问时需加前缀：http://localhost:3000/static/文件
```

### nodemon
方便开发调试，代码修改后自动重启服务器。

#### 安装
```bash
npm i -g nodemon
```

#### 使用
```bash
nodemon app.js
# 代替 node app.js，nodemon 会监听文件变化并自动重启
```

### 路由
路由用于定义客户端请求 URL 与服务器处理函数的映射关系。
```javascript
app.请求类型("路径", 处理函数);
```

#### 模块化路由
1. **创建单独的路由模块**（如 `routes/user.js`）：
   ```javascript
   const express = require("express");
   const router = express.Router();
   
   // 挂载具体路由
   router.get("/list", (req, res) => {
     res.send({ msg: "用户列表" });
   });
   
   router.post("/add", (req, res) => {
     res.send({ msg: "添加用户" });
   });
   
   // 向外共享路由对象
   module.exports = router;
   ```

2. **在入口文件中注册路由模块**：
   ```javascript
   const express = require("express");
   const app = express();
   // 导入路由模块
   const userRouter = require("./routes/user");
   // 注册路由模块
   app.use("/api/user", userRouter);
   // 访问路径：http://localhost:3000/api/user/list
   ```

### 中间件
中间件是一个函数，用于处理请求和响应，可访问 `req`、`res` 和 `next`（下一个中间件/路由）。

#### 调用流程
请求到达 Express 后，可连续调用多个中间件对请求进行预处理，最后进入路由。

#### 中间件格式
```javascript
const middleware = (req, res, next) => {
  console.log("这是中间件");
  next(); // 必须调用 next() 才能进入下一个中间件/路由
};
```

#### 全局中间件
所有请求都会触发的中间件。
```javascript
app.use((req, res, next) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  next();
});
```

#### 局部中间件
只在单个路由中生效的中间件。
```javascript
const localMiddleware = (req, res, next) => {
  console.log("这是局部中间件");
  next();
};

app.get("/about", localMiddleware, (req, res) => {
  res.send("关于我们");
});
```

#### 中间件间共享 req 和 res
上游中间件可向 `req` 或 `res` 添加属性/方法，下游中间件/路由可访问。
```javascript
app.use((req, res, next) => {
  req.user = { name: "张三" };
  next();
});

app.get("/user", (req, res) => {
  res.send({ user: req.user });
});
```

#### 中间件分类
- **应用级中间件**：绑定到 `app` 上的中间件（如 `app.use()`、`app.get()`）。
- **路由级中间件**：绑定到 `router` 上的中间件（如 `router.use()`）。
- **错误级中间件**：用于捕获项目中的错误，函数参数必须是 `(err, req, res, next)`，且要放在所有路由之后。
  ```javascript
  // 路由
  app.get("/error", (req, res) => {
    throw new Error("故意抛出的错误");
  });
  
  // 错误处理中间件
  app.use((err, req, res, next) => {
    console.error("错误信息：", err.stack);
    res.status(500).send({
      status: 1,
      msg: "服务器内部错误",
      error: err.message
    });
  });
  ```
- **内置中间件**：Express 官方提供的中间件：
  - `express.static`：托管静态资源。
  - `express.json`：解析 JSON 格式的请求体。
  - `express.urlencoded`：解析 URL-encoded 格式的请求体。
- **第三方中间件**：由社区提供的中间件（如 `cors`、`helmet`）。


### 接口
#### GET 接口示例
```javascript
app.get("/api/data", (req, res) => {
  const query = req.query;
  res.send({
    status: 0,
    msg: "请求成功",
    data: query
  });
});
```

#### POST 接口示例
```javascript
// 需先使用 express.json() 解析请求体
app.use(express.json());

app.post("/api/data", (req, res) => {
  const body = req.body;
  res.send({
    status: 0,
    msg: "提交成功",
    data: body
  });
});
```


### CORS 跨域处理
CORS（跨域资源共享）用于解决浏览器的同源策略限制，Express 可通过 `cors` 中间件轻松处理。

#### 安装 cors
```bash
npm install cors
```

#### 基础使用
```javascript
const express = require("express");
const cors = require("cors");
const app = express();

// 允许所有跨域请求（开发环境常用）
app.use(cors());

// 路由
app.get("/api/data", (req, res) => {
  res.send({ data: "跨域请求成功" });
});
```

#### 配置允许的源
```javascript
// 只允许特定域名访问
const corsOptions = {
  origin: "https://www.example.com", // 允许的源
  methods: "GET,POST", // 允许的请求方法
  allowedHeaders: "Content-Type,Authorization" // 允许的请求头
};

app.use(cors(corsOptions));
```

#### 动态允许源
```javascript
const allowedOrigins = ["https://www.example.com", "https://www.test.com"];
const corsOptions = {
  origin: (origin, callback) => {
    if (allowedOrigins.includes(origin) || !origin) {
      // !origin 允许非浏览器请求（如 Postman）
      callback(null, true);
    } else {
      callback(new Error("不允许的跨域请求"));
    }
  }
};

app.use(cors(corsOptions));
```


### Express 身份验证（JWT）
JWT（JSON Web Token）是常用的无状态身份验证方式。

#### 安装依赖
```bash
npm install jsonwebtoken express-jwt
```

#### 实现登录和验证
```javascript
const express = require("express");
const jwt = require("jsonwebtoken");
const { expressjwt: expressJwt } = require("express-jwt");
const app = express();

app.use(express.json());

// 密钥（生产环境应放在环境变量中）
const secretKey = "your-secret-key-123456";

// 登录接口：生成 Token
app.post("/api/login", (req, res) => {
  const { username, password } = req.body;
  // 模拟验证用户名密码
  if (username === "admin" && password === "123456") {
    // 生成 Token（有效期 2 小时）
    const token = jwt.sign({ username }, secretKey, { expiresIn: "2h" });
    res.send({ status: 0, msg: "登录成功", token });
  } else {
    res.send({ status: 1, msg: "用户名或密码错误" });
  }
});

// 验证 Token 的中间件（除了 /api/login 外都需要验证）
app.use(expressJwt({ secret: secretKey, algorithms: ["HS256"] }).unless({ path: ["/api/login"] }));

// 需要身份验证的接口
app.get("/api/userinfo", (req, res) => {
  // req.auth 包含解析后的 Token 信息
  res.send({ status: 0, msg: "获取成功", user: req.auth });
});

// 错误处理：Token 无效或过期
app.use((err, req, res, next) => {
  if (err.name === "UnauthorizedError") {
    res.status(401).send({ status: 1, msg: "身份验证失败" });
  }
});
```


### Express 连接数据库
#### 连接 MongoDB（使用 Mongoose）
1. **安装依赖**：
   ```bash
   npm install mongoose
   ```
2. **连接代码**：
   ```javascript
   const mongoose = require("mongoose");
   
   // 连接 MongoDB（数据库名：mydb）
   mongoose.connect("mongodb://localhost:27017/mydb")
     .then(() => console.log("MongoDB 连接成功"))
     .catch(err => console.error("MongoDB 连接失败：", err));
   
   // 定义 Schema（数据结构）
   const userSchema = new mongoose.Schema({
     name: String,
     age: Number
   });
   
   // 定义 Model
   const User = mongoose.model("User", userSchema);
   
   // 示例：添加用户
   const newUser = new User({ name: "李四", age: 25 });
   newUser.save().then(() => console.log("用户添加成功"));
   ```

#### 连接 MySQL（使用 mysql2）
1. **安装依赖**：
   ```bash
   npm install mysql2
   ```
2. **连接代码**：
   ```javascript
   const mysql = require("mysql2/promise");
   
   // 创建连接池（推荐，性能更好）
   const pool = mysql.createPool({
     host: "localhost",
     user: "root",
     password: "your-password",
     database: "mydb"
   });
   
   // 示例：查询用户
   async function getUsers() {
     const [rows] = await pool.query("SELECT * FROM users");
     console.log("用户列表：", rows);
   }
   getUsers();
   ```


## Node.js 调试
### 使用 Chrome DevTools 调试
1. 启动 Node.js 时添加 `--inspect` 参数：
   ```bash
   node --inspect app.js
   ```
2. 打开 Chrome 浏览器，访问 `chrome://inspect`，点击 "Configure"，添加 `localhost:9229`。
3. 点击 "inspect" 即可打开调试面板，设置断点调试。

### 使用 debug 模块
1. **安装依赖**：
   ```bash
   npm install debug
   ```
2. **使用示例**：
   ```javascript
   const debug = require("debug")("app:main");
   const debugDB = require("debug")("app:db");
   
   debug("应用启动");
   debugDB("数据库连接成功");
   ```
3. **运行时启用调试**：
   - Windows：`set DEBUG=app:* && node app.js`
   - macOS/Linux：`DEBUG=app:* node app.js`


## Node.js 最佳实践
### 项目结构
推荐的项目目录结构：
```
my-project/
├── node_modules/       # 依赖包
├── public/             # 静态资源
├── routes/             # 路由模块
│   ├── index.js
│   └── user.js
├── middlewares/        # 中间件
│   └── auth.js
├── models/             # 数据模型（数据库）
│   └── User.js
├── utils/              # 工具函数
│   └── db.js
├── app.js              # 入口文件
├── package.json        # 项目配置
└── .env                # 环境变量（生产环境应忽略）
```

### 环境变量
使用 `dotenv` 管理环境变量（如数据库密码、密钥）：
1. **安装**：
   ```bash
   npm install dotenv
   ```
2. **创建 `.env` 文件**：
   ```
   PORT=3000
   DB_HOST=localhost
   DB_USER=root
   DB_PASS=your-password
   JWT_SECRET=your-secret-key
   ```
3. **在 `app.js` 中加载**：
   ```javascript
   require("dotenv").config();
   const port = process.env.PORT || 3000;
   ```

### 错误处理
- 统一使用错误处理中间件。
- 抛出错误时使用自定义错误类：
  ```javascript
  class AppError extends Error {
    constructor(message, statusCode) {
      super(message);
      this.statusCode = statusCode;
      this.isOperational = true; // 标记为可预期的错误
    }
  }
  
  // 使用
  app.get("/api/user/:id", (req, res, next) => {
    if (!req.params.id) {
      return next(new AppError("用户ID不能为空", 400));
    }
    // ...
  });
  ```

### 安全
- 使用 `helmet` 中间件设置安全头：
  ```bash
  npm install helmet
  ```
  ```javascript
  const helmet = require("helmet");
  app.use(helmet()); // 自动设置多个安全相关的响应头
  ```
- 防止 SQL 注入：使用参数化查询（如 mysql2 的 `?` 占位符）。
- 防止 XSS 攻击：对用户输入进行转义（如使用 `xss` 模块）。


需要我帮你**生成一个完整的 Express 项目模板**吗？

已将新内容整合进原有文档，按类别重新组织为两大部分：**开发环境基础工具** 和 **进阶项目工具集**。结构更清晰，方便查阅。以下是融合后的完整内容：

---

## 项目全局包

这些是开发环境的基石，通常推荐全局安装以提升效率和复用性。

### 包管理器

- **pnpm**：高性能、节省磁盘空间的包管理器。通过硬链接和符号链接管理依赖，一个包在磁盘上只存一次，所有项目共享。

  ```bash
  npm install -g pnpm
  ```

- **yarn**：Facebook 推出的包管理器，是 npm 的经典替代品，以速度快和可靠性著称。

  ```bash
  npm install -g yarn
  ```

- **bun**：集运行时、包管理器、打包器于一身的全能工具，旨在提供极致的性能。

  ```bash
  npm install -g bun
  ```

- **volta**：现代化的 JavaScript 工具管理器，可无缝管理 Node.js、npm、yarn 等多个版本。

  ```bash
  npm install -g volta
  ```

### 开发辅助与自动化工具

这些工具能帮你自动执行重复性任务，让你专注于代码逻辑。

- **nodemon**：开发 Node.js 应用的必备工具。它会监视文件变动并自动重启服务。

  ```bash
  npm install -g nodemon
  ```

- **pm2**：Node.js 应用的生产级进程管理器，内置负载均衡，能确保应用持续运行。

  ```bash
  npm install -g pm2
  ```

- **concurrently / npm-run-all**：用于并行或顺序执行多个 npm 脚本，常见于全栈项目。

  ```bash
  npm install -g concurrently
  ```

### 代码质量工具

这类工具帮助你和团队维持统一的代码风格，发现潜在问题。

- **prettier**：流行的代码格式化工具，支持多种语言，一键统一代码风格。

  ```bash
  npm install -g prettier
  ```

- **eslint**：强大的代码检查工具，用于发现并修复语法错误、风格问题和不佳实践。

  ```bash
  npm install -g eslint
  ```

- **typescript**：微软出品的强类型语言，是大型前端项目的标准配置。

  ```bash
  npm install -g typescript
  ```

### 框架与项目脚手架

一键生成项目骨架，快速启动新项目。

- **create-react-app**：React 官方脚手架，一键生成现代化 React 单页应用。

  ```bash
  npm install -g create-react-app
  ```

- **@vue/cli**：Vue.js 官方脚手架，提供交互式项目创建和图形化管理界面。

  ```bash
  npm install -g @vue/cli
  ```

- **@angular/cli**：Angular 官方命令行工具，用于创建、开发和构建 Angular 应用。

  ```bash
  npm install -g @angular/cli
  ```

- **vite**：下一代前端构建工具，以极快的冷启动和热更新著称。

  ```bash
  npm install -g vite
  ```

### 实用工具集

一些能极大简化日常开发的小工具。

- **http-server**：零配置的命令行 HTTP 服务器，在任意目录下运行即可快速启动一个静态文件服务。

  ```bash
  npm install -g http-server
  ```

- **live-server**：带实时刷新功能的小型开发服务器，修改代码后浏览器会自动刷新。

  ```bash
  npm install -g live-server
  ```

- **npm-check-updates**：一键检查并更新 `package.json` 中的依赖到最新版本。

  ```bash
  npm install -g npm-check-updates
  ```

- **tldr**：简化版的 `man` 命令，为常用命令提供实用示例，方便快速查阅。

  ```bash
  npm install -g tldr
  ```

---

### 一个更干净的选择npx`

全局安装很方便，但装太多也会让系统环境变乱。更好的习惯是使用 **`npx`**。

`npx` 是 npm 自带的命令，可以让你**直接运行一个未安装的 npm 包**，执行完不会在本地留下文件，非常适合运行脚手架、代码格式化等临时性命令。

比如，你想用 `create-react-app` 创建新项目，可以用 `npx` 直接运行：

```bash
npx create-react-app my-app
```

`npx` 会临时下载并执行它，然后用完即删，保持你的全局环境清爽。

---

## 项目工具包

这部分工具通常作为项目依赖安装，覆盖后端开发、数据库、测试、安全等专业领域。

### 后端开发增强

这些框架和库能让你更高效地构建 Node.js 后端服务。

- **koa**：Express 原班人马打造的下一代 Web 框架，更轻量（核心仅 1600+ 行），通过 async/await 优雅处理异步逻辑，中间件机制更灵活。

  ```bash
  npm install koa
  ```

  示例：

  ```javascript
  const Koa = require('koa');
  const app = new Koa();
  
  app.use(async ctx => {
    ctx.body = 'Hello Koa';
  });
  
  app.listen(3000);
  ```

- **nestjs**：基于 TypeScript 的企业级 Node.js 框架，采用模块化架构（类似 Angular），内置依赖注入、ORM 集成、微服务支持，适合构建大型可维护的后端系统。

  ```bash
  npm install -g @nestjs/cli
  nest new my-nest-app
  ```

- **hapi**：沃尔玛实验室出品的企业级框架，注重安全、可靠性和可扩展性，内置输入验证、缓存、认证等功能，无需额外中间件即可处理复杂业务。

  ```bash
  npm install @hapi/hapi
  ```

### 数据库与 ORM

让你更轻松地与数据库交互，避免手写 SQL。

- **prisma**：现代数据库工具包，提供类型安全的 ORM、直观的数据建模（Prisma Schema）和强大的查询 API，支持 PostgreSQL、MySQL、MongoDB 等多种数据库。

  ```bash
  npm install prisma --save-dev
  npx prisma init
  ```

  示例（定义模型）：

  ```prisma
  // schema.prisma
  model User {
    id    Int     @id @default(autoincrement())
    name  String
    email String  @unique
  }
  ```

  查询数据：

  ```javascript
  const { PrismaClient } = require('@prisma/client');
  const prisma = new PrismaClient();
  
  async function getUsers() {
    const users = await prisma.user.findMany();
    console.log(users);
  }
  getUsers();
  ```

- **sequelize**：成熟的 Node.js ORM，支持 MySQL、PostgreSQL、SQLite 等，提供 Promise 化的 API，支持关联查询、事务、迁移等功能。

  ```bash
  npm install sequelize mysql2
  ```

- **typeorm**：TypeScript 优先的 ORM，支持 Active Record 和 Data Mapper 模式，与 NestJS 完美集成，支持多种数据库和迁移工具。

  ```bash
  npm install typeorm mysql2 reflect-metadata
  ```

- **redis**：Redis 官方客户端，用于缓存、会话存储、消息队列等，支持 Promise 和回调。

  ```bash
  npm install redis
  ```

  示例：

  ```javascript
  const { createClient } = require('redis');
  const client = createClient();
  
  client.on('error', err => console.log('Redis Client Error', err));
  
  async function connectRedis() {
    await client.connect();
    await client.set('key', 'value');
    const value = await client.get('key');
    console.log(value);
  }
  connectRedis();
  ```

### API 开发与文档

让你的 API 更规范、更易维护。

- **swagger-ui-express**：为 Express API 自动生成交互式 Swagger 文档，支持在线调试接口。

  ```bash
  npm install swagger-ui-express swagger-jsdoc
  ```

  示例：

  ```javascript
  const express = require('express');
  const swaggerUi = require('swagger-ui-express');
  const swaggerJsdoc = require('swagger-jsdoc');
  
  const app = express();
  
  const options = {
    definition: {
      openapi: '3.0.0',
      info: { title: 'My API', version: '1.0.0' },
    },
    apis: ['./routes/*.js'], // 路由文件路径
  };
  
  const specs = swaggerJsdoc(options);
  app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(specs));
  ```

- **apollo-server**：GraphQL 服务器，支持 Express、Koa 等，提供类型安全的 API 和强大的查询能力，适合复杂数据场景。

  ```bash
  npm install apollo-server-express graphql
  ```

### 测试工具

保证代码质量，减少 bug。

- **jest**：Facebook 出品的测试框架，内置断言、模拟（Mock）、快照测试，零配置即可运行，适合 React、Node.js 项目。

  ```bash
  npm install --save-dev jest
  ```

  示例（sum.test.js）：

  ```javascript
  const sum = require('./sum');
  
  test('adds 1 + 2 to equal 3', () => {
    expect(sum(1, 2)).toBe(3);
  });
  ```

  运行测试：

  ```bash
  npx jest
  ```

- **mocha** + **chai**：灵活的测试组合，Mocha 负责测试框架，Chai 提供多种断言风格（should/expect/assert），适合自定义测试流程。

  ```bash
  npm install --save-dev mocha chai
  ```

- **supertest**：测试 HTTP 服务器的利器，配合 Express、Koa 等，无需启动服务器即可测试接口。

  ```bash
  npm install --save-dev supertest
  ```

  示例：

  ```javascript
  const request = require('supertest');
  const app = require('./app');
  
  test('GET /api/users returns users', async () => {
    const response = await request(app).get('/api/users');
    expect(response.statusCode).toBe(200);
    expect(response.body).toEqual(expect.arrayContaining([]));
  });
  ```

- **cypress**：端到端（E2E）测试框架，在真实浏览器中运行，支持可视化调试，适合测试前端交互流程。

  ```bash
  npm install cypress --save-dev
  npx cypress open
  ```

### 安全加固

保护你的应用免受常见攻击。

- **bcrypt**：安全的密码哈希库，使用加盐哈希（Salted Hash）存储密码，防止彩虹表攻击。

  ```bash
  npm install bcrypt
  ```

  示例：

  ```javascript
  const bcrypt = require('bcrypt');
  
  async function hashPassword(password) {
    const saltRounds = 10;
    const hashedPassword = await bcrypt.hash(password, saltRounds);
    return hashedPassword;
  }
  
  async function comparePassword(password, hashedPassword) {
    const match = await bcrypt.compare(password, hashedPassword);
    return match;
  }
  ```

- **express-validator**：验证和清理请求数据，防止 SQL 注入、XSS 等攻击，支持链式调用。

  ```bash
  npm install express-validator
  ```

  示例：

  ```javascript
  const { body, validationResult } = require('express-validator');
  
  app.post('/api/user', [
    body('email').isEmail(),
    body('password').isLength({ min: 6 })
  ], (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    // 处理请求
  });
  ```

### 日志与监控

追踪应用状态，快速定位问题。

- **winston**：灵活的日志库，支持多种传输（文件、控制台、数据库），可自定义日志格式和级别。

  ```bash
  npm install winston
  ```

  示例：

  ```javascript
  const winston = require('winston');
  
  const logger = winston.createLogger({
    level: 'info',
    format: winston.format.json(),
    transports: [
      new winston.transports.File({ filename: 'error.log', level: 'error' }),
      new winston.transports.File({ filename: 'combined.log' })
    ]
  });
  
  logger.info('Hello winston');
  logger.error('An error occurred');
  ```

- **pino**：极快的 Node.js 日志库，低开销（比 winston 快 5-10 倍），适合生产环境。

  ```bash
  npm install pino
  ```

- **morgan**：Express 的 HTTP 请求日志中间件，可记录请求方法、URL、状态码、响应时间等。

  ```bash
  npm install morgan
  ```

  示例：

  ```javascript
  const express = require('express');
  const morgan = require('morgan');
  const app = express();
  
  app.use(morgan('combined')); // 或 'dev'（开发环境简洁输出）
  ```

### 文件与图片处理

轻松处理文件上传、图片编辑等需求。

- **multer**：Express 的文件上传中间件，支持单文件、多文件上传，可自定义存储路径和文件名。

  ```bash
  npm install multer
  ```

  示例：

  ```javascript
  const express = require('express');
  const multer = require('multer');
  const app = express();
  
  const storage = multer.diskStorage({
    destination: (req, file, cb) => cb(null, 'uploads/'),
    filename: (req, file, cb) => cb(null, Date.now() + '-' + file.originalname)
  });
  const upload = multer({ storage });
  
  app.post('/upload', upload.single('file'), (req, res) => {
    res.send('File uploaded: ' + req.file.filename);
  });
  ```

- **sharp**：高性能图片处理库，支持调整大小、裁剪、格式转换（JPEG/PNG/WebP）、压缩等，速度比 ImageMagick 快 4-5 倍。

  ```bash
  npm install sharp
  ```

  示例：

  ```javascript
  const sharp = require('sharp');
  
  async function resizeImage() {
    await sharp('input.jpg')
      .resize(300, 200)
      .toFile('output.jpg');
  }
  resizeImage();
  ```

### 网络请求

优雅地发送 HTTP 请求。

- **axios**：基于 Promise 的 HTTP 客户端，支持浏览器和 Node.js，内置请求/响应拦截器、自动转换 JSON、取消请求等功能。

  ```bash
  npm install axios
  ```

  示例：

  ```javascript
  const axios = require('axios');
  
  async function fetchData() {
    try {
      const response = await axios.get('https://api.example.com/data');
      console.log(response.data);
    } catch (error) {
      console.error(error);
    }
  }
  fetchData();
  ```

- **got**：更现代的 HTTP 客户端，API 简洁，支持流式请求、缓存、重试等，适合 Node.js 环境。

  ```bash
  npm install got
  ```

### 实时通信

构建聊天、通知等实时功能。

- **socket.io**：实时双向通信库，基于 WebSocket，支持自动回退（如长轮询），兼容旧浏览器，适合构建聊天、协作工具。

  ```bash
  npm install socket.io
  ```

  示例（服务器）：

  ```javascript
  const express = require('express');
  const http = require('http');
  const { Server } = require('socket.io');
  
  const app = express();
  const server = http.createServer(app);
  const io = new Server(server);
  
  io.on('connection', (socket) => {
    console.log('a user connected');
    socket.on('chat message', (msg) => {
      io.emit('chat message', msg);
    });
  });
  
  server.listen(3000);
  ```

- **ws**：轻量级 WebSocket 库，性能高，无额外依赖，适合对性能要求高的场景。

  ```bash
  npm install ws
  ```

### 数据验证

确保输入数据符合预期。

- **joi**：强大的 Schema 验证库，支持对象、数组、字符串、数字等多种类型，可自定义验证规则和错误信息。

  ```bash
  npm install joi
  ```

  示例：

  ```javascript
  const Joi = require('joi');
  
  const schema = Joi.object({
    username: Joi.string().alphanum().min(3).max(30).required(),
    password: Joi.string().pattern(new RegExp('^[a-zA-Z0-9]{3,30}$')),
    email: Joi.string().email()
  });
  
  const { error, value } = schema.validate({ username: 'abc', email: 'test@example.com' });
  if (error) {
    console.error(error.details);
  }
  ```

- **yup**：更简洁的 Schema 验证库，API 友好，支持 TypeScript，适合与 React Hook Form 配合使用。

  ```bash
  npm install yup
  ```

### 日期与时间处理

轻松处理日期格式化、计算等需求。

- **dayjs**：轻量级日期库（仅 2KB），API 与 Moment.js 类似，但体积小得多，支持插件扩展。

  ```bash
  npm install dayjs
  ```

  示例：

  ```javascript
  const dayjs = require('dayjs');
  
  console.log(dayjs().format('YYYY-MM-DD HH:mm:ss')); // 当前时间
  console.log(dayjs().add(7, 'day').format('YYYY-MM-DD')); // 7天后
  ```

- **date-fns**：函数式日期库，模块化设计，可按需引入函数，支持 Tree Shaking，适合前端项目。

  ```bash
  npm install date-fns
  ```

  示例：

  ```javascript
  const { format, addDays } = require('date-fns');
  
  console.log(format(new Date(), 'yyyy-MM-dd'));
  console.log(format(addDays(new Date(), 7), 'yyyy-MM-dd'));
  ```

### 前端状态管理

管理复杂的前端应用状态。

- **zustand**：轻量级 React 状态管理库，无样板代码，支持 TypeScript，适合中小项目。

  ```bash
  npm install zustand
  ```

  示例：

  ```javascript
  import { create } from 'zustand';
  
  const useStore = create((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
    decrement: () => set((state) => ({ count: state.count - 1 }))
  }));
  
  function Counter() {
    const { count, increment, decrement } = useStore();
    return (
      <div>
        <button onClick={decrement}>-</button>
        <span>{count}</span>
        <button onClick={increment}>+</button>
      </div>
    );
  }
  ```

- **pinia**：Vue 3 官方推荐的状态管理库，替代 Vuex，API 简洁，支持 TypeScript 和 DevTools。

  ```bash
  npm install pinia
  ```

- **jotai**：原子化 React 状态管理，灵活且高性能，适合需要细粒度状态控制的场景。

  ```bash
  npm install jotai
  ```

### 前端动画与可视化

让你的界面更生动。

- **framer-motion**：React 动画库，API 简单，支持声明式动画、手势交互、布局动画，适合快速实现复杂动画效果。

  ```bash
  npm install framer-motion
  ```

  示例：

  ```javascript
  import { motion } from 'framer-motion';
  
  function AnimatedBox() {
    return (
      <motion.div
        initial={{ opacity: 0, scale: 0.5 }}
        animate={{ opacity: 1, scale: 1 }}
        transition={{ duration: 0.5 }}
        style={{ width: 100, height: 100, background: 'blue' }}
      />
    );
  }
  ```

- **echarts**：百度开源的可视化库，功能强大，支持折线图、柱状图、饼图、地图等 20+ 图表类型，适合数据大屏。

  ```bash
  npm install echarts
  ```

- **recharts**：基于 D3 的 React 图表库，组件化设计，可自定义样式，适合快速构建图表。

  ```bash
  npm install recharts
  ```

### 命令行工具开发

快速构建自己的 CLI 工具。

- **commander**：Node.js CLI 开发库，支持命令、选项、参数解析，适合构建如 `create-react-app` 这样的工具。

  ```bash
  npm install commander
  ```

  示例：

  ```javascript
  const { program } = require('commander');
  
  program
    .name('my-cli')
    .version('1.0.0')
    .option('-n, --name <name>', 'your name')
    .action((options) => {
      console.log(`Hello, ${options.name}!`);
    });
  
  program.parse();
  ```

- **inquirer**：交互式命令行提示库，支持输入、选择、确认等多种交互类型，让 CLI 更友好。

  ```bash
  npm install inquirer
  ```

  示例：

  ```javascript
  const inquirer = require('inquirer');
  
  inquirer
    .prompt([
      { type: 'input', name: 'name', message: 'What is your name?' },
      { type: 'confirm', name: 'confirmed', message: 'Are you sure?' }
    ])
    .then((answers) => {
      console.log(answers);
    });
  ```

- **chalk**：终端字符串样式库，支持彩色输出、加粗、下划线等，让 CLI 输出更美观。

  ```bash
  npm install chalk
  ```

  示例：

  ```javascript
  const chalk = require('chalk');
  
  console.log(chalk.blue('Hello') + ' World' + chalk.red('!'));
  console.log(chalk.bold.green('Success!'));
  ```

- **ora**：终端加载动画库，适合在 CLI 中显示等待状态。

  ```bash
  npm install ora
  ```

  示例：

  ```javascript
  const ora = require('ora');
  
  const spinner = ora('Loading...').start();
  setTimeout(() => {
    spinner.succeed('Done!');
  }, 2000);
  ```

### 其他实用工具

提升开发效率的小工具。

- **nanoid**：轻量级唯一 ID 生成库，比 UUID 更短（21 字符）、更快，适合生成短链接、ID 等。

  ```bash
  npm install nanoid
  ```

  示例：

  ```javascript
  const { nanoid } = require('nanoid');
  console.log(nanoid()); // 输出类似：V1StGXR8_Z5jdHi6B-myT
  ```

- **fs-extra**：Node.js 内置 `fs` 模块的扩展，提供更多实用方法（如 `copy`、`remove`、`mkdirp`），支持 Promise。

  ```bash
  npm install fs-extra
  ```

  示例：

  ```javascript
  const fs = require('fs-extra');
  
  async function copyFile() {
    await fs.copy('src.txt', 'dest.txt');
    console.log('File copied!');
  }
  copyFile();
  ```

- **glob**：文件路径匹配库，支持 `*`、`**` 等通配符，方便查找文件。

  ```bash
  npm install glob
  ```

  示例：

  ```javascript
  const glob = require('glob');
  
  glob('**/*.js', (err, files) => {
    if (err) throw err;
    console.log(files); // 所有 .js 文件
  });
  ```

- **execa**：更好的子进程执行库，支持 Promise、流式输出、跨平台，替代 `child_process`。

  ```bash
  npm install execa
  ```

  示例：

  ```javascript
  const { execa } = require('execa');
  
  async function runCommand() {
    const { stdout } = await execa('ls', ['-la']);
    console.log(stdout);
  }
  runCommand();
  ```

- **husky** + **lint-staged**：Git 钩子工具，在提交代码前自动运行 lint、测试等，保证代码质量。

  ```bash
  npm install husky lint-staged --save-dev
  npx husky install
  npx husky add .husky/pre-commit "npx lint-staged"
  ```

  配置 `package.json`：

  ```json
  {
    "lint-staged": {
      "*.js": ["eslint --fix", "prettier --write"]
    }
  }
  ```

