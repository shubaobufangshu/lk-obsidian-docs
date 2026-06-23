# JavaScript

## 严格模式
```javascript
'use strict';
```
- JavaScript 默认从上到下顺序执行，单线程。
- BOM 可实现多线程效果。
- 禁止隐式全局变量（`a=1` 报错）
- 禁止 `this` 指向 `window`
- 禁止重复声明变量

---

## JavaScript 基础

### 简介
JavaScript 是一种轻量级、解释型编程语言，主要用于网页开发，也用于 Node.js 等非浏览器环境。动态类型、基于原型的面向对象脚本语言。

```javascript
JavaScript {
    ECMAScript：语言核心
    Web API {
        DOM：页面文档对象
        BOM：浏览器对象
    }
}
```

### JavaScript 的嵌入方式

| 方式 | 语法                              | 说明                                 |
| ---- | --------------------------------- | ------------------------------------ |
| 行内 | `<标签 onclick="alert(1)">`       | 在标签属性内写少量代码               |
| 内嵌 | `<script> //代码 </script>`       | 写在 HTML 的 `<body>` 或 `<head>` 中 |
| 外部 | `<script src="js/a.js"></script>` | 引入外部 `.js` 文件                  |

### 输入输出

| 方法               | 作用                                          | 示例                               |
| ------------------ | --------------------------------------------- | ---------------------------------- |
| `alert()`          | 弹出警示框                                    | `alert('Hello')`                   |
| `document.write()` | 向 body 输出内容/HTML                         | `document.write('<h1>标题</h1>')`  |
| `console.log()`    | 向控制台输出                                  | `console.log('调试信息')`          |
| `prompt()`         | 弹出输入对话框，返回用户输入的值              | `let a = prompt('请输入姓名')`     |
| 模板字符串         | 使用反引号 `` ` `` 包裹，`${变量}` 格式化输出 | `` console.log(`姓名：${name}`) `` |

### 注释与结束符

- **单行注释**：`// 注释内容`
- **多行注释**：`/* 注释内容 */`
- 语句结束符：分号 `;`（可省略，但建议保留）

---

### 变量、数据类型与运算

> **NaN**：Not a Number，表示非数值。

#### 变量声明

| 关键字  | 作用域     | 特点                                           | 示例           |
| ------- | ---------- | ---------------------------------------------- | -------------- |
| `var`   | 函数作用域 | 可重复声明，可重新赋值                         | `var a = 1;`   |
| `let`   | 块级作用域 | 不可重复声明，可重新赋值                       | `let b = 2;`   |
| `const` | 块级作用域 | 不可重复声明，不可重新赋值（引用类型内部可变） | `const c = 3;` |

#### 数据类型

**基本类型**：
- `string`：字符串
- `number`：整数 / 浮点数
- `boolean`：`true` / `false`
- `undefined`：未定义
- `null`：空值
- `symbol`（ES6）：唯一且不可变，`const s = Symbol('id'); const s2 = Symbol('id'); s !== s2`（唯一性）
- `bigint`（ES2020）：大整数，`const num = 123n; const num2 = BigInt(123)`（大整数场景）

**引用类型**：
- `object`：对象（含数组、函数等）

#### 类型检查
```javascript
typeof "hello";   // "string"
typeof 123;       // "number"
typeof(123);      // 括号可选
Object.prototype.toString.call([]) // "[object Array]" //精准类型判断
```

#### 类型转换

| 方式     | 方法                                                         | 说明                                   |
| -------- | ------------------------------------------------------------ | -------------------------------------- |
| 显式转换 | `Number(变量)`、`parseInt(数据)`、`parseFloat(数据)`，String(123)`、`Boolean(0) | `parseInt` 取整，`parseFloat` 保留小数 |
| 隐式转换 | `+` 运算符，隐式转换坑：`[] == ![] // true`、`0 == '' // true` | 字符串拼接，或自动将数字字符串转为数值 |

#### 运算符

**算术运算符**：`+` `-` `*` `/` `%`（取余） `**`（幂）
**比较运算符**：`==`（值相等） `===`（全等） `!=` `!==` `>` `<` `>=` `<=`
**逻辑运算符**：`&&`（与） `||`（或） `!`（非）
**自增/自减**：`++a`（先加后赋值） `a++`（先赋值后加），`--` 同理
**赋值运算符**：`=` `+=` `-=` `*=` `/=` `%=` `**=`

示例：
```javascript
let a = 10, b = 3;
console.log(a ** b); // 1000

let x = true, y = false;
console.log(x && y); // false

let c = 5;
c += 3; // c = 8
```

---

### 控制流程

#### 条件语句

- `if / else if / else`

- `switch`（配合 `break`，`default`）

- 三元运算符：`条件 ? 真值 : 假值`

  ```javascript
  // if-else
  let score = 85;
  if (score >= 90) {
      console.log("优秀");
  } else if (score >= 80) {
      console.log("良好");
  } else if (score >= 60) {
      console.log("及格");
  } else {
      console.log("不及格");
  }
  // switch
  let day = 3;
  let dayName;
  switch (day) {
      case 1:
          dayName = "星期一";
          break;
      case 2:
          dayName = "星期二";
          break;
      case 3:
          dayName = "星期三";
          break;
      default:
          dayName = "未知";
  }
  console.log(dayName); // "星期三"
  // 三元运算符
  let age = 20;
  let canVote = age >= 18 ? "可以投票" : "不能投票";
  ```

  

#### 循环语句

| 循环              | 适用场景                         | 示例                          |
| ----------------- | -------------------------------- | ----------------------------- |
| `for`             | 已知次数                         | `for (let i = 0; i < 5; i++)` |
| `while`           | 条件前置                         | `while (j < 5) { j++; }`      |
| `do-while`        | 至少执行一次                     | `do {...} while (k < 5);`     |
| `for...of`（ES6） | 遍历可迭代对象（数组、字符串等） | `for (let fruit of fruits)`   |
| `for...in`        | 遍历对象属性                     | `for (let key in person)`     |

```javascript
// for 循环
for (let i = 0; i < 5; i++) {
    console.log(i); // 0, 1, 2, 3, 4
}
// while 循环
let j = 0;
while (j < 5) {
    console.log(j); // 0, 1, 2, 3, 4
    j++;
}
// do-while 循环
let k = 0;
do {
    console.log(k); // 0, 1, 2, 3, 4
    k++;
} while (k < 5);
// for...of 循环 (ES6)
let fruits = ["苹果", "香蕉", "橙子"];
for (let fruit of fruits) {
    console.log(fruit); // "苹果", "香蕉", "橙子"
}
// for...in 循环 (遍历对象属性)
let person = {name: "John", age: 30};
for (let key in person) {
    console.log(key + ": " + person[key]); // "name: John", "age: 30"
}
```

#### break/continue

```javascript
// break 跳出循环
for (let i = 0; i < 5; i++) {
  if (i === 3) break;
  console.log(i); // 0,1,2
}
// continue 跳过当前次
for (let i = 0; i < 5; i++) {
  if (i === 3) continue;
  console.log(i); // 0,1,2,4
}
```

#### 断点调试

- 开发者工具 → 源代码（Sources）→ 打断点 → 刷新 → 执行暂停 → 查看数据

---

### 正则表达式

#### 语法
```javascript
const 正则 = /匹配规则/修饰符;
正则.test(字符串);      // 返回 true/false
字符串.replace(/表达式/, 替换内容);
```

#### 元字符

| 类别     | 符号    | 说明                       |
| -------- | ------- | -------------------------- |
| 边界     | `^`     | 以...开头                  |
|          | `$`     | 以...结尾                  |
|          | `.`     | 通配符（除换行外任意字符） |
| 量词     | `*`     | 0 次或多次                 |
|          | `+`     | 1 次或多次                 |
|          | `?`     | 0 次或 1 次                |
|          | `{n,m}` | n 到 m 次                  |
| 字符类   | `[a-z]` | 小写字母范围               |
|          | `[^]`   | 取反（中括号内 `^`）       |
|          | `\d`    | 数字                       |
| 预定义类 | `\D`    | 非数字                     |
|          | `\w`    | 字母、数字、下划线         |
|          | `\W`    | 非（字母、数字、下划线）   |
|          | `\s`    | 空白（空格、换行、制表符） |
|          | `\S`    | 非空白                     |

#### 修饰符
- `i`：不区分大小写
- `g`：全局匹配（所有结果）

#### 正则补充：

- 贪婪 / 非贪婪匹配：`/a+/`（贪婪，匹配多）vs `/a+?/`（非贪婪，匹配少）；
- 分组与反向引用：`/(\d{3})-\1/`（匹配 123-123）；
- 实用示例：手机号验证 `const reg = /^1[3-9]\d{9}$/; reg.test('13800138000')`。

---

### 异常处理

- **抛出异常**：`throw new Error("错误信息")`
- **捕获异常**：
```javascript
try {
    // 可能出错的代码
} catch (err) {
    // 处理错误
} finally {
    // 无论是否出错都会执行
}
```
- **调试**：`debugger;` 语句

---

### 改变 `this` 指向

| 方法    | 语法                                 | 特点                     |
| ------- | ------------------------------------ | ------------------------ |
| `call`  | `fun.call(thisArg, arg1, arg2, ...)` | 立即调用，参数逐个传递   |
| `apply` | `fun.apply(thisArg, [argsArray])`    | 立即调用，参数以数组传递 |
| `bind`  | `fun.bind(thisArg, arg1, ...)`       | 返回新函数，不立即调用   |

示例：
```javascript
function show(age) {
    console.log(this.name, age);
}
const obj = { name: "张三" };
show.call(obj, 20);       // 立即执行
show.apply(obj, [20]);    // 立即执行
const fn = show.bind(obj, 20);
fn();                     // 稍后执行
```

## JavaScript 对象、数组和字符串

### 对象（Object）

对象是键值对的集合，用于存储多个相关值。

#### 创建对象

```javascript
// 字面量
let person = {
    firstName: "John",
    lastName: "Doe",
    age: 30,
    greet() { console.log("Hello, " + this.firstName); }
};

// new Object()
let car = new Object();
car.brand = "Toyota";

// 构造函数
function Person(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
}
let p = new Person("Jane", "Doe");
```

- **属性**：描述特征  
- **方法**：定义行为  

#### 访问与操作属性

```javascript
console.log(person.firstName);      // 点语法
console.log(person['lastName']);    // 方括号
let key = 'age';
console.log(person[key]);           // 动态属性名

person.age = 31;                    // 修改
person.nationality = "American";    // 添加
delete person.isStudent;            // 删除
```

#### 遍历对象

```javascript
for (let key in person) {
    console.log(key, person[key]);
}
Object.keys(person);    // 键数组
Object.values(person);  // 值数组
Object.entries(person); // 键值对数组
```

---

### 数组（Array）

有序集合，可存储任意类型。

#### 创建数组

```javascript
let fruits = ["apple", "banana"];
let numbers = new Array(1, 2, 3);
let mixed = [1, "hello", true, {name: "John"}, [1,2,3]];
```

#### 基本操作

```javascript
console.log(fruits[0]);       // 访问
fruits[1] = "grape";          // 修改
console.log(fruits.length);   // 长度
```

#### 常用方法

| 类别 | 方法                                   | 示例                             | 说明                 |
| ---- | -------------------------------------- | -------------------------------- | -------------------- |
| 增   | `push(v)`                              | `arr.push(4)`                    | 末尾添加             |
| 删   | `pop()`                                | `arr.pop()`                      | 末尾删除并返回       |
| 增   | `unshift(v)`                           | `arr.unshift(0)`                 | 开头添加             |
| 删   | `shift()`                              | `arr.shift()`                    | 开头删除             |
| 删   | `splice(start, deleteCount, ...items)` | `arr.splice(1,0,'a')`            | 任意位置增删         |
| 遍历 | `forEach(fn)`                          | `arr.forEach(v=>console.log(v))` | 遍历                 |
| 遍历 | `map(fn)`                              | `arr.map(x=>x*2)`                | 映射为新数组         |
|      | `filter(fn)`                           | `arr.filter(x=>x%2===0)`         | 过滤                 |
|      | `reduce(fn, init)`                     | `arr.reduce((s,x)=>s+x,0)`       | 累积                 |
|      | `find(fn)`                             | `arr.find(x=>x>2)`               | 返回第一个匹配元素   |
|      | `findIndex(fn)`                        | `arr.findIndex(x=>x>2)`          | 返回索引             |
| 其他 | `slice(start,end)`                     | `arr.slice(1,3)`                 | 切片（不改变原数组） |
|      | `concat(arr2)`                         | `arr.concat([6,7])`              | 合并                 |
|      | `indexOf(v)`                           | `arr.indexOf(3)`                 | 查找索引             |
|      | `includes(v)`                          | `arr.includes(3)`                | 是否包含             |
|      | `sort(fn)`                             | `arr.sort((a,b)=>a-b)`           | 排序（数字升序）     |
|      | `reverse()`                            | `arr.reverse()`                  | 反转                 |

---

### 字符串（String）

#### 创建与基本操作

```javascript
let str1 = "Hello";
let str2 = 'World';
let str3 = `Hello World`;   // 模板字符串

let greeting = str1 + " " + str2;          // 拼接
let template = `${str1} ${str2}`;          // 模板拼接

console.log(greeting.length);              // 长度
console.log(greeting[0]);                  // 'H'
console.log(greeting.charAt(0));           // 'H'
```

#### 常用方法

| 类别     | 方法                                   | 示例                        | 说明                |
| -------- | -------------------------------------- | --------------------------- | ------------------- |
| 查找     | `indexOf(sub)`                         | `str.indexOf("World")`      | 返回索引，未找到 -1 |
|          | `includes(sub)`                        | `str.includes("Hello")`     | 是否包含            |
|          | `startsWith(sub)`                      | `str.startsWith("Hello")`   | 是否以...开头       |
|          | `endsWith(sub)`                        | `str.endsWith("!")`         | 是否以...结尾       |
| 提取     | `slice(start,end)`                     | `str.slice(7,12)`           | 子串                |
|          | `substring(start,end)`                 | 同 slice                    | 不支持负数          |
|          | `substr(start,length)`                 | 已废弃                      | 不推荐              |
| 替换     | `replace(old, new)`                    | `str.replace("World","JS")` | 替换首个            |
| 大小写   | `toUpperCase()` / `toLowerCase()`      |                             |                     |
| 去空白   | `trim()` / `trimStart()` / `trimEnd()` |                             |                     |
| 分割     | `split(sep)`                           | `"a,b,c".split(",")`        | 返回数组            |
| 补零常用 | padStart()`/`padEnd()                  |                             | 补零常用            |

#### 模板字符串（ES6）

```javascript
let name = "Alice", age = 25;
let multi = `
  姓名: ${name}
  年龄: ${age}
  出生年: ${new Date().getFullYear() - age}
`;
let expr = `2+3=${2+3}`;   // "2+3=5"
```

---

## 函数

### 函数定义与调用

```javascript
// 函数声明（可提升）
function greet(name) {
    return "Hello " + name;
}

// 函数表达式（不提升）
const square = function(x) { return x * x; };

// 箭头函数
const multiply = (a, b) => a * b;
// 默认参数 (ES6)
function createUser(name, age = 18) {
    return {name, age};
}
// 剩余参数 (ES6)
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}
// 调用
greet("Alice");    // "Hello Alice"
square(5);         // 25
multiply(4,3);     // 12
console.log(createUser("Bob"));     // {name: "Bob", age: 18}
console.log(sum(1, 2, 3, 4, 5));    // 15
```

### 参数处理

- **默认参数**：`function fn(a, b = 1) {}`
- **剩余参数**：`function sum(...nums) { return nums.reduce((s,v)=>s+v,0); }`
- **动态参数**：`arguments`（伪数组，箭头函数中无）
- **参数解构**：`function print({ name, age }) {}`

### 箭头函数深入

```javascript
// 简写
const add = (a,b) => a + b;
const square = x => x * x;
const getRandom = () => Math.random();

// 多行需 {} + return
const createUser = (name, age) => {
    return { name, age };
};

// 返回对象字面量需加括号
const makePoint = (x,y) => ({ x, y });

// 箭头函数没有自己的 this，继承外层
const obj = {
    name: "JS",
    regular: function() { console.log(this.name); }, // "JS"
    arrow: () => console.log(this.name)              // undefined
};
```

### 高阶函数

- **函数作为参数**：`operate(10,5,(x,y)=>x+y)`
- **函数作为返回值**：`createMultiplier(m) => (n) => m*n`
- **数组方法**：`map`、`filter`、`reduce`、`sort` 等

### 立即执行函数（IIFE）

```javascript
(function() { console.log("立即执行"); })();
(() => console.log("箭头IIFE"))();

// 带参数
(function(name) { console.log("Hello", name); })("World");

// 返回值
const counter = (function() {
    let count = 0;
    return { inc: () => ++count, get: () => count };
})();
```

### 逻辑中断（短路）

```javascript
// &&：遇到 false 返回该值，否则返回最后一个
console.log(0 && 2);  // 0
console.log(1 && 3);  // 3

// ||：遇到 true 返回该值，否则返回最后一个
console.log(0 || 2);  // 2
console.log(1 || 3);  // 1
```

---

#### 函数补充：

- `arguments` 转真数组：`Array.from(arguments)` 或 `[...arguments]`；

- 柯里化示例：

  ```
  function curry(fn) {
    return function curried(...args) {
      if (args.length >= fn.length) return fn(...args);
      return (...next) => curried(...args, ...next);
    };
  }
  const add = (a,b,c) => a+b+c;
  const curriedAdd = curry(add);
  curriedAdd(1)(2)(3); // 6
  ```

### 作用域（Scope）

| 类型       | 关键字         | 特点                   |
| ---------- | -------------- | ---------------------- |
| 全局作用域 | 全局声明       | 任何地方可访问         |
| 函数作用域 | `var`          | 在函数内有效，变量提升 |
| 块级作用域 | `let`、`const` | 在 `{}` 内有效，无提升 |

**作用域链**：内部函数可访问外部函数的变量，反之不成立。

**变量提升**：
- `var` 声明提升，赋值不提升 → 值为 `undefined`
- `let`/`const` 不提升 → 暂时性死区
- 函数声明整体提升

```javascript
console.log(a);   // undefined
var a = 1;

console.log(b);   // ReferenceError
let b = 2;
```

#### 作用域补充：`for` 循环中 `var` vs `let` 对比示例：

```
// var 无块级作用域，i 共享
for (var i = 0; i < 3; i++) { setTimeout(() => console.log(i), 0); } // 3,3,3
// let 有块级作用域，每次循环创建新 i
for (let i = 0; i < 3; i++) { setTimeout(() => console.log(i), 0); } // 0,1,2
```

### 闭包（Closure）

**定义**：函数 + 它能访问的外部变量（即使外部函数已执行完毕）。

```javascript
function createCounter() {
    let count = 0;
    return function() { return ++count; };
}
const c1 = createCounter();
console.log(c1()); // 1
console.log(c1()); // 2
```

#### 常见用途

1. **数据私有化**
2. **函数工厂**
3. **缓存（Memoization）**
4. **模块模式**

```javascript
// 模块模式示例
const Calculator = (function() {
    let history = [];
    function add(a,b) { let r = a+b; history.push(`${a}+${b}=${r}`); return r; }
    function getHistory() { return [...history]; }
    return { add, getHistory };
})();
```

#### 闭包补充：

- 内存泄漏风险：闭包引用的变量不会被垃圾回收，需及时解除引用；

### 递归函数

函数调用自身，需有终止条件。

```javascript
// 阶乘
function factorial(n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}

// 斐波那契（优化缓存）
function memoizedFib() {
    const cache = new Map();
    return function fib(n) {
        if (cache.has(n)) return cache.get(n);
        if (n <= 1) { cache.set(n, n); return n; }
        const res = fib(n-1) + fib(n-2);
        cache.set(n, res);
        return res;
    };
}
const fib = memoizedFib();
console.log(fib(40)); // 快速计算
```

## JavaScript API

> API 使 JS 能操作页面元素。

### DOM（文档对象模型）

#### DOM 树
- 将 HTML 文档以树形结构表示标签之间的关系
- `window` 包含 `document`
- 整个 HTML 对应的根对象是 `document`

#### 获取 DOM 元素

**CSS 选择器方式**（推荐）：
- `document.querySelector("选择器")` → 返回第一个匹配元素
- `document.querySelectorAll("选择器")` → 返回伪数组，可遍历

**传统方式**：

- `document.getElementById("id")`
- `document.getElementsByClassName("类名")`
- `document.getElementsByTagName("标签名")`

**`closest()` 方法**:

+ `elem.closest('.box')`（向上查找最近的.box 元素）；	

#### 操作元素内容与属性

| 属性/方法                 | 作用                                                         |
| ------------------------- | ------------------------------------------------------------ |
| `innerText`               | 纯文本内容                                                   |
| `innerHTML`               | 解析 HTML 标签后渲染                                         |
| `href`、`title`、`src` 等 | 标准属性直接读写                                             |
| `style`                   | 操作行内样式：`元素.style.css属性 = "值"`                    |
| `className`               | 替换或设置类名（适合批量修改样式）                           |
| `classList`               | 类名集合操作：<br> `add(类名)` 追加<br> `remove(类名)` 移除<br> `toggle(类名)` 切换 |

```javascript
const btn = document.getElementById("greetButton");
btn.innerText = "点击我";
btn.classList.add("active");
```

#### 操作表单元素
- 获取值：`dom对象.属性名`
- 设置值：`dom对象.属性 = 新值`

##### **表单操作**:

checkbox 的 `checked`、select 的 `value`/`selectedIndex`：

```javascript
// 复选框
const cb = document.querySelector('input[type="checkbox"]');
console.log(cb.checked); // true/false
// 下拉框
const sel = document.querySelector('select');
console.log(sel.value); // 选中值
sel.selectedIndex = 1; // 选中第二个选项
```

#### 自定义属性 `data-*`
```html
<h1 data-id="123">标题</h1>
```
```javascript
const h1 = document.querySelector('h1');
console.log(h1.dataset.id);  // "123"
```

#### 间歇函数（定时器）
```javascript
let timer = setInterval(回调函数, 间隔毫秒);
clearInterval(timer);  // 清除
```

#### 事件

**绑定方式**：
```javascript
元素.on事件名 = function() {}
// 或（推荐）
元素.addEventListener("事件名", 事件处理函数);
```

**解绑**：
- `元素.on事件名 = null`
- `元素.removeEventListener("事件名", 函数名)`（需使用具名函数）

**常用事件**：

| 事件                       | 触发时机                            |
| -------------------------- | ----------------------------------- |
| `click`                    | 鼠标单击                            |
| `mouseover` / `mouseenter` | 鼠标经过（前者冒泡）                |
| `mouseout` / `mouseleave`  | 鼠标离开（前者冒泡）                |
| `focus`                    | 表单获得光标                        |
| `blur`                     | 表单失去光标                        |
| `keydown` / `keyup`        | 键盘按下/抬起                       |
| `input`                    | 表单用户输入                        |
| `load`                     | 页面资源加载完成（通常给 `window`） |
| `DOMContentLoaded`         | DOM 树构建完成（无需等待图片等）    |
| `scroll`                   | 页面滚动（给 `window` 添加）        |
| `resize`                   | 窗口尺寸变化                        |

**事件对象属性**（回调的第一个参数 `e`）：
- `e.type`：事件类型
- `e.clientX` / `e.clientY`：光标相对于窗口左上角
- `e.offsetX` / `e.offsetY`：光标相对于目标元素左上角
- `e.key`：按下的键值
- `e.stopPropagation()`：阻止冒泡
- `e.preventDefault()`：阻止默认行为

#### 环境对象 `this`
- 普通函数中 `this` 指向调用者
- 全局函数中 `this` 指向 `window`

#### 事件流（捕获/冒泡）
- 默认冒泡阶段触发
- 阻止冒泡：`e.stopPropagation()`

#### 事件委托
利用冒泡机制，将事件监听器绑定到父元素，通过 `e.target` 判断实际触发元素。

```javascript
父元素.addEventListener('click', function(e) {
    if (e.target.tagName === 'LI') {
        // 处理 li 的点击
    }
});
```

**事件委托完整示例：**

```
const ul = document.querySelector('ul');
ul.addEventListener('click', (e) => {
  if (e.target.tagName === 'LI') {
    e.target.style.color = 'red';
  }
});
```

### DOM 节点操作

#### 节点类型
- 元素节点、属性节点、文本节点等

#### 查找节点
- 子元素获取父元素：`子元素.parentNode`
- 父元素获取所有子元素：`父元素.children`（返回集合）

#### 增加节点
```javascript
// 创建
const newNode = document.createElement("标签名");

// 追加
父元素.appendChild(newNode);

// 插入到指定子元素之前
父元素.insertBefore(newNode, 参考子元素);

// 复制节点
元素.cloneNode(布尔值);   // true 深克隆（含后代），false 浅克隆
```

#### 删除节点
```javascript
父元素.removeChild(子元素);
```

#### 移动端触屏事件
- `touchstart`：手指触摸
- `touchmove`：手指滑动
- `touchend`：手指离开

> 推荐使用 **Swiper** 库（https://www.swiper.com）实现移动端滑动效果。

---

### BOM（浏览器对象模型）

`window` 是全局对象，包含：
- `navigator`、`location`、`document`、`history`、`screen`
- `alert()`、`console.log()` 等实际是 `window` 的方法
- 用 `var` 声明的全局变量会成为 `window` 的属性

#### 延时函数
```javascript
let timer = setTimeout(回调, 延迟毫秒);
clearTimeout(timer);
```

#### location 对象（URL 操作）
| 属性/方法      | 作用                              |
| -------------- | --------------------------------- |
| `href`         | 完整 URL，赋值可跳转              |
| `search`       | `?` 后的查询参数                  |
| `hash`         | `#` 后的锚点                      |
| `reload(true)` | 刷新页面，`true` 强制从服务器刷新 |

#### navigator 对象
- `navigator.userAgent`：检测浏览器信息

#### history 对象
- `back()`：后退
- `forward()`：前进
- `go(数字)`：正数前进，负数后退

#### BOM 补充：

- 滚动方法：`window.scrollTo({ top: 0, behavior: 'smooth' })`（平滑滚动）；

```
URLSearchParams
```
- 解析查询参数：

```
const params = new URLSearchParams('?name=张三&age=20');
params.get('name'); // 张三
```

### 本地存储

+ localStorage 存储复杂数据的坑：`JSON.stringify` 无法序列化循环引用对象；
+ IndexedDB 补充事务失败的错误处理；
+ Cookie 补充 `SameSite` 属性（防止 CSRF）、中文处理（`encodeURIComponent`）。

#### localStorage
- 永久存储（除非手动清除）
- 容量约 5MB
- 多窗口共享
- 只能存字符串

```javascript
// 存储
localStorage.setItem('key', 'value');
// 读取
localStorage.getItem('key');
// 删除
localStorage.removeItem('key');
// 修改（重新 set）
localStorage.setItem('key', 'newValue');
```

#### sessionStorage
- 会话级存储，关闭页面即清除
- 其他特点同 `localStorage`

#### 存储复杂数据类型
```javascript
// 存储对象
const obj = { name: "张三" };
localStorage.setItem('user', JSON.stringify(obj));
// 读取
const user = JSON.parse(localStorage.getItem('user'));
```

---

### IndexedDB（浏览器内置 NoSQL 数据库）

#### 打开/创建数据库
```javascript
const request = indexedDB.open("MyDB", 1);
let db;

request.onupgradeneeded = (e) => {
    db = e.target.result;
    if (!db.objectStoreNames.contains("userStore")) {
        const store = db.createObjectStore("userStore", {
            keyPath: "id",
            autoIncrement: true
        });
        store.createIndex("nameIndex", "name", { unique: false });
    }
};

request.onsuccess = (e) => { db = e.target.result; };
request.onerror = (e) => { console.error("打开失败", e.target.error); };
```

#### 操作数据（需在事务中）
```javascript
// 新增/修改（put 覆盖，add 不可重复主键）
function addUser(data) {
    const tx = db.transaction("userStore", "readwrite");
    const store = tx.objectStore("userStore");
    store.add(data);
}

// 按主键查询
function getUser(id) {
    const tx = db.transaction("userStore", "readonly");
    const store = tx.objectStore("userStore");
    store.get(id).onsuccess = (e) => console.log(e.target.result);
}

// 按索引查询
function searchByName(keyword) {
    const tx = db.transaction("userStore", "readonly");
    const index = tx.objectStore("userStore").index("nameIndex");
    const range = IDBKeyRange.bound(keyword, keyword + '\uffff');
    index.openCursor(range).onsuccess = (e) => {
        const cursor = e.target.result;
        if (cursor) { console.log(cursor.value); cursor.continue(); }
    };
}

// 删除
function deleteUser(id) {
    const tx = db.transaction("userStore", "readwrite");
    tx.objectStore("userStore").delete(id);
}
```

#### 关闭/删除数据库
```javascript
db.close();
indexedDB.deleteDatabase("MyDB");
```

---

### Cookie

#### 核心规则
- 同源（协议+域名+端口）共享
- 格式：`key=value; 属性列表`
- `HttpOnly` 的 Cookie 无法被 JS 读写

#### 设置 Cookie
```javascript
document.cookie = "username=test; expires=" + new Date(Date.now() + 86400000).toUTCString() + "; path=/";
```
- `expires`：过期时间（GMT 字符串），不设则关闭浏览器失效
- `path=/`：整个站点可访问

#### 读取 Cookie
```javascript
function getCookie(key) {
    const cookies = document.cookie.split("; ");
    for (let c of cookies) {
        const [name, value] = c.split("=");
        if (name === key) return decodeURIComponent(value);
    }
    return null;
}
```

#### 删除 Cookie（设置过期时间为过去）
```javascript
document.cookie = "username=; expires=" + new Date(0).toUTCString() + "; path=/";
```

#### 后端设置 Cookie（Node.js Express 示例）
```javascript
res.cookie("userid", "123", {
    expires: new Date(Date.now() + 86400000),
    path: "/",
    httpOnly: true   // 防止 XSS 读取
});
```

## 异步编程基础

宏任务 / 微任务分类清晰化：

|  类型  |                        包含内容                         |
| :----: | :-----------------------------------------------------: |
| 宏任务 |      setTimeout/setInterval、I/O、UI 交互、script       |
| 微任务 | Promise.then/catch/finally、async/await、queueMicrotask |

### 同步与异步
- **同步**：所有任务在主线程执行栈中依次执行，阻塞后续代码。
- **异步**：通过回调函数实现，不阻塞主线程。常见异步场景：
  - 事件（click、resize 等）
  - 资源加载（load、error）
  - 定时器（setInterval、setTimeout）

### 事件循环（Event Loop）
1. 先执行执行栈中的同步任务
2. 异步任务进入任务队列等待
3. 同步任务执行完毕后，依次将任务队列中的异步任务推入执行栈执行

```javascript
console.log("1"); // 同步
setTimeout(() => console.log("2"), 0); // 宏任务
Promise.resolve().then(() => console.log("3")); // 微任务
console.log("4"); // 同步
// 输出顺序：1 → 4 → 3 → 2
```

### 回调函数（Callback）

```javascript
function doHomework(subject, callback) {
    console.log(`做${subject}作业`);
    setTimeout(() => {
        callback();
    }, 1000);
}
doHomework("数学", () => console.log("完成"));
```

**回调地狱**：多层嵌套，难以维护。

---

### Promise

#### 基本用法
```javascript
const p = new Promise((resolve, reject) => {
    // 异步操作
    success ? resolve("成功") : reject("失败");
});
p.then(res => console.log(res)).catch(err => console.error(err)).finally(() => console.log('无论成败都执行'));;
```

#### 静态方法

| 方法                          | 作用                       |
| ----------------------------- | -------------------------- |
| `Promise.all([p1,p2])`        | 全部成功才成功，结果数组   |
| `Promise.race([p1,p2])`       | 第一个完成的（成功/失败）  |
| `Promise.allSettled([p1,p2])` | 全部完成后返回状态数组     |
| `Promise.any([p1,p2])`        | 第一个成功的，全失败才失败 |

#### 链式调用解决回调地狱
```javascript
getUser(1)
    .then(user => getPosts(user))
    .then(posts => getComments(posts))
    .then(result => console.log(result))
    .catch(console.error);
.finally(() => console.log('无论成败都执行'));
```

---

### async/await

```javascript
async function fetchData() {
    try {
        const user = await getUser(1);
        const posts = await getPosts(user);
        return posts;
    } catch (error) {
        console.error(error);
    }
}
```

**常用模式**：

- 并行请求：`const [a, b] = await Promise.all([api1(), api2()])`
- 重试机制 + 超时控制 + 并发限制

**async/await 补充：**

- 顶层 await（ES2022）：模块中可直接 `await fetch()` 无需包裹函数；

- 重试封装示例：

  ```
  async function fetchWithRetry(url, times = 3) {
    for (let i = 0; i < times; i++) {
      try {
        return await fetch(url).then(res => res.json());
      } catch (e) {
        if (i === times - 1) throw e;
        await delay(1000); // 延迟 1s 重试
      }
    }
  }
  ```

---

## 面向对象编程

### 主流写法

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    console.log(`Hi, ${this.name}`);
  }
  // 静态方法
  static create(name) {
    return new Person(name);
  }
}
// 继承
class Student extends Person {
  constructor(name, grade) {
    super(name); // 调用父类构造器
    this.grade = grade;
  }
}
const s = new Student('张三', 3);
s.sayHi(); // Hi, 张三
```

### 构造函数

```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
}
const p = new Person("张三", 18);
```

### 原型（prototype）

- 每个构造函数都有 `prototype` 属性，指向原型对象
- 原型对象上的属性和方法被所有实例共享
- 实例内部 `__proto__` 指向构造函数的 `prototype`
- 原型对象中的 `constructor` 指向构造函数本身

```javascript
Person.prototype.sayHi = function() {
    console.log(this.name);
};
// 继承：Child.prototype = Object.create(Parent.prototype)
```

---

## 内置对象

### Math

| 方法              | 作用         |
| ----------------- | ------------ |
| `Math.random()`   | [0,1) 随机数 |
| `Math.floor(x)`   | 向下取整     |
| `Math.ceil(x)`    | 向上取整     |
| `Math.max(a,b,c)` | 最大值       |
| `Math.min(a,b,c)` | 最小值       |
| `Math.pow(x,y)`   | x 的 y 次幂  |
| `Math.abs(x)`     | 绝对值       |

**随机整数公式**：`Math.floor(Math.random() * (max - min + 1)) + min`

### 日期（Date）

```javascript
const now = new Date();          // 当前时间
const date = new Date("2025-01-01");

// 获取
now.getFullYear()   // 年
now.getMonth()      // 月 (0~11)
now.getDate()       // 日 (1~31)
now.getDay()        // 星期 (0~6, 0=周日)
now.getHours() / getMinutes() / getSeconds()

// 时间戳（毫秒）
now.getTime()
+new Date()
Date.now()
```

### 数组方法

#### `map()` - 映射，有返回值
```javascript
const newArr = arr.map((item, index) => item * 2);
```

#### `forEach()` - 遍历，无返回值
```javascript
arr.forEach((item, index) => console.log(item));
```

### JSON

```javascript
JSON.stringify(obj)   // 对象 → 字符串
JSON.parse(str)       // 字符串 → 对象
```

### 数组转字符串
- `arr.join(分隔符)` → 默认逗号分隔

## 性能优化

### 防抖（Debounce）

单位时间内频繁触发的事件，只执行最后一次。

**实现**：
- 使用 Lodash：`_.debounce(fn, 延迟时间)`
- 手写：

```javascript
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}
```

### 节流（Throttle）

单位时间内频繁触发的事件，只执行一次。

**实现**：
- 使用 Lodash：`_.throttle(fn, 间隔时间)`
- 手写：

```javascript
function throttle(fn, delay) {
  let timer = null;
  return function (...args) {
    if (!timer) {
      timer = setTimeout(() => {
        fn.apply(this, args);
        timer = null;
      }, delay);
    }
  };
}
```

### DOM 优化：

减少回流重绘（批量修改 DOM、使用 documentFragment）；

### 图片懒加载：

`img loading="lazy"` 或 IntersectionObserver；

### 减少 HTTP 请求：

合并接口、雪碧图、CDN 缓存。

### 实际应用场景

#### 文件上传模拟

```javascript
class FileUploader {
    static async uploadFile(file) {
        console.log(`开始上传: ${file.name}`);
        for (let p = 0; p <= 100; p += 20) {
            await delay(200);
            console.log(`${file.name} 进度: ${p}%`);
        }
        console.log(`${file.name} 完成`);
        return { id: Date.now(), name: file.name };
    }
}

async function uploadMultipleFiles(files) {
    const results = await Promise.all(files.map(f => FileUploader.uploadFile(f)));
    console.log("全部上传完成", results);
    return results;
}
```

#### 数据抓取与缓存

```javascript
class DataFetcher {
    constructor() { this.cache = new Map(); }
    
    async fetchWithCache(url) {
        if (this.cache.has(url)) {
            console.log(`缓存命中: ${url}`);
            return this.cache.get(url);
        }
        console.log(`请求: ${url}`);
        const data = await delay(1000, `数据来自 ${url}`);
        this.cache.set(url, data);
        return data;
    }
    
    clearCache() { this.cache.clear(); }
}

const fetcher = new DataFetcher();
async function fetchMultipleUrls(urls) {
    return Promise.all(urls.map(url => fetcher.fetchWithCache(url)));
}
```

## form-serialize 插件

**作用**：快速收集表单数据。

**引入与使用**：

```javascript
const form = document.querySelector('表单选择器');
const data = serialize(form, { hash: true, empty: true });
```

**规则说明**：

- `name` 属性为提交键名。
- `hash: true` 返回 JS 对象；`false` 返回查询字符串。
- `empty: true` 收集空值；`false` 忽略空字段。

---

## Bootstrap 弹窗

**作用**：快速调用模态框（Modal）。

**引入与使用**：

```html
<!-- 依赖 Bootstrap CSS/JS 及 Popper -->
<link href="bootstrap.min.css" rel="stylesheet">
<script src="bootstrap.bundle.min.js"></script>

<!-- 弹窗结构 -->
<div class="modal fade" id="exampleModal" tabindex="-1">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">标题</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
      </div>
      <div class="modal-body">内容</div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">关闭</button>
        <button type="button" class="btn btn-primary">保存</button>
      </div>
    </div>
  </div>
</div>
```

**JS 控制**：

```javascript
const modal = new bootstrap.Modal(document.getElementById('exampleModal'));
modal.show();   // 显示
modal.hide();   // 隐藏
```

---

## 图片上传

**核心流程**：基于 `FormData` 提交 `multipart/form-data` 格式。

**示例代码**：

```javascript
// 监听文件选择
document.querySelector('.file-input').addEventListener('change', e => {
  const file = e.target.files[0];
  const fd = new FormData();
  fd.append('avatar', file);  // 参数名需与后端一致

  // 发送请求（结合 XMLHttpRequest 或 fetch）
});
```

**要点**：

- 文件数据通过 `e.target.files[0]` 获取。
- 必须使用 `FormData` 添加文件字段。

---

## XMLHttpRequest

**作用**：原生 AJAX 请求。

**基础示例**：

```javascript
const xhr = new XMLHttpRequest();
xhr.open('POST', '请求URL');
xhr.setRequestHeader('Content-Type', 'application/json'); // 按需设置

xhr.addEventListener('loadend', () => {
  console.log(xhr.response); // 响应结果
});

const data = JSON.stringify({ key: 'value' });
xhr.send(data);
```

**关键步骤**：

1. `open` 配置方法与 URL。
2. `setRequestHeader` 设置请求头。
3. `send` 发送请求体（GET 请求通常为 `null`）。
4. `loadend` 事件处理响应。
