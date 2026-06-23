# 入门与基础环境搭建

## 1. TypeScript 简介

### 1.1 什么是 TypeScript

TypeScript 是由微软开发的一种**开源的静态类型编程语言**，它是 JavaScript 的超集，即在 JavaScript 的基础上添加了**静态类型系统**和一些面向对象的特性。TypeScript 最终会被编译成纯 JavaScript 代码，可以在任何支持 JavaScript 的环境中运行（如浏览器、Node.js 等）。

### 1.2 为什么使用 TypeScript

- **静态类型检查**：在编译阶段就能发现类型错误，减少运行时 bug。
- **更好的代码提示与补全**：IDE（如 VS Code）可以基于类型信息提供更精准的代码提示。
- **增强代码可维护性**：类型声明让代码意图更清晰，便于团队协作和后期维护。
- **兼容 JavaScript 生态**：可以直接使用现有的 JavaScript 库，也支持逐步迁移。

### 1.3 TypeScript 与 JavaScript 的关系

- TypeScript 是 JavaScript 的超集，所有合法的 JavaScript 代码在 TypeScript 中都能运行。
- TypeScript 新增了静态类型、接口、类、泛型等特性，这些特性在编译后会被转换为等效的 JavaScript 代码。

## 2. 环境搭建

### 2.1 安装 Node.js

TypeScript 的编译依赖 Node.js 环境，首先需要安装 Node.js：

- 访问 [Node.js 官网](https://nodejs.org/)，下载并安装 LTS 版本（长期支持版）。

- 安装完成后，在终端（命令行）中输入以下命令验证安装：

  ```bash
node -v
  npm -v
```
  
若能输出版本号，说明安装成功。

### 2.2 安装 TypeScript 编译器

使用 npm（Node.js 包管理器）全局安装 TypeScript 编译器 `tsc`：

```bash
npm install -g typescript
```

安装完成后，验证安装：

```bash
tsc -v
```

若输出版本号，说明 TypeScript 编译器安装成功。

### 2.3 第一个 TypeScript 程序

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

   ```bash
// hello.js
   var message = "Hello, TypeScript!";
console.log(message);
   ```

3. 运行编译后的 JavaScript 文件：

   ```bash
node hello.js
   ```

   终端会输出：`Hello, TypeScript!`


### 2.4 配置 tsconfig.json

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
       "target": "ES6",                          // 编译目标 JavaScript 版本（如 ES5、ES6）
       "module": "commonjs",                     // 模块系统（如 commonjs、ESNext）
       "outDir": "./dist",                       // 编译输出目录
       "rootDir": "./src",                       // 源代码目录
       "strict": true,                           // 启用所有严格类型检查选项
       "esModuleInterop": true,                  // 兼容 ES 模块和 CommonJS 模块
       "skipLibCheck": true,                     // 跳过声明文件的类型检查
       "forceConsistentCasingInFileNames": true // 强制文件名大小写一致
     },
     "include": ["src/**/*"],                    // 需要编译的文件
     "exclude": ["node_modules"]                 // 排除编译的文件
   }
   ```
   
   
   
3. 配置完成后，直接执行 `tsc` 命令即可按照配置编译整个项目。

# 基础类型

TypeScript 提供了丰富的**静态类型系统**，可以帮助我们在开发阶段就明确变量、函数参数和返回值的类型，从而减少运行时错误。本部分将详细讲解 TypeScript 的核心基础类型。

## 1. 原始类型（Primitive Types）

原始类型是 TypeScript 中最基础的数据类型，包括：`boolean`、`number`、`string`、`null`、`undefined`、`symbol` 和 `bigint`。

### 1.1 布尔值（boolean）

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

### 1.2 数字（number）

TypeScript 中的数字类型支持十进制、二进制、八进制和十六进制，同时也支持浮点数。

```typescript
let decimal: number = 6;          // 十进制
let binary: number = 0b1010;      // 二进制（ES6+）
let octal: number = 0o744;        // 八进制（ES6+）
let hex: number = 0xf00d;         // 十六进制
let float: number = 3.14;          // 浮点数
let notANumber: number = NaN;      // 特殊值：非数字
let infinity: number = Infinity;   // 特殊值：无穷大
```

### 1.3 字符串（string）

字符串类型用于表示文本数据，可以使用单引号 `'`、双引号 `"` 或模板字符串 ```（支持多行和插值）。

```typescript
let firstName: string = "Alice";
let lastName: string = 'Smith';

// 模板字符串：支持多行和变量插值
let fullName: string = `${firstName} ${lastName}`;
let greeting: string = `Hello, ${fullName}! 
Welcome to TypeScript.`;
console.log(greeting);
// 输出:
// Hello, Alice Smith! 
// Welcome to TypeScript.
```

### 1.4 null 和 undefined

`null` 和 `undefined` 是 TypeScript 中的两个特殊类型，分别表示 “空值” 和 “未定义”。

- 默认情况下，`null` 和 `undefined` 是**所有类型的子类型**（可以赋值给任意类型，如 `number`、`string` 等）。
- 若在 `tsconfig.json` 中开启了 `strict: true`（严格模式），则 `null` 和 `undefined` 只能赋值给 `void` 和它们自身的类型（需通过联合类型处理）。

```typescript
let u: undefined = undefined;
let n: null = null;

// 严格模式下，需使用联合类型允许 null/undefined
let maybeNumber: number | null = 10;
maybeNumber = null; // 合法
```

### 1.5 symbol 和 bigint（ES6+ 新增）

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

## 2. 复杂类型（Complex Types）

复杂类型是基于原始类型构建的更高级的数据结构，包括：数组、元组、枚举、任意值、void、never、object 等。

### 2.1 数组（Array）

数组用于存储**同一类型**的多个元素，有两种定义方式：

1. `类型[]`：在类型后加方括号（推荐，更简洁）。
2. `Array<类型>`：使用泛型语法（后续会详细讲解泛型）。

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

### 2.2 元组（Tuple）

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

### 2.3 枚举（Enum）

枚举用于定义一组**命名的常量集合**，使代码更具可读性和可维护性。枚举分为数字枚举和字符串枚举。

#### 数字枚举（默认）

数字枚举的成员默认从 `0` 开始递增，也可以手动指定初始值。

```typescript
// 默认数字枚举（从 0 开始）
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right  // 3
}
let dir: Direction = Direction.Up;
console.log(dir); // 输出: 0

// 手动指定初始值
enum Status {
  Success = 200,
  NotFound = 404,
  ServerError = 500
}
console.log(Status.NotFound); // 输出: 404
```

#### 字符串枚举

字符串枚举的每个成员都必须是字符串字面量，更直观且便于调试。

```typescript
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE"
}
let favoriteColor: Color = Color.Green;
console.log(favoriteColor); // 输出: "GREEN"
```

### 2.4 任意值（any）

`any` 类型表示 “任意类型”，可以绕过 TypeScript 的类型检查，适用于不确定类型的场景（如来自第三方库的数据）。

- 注意：过度使用 `any` 会失去 TypeScript 的类型安全优势，建议尽量避免。

```typescript
let notSure: any = 4;
notSure = "hello"; // 合法（可以赋值为任意类型）
notSure = true;    // 合法

// 访问 any 类型的属性和方法（不会报错，但运行时可能出错）
notSure.toUpperCase(); // 编译通过，但若 notSure 不是字符串，运行时会报错

// 示例：处理第三方库数据
let dataFromAPI: any = fetchDataFromAPI(); // 假设 API 返回类型不确定
```

### 2.5 void

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

### 2.6 never

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

### 2.7 object

`object` 类型表示**非原始类型**（即除了 `number`、`string`、`boolean`、`symbol`、`null`、`undefined` 之外的类型），如对象、数组、函数等。

- 注意：`object` 类型比较宽泛，通常建议使用 ** 接口（interface）**或**类型别名（type alias）** 来更精确地定义对象类型（后续会讲解）。

```typescript
let obj: object = { name: "Alice" };
let arr: object = [1, 2, 3];
let func: object = function() {};

// 错误示例：原始类型不能赋值给 object
// let num: object = 10; // 报错
```

## 3. 类型断言（Type Assertion）

类型断言用于**手动指定一个值的类型**，告诉 TypeScript 编译器 “我比你更清楚这个值的类型”。类型断言有两种语法：

1. `<类型>值`（尖括号语法）。
2. `值 as 类型`（as 语法，推荐，因为在 JSX 中只能用 as 语法）。

```typescript
// 示例：将 any 类型断言为 string
let someValue: any = "hello world";

// 语法一：尖括号
let strLength1: number = (<string>someValue).length;

// 语法二：as（推荐）
let strLength2: number = (someValue as string).length;

// 注意：类型断言只是编译时的语法，不会改变运行时的类型
```

------

这是教程的第二部分，详细讲解了 TypeScript 的基础类型和类型断言。接下来的部分会深入讲解函数、接口、类等核心特性。

# 函数详解

函数是 TypeScript 中构建应用程序的核心模块之一，TypeScript 为函数提供了**完整的类型系统支持**，包括参数类型、返回值类型、函数重载等特性，让函数的使用更安全、更可维护。

## 1. 函数的基本定义与类型注解

在 TypeScript 中，函数可以通过**函数声明**或**函数表达式**定义，并且可以为参数和返回值添加类型注解。

### 1.1 函数声明

函数声明是最常见的函数定义方式，直接在参数后加 `: 类型` 注解参数类型，在函数名后加 `: 类型` 注解返回值类型。

```typescript
// 函数声明：参数 a 和 b 为 number 类型，返回值为 number 类型
function add(a: number, b: number): number {
  return a + b;
}

let result: number = add(1, 2); // 合法
// add(1, "2"); // 报错：类型 "string" 不能赋值给类型 "number"
```

### 1.2 函数表达式

函数表达式可以将函数赋值给变量，此时变量的类型可以通过**类型推断**自动确定，也可以显式定义函数类型。

```typescript
// 方式一：类型推断（推荐，更简洁）
const multiply = function(a: number, b: number): number {
  return a * b;
};

// 方式二：显式定义函数类型（变量类型为 (a: number, b: number) => number）
const divide: (a: number, b: number) => number = function(a, b) {
  return a / b;
};
```

### 1.3 箭头函数

箭头函数是 ES6+ 引入的简洁函数写法，TypeScript 同样支持箭头函数的类型注解。

```typescript
// 箭头函数的类型注解
const subtract = (a: number, b: number): number => a - b;

// 箭头函数作为参数时的类型
const numbers: number[] = [1, 2, 3];
const doubled: number[] = numbers.map((num: number) => num * 2);
```

## 2. 函数参数的高级特性

TypeScript 支持多种灵活的参数处理方式，包括可选参数、默认参数、剩余参数等。

### 2.1 可选参数

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

console.log(greet("Alice"));        // 输出: "Hello, Alice!"
console.log(greet("Alice", "Smith"));// 输出: "Hello, Alice Smith!"
```

### 2.2 默认参数

为参数设置默认值，当调用函数时未传该参数或传 `undefined` 时，会使用默认值。默认参数可以放在任意位置，但如果默认参数在必选参数之前，必须显式传 `undefined` 才能使用默认值。

```typescript
// 参数 greeting 有默认值 "Hello"
function sayHello(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`;
}

console.log(sayHello("Bob"));              // 输出: "Hello, Bob!"
console.log(sayHello("Bob", "Hi"));        // 输出: "Hi, Bob!"
console.log(sayHello("Bob", undefined));   // 输出: "Hello, Bob!"
```

### 2.3 剩余参数

使用 `...` 语法将**任意数量的参数**收集到一个数组中，适用于参数数量不确定的场景。

```typescript
// 剩余参数 numbers 是一个 number 类型的数组
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2));          // 输出: 3
console.log(sum(1, 2, 3, 4));    // 输出: 10
```

### 2.4 参数解构

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

## 3. 函数返回值类型

### 3.1 基本返回值类型

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

### 3.2 void 类型

当函数**没有返回值**时，返回值类型为 `void`（可以省略，TypeScript 会自动推断）。

```typescript
function log(message: string): void {
  console.log(message);
  // 不需要 return，或 return undefined
}
```

### 3.3 never 类型

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

## 4. 函数重载（Function Overloads）

函数重载允许为**同一个函数**定义**多个不同的参数类型和返回值类型**，TypeScript 会根据传入的参数类型自动匹配对应的重载签名。

### 4.1 重载的定义方式

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
console.log(splitString("hello"));        // 输出: ["h", "e", "l", "l", "o"]
console.log(splitString("a,b,c", ","));   // 输出: ["a", "b", "c"]
```

### 4.2 重载的注意事项

- 实现签名必须兼容所有重载签名（参数类型和返回值类型要覆盖所有重载的可能）。
- 调用函数时，TypeScript 会根据参数类型**从上到下**匹配重载签名，因此更具体的重载签名应放在前面。

## 5. 函数类型与类型别名 / 接口

可以使用 ** 类型别名（type alias）**或**接口（interface）** 来定义函数类型，使代码更清晰、更易复用。

### 5.1 使用类型别名定义函数类型

```typescript
// 定义一个函数类型：接收两个 number 参数，返回 number
type MathOperation = (a: number, b: number) => number;

// 使用该类型定义函数
const add: MathOperation = (a, b) => a + b;
const multiply: MathOperation = (a, b) => a * b;
```

### 5.2 使用接口定义函数类型

```typescript
// 定义一个函数类型的接口
interface StringFormatter {
  (str: string, toUpperCase: boolean): string;
}

// 使用该接口定义函数
const formatString: StringFormatter = (str, toUpperCase) => {
  return toUpperCase ? str.toUpperCase() : str.toLowerCase();
};

console.log(formatString("Hello", true));  // 输出: "HELLO"
console.log(formatString("Hello", false)); // 输出: "hello"
```

## 6. this 类型

在 JavaScript 中，`this` 的指向容易出错，TypeScript 允许通过**this 参数**明确指定函数中 `this` 的类型（this 参数是虚拟参数，放在参数列表的第一位，调用时不需要传）。

### 6.1 this 参数的使用

```typescript
interface User {
  name: string;
  greet(): void;
}

const user: User = {
  name: "Alice",
  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
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
  }
};

const betterGreetFn = betterUser.greet;
// betterGreetFn(); // 报错：需要 "this" 类型的参数
betterGreetFn.call(betterUser); // 合法，通过 call 绑定 this
```

------

这是教程的第三部分，详细讲解了 TypeScript 函数的核心特性，包括参数处理、返回值类型、函数重载、this 类型等。接下来的部分会深入讲解接口（Interface），这是 TypeScript 中用于定义对象结构的强大工具。

# 接口详解

接口（Interface）是 TypeScript 中用于**定义对象结构、契约和类型约束**的核心特性。它可以描述对象的形状（Shape）、函数的类型、类的契约等，是 TypeScript 实现 “面向接口编程” 的关键工具。

## 1. 接口简介

### 1.1 什么是接口

接口是一种**抽象的类型定义**，它不包含具体的实现代码，只描述对象应该具有的属性和方法的类型。TypeScript 的核心原则之一是 “类型检查关注值的形状”，接口正是用于明确这种 “形状” 的工具。

### 1.2 接口的作用

- **类型约束**：明确对象、函数、类的结构，减少类型错误。
- **代码契约**：定义类或函数的 “约定”，确保实现符合预期。
- **类型复用**：通过接口继承或组合，复用类型定义，提高代码可维护性。

## 2. 接口的基本定义与必选属性

使用 `interface` 关键字定义接口，接口中的属性默认是**必选**的（即对象必须包含所有必选属性）。

### 2.1 简单对象接口示例

```typescript
// 定义一个 Person 接口，描述人的基本信息
interface Person {
  name: string;    // 必选属性：字符串类型
  age: number;     // 必选属性：数字类型
  greet(): void;   // 必选方法：无返回值
}

// 使用接口定义对象：必须包含所有必选属性和方法
const alice: Person = {
  name: "Alice",
  age: 25,
  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
};

alice.greet(); // 输出: "Hello, my name is Alice"
```

### 2.2 “鸭子类型” 特性

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

## 3. 可选属性

使用 `?` 标记属性为**可选**，表示对象可以包含该属性，也可以不包含。可选属性常用于 “配置对象” 等场景。

```typescript
// 定义一个配置接口，其中 color 和 fontSize 是可选的
interface Config {
  width: number;
  height: number;
  color?: string;       // 可选属性
  fontSize?: number;    // 可选属性
}

// 使用接口：可以只传必选属性，也可以传部分或全部可选属性
const config1: Config = { width: 100, height: 200 };
const config2: Config = { width: 100, height: 200, color: "red" };
const config3: Config = { width: 100, height: 200, color: "blue", fontSize: 16 };
```

## 4. 只读属性

使用 `readonly` 标记属性为**只读**，表示属性只能在对象创建时赋值，之后不能修改。

- 注意：`readonly` 用于属性，`const` 用于变量，两者作用类似但使用场景不同。

```typescript
interface Point {
  readonly x: number;  // 只读属性
  readonly y: number;
}

const point: Point = { x: 10, y: 20 };
// point.x = 30; // 报错：无法分配到 "x" ，因为它是只读属性

// 只读数组：ReadonlyArray<T>
let readonlyArr: ReadonlyArray<number> = [1, 2, 3];
// readonlyArr[0] = 10; // 报错：索引签名仅允许读取
// readonlyArr.push(4);  // 报错：类型 "readonly number[]" 上不存在属性 "push"
```

## 5. 函数类型的接口

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
console.log(compareLength("hello", "hi"));     // 输出: true
```

## 6. 可索引类型的接口

可索引类型的接口用于描述**可以通过索引访问的类型**（如数组、对象），支持两种索引签名：

- **数字索引签名**：通过数字访问（如数组 `arr[0]`）。
- **字符串索引签名**：通过字符串访问（如对象 `obj["key"]`）。

### 6.1 数字索引签名

```typescript
// 定义一个数字索引的接口：类似数组，索引为 number，返回值为 string
interface StringArray {
  [index: number]: string;
}

const arr: StringArray = ["a", "b", "c"];
console.log(arr[0]); // 输出: "a"
```

### 6.2 字符串索引签名

```typescript
// 定义一个字符串索引的接口：类似对象，键为 string，值为 number
interface NumberDictionary {
  [key: string]: number;
  length: number;  // 合法：length 是 number 类型
  // name: string; // 报错：属性 "name" 的类型 "string" 不能赋值给索引类型 "number"
}

const dict: NumberDictionary = { length: 3, age: 25, score: 100 };
console.log(dict["age"]); // 输出: 25
```

### 6.3 两种索引签名的共存

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

## 7. 类类型的接口

接口可以用于**约束类的结构**，类通过 `implements` 关键字实现接口，必须实现接口中定义的所有必选属性和方法。

### 7.1 类实现接口的基本示例

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

### 7.2 接口描述类的公共部分

接口只描述类的**公共部分**（public members），不会检查类的私有成员（private）或受保护成员（protected）。

## 8. 接口的继承

接口可以通过 `extends` 关键字**继承其他接口**，从而复用已有接口的属性和方法。接口支持**单继承**和**多继承**（继承多个接口）。

### 8.1 单继承

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
  sideLength: 10
};
```

### 8.2 多继承

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
  sideLength: 15
};
```

## 9. 混合类型的接口

在 JavaScript 中，对象可以同时是函数和对象（即有自己的属性和方法）。TypeScript 可以通过**混合类型的接口**来描述这种情况。

```typescript
// 定义一个混合类型的接口：既是函数，又有属性和方法
interface Counter {
  (start: number): string;  // 函数签名
  interval: number;          // 属性
  reset(): void;             // 方法
}

// 实现混合类型
function getCounter(): Counter {
  // 先定义函数部分
  const counter = function(start: number) {
    return `Count started at ${start}`;
  } as Counter;

  // 添加属性和方法
  counter.interval = 1000;
  counter.reset = function() {
    console.log("Counter reset");
  };

  return counter;
}

const counter = getCounter();
console.log(counter(5));    // 输出: "Count started at 5"
console.log(counter.interval); // 输出: 1000
counter.reset();             // 输出: "Counter reset"
```

## 10. 接口继承类

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

## 11. 类静态部分与实例部分的区别（进阶）

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
function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
  return new ctor(hour, minute);
}

const clock = createClock(Clock, 12, 30);
clock.tick(); // 输出: "tick tock"
```

------

这是教程的第四部分，详细讲解了 TypeScript 接口的核心特性，包括基本定义、可选 / 只读属性、函数类型、可索引类型、类类型、继承、混合类型等。接下来的部分会深入讲解 TypeScript 的类（Class），这是面向对象编程的核心。

# 类详解

类（Class）是面向对象编程（OOP）的核心，TypeScript 基于 ES6+ 的类语法，新增了**访问修饰符、抽象类、类型注解**等特性，让类的使用更安全、更符合工程化需求。本部分将详细讲解 TypeScript 类的核心特性。

## 1. 类的基本定义

在 TypeScript 中，使用 `class` 关键字定义类，类包含**属性**（成员变量）、**构造函数**（constructor）和**方法**（成员函数）。

### 1.1 简单类示例

```typescript
class Person {
  // 属性：成员变量，需先声明类型（也可在构造函数中通过参数属性声明，见第4节）
  name: string;
  age: number;

  // 构造函数：初始化对象，在 new 时自动调用
  constructor(name: string, age: number) {
    this.name = name; // this 指向当前实例
    this.age = age;
  }

  // 方法：成员函数
  greet(): void {
    console.log(`Hello, my name is ${this.name}, I'm ${this.age} years old.`);
  }
}

// 创建类的实例
const alice = new Person("Alice", 25);
alice.greet(); // 输出: "Hello, my name is Alice, I'm 25 years old."
```

## 2. 访问修饰符

TypeScript 提供了三种访问修饰符，用于控制类成员的**可访问性**：

- `public`（默认）：公共成员，可在类内部、子类、类外部访问。
- `private`：私有成员，只能在**类内部**访问，子类和类外部无法访问。
- `protected`：受保护成员，可在**类内部**和**子类**中访问，类外部无法访问。

### 2.1 访问修饰符示例

```typescript
class Animal {
  public name: string;       // 公共成员
  private age: number;       // 私有成员
  protected type: string;    // 受保护成员

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
console.log(dog.name);       // 合法：public 成员可在外部访问
// console.log(dog.age);     // 报错：private 成员不可在外部访问
// console.log(dog.type);    // 报错：protected 成员不可在外部访问
dog.bark();                   // 输出: "Buddy (Dog) is barking!"
console.log(dog.getInfo());  // 输出: "Name: Buddy, Age: 3, Type: Dog"
```

## 3. readonly 修饰符

使用 `readonly` 标记属性为**只读**，表示属性只能在**声明时**或**构造函数中**赋值，之后不能修改。

### 3.1 readonly 示例

```typescript
class Point {
  readonly x: number;
  readonly y: number;

  constructor(x: number, y: number) {
    this.x = x; // 构造函数中可以赋值
    this.y = y;
  }

  // 方法中不能修改 readonly 属性
  move(): void {
    // this.x = 10; // 报错：无法分配到 "x" ，因为它是只读属性
  }
}

const point = new Point(10, 20);
console.log(point.x, point.y); // 输出: 10 20
// point.x = 30; // 报错：只读属性不可修改
```

## 4. 参数属性

参数属性是 TypeScript 的语法糖，允许在**构造函数参数前加访问修饰符**，直接将参数声明为类的属性，简化代码。

### 4.1 参数属性示例

```typescript
// 不使用参数属性：需先声明属性，再在构造函数中赋值
class Person1 {
  name: string;
  private age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

// 使用参数属性：直接在构造函数参数前加修饰符，自动声明为类属性
class Person2 {
  constructor(
    public name: string,    // 自动声明为 public 属性
    private age: number     // 自动声明为 private 属性
  ) {}
}

const person = new Person2("Alice", 25);
console.log(person.name); // 合法
```

## 5. 类的继承

类通过 `extends` 关键字实现**继承**，子类可以继承父类的属性和方法，并可以**重写**（Override）父类的方法。继承时需在子类构造函数中调用 `super()` 来执行父类的构造函数。

### 5.1 继承与方法重写示例



```typescript
class Animal {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  // 父类方法
  makeSound(): void {
    console.log("Some generic sound");
  }
}

class Cat extends Animal {
  color: string;

  constructor(name: string, color: string) {
    super(name); // 必须调用父类构造函数，且需在使用 this 之前调用
    this.color = color;
  }

  // 重写父类的 makeSound 方法
  makeSound(): void {
    console.log(`${this.name} (${this.color} cat) says: Meow!`);
  }

  // 子类新增方法
  purr(): void {
    console.log(`${this.name} is purring...`);
  }
}

const cat = new Cat("Mimi", "white");
cat.makeSound(); // 输出: "Mimi (white cat) says: Meow!"（重写后的方法）
cat.purr();      // 输出: "Mimi is purring..."（子类新增方法）
```

## 6. 存取器（Getter/Setter）

存取器用于**控制属性的访问和修改**，通过 `get` 和 `set` 关键字定义，可以在访问或修改属性时添加逻辑（如验证、计算）。

### 6.1 存取器示例

```typescript
class User {
  private _age: number; // 私有属性，通常以下划线开头

  constructor(age: number) {
    this._age = age;
  }

  // getter：访问属性时调用
  get age(): number {
    return this._age;
  }

  // setter：修改属性时调用，可添加验证逻辑
  set age(newAge: number) {
    if (newAge > 0 && newAge < 150) {
      this._age = newAge;
    } else {
      console.log("Invalid age!");
    }
  }
}

const user = new User(25);
console.log(user.age); // 输出: 25（调用 getter）
user.age = 30;         // 调用 setter，合法
console.log(user.age); // 输出: 30
user.age = -5;         // 调用 setter，验证失败，输出: "Invalid age!"
console.log(user.age); // 输出: 30（未修改）
```

## 7. 静态属性与静态方法

使用 `static` 关键字定义**静态成员**（属性或方法），静态成员属于**类本身**，而不是类的实例，需通过**类名**访问。

### 7.1 静态成员示例

```typescript
class MathUtil {
  // 静态属性：属于类
  static PI: number = 3.14159;

  // 静态方法：属于类，不能访问实例成员（this 指向类本身）
  static calculateCircleArea(radius: number): number {
    return this.PI * radius * radius; // 访问静态属性
  }
}

// 访问静态成员：通过类名
console.log(MathUtil.PI);                          // 输出: 3.14159
console.log(MathUtil.calculateCircleArea(5));     // 输出: 78.53975

// 错误：不能通过实例访问静态成员
// const math = new MathUtil();
// console.log(math.PI); // 报错
```

### 7.2 静态成员的应用：实例计数

```typescript
class Counter {
  static count: number = 0; // 静态属性：记录实例数量

  constructor() {
    Counter.count++; // 每次创建实例，count 加 1
  }

  static getCount(): number {
    return Counter.count;
  }
}

new Counter();
new Counter();
new Counter();
console.log(Counter.getCount()); // 输出: 3
```

## 8. 抽象类

抽象类使用 `abstract` 关键字定义，**不能被实例化**，只能被继承。抽象类可以包含**抽象方法**（没有实现的方法），子类必须实现所有抽象方法。抽象类用于定义**基类的契约**，强制子类遵循特定的结构。

### 8.1 抽象类示例

```typescript
// 抽象类：Shape
abstract class Shape {
  color: string;

  constructor(color: string) {
    this.color = color;
  }

  // 抽象方法：没有实现，子类必须实现
  abstract getArea(): number;

  // 普通方法：有实现，子类可直接继承
  getColor(): string {
    return this.color;
  }
}

// 子类：Circle，必须实现抽象方法 getArea
class Circle extends Shape {
  radius: number;

  constructor(color: string, radius: number) {
    super(color);
    this.radius = radius;
  }

  // 实现抽象方法
  getArea(): number {
    return Math.PI * this.radius * this.radius;
  }
}

// 子类：Square，必须实现抽象方法 getArea
class Square extends Shape {
  sideLength: number;

  constructor(color: string, sideLength: number) {
    super(color);
    this.sideLength = sideLength;
  }

  // 实现抽象方法
  getArea(): number {
    return this.sideLength * this.sideLength;
  }
}

// 错误：不能实例化抽象类
// const shape = new Shape("red"); // 报错

// 实例化子类
const circle = new Circle("blue", 5);
console.log(circle.getColor()); // 输出: "blue"（继承自抽象类的普通方法）
console.log(circle.getArea());  // 输出: 78.5398...（实现的抽象方法）

const square = new Square("green", 10);
console.log(square.getArea());  // 输出: 100
```

## 9. 类与接口的关系

类可以通过 `implements` 关键字**实现接口**，接口用于约束类的结构（类必须实现接口中定义的所有必选属性和方法）。类可以实现多个接口，接口也可以继承类（见第四部分接口详解）。

### 9.1 类实现接口示例

```typescript
// 接口 1：描述可打印的能力
interface Printable {
  print(): void;
}

// 接口 2：描述可序列化的能力
interface Serializable {
  serialize(): string;
}

// 类 Document 实现两个接口
class Document implements Printable, Serializable {
  content: string;

  constructor(content: string) {
    this.content = content;
  }

  // 实现 Printable 接口的方法
  print(): void {
    console.log(`Printing: ${this.content}`);
  }

  // 实现 Serializable 接口的方法
  serialize(): string {
    return JSON.stringify({ content: this.content });
  }
}

const doc = new Document("Hello, TypeScript!");
doc.print();                // 输出: "Printing: Hello, TypeScript!"
console.log(doc.serialize()); // 输出: '{"content":"Hello, TypeScript!"}'
```

## 10. 类的类型

类本身可以作为**类型**使用，用于约束变量、函数参数或返回值的类型。

### 10.1 类作为类型示例

```typescript
class Person {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
}

// 变量类型为 Person
let person: Person;
person = new Person("Alice"); // 合法
// person = { name: "Bob" }; // 报错：结构符合但不是 Person 实例（严格模式下）

// 函数参数类型为 Person
function greet(p: Person): void {
  console.log(`Hello, ${p.name}`);
}
greet(new Person("Charlie")); // 合法
```

------

这是教程的第五部分，详细讲解了 TypeScript 类的核心特性，包括基本定义、访问修饰符、继承、存取器、静态成员、抽象类等。接下来的部分会深入讲解 TypeScript 的**泛型**，这是实现类型复用和灵活性的关键工具。

# 泛型详解

泛型（Generics）是 TypeScript 中实现**类型复用、类型安全和灵活性**的核心特性。它允许我们在定义函数、接口、类时不预先指定具体类型，而是在使用时动态指定，从而让代码更通用、更易维护。

## 1. 泛型简介

### 1.1 什么是泛型

泛型的核心思想是 “**类型参数化**”，即将类型作为参数传递给函数、接口或类。通过泛型，我们可以创建 “适用于多种类型的组件”，同时保持类型安全（避免使用 `any` 导致的类型丢失）。

### 1.2 为什么使用泛型

- **类型复用**：一个组件可以支持多种类型，无需为每种类型重复编写代码。
- **类型安全**：在编译阶段就能发现类型错误，避免运行时 bug。
- **灵活性**：类型在使用时动态指定，不限制组件的通用性。

### 1.3 不使用泛型的问题

先看一个不使用泛型的例子，感受其局限性：

```typescript
// 函数只能处理 number 类型，复用性差
function identityNumber(arg: number): number {
  return arg;
}

// 用 any 可以处理任意类型，但失去了类型安全
function identityAny(arg: any): any {
  return arg;
}
const result = identityAny("hello");
// result.toUpperCase(); // 编译通过，但如果 arg 不是字符串，运行时会报错
```

## 2. 泛型函数

泛型函数通过在函数名后添加 `<类型参数>` 来定义，类型参数通常用单个大写字母表示（如 `T`、`U`、`V` 等）。

### 2.1 基本泛型函数示例

```typescript
// 定义泛型函数：T 是类型参数，使用时动态指定
function identity<T>(arg: T): T {
  return arg;
}

// 使用方式一：显式指定类型
const num = identity<number>(123); // T 为 number
const str = identity<string>("hello"); // T 为 string

// 使用方式二：类型推断（推荐，更简洁）
const bool = identity(true); // TypeScript 自动推断 T 为 boolean
```

### 2.2 泛型函数处理数组

当需要处理数组类型的参数时，可以将类型参数与数组结合使用：

```typescript
// 方式一：T[]
function getArrayLength<T>(arr: T[]): number {
  return arr.length;
}
getArrayLength([1, 2, 3]);       // T 为 number
getArrayLength(["a", "b"]);      // T 为 string

// 方式二：Array<T>
function getFirstElement<T>(arr: Array<T>): T {
  return arr[0];
}
getFirstElement([true, false]);   // T 为 boolean
```

## 3. 泛型接口

泛型接口可以将类型参数应用到接口的属性或方法上，使接口更通用。

### 3.1 基本泛型接口示例

```typescript
// 定义泛型接口：T 是类型参数
interface Box<T> {
  value: T;
  getValue(): T;
}

// 实现泛型接口：显式指定 T 为 number
const numberBox: Box<number> = {
  value: 100,
  getValue() {
    return this.value;
  }
};

// 实现泛型接口：显式指定 T 为 string
const stringBox: Box<string> = {
  value: "TypeScript",
  getValue() {
    return this.value;
  }
};
```

### 3.2 泛型函数类型的接口

泛型也可以用于描述函数类型的接口：

```typescript
// 泛型函数类型的接口
interface Comparator<T> {
  (a: T, b: T): number;
}

// 实现：比较数字大小
const numberComparator: Comparator<number> = (a, b) => a - b;
console.log(numberComparator(5, 3)); // 输出: 2

// 实现：比较字符串长度
const stringComparator: Comparator<string> = (a, b) => a.length - b.length;
console.log(stringComparator("apple", "banana")); // 输出: -1
```

## 4. 泛型类

泛型类通过在类名后添加 `<类型参数>` 来定义，类型参数可以应用到类的属性、方法和构造函数中。

### 4.1 基本泛型类示例

```typescript
// 定义泛型类：T 是类型参数
class Stack<T> {
  private items: T[] = [];

  // 入栈
  push(item: T): void {
    this.items.push(item);
  }

  // 出栈
  pop(): T | undefined {
    return this.items.pop();
  }

  // 获取栈顶元素
  peek(): T | undefined {
    return this.items[this.items.length - 1];
  }
}

// 使用泛型类：指定 T 为 number
const numberStack = new Stack<number>();
numberStack.push(1);
numberStack.push(2);
console.log(numberStack.pop()); // 输出: 2

// 使用泛型类：指定 T 为 string
const stringStack = new Stack<string>();
stringStack.push("a");
stringStack.push("b");
console.log(stringStack.peek()); // 输出: "b"
```

## 5. 泛型约束

泛型约束用于**限制类型参数的范围**，通过 `extends` 关键字实现，确保类型参数具有特定的属性或方法。

### 5.1 基本泛型约束示例

```typescript
// 定义一个接口，约束类型参数必须有 length 属性
interface Lengthwise {
  length: number;
}

// 泛型函数：T 必须继承 Lengthwise（即必须有 length 属性）
function logLength<T extends Lengthwise>(arg: T): void {
  console.log(arg.length);
}

logLength("hello");        // 合法：字符串有 length 属性
logLength([1, 2, 3]);      // 合法：数组有 length 属性
logLength({ length: 10 }); // 合法：对象有 length 属性
// logLength(123);          // 报错：数字没有 length 属性
```

### 5.2 泛型约束中使用类型参数

可以在泛型约束中使用另一个类型参数，例如约束一个类型参数必须是另一个类型参数的键：

```typescript
// 泛型函数：K 必须是 T 的键（keyof T）
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "Alice", age: 25 };
getProperty(person, "name"); // 合法："name" 是 person 的键
getProperty(person, "age");  // 合法："age" 是 person 的键
// getProperty(person, "gender"); // 报错："gender" 不是 person 的键
```

## 6. 泛型参数的默认类型

可以为泛型参数设置**默认类型**，当使用时未显式指定类型时，会使用默认类型。

### 6.1 默认类型示例

```typescript
// 泛型函数：T 的默认类型为 string
function createArray<T = string>(length: number, value: T): T[] {
  return Array.from({ length }, () => value);
}

// 使用默认类型：T 为 string
const strArray = createArray(3, "a"); // 输出: ["a", "a", "a"]

// 显式指定类型：T 为 number
const numArray = createArray<number>(3, 10); // 输出: [10, 10, 10]
```

## 7. 泛型工具类型

TypeScript 内置了一些常用的**泛型工具类型**，用于快速转换或操作类型，以下是最常用的几种：

### 7.1 Partial<T>：将所有属性变为可选



```typescript
interface User {
  name: string;
  age: number;
}

// Partial<User> 等价于 { name?: string; age?: number }
const partialUser: Partial<User> = { name: "Alice" }; // 合法，age 可选
```

### 7.2 Required<T>：将所有属性变为必选

```typescript
interface User {
  name?: string;
  age?: number;
}

// Required<User> 等价于 { name: string; age: number }
const requiredUser: Required<User> = { name: "Bob", age: 30 }; // 必须包含所有属性
```

### 7.3 Pick<T, K>：从 T 中选取指定的属性 K

```typescript
interface User {
  name: string;
  age: number;
  gender: string;
}

// Pick<User, "name" | "age"> 等价于 { name: string; age: number }
const pickedUser: Pick<User, "name" | "age"> = { name: "Charlie", age: 25 };
```

### 7.4 Omit<T, K>：从 T 中排除指定的属性 K

```typescript
interface User {
  name: string;
  age: number;
  gender: string;
}

// Omit<User, "gender"> 等价于 { name: string; age: number }
const omittedUser: Omit<User, "gender"> = { name: "David", age: 35 };
```

### 7.5 Exclude<T, U>：从 T 中排除属于 U 的类型

```typescript
// Exclude<"a" | "b" | "c", "a" | "d"> 等价于 "b" | "c"
type Result1 = Exclude<"a" | "b" | "c", "a" | "d">;
```

### 7.6 Extract<T, U>：从 T 中提取属于 U 的类型

```typescript
// Extract<"a" | "b" | "c", "a" | "d"> 等价于 "a"
type Result2 = Extract<"a" | "b" | "c", "a" | "d">;
```

### 7.7 ReturnType<T>：获取函数 T 的返回值类型

```typescript
function greet(): string {
  return "Hello";
}

// ReturnType<typeof greet> 等价于 string
type GreetReturnType = ReturnType<typeof greet>;
```

## 8. 泛型条件类型

泛型条件类型通过 `T extends U ? X : Y` 的语法，根据类型关系动态选择类型，类似于 JavaScript 中的三元运算符。

### 8.1 基本条件类型示例

```typescript
// 如果 T 是 string 类型，则返回 string，否则返回 number
type TypeName<T> = T extends string ? string : number;

type T1 = TypeName<string>; // string
type T2 = TypeName<number>; // number
```

### 8.2 条件类型与泛型结合

```typescript
// 提取数组元素的类型：如果 T 是数组，则返回元素类型，否则返回 T
type ArrayElement<T> = T extends (infer U)[] ? U : T;

type E1 = ArrayElement<string[]>; // string
type E2 = ArrayElement<number>;   // number
```

------

这是教程的第六部分，详细讲解了 TypeScript 泛型的核心特性，包括泛型函数、泛型接口、泛型类、泛型约束、泛型工具类型、条件类型等。接下来的部分会深入讲解 TypeScript 的**类型别名、联合类型、交叉类型**等高级类型特性。

# 高级类型详解

TypeScript 的高级类型系统是其强大之处，通过**类型别名、联合类型、交叉类型、字面量类型、类型守卫**等特性，我们可以更精确地描述复杂的数据结构和类型关系，进一步提升代码的类型安全性和可维护性。

## 1. 类型别名（Type Alias）

类型别名使用 `type` 关键字定义，用于给一个类型起一个新名字，常用于简化复杂类型的书写或复用类型定义。

### 1.1 类型别名的基本使用

```typescript
// 给原始类型起别名
type ID = string | number;
let userId: ID = "123";
let orderId: ID = 456;

// 给对象类型起别名
type User = {
  name: string;
  age: number;
};
const user: User = { name: "Alice", age: 25 };

// 给函数类型起别名
type GreetFunction = (name: string) => string;
const greet: GreetFunction = (name) => `Hello, ${name}`;
```

### 1.2 类型别名 vs 接口

类型别名和接口都可以定义对象类型，但有以下区别：

- **接口**：可以被继承（`extends`）、被类实现（`implements`），适合定义 “契约”。
- **类型别名**：可以定义原始类型、联合类型、交叉类型等，更灵活，适合定义复杂类型组合。

```typescript
// 接口可以继承
interface Person {
  name: string;
}
interface Student extends Person {
  studentId: string;
}

// 类型别名可以定义联合类型
type Status = "success" | "error" | "loading";
```

## 2. 联合类型（Union Types）

联合类型使用 `|` 符号定义，表示一个值可以是**多种类型中的一种**。

### 2.1 基本联合类型示例

```typescript
// 联合类型：值可以是 string 或 number
let value: string | number;
value = "hello"; // 合法
value = 123;     // 合法
// value = true;  // 报错：布尔值不属于联合类型

// 联合类型结合数组
let arr: (string | number)[] = ["a", 1, "b", 2];
```

### 2.2 联合类型的访问限制

当使用联合类型时，只能访问**所有类型共有的属性或方法**，否则需要使用**类型守卫**（见第 5 节）缩小类型范围。

```typescript
interface Cat {
  meow(): void;
  scratch(): void;
}
interface Dog {
  bark(): void;
  fetch(): void;
}

function playWithPet(pet: Cat | Dog) {
  // pet.meow(); // 报错：不确定 pet 是 Cat 还是 Dog
  // 只能访问共有的属性（如果有的话）
}
```

## 3. 交叉类型（Intersection Types）

交叉类型使用 `&` 符号定义，表示一个值**同时具有多种类型的所有属性**。

### 3.1 基本交叉类型示例

```typescript
// 交叉类型：同时具有 Person 和 Employee 的属性
type Person = {
  name: string;
  age: number;
};
type Employee = {
  employeeId: string;
  department: string;
};

type Worker = Person & Employee;
const worker: Worker = {
  name: "Bob",
  age: 30,
  employeeId: "E123",
  department: "Engineering"
};
```

### 3.2 交叉类型的冲突处理

如果交叉的类型中有**同名属性但类型不同**，会导致该属性类型为 `never`（无法使用）。

```typescript
type A = { x: string };
type B = { x: number };
type C = A & B;
// const c: C = { x: "hello" }; // 报错：x 的类型是 never
```

## 4. 字面量类型（Literal Types）

字面量类型允许我们将值**精确限制为特定的字面量**（如特定的字符串、数字或布尔值），而不是宽泛的原始类型。

### 4.1 字符串字面量类型

```typescript
// 限制 status 只能是 "success"、"error" 或 "loading"
type Status = "success" | "error" | "loading";
let currentStatus: Status;
currentStatus = "success"; // 合法
currentStatus = "error";   // 合法
// currentStatus = "pending"; // 报错：不在允许的字面量范围内
```

### 4.2 数字字面量类型

```typescript
// 限制 count 只能是 1、2 或 3
type Count = 1 | 2 | 3;
let itemCount: Count;
itemCount = 2; // 合法
// itemCount = 4; // 报错
```

### 4.3 布尔字面量类型

```typescript
// 限制 isEnabled 只能是 true
type IsEnabled = true;
let enabled: IsEnabled = true;
// enabled = false; // 报错
```

### 4.4 字面量类型的应用：函数参数约束

```typescript
function setMode(mode: "dark" | "light") {
  console.log(`Mode set to ${mode}`);
}
setMode("dark");  // 合法
setMode("light"); // 合法
// setMode("auto"); // 报错
```

## 5. 类型守卫（Type Guards）

类型守卫是一种**在运行时缩小类型范围**的机制，帮助 TypeScript 在编译阶段更准确地推断类型，解决联合类型的访问限制问题。

### 5.1 typeof 类型守卫

适用于**原始类型**（string、number、boolean、symbol）的判断。

```typescript
function printValue(value: string | number) {
  if (typeof value === "string") {
    // 这里 value 被推断为 string
    console.log(value.toUpperCase());
  } else {
    // 这里 value 被推断为 number
    console.log(value.toFixed(2));
  }
}
```

### 5.2 instanceof 类型守卫

适用于**类实例**的判断。

```typescript
class Cat {
  meow() { console.log("Meow!"); }
}
class Dog {
  bark() { console.log("Woof!"); }
}

function makeSound(pet: Cat | Dog) {
  if (pet instanceof Cat) {
    pet.meow(); // 推断为 Cat
  } else {
    pet.bark(); // 推断为 Dog
  }
}
```

### 5.3 in 类型守卫

适用于**判断对象是否具有特定属性**。

```typescript
interface Cat {
  meow(): void;
}
interface Dog {
  bark(): void;
}

function interact(pet: Cat | Dog) {
  if ("meow" in pet) {
    pet.meow(); // 推断为 Cat
  } else {
    pet.bark(); // 推断为 Dog
  }
}
```

### 5.4 自定义类型守卫

通过定义一个返回 `类型谓词`（`parameterName is Type`）的函数，实现更灵活的类型判断。

```typescript
interface Fish {
  swim(): void;
}
interface Bird {
  fly(): void;
}

// 自定义类型守卫：判断是否是 Fish
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

function move(pet: Fish | Bird) {
  if (isFish(pet)) {
    pet.swim(); // 推断为 Fish
  } else {
    pet.fly();  // 推断为 Bird
  }
}
```

## 6. 可辨识联合（Discriminated Unions）

可辨识联合是联合类型的进阶用法，通过**共同的 “可辨识属性”**（如 `kind`、`type`）来区分不同的类型，结合类型守卫可以实现类型安全的分支处理。

### 6.1 可辨识联合示例

```typescript
// 定义三个接口，都有共同的 kind 属性（可辨识属性）
interface Circle {
  kind: "circle"; // 字符串字面量类型
  radius: number;
}
interface Square {
  kind: "square";
  sideLength: number;
}
interface Triangle {
  kind: "triangle";
  base: number;
  height: number;
}

// 联合类型
type Shape = Circle | Square | Triangle;

// 使用可辨识属性和类型守卫处理
function calculateArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2; // 推断为 Circle
    case "square":
      return shape.sideLength ** 2; // 推断为 Square
    case "triangle":
      return (shape.base * shape.height) / 2; // 推断为 Triangle
    default:
      //  exhaustive check：确保所有类型都被处理
      const exhaustiveCheck: never = shape;
      return exhaustiveCheck;
  }
}
```

## 7. 类型断言与非空断言

### 7.1 类型断言（Type Assertion）

类型断言用于**手动指定值的类型**，告诉编译器 “我比你更清楚这个值的类型”，有两种语法：

- `值 as 类型`（推荐，JSX 中只能用此语法）。
- `<类型>值`（尖括号语法）。

```typescript
let someValue: any = "hello world";
// 语法一：as
let strLength1: number = (someValue as string).length;
// 语法二：尖括号
let strLength2: number = (<string>someValue).length;
```

### 7.2 非空断言操作符（!）

非空断言操作符 `!` 用于告诉编译器 “这个值**一定不是 null 或 undefined**”，跳过编译阶段的空值检查。

```typescript
function getString(): string | undefined {
  return "hello";
}

const str: string = getString()!; // 断言返回值一定不是 undefined
console.log(str.toUpperCase()); // 合法
```

## 8. 类型兼容性（Type Compatibility）

TypeScript 的类型兼容性是**结构性的**（即 “鸭子类型”），只要两个类型的结构一致，就认为它们是兼容的，而不需要显式声明继承或实现关系。

### 8.1 对象类型的兼容性

```typescript
interface Person {
  name: string;
  age: number;
}
interface User {
  name: string;
  age: number;
  email: string;
}

// User 包含 Person 的所有属性，因此 User 兼容 Person
let person: Person;
const user: User = { name: "Alice", age: 25, email: "alice@example.com" };
person = user; // 合法（User 可以赋值给 Person）
// user = person; // 报错：Person 缺少 email 属性
```

### 8.2 函数类型的兼容性

函数类型的兼容性主要看**参数类型**和**返回值类型**：

- 参数：目标函数的参数类型可以是源函数参数类型的父类型（参数少的可以赋值给参数多的）。
- 返回值：源函数的返回值类型必须是目标函数返回值类型的子类型。

```typescript
let fn1 = (a: number) => {};
let fn2 = (a: number, b: number) => {};
fn2 = fn1; // 合法：fn1 参数少，可以赋值给 fn2
// fn1 = fn2; // 报错：fn2 参数多，不能赋值给 fn1
```

------

这是教程的第七部分，详细讲解了 TypeScript 的高级类型特性，包括类型别名、联合类型、交叉类型、字面量类型、类型守卫、可辨识联合等。接下来的部分会深入讲解 TypeScript 的**模块系统、命名空间、装饰器**等工程化特性。

# 模块与工程化

模块（Module）是 TypeScript 组织代码的核心方式，通过模块系统可以实现代码的**复用、隔离和按需加载**。本部分将详细讲解 TypeScript 的模块系统、命名空间、模块解析策略，以及实验性特性装饰器，帮助你构建更工程化的 TypeScript 项目。

## 1. 模块系统基础

TypeScript 采用 **ES Modules（ES6 模块系统）** 作为标准模块系统，通过 `export` 关键字导出模块内容，通过 `import` 关键字导入其他模块的内容。每个 `.ts` 文件默认都是一个独立的模块，文件内的变量、函数、类等默认是私有的，只有通过 `export` 导出后才能被其他文件访问。

### 1.1 命名导出（Named Exports）

命名导出允许一个模块导出多个内容，导入时需要使用相同的名称。

```typescript
// math.ts：命名导出
export const PI = 3.14159;

export function add(a: number, b: number): number {
  return a + b;
}

export class Calculator {
  multiply(a: number, b: number): number {
    return a * b;
  }
}
```

```
// app.ts：导入命名导出的内容
import { PI, add, Calculator } from "./math";

console.log(PI);          // 输出: 3.14159
console.log(add(1, 2));   // 输出: 3
const calc = new Calculator();
console.log(calc.multiply(3, 4)); // 输出: 12
```

### 1.2 重命名导入 / 导出

可以使用 `as` 关键字对导入或导出的内容进行重命名，避免名称冲突。

```typescript
// 重命名导出
export { PI as MathPI, add as sum };

// 重命名导入
import { PI as MathPI, add as sum } from "./math";
console.log(MathPI);
console.log(sum(1, 2));
```

### 1.3 整体导入（Namespace Import）

可以使用 `* as 命名空间` 将整个模块的内容导入到一个对象中。

```typescript
import * as MathModule from "./math";

console.log(MathModule.PI);
console.log(MathModule.add(1, 2));
const calc = new MathModule.Calculator();
```

### 1.4 默认导出（Default Export）

默认导出允许一个模块只导出一个 “主要内容”，导入时可以自定义名称（不需要大括号）。

```typescript
// user.ts：默认导出一个类
export default class User {
  constructor(public name: string) {}
}
```

```typescript
// app.ts：导入默认导出，可自定义名称
import MyUser from "./user";
const user = new MyUser("Alice");
```

### 1.5 混合导出

一个模块可以同时使用命名导出和默认导出。

```typescript
// utils.ts
export default function log(message: string): void {
  console.log(message);
}

export function formatDate(date: Date): string {
  return date.toISOString();
}
```

```
// 导入混合导出
import log, { formatDate } from "./utils";
log("Hello");
console.log(formatDate(new Date()));
```

### 1.6 重新导出（Re-export）

可以将一个模块的内容重新导出，方便构建 “聚合模块”。

```typescript
// index.ts：重新导出其他模块的内容
export { add, Calculator } from "./math";
export { default as User } from "./user";
```

```typescript
// 直接从 index.ts 导入
import { add, User } from "./index";
```
## 2. 命名空间（Namespace）
命名空间是 TypeScript 早期用于组织代码的方式，主要用于**避免全局变量污染**，将相关的变量、函数、类组织在一个命名空间内。虽然现在推荐使用模块系统，但在一些场景（如维护旧代码）中仍会用到。
### 2.1 命名空间的基本使用

使用 `namespace` 关键字定义命名空间，通过 `export` 暴露命名空间内的内容。

```typescript
// shapes.ts
namespace Shapes {
  // 命名空间内的私有内容（未 export）
  const PI = 3.14;

  // 暴露的内容
  export class Circle {
    constructor(public radius: number) {}
    getArea(): number {
      return PI * this.radius * this.radius;
    }
  }

  export class Square {
    constructor(public sideLength: number) {}
    getArea(): number {
      return this.sideLength * this.sideLength;
    }
  }
}

// 使用命名空间
const circle = new Shapes.Circle(5);
console.log(circle.getArea()); // 输出: 78.5
```
### 2.2 命名空间的拆分

可以将一个命名空间拆分到多个文件中，通过 `/// <reference path="..." />` 引用。


```typescript
// circle.ts
namespace Shapes {
  export class Circle { /* ... */ }
}
```

```typescript
// square.ts
/// <reference path="circle.ts" /> // 引用 circle.ts
namespace Shapes {
  export class Square { /* ... */ }
}
```

```typescript
// app.ts
/// <reference path="square.ts" />
const circle = new Shapes.Circle(5);
```
### 2.3 命名空间 vs 模块

|   特性   |             命名空间             |              模块              |
| :------: | :------------------------------: | :----------------------------: |
| 作用范围 | 文件内或跨文件（通过 reference） | 文件级别（每个文件是独立模块） |
| 依赖管理 |     手动通过 reference 引用      |  自动通过 import/export 管理   |
| 推荐场景 |       旧代码维护、小型项目       |    现代工程化项目、大型应用    |
## 3. 模块解析策略
模块解析是指 TypeScript 编译器在遇到 `import` 语句时，如何找到对应模块文件的过程。TypeScript 支持两种模块解析策略：**Node**（默认，推荐）和 **Classic**（兼容旧版本）。
### 3.1 Node 模块解析策略
Node 策略模拟 Node.js 的模块解析逻辑，优先查找 `node_modules` 目录。
- 相对路径导入（如 `./math`）：从当前文件所在目录开始查找。
- 非相对路径导入（如 `lodash`）：从当前目录的 `node_modules` 开始，逐级向上查找。
### 3.2 tsconfig.json 中的模块解析配置
可以通过 `tsconfig.json` 中的配置项自定义模块解析行为：
```json
{
  "compilerOptions": {
    "moduleResolution": "node",        // 模块解析策略：node 或 classic
    "baseUrl": "./src",                // 基础路径，用于简化非相对路径导入
    "paths": {                          // 路径映射，配合 baseUrl 使用
      "@/*": ["*"],                    // 将 @/ 映射到 src/
      "@utils/*": ["utils/*"]          // 将 @utils/ 映射到 src/utils/
    },
    "rootDirs": ["src", "generated"],  // 多个根目录，用于合并不同目录的模块
    "typeRoots": ["node_modules/@types"], // 类型声明文件的查找目录
    "types": ["node", "lodash"]        // 自动引入的类型声明
  }
}
```

### 3.3 路径映射示例

通过 `baseUrl` 和 `paths` 可以简化导入路径，避免深层级的 `../../`。
```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": "./src",
    "paths": {
      "@/*": ["*"]
    }
  }
}
```
```typescript
// 之前：深层级相对路径
import { add } from "../../../utils/math";

// 之后：使用路径映射
import { add } from "@/utils/math";
```
## 4. 装饰器（Decorator）
装饰器是 TypeScript 的**实验性特性**，用于在不修改类、方法、属性或参数代码的情况下，动态扩展其功能。装饰器本质是一个函数，通过 `@装饰器名` 语法应用。
### 4.1 启用装饰器
需要在 `tsconfig.json` 中启用装饰器支持：
```json
{
  "compilerOptions": {
    "experimentalDecorators": true,    // 启用装饰器
    "emitDecoratorMetadata": true       // 启用装饰器元数据（可选，配合 reflect-metadata 使用）
  }
}
```

### 4.2 类装饰器

类装饰器应用于类的构造函数，用于修改或扩展类的行为。
```typescript
// 类装饰器：给类添加一个静态属性
function AddStaticProperty(target: Function) {
  target.version = "1.0";
}

@AddStaticProperty
class MyClass {}

console.log((MyClass as any).version); // 输出: "1.0"
```
### 4.3 方法装饰器
方法装饰器应用于类的方法，用于修改方法的行为。
```typescript
// 方法装饰器：记录方法的执行时间
function LogExecutionTime(target: any, methodName: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function(...args: any[]) {
    const start = Date.now();
    const result = originalMethod.apply(this, args);
    const end = Date.now();
    console.log(`${methodName} executed in ${end - start}ms`);
    return result;
  };
}

class Calculator {
  @LogExecutionTime
  add(a: number, b: number): number {
    return a + b;
  }
}

const calc = new Calculator();
calc.add(1, 2); // 输出: "add executed in Xms"
```
### 4.4 属性装饰器
属性装饰器应用于类的属性，用于修改属性的行为。
```typescript
// 属性装饰器：将属性转为只读
function ReadOnly(target: any, propertyName: string) {
  Object.defineProperty(target, propertyName, {
    writable: false
  });
}

class User {
  @ReadOnly
  name: string = "Alice";
}

const user = new User();
// user.name = "Bob"; // 报错：只读属性不可修改
```

### 4.5 参数装饰器

参数装饰器应用于方法的参数，用于标记或处理参数。
```typescript
// 参数装饰器：标记参数
function LogParameter(target: any, methodName: string, parameterIndex: number) {
  console.log(`Parameter ${parameterIndex} of ${methodName} is decorated`);
}

class UserService {
  getUser(@LogParameter userId: string) {
    console.log(`Getting user ${userId}`);
  }
}

const service = new UserService();
service.getUser("123"); // 输出: "Parameter 0 of getUser is decorated"
```
------
这是教程的第八部分，详细讲解了 TypeScript 的模块系统、命名空间、模块解析策略和装饰器。接下来的部分会深入讲解 TypeScript 与前端框架（如 React、Vue）的结合使用，以及常见问题与最佳实践。
# TypeScript 与 React/Vue 结合
TypeScript 与主流前端框架（React、Vue 3）的结合能显著提升项目的类型安全性和开发体验。本部分将详细讲解如何在 React 和 Vue 3 项目中使用 TypeScript，包括环境搭建、组件类型定义、Props/Emits 类型、Hook / 响应式 API 的类型注解等核心内容。
## 1. React 中使用 TypeScript
React 对 TypeScript 有原生支持，结合 TypeScript 可以为组件 Props、状态、事件等提供完整的类型约束。
### 1.1 环境搭建
#### 方式一：Create React App（CRA）
使用 CRA 快速创建 TypeScript 版本的 React 项目：
```bash
npx create-react-app my-react-ts-app --template typescript
```
生成的项目会自动配置好 `tsconfig.json` 和相关依赖。
#### 方式二：Vite（推荐，更快）
使用 Vite 创建 React + TypeScript 项目：
```bash
npm create vite@latest my-react-ts-app -- --template react-ts
```
Vite 提供了更轻量、更快的开发体验。
### 1.2 函数组件与 Props 类型
#### 基本组件定义
使用 `React.FC`（Function Component）或直接定义 Props 接口来约束组件：
```typescript
// 方式一：使用 React.FC（自动包含 children 类型）
import React from "react";

interface GreetingProps {
  name: string;
  age?: number; // 可选属性
}

const Greeting: React.FC<GreetingProps> = ({ name, age = 18 }) => {
  return (
    <div>
      <p>Hello, {name}!</p>
      {age && <p>Age: {age}</p>}
    </div>
  );
};

export default Greeting;
```

```typescript
// 方式二：直接定义 Props 类型（更灵活，推荐）
import React from "react";

interface ButtonProps {
  text: string;
  onClick: () => void;
  disabled?: boolean;
}

// 组件函数直接接收 Props，不使用 React.FC
function Button({ text, onClick, disabled = false }: ButtonProps) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {text}
    </button>
  );
}

export default Button;
```

#### Children 类型

如果需要显式约束 `children`，可以使用 `React.ReactNode`：
```typescript
import React from "react";

interface CardProps {
  title: string;
  children: React.ReactNode; // 显式定义 children 类型
}

function Card({ title, children }: CardProps) {
  return (
    <div className="card">
      <h3>{title}</h3>
      <div className="card-content">{children}</div>
    </div>
  );
}
```

### 1.3 React Hook 的类型注解

#### useState

`useState` 可以通过泛型指定状态类型，也可以让 TypeScript 自动推断：
```typescript
import React, { useState } from "react";

function Counter() {
  // 显式指定类型
  const [count, setCount] = useState<number>(0);
  // 自动推断（推荐，初始值明确时）
  const [text, setText] = useState("");

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <input value={text} onChange={(e) => setText(e.target.value)} />
    </div>
  );
}
```
#### useRef
`useRef` 用于访问 DOM 元素或保存可变值，需要通过泛型指定元素类型：
```typescript
import React, { useRef, useEffect } from "react";

function InputFocus() {
  // 指定 ref 为 HTMLInputElement 类型
  const inputRef = useRef<HTMLInputElement>(null);

  useEffect(() => {
    // 访问 ref 时需要判断非空
    inputRef.current?.focus();
  }, []);

  return <input ref={inputRef} type="text" />;
}
```
#### useContext
使用 `createContext` 时需要指定 Context 的类型：
```typescript
import React, { createContext, useContext, useState } from "react";

// 定义 Context 类型
interface ThemeContextType {
  theme: "light" | "dark";
  toggleTheme: () => void;
}

// 创建 Context，指定默认值（需符合类型）
const ThemeContext = createContext<ThemeContextType>({
  theme: "light",
  toggleTheme: () => {},
});

// Provider 组件
function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<"light" | "dark">("light");
  const toggleTheme = () => setTheme(theme === "light" ? "dark" : "light");

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// 使用 Context
function ThemeButton() {
  const { theme, toggleTheme } = useContext(ThemeContext);
  return (
    <button onClick={toggleTheme} style={{ background: theme === "light" ? "#fff" : "#333" }}>
      Toggle Theme
    </button>
  );
}
```
### 1.4 事件处理类型
React 事件对象需要使用特定的类型，如 `React.MouseEvent`、`React.ChangeEvent` 等：
```typescript
import React from "react";

function EventDemo() {
  // 点击事件
  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    console.log("Button clicked", e.target);
  };

  // 输入框变化事件
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    console.log("Input value:", e.target.value);
  };

  // 表单提交事件
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    console.log("Form submitted");
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" onChange={handleChange} />
      <button onClick={handleClick}>Click Me</button>
      <button type="submit">Submit</button>
    </form>
  );
}
```
## 2. Vue 3 中使用 TypeScript
Vue 3 对 TypeScript 有极佳的支持，结合 `<script setup>` 语法可以实现简洁、类型安全的组件开发。
### 2.1 环境搭建
#### 方式一：Vite（推荐）
使用 Vite 创建 Vue 3 + TypeScript 项目：
```bash
npm create vite@latest my-vue-ts-app -- --template vue-ts
```
生成的项目会自动配置好 `tsconfig.json`、`vue-tsc` 等依赖。
#### 方式二：Vue CLI
使用 Vue CLI 创建项目时选择 TypeScript 模板：
```bash
vue create my-vue-ts-app
# 选择 "Manually select features"，勾选 "TypeScript"
```
### 2.2 `<script setup>` 基础语法
Vue 3 的 `<script setup>` 是推荐的写法，结合 TypeScript 可以实现类型自动推断：
```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

// ref 自动推断类型为 Ref<number>
const count = ref(0);

const increment = () => {
  count.value++;
};
</script>
```

### 2.3 Props 类型定义
使用 `defineProps` 结合接口定义 Props 类型：
```vue
<template>
  <div class="user-card">
    <h3>{{ user.name }}</h3>
    <p>Age: {{ user.age }}</p>
    <p v-if="email">Email: {{ email }}</p>
  </div>
</template>

<script setup lang="ts">
import { defineProps } from "vue";

// 定义 Props 接口
interface User {
  name: string;
  age: number;
}

interface Props {
  user: User;
  email?: string; // 可选属性
}

// 使用 defineProps，指定泛型为 Props
const props = defineProps<Props>();

// 访问 Props
console.log(props.user.name);
</script>
```
#### 带默认值的 Props
使用 `withDefaults` 为 Props 设置默认值：
```vue
<script setup lang="ts">
import { defineProps, withDefaults } from "vue";

interface Props {
  title: string;
  count?: number;
}

// withDefaults 为可选属性设置默认值
const props = withDefaults(defineProps<Props>(), {
  count: 0,
});
</script>
```

### 2.4 Emits 类型定义

使用 `defineEmits` 结合类型字面量定义事件类型：
```vue
<template>
  <button @click="handleClick">Click Me</button>
</template>

<script setup lang="ts">
import { defineEmits } from "vue";

// 定义 Emits 类型：事件名 -> 参数类型
interface Emits {
  (e: "click", data: { id: number }): void;
  (e: "update:title", newTitle: string): void;
}

const emit = defineEmits<Emits>();

const handleClick = () => {
  // 触发事件，参数类型受约束
  emit("click", { id: 123 });
  emit("update:title", "New Title");
};
</script>
```
### 2.5 响应式 API 的类型注解
#### ref 与 reactive
```vue
<script setup lang="ts">
import { ref, reactive } from "vue";

// ref：显式指定类型（可选，初始值明确时可自动推断）
const count = ref<number>(0);
const name = ref("Alice"); // 自动推断为 Ref<string>

// reactive：通过接口定义类型
interface User {
  name: string;
  age: number;
}

const user = reactive<User>({
  name: "Bob",
  age: 25,
});
</script>
```

#### computed
```vue
<script setup lang="ts">
import { ref, computed } from "vue";

const firstName = ref("Alice");
const lastName = ref("Smith");

// computed 自动推断返回值类型
const fullName = computed(() => `${firstName.value} ${lastName.value}`);

// 显式指定返回值类型（可选）
const fullNameUpper = computed<string>(() => fullName.value.toUpperCase());
</script>
```
### 2.6 组件引用与 `defineExpose`

使用 `defineExpose` 暴露组件内部方法 / 属性，结合 `ref` 类型注解：
```vue
<!-- ChildComponent.vue -->
<script setup lang="ts">
import { ref, defineExpose } from "vue";

const internalCount = ref(0);
const increment = () => internalCount.value++;

// 暴露给父组件的内容
defineExpose({
  increment,
  getCount: () => internalCount.value,
});
</script>
```
```vue
<!-- ParentComponent.vue -->
<template>
  <ChildComponent ref="childRef" />
  <button @click="callChildMethod">Call Child Method</button>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import ChildComponent from "./ChildComponent.vue";

// 定义 ChildComponent 暴露的类型
interface ChildExposed {
  increment: () => void;
  getCount: () => number;
}

// 指定 ref 类型为 ChildExposed
const childRef = ref<ChildExposed | null>(null);

const callChildMethod = () => {
  childRef.value?.increment();
  console.log(childRef.value?.getCount());
};
</script>
```

------

这是教程的第九部分，详细讲解了 TypeScript 与 React、Vue 3 的结合使用，包括环境搭建、组件类型定义、Hook / 响应式 API 的类型注解等。接下来的部分会深入讲解 TypeScript 项目的**常见问题与最佳实践**，帮助你写出更优雅、更易维护的 TypeScript 代码。