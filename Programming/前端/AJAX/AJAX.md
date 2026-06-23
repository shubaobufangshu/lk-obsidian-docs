# AJAX

在不刷新页面的情况下，使用 JS 对象与服务器通信，可传输 JSON、XML、HTML、纯文本等数据。

## 1. 引入 Axios 库

```html
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
<script>
axios({
    url: "/路径",
    method: "请求方法",   // GET, POST, PUT, DELETE, PATCH
    params: { 参数名: 值 },   // 查询参数（GET）
    data: { 参数名: 值 }      // 请求体（POST/PUT等）
}).then(response => {//处理数据
    console.log(response.data);
}).catch(error => {//处理错误
    console.error(error);
});
</script>
```

## 2. 请求方法

| 方法   | 作用             |
| ------ | ---------------- |
| GET    | 获取数据         |
| POST   | 提交数据         |
| PUT    | 修改数据（整体） |
| PATCH  | 修改数据（部分） |
| DELETE | 删除数据         |

## 3. URL（统一资源定位符）

**组成**：`协议://域名/资源路径`

**查询参数**：`协议://域名/路径?key1=value1&key2=value2`

在 Axios 中使用 `params` 自动拼接。

## 4. HTTP 协议

### 4.1 请求报文

```
POST /api/user/register HTTP/1.1      ← 请求行
Host: api.example.com:8080
Content-Type: application/json
...                                   ← 请求头
									  ← 空行-标准
{ "username": "test" }                ← 请求体（可选）
```

**请求行**：`方法 URL 协议版本`

**常见请求头**：

| 头字段           | 示例值                                       | 作用说明                                                     |
| ---------------- | -------------------------------------------- | ------------------------------------------------------------ |
| Host             | api.example.com:8080                         | 指定服务器的域名和端口（HTTP/1.1 必需字段，无默认值）        |
| User-Agent       | 浏览器 / 客户端标识（如 Chrome、Postman 等） | 服务器可据此判断客户端类型，返回适配内容（如移动端 / PC 端页面）。 |
| Accept           | application/json, text/plain, */*            | 客户端可接受的响应数据格式（优先级用`q=权重`表示，如`zh-CN;q=0.9`）。 |
| Accept-Encoding  | gzip, deflate, br                            | 客户端支持的压缩算法（服务器可据此压缩响应数据，减少传输量）。 |
| Accept-Language  | zh-CN,zh;q=0.9,en;q=0.8                      | 客户端偏好的语言（服务器可返回对应语言的内容）。             |
| Content-Type     | application/json; charset=UTF-8              | 描述请求体的数据格式（如`application/x-www-form-urlencoded`表单、`multipart/form-data`文件上传等）。 |
| Content-Length   | 102                                          | 请求体的字节长度（服务器据此判断数据是否完整接收）。         |
| Connection       | keep-alive                                   | 表示是否保持 TCP 连接（HTTP/1.1 默认`keep-alive`，可复用连接减少开销）。 |
| Cookie           | session_id=abc123xyz; ...                    | 客户端存储的 Cookie 信息（用于身份认证、会话保持等）。       |
| Authorization    | Bearer eyJhbGciOiJIUzI1Ni...                 | 身份验证信息（如 JWT 令牌、Basic Auth 等，用于服务器验证用户权限）。 |
| Origin           | https://www.example.com                      | 跨域请求时，标识请求的源站（协议 + 域名 + 端口），用于 CORS 跨域验证。 |
| Cache-Control    | no-cache                                     | 缓存控制（`no-cache`表示不使用缓存，需向服务器验证；`max-age=3600`表示缓存 1 小时）。 |
| Pragma           | no-cache                                     | 兼容 HTTP/1.0 的缓存控制（作用类似`Cache-Control`）。        |
| X-Requested-With | XMLHttpRequest                               | 标识请求来自 AJAX（而非页面跳转，常用于后端判断请求类型）。  |
| X-CSRF-Token     | d8f7e6g5h4j3k2l1m0n9                         | 跨站请求伪造（CSRF）防护令牌（服务器需验证该令牌，防止恶意请求）。 |

**空行**

- 是请求头与请求体的分隔符（必须存在，由`\r\n`组成），即使没有请求体，也需要空行（但 GET 请求通常无请求体，空行可省略）。

**请求体**：POST/PUT 等携带的数据。

### 4.2 响应报文

```
HTTP/1.1 200 OK                       ← 状态行
Server: Nginx
Content-Type: application/json
...                                   ← 响应头
									  ← 空行-标准
{ "code": 200, "data": {...} }        ← 响应体
```

**状态行**：`协议版本 状态码 状态短语`

**常见响应头**：

| 头字段                             | 示例值                                                   | 作用说明                                                     |
| ---------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| Server                             | Nginx/1.24.0                                             | 服务器软件标识（如 Nginx、Apache、Tomcat），可隐藏具体版本提升安全性。 |
| Date                               | Wed, 08 Nov 2025 08:30:45 GMT                            | 响应生成的时间（GMT 时区，用于客户端时间校准和缓存判断）。   |
| Content-Type                       | application/json; charset=UTF-8                          | 响应体的数据格式和编码（如`text/html`网页、`image/png`图片、`multipart/form-data`文件响应等）。 |
| Content-Length                     | 286                                                      | 响应体的字节长度（解压后），客户端据此判断数据是否完整接收。 |
| Content-Encoding                   | gzip                                                     | 响应数据的压缩算法（与请求头`Accept-Encoding`对应，减少传输量）。 |
| Connection                         | keep-alive                                               | 保持 TCP 连接（HTTP/1.1 默认，可复用连接减少握手开销）。     |
| Cache-Control                      | public, max-age=3600                                     | 缓存规则：`public`允许代理缓存，`max-age=3600`表示缓存 1 小时（单位：秒）。 |
| Expires                            | Wed, 08 Nov 2025 09:30:45 GMT                            | 缓存过期时间（HTTP/1.0 兼容字段，优先级低于`Cache-Control`）。 |
| ETag                               | "654b7a8d-11e"                                           | 资源唯一标识（哈希值），用于缓存验证（客户端下次请求带`If-None-Match`对比）。 |
| Set-Cookie                         | session_id=new_abc123xyz; Path=/; HttpOnly; Secure       | 资源最后修改时间，用于缓存验证（客户端下次请求带`If-Modified-Since`对比）。 |
| Access-Control-Allow-Origin        | https://www.example.com                                  | CORS 跨域配置：允许的请求源站（仅跨域请求时返回，`*`表示允许所有源）。 |
| `Access-Control-Allow-Origin`      | `https://www.example.com`GET, POST, PUT, DELETE, OPTIONS | CORS 跨域配置：允许的请求源站（仅跨域请求时返回，`*`表示允许所有源）。CORS 跨域配置：允许的请求方法。 |
| `Access-Control-Allow-Methods`     | GET, POST, PUT, DELETE, OPTIONS                          | CORS 跨域配置：允许的请求方法。                              |
| `Access-Control-Allow-Headers`     | `Content-Type, Authorization, X-CSRF-Token`              | CORS 跨域配置：允许的自定义请求头。                          |
| `Access-Control-Allow-Credentials` | `true`                                                   | CORS 跨域配置：允许客户端携带 Cookie 跨域请求。              |
| `Access-Control-Max-Age`           | `86400`                                                  | CORS 跨域配置：预检请求（OPTIONS）的缓存时间（1 天，避免重复预检）。 |
| `X-Frame-Options`                  | `SAMEORIGIN`                                             | 安全防护：仅允许同源页面嵌入当前资源（防点击劫持）。         |
| `X-XSS-Protection`                 | `1; mode=block`                                          | 安全防护：开启浏览器 XSS 过滤，检测到攻击时阻断页面加载。    |
| `X-Content-Type-Options`           | `nosniff`                                                | 安全防护：禁止浏览器自动推断响应类型（防 MIME 类型混淆攻击）。 |
| `Strict-Transport-Security`        | `max-age=31536000; includeSubDomains; preload`           | 安全防护（HSTS）：强制客户端后续用 HTTPS 访问，有效期 1 年，包含子域名。 |

**响应体**：服务器返回的数据（JSON、HTML、图片等）。

| code                              | 业务状态码                                         |
| --------------------------------- | -------------------------------------------------- |
| message                           | 业务提示信息（用户友好提示）                       |
| data                              | 核心业务数据（按需返回，如用户信息、列表数据等）。 |
| meta                              | 附加元信息（如时间戳、请求 ID，用于问题排查）      |
| text/html                         | HTML 网页（响应体为标签代码）                      |
| application/x-www-form-urlencoded | 表单格式数据                                       |
| image/jpeg                        | 图片文件（响应体为二进制数据                       |

### 4.3 HTTP 响应状态码

| 分类 | 说明                        |
| ---- | --------------------------- |
| 1xx  | 信息性响应                  |
| 2xx  | 成功（如 200 OK）           |
| 3xx  | 重定向                      |
| 4xx  | 客户端错误（如 404 未找到） |
| 5xx  | 服务端错误                  |

## 接口文档

### 接口文档核心定义与规范

#### 核心定义

接口文档（API文档）是标准化定义客户端与服务端、服务间通信规则的技术契约，明确接口通信、数据契约与交互逻辑，是协同开发、测试、对接与维护的依据。

#### 必备组成项

1. **基础信息**：名称、描述、URL、方法、版本、鉴权、频率限制  
2. **请求规范**：Header格式、鉴权字段、Content-Type  
3. **请求参数**：路径/查询/Body参数，含类型、必填、默认值、校验规则  
4. **响应规范**：统一结构、成功/异常示例、业务状态码、HTTP状态码  
5. **补充说明**：业务边界、异常处理、调用示例、更新日志

#### 核心价值

- 统一认知，降低沟通成本，支持并行开发  
- 为测试、自动化用例提供依据  
- 沉淀规范，支撑迭代与第三方对接  
- 降低新人与交接成本

---

### 主流API文档生成工具全维度对比表

| 工具                  | 核心原理                            | 适用场景                                | 核心优点                                      | 核心缺点                                 |
| :-------------------- | :---------------------------------- | :-------------------------------------- | :-------------------------------------------- | :--------------------------------------- |
| **JSDoc**             | 解析JSDoc注释生成静态文档           | JS库/组件库、小型项目                   | 轻量零侵入；IDE原生支持；开箱即用             | 无调试；RESTful支持弱；静态技术化文档    |
| **Swagger (OpenAPI)** | 基于OpenAPI规范，注解生成交互式文档 | 中大型RESTful项目、对外服务、多团队协作 | 行业标准；在线调试；生态完善；可生成SDK/Mock  | 配置复杂；代码侵入性；注解过多影响可读性 |
| **apiDoc**            | 解析@api系注解生成静态文档          | 中小型项目、MVP快速落地、轻量化需求     | 配置极简；语义化强；文档界面友好；多语言支持  | 维护度低；无原生调试；扩展能力弱         |
| **TypeDoc**           | 解析TS类型系统与注释生成文档        | TS库/框架、全TS项目                     | 类型精准同步；支持全TS语法；结构清晰          | 仅支持TS；RESTful专项弱；扩展需插件      |
| **ESDoc**             | 解析ES6+模块化语法与注释            | ES6+大型项目/类库、存量项目             | 现代语法支持度高；可插件扩展；文档可读性强    | 已停止维护；生态停滞；仅支持ES6+         |
| **documentation.js**  | 基于JSDoc扩展，多格式输出           | 静态站点集成、需自定义格式的项目        | 输出格式灵活(HTML/MD/JSON)；支持TS/Flow；轻量 | 社区小；无调试；高阶功能需插件           |

---

### 各工具详细使用指南

#### JSDoc

**核心定位**：JS生态最基础的注释文档工具，轻量无依赖。

**环境**：Node.js ≥ 12.0.0

**安装**：
```bash
npm install --save-dev jsdoc
```

**高频注解**：

| 注解           | 作用                 | 示例                           |
| :------------- | :------------------- | :----------------------------- |
| `@param`       | 定义参数类型与描述   | `@param {number} a - 第一个数` |
| `@returns`     | 定义返回值类型与描述 | `@returns {number} 两数之和`   |
| `@module`      | 标记模块             | `@module utils`                |
| `@class`       | 标记类               | `@class UserService`           |
| `@description` | 功能详述             | `@description 核心计算方法`    |
| `@example`     | 调用示例             | `@example add(1,2) // 3`       |
| `@throws`      | 抛出异常             | `@throws {TypeError} 类型错误` |
| `@deprecated`  | 标记废弃             | `@deprecated 已改用newAdd`     |

**示例代码**：
```javascript
/**
 * @module mathUtils
 * @description 数学工具库
 */

/**
 * @description 两数相加
 * @param {number} a - 第一个数
 * @param {number} b - 第二个数
 * @param {number} [c=0] - 第三个数（可选）
 * @returns {number} 累加和
 * @throws {TypeError} 非数字入参
 * @example add(1, 2) // 3
 * @deprecated 改用sum方法
 */
function add(a, b, c = 0) {
  if (typeof a !== 'number' || typeof b !== 'number' || typeof c !== 'number') {
    throw new TypeError('入参必须为数字');
  }
  return a + b + c;
}
```

**生成命令**：
```bash
# 基本生成
jsdoc src/ -r -d docs/jsdoc
# 使用配置
jsdoc -c jsdoc.json
```

项目本地生成（配置package.json脚本）

```JSON
{
  "scripts": {
    "docs:build": "jsdoc src/ -r -d docs/jsdoc"
  }
}
npm run docs:build
```

- 核心参数说明：
  - `-r`：递归遍历目录下的所有文件
  - `-d <目录>`：指定文档输出目录，默认生成`out`目录
  - `-c <配置文件>`：指定自定义配置文件
  - `-t <模板路径>`：指定自定义文档主题模板

##### 进阶配置

**配置示例** (`jsdoc.json`)：

```json
{
  "source": {
    "include": ["src/"],
    "excludePattern": "(node_modules|test)"
  },
  "opts": {
    "destination": "./docs/jsdoc",
    "recurse": true
  }
}
```

**最佳实践**：同步更新注释；必选参数前置；核心方法必加`@example`；废弃方法标注`@deprecated`。

基于配置文件生成：

```Bash
jsdoc -c jsdoc.json
```

#### Swagger (OpenAPI)

**核心定位**：RESTful API国际标准工具集，支持交互式文档与全生命周期管理。

**环境**：Node.js ≥ 14.0.0，Express等框架。

**安装**：
```bash
npm install swagger-jsdoc swagger-ui-express
```

**配置示例** (`app.js`)：
```javascript
const swaggerJsdoc = require('swagger-jsdoc');
const swaggerUi = require('swagger-ui-express');

const options = {
  definition: {
    openapi: '3.0.0',
    info: { title: 'API文档', version: '1.0.0' ,description: '前后端分离项目的用户管理接口规范文档',},
    servers: [{ url: 'http://localhost:3000' }],
    components: {
      securitySchemes: { BearerAuth: { type: 'http', scheme: 'bearer' } }
    },
    security: [{ BearerAuth: [] }]
  },
  apis: ['./routes/*.js'],
};

const spec = swaggerJsdoc(options);
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(spec));
```

**路由注解示例** (`routes/user.js`)：
```javascript
const express = require('express');
const router = express.Router();

/**
 * @openapi
 * /api/users:
 *   get:
 *     summary: 获取用户列表
 *     tags: [用户管理]
 *     description: 分页查询系统内的用户列表，需管理员权限
 *     parameters:
 *       - in: query
 *         name: pageNum
 *         schema:
 *           type: integer
 *           default: 1
 *         description: 页码，必填
 *       - in: query
 *         name: pageSize
 *         schema:
 *           type: integer
 *           default: 10
 *         description: 每页条数，必填
 *       - in: query
 *         name: keyword
 *         schema:
 *           type: string
 *         description: 用户名搜索关键词，选填
 *     responses:
 *       200:
 *         description: 成功获取用户列表
 *         content:
 *           application/json:
 *             schema:
 *               type: object
 *               properties:
 *                 code:
 *                   type: integer
 *                   example: 200
 *                 message:
 *                   type: string
 *                   example: 操作成功
 *                 data:
 *                   type: object
 *                   properties:
 *                     total:
 *                       type: integer
 *                       example: 100
 *                     list:
 *                       type: array
 *                       items:
 *                         type: object
 *                         properties:
 *                           id:
 *                             type: integer
 *                             example: 1
 *                           username:
 *                             type: string
 *                             example: 张三
 *                           email:
 *                             type: string
 *                             example: zhangsan@example.com
 *       401:
 *         description: 未授权，Token无效或过期
 *       403:
 *         description: 权限不足，无法访问
 */
router.get('/users', (req, res) => {
  // 业务逻辑
  res.json({
    code: 200,
    message: '操作成功',
    data: {
      total: 100,
      list: [{ id: 1, username: '张三', email: 'zhangsan@example.com' }]
    }
  });
});

/**
 * @openapi
 * /api/user/{id}:
 *   get:
 *     summary: 获取用户详情
 *     tags: [用户管理]
 *     description: 根据用户ID查询用户详细信息
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: integer
 *         description: 用户唯一ID，必填
 *     responses:
 *       200:
 *         description: 成功获取用户详情
 *       404:
 *         description: 用户不存在
 */
router.get('/user/:id', (req, res) => {
  // 业务逻辑
  res.json({
    code: 200,
    message: '操作成功',
    data: { id: req.params.id, username: '张三', email: 'zhangsan@example.com' }
  });
});

module.exports = router;
```

**核心流程**：配置基础信息 → 编写注解 → 访问 `/api-docs` 调试。

**进阶能力**：`tags`分组；全局鉴权；`components/schemas`模型复用；Mock集成；多环境配置。

**最佳实践**：遵循OpenAPI 3.0规范；标注summary与description；明确参数必填/类型/示例；覆盖核心响应码；复用通用模型。

#### apiDoc

**核心定位**：轻量化RESTful API文档工具，语义注解，开箱即用。

**环境**：Node.js ≥ 10.0.0

**安装**：
```bash
npm install -g apidoc
```

**高频注解**：

| 注解标签          | 作用说明                                                    | 必选 |
| ----------------- | ----------------------------------------------------------- | ---- |
| `@api`            | 定义接口的请求方法、URL、接口名称，无此注解的注释块会被忽略 | 是   |
| `@apiName`        | 定义接口的唯一标识名称，同一分组内不可重复                  | 是   |
| `@apiGroup`       | 定义接口的分组名称，用于文档分类                            | 是   |
| `@apiParam`       | 定义请求参数，含类型、名称、必填、描述                      | 否   |
| `@apiSuccess`     | 定义成功响应的字段、类型、描述                              | 否   |
| `@apiError`       | 定义异常响应的字段、类型、描述                              | 否   |
| `@apiDescription` | 接口的详细业务描述                                          | 否   |
| `@apiExample`     | 接口调用示例（请求/响应）                                   | 否   |
| `@apiVersion`     | 接口版本号，支持版本对比                                    | 否   |
| `@apiPermission`  | 接口所需的权限标识                                          | 否   |

**配置文件** (`apidoc.json`)：

```json
{
  "name": "订单系统API",
  "version": "1.0.0",
  "url": "http://localhost:8080/api",
  "sampleUrl": "http://localhost:8080/api"
}
```

**代码示例**：
```javascript
/**
 * @api {get} /order/list 获取订单列表
 * @apiName GetOrderList
 * @apiGroup 订单管理
 * @apiParam {Number} pageNum=1 页码
 * @apiSuccess {Object[]} data.list 订单列表
 * @apiSuccessExample {json} 成功响应:
 * { "code": 200, "data": { "list": [] } }
 */
router.get('/list', (req, res) => { ... });
```

**生成命令**：
```bash
apidoc -i src/routes/ -o docs/apidoc
```

**最佳实践**：必须定义`@apiName`与`@apiGroup`；明确参数规则；补充示例；同步更新版本号。

#### 其他工具简览

##### 1. TypeDoc
- **定位**：TS项目首选，类型自动解析。
- **安装**：`npm install --save-dev typedoc`
- **命令**：`typedoc --out docs src/index.ts`
- **特点**：类型精准同步，支持全TS语法。

##### 2. ESDoc
- **定位**：ES6+模块化文档工具（已停维）。
- **安装**：`npm install -g esdoc`
- **配置**：`esdoc.json`
- **注意**：仅限存量项目，不推荐新用。

##### 3. documentation.js
- **定位**：多格式输出，对接静态站点。
- **安装**：`npm install --save-dev documentation`
- **生成Markdown**：`documentation build src/** -f md -o api.md`
- **特点**：灵活输出，原生支持TS/Flow。