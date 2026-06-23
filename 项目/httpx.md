
## 一、概述与安装

### 1.1 什么是httpx
httpx是Python的一个现代、功能齐全的HTTP客户端库，支持同步和异步API，完全兼容HTTP/1.1和HTTP/2协议。它是requests库的现代替代品，提供了更丰富的功能和更好的性能。

### 1.2 核心特性
- 同时支持**同步**和**异步**请求
- 原生支持**HTTP/2**协议
- 支持连接池和保持连接
- 内置超时控制
- 支持代理、认证、SSL/TLS配置
- 支持流式上传和下载
- 自动处理重定向和Cookie
- 类型提示完善
- 支持请求/响应钩子

### 1.3 安装
```bash
# 基础安装（仅支持HTTP/1.1）
pip install httpx

# 完整安装（支持HTTP/2）
pip install httpx[http2]

# 安装开发版本
pip install git+https://github.com/encode/httpx.git
```

## 二、快速入门

### 2.1 基本GET请求
```python
import httpx

# 同步请求
response = httpx.get("https://httpbin.org/get")
print(response.status_code)  # 200
print(response.text)         # 响应文本
print(response.json())       # 解析JSON响应

# 异步请求
import asyncio

async def main():
    async with httpx.AsyncClient() as client:
        response = await client.get("https://httpbin.org/get")
        print(response.json())

asyncio.run(main())
```

### 2.2 其他HTTP方法
```python
# 同步
httpx.post("https://httpbin.org/post", data={"key": "value"})
httpx.put("https://httpbin.org/put", data={"key": "value"})
httpx.delete("https://httpbin.org/delete")
httpx.head("https://httpbin.org/get")
httpx.options("https://httpbin.org/get")
httpx.patch("https://httpbin.org/patch", data={"key": "value"})

# 异步
async with httpx.AsyncClient() as client:
    await client.post("https://httpbin.org/post", data={"key": "value"})
    await client.put("https://httpbin.org/put", data={"key": "value"})
```

## 三、同步客户端详解

### 3.1 使用Client实例
推荐使用`Client`实例而不是顶级函数，因为它会自动管理连接池，提高性能：
```python
with httpx.Client() as client:
    response1 = client.get("https://httpbin.org/get")
    response2 = client.get("https://httpbin.org/status/200")
    # 连接会自动复用
```

### 3.2 Client配置
```python
client = httpx.Client(
    base_url="https://httpbin.org/",  # 基础URL
    headers={"User-Agent": "my-app/1.0.0"},  # 默认请求头
    timeout=10.0,  # 全局超时时间（秒）
    follow_redirects=True,  # 自动跟随重定向
    verify=True,  # 验证SSL证书
    http2=True,  # 启用HTTP/2
    limits=httpx.Limits(
        max_connections=100,
        max_keepalive_connections=20
    )  # 连接池限制
)

# 使用相对URL
response = client.get("/get")  # 实际请求 https://httpbin.org/get
```

## 四、异步客户端详解

### 4.1 使用AsyncClient
```python
import asyncio
import httpx

async def main():
    async with httpx.AsyncClient() as client:
        tasks = [
            client.get(f"https://httpbin.org/delay/{i}")
            for i in range(1, 4)
        ]
        responses = await asyncio.gather(*tasks)
        
        for response in responses:
            print(response.status_code)

asyncio.run(main())
```

### 4.2 AsyncClient配置
与`Client`配置基本相同，但异步客户端有一些额外的选项：
```python
async with httpx.AsyncClient(
    http2=True,
    timeout=httpx.Timeout(5.0, connect=10.0),
    limits=httpx.Limits(
        max_connections=100,
        max_keepalive_connections=20,
        keepalive_expiry=5.0
    )
) as client:
    response = await client.get("https://httpbin.org/get")
```

## 五、请求配置

### 5.1 URL参数
```python
# 字典形式
params = {"key1": "value1", "key2": "value2"}
response = httpx.get("https://httpbin.org/get", params=params)
print(response.url)  # https://httpbin.org/get?key1=value1&key2=value2

# 列表形式（多值参数）
params = {"key": ["value1", "value2"]}
response = httpx.get("https://httpbin.org/get", params=params)
print(response.url)  # https://httpbin.org/get?key=value1&key=value2
```

### 5.2 请求头
```python
headers = {
    "User-Agent": "my-app/1.0.0",
    "Accept": "application/json",
    "Authorization": "Bearer token123"
}
response = httpx.get("https://httpbin.org/headers", headers=headers)
```

### 5.3 Cookies
```python
# 设置cookies
cookies = {"session_id": "abc123", "user_id": "12345"}
response = httpx.get("https://httpbin.org/cookies", cookies=cookies)

# 使用CookieJar
jar = httpx.Cookies()
jar.set("session_id", "abc123", domain="httpbin.org")
response = httpx.get("https://httpbin.org/cookies", cookies=jar)
```

### 5.4 请求体

#### 表单数据
```python
data = {"username": "admin", "password": "secret"}
response = httpx.post("https://httpbin.org/post", data=data)
```

#### JSON数据
```python
json_data = {"name": "John", "age": 30}
response = httpx.post("https://httpbin.org/post", json=json_data)
# 自动设置Content-Type: application/json
```

#### 文件上传
```python
# 单个文件
files = {"file": open("test.txt", "rb")}
response = httpx.post("https://httpbin.org/post", files=files)

# 多个文件
files = {
    "file1": open("test1.txt", "rb"),
    "file2": open("test2.txt", "rb")
}
response = httpx.post("https://httpbin.org/post", files=files)

# 带文件名和Content-Type
files = {
    "file": ("test.txt", open("test.txt", "rb"), "text/plain")
}
response = httpx.post("https://httpbin.org/post", files=files)
```

#### 原始二进制数据
```python
content = b"Hello, world!"
response = httpx.post("https://httpbin.org/post", content=content)
```

## 六、响应处理

### 6.1 响应属性
```python
response = httpx.get("https://httpbin.org/get")

print(response.status_code)  # 状态码
print(response.reason_phrase)  # 状态文本
print(response.url)  # 请求URL
print(response.headers)  # 响应头
print(response.cookies)  # 响应cookies
print(response.encoding)  # 响应编码
print(response.content)  # 原始字节内容
print(response.text)  # 解码后的文本
print(response.json())  # 解析JSON
```

### 6.2 响应状态检查
```python
response = httpx.get("https://httpbin.org/status/404")

# 检查是否成功
if response.is_success:
    print("请求成功")
else:
    print(f"请求失败: {response.status_code}")

# 抛出异常
response.raise_for_status()  # 状态码>=400时抛出HTTPStatusError
```

### 6.3 响应编码
```python
response = httpx.get("https://httpbin.org/get")

# 自动检测编码
print(response.text)

# 手动指定编码
response.encoding = "utf-8"
print(response.text)
```

## 七、高级特性

### 7.1 超时控制
httpx提供了精细的超时控制：
```python
# 全局超时（所有阶段总共10秒）
httpx.get("https://httpbin.org/delay/5", timeout=10.0)

# 分阶段超时
timeout = httpx.Timeout(
    connect=5.0,    # 连接超时
    read=10.0,      # 读取超时
    write=10.0,     # 写入超时
    pool=5.0        # 从连接池获取连接超时
)
httpx.get("https://httpbin.org/delay/5", timeout=timeout)

# 禁用超时
httpx.get("https://httpbin.org/delay/10", timeout=None)
```

### 7.2 重定向处理
```python
# 默认不跟随重定向
response = httpx.get("https://httpbin.org/redirect/3")
print(response.status_code)  # 302

# 自动跟随重定向
response = httpx.get("https://httpbin.org/redirect/3", follow_redirects=True)
print(response.status_code)  # 200
print(response.history)  # 重定向历史

# 限制重定向次数
response = httpx.get(
    "https://httpbin.org/redirect/5",
    follow_redirects=True,
    max_redirects=3
)
```

### 7.3 代理设置
```python
# HTTP代理
proxies = {
    "http://": "http://127.0.0.1:8080",
    "https://": "http://127.0.0.1:8080"
}
httpx.get("https://httpbin.org/get", proxies=proxies)

# 认证代理
proxies = {
    "https://": "http://user:password@127.0.0.1:8080"
}
httpx.get("https://httpbin.org/get", proxies=proxies)

# 客户端级代理
client = httpx.Client(proxies=proxies)
```

### 7.4 认证

#### 基本认证
```python
auth = httpx.BasicAuth("username", "password")
response = httpx.get("https://httpbin.org/basic-auth/username/password", auth=auth)
```

#### 摘要认证
```python
auth = httpx.DigestAuth("username", "password")
response = httpx.get("https://httpbin.org/digest-auth/auth/username/password", auth=auth)
```

#### Bearer令牌认证
```python
headers = {"Authorization": "Bearer your_token_here"}
response = httpx.get("https://httpbin.org/bearer", headers=headers)
```

### 7.5 SSL/TLS配置
```python
# 禁用SSL验证（不推荐）
httpx.get("https://expired.badssl.com", verify=False)

# 使用自定义CA证书
httpx.get("https://example.com", verify="path/to/ca.pem")

# 客户端证书认证
httpx.get(
    "https://example.com",
    cert=("client.crt", "client.key")
)
```

### 7.6 HTTP/2支持
```python
# 启用HTTP/2
response = httpx.get("https://http2.github.io/", http2=True)
print(response.http_version)  # HTTP/2
```

### 7.7 请求/响应钩子
```python
def log_request(request):
    print(f"请求: {request.method} {request.url}")

def log_response(response):
    print(f"响应: {response.status_code} {response.url}")

client = httpx.Client(
    event_hooks={
        "request": [log_request],
        "response": [log_response]
    }
)

response = client.get("https://httpbin.org/get")
```

## 八、流式处理

### 8.1 流式下载
```python
# 流式下载大文件
with httpx.stream("GET", "https://httpbin.org/stream/10") as response:
    for line in response.iter_lines():
        print(line)

# 按字节块下载
with open("large_file.zip", "wb") as f:
    with httpx.stream("GET", "https://example.com/large_file.zip") as response:
        for chunk in response.iter_bytes(chunk_size=1024*1024):
            f.write(chunk)
```

### 8.2 流式上传
```python
# 流式上传大文件
with open("large_file.zip", "rb") as f:
    response = httpx.post("https://httpbin.org/post", content=f)
```

## 九、异常处理

### 9.1 常见异常
```python
try:
    response = httpx.get("https://httpbin.org/delay/10", timeout=5.0)
    response.raise_for_status()
except httpx.TimeoutException:
    print("请求超时")
except httpx.ConnectError:
    print("连接失败")
except httpx.HTTPStatusError as e:
    print(f"HTTP错误: {e.response.status_code}")
except httpx.RequestError as e:
    print(f"请求错误: {e}")
```

### 9.2 异常层次结构
```
httpx.Exception
├── httpx.RequestError
│   ├── httpx.TransportError
│   │   ├── httpx.ConnectError
│   │   ├── httpx.ReadError
│   │   ├── httpx.WriteError
│   │   └── httpx.TimeoutException
│   └── httpx.ProxyError
├── httpx.HTTPStatusError
├── httpx.DecodingError
└── httpx.UnsupportedProtocol
```

## 十、客户端实例与连接池

### 10.1 连接池原理
httpx的`Client`和`AsyncClient`内部维护了一个连接池，可以自动复用连接，减少TCP握手开销。

### 10.2 连接池配置
```python
limits = httpx.Limits(
    max_connections=100,          # 最大并发连接数
    max_keepalive_connections=20, # 最大保持连接数
    keepalive_expiry=5.0          # 保持连接过期时间（秒）
)

client = httpx.Client(limits=limits)
```

### 10.3 客户端复用最佳实践
```python
# 错误：每次请求都创建新客户端
for i in range(10):
    response = httpx.get("https://httpbin.org/get")

# 正确：复用同一个客户端
with httpx.Client() as client:
    for i in range(10):
        response = client.get("https://httpbin.org/get")
```

## 十一、最佳实践

### 11.1 性能优化
1. **复用客户端实例**：始终使用`Client`或`AsyncClient`而不是顶级函数
2. **启用HTTP/2**：对于支持HTTP/2的服务器，启用HTTP/2可以显著提高性能
3. **合理设置超时**：避免长时间等待
4. **使用连接池**：合理配置连接池大小

### 11.2 安全性
1. **不要禁用SSL验证**：除非在开发环境中
2. **保护敏感信息**：不要在代码中硬编码密码和令牌
3. **使用安全的认证方式**：优先使用Bearer令牌或OAuth2

### 11.3 错误处理
1. **总是处理异常**：网络请求可能会失败
2. **使用raise_for_status()**：自动检查HTTP状态码
3. **提供有意义的错误信息**

### 11.4 异步最佳实践
1. **使用async/await**：正确使用异步语法
2. **并发请求**：使用`asyncio.gather()`并发执行多个请求
3. **避免阻塞操作**：在异步代码中不要使用同步IO操作

## 十二、与requests的对比

| 特性 | httpx | requests |
|------|-------|----------|
| 同步API | ✅ | ✅ |
| 异步API | ✅ | ❌ |
| HTTP/2支持 | ✅ | ❌ |
| 类型提示 | ✅ | 有限 |
| 连接池 | ✅ | ✅ |
| 流式上传/下载 | ✅ | ✅ |
| 请求/响应钩子 | ✅ | ✅ |
| 自动解压缩 | ✅ | ✅ |
| 代理支持 | ✅ | ✅ |
| 认证支持 | ✅ | ✅ |
| 超时控制 | 精细 | 简单 |

## 十三、常见问题解答

### Q1: httpx和requests可以互换吗？
A: 大部分情况下可以。httpx的API设计与requests非常相似，大多数requests代码只需少量修改即可迁移到httpx。

### Q2: 什么时候应该使用异步客户端？
A: 当你需要同时发送多个请求时，使用异步客户端可以显著提高性能。例如爬虫、API聚合服务等。

### Q3: 如何处理会话保持？
A: 使用同一个`Client`或`AsyncClient`实例会自动保持会话状态，包括cookies和连接。

### Q4: 如何自定义用户代理？
A: 可以在创建客户端时设置默认头，或者在单个请求中设置：
```python
client = httpx.Client(headers={"User-Agent": "my-app/1.0.0"})
# 或
response = client.get("/get", headers={"User-Agent": "custom-agent"})
```