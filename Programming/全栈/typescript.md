# TypeScript 指南

## 前言

TypeScript 作为 JavaScript 的超集，为前端/后端开发带来了静态类型的优势，大幅提升代码可维护性和开发效率。本指南从环境搭建到高级特性，全面覆盖 TypeScript 核心知识点，重点补充了 **HTML/CSS/JS/Vue（兼容 VitePress）** 场景下的 TS 实践，适配 VitePress 阅读体验，适合入门到进阶学习。

## 入门与基础环境搭建

### 1. TypeScript 简介

#### 1.1 什么是 TypeScript

TypeScript 是由微软开发的一种**开源的静态类型编程语言**，它是 JavaScript 的超集，即在 JavaScript 的基础上添加了**静态类型系统**和一些面向对象的特性。TypeScript 最终会被编译成纯 JavaScript 代码，可以在任何支持 JavaScript 的环境中运行（如浏览器、Node.js 等）。

#### 1.2 为什么使用 TypeScript

::: tip 核心优势

- **静态类型检查**：在编译阶段就能发现类型错误，减少运行时 bug。
- **更好的代码提示与补全**：IDE（如 VS Code）可以基于类型信息提供更精准的代码提示。
- **增强代码可维护性**：类型声明让代码意图更清晰，便于团队协作和后期维护。
- **兼容 JavaScript 生态**：可以直接使用现有的 JavaScript 库，也支持逐步迁移。
  :::

#### 1.3 TypeScript 与 JavaScript 的关系

- TypeScript 是 JavaScript 的超集，所有合法的 JavaScript 代码在 TypeScript 中都能运行。
- TypeScript 新增了静态类型、接口、类、泛型等特性，这些特性在编译后会被转换为等效的 JavaScript 代码。

### 2. 环境搭建

#### 2.1 安装 Node.js

TypeScript 的编译依赖 Node.js 环境，首先需要安装 Node.js：

- 访问 [Node.js 官网](https://nodejs.org/)，下载并安装 LTS 版本（长期支持版）。
- 安装完成后，在终端（命令行）中输入以下命令验证安装：

```bash
node -v
npm -v
```

若能输出版本号，说明安装成功。

#### 2.2 安装 TypeScript 编译器

使用 npm（Node.js 包管理器）全局安装 TypeScript 编译器 `tsc`：

```bash
npm install -g typescript
```

安装完成后，验证安装：

```bash
tsc -v
```

若输出版本号，说明 TypeScript 编译器安装成功。

#### 2.3 第一个 TypeScript 程序

1. 创建一个名为 `hello.ts` 的文件，输入以下代码：

```typescript
// hello.ts
let message: string = "Hello, TypeScript!";
console.log(message);
```

这里 `: string` 是类型注解，声明 `message` 变量的类型为字符串。

2. 在终端中进入文件所在目录，执行编译命令：

```bash
tsc hello.ts
```

编译成功后，会生成一个同名的 `hello.js` 文件，内容为编译后的 JavaScript 代码：

```javascript
// hello.js
var message = "Hello, TypeScript!";
console.log(message);
```

3. 运行编译后的 JavaScript 文件：

```bash
node hello.js
```

终端会输出：`Hello, TypeScript!`

#### 2.4 配置 tsconfig.json

`tsconfig.json` 是 TypeScript 项目的配置文件，用于指定编译选项和项目结构。

1. 在项目根目录下初始化 `tsconfig.json`：

```bash
tsc --init
```

执行后会生成一个包含默认配置的 `tsconfig.json` 文件。

2. 常用配置项说明（可根据需要修改）：

```json
{
  "compilerOptions": {
    "target": "ES6", // 编译目标 JavaScript 版本（如 ES5、ES6）
    "module": "commonjs", // 模块系统（如 commonjs、ESNext）
    "outDir": "./dist", // 编译输出目录
    "rootDir": "./src", // 源代码目录
    "strict": true, // 启用所有严格类型检查选项
    "esModuleInterop": true, // 兼容 ES 模块和 CommonJS 模块
    "skipLibCheck": true, // 跳过声明文件的类型检查
    "forceConsistentCasingInFileNames": true // 强制文件名大小写一致
  },
  "include": ["src/**/*"], // 需要编译的文件
  "exclude": ["node_modules"] // 排除编译的文件
}
```

3. 配置完成后，直接执行 `tsc` 命令即可按照配置编译整个项目。

## 基础类型

TypeScript 提供了丰富的**静态类型系统**，可以帮助我们在开发阶段就明确变量、函数参数和返回值的类型，从而减少运行时错误。

### 1. 原始类型（Primitive Types）

原始类型是 TypeScript 中最基础的数据类型，包括：`boolean`、`number`、`string`、`null`、`undefined`、`symbol` 和 `bigint`。

#### 1.1 布尔值（boolean）

布尔值只有两个取值：`true` 和 `false`，用于表示逻辑判断的结果。

```typescript
let isDone: boolean = false;
let hasStarted: boolean = true;

// 示例：函数返回布尔值
function isEven(num: number): boolean {
  return num % 2 === 0;
}
console.log(isEven(4)); // 输出: true
```

#### 1.2 数字（number）

TypeScript 中的数字类型支持十进制、二进制、八进制和十六进制，同时也支持浮点数。

```typescript
let decimal: number = 6; // 十进制
let binary: number = 0b1010; // 二进制（ES6+）
let octal: number = 0o744; // 八进制（ES6+）
let hex: number = 0xf00d; // 十六进制
let float: number = 3.14; // 浮点数
let notANumber: number = NaN; // 特殊值：非数字
let infinity: number = Infinity; // 特殊值：无穷大
```

#### 1.3 字符串（string）

字符串类型用于表示文本数据，可以使用单引号 `'`、双引号 `"` 或模板字符串 `` ` ``（支持多行和插值）。

```typescript
let firstName: string = "Alice";
let lastName: string = "Smith";

// 模板字符串：支持多行和变量插值
let fullName: string = `${firstName} ${lastName}`;
let greeting: string = `Hello, ${fullName}! 
Welcome to TypeScript.`;
console.log(greeting);
// 输出:
// Hello, Alice Smith!
// Welcome to TypeScript.
```

#### 1.4 null 和 undefined

`null` 和 `undefined` 是 TypeScript 中的两个特殊类型，分别表示 “空值” 和 “未定义”。

::: warning 严格模式注意
默认情况下，`null` 和 `undefined` 是**所有类型的子类型**（可以赋值给任意类型，如 `number`、`string` 等）。
若在 `tsconfig.json` 中开启了 `strict: true`（严格模式），则 `null` 和 `undefined` 只能赋值给 `void` 和它们自身的类型（需通过联合类型处理）。
:::

```typescript
let u: undefined = undefined;
let n: null = null;

// 严格模式下，需使用联合类型允许 null/undefined
let maybeNumber: number | null = 10;
maybeNumber = null; // 合法
```

#### 1.5 symbol 和 bigint（ES6+ 新增）

- `symbol`：表示唯一的、不可变的原始值，常用于对象的属性键，避免属性名冲突。
- `bigint`：用于表示大于 `2^53 - 1` 的整数（解决数字精度丢失问题）。

```typescript
// symbol 示例
let sym1: symbol = Symbol("key");
let sym2: symbol = Symbol("key");
console.log(sym1 === sym2); // 输出: false（每个 symbol 都是唯一的）

// bigint 示例（需 ES2020+ 支持）
let big: bigint = 100n;
let anotherBig: bigint = BigInt(200);
```

### 2. 复杂类型（Complex Types）

复杂类型是基于原始类型构建的更高级的数据结构，包括：数组、元组、枚举、任意值、void、never、object 等。

#### 2.1 数组（Array）

数组用于存储**同一类型**的多个元素，有两种定义方式：

1. `类型[]`：在类型后加方括号（推荐，更简洁）。
2. `Array<类型>`：使用泛型语法。

```typescript
// 方式一：类型[]
let numbers: number[] = [1, 2, 3, 4];
let strings: string[] = ["a", "b", "c"];

// 方式二：Array<类型>
let booleans: Array<boolean> = [true, false, true];

// 示例：数组方法的类型提示
numbers.push(5); // 合法（只能 push number 类型）
// numbers.push("6"); // 报错：类型 "string" 不能赋值给类型 "number"
```

#### 2.2 元组（Tuple）

元组是一种**固定长度**且**每个位置的类型已知**的数组，允许存储不同类型的元素。

```typescript
// 定义一个元组：第一个元素是 string，第二个是 number
let person: [string, number] = ["Alice", 25];

// 访问元组元素
console.log(person[0]); // 输出: Alice
console.log(person[1]); // 输出: 25

// 元组的越界访问（TypeScript 2.7+ 会严格限制）
// person[2] = "extra"; // 报错：长度为 "2" 的元组类型 "[string, number]" 在索引 "2" 处没有元素

// 元组的可选元素（使用 ? 标记）
let tupleWithOptional: [string, number?] = ["Bob"]; // 第二个元素可选
```

#### 2.3 枚举（Enum）

枚举用于定义一组**命名的常量集合**，使代码更具可读性和可维护性。枚举分为数字枚举和字符串枚举。

##### 数字枚举（默认）

数字枚举的成员默认从 `0` 开始递增，也可以手动指定初始值。

```typescript
// 默认数字枚举（从 0 开始）
enum Direction {
  Up, // 0
  Down, // 1
  Left, // 2
  Right, // 3
}
let dir: Direction = Direction.Up;
console.log(dir); // 输出: 0

// 手动指定初始值
enum Status {
  Success = 200,
  NotFound = 404,
  ServerError = 500,
}
console.log(Status.NotFound); // 输出: 404
```

##### 字符串枚举

字符串枚举的每个成员都必须是字符串字面量，更直观且便于调试。

```typescript
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE",
}
let favoriteColor: Color = Color.Green;
console.log(favoriteColor); // 输出: "GREEN"
```

#### 2.4 任意值（any）

`any` 类型表示 “任意类型”，可以绕过 TypeScript 的类型检查，适用于不确定类型的场景（如来自第三方库的数据）。

::: danger 慎用 any
过度使用 `any` 会失去 TypeScript 的类型安全优势，建议尽量避免。
:::

```typescript
let notSure: any = 4;
notSure = "hello"; // 合法（可以赋值为任意类型）
notSure = true; // 合法

// 访问 any 类型的属性和方法（不会报错，但运行时可能出错）
notSure.toUpperCase(); // 编译通过，但若 notSure 不是字符串，运行时会报错

// 示例：处理第三方库数据
let dataFromAPI: any = fetchDataFromAPI(); // 假设 API 返回类型不确定
```

#### 2.5 void

`void` 类型表示 “没有任何类型”，通常用于**函数没有返回值**的情况。

- 对于变量，`void` 类型只能赋值为 `undefined` 或 `null`（严格模式下只能赋值为 `undefined`）。

```typescript
// 函数没有返回值，返回类型为 void
function logMessage(message: string): void {
  console.log(message);
  // 不需要 return，或 return undefined
}

// void 类型的变量
let unusable: void = undefined;
// unusable = null; // 严格模式下报错
```

#### 2.6 never

`never` 类型表示**永不存在的值的类型**，常用于：

- 函数抛出错误时（永远不会正常返回）。
- 函数无限循环时（永远不会结束）。
- 类型守卫中处理不可能的分支。

```typescript
// 函数抛出错误，返回类型为 never
function throwError(message: string): never {
  throw new Error(message);
}

// 函数无限循环，返回类型为 never
function infiniteLoop(): never {
  while (true) {}
}

// 类型守卫示例：处理不可能的分支
type Shape = Circle | Square;
function handleShape(shape: Shape) {
  if (shape.kind === "circle") {
    // 处理圆形
  } else if (shape.kind === "square") {
    // 处理正方形
  } else {
    // 这里的 shape 类型为 never（不可能到达的分支）
    const exhaustiveCheck: never = shape;
  }
}
```

#### 2.7 object

`object` 类型表示**非原始类型**（即除了 `number`、`string`、`boolean`、`symbol`、`null`、`undefined` 之外的类型），如对象、数组、函数等。

::: info 最佳实践
`object` 类型比较宽泛，通常建议使用 **接口（interface）** 或 **类型别名（type alias）** 来更精确地定义对象类型。
:::

```typescript
let obj: object = { name: "Alice" };
let arr: object = [1, 2, 3];
let func: object = function () {};

// 错误示例：原始类型不能赋值给 object
// let num: object = 10; // 报错
```

### 3. 类型断言（Type Assertion）

类型断言用于**手动指定一个值的类型**，告诉 TypeScript 编译器 “我比你更清楚这个值的类型”。类型断言有两种语法：

1. `<类型>值`（尖括号语法）。
2. `值 as 类型`（as 语法，推荐，因为在 JSX 中只能用 as 语法）。

::: note 重要提示
类型断言只是编译时的语法，不会改变运行时的类型。
:::

```typescript
// 示例：将 any 类型断言为 string
let someValue: any = "hello world";

// 语法一：尖括号
let strLength1: number = (<string>someValue).length;

// 语法二：as（推荐）
let strLength2: number = (someValue as string).length;
```

## 函数详解

函数是 TypeScript 中构建应用程序的核心模块之一，TypeScript 为函数提供了**完整的类型系统支持**，包括参数类型、返回值类型、函数重载等特性。

### 1. 函数的基本定义与类型注解

在 TypeScript 中，函数可以通过**函数声明**或**函数表达式**定义，并且可以为参数和返回值添加类型注解。

#### 1.1 函数声明

函数声明是最常见的函数定义方式，直接在参数后加 `: 类型` 注解参数类型，在函数名后加 `: 类型` 注解返回值类型。

```typescript
// 函数声明：参数 a 和 b 为 number 类型，返回值为 number 类型
function add(a: number, b: number): number {
  return a + b;
}

let result: number = add(1, 2); // 合法
// add(1, "2"); // 报错：类型 "string" 不能赋值给类型 "number"
```

#### 1.2 函数表达式

函数表达式可以将函数赋值给变量，此时变量的类型可以通过**类型推断**自动确定，也可以显式定义函数类型。

```typescript
// 方式一：类型推断（推荐，更简洁）
const multiply = function (a: number, b: number): number {
  return a * b;
};

// 方式二：显式定义函数类型（变量类型为 (a: number, b: number) => number）
const divide: (a: number, b: number) => number = function (a, b) {
  return a / b;
};
```

#### 1.3 箭头函数

箭头函数是 ES6+ 引入的简洁函数写法，TypeScript 同样支持箭头函数的类型注解。

```typescript
// 箭头函数的类型注解
const subtract = (a: number, b: number): number => a - b;

// 箭头函数作为参数时的类型
const numbers: number[] = [1, 2, 3];
const doubled: number[] = numbers.map((num: number) => num * 2);
```

### 2. 函数参数的高级特性

TypeScript 支持多种灵活的参数处理方式，包括可选参数、默认参数、剩余参数等。

#### 2.1 可选参数

使用 `?` 标记参数为可选，可选参数必须放在**必选参数之后**。

```typescript
// 参数 lastName 是可选的（可以不传）
function greet(firstName: string, lastName?: string): string {
  if (lastName) {
    return `Hello, ${firstName} ${lastName}!`;
  } else {
    return `Hello, ${firstName}!`;
  }
}

console.log(greet("Alice")); // 输出: "Hello, Alice!"
console.log(greet("Alice", "Smith")); // 输出: "Hello, Alice Smith!"
```

#### 2.2 默认参数

为参数设置默认值，当调用函数时未传该参数或传 `undefined` 时，会使用默认值。默认参数可以放在任意位置，但如果默认参数在必选参数之前，必须显式传 `undefined` 才能使用默认值。

```typescript
// 参数 greeting 有默认值 "Hello"
function sayHello(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`;
}

console.log(sayHello("Bob")); // 输出: "Hello, Bob!"
console.log(sayHello("Bob", "Hi")); // 输出: "Hi, Bob!"
console.log(sayHello("Bob", undefined)); // 输出: "Hello, Bob!"
```

#### 2.3 剩余参数

使用 `...` 语法将**任意数量的参数**收集到一个数组中，适用于参数数量不确定的场景。

```typescript
// 剩余参数 numbers 是一个 number 类型的数组
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2)); // 输出: 3
console.log(sum(1, 2, 3, 4)); // 输出: 10
```

#### 2.4 参数解构

可以对对象或数组类型的参数进行解构，同时添加类型注解。

```typescript
// 对象参数解构
interface User {
  name: string;
  age: number;
}
function printUser({ name, age }: User): void {
  console.log(`Name: ${name}, Age: ${age}`);
}
printUser({ name: "Charlie", age: 30 });

// 数组参数解构
function getFirstAndSecond([first, second]: number[]): [number, number] {
  return [first, second];
}
console.log(getFirstAndSecond([10, 20, 30])); // 输出: [10, 20]
```

### 3. 函数返回值类型

#### 3.1 基本返回值类型

函数的返回值类型可以通过类型注解明确指定，TypeScript 也会自动推断返回值类型。

```typescript
// 显式指定返回值类型为 string
function getString(): string {
  return "Hello";
}

// 类型推断：返回值类型自动推断为 number
function getNumber() {
  return 123;
}
```

#### 3.2 void 类型

当函数**没有返回值**时，返回值类型为 `void`（可以省略，TypeScript 会自动推断）。

```typescript
function log(message: string): void {
  console.log(message);
  // 不需要 return，或 return undefined
}
```

#### 3.3 never 类型

当函数**永远不会正常返回**时（如抛出错误或无限循环），返回值类型为 `never`。

```typescript
// 抛出错误，返回 never
function throwError(message: string): never {
  throw new Error(message);
}

// 无限循环，返回 never
function infiniteLoop(): never {
  while (true) {}
}
```

### 4. 函数重载（Function Overloads）

函数重载允许为**同一个函数**定义**多个不同的参数类型和返回值类型**，TypeScript 会根据传入的参数类型自动匹配对应的重载签名。

#### 4.1 重载的定义方式

1. 先定义**多个重载签名**（只有类型注解，没有函数体）。
2. 再定义**一个实现签名**（参数类型和返回值类型要兼容所有重载签名，且必须有函数体）。

```typescript
// 重载签名 1：参数为 string，返回值为 string[]
function splitString(str: string): string[];
// 重载签名 2：参数为 string 和分隔符，返回值为 string[]
function splitString(str: string, separator: string): string[];
// 实现签名（必须兼容所有重载签名）
function splitString(str: string, separator?: string): string[] {
  if (separator) {
    return str.split(separator);
  } else {
    return str.split("");
  }
}

// 调用时会自动匹配重载签名
console.log(splitString("hello")); // 输出: ["h", "e", "l", "l", "o"]
console.log(splitString("a,b,c", ",")); // 输出: ["a", "b", "c"]
```

#### 4.2 重载的注意事项

::: tip 重载规则

- 实现签名必须兼容所有重载签名（参数类型和返回值类型要覆盖所有重载的可能）。
- 调用函数时，TypeScript 会根据参数类型**从上到下**匹配重载签名，因此更具体的重载签名应放在前面。
  :::

### 5. 函数类型与类型别名/接口

可以使用 **类型别名（type alias）** 或 **接口（interface）** 来定义函数类型，使代码更清晰、更易复用。

#### 5.1 使用类型别名定义函数类型

```typescript
// 定义一个函数类型：接收两个 number 参数，返回 number
type MathOperation = (a: number, b: number) => number;

// 使用该类型定义函数
const add: MathOperation = (a, b) => a + b;
const multiply: MathOperation = (a, b) => a * b;
```

#### 5.2 使用接口定义函数类型

```typescript
// 定义一个函数类型的接口
interface StringFormatter {
  (str: string, toUpperCase: boolean): string;
}

// 使用该接口定义函数
const formatString: StringFormatter = (str, toUpperCase) => {
  return toUpperCase ? str.toUpperCase() : str.toLowerCase();
};

console.log(formatString("Hello", true)); // 输出: "HELLO"
console.log(formatString("Hello", false)); // 输出: "hello"
```

### 6. this 类型

在 JavaScript 中，`this` 的指向容易出错，TypeScript 允许通过**this 参数**明确指定函数中 `this` 的类型（this 参数是虚拟参数，放在参数列表的第一位，调用时不需要传）。

```typescript
interface User {
  name: string;
  greet(): void;
}

const user: User = {
  name: "Alice",
  greet() {
    console.log(`Hello, my name is ${this.name}`);
  },
};

// 直接调用，this 指向 user
user.greet(); // 输出: "Hello, my name is Alice"

// 错误示例：将 greet 赋值给变量后调用，this 会指向 undefined（严格模式下）
const greetFn = user.greet;
// greetFn(); // 报错：this 隐式具有类型 "any"

// 解决：使用 this 参数明确类型
interface Greetable {
  name: string;
  greet(this: Greetable): void;
}

const betterUser: Greetable = {
  name: "Bob",
  greet(this: Greetable) {
    console.log(`Hello, my name is ${this.name}`);
  },
};

const betterGreetFn = betterUser.greet;
// betterGreetFn(); // 报错：需要 "this" 类型的参数
betterGreetFn.call(betterUser); // 合法，通过 call 绑定 this
```

## 接口详解

接口（Interface）是 TypeScript 中用于**定义对象结构、契约和类型约束**的核心特性。它可以描述对象的形状（Shape）、函数的类型、类的契约等，是 TypeScript 实现 “面向接口编程” 的关键工具。

### 1. 接口简介

#### 1.1 什么是接口

接口是一种**抽象的类型定义**，它不包含具体的实现代码，只描述对象应该具有的属性和方法的类型。TypeScript 的核心原则之一是 “类型检查关注值的形状”，接口正是用于明确这种 “形状” 的工具。

#### 1.2 接口的作用

- **类型约束**：明确对象、函数、类的结构，减少类型错误。
- **代码契约**：定义类或函数的 “约定”，确保实现符合预期。
- **类型复用**：通过接口继承或组合，复用类型定义，提高代码可维护性。

### 2. 接口的基本定义与必选属性

使用 `interface` 关键字定义接口，接口中的属性默认是**必选**的（即对象必须包含所有必选属性）。

```typescript
// 定义一个 Person 接口，描述人的基本信息
interface Person {
  name: string; // 必选属性：字符串类型
  age: number; // 必选属性：数字类型
  greet(): void; // 必选方法：无返回值
}

// 使用接口定义对象：必须包含所有必选属性和方法
const alice: Person = {
  name: "Alice",
  age: 25,
  greet() {
    console.log(`Hello, my name is ${this.name}`);
  },
};

alice.greet(); // 输出: "Hello, my name is Alice"
```

#### “鸭子类型” 特性

TypeScript 的类型检查是**结构性的**（即 “鸭子类型”）：只要对象的结构符合接口的要求，即使没有显式声明实现该接口，也会被认为是该接口的实例。

```typescript
interface Point {
  x: number;
  y: number;
}

// 没有显式声明实现 Point 接口，但结构符合，因此合法
const point = { x: 10, y: 20 };
function printPoint(p: Point) {
  console.log(`x: ${p.x}, y: ${p.y}`);
}
printPoint(point); // 输出: "x: 10, y: 20"
```

### 3. 可选属性

使用 `?` 标记属性为**可选**，表示对象可以包含该属性，也可以不包含。可选属性常用于 “配置对象” 等场景。

```typescript
// 定义一个配置接口，其中 color 和 fontSize 是可选的
interface Config {
  width: number;
  height: number;
  color?: string; // 可选属性
  fontSize?: number; // 可选属性
}

// 使用接口：可以只传必选属性，也可以传部分或全部可选属性
const config1: Config = { width: 100, height: 200 };
const config2: Config = { width: 100, height: 200, color: "red" };
const config3: Config = {
  width: 100,
  height: 200,
  color: "blue",
  fontSize: 16,
};
```

### 4. 只读属性

使用 `readonly` 标记属性为**只读**，表示属性只能在对象创建时赋值，之后不能修改。

::: info 只读 vs const
`readonly` 用于属性，`const` 用于变量，两者作用类似但使用场景不同。
:::

```typescript
interface Point {
  readonly x: number; // 只读属性
  readonly y: number;
}

const point: Point = { x: 10, y: 20 };
// point.x = 30; // 报错：无法分配到 "x" ，因为它是只读属性

// 只读数组：ReadonlyArray《T》
let readonlyArr: ReadonlyArray<number> = [1, 2, 3];
// readonlyArr[0] = 10; // 报错：索引签名仅允许读取
// readonlyArr.push(4);  // 报错：类型 "readonly number[]" 上不存在属性 "push"
```

### 5. 函数类型的接口

接口不仅可以描述对象，还可以描述**函数的类型**（即函数的参数类型和返回值类型）。

```typescript
// 定义一个函数类型的接口：接收两个 string 参数，返回 boolean
interface StringComparator {
  (a: string, b: string): boolean;
}

// 使用该接口定义函数：参数名可以与接口不同，但类型必须匹配
const compareLength: StringComparator = (str1, str2) => {
  return str1.length > str2.length;
};

console.log(compareLength("apple", "banana")); // 输出: false
console.log(compareLength("hello", "hi")); // 输出: true
```

### 6. 可索引类型的接口

可索引类型的接口用于描述**可以通过索引访问的类型**（如数组、对象），支持两种索引签名：

- **数字索引签名**：通过数字访问（如数组 `arr[0]`）。
- **字符串索引签名**：通过字符串访问（如对象 `obj["key"]`）。

#### 6.1 数字索引签名

```typescript
// 定义一个数字索引的接口：类似数组，索引为 number，返回值为 string
interface StringArray {
  [index: number]: string;
}

const arr: StringArray = ["a", "b", "c"];
console.log(arr[0]); // 输出: "a"
```

#### 6.2 字符串索引签名

```typescript
// 定义一个字符串索引的接口：类似对象，键为 string，值为 number
interface NumberDictionary {
  [key: string]: number;
  length: number; // 合法：length 是 number 类型
  // name: string; // 报错：属性 "name" 的类型 "string" 不能赋值给索引类型 "number"
}

const dict: NumberDictionary = { length: 3, age: 25, score: 100 };
console.log(dict["age"]); // 输出: 25
```

#### 6.3 两种索引签名的共存

可以同时使用数字索引和字符串索引，但**数字索引的返回值类型必须是字符串索引返回值类型的子类型**（因为 JavaScript 中数字索引会被转换为字符串索引）。

```typescript
interface Animal {
  name: string;
}
interface Dog extends Animal {
  breed: string;
}

// 合法：Dog 是 Animal 的子类型
interface AnimalDictionary {
  [x: string]: Animal;
  [x: number]: Dog;
}
```

### 7. 类类型的接口

接口可以用于**约束类的结构**，类通过 `implements` 关键字实现接口，必须实现接口中定义的所有必选属性和方法。

```typescript
// 定义一个 Person 接口，约束类的结构
interface Person {
  name: string;
  age: number;
  greet(): void;
}

// 类 Student 实现 Person 接口
class Student implements Person {
  name: string;
  age: number;
  studentId: string; // 类可以有接口未定义的额外属性

  constructor(name: string, age: number, studentId: string) {
    this.name = name;
    this.age = age;
    this.studentId = studentId;
  }

  // 必须实现接口中的 greet 方法
  greet() {
    console.log(`Hello, I'm ${this.name}, a student with ID ${this.studentId}`);
  }
}

const student = new Student("Bob", 20, "S12345");
student.greet(); // 输出: "Hello, I'm Bob, a student with ID S12345"
```

::: note 接口与类的范围
接口只描述类的**公共部分**（public members），不会检查类的私有成员（private）或受保护成员（protected）。
:::

### 8. 接口的继承

接口可以通过 `extends` 关键字**继承其他接口**，从而复用已有接口的属性和方法。接口支持**单继承**和**多继承**（继承多个接口）。

#### 8.1 单继承

```typescript
interface Shape {
  color: string;
}

// 接口 Square 继承 Shape，新增 sideLength 属性
interface Square extends Shape {
  sideLength: number;
}

const square: Square = {
  color: "red",
  sideLength: 10,
};
```

#### 8.2 多继承

使用逗号分隔多个父接口，实现多继承。

```typescript
interface Shape {
  color: string;
}
interface PenStroke {
  penWidth: number;
}

// 接口 Square 同时继承 Shape 和 PenStroke
interface Square extends Shape, PenStroke {
  sideLength: number;
}

const square: Square = {
  color: "blue",
  penWidth: 2,
  sideLength: 15,
};
```

### 9. 混合类型的接口

在 JavaScript 中，对象可以同时是函数和对象（即有自己的属性和方法）。TypeScript 可以通过**混合类型的接口**来描述这种情况。

```typescript
// 定义一个混合类型的接口：既是函数，又有属性和方法
interface Counter {
  (start: number): string; // 函数签名
  interval: number; // 属性
  reset(): void; // 方法
}

// 实现混合类型
function getCounter(): Counter {
  // 先定义函数部分
  const counter = function (start: number) {
    return `Count started at ${start}`;
  } as Counter;

  // 添加属性和方法
  counter.interval = 1000;
  counter.reset = function () {
    console.log("Counter reset");
  };

  return counter;
}

const counter = getCounter();
console.log(counter(5)); // 输出: "Count started at 5"
console.log(counter.interval); // 输出: 1000
counter.reset(); // 输出: "Counter reset"
```

### 10. 接口继承类

接口不仅可以继承接口，还可以**继承类**。当接口继承类时，会继承类的所有成员（包括公共、私有、受保护成员），但只有该类或其子类才能实现这个接口（因为私有和受保护成员只能在类内部或子类中访问）。

```typescript
// 定义一个类
class Control {
  private state: any;
}

// 接口 SelectableControl 继承 Control，继承了私有成员 state
interface SelectableControl extends Control {
  select(): void;
}

// 只有 Control 的子类才能实现 SelectableControl（因为需要访问私有成员 state）
class Button extends Control implements SelectableControl {
  select() {
    // 可以访问继承的 state（虽然是私有，但在子类中通过继承链访问）
    console.log("Button selected");
  }
}

// 错误示例：非 Control 的子类无法实现 SelectableControl（因为没有 state 成员）
// class Image implements SelectableControl {
//   select() {}
// }
```

### 11. 类静态部分与实例部分的区别（进阶）

接口只能描述类的**实例部分**（instance side），不能直接描述类的**静态部分**（static side，如构造函数）。如果需要约束类的构造函数，可以通过 “类型别名 + 接口” 的方式实现。

```typescript
// 接口 ClockInterface 描述实例部分
interface ClockInterface {
  currentTime: Date;
  tick(): void;
}

// 类型别名 ClockConstructor 描述静态部分（构造函数）
type ClockConstructor = new (hour: number, minute: number) => ClockInterface;

// 实现类
class Clock implements ClockInterface {
  currentTime: Date;
  constructor(hour: number, minute: number) {
    this.currentTime = new Date();
  }
  tick() {
    console.log("tick tock");
  }
}

// 工厂函数：使用 ClockConstructor 约束构造函数
function createClock(
  ctor: ClockConstructor,
  hour: number,
  minute: number,
): ClockInterface {
  return new ctor(hour, minute);
}

const clock = createClock(Clock, 12, 30);
clock.tick(); // 输出: "tick tock"
```

## 类详解

类（Class）是面向对象编程（OOP）的核心，TypeScript 基于 ES6+ 的类语法，新增了**访问修饰符、抽象类、类型注解**等特性，让类的使用更安全、更符合工程化需求。

### 1. 类的基本定义

在 TypeScript 中，使用 `class` 关键字定义类，类包含**属性**（成员变量）、**构造函数**（constructor）和**方法**（成员函数）。

```typescript
class Person {
  // 属性：成员变量，需先声明类型
  name: string;
  age: number;
  // 构造函数：初始化对象，在 new 时自动调用
  constructor(name: string, age: number) {
    this.name = name; // this 指向当前实例
    this.age = age;
  },
  // 方法：成员函数
  greet(): void {
    console.log(`Hello, my name is ${this.name}, I'm ${this.age} years old.`);
  },
}

// 创建类的实例
const alice = new Person("Alice", 25);
alice.greet(); // 输出: "Hello, my name is Alice, I'm 25 years old."
```

### 2. 访问修饰符

TypeScript 提供了三种访问修饰符，用于控制类成员的**可访问性**：

- `public`（默认）：公共成员，可在类内部、子类、类外部访问。
- `private`：私有成员，只能在**类内部**访问，子类和类外部无法访问。
- `protected`：受保护成员，可在**类内部**和**子类**中访问，类外部无法访问。

```typescript
class Animal {
  public name: string; // 公共成员
  private age: number; // 私有成员
  protected type: string; // 受保护成员

  constructor(name: string, age: number, type: string) {
    this.name = name;
    this.age = age;
    this.type = type;
  }

  // 类内部可以访问所有成员
  getInfo(): string {
    return `Name: ${this.name}, Age: ${this.age}, Type: ${this.type}`;
  }
}

class Dog extends Animal {
  constructor(name: string, age: number) {
    super(name, age, "Dog"); // 调用父类构造函数
  }

  // 子类可以访问 public 和 protected 成员，不能访问 private 成员
  bark(): void {
    console.log(`${this.name} (${this.type}) is barking!`);
    // console.log(this.age); // 报错：属性 "age" 是私有的，只能在类 "Animal" 中访问
  }
}

const dog = new Dog("Buddy", 3);
console.log(dog.name); // 合法：public 成员可在外部访问
// console.log(dog.age);     // 报错：private 成员不可在外部访问
// console.log(dog.type);    // 报错：protected 成员不可在外部访问
```

### 3. 参数属性

参数属性允许在构造函数参数中直接声明类的属性，简化代码（结合访问修饰符使用）。

```typescript
class Person {
  // 直接在构造函数中声明参数属性（public/protected/private + 参数）
  constructor(
    public name: string,
    protected age: number,
    private id: string,
  ) {}

  getInfo(): string {
    return `Name: ${this.name}, Age: ${this.age}, ID: ${this.id}`;
  }
}

const person = new Person("Charlie", 30, "ID123456");
console.log(person.name); // 合法：public
// console.log(person.age); // 报错：protected
// console.log(person.id);  // 报错：private
console.log(person.getInfo()); // 输出: Name: Charlie, Age: 30, ID: ID123456
```

### 4. 只读修饰符（readonly）

`readonly` 用于声明**只读属性**，只能在声明时或构造函数中赋值，之后无法修改。

```typescript
class Circle {
  readonly PI: number = 3.14159;
  readonly radius: number;

  constructor(radius: number) {
    this.radius = radius; // 构造函数中可赋值
  }

  getArea(): number {
    // this.radius = 10; // 报错：只读属性无法修改
    return this.PI * this.radius * this.radius;
  }
}

const circle = new Circle(5);
console.log(circle.getArea()); // 输出: 78.53975
// circle.PI = 3.14; // 报错：只读属性无法修改
```

### 5. 静态成员（static）

静态成员（属性/方法）属于**类本身**，而非类的实例，通过 `类名.成员名` 访问。

```typescript
class MathUtil {
  static PI: number = 3.14159;

  static calculateArea(radius: number): number {
    return this.PI * radius * radius;
  }
}

// 无需创建实例，直接通过类名访问
console.log(MathUtil.PI); // 输出: 3.14159
console.log(MathUtil.calculateArea(5)); // 输出: 78.53975
```

### 6. 抽象类（abstract）

抽象类是**不能被实例化**的类，用于定义子类的通用接口，通过 `abstract` 关键字声明。抽象类中可以包含抽象方法（只有声明，没有实现），子类必须实现抽象方法。

```typescript
// 抽象类
abstract class Shape {
  abstract getArea(): number; // 抽象方法：只有声明，没有实现

  // 普通方法
  printArea(): void {
    console.log(`Area: ${this.getArea()}`);
  }
}

// 子类实现抽象类
class Rectangle extends Shape {
  constructor(
    private width: number,
    private height: number,
  ) {
    super(); // 抽象类的子类必须调用 super()
  }

  // 必须实现抽象方法 getArea
  getArea(): number {
    return this.width * this.height;
  }
}

const rect = new Rectangle(10, 20);
rect.printArea(); // 输出: Area: 200

// 错误：抽象类不能实例化
// const shape = new Shape();
```

### 7. 类的继承（extends）

通过 `extends` 关键字实现类的继承，子类可以复用父类的属性和方法，也可以重写父类方法。

```typescript {5,18-20} showLineNumbers
class Parent {
  protected name: string;

  constructor(name: string) {
    this.name = name;
  }

  greet(): void {
    console.log(`Hello, I'm ${this.name} (Parent)`);
  }
}

class Child extends Parent {
  private age: number;

  constructor(name: string, age: number) {
    super(name); // 调用父类构造函数
    this.age = age;
  }

  // 重写父类方法
  greet(): void {
    console.log(`Hello, I'm ${this.name}, ${this.age} years old (Child)`);
  }

  // 新增方法
  getAge(): number {
    return this.age;
  }
}

const child = new Child("Bob", 10);
child.greet(); // 输出: Hello, I'm Bob, 10 years old (Child)
console.log(child.getAge()); // 输出: 10
```

### 8. 泛型类

泛型类允许类在定义时不指定具体类型，而是在实例化时动态指定，提升类的复用性。

```typescript showLineNumbers
// 泛型类：T 是类型参数
class Box《T》 {
  private content: T;

  constructor(content: T) {
    this.content = content;
  }

  getContent(): T {
    return this.content;
  }

  setContent(content: T): void {
    this.content = content;
  }
}

// 实例化时指定类型为 string
const stringBox = new Box<string>("Hello");
console.log(stringBox.getContent()); // 输出: Hello

// 实例化时指定类型为 number
const numberBox = new Box<number>(123);
console.log(numberBox.getContent()); // 输出: 123
```

## 泛型（Generics）

泛型是 TypeScript 中用于创建可复用、类型安全的组件的核心特性，允许在定义函数、类、接口时不指定具体类型，而是在使用时动态指定。

### 1. 泛型的基本概念

泛型的核心是 “类型参数化”，即把类型作为参数传递给函数、类、接口，解决 “重复代码 + 不同类型” 的问题。

### 2. 泛型函数

泛型函数是最常用的泛型形式，通过 `《T》` 定义类型参数（T 是约定俗成的名称，也可以用 U、V 等）。

```typescript {1} showLineNumbers
// 泛型函数：T 是类型参数
function identity《T》(arg: T): T {
  return arg;
}

// 使用时指定类型
const str = identity<string>("hello"); // str: string
const num = identity<number>(123); // num: number

// 类型推断：省略类型参数，TS 自动推断
const bool = identity(true); // bool: boolean
```

#### 泛型函数的多类型参数

```typescript showLineNumbers
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const p1 = pair<string, number>("age", 25); // [string, number]
const p2 = pair<boolean, string>(true, "success"); // [boolean, string]
```

### 3. 泛型接口

泛型接口允许接口的属性/方法依赖于类型参数。

```typescript {2-6} showLineNumbers
// 泛型接口
interface Result《T》 {
  success: boolean;
  data: T;
  message?: string;
}

// 使用泛型接口
type UserResult = Result<{ name: string; age: number }>;
const userResult: UserResult = {
  success: true,
  data: { name: "Alice", age: 25 },
};

type ListResult = Result<string[]>;
const listResult: ListResult = {
  success: true,
  data: ["a", "b", "c"],
};
```

### 4. 泛型约束

泛型约束用于限制类型参数的范围，确保类型参数具有某些属性/方法。

```typescript {8} showLineNumbers
// 定义约束接口
interface Lengthwise {
  length: number;
}

// 泛型约束：T 必须满足 Lengthwise 接口
function logLength<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}

logLength("hello"); // 合法：string 有 length 属性
logLength([1, 2, 3]); // 合法：数组有 length 属性
// logLength(123); // 报错：number 没有 length 属性
```

### 5. 泛型默认类型

泛型默认类型允许为类型参数设置默认值，当使用时未指定类型，自动使用默认类型。

```typescript {1} showLineNumbers
// 泛型默认类型：T 默认是 string
function createArray<T = string>(length: number, value: T): T[] {
  return new Array(length).fill(value);
}

// 使用默认类型（string）
const arr1 = createArray(3, "a"); // string[]
// 指定类型为 number
const arr2 = createArray<number>(3, 1); // number[]
```

### 6. 常用泛型工具类型

TypeScript 内置了一系列泛型工具类型，用于快速转换类型，提升开发效率。

#### 6.1 Partial《T》

将 T 的所有属性变为可选。

```typescript showLineNumbers
interface User {
  name: string;
  age: number;
}

// Partial<User> = { name?: string; age?: number }
type PartialUser = Partial<User>;
const partialUser: PartialUser = { name: "Alice" }; // 合法
```

#### 6.2`Required《T》`

将 T 的所有属性变为必选。

```typescript showLineNumbers
interface Config {
  color?: string;
  size?: number;
}

// Required<Config> = { color: string; size: number }
type RequiredConfig = Required<Config>;
const config: RequiredConfig = { color: "red", size: 10 }; // 必须传所有属性
```

#### 6.3`Readonly《T》`

将 T 的所有属性变为只读。

```typescript showLineNumbers
interface Point {
  x: number;
  y: number;
}

// Readonly<Point> = { readonly x: number; readonly y: number }
type ReadonlyPoint = Readonly<Point>;
const point: ReadonlyPoint = { x: 10, y: 20 };
// point.x = 30; // 报错：只读属性
```

#### 6.4 Pick<T, K>

从 T 中选取部分属性 K 构建新类型。

```typescript showLineNumbers
interface User {
  name: string;
  age: number;
  email: string;
}

// Pick<User, "name" | "age"> = { name: string; age: number }
type UserBasic = Pick<User, "name" | "age">;
const basicUser: UserBasic = { name: "Bob", age: 20 };
```

#### 6.5 Omit<T, K>

从 T 中排除部分属性 K 构建新类型。

```typescript showLineNumbers
interface User {
  name: string;
  age: number;
  email: string;
}

// Omit<User, "email"> = { name: string; age: number }
type UserWithoutEmail = Omit<User, "email">;
const user: UserWithoutEmail = { name: "Charlie", age: 30 };
```

## 高级类型

TypeScript 提供了丰富的高级类型，用于组合、转换已有类型，提升类型系统的灵活性。

### 1. 联合类型（Union Types）

联合类型表示一个值可以是多种类型中的一种，使用 `|` 分隔类型。

```typescript {2} showLineNumbers
// 联合类型：string | number
type ID = string | number;

function printID(id: ID) {
  console.log(`ID: ${id}`);
}
printID("123"); // 合法
printID(456); // 合法
// printID(true); // 报错：类型 "boolean" 不能赋值给类型 "ID"
```

#### 类型守卫与联合类型

使用 `typeof`、`instanceof` 等进行类型守卫，缩小联合类型的范围。

```typescript {2} showLineNumbers
function handleValue(value: string | number) {
  if (typeof value === "string") {
    // 此处 value 类型为 string
    console.log(value.toUpperCase());
  } else {
    // 此处 value 类型为 number
    console.log(value.toFixed(2));
  }
}

handleValue("hello"); // HELLO
handleValue(3.1415); // 3.14
```

### 2. 交叉类型（Intersection Types）

交叉类型表示一个值同时具有多种类型的属性，使用 `&` 分隔类型。

```typescript {11} showLineNumbers
interface User {
  name: string;
  age: number;
}

interface Contact {
  phone: string;
  email: string;
}

// 交叉类型：User & Contact
type UserContact = User & Contact;
const userContact: UserContact = {
  name: "Alice",
  age: 25,
  phone: "123456789",
  email: "alice@example.com",
};
```

### 3. 类型别名（Type Aliases）

类型别名用于给已有类型起一个新名字，支持原始类型、联合类型、交叉类型、泛型等。

```typescript showLineNumbers
// 原始类型别名
type Str = string;
let s: Str = "hello";

// 联合类型别名
type Status = "success" | "error" | "loading";
let status: Status = "success";

// 泛型类型别名
type Container《T》 = { value: T };
let container: Container<number> = { value: 123 };
```

::: info 类型别名 vs 接口

- 接口：侧重描述“对象的形状”，支持继承、实现，适合定义公共契约。
- 类型别名：更灵活，支持联合类型、交叉类型、原始类型，无法被继承/实现。
  :::

### 4. 类型守卫（Type Guards）

类型守卫是用于在运行时检查类型的表达式，确保类型在某个作用域内的正确性。

#### 4.1 typeof 类型守卫

用于检查原始类型（string/number/boolean/symbol）。

```typescript {1} showLineNumbers
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function processValue(value: unknown) {
  if (isString(value)) {
    console.log(value.length); // 合法：value 是 string
  }
}
```

#### 4.2 instanceof 类型守卫

用于检查类的实例。

```typescript {12} showLineNumbers
class Cat {
  meow() {
    console.log("meow");
  }
}

class Dog {
  bark() {
    console.log("bark");
  }
}

function petAnimal(animal: Cat | Dog) {
  if (animal instanceof Cat) {
    animal.meow(); // 合法：animal 是 Cat
  } else {
    animal.bark(); // 合法：animal 是 Dog
  }
}
```

#### 4.3 自定义类型守卫

通过 `value is T` 语法定义自定义类型守卫。

```typescript {18} showLineNumbers
interface Square {
  kind: "square";
  sideLength: number;
}

interface Circle {
  kind: "circle";
  radius: number;
}

type Shape = Square | Circle;

function isSquare(shape: Shape): shape is Square {
  return shape.kind === "square";
}

function getArea(shape: Shape): number {
  if (isSquare(shape)) {
    return shape.sideLength ** 2; // 合法：shape 是 Square
  } else {
    return Math.PI * shape.radius ** 2; // 合法：shape 是 Circle
  }
}
```

### 5. 索引类型（Index Types）

索引类型用于动态获取对象的属性名和属性类型，核心语法：

- `keyof T`：获取 T 的所有属性名的联合类型。
- `T[K]`：获取 T 中属性 K 的类型。

```typescript {10,13} showLineNumbers
interface User {
  name: string;
  age: number;
}

// keyof User = "name" | "age"
type UserKeys = keyof User;

// T[K] 示例
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user: User = { name: "Bob", age: 20 };
const name = getProperty(user, "name"); // string
const age = getProperty(user, "age"); // number
// getProperty(user, "email"); // 报错："email" 不是 keyof User
```

### 6. 映射类型（Mapped Types）

映射类型用于基于已有类型创建新类型，遍历已有类型的属性并修改其特性。

```typescript {2-5} showLineNumbers
// 自定义映射类型：将所有属性变为只读
type Readonly《T》 = {
  readonly [K in keyof T]: T[K];
};

interface User {
  name: string;
  age: number;
}

type ReadonlyUser = Readonly<User>;
const user: ReadonlyUser = { name: "Alice", age: 25 };
// user.name = "Bob"; // 报错：只读属性
```

### 7. 条件类型（Conditional Types）

条件类型用于根据条件选择类型，语法：`T extends U ? X : Y`。

```typescript showLineNumbers
// 基础条件类型
type IsString《T》 = T extends string ? true : false;
type A = IsString<string>; // true
type B = IsString<number>; // false

// 分布式条件类型
type ExtractString《T》 = T extends string ? T : never;
type C = ExtractString<string | number | boolean>; // string

// 内置条件类型：Exclude、Extract、ReturnType 等
type Exclude<T, U> = T extends U ? never : T;
type Extract<T, U> = T extends U ? T : never;
type ReturnType<T extends (...args: any) => any> = T extends (
  ...args: any
) => infer R
  ? R
  : any;
```

## 模块与命名空间

TypeScript 支持 ES 模块（ESM）和 CommonJS 模块，同时提供命名空间（Namespace）用于组织代码。

### 1. 模块（Modules）

模块是 TypeScript 中组织代码的基本单元，文件级别的作用域，通过 `import/export` 实现模块间的交互。

#### 1.1 导出模块

```typescript showLineNumbers
// user.ts
export interface User {
  name: string;
  age: number;
}

export function createUser(name: string, age: number): User {
  return { name, age };
}

// 默认导出
export default class UserService {
  getUserName(user: User): string {
    return user.name;
  }
}
```

#### 1.2 导入模块

```typescript showLineNumbers
// app.ts
// 导入命名导出
import { User, createUser } from "./user";
// 导入默认导出
import UserService from "./user";

const user: User = createUser("Alice", 25);
const service = new UserService();
console.log(service.getUserName(user)); // Alice
```

### 2. 命名空间（Namespaces）

命名空间（原名 “内部模块”）用于在单个文件内组织代码，避免全局作用域污染。

```typescript {2} showLineNumbers
// shapes.ts
namespace Shapes {
  export interface Square {
    sideLength: number;
  }

  export interface Circle {
    radius: number;
  }

  export function getSquareArea(square: Square): number {
    return square.sideLength ** 2;
  }
}

// 使用命名空间
const square: Shapes.Square = { sideLength: 10 };
console.log(Shapes.getSquareArea(square)); // 100
```

#### 命名空间的拆分与合并

```typescript showLineNumbers
// shapes-1.ts
namespace Shapes {
  export interface Square {
    sideLength: number;
  }
}

// shapes-2.ts
namespace Shapes {
  export interface Circle {
    radius: number;
  }
}

// 使用合并后的命名空间
const square: Shapes.Square = { sideLength: 10 };
const circle: Shapes.Circle = { radius: 5 };
```

### 3. 模块与命名空间的区别

| 特性     | 模块（Modules）      | 命名空间（Namespaces）        |
| -------- | -------------------- | ----------------------------- |
| 作用域   | 文件级               | 全局作用域内的逻辑分组        |
| 依赖管理 | 通过 `import/export` | 通过引用标签（`<reference>`） |
| 使用场景 | 跨文件组织代码       | 单文件内组织代码              |
| 兼容性   | 兼容 ES 模块         | TypeScript 特有               |

## 装饰器（Decorators）

装饰器是 TypeScript 的实验性特性（ES 提案），用于修改类、方法、属性、参数的行为，常见于框架（如 Angular、NestJS）。

::: warning 启用装饰器
需在 `tsconfig.json` 中启用：

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

:::

### 1. 装饰器的基本概念

装饰器是一个函数，接收目标对象、属性名、描述符等参数，返回修改后的描述符或 void。

### 2. 类装饰器

类装饰器应用于类的构造函数，用于修改类的行为。

```typescript {3} showLineNumbers
// 类装饰器
function logClass(target: Function) {
  console.log(`Class ${target.name} is decorated`);
  // 修改类的原型
  target.prototype.log = function () {
    console.log(`Instance of ${target.name}`);
  };
}

@logClass
class User {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

const user = new User("Alice");
(user as any).log(); // Instance of User
```

### 3. 方法装饰器

方法装饰器应用于类的方法，用于修改方法的行为。

```typescript {6-14} showLineNumbers
// 方法装饰器：记录方法执行时间
function logMethod(
  target: any,
  propertyKey: string,
  descriptor: PropertyDescriptor,
) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Method ${propertyKey} start`);
    const result = originalMethod.apply(this, args);
    console.log(`Method ${propertyKey} end`);
    return result;
  };
  return descriptor;
}

class Calculator {
  @logMethod
  add(a: number, b: number): number {
    return a + b;
  }
}

const calc = new Calculator();
calc.add(1, 2);
// 输出:
// Method add start
// Method add end
```

### 4. 属性装饰器

属性装饰器应用于类的属性，用于修改属性的行为。

```typescript {3-20} showLineNumbers
// 属性装饰器：标记属性为必填
function required(target: any, propertyKey: string) {
  // 模拟验证逻辑
  const privateKey = `_${propertyKey}`;

  Object.defineProperty(target, propertyKey, {
    get() {
      return this[privateKey];
    },
    set(value: any) {
      if (!value) {
        throw new Error(`Property ${propertyKey} is required`);
      }
      this[privateKey] = value;
    },
    enumerable: true,
    configurable: true,
  });
}

class User {
  @required
  name!: string; // 使用 ! 断言避免初始化错误

  constructor(name: string) {
    this.name = name;
  }
}

const user = new User("Alice");
console.log(user.name); // Alice
// const invalidUser = new User(""); // 抛出错误
```

## TypeScript 与 HTML/原生 JS 结合

### 1. HTML 中直接引入 TypeScript（开发/调试场景）

TypeScript 无法直接在浏览器中运行，需先编译为 JS，或通过 `ts-node`/`esbuild` 等工具实时编译。以下是基础示例：

#### 1.1 基础示例（编译后引入）

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="UTF-8" />
    <title>TS + HTML</title>
  </head>
  <body>
    <div id="app"></div>

    <!-- 引入编译后的 JS 文件 -->
    <script src="./dist/main.js"></script>
  </body>
</html>
```

```typescript
// src/main.ts
const app = document.getElementById("app");
if (app) {
  app.innerText = "Hello TypeScript + HTML!";
}
```

编译命令（基于 `tsconfig.json`）：

```bash
tsc
```

#### 1.2 开发时实时编译

使用 `tsc --watch` 监听文件变化，自动编译：

```bash
tsc --watch
```

### 2. 原生 JS 项目迁移 TypeScript

#### 2.1 逐步迁移策略

1. 保留原有 JS 文件，新增 `tsconfig.json` 并配置：

```json
{
  "compilerOptions": {
    "allowJs": true, // 允许编译 JS 文件
    "checkJs": false, // 暂不检查 JS 文件类型（逐步开启）
    "outDir": "./dist",
    "target": "ES6",
    "strict": false // 先关闭严格模式，逐步开启
  },
  "include": ["src/**/*"]
}
```

2. 将 `.js` 文件重命名为 `.ts`，逐步添加类型注解。

#### 2.2 JS 文件中使用 TS 类型提示

通过 JSDoc 为 JS 文件添加类型注解，获得 TS 类型提示：

```javascript
// src/utils.js
/**
 * 计算两数之和
 * @param {number} a
 * @param {number} b
 * @returns {number}
 */
export function add(a, b) {
  return a + b;
}
```

## TypeScript 与 CSS 模块

### 1. CSS 模块类型声明（解决 TS 类型报错）

Vite/VitePress 中使用 CSS 模块时，TS 会提示 “找不到模块 xxx.css”，需添加类型声明文件：

#### 1.1 全局声明（推荐）

在项目根目录创建 `src/types/css-modules.d.ts`：

```typescript
// css-modules.d.ts
declare module "*.module.css" {
  const classes: { [key: string]: string };
  export default classes;
}

// 支持 SCSS/SASS
declare module "*.module.scss" {
  const classes: { [key: string]: string };
  export default classes;
}

// 支持 Less
declare module "*.module.less" {
  const classes: { [key: string]: string };
  export default classes;
}
```

#### 1.2 在 tsconfig.json 中引入声明文件

```json
{
  "compilerOptions": {
    "typeRoots": ["./src/types", "./node_modules/@types"]
  }
}
```

### 2. CSS 模块使用示例（VitePress 兼容）

```vue
<!-- src/components/Button.vue -->
<template>
  <button :class="styles.btn">点击按钮</button>
</template>

<script setup lang="ts">
// 引入 CSS 模块
import styles from "./Button.module.css";
</script>

<style module>
.btn {
  padding: 8px 16px;
  background: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>
```

## TypeScript 与 Vue3（兼容 VitePress）

VitePress 基于 Vue3 构建，以下是 Vue3 + TS 在 VitePress 中的核心实践。

### 1. VitePress 项目开启 TS 支持

#### 1.1 基础配置

VitePress 内置 TS 支持，只需将组件/脚本的 `<script>` 标签改为 `<script setup lang="ts">` 即可。

#### 1.2 项目级 TS 配置（可选）

在 VitePress 项目根目录创建 `tsconfig.json`：

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "Node",
    "strict": true,
    "jsx": "preserve",
    "sourceMap": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "lib": ["ESNext", "DOM"],
    "types": ["vitepress/client"]
  },
  "include": ["src/**/*", ".vitepress/**/*"]
}
```

### 2. Vue3 选项式 API 中的 TS 用法

```vue
<!-- .vitepress/theme/components/HelloOption.ts.vue -->
<script lang="ts">
import { defineComponent } from "vue";

// 定义 Props 接口
interface HelloProps {
  msg: string;
  count?: number;
}

export default defineComponent({
  // 声明 Props 类型
  props: {
    msg: {
      type: String,
      required: true,
    },
    count: {
      type: Number,
      default: 0,
    },
  },
  // 声明 data 类型
  data() {
    return {
      name: "Vue3 + TS",
    } as {
      name: string;
    };
  },
  // 方法类型注解
  methods: {
    greet(): string {
      return `${this.name}: ${this.msg} (${this.count})`;
    },
  },
  // 计算属性类型注解
  computed: {
    doubleCount(): number {
      return (this.count || 0) * 2;
    },
  },
});
</script>

<template>
  <div>{{ greet() }} - 双倍计数：{{ doubleCount }}</div>
</template>
```

### 3. Vue3 组合式 API 中的 TS 用法（推荐）

#### 3.1 基础示例（`<script setup lang="ts">`）

```vue
<!-- .vitepress/theme/components/HelloSetup.ts.vue -->
<script setup lang="ts">
// 1. Props 类型定义（withDefaults 支持默认值）
import { ref, computed, withDefaults } from "vue";

interface Props {
  msg: string;
  count?: number;
  list?: string[];
}

// 带默认值的 Props
const props = withDefaults(defineProps<Props>(), {
  count: 0,
  list: () => ["Vue", "TypeScript", "VitePress"],
});

// 2. 响应式数据类型注解
const name = ref<string>("VitePress");
const age = ref<number>(18);

// 3. 计算属性类型推断/注解
const doubleAge = computed(() => age.value * 2); // 自动推断为 number
const fullMsg = computed<string>(() => `${name.value}: ${props.msg}`);

// 4. 方法类型注解
function handleClick(): void {
  age.value++;
  console.log(fullMsg.value);
}

// 5. Emits 类型定义
const emit = defineEmits<{
  (e: "change", value: number): void;
  (e: "update", msg: string): void;
}>();

// 触发事件
const triggerChange = () => {
  emit("change", age.value);
};
</script>

<template>
  <div>
    <h3>{{ fullMsg }}</h3>
    <p>年龄：{{ age }} → 双倍：{{ doubleAge }}</p>
    <button @click="handleClick">增加年龄</button>
    <button @click="triggerChange">触发 change 事件</button>
    <ul>
      <li v-for="item in list" :key="item">{{ item }}</li>
    </ul>
  </div>
</template>
```

#### 3.2 组合式函数的 TS 封装

```typescript
// .vitepress/theme/composables/useCounter.ts
import { ref, Ref } from "vue";

// 定义入参和返回值类型
interface UseCounterOptions {
  initialValue?: number;
  step?: number;
}

type CounterReturn = {
  count: Ref<number>;
  increment: () => void;
  decrement: () => void;
  reset: () => void;
};

export function useCounter(options: UseCounterOptions = {}): CounterReturn {
  const { initialValue = 0, step = 1 } = options;
  const count = ref<number>(initialValue);

  const increment = () => {
    count.value += step;
  };

  const decrement = () => {
    count.value -= step;
  };

  const reset = () => {
    count.value = initialValue;
  };

  return { count, increment, decrement, reset };
}
```

在组件中使用：

```vue
<script setup lang="ts">
import { useCounter } from "../composables/useCounter";

const { count, increment, decrement, reset } = useCounter({
  initialValue: 10,
  step: 2,
});
</script>

<template>
  <div>
    <p>计数：{{ count }}</p>
    <button @click="increment">+2</button>
    <button @click="decrement">-2</button>
    <button @click="reset">重置</button>
  </div>
</template>
```

### 4. VitePress 中 TS 组件的使用

#### 4.1 全局注册 TS 组件

在 `.vitepress/theme/index.ts` 中注册全局组件：

```typescript
// .vitepress/theme/index.ts
import DefaultTheme from "vitepress/theme";
import { defineConfig } from "vitepress";
// 导入 TS 组件
import HelloSetup from "./components/HelloSetup.ts.vue";

export default {
  ...DefaultTheme,
  enhanceApp({ app }) {
    // 全局注册
    app.component("HelloSetup", HelloSetup);
  },
};
```

#### 4.2 在 Markdown 中使用 TS 组件

```markdown
<!-- docs/ts-vue.md -->

# TypeScript + Vue3 示例

<HelloSetup msg="Hello VitePress!" :count="5" />
```

### 5. VitePress 配置文件的 TS 改造

将 `.vitepress/config.js` 改为 `.vitepress/config.ts`：

```typescript
// .vitepress/config.ts
import { defineConfig } from "vitepress";

// 配置类型注解
export default defineConfig({
  title: "TypeScript 完全指南",
  description: "TypeScript 从入门到进阶，适配 VitePress",
  themeConfig: {
    nav: [
      { text: "首页", link: "/" },
      { text: "TS 基础", link: "/basic/" },
      { text: "Vue + TS", link: "/vue-ts/" },
    ],
    sidebar: {
      "/basic/": [
        {
          text: "基础类型",
          link: "/basic/types",
        },
        {
          text: "函数",
          link: "/basic/functions",
        },
      ],
    },
  },
});
```
