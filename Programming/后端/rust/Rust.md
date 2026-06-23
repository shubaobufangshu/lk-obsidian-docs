# Rust 编程语言详细教程

本教程假设你具备扎实的编程基础（如 JavaScript/Node.js、C/C++ 或 Python），熟悉变量、函数、控制流、面向对象基础及内存管理概念。我们将直接聚焦 Rust 的核心特性与设计哲学，通过对比你已有的知识来加速理解。

---

## 第一章：环境准备与 Hello World

### 1.1 安装 Rust
Rust 通过 `rustup` 工具链管理器安装，它能同时管理多个 Rust 版本并保持更新。

**安装命令（Linux/macOS）：**
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

**Windows：** 下载并运行 [rustup-init.exe](https://rustup.rs/)。

安装后重启终端，验证安装：
```bash
rustc --version  # 编译器版本
cargo --version  # 包管理器版本
```

### 1.2 第一个程序：Hello World
创建一个文件 `main.rs`：
```rust
// main.rs

// 主函数，程序入口点
fn main() {
    // println! 是一个宏（注意 !），用于打印文本到标准输出
    println!("Hello, World!");
}
```

**编译与运行：**
```bash
rustc main.rs  # 编译生成可执行文件
./main         # 运行 (Windows: main.exe)
```

### 1.3 使用 Cargo（推荐）
Cargo 是 Rust 的包管理器和构建工具，类似 npm 或 pip。

**创建新项目：**
```bash
cargo new hello_world
cd hello_world
```

Cargo 会生成以下结构：
```
hello_world/
├── Cargo.toml  # 项目配置文件（类似 package.json）
└── src/
    └── main.rs # 源代码
```

**构建与运行：**
```bash
cargo run  # 自动编译并运行
cargo build  # 仅编译（调试模式）
cargo build --release  # 编译优化版本（发布用）
```

---

## 第二章：核心概念：变量、数据类型与函数

### 2.1 变量与可变性
Rust 变量**默认不可变**（Immutable），这是 Rust 保证内存安全的核心设计之一。

```rust
fn main() {
    let x = 5;       // 不可变变量
    // x = 6;        // 错误！无法修改不可变变量
    
    let mut y = 5;   // 可变变量（mut 关键字）
    y = 6;           // 正确
    
    // 变量遮蔽（Shadowing）：可以重复使用 let 声明同名变量
    let z = 5;
    let z = z + 1;   // 这里的 z 是一个新变量，类型也可以改变
    let z = "hello"; 
}
```

**对比 JavaScript：**
- `let` 在 Rust 中默认不可变，类似 JS 的 `const`。
- Rust 的 `mut` 类似 JS 的 `let`。
- **变量遮蔽**是 Rust 特有，允许在同一作用域内重用变量名。

### 2.2 数据类型
Rust 是静态类型语言，编译时必须知道所有变量的类型。

#### 2.2.1 标量类型（Scalar）
代表单个值，分为四类：

| 类型   | 描述              | 示例                  |
| ------ | ----------------- | --------------------- |
| 整数   | 无小数的数字      | `let a: i32 = 10;`    |
| 浮点数 | 带小数的数字      | `let b: f64 = 3.14;`  |
| 布尔值 | `true` 或 `false` | `let c: bool = true;` |
| 字符   | 单个 Unicode 字符 | `let d: char = '🦀';`  |

**整数类型细节：**
- 有符号：`i8`, `i16`, `i32`（默认）, `i64`, `i128`, `isize`（指针大小）
- 无符号：`u8`, `u16`, `u32`, `u64`, `u128`, `usize`（指针大小）

#### 2.2.2 复合类型（Compound）
将多个值组合成一个类型。

**元组（Tuple）：** 固定长度，类型可不同。
```rust
fn main() {
    let tup: (i32, f64, char) = (500, 6.4, 'z');
    
    // 解构（Destructuring）
    let (x, y, z) = tup;
    
    // 索引访问
    let five_hundred = tup.0;
}
```

**数组（Array）：** 固定长度，类型相同。
```rust
fn main() {
    let arr: [i32; 5] = [1, 2, 3, 4, 5]; // [类型; 长度]
    let first = arr[0];
    
    // 初始化：所有元素为 3
    let arr2 = [3; 5]; // 等价于 [3, 3, 3, 3, 3]
}
```

### 2.3 函数
Rust 函数使用 `fn` 关键字定义，参数必须标注类型。

```rust
// 函数定义：参数 x 类型为 i32，返回值类型也是 i32
fn add_one(x: i32) -> i32 {
    x + 1  // 注意：没有分号，这是表达式（Expression）
}

fn main() {
    let num = add_one(5);
    println!("{}", num); // 输出 6
}
```

**关键概念：语句 vs 表达式**
- **语句（Statement）：** 执行操作但不返回值，以分号结尾（如 `let x = 5;`）。
- **表达式（Expression）：** 计算并产生一个值，不以分号结尾（如 `x + 1`、代码块 `{}`）。

```rust
fn main() {
    let y = {
        let x = 3;
        x + 1 // 这个代码块的值是 4
    };
}
```

---

## 第三章：所有权（Ownership）—— Rust 的核心

所有权是 Rust 最独特的特性，它让 Rust 无需垃圾回收（GC）即可保证内存安全。

### 3.1 所有权规则
1. 每个值都有一个**所有者**（Owner）变量。
2. 同一时间值只能有**一个**所有者。
3. 当所有者离开作用域（Scope），值将被**丢弃**（Drop），内存释放。

### 3.2 作用域与移动（Move）
```rust
fn main() {
    {
        let s = String::from("hello"); // s 进入作用域
        // 使用 s
    } // s 离开作用域，内存自动释放
    
    // 移动（Move）：对于堆上的数据（如 String）
    let s1 = String::from("hello");
    let s2 = s1; // s1 的所有权“移动”给了 s2，s1 失效！
    // println!("{}", s1); // 错误！s1 已无效
    
    // 克隆（Clone）：深度复制堆上的数据
    let s3 = String::from("hello");
    let s4 = s3.clone(); // s3 仍然有效
}
```

**对比 C++：** 这类似 C++ 的“移动语义”，但 Rust 在编译时强制检查，防止使用已移动的变量。

**栈上数据的拷贝：** 实现了 `Copy` trait 的类型（如整数、布尔、浮点数、char）会自动拷贝，不会移动。
```rust
let x = 5;
let y = x; // x 仍然有效，因为 i32 实现了 Copy
```

### 3.3 引用与借用（References and Borrowing）
如果我们想使用一个值但不获取其所有权，可以使用**引用**（`&`）。

```rust
fn main() {
    let s1 = String::from("hello");
    
    // 传递引用给函数，这叫“借用”（Borrowing）
    let len = calculate_length(&s1); 
    
    println!("'{}' 的长度是 {}", s1, len); // s1 仍然有效
}

// 函数签名表明它接受一个 String 的引用
fn calculate_length(s: &String) -> usize {
    s.len()
} // s 离开作用域，但因为它不拥有引用的值，所以什么都不会发生
```

#### 3.3.1 可变引用
```rust
fn main() {
    let mut s = String::from("hello");
    
    change(&mut s); // 传递可变引用
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

**可变引用的限制（核心安全保证）：**
1. 在同一时间，对于一个特定的数据，**只能有一个**可变引用。
2. 不能同时拥有可变引用和不可变引用。

```rust
let mut s = String::from("hello");

let r1 = &s; // 不可变引用
let r2 = &s; // 可以有多个不可变引用
// let r3 = &mut s; // 错误！不能在有不可变引用时创建可变引用

println!("{} and {}", r1, r2); // r1 和 r2 在这里之后不再使用

let r3 = &mut s; // 正确！现在可以创建可变引用了
```

这些规则在编译时防止了**数据竞争**（Data Race）。

#### 3.3.2 悬垂引用（Dangling References）
Rust 编译器确保引用永远不会变成悬垂状态（即引用指向已释放的内存）。

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String { // 错误！返回了一个悬垂引用
    let s = String::from("hello");
    &s // 返回 s 的引用
} // s 离开作用域并被丢弃，内存释放

// 正确做法：直接返回 String，转移所有权
fn no_dangle() -> String {
    let s = String::from("hello");
    s
}
```

---

## 第四章：结构体（Structs）与方法

### 4.1 定义与实例化结构体
结构体允许你将多个相关的值组合成一个有意义的类型。

```rust
// 定义一个结构体
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn main() {
    // 实例化结构体
    let user1 = User {
        email: String::from("someone@example.com"),
        username: String::from("someusername123"),
        active: true,
        sign_in_count: 1,
    };
    
    // 访问字段
    println!("{}", user1.email);
    
    // 结构体更新语法（Struct Update Syntax）
    let user2 = User {
        email: String::from("another@example.com"),
        username: String::from("anotherusername567"),
        ..user1 // 其余字段从 user1 中获取
    };
}
```

### 4.2 元组结构体（Tuple Structs）
类似元组的结构体，没有字段名，只有类型。

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
    // black 和 origin 是不同的类型，即使它们的字段类型相同
}
```

### 4.3 方法（Methods）
方法是在结构体（或枚举、trait 对象）上下文中定义的函数，第一个参数总是 `self`。

```rust
#[derive(Debug)] // 自动派生 Debug trait，用于打印
struct Rectangle {
    width: u32,
    height: u32,
}

// impl 块（Implementation）
impl Rectangle {
    // 方法：计算面积
    // &self 是 self: &Self 的缩写，表示借用 Rectangle 实例
    fn area(&self) -> u32 {
        self.width * self.height
    }
    
    // 方法：判断是否能容纳另一个矩形
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
    
    // 关联函数（Associated Function）：不以 self 为参数
    // 类似静态方法，通常用于构造器
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };
    
    println!("面积: {}", rect1.area());
    
    let rect2 = Rectangle {
        width: 10,
        height: 40,
    };
    
    println!("rect1 能容纳 rect2? {}", rect1.can_hold(&rect2));
    
    // 调用关联函数，使用 :: 语法
    let sq = Rectangle::square(10);
    println!("正方形: {:?}", sq); // {:?} 用于 Debug 打印
}
```

---

## 第五章：枚举（Enums）与模式匹配

### 5.1 定义枚举
枚举允许你定义一个类型，它可以是几个不同变体（Variants）中的一个。

```rust
// 定义一个 IP 地址类型的枚举
enum IpAddr {
    V4(u8, u8, u8, u8), // 变体可以关联数据
    V6(String),
}

fn main() {
    let home = IpAddr::V4(127, 0, 0, 1);
    let loopback = IpAddr::V6(String::from("::1"));
}
```

### 5.2 Option 枚举
Rust 没有 `null` 值，但有一个标准库枚举 `Option<T>` 来表示值可能存在或不存在。

```rust
// Option 的定义（标准库中）：
// enum Option<T> {
//     None,
//     Some(T),
// }

fn main() {
    let some_number = Some(5); // Option<i32>
    let some_char = Some('e'); // Option<char>
    let absent_number: Option<i32> = None; // 必须标注类型
    
    // 不能直接将 Option<T> 与 T 相加，必须先处理 Option
    // let x = some_number + 5; // 错误！
}
```

### 5.3 match 控制流结构
`match` 允许你将一个值与一系列模式比较，并执行匹配的代码。

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

**匹配 Option<T>：**
```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

fn main() {
    let five = Some(5);
    let six = plus_one(five);
    let none = plus_one(None);
}
```

**match 必须穷尽所有可能！** 如果你漏掉了某些情况，编译器会报错。可以使用 `_` 通配符匹配剩余情况。

### 5.4 if let 简洁控制流
如果你只关心一种模式，可以使用 `if let` 代替 `match`。

```rust
fn main() {
    let some_u8_value = Some(0u8);
    
    // 使用 match
    match some_u8_value {
        Some(3) => println!("three"),
        _ => (), // 什么都不做
    }
    
    // 使用 if let（更简洁）
    if let Some(3) = some_u8_value {
        println!("three");
    } else {
        // 可选的 else 块
    }
}
```

---

## 第六章：错误处理

Rust 将错误分为两大类：
1. **可恢复错误**（Recoverable）：使用 `Result<T, E>` 枚举。
2. **不可恢复错误**（Unrecoverable）：使用 `panic!` 宏。

### 6.1 panic! 宏（不可恢复错误）
当程序遇到无法处理的错误时，`panic!` 会打印错误信息，清理栈并退出。

```rust
fn main() {
    // panic!("crash and burn"); // 直接 panic
    
    let v = vec![1, 2, 3];
    v[10]; // 索引越界，自动 panic
}
```

### 6.2 Result 枚举（可恢复错误）
`Result` 用于处理可能失败的操作，定义如下：
```rust
// enum Result<T, E> {
//     Ok(T),  // 成功，包含类型 T 的值
//     Err(E), // 失败，包含类型 E 的错误信息
// }
```

**示例：打开文件**
```rust
use std::fs::File;
use std::io::Error;

fn main() {
    let f: Result<File, Error> = File::open("hello.txt");
    
    // 使用 match 处理 Result
    let f = match f {
        Ok(file) => file,
        Err(error) => {
            panic!("打开文件失败: {:?}", error);
        }
    };
}
```

#### 6.2.1 unwrap 和 expect
如果我们确定 `Result` 一定是 `Ok`，可以使用 `unwrap` 或 `expect` 来简化代码。

```rust
use std::fs::File;

fn main() {
    // unwrap：如果是 Err 则 panic
    let f = File::open("hello.txt").unwrap();
    
    // expect：类似 unwrap，但可以自定义 panic 信息
    let f = File::open("hello.txt").expect("无法打开 hello.txt");
}
```

#### 6.2.2 ? 运算符（传播错误）
`?` 运算符用于快速传播错误。如果 `Result` 是 `Ok`，则取出值继续执行；如果是 `Err`，则将错误返回给调用者。

```rust
use std::fs::File;
use std::io::{self, Read};

// 读取文件内容并返回 Result<String, io::Error>
fn read_username_from_file() -> Result<String, io::Error> {
    // 方式 1：显式 match
    // let mut f = match File::open("username.txt") {
    //     Ok(file) => file,
    //     Err(e) => return Err(e),
    // };
    
    // 方式 2：使用 ? 运算符（更简洁）
    let mut f = File::open("username.txt")?;
    
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}

fn main() {
    match read_username_from_file() {
        Ok(username) => println!("用户名: {}", username),
        Err(e) => println!("读取失败: {:?}", e),
    }
}
```

**注意：** `?` 只能在返回 `Result` 或 `Option` 的函数中使用。

---

## 第七章：通用集合类型

### 7.1 Vector (`Vec<T>`)
动态数组，允许在一个单独的数据结构中存储多个相同类型的值。

```rust
fn main() {
    // 创建空 Vector
    let v: Vec<i32> = Vec::new();
    
    // 使用 vec! 宏创建并初始化
    let v = vec![1, 2, 3];
    
    // 更新 Vector
    let mut v = Vec::new();
    v.push(5);
    v.push(6);
    
    // 读取元素
    let third: &i32 = &v[2]; // 索引访问，越界会 panic
    let third: Option<&i32> = v.get(2); // get 方法，返回 Option，越界返回 None
    
    // 遍历
    let v = vec![100, 32, 57];
    for i in &v {
        println!("{}", i);
    }
    
    // 遍历并修改
    let mut v = vec![100, 32, 57];
    for i in &mut v {
        *i += 50; // * 解引用
    }
}
```

### 7.2 字符串 (`String`)
Rust 的字符串核心是 UTF-8 编码的字节集合。

```rust
fn main() {
    // 创建 String
    let mut s = String::new();
    let s = "initial contents".to_string();
    let s = String::from("initial contents");
    
    // 更新 String
    let mut s = String::from("foo");
    s.push_str("bar"); // 追加字符串 slice
    s.push('l');       // 追加单个字符
    
    // 拼接
    let s1 = String::from("Hello, ");
    let s2 = String::from("world!");
    let s3 = s1 + &s2; // s1 被移动，之后不能再使用
    
    // 使用 format! 宏（更灵活，不获取所有权）
    let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = format!("{}-{}-{}", s1, s2, "toe");
    
    // 注意：Rust 字符串不支持索引访问单个字符（因为 UTF-8 是变长编码）
    // let h = s[0]; // 错误！
    
    // 正确遍历字符串的方法
    for c in "नमस्ते".chars() {
        println!("{}", c); // 打印 Unicode 标量值
    }
    
    for b in "नमस्ते".bytes() {
        println!("{}", b); // 打印原始字节
    }
}
```

### 7.3 哈希映射 (`HashMap<K, V>`)
键值对集合，类似 Python 的字典或 JavaScript 的对象。

```rust
use std::collections::HashMap;

fn main() {
    // 创建 HashMap
    let mut scores = HashMap::new();
    
    // 插入键值对
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);
    
    // 访问值
    let team_name = String::from("Blue");
    let score: Option<&i32> = scores.get(&team_name); // 返回 Option
    
    // 遍历
    for (key, value) in &scores {
        println!("{}: {}", key, value);
    }
    
    // 覆盖旧值
    scores.insert(String::from("Blue"), 25); // Blue 的值变为 25
    
    // 只在键没有对应值时插入
    scores.entry(String::from("Blue")).or_insert(50); // 不会插入，因为 Blue 已存在
    scores.entry(String::from("Red")).or_insert(50);  // 会插入 Red: 50
}
```

---

## 第八章：泛型（Generics）
泛型是一种**代码复用的类型安全抽象**，可以让你编写适用于多种类型的通用代码，而无需为每个类型重复编写逻辑。
> 对比你熟悉的概念：Rust 泛型类似 TypeScript/Java 的泛型、C++ 的模板，核心区别是 Rust 泛型有严格的编译期类型检查，且是**零成本抽象**——编译后和手写具体类型的代码性能完全一致。

### 8.1 泛型解决的核心问题
先看一个重复代码的场景：我们需要两个函数，分别找到 i32 数组和 char 数组中的最大值。
```rust
// 找 i32 数组的最大值
fn largest_i32(list: &[i32]) -> &i32 {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

// 找 char 数组的最大值
fn largest_char(list: &[char]) -> &char {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];
    println!("最大数字: {}", largest_i32(&number_list));

    let char_list = vec!['y', 'm', 'a', 'q'];
    println!("最大字符: {}", largest_char(&char_list));
}
```
两个函数的逻辑完全一致，仅参数类型不同。泛型就是用来消除这种重复的，我们可以用一个泛型函数替代两个重复实现。

### 8.2 函数中的泛型
泛型参数使用 `<T>` 声明（T 是通用的类型占位符，也可以用其他字母/名称），放在函数名之后、参数列表之前。

#### 泛型函数的基础语法
```rust
// 泛型函数：T 是类型参数，代表任意类型
fn largest<T>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        // 注意：这里会编译报错！
        if item > largest {
            largest = item;
        }
    }
    largest
}
```
**为什么报错？** 不是所有类型都支持比较大小（`>` 运算符）。Rust 要求我们必须明确约束泛型类型的行为——只有实现了**比较 trait** `PartialOrd` 的类型，才能使用比较运算符。

#### 带 Trait 约束的泛型函数（可正常编译）
```rust
// T: PartialOrd 表示：T 必须实现了 PartialOrd 这个 trait
fn largest<T: PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];
    for item in list {
        if item > largest {
            largest = item;
        }
    }
    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];
    println!("最大数字: {}", largest(&number_list)); // 自动推断 T 为 i32

    let char_list = vec!['y', 'm', 'a', 'q'];
    println!("最大字符: {}", largest(&char_list)); // 自动推断 T 为 char
}
```
> 注：Trait 约束会在第九章详细讲解，这里只需理解：它用来限制泛型类型的可用行为，保证编译期类型安全。

### 8.3 结构体中的泛型
结构体的字段也可以使用泛型类型，支持单个或多个泛型参数。

#### 单个泛型参数
```rust
// 定义一个泛型结构体 Point，x 和 y 是相同的泛型类型 T
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let integer_point = Point { x: 5, y: 10 }; // T 推断为 i32
    let float_point = Point { x: 1.0, y: 4.5 }; // T 推断为 f64
    // let error_point = Point { x: 5, y: 4.5 }; // 报错！x 和 y 类型必须一致
}
```

#### 多个泛型参数
```rust
// 两个泛型参数 T 和 U，x 和 y 可以是不同类型
struct Point<T, U> {
    x: T,
    y: U,
}

fn main() {
    let mixed_point = Point { x: 5, y: 4.5 }; // T=i32, U=f64
    let string_point = Point { x: "hello", y: 'z' }; // T=&str, U=char
}
```

### 8.4 枚举中的泛型
你已经接触过 Rust 标准库中最常用的泛型枚举：`Option<T>` 和 `Result<T, E>`。
```rust
// 标准库 Option 枚举的定义
enum Option<T> {
    Some(T), // 包含一个泛型类型 T 的值
    None,
}

// 标准库 Result 枚举的定义
enum Result<T, E> {
    Ok(T),    // 成功时返回类型 T 的值
    Err(E),   // 失败时返回类型 E 的错误
}
```
你也可以自定义泛型枚举，比如一个支持多种数据类型的消息枚举：
```rust
enum Message<T> {
    Quit,
    Move { x: T, y: T },
    Write(String),
    ChangeColor(T, T, T),
}
```

### 8.5 方法中的泛型
为泛型结构体/枚举实现方法时，需要在 `impl` 关键字后声明泛型参数，告诉编译器这个 `impl` 是针对泛型类型的。

#### 为泛型结构体实现通用方法
```rust
struct Point<T> {
    x: T,
    y: T,
}

// impl<T> 表示：为所有类型 T 的 Point<T> 实现方法
impl<T> Point<T> {
    // 获取 x 字段的引用
    fn x(&self) -> &T {
        &self.x
    }

    // 获取 y 字段的引用
    fn y(&self) -> &T {
        &self.y
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };
    println!("x: {}, y: {}", p.x(), p.y());
}
```

#### 为特定类型实现专属方法
你可以只为特定的泛型类型实现方法，而非所有泛型类型。比如只为 `Point<f64>` 实现一个计算距离的方法，其他类型的 Point 没有这个方法：
```rust
struct Point<T> {
    x: T,
    y: T,
}

// 通用方法：所有 Point<T> 都有
impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

// 专属方法：只有 Point<f64> 才有
impl Point<f64> {
    // 计算到原点的距离（仅 f64 类型支持浮点运算）
    fn distance_from_origin(&self) -> f64 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}

fn main() {
    let p_float = Point { x: 3.0, y: 4.0 };
    println!("距离原点: {}", p_float.distance_from_origin()); // 正常运行

    let p_int = Point { x: 3, y: 4 };
    // p_int.distance_from_origin(); // 报错！i32 类型的 Point 没有这个方法
}
```

### 8.6 泛型的性能：零成本抽象
Rust 的泛型是**编译期单态化（Monomorphization）**实现的：编译器会在编译时，将泛型代码展开为对应具体类型的专用代码。
比如你调用了 `Point<i32>` 和 `Point<f64>`，编译器会自动生成两个结构体：`Point_i32` 和 `Point_f64`，完全替代泛型版本。

这意味着：
- 运行时**无任何性能开销**，和手写具体类型的代码完全一致
- 没有 Java 泛型的“类型擦除”问题，编译后保留完整的类型信息
- 编译时间会变长（因为要展开多个版本），但换来运行时的极致性能

---

## 第九章：Trait（特质）
Trait 是 Rust 对**类型行为的抽象**，用来定义一组类型可以共享的方法签名，是 Rust 实现“接口”、多态和代码复用的核心机制。
> 对比你熟悉的概念：Trait 类似 Java/TypeScript 的接口（Interface），但更强大：
> 1. 可以提供方法的默认实现
> 2. 可以为外部类型实现自定义 Trait（符合孤儿规则）
> 3. 可以作为泛型约束（Trait Bound）
> 4. 支持关联类型、泛型、运算符重载等高级特性

### 9.1 定义 Trait
Trait 的核心是定义**方法签名**，描述实现这个 Trait 的类型必须具备的行为。
语法：`trait TraitName { 方法签名 }`

我们定义一个 `Summary` Trait，用来描述“可以生成摘要”的行为：
```rust
// 定义 Summary Trait
pub trait Summary {
    // 方法签名：实现这个 Trait 的类型必须提供这个方法
    fn summarize(&self) -> String;
}
```
这个 Trait 声明：任何实现了 `Summary` 的类型，都必须有一个 `summarize` 方法，接收 `&self`，返回 `String`。

### 9.2 为类型实现 Trait
实现 Trait 的语法：`impl TraitName for TypeName { 方法实现 }`

我们为两个自定义类型（新闻文章、推文）实现 `Summary` Trait：
```rust
// 定义 Summary Trait
pub trait Summary {
    fn summarize(&self) -> String;
}

// 新闻文章结构体
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

// 为 NewsArticle 实现 Summary Trait
impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}，作者：{}（{}）", self.headline, self.author, self.location)
    }
}

// 推文结构体
pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

// 为 Tweet 实现 Summary Trait
impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("@{}：{}", self.username, self.content)
    }
}

fn main() {
    let article = NewsArticle {
        headline: String::from("Rust 1.80 版本发布"),
        location: String::from("北京"),
        author: String::from("Rust 团队"),
        content: String::from("本次更新带来了多项性能优化..."),
    };

    let tweet = Tweet {
        username: String::from("rust_lang"),
        content: String::from("新版本的泛型优化太赞了！"),
        reply: false,
        retweet: false,
    };

    println!("文章摘要：{}", article.summarize());
    println!("推文摘要：{}", tweet.summarize());
}
```

### 9.3 Trait 的默认实现
Trait 可以为方法提供**默认实现**，实现该 Trait 的类型可以选择重写，或者直接使用默认实现。
> 注意：默认实现可以调用同一个 Trait 中的其他方法（哪怕这些方法没有默认实现）。

```rust
pub trait Summary {
    // 必须由实现者提供的方法
    fn summarize_author(&self) -> String;

    // 带默认实现的方法
    fn summarize(&self) -> String {
        // 调用同一个 Trait 中的 summarize_author 方法
        format!("阅读更多 {} 的文章...", self.summarize_author())
    }
}

// 为 NewsArticle 实现 Summary
impl Summary for NewsArticle {
    // 只需要实现必须的 summarize_author 方法
    fn summarize_author(&self) -> String {
        format!("@{}", self.author)
    }
    // 直接使用 summarize 的默认实现，无需重写
}

// 为 Tweet 实现 Summary
impl Summary for Tweet {
    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }

    // 重写 summarize 方法，覆盖默认实现
    fn summarize(&self) -> String {
        format!("@{}：{}", self.username, self.content)
    }
}

fn main() {
    let article = NewsArticle {
        headline: String::from("Rust 1.80 版本发布"),
        location: String::from("北京"),
        author: String::from("Rust 团队"),
        content: String::from("本次更新带来了多项性能优化..."),
    };

    let tweet = Tweet {
        username: String::from("rust_lang"),
        content: String::from("新版本的泛型优化太赞了！"),
        reply: false,
        retweet: false,
    };

    println!("文章摘要：{}", article.summarize()); // 使用默认实现
    println!("推文摘要：{}", tweet.summarize()); // 使用重写的实现
}
```

### 9.4 Trait 作为函数参数
Trait 最常用的场景之一，是作为函数参数，用来接收**任何实现了该 Trait 的类型**。

#### 方式1：impl Trait 语法（简洁版）
适合简单场景，直接用 `impl TraitName` 作为参数类型：
```rust
// 接收任何实现了 Summary Trait 的类型
pub fn notify(item: &impl Summary) {
    println!("新通知：{}", item.summarize());
}

fn main() {
    let article = NewsArticle { /* 字段 */ };
    let tweet = Tweet { /* 字段 */ };

    notify(&article); // 正常运行：NewsArticle 实现了 Summary
    notify(&tweet);   // 正常运行：Tweet 实现了 Summary
}
```

#### 方式2：Trait Bound（Trait 约束，完整版）
`impl Trait` 其实是 Trait Bound 的语法糖，完整的写法是用泛型参数加 Trait 约束：
```rust
// 泛型函数：T 必须实现 Summary Trait
pub fn notify<T: Summary>(item: &T) {
    println!("新通知：{}", item.summarize());
}
```
**两者的核心区别**：
- `impl Trait` 适合简单场景，参数可以是不同的实现类型
- Trait Bound 适合复杂场景，要求多个参数是**同一个实现类型**

比如下面的场景，必须用 Trait Bound：
```rust
// 要求两个参数是同一个类型，且都实现了 Summary
pub fn notify<T: Summary>(item1: &T, item2: &T) {
    println!("通知1：{}", item1.summarize());
    println!("通知2：{}", item2.summarize());
}

fn main() {
    let article1 = NewsArticle { /* 字段 */ };
    let article2 = NewsArticle { /* 字段 */ };
    let tweet = Tweet { /* 字段 */ };

    notify(&article1, &article2); // 正常：两个都是 NewsArticle 类型
    // notify(&article1, &tweet); // 报错：类型不一致
}
```

### 9.5 多 Trait 约束与 where 子句
#### 多个 Trait 约束
用 `+` 语法可以要求类型同时实现多个 Trait：
```rust
// 要求类型同时实现 Summary 和 Display（可打印）两个 Trait
pub fn notify(item: &(impl Summary + std::fmt::Display)) {
    println!("新通知：{}", item);
}

// 泛型版本的多约束
pub fn notify<T: Summary + std::fmt::Display>(item: &T) {
    println!("新通知：{}", item);
}
```

#### where 子句（简化复杂约束）
当 Trait 约束太多时，函数签名会变得非常冗长，`where` 子句可以把约束单独拆分出来，提升可读性：
```rust
// 冗长的写法
fn some_function<T: std::fmt::Display + Clone, U: Clone + std::fmt::Debug>(t: &T, u: &U) -> i32 {
    // 函数逻辑
}

// 用 where 子句简化
fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: std::fmt::Display + Clone,
    U: Clone + std::fmt::Debug,
{
    // 函数逻辑
}
```

### 9.6 返回实现了 Trait 的类型
你可以用 `impl Trait` 作为函数的返回值类型，表示函数返回**某个实现了该 Trait 的类型**，隐藏具体的实现细节。
```rust
// 返回一个实现了 Summary Trait 的类型
fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("rust_lang"),
        content: String::from("新版本的泛型优化太赞了！"),
        reply: false,
        retweet: false,
    }
}
```
**核心限制**：`impl Trait` 作为返回值时，函数**只能返回同一个具体类型**，不能返回不同的类型。
比如下面的代码会编译报错：
```rust
// 报错！if 和 else 返回了不同的类型，哪怕都实现了 Summary
fn returns_summarizable(switch: bool) -> impl Summary {
    if switch {
        NewsArticle { /* 字段 */ }
    } else {
        Tweet { /* 字段 */ }
    }
}
```
> 注：如果需要返回多个不同的 Trait 实现类型，需要使用**Trait 对象**（后续章节会讲解）。

### 9.7 覆盖实现（Blanket Implementation）
覆盖实现是 Rust 最强大的特性之一：你可以为**所有满足某个 Trait 约束的类型**，批量实现另一个 Trait。

这是 Rust 标准库大量使用的模式，比如标准库的 `ToString` Trait，为所有实现了 `Display` Trait 的类型自动实现了 `to_string()` 方法：
```rust
// 标准库的简化实现
pub trait ToString {
    fn to_string(&self) -> String;
}

// 为所有实现了 Display 的类型，实现 ToString Trait
impl<T: std::fmt::Display> ToString for T {
    fn to_string(&self) -> String {
        // 用 Display 格式化字符串
        let mut s = String::new();
        std::fmt::write(&mut s, format_args!("{}", self)).unwrap();
        s
    }
}
```
这就是为什么所有可打印的类型（比如 i32、&str、bool）都可以调用 `to_string()` 方法——它们都实现了 `Display` Trait，自动获得了 `ToString` 的实现。

### 9.8 孤儿规则（Orphan Rule）
Rust 有一个核心规则，限制 Trait 的实现范围，避免依赖冲突：
> 要为一个类型实现 Trait，**要么 Trait 是在当前 crate 中定义的，要么类型是在当前 crate 中定义的**。

简单来说：你不能为**外部类型**实现**外部 Trait**。
- 合法：为你自定义的 `MyType` 实现标准库的 `Display` Trait
- 合法：为标准库的 `String` 实现你自定义的 `MyTrait` Trait
- 非法：为标准库的 `String` 实现标准库的 `Display` Trait（两者都是外部的）

这个规则保证了两个依赖不会为同一个类型实现同一个 Trait，避免编译器不知道该用哪个实现。

---

## 第十章：生命周期（Lifetimes）
生命周期是 Rust 独有的核心特性，也是大多数初学者的最大难点。它的本质是**编译器用来追踪引用有效作用域的机制**，核心目标是**彻底杜绝悬垂引用**，保证内存安全。

> 前置回顾：我们在第三章讲过，引用必须始终有效，不能指向已释放的内存；Rust 编译器会检查引用的作用域，防止悬垂引用。生命周期就是对这个检查的显式标注，用来告诉编译器多个引用之间的有效范围关系。

### 10.1 生命周期解决的核心问题
先看一个会触发编译错误的例子：我们想写一个函数，返回两个字符串 slice 中较长的那个。
```rust
// 编译报错！缺少生命周期标注
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
**为什么报错？** 编译器无法确定返回的引用的生命周期，不知道它和 `x`、`y` 的生命周期有什么关系。

举个调用场景，可能出现悬垂引用：
```rust
fn main() {
    let string1 = String::from("长字符串");
    let result;
    {
        let string2 = String::from("短");
        result = longest(string1.as_str(), string2.as_str());
    } // string2 在这里被释放
    println!("最长的字符串是: {}", result); // 这里 result 可能指向已释放的 string2！
}
```
编译器不知道返回的引用是来自 `x` 还是 `y`，所以无法判断返回的引用是否在当前作用域有效。**生命周期标注就是用来明确告诉编译器：返回的引用的有效范围，和输入参数的有效范围的绑定关系**。

### 10.2 生命周期标注的语法
生命周期标注的格式是 `'a`（单引号 + 小写字母/名称），用来描述引用的生命周期。
- 标注位置：放在引用符号 `&` 之后，类型之前，比如 `&'a str`、`&'a i32`
- 核心原则：**生命周期标注不会改变引用的实际生命周期，只是描述多个引用之间的生命周期约束关系**，让编译器做安全检查。

### 10.3 函数签名中的生命周期标注
我们给 `longest` 函数加上正确的生命周期标注：
```rust
// 生命周期标注：'a 是一个生命周期参数
// 含义：x 和 y 的生命周期至少和 'a 一样长，返回的引用的生命周期和 'a 一致
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```
**标注的含义**：
1. 声明一个生命周期参数 `'a`，放在函数名后的 `<>` 中
2. 输入参数 `x` 和 `y` 都标注了 `'a`，表示它们的生命周期都和 `'a` 绑定
3. 返回值也标注了 `'a`，表示返回的引用的生命周期，等于 `x` 和 `y` 生命周期的**交集**（也就是两个参数中生命周期更短的那个）

编译器会根据这个标注，检查所有调用场景：
- 如果返回的引用的使用范围，没有超过两个输入参数的生命周期交集，编译通过
- 如果超过了，编译报错，防止悬垂引用

#### 正确的调用场景
```rust
fn main() {
    let string1 = String::from("长字符串");
    let string2 = String::from("短字符串");
    let result = longest(string1.as_str(), string2.as_str());
    // result 的生命周期是 string1 和 string2 的交集，在这里仍然有效
    println!("最长的字符串是: {}", result); // 编译通过
}
```

#### 错误的调用场景（编译器拦截）
```rust
fn main() {
    let string1 = String::from("长字符串");
    let result;
    {
        let string2 = String::from("短");
        result = longest(string1.as_str(), string2.as_str());
    } // string2 在这里被释放，'a 的生命周期结束
    // result 的生命周期已经结束，这里使用会报错
    println!("最长的字符串是: {}", result); // 编译报错！
}
```
编译器会明确提示：`string2` 活得不够久，返回的引用在 `string2` 释放后仍然被使用，违反了生命周期约束。

### 10.4 生命周期省略规则
很多时候，编译器会自动推断引用的生命周期，不需要我们手动标注。Rust 定义了3条**生命周期省略规则**，编译器会按照规则自动补全生命周期标注，无需手动编写。

#### 规则1：输入生命周期规则
每个引用类型的输入参数，都会被分配一个独立的生命周期参数。
- 示例：`fn foo(x: &i32)` 自动补全为 `fn foo<'a>(x: &'a i32)`
- 示例：`fn foo(x: &i32, y: &str)` 自动补全为 `fn foo<'a, 'b>(x: &'a i32, y: &'b str)`

#### 规则2：单输入生命周期规则
如果只有一个输入生命周期参数，那么这个生命周期会被分配给所有输出生命周期参数。
- 示例：`fn foo(x: &str) -> &str` 自动补全为 `fn foo<'a>(x: &'a str) -> &'a str`
- 这就是为什么我们第三章的 `calculate_length` 函数不需要标注生命周期：完全符合这个规则

#### 规则3：方法生命周期规则
如果有多个输入生命周期参数，但其中一个是 `&self` 或 `&mut self`（方法），那么 `self` 的生命周期会被分配给所有输出生命周期参数。
- 这个规则让绝大多数方法都不需要手动标注生命周期，极大简化了代码

**什么时候需要手动标注？**
当编译器应用完这3条规则后，仍然无法确定输出引用的生命周期时，就必须手动标注。比如 `longest` 函数，有两个输入生命周期参数，不符合规则2，也不是方法，所以必须手动标注。

### 10.5 结构体中的生命周期标注
如果结构体中包含引用类型的字段，就必须为这个引用加上生命周期标注，保证**结构体实例的生命周期，不会超过其引用字段的生命周期**。
```rust
// 结构体 ImportantExcerpt 的生命周期 'a，和它的 part 字段的生命周期绑定
struct ImportantExcerpt<'a> {
    part: &'a str, // 引用类型的字段，必须标注生命周期
}

fn main() {
    let novel = String::from("这是一本小说。第一章：Rust 入门...");
    let first_chapter = novel.split('。').next().unwrap();
    
    // 实例 excerpt 的生命周期，不能超过 first_chapter 引用的 novel 的生命周期
    let excerpt = ImportantExcerpt {
        part: first_chapter,
    };

    println!("节选内容：{}", excerpt.part); // 编译通过
}
```
**核心约束**：`ImportantExcerpt` 的实例，绝对不能活得比 `part` 字段引用的字符串更久，否则就会出现悬垂引用，编译器会直接拦截。

### 10.6 方法中的生命周期标注
为带生命周期的结构体实现方法时，需要在 `impl` 关键字后声明生命周期参数，和泛型的语法一致：
```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

// impl 后必须声明生命周期参数
impl<'a> ImportantExcerpt<'a> {
    // 符合生命周期省略规则3，返回值的生命周期和 self 一致，无需手动标注
    fn get_part(&self) -> &str {
        self.part
    }

    // 手动标注生命周期的方法
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("公告：{}", announcement);
        self.part
    }
}
```
上面的方法完全符合生命周期省略规则3，返回值的生命周期自动和 `&self` 绑定，无需手动标注。

### 10.7 静态生命周期 `'static`
`'static` 是 Rust 中一个特殊的生命周期，它表示**引用在整个程序的运行期间都有效**。

最常见的 `'static` 引用是字符串字面量：
```rust
// 字符串字面量被编译到二进制文件中，整个程序运行期间都存在
let s: &'static str = "我是静态生命周期字符串";
```
**使用注意事项**：
- 不要滥用 `'static` 来解决生命周期报错，这是初学者最常见的误区。`'static` 只适用于真的在整个程序运行期间都有效的引用。
- 绝大多数生命周期报错，都是因为代码逻辑本身存在悬垂引用的风险，应该修复逻辑，而不是强行标注 `'static`。

### 10.8 泛型、Trait Bound、生命周期的组合使用
这三个特性经常一起使用，下面是一个完整的示例，把三者结合起来：
```rust
use std::fmt::Display;

// 带生命周期标注、泛型、Trait Bound 的函数
fn longest_with_announcement<'a, T>(
    x: &'a str,
    y: &'a str,
    ann: T,
) -> &'a str
where
    T: Display, // T 必须实现 Display Trait
{
    println!("公告：{}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

fn main() {
    let string1 = String::from("Rust 生命周期");
    let string2 = String::from("泛型与 Trait");
    let result = longest_with_announcement(string1.as_str(), string2.as_str(), "开始比较！");
    println!("最长的字符串是：{}", result);
}
```

---

## 第十一章：智能指针（Smart Pointers）
智能指针是 Rust 对所有权模型的核心扩展，它是一类**行为类似指针，同时拥有额外元数据和能力**的结构体。和普通引用最核心的区别是：**绝大多数智能指针拥有其指向的数据的所有权**，而普通引用只是借用数据。

> 对比熟悉的概念：Rust 智能指针类似 C++ 的 `std::unique_ptr`/`std::shared_ptr`，但 Rust 在编译期就保证了内存安全，绝不会出现重复释放、空悬指针、内存泄漏（循环引用除外）等问题。
> 智能指针的核心是两个 Trait：
> 1. `Deref`：重载解引用运算符 `*`，让智能指针可以像普通引用一样使用
> 2. `Drop`：自定义值离开作用域时的清理行为（类似 C++ 析构函数）

### 11.1 核心基础：Deref 与 Drop Trait
#### 11.1.1 Deref Trait：解引用重载
`Deref` Trait 允许你自定义解引用运算符 `*` 的行为，让智能指针可以无缝兼容针对普通引用编写的代码。

**基础示例：自定义智能指针**
```rust
// 定义一个元组结构体，包裹一个 i32 值
struct MyBox<T>(T);

impl<T> MyBox<T> {
    // 构造函数：创建一个 MyBox 实例
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}

// 为 MyBox 实现 Deref Trait
use std::ops::Deref;
impl<T> Deref for MyBox<T> {
    type Target = T; // 定义解引用后的目标类型

    // 重载 * 运算符的行为：返回内部值的引用
    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

fn main() {
    let x = 5;
    let y = MyBox::new(x);

    assert_eq!(5, x);
    // 底层执行：*(y.deref())，先调用 deref 方法，再解引用
    assert_eq!(5, *y); 
}
```

#### 11.1.2 Deref 强制转换（Deref Coercion）
这是 Rust 提供的语法糖：当一个类型实现了 `Deref` Trait 时，Rust 会自动将该类型的引用，转换为其 `Deref` 目标类型的引用。

这就是为什么你可以直接把 `&String` 传给一个接收 `&str` 的函数——因为 `String` 实现了 `Deref<Target=str>`，Rust 自动完成了转换。
```rust
// 接收 &str 类型的函数
fn hello(name: &str) {
    println!("Hello, {}!", name);
}

fn main() {
    let m = MyBox::new(String::from("Rust"));
    // 自动完成三次 Deref 转换：&MyBox<String> → &String → &str
    hello(&m);
    // 如果没有 Deref 强制转换，你需要写成：hello(&(*m)[..]);
}
```

#### 11.1.3 Drop Trait：自定义清理逻辑
`Drop` Trait 允许你自定义值**离开作用域时**的行为，比如释放堆内存、关闭文件、释放网络连接等资源，是 Rust 自动资源管理的核心。
```rust
struct CustomSmartPointer {
    data: String,
}

// 实现 Drop Trait
impl Drop for CustomSmartPointer {
    // 唯一的方法 drop，值离开作用域时自动调用
    fn drop(&mut self) {
        println!("释放资源：{}", self.data);
    }
}

fn main() {
    let c = CustomSmartPointer {
        data: String::from("我的第一个智能指针"),
    };
    let d = CustomSmartPointer {
        data: String::from("我的第二个智能指针"),
    };
    println!("CustomSmartPointer 实例创建完成");
    // 离开作用域时，自动调用 drop 方法，释放顺序和创建顺序相反（先 d 后 c）
}
```

**手动提前释放：`std::mem::drop`**
Rust 不允许你手动调用 `drop` 方法（避免重复释放），但可以通过标准库的 `std::mem::drop` 函数提前释放值：
```rust
fn main() {
    let c = CustomSmartPointer {
        data: String::from("提前释放的指针"),
    };
    println!("实例创建完成，即将手动释放");
    drop(c); // 提前调用 drop 方法，释放资源
    println!("实例已释放");
}
```

---

### 11.2 最基础的智能指针：`Box<T>`
`Box<T>` 是 Rust 最简单的智能指针，也叫**堆分配指针**，它的作用是将值放在堆内存上，栈上只保留一个指向堆数据的固定大小指针。

`Box<T>` 几乎没有任何运行时开销，除了把数据放到堆上之外，没有额外功能，完全遵循 Rust 的所有权规则。

#### 11.2.1 核心使用场景
1. **处理编译时无法确定大小的类型**（最常用）：比如递归类型（链表、树），编译器无法确定其占用的栈空间大小，用 `Box<T>` 包裹后，指针大小是固定的，编译器可以正常编译。
2. **转移大量数据的所有权，避免栈拷贝**：当数据量很大时，转移 `Box<T>` 只需要拷贝栈上的指针，不需要拷贝堆上的大量数据，性能极高。
3. **Trait 对象的载体**：当你只关心值实现了某个 Trait，而不关心具体类型时，用 `Box<dyn Trait>` 存储 Trait 对象（后续章节详细讲解）。

#### 11.2.2 基础用法
```rust
fn main() {
    // 将 i32 值 5 放到堆上，栈上保留 Box 指针
    let b = Box::new(5);
    println!("b = {}", b);
    println!("解引用后的值 = {}", *b);
    // 离开作用域时，Box 自动释放堆上的内存
}
```

#### 11.2.3 经典场景：实现递归类型（Cons 链表）
递归类型的核心问题是：编译器无法确定其大小。比如一个链表节点，包含一个值和下一个节点，无限递归下去，大小是无限的。用 `Box<T>` 包裹下一个节点后，指针大小固定，编译器可以确定节点的大小。
```rust
// 定义 Cons 链表（Lisp 风格的链表）
enum List {
    // Cons 节点：包含一个值和下一个节点的 Box 指针
    Cons(i32, Box<List>),
    // 链表结束标记
    Nil,
}

use List::{Cons, Nil};

fn main() {
    // 构建链表：1 -> 2 -> 3 -> Nil
    let list = Cons(
        1,
        Box::new(Cons(
            2,
            Box::new(Cons(
                3,
                Box::new(Nil)
            ))
        ))
    );
}
```
编译器计算 `List` 枚举的大小：
- `Nil` 变体不占用空间
- `Cons` 变体包含一个 `i32`（4字节）和一个 `Box<List>`（8字节，64位系统），总大小固定为12字节，编译器可以正常编译。

---

### 11.3 引用计数智能指针：`Rc<T>`
Rust 所有权规则默认一个值只能有一个所有者，但在某些场景下（比如单线程内的图结构、共享配置），我们需要多个所有者共享同一个值。`Rc<T>`（Reference Counted，引用计数）就是为这个场景设计的。

`Rc<T>` 的核心原理：
- 内部维护一个引用计数，记录当前有多少个所有者持有这个值
- 每次调用 `Rc::clone()`，计数+1，不会深拷贝数据，仅复制指针，性能极高
- 每个所有者离开作用域时，计数-1
- 当计数变为0时，自动释放值

> 注意：`Rc<T>` 仅适用于**单线程环境**，多线程请使用 `Arc<T>`（原子引用计数）；且 `Rc<T>` 只能提供**不可变访问**，无法直接修改内部值（如需修改，需配合 `RefCell<T>`）。

#### 11.3.1 基础用法
```rust
// 复用上面的 List 枚举，改为 Rc 版本
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use List::{Cons, Nil};
use std::rc::Rc;

fn main() {
    // 创建共享的链表尾部：3 -> Nil
    let tail = Rc::new(Cons(3, Rc::new(Nil)));
    println!("创建 tail 后，引用计数 = {}", Rc::strong_count(&tail)); // 1

    // 链表1：1 -> tail，共享尾部数据
    let list1 = Cons(1, Rc::clone(&tail));
    println!("创建 list1 后，引用计数 = {}", Rc::strong_count(&tail)); // 2

    // 链表2：2 -> tail，共享尾部数据
    let list2 = Cons(2, Rc::clone(&tail));
    println!("创建 list2 后，引用计数 = {}", Rc::strong_count(&tail)); // 3

    // list2 离开作用域，计数-1
    drop(list2);
    println!("销毁 list2 后，引用计数 = {}", Rc::strong_count(&tail)); // 2

    // 离开作用域时，list1 和 tail 依次销毁，计数最终变为0，释放内存
}
```

#### 11.3.2 核心限制
1. **单线程限制**：`Rc<T>` 的引用计数增减不是原子操作，多线程环境下会出现数据竞争，因此 Rust 禁止 `Rc<T>` 跨线程传递。
2. **不可变访问**：`Rc<T>` 只能提供内部值的不可变引用，遵循 Rust 的借用规则，无法直接修改内部值。
3. **循环引用导致内存泄漏**：如果两个 `Rc<T>` 互相引用，它们的引用计数永远不会变为0，导致内存永远无法释放。解决这个问题需要使用 `Weak<T>` 弱引用。

---

### 11.4 内部可变性：`RefCell<T>`
Rust 默认的借用规则是：**同一时间，要么有一个可变引用，要么有多个不可变引用**，且这个规则在**编译期**强制检查，违反直接报错。

但在某些场景下，你需要在**只有不可变引用的情况下，修改内部的值**，这就是**内部可变性（Interior Mutability）**模式。`RefCell<T>` 就是实现这个模式的核心智能指针。

`RefCell<T>` 的核心特点：
- 将借用规则的检查从**编译期**移到了**运行期**，如果违反借用规则，程序会在运行时 panic 并退出
- 仅适用于**单线程环境**，多线程请使用 `Mutex<T>`/`RwLock<T>`
- 可以在不可变的情况下，修改内部包裹的值

#### 11.4.1 核心方法
| 方法                  | 作用                                                         |
| --------------------- | ------------------------------------------------------------ |
| `RefCell::new(value)` | 创建一个新的 `RefCell<T>` 实例，包裹目标值                   |
| `.borrow()`           | 获取内部值的**不可变引用**，返回 `Ref<T>` 智能指针；运行时检查，此时不能再获取可变引用 |
| `.borrow_mut()`       | 获取内部值的**可变引用**，返回 `RefMut<T>` 智能指针；运行时检查，此时不能再获取任何引用 |

#### 11.4.2 基础用法
```rust
use std::cell::RefCell;

fn main() {
    // 创建 RefCell，包裹一个 i32 值
    let num = RefCell::new(5);

    // 注意：num 本身是不可变的，但我们可以修改内部的值
    {
        // 获取可变引用，修改内部值
        let mut mut_num = num.borrow_mut();
        *mut_num += 10;
    } // mut_num 离开作用域，可变引用释放

    // 获取不可变引用，读取值
    println!("修改后的值：{}", num.borrow()); // 输出 15
}
```

#### 11.4.3 经典组合：`Rc<RefCell<T>>`
`Rc<T>` 允许多个所有者，但只能提供不可变访问；`RefCell<T>` 提供内部可变性，但只能有一个所有者。两者结合，就能实现**多个所有者共享同一个可修改的值**，这是 Rust 单线程内最常用的共享可变模式。
```rust
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    // 创建 Rc<RefCell<i32>>，共享可修改的计数器
    let counter = Rc::new(RefCell::new(0));
    println!("初始值：{}", counter.borrow()); // 0

    // 克隆多个所有者，每个都可以修改计数器
    let c1 = Rc::clone(&counter);
    let c2 = Rc::clone(&counter);

    // 所有者1修改值
    *c1.borrow_mut() += 5;
    println!("c1 修改后：{}", counter.borrow()); // 5

    // 所有者2修改值
    *c2.borrow_mut() += 10;
    println!("c2 修改后：{}", counter.borrow()); // 15
}
```

#### 11.4.4 注意事项
1. **运行时 panic 风险**：如果违反借用规则（比如同时获取可变引用和不可变引用），编译器不会报错，但程序运行时会直接 panic。因此你必须自己保证借用规则的正确性。
2. **轻微运行时开销**：`RefCell<T>` 需要维护借用计数，有极轻微的运行时性能损耗。
3. **单线程限制**：`RefCell<T>` 不是线程安全的，不能跨线程使用。

---

### 11.5 解决循环引用：`Weak<T>`
`Rc<T>` 的循环引用是 Rust 中极少数会导致内存泄漏的场景。比如双向链表的两个节点，互相持有对方的 `Rc<T>` 引用，它们的引用计数永远不会变为0，内存永远无法释放。

`Weak<T>`（弱引用）就是为了解决这个问题设计的，它和 `Rc<T>` 配合使用，核心特点：
- 弱引用**不会增加强引用计数**，因此不会阻止值的释放
- 弱引用不拥有值的所有权，访问值时需要调用 `upgrade()` 方法，尝试升级为 `Rc<T>`
  - 如果值还没被释放，返回 `Some(Rc<T>)`
  - 如果值已经被释放，返回 `None`
- 弱引用有独立的弱引用计数，只有当强引用计数和弱引用计数都为0时，值占用的内存才会被完全释放

#### 11.5.1 经典场景：树结构的父节点引用
树结构中，子节点需要引用父节点，同时父节点也引用子节点，如果都用 `Rc<T>` 会导致循环引用。用 `Weak<T>` 存储父节点引用，就可以完美解决这个问题。
```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

// 定义树节点
struct Node {
    value: i32,
    // 子节点：强引用，拥有子节点的所有权
    children: RefCell<Vec<Rc<Node>>>,
    // 父节点：弱引用，不拥有父节点的所有权，避免循环引用
    parent: RefCell<Weak<Node>>,
}

fn main() {
    // 创建根节点
    let root = Rc::new(Node {
        value: 1,
        children: RefCell::new(vec![]),
        parent: RefCell::new(Weak::new()),
    });
    println!("根节点的强引用计数：{}", Rc::strong_count(&root)); // 1

    // 创建子节点
    let child = Rc::new(Node {
        value: 2,
        children: RefCell::new(vec![]),
        // 父节点用 Weak 弱引用，通过 Rc::downgrade 创建
        parent: RefCell::new(Rc::downgrade(&root)),
    });

    // 把子节点添加到根节点的子节点列表
    root.children.borrow_mut().push(Rc::clone(&child));
    println!("添加子节点后，根节点的强引用计数：{}", Rc::strong_count(&root)); // 1（弱引用不增加计数）
    println!("子节点的强引用计数：{}", Rc::strong_count(&child)); // 2

    // 通过子节点的弱引用访问父节点
    let parent = child.parent.borrow().upgrade().unwrap();
    println!("子节点的父节点值：{}", parent.value); // 1

    // 根节点离开作用域，强引用计数变为0，内存释放，子节点的弱引用升级会返回None
}
```

---

### 11.6 其他常用智能指针简介
| 智能指针    | 作用                                                    | 适用场景                                             |
| ----------- | ------------------------------------------------------- | ---------------------------------------------------- |
| `Arc<T>`    | 原子引用计数，多线程版本的 `Rc<T>`，线程安全            | 多线程环境下多个所有者共享同一个值                   |
| `Mutex<T>`  | 互斥锁，多线程版本的 `RefCell<T>`，线程安全的内部可变性 | 多线程环境下共享可变数据，同一时间只允许一个线程访问 |
| `RwLock<T>` | 读写锁，多线程环境下允许多个读线程、一个写线程          | 读多写少的并发场景，性能优于 `Mutex<T>`              |
| `Cow<T>`    | 写时复制（Clone on Write），可以在需要修改时才复制数据  | 读多写少的场景，减少不必要的内存拷贝，提升性能       |

---

## 第十二章：无畏并发（Fearless Concurrency）
并发编程是软件开发中最容易出 bug 的领域之一，而 Rust 的核心亮点之一就是**无畏并发**：它在**编译期就杜绝了数据竞争**，让你写出安全、高效的并发代码，不用再为各种诡异的并发 bug 头疼。

> 前置概念：
> - 并发（Concurrency）：程序的不同部分可以交替执行
> - 并行（Parallelism）：程序的不同部分可以同时在多个核心上执行
> - 数据竞争（Data Race）：两个或多个线程同时访问同一个内存地址，其中至少一个是写操作，且没有同步机制，这是并发 bug 的万恶之源
> Rust 保证：只要代码能编译通过，就绝对不会出现数据竞争。

---

### 12.1 线程的创建与管理
Rust 标准库的 `std::thread` 模块提供了线程创建和管理的基础能力。

#### 12.1.1 创建线程：`thread::spawn`
使用 `thread::spawn` 函数创建新线程，参数是一个闭包，闭包内的代码会在新线程中执行。
```rust
use std::thread;
use std::time::Duration;

fn main() {
    // 创建一个新线程
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("子线程：计数 {}", i);
            // 线程休眠1毫秒，让出CPU
            thread::sleep(Duration::from_millis(1));
        }
    });

    // 主线程执行的代码
    for i in 1..5 {
        println!("主线程：计数 {}", i);
        thread::sleep(Duration::from_millis(1));
    }

    // 等待子线程执行完成，避免主线程提前退出导致子线程被强制终止
    handle.join().unwrap();
    println!("子线程执行完成，程序退出");
}
```
**核心注意点**：
- `thread::spawn` 返回一个 `JoinHandle`，调用它的 `join()` 方法会阻塞当前线程，直到子线程执行完成。
- 如果不调用 `join()`，主线程退出时，所有子线程都会被强制终止，不管有没有执行完。

#### 12.1.2 线程间转移所有权：`move` 闭包
创建线程时，闭包如果要使用主线程的变量，必须通过 `move` 关键字将变量的所有权转移到子线程中。因为 Rust 无法保证子线程的生命周期一定比主线程短，所以不允许子线程借用主线程的变量，必须转移所有权。
```rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    // 必须加 move 关键字，将 v 的所有权转移到子线程闭包中
    let handle = thread::spawn(move || {
        println!("子线程访问向量：{:?}", v);
    });

    // 这里不能再访问 v，因为所有权已经转移到子线程
    // println!("{:?}", v); // 编译报错！

    handle.join().unwrap();
}
```

---

### 12.2 线程间消息传递：通道（Channel）
Rust 并发编程推崇的核心哲学是：**通过通信共享内存，而不是通过共享内存通信**。通道（Channel）就是线程间消息传递的核心机制。

Rust 标准库的 `std::sync::mpsc` 模块实现了通道，`mpsc` 是 **multiple producer, single consumer**（多生产者，单消费者）的缩写，意味着一个通道可以有多个发送端，但只能有一个接收端。

#### 12.2.1 通道的核心用法
```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
    // 创建一个通道，返回 (发送端Sender, 接收端Receiver)
    let (tx, rx) = mpsc::channel();

    // 创建子线程，通过发送端发送消息
    thread::spawn(move || {
        let msg = String::from("Hello from thread!");
        // send 方法会转移消息的所有权
        tx.send(msg).unwrap();
        println!("子线程已发送消息");

        // 这里不能再访问 msg，所有权已经转移
        // println!("{}", msg); // 编译报错！
    });

    // 主线程通过接收端接收消息，recv() 会阻塞线程，直到收到消息或通道关闭
    let received = rx.recv().unwrap();
    println!("主线程收到消息：{}", received);
}
```

#### 12.2.2 多生产者场景：克隆发送端
通道支持克隆多个发送端，实现多个线程向同一个通道发送消息：
```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();

    // 克隆第一个发送端
    let tx1 = tx.clone();
    thread::spawn(move || {
        let msgs = vec!["线程1：消息1", "线程1：消息2", "线程1：消息3"];
        for msg in msgs {
            tx1.send(msg).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    // 克隆第二个发送端
    let tx2 = tx.clone();
    thread::spawn(move || {
        let msgs = vec!["线程2：消息1", "线程2：消息2", "线程2：消息3"];
        for msg in msgs {
            tx2.send(msg).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    // 接收端可以直接作为迭代器使用，遍历所有收到的消息
    for received in rx {
        println!("收到：{}", received);
    }
}
```

---

### 12.3 共享状态并发：互斥锁 `Mutex<T>`
虽然 Rust 推崇消息传递，但也支持共享内存的并发模式。`Mutex<T>`（互斥锁，Mutual Exclusion）是共享状态并发的核心工具，它保证**同一时间只允许一个线程访问内部的数据**，通过锁机制避免数据竞争。

`Mutex<T>` 是线程安全的内部可变性智能指针，配合 `Arc<T>`（多线程版本的 `Rc<T>`），可以实现多个线程共享同一个可修改的值。

#### 12.3.1 核心用法
```rust
use std::sync::{Mutex, Arc};
use std::thread;

fn main() {
    // 创建 Arc<Mutex<i32>>，多线程共享的计数器
    // Arc：多线程安全的引用计数，保证多个线程拥有计数器的所有权
    // Mutex：保证同一时间只有一个线程可以修改计数器
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    // 创建10个线程，每个线程给计数器加1
    for _ in 0..10 {
        // 克隆 Arc，每个线程都拥有一个计数器的引用
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            // 获取锁，lock() 会阻塞线程，直到拿到锁
            // 返回 MutexGuard 智能指针，解引用可以访问内部值
            let mut num = counter.lock().unwrap();
            *num += 1;
        }); // MutexGuard 离开作用域，自动释放锁
        handles.push(handle);
    }

    // 等待所有线程执行完成
    for handle in handles {
        handle.join().unwrap();
    }

    // 输出最终结果
    println!("最终计数：{}", counter.lock().unwrap()); // 输出 10
}
```

#### 12.3.2 注意事项
1. **锁的自动释放**：`MutexGuard` 离开作用域时会自动释放锁，无需手动释放，避免了忘记释放锁导致的死锁问题。
2. **锁中毒（Poisoned）**：如果持有锁的线程 panic 了，锁会进入中毒状态，此时 `lock()` 会返回 `Err`，表示锁内的数据可能已经不一致。
3. **死锁风险**：Rust 只能杜绝数据竞争，无法完全杜绝死锁。如果多个线程互相等待对方释放锁，就会导致死锁，需要你自己保证锁的获取顺序一致。

---

### 12.4 并发安全的核心：`Send` 和 `Sync` Trait
Rust 之所以能在编译期杜绝数据竞争，核心是两个标记 Trait：`Send` 和 `Sync`，它们定义了类型的线程安全特性。

#### 12.4.1 `Send` Trait
实现了 `Send` 的类型，可以**安全地在线程间转移所有权**。
- Rust 中绝大多数类型都是 `Send`，比如 `i32`、`String`、`Vec<T>`、`Box<T>`、`Arc<T>`、`Mutex<T>`
- 例外：`Rc<T>` 不是 `Send`，因为它的引用计数增减不是原子操作，多线程下会出现数据竞争，因此无法跨线程传递。

#### 12.4.2 `Sync` Trait
实现了 `Sync` 的类型，可以**安全地被多个线程同时引用**（也就是 `&T` 是 `Send`）。
- 基础类型（`i32`、`bool`、`f64` 等）都是 `Sync`
- `Arc<T>`、`Mutex<T>`、`RwLock<T>` 是 `Sync`，可以被多个线程同时引用
- `Rc<T>`、`RefCell<T>` 不是 `Sync`，无法在多线程环境下共享引用

#### 12.4.3 核心规则
- 如果一个类型的所有成员都是 `Send`，那么这个类型自动实现 `Send`
- 如果一个类型的所有成员都是 `Sync`，那么这个类型自动实现 `Sync`
- 手动实现 `Send` 和 `Sync` 是 unsafe 操作，因为你需要保证线程安全，绝大多数情况下不需要手动实现。

---

## 第十三章：Trait 对象与动态分发
我们之前学的泛型是**静态分发（Static Dispatch）**，编译器在编译期就确定了调用的具体方法，性能极高，但不够灵活。而 **Trait 对象（Trait Object）** 实现了**动态分发（Dynamic Dispatch）**，在运行时确定调用的方法，提供了极大的灵活性，是 Rust 实现运行时多态的核心机制。

---

### 13.1 静态分发 vs 动态分发
#### 静态分发
泛型的单态化实现：编译器会为每个具体的泛型类型生成对应的专用代码，在编译期就确定了要调用的方法，没有任何运行时开销。
- 优点：零运行时开销，性能极高
- 缺点：代码膨胀（生成多个版本的代码），只能处理同一类型

#### 动态分发
通过 Trait 对象实现：编译器在编译期不知道要调用的具体方法，只有在运行时，通过虚函数表（vtable）查找对应的方法并调用。
- 优点：极度灵活，支持不同的类型，代码体积小
- 缺点：有轻微的运行时开销（vtable 查找），编译期无法做类型优化

---

### 13.2 什么是 Trait 对象
Trait 对象是**指向实现了某个 Trait 的类型的引用**，加上一个**虚函数表（vtable）**，用来在运行时查找对应的方法。

语法格式：
- 引用形式：`&dyn TraitName`
- 智能指针形式：`Box<dyn TraitName>`（最常用）

> 注意：`dyn` 关键字是 Rust 2018 版本引入的，用来显式标记动态分发，必须写，避免和泛型混淆。

#### 13.2.1 核心使用场景
当你需要一个集合，里面存放**不同的类型，但都实现了同一个 Trait**，泛型无法做到（泛型要求集合内的元素是同一类型），而 Trait 对象可以完美实现。

最经典的例子：GUI 框架的控件绘制
```rust
// 定义绘制 Trait
pub trait Draw {
    fn draw(&self);
}

// 按钮控件，实现 Draw Trait
pub struct Button {
    pub width: u32,
    pub height: u32,
    pub label: String,
}

impl Draw for Button {
    fn draw(&self) {
        println!("绘制按钮：{} ({}x{})", self.label, self.width, self.height);
    }
}

// 文本框控件，实现 Draw Trait
pub struct TextField {
    pub width: u32,
    pub height: u32,
    pub placeholder: String,
}

impl Draw for TextField {
    fn draw(&self) {
        println!("绘制文本框：占位符 '{}' ({}x{})", self.placeholder, self.width, self.height);
    }
}

// 复选框控件，实现 Draw Trait
pub struct CheckBox {
    pub width: u32,
    pub height: u32,
    pub label: String,
    pub checked: bool,
}

impl Draw for CheckBox {
    fn draw(&self) {
        let status = if self.checked { "已选中" } else { "未选中" };
        println!("绘制复选框：{} [{}] ({}x{})", self.label, status, self.width, self.height);
    }
}

// 屏幕结构体，存放所有控件
pub struct Screen {
    // 核心：Vec 里存放 Box<dyn Draw>，也就是不同类型的 Trait 对象
    pub components: Vec<Box<dyn Draw>>,
}

impl Screen {
    // 遍历所有控件，调用 draw 方法
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();
        }
    }
}

fn main() {
    // 创建屏幕，添加不同类型的控件
    let screen = Screen {
        components: vec![
            Box::new(Button {
                width: 100,
                height: 50,
                label: String::from("提交"),
            }),
            Box::new(TextField {
                width: 200,
                height: 30,
                placeholder: String::from("请输入用户名"),
            }),
            Box::new(CheckBox {
                width: 20,
                height: 20,
                label: String::from("记住密码"),
                checked: true,
            }),
        ],
    };

    // 运行，绘制所有控件
    screen.run();
}
```
在这个例子中，`Screen` 的 `components` 列表可以存放任何实现了 `Draw` Trait 的控件，不管它的具体类型是什么，运行时会自动调用对应类型的 `draw` 方法，实现了真正的多态。

---

### 13.3 Trait 对象的对象安全规则
不是所有的 Trait 都能作为 Trait 对象，必须满足**对象安全（Object Safe）**规则，核心有两条：
1. **方法的返回类型不能是 `Self`**
2. **方法不能有泛型参数**

#### 为什么有这个规则？
- 动态分发时，编译器不知道具体的 `Self` 类型是什么，无法确定返回值的大小和类型。
- 泛型参数会在编译期单态化，而动态分发是运行时行为，无法处理泛型参数。

**反例**：`Clone` Trait 就不是对象安全的，因为它的 `clone()` 方法返回 `Self`，所以你不能创建 `Box<dyn Clone>`。
```rust
// 编译报错！Clone 不是对象安全的 Trait
let x: Box<dyn Clone> = Box::new(String::from("test"));
```

---

## 第十四章：Cargo 进阶与 Rust 生态
Cargo 是 Rust 的包管理器和构建工具，除了基础的 `cargo new`、`cargo run`、`cargo build` 之外，它还有很多强大的功能，是 Rust 工程化开发的核心。

---

### 14.1 `Cargo.toml` 配置详解
`Cargo.toml` 是 Rust 项目的配置文件，类似前端的 `package.json`，核心分为几个部分：

#### 14.1.1 项目元数据 `[package]`
```toml
[package]
# 项目名称，发布到 crates.io 时的唯一标识
name = "my_rust_project"
# 版本号，遵循语义化版本（MAJOR.MINOR.PATCH）
version = "0.1.0"
# 项目作者
authors = ["Your Name <your.email@example.com>"]
# 项目描述
description = "一个 Rust 示例项目"
# 开源协议
license = "MIT"
# Rust 版本，指定最低支持的 Rust 版本
edition = "2021"
```

#### 14.1.2 依赖配置 `[dependencies]`
这里声明项目的依赖，Cargo 会自动从 crates.io 下载并构建。
```toml
[dependencies]
# 最简写法：指定版本号，默认使用 ^ 兼容规则
serde = "1.0"
# 完整写法：指定版本、特性
# serde 是 Rust 最常用的序列化/反序列化库，derive 特性开启派生宏
serde = { version = "1.0", features = ["derive"] }
# 引入 git 仓库的依赖
rand = { git = "https://github.com/rust-random/rand", branch = "master" }
# 引入本地路径的依赖
my_utils = { path = "../my_utils" }
```

**版本号规则**：
- `^1.0`：兼容更新，允许 1.0.0 <= 版本 < 2.0.0（默认规则）
- `~1.0.0`：补丁更新，允许 1.0.0 <= 版本 < 1.1.0
- `1.0.0`：固定版本，只能使用 1.0.0
- `*`：任意版本

#### 14.1.3 其他依赖段
- `[dev-dependencies]`：开发依赖，仅用于测试、示例等开发场景，不会被打包到发布的二进制文件中。
- `[build-dependencies]`：构建脚本依赖，用于 `build.rs` 构建脚本。

---

### 14.2 工作空间（Workspace）
当你的项目变得复杂，包含多个 crate（比如一个主二进制 crate、多个库 crate、多个工具 crate）时，Cargo 工作空间可以帮你统一管理所有 crate 的依赖，避免重复下载和构建，保证所有 crate 使用的依赖版本一致。

#### 工作空间的目录结构
```
my_workspace/
├── Cargo.toml       # 工作空间根配置文件
├── Cargo.lock       # 工作空间统一的锁文件
├── main_app/        # 主二进制 crate
│   ├── Cargo.toml
│   └── src/main.rs
├── utils/           # 工具库 crate
│   ├── Cargo.toml
│   └── src/lib.rs
└── config/          # 配置库 crate
    ├── Cargo.toml
    └── src/lib.rs
```

#### 工作空间根配置 `Cargo.toml`
```toml
[workspace]
# 声明工作空间包含的成员
members = [
    "main_app",
    "utils",
    "config",
]
# 统一管理所有成员的依赖版本，避免版本冲突
[workspace.dependencies]
serde = { version = "1.0", features = ["derive"] }
anyhow = "1.0"
thiserror = "1.0"
```

#### 子 crate 的依赖配置
子 crate 可以直接引用工作空间的统一依赖，无需重复指定版本：
```toml
# main_app/Cargo.toml
[package]
name = "main_app"
version = "0.1.0"
edition = "2021"

[dependencies]
# 引用工作空间的统一依赖
serde.workspace = true
anyhow.workspace = true
# 引用工作空间内的本地库 crate
utils = { path = "../utils" }
config = { path = "../config" }
```

---

### 14.3 常用 Cargo 命令
| 命令               | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| `cargo check`      | 快速检查代码是否能编译通过，不生成可执行文件，比 `cargo build` 快很多，开发时高频使用 |
| `cargo test`       | 运行项目的所有测试用例                                       |
| `cargo doc --open` | 生成项目和所有依赖的文档，并在浏览器中打开                   |
| `cargo publish`    | 将你的库 crate 发布到 crates.io                              |
| `cargo update`     | 更新所有依赖到最新的兼容版本                                 |
| `cargo clean`      | 清理构建产物，释放磁盘空间                                   |
| `cargo fmt`        | 自动格式化代码，遵循 Rust 官方风格规范                       |
| `cargo clippy`     | 代码 lint 工具，检查代码中的常见错误、不良风格，给出优化建议 |

---

### 14.4 Rust 核心生态与常用 crate
Rust 生态已经非常成熟，覆盖了绝大多数开发场景，这里列出最常用、最稳定的核心 crate：

| 分类        | crate       | 作用                                                         |
| ----------- | ----------- | ------------------------------------------------------------ |
| 错误处理    | `anyhow`    | 快速开发场景的通用错误处理，简化错误传递                     |
| 错误处理    | `thiserror` | 库开发场景的自定义错误类型，派生宏生成标准错误实现           |
| 序列化      | `serde`     | Rust 事实标准的序列化/反序列化库，支持 JSON、YAML、TOML 等几乎所有格式 |
| 异步运行时  | `tokio`     | Rust 最主流的异步运行时，支持多线程、网络 IO、定时器等，是 Rust 异步开发的事实标准 |
| HTTP 客户端 | `reqwest`   | 基于 tokio 的异步 HTTP 客户端，支持同步/异步模式，功能全面   |
| Web 框架    | `axum`      | 基于 tokio 的高性能、易用的 Web 框架，类型安全，生态完善     |
| Web 框架    | `actix-web` | 高性能、功能全面的 Web 框架，支持异步，稳定成熟              |
| 命令行开发  | `clap`      | Rust 事实标准的命令行参数解析库，派生宏功能强大，自动生成帮助文档 |
| 日志        | `tracing`   | 结构化日志库，支持异步、链路追踪，适合复杂应用开发           |
| 数据库      | `sqlx`      | 异步 SQL 工具库，编译时检查 SQL 语句，支持 PostgreSQL、MySQL、SQLite 等 |
| 配置管理    | `config`    | 支持多种格式的配置管理库，支持环境变量、配置文件分层合并     |
| 随机数      | `rand`      | Rust 标准的随机数生成库                                      |

---

## 实战项目 - 命令行待办工具 `todo-rs`
我们将实现一个生产级可用的命令行待办管理工具，覆盖Rust核心语法、工程化规范、第三方库使用、文件IO、错误处理等所有核心知识点，最终成品可直接编译使用。

### 项目核心功能
1. 添加待办事项（支持内容、优先级）
2. 列出所有待办（区分已完成/未完成，按创建时间排序）
3. 标记待办为已完成
4. 删除待办事项
5. 本地文件持久化（JSON格式，重启不丢失数据）
6. 自动生成命令行帮助文档

### 步骤1：创建项目与依赖配置
#### 1.1 初始化项目
```bash
cargo new todo-rs
cd todo-rs
```

#### 1.2 配置 `Cargo.toml` 依赖
我们将使用Rust生态的标准库来实现功能，每个依赖的作用都标注清楚，对应你之前学的知识点：
```toml
[package]
name = "todo-rs"
version = "0.1.0"
edition = "2021"
description = "Rust 实战：命令行待办管理工具"
license = "MIT"

[dependencies]
# 命令行参数解析：Rust事实标准，用派生宏自动生成参数解析和帮助文档
clap = { version = "4.5", features = ["derive"] }
# 序列化/反序列化：将待办数据转为JSON持久化，对应泛型、Trait知识点
serde = { version = "1.0", features = ["derive"] }
# JSON格式处理：配合serde实现数据持久化
serde_json = "1.0"
# 通用错误处理：简化错误传递，对应错误处理章节知识点
anyhow = "1.0"
# 时间处理：记录待办的创建/完成时间
chrono = { version = "0.4", features = ["serde", "local"] }
```

### 步骤2：定义核心数据结构
创建 `src/main.rs`，先定义项目的核心数据模型，这里会用到结构体、枚举、Trait派生、生命周期等知识点。
```rust
// 引入依赖
use anyhow::{Result, Context};
use chrono::{Local, DateTime};
use clap::Parser;
use serde::{Serialize, Deserialize};
use std::path::PathBuf;
use std::fs;

// ====================== 核心数据结构定义 ======================
/// 单个待办事项的结构体
#[derive(Debug, Clone, Serialize, Deserialize)] // serde派生实现序列化/反序列化
pub struct Todo {
    /// 待办唯一ID，自增
    pub id: u64,
    /// 待办内容
    pub content: String,
    /// 是否完成
    pub is_completed: bool,
    /// 创建时间
    pub created_at: DateTime<Local>,
    /// 完成时间（可选）
    pub completed_at: Option<DateTime<Local>>,
}

/// 待办列表管理器，核心业务逻辑的载体
#[derive(Debug, Serialize, Deserialize)]
pub struct TodoList {
    /// 所有待办事项
    pub todos: Vec<Todo>,
    /// 下一个待办的自增ID
    next_id: u64,
    /// 数据持久化的文件路径
    #[serde(skip)] // 序列化时忽略这个字段，不存入JSON
    file_path: PathBuf,
}
```

### 步骤3：定义命令行参数（CLI）
用`clap`定义命令行的子命令，对应增删改查功能，用到枚举、Trait派生知识点：
```rust
// ====================== 命令行参数定义 ======================
/// Rust 命令行待办管理工具
#[derive(Debug, Parser)] // clap派生实现参数自动解析
#[command(author, version, about, long_about = None)]
#[command(propagate_version = true)]
pub struct Cli {
    /// 子命令：增删改查
    #[command(subcommand)]
    pub command: Commands,
}

/// 支持的子命令枚举
#[derive(Debug, clap::Subcommand)]
pub enum Commands {
    /// 添加一个新的待办事项
    Add {
        /// 待办事项的内容
        content: String,
    },
    /// 列出所有待办事项
    List {
        /// 仅显示未完成的待办
        #[arg(short, long)]
        unfinished: bool,
    },
    /// 标记指定ID的待办为已完成
    Complete {
        /// 待办事项的ID
        id: u64,
    },
    /// 删除指定ID的待办事项
    Delete {
        /// 待办事项的ID
        id: u64,
    },
}
```

### 步骤4：实现核心业务逻辑
为`TodoList`实现核心方法，覆盖文件IO、错误处理、所有权、借用、模式匹配等知识点，每一步都有详细注释：
```rust
// ====================== 核心业务逻辑实现 ======================
impl TodoList {
    /// 初始化待办列表：从本地文件加载，文件不存在则创建新的
    pub fn init() -> Result<Self> {
        // 确定数据文件路径：用户主目录下的 .todo_rs.json
        let file_path = dirs::home_dir()
            .context("无法获取用户主目录")? // 错误处理：给错误添加上下文
            .join(".todo_rs.json");

        // 如果文件存在，加载数据；不存在则创建空的待办列表
        let mut todo_list = if file_path.exists() {
            let content = fs::read_to_string(&file_path)
                .context("读取待办数据文件失败")?;
            serde_json::from_str::<TodoList>(&content)
                .context("解析待办数据失败")?
        } else {
            TodoList {
                todos: Vec::new(),
                next_id: 1,
                file_path: file_path.clone(),
            }
        };

        // 把文件路径赋值给结构体（反序列化时跳过了这个字段）
        todo_list.file_path = file_path;
        Ok(todo_list)
    }

    /// 保存待办列表到本地文件
    pub fn save(&self) -> Result<()> {
        let content = serde_json::to_string_pretty(self)
            .context("序列化待办数据失败")?;
        fs::write(&self.file_path, content)
            .context("写入待办数据文件失败")?;
        Ok(())
    }

    /// 添加新的待办事项
    pub fn add(&mut self, content: String) -> Result<()> {
        let todo = Todo {
            id: self.next_id,
            content,
            is_completed: false,
            created_at: Local::now(),
            completed_at: None,
        };

        self.todos.push(todo);
        self.next_id += 1;
        self.save()?; // 添加后自动保存
        println!("✅ 待办添加成功！ID: {}", self.next_id - 1);
        Ok(())
    }

    /// 标记指定ID的待办为已完成
    pub fn complete(&mut self, id: u64) -> Result<()> {
        // 查找对应ID的待办，模式匹配
        let todo = self.todos.iter_mut()
            .find(|t| t.id == id)
            .with_context(|| format!("未找到ID为 {} 的待办事项", id))?;

        if todo.is_completed {
            println!("⚠️  该待办已经完成了");
            return Ok(());
        }

        // 修改状态，记录完成时间
        todo.is_completed = true;
        todo.completed_at = Some(Local::now());
        self.save()?;
        println!("✅ 待办 ID: {} 已标记为完成！", id);
        Ok(())
    }

    /// 删除指定ID的待办事项
    pub fn delete(&mut self, id: u64) -> Result<()> {
        // 检查待办是否存在
        let exists = self.todos.iter().any(|t| t.id == id);
        if !exists {
            anyhow::bail!("未找到ID为 {} 的待办事项", id); // 直接抛出错误
        }

        // 保留ID不匹配的待办，实现删除
        self.todos.retain(|t| t.id != id);
        self.save()?;
        println!("✅ 待办 ID: {} 已删除！", id);
        Ok(())
    }

    /// 列出所有待办事项
    pub fn list(&self, only_unfinished: bool) -> Result<()> {
        if self.todos.is_empty() {
            println!("📭 暂无待办事项，快去添加吧！");
            return Ok(());
        }

        // 过滤待办：如果only_unfinished为true，只显示未完成的
        let todos_to_show: Vec<&Todo> = self.todos.iter()
            .filter(|t| !only_unfinished || !t.is_completed)
            .collect();

        if todos_to_show.is_empty() {
            println!("🎉 所有待办都已完成！");
            return Ok(());
        }

        // 打印待办列表
        println!("\n📋 你的待办列表：");
        println!("{}", "=".repeat(80));
        for todo in todos_to_show {
            let status = if todo.is_completed { "✅ 已完成" } else { "🔲 未完成" };
            let created = todo.created_at.format("%Y-%m-%d %H:%M");
            let completed_info = todo.completed_at
                .map(|t| format!(" | 完成时间: {}", t.format("%Y-%m-%d %H:%M")))
                .unwrap_or_default();

            println!(
                "ID: {:<4} | {} | 内容: {}\n创建时间: {}{}\n",
                todo.id,
                status,
                todo.content,
                created,
                completed_info
            );
        }
        println!("{}", "=".repeat(80));
        Ok(())
    }
}
```

### 步骤5：实现主函数，串联整个流程
主函数负责解析命令行参数，分发到对应的业务逻辑，处理全局错误：
```rust
// ====================== 程序入口 ======================
fn main() -> Result<()> {
    // 1. 解析命令行参数
    let cli = Cli::parse();

    // 2. 初始化待办列表
    let mut todo_list = TodoList::init()?;

    // 3. 匹配子命令，分发业务逻辑
    match cli.command {
        Commands::Add { content } => {
            todo_list.add(content)?;
        }
        Commands::List { unfinished } => {
            todo_list.list(unfinished)?;
        }
        Commands::Complete { id } => {
            todo_list.complete(id)?;
        }
        Commands::Delete { id } => {
            todo_list.delete(id)?;
        }
    }

    Ok(())
}
```

### 步骤6：编译运行与使用示例
#### 1. 编译项目
```bash
cargo build --release
```
编译完成后，可执行文件在 `target/release/todo-rs`（Windows为`todo-rs.exe`），可以直接放到系统PATH中全局使用。

#### 2. 核心使用命令
```bash
# 查看帮助文档（自动生成）
cargo run -- --help

# 添加待办
cargo run -- add "学习Rust异步编程"
cargo run -- add "完成Rust实战项目"

# 列出所有待办
cargo run -- list

# 仅列出未完成的待办
cargo run -- list --unfinished

# 标记ID为1的待办为已完成
cargo run -- complete 1

# 删除ID为2的待办
cargo run -- delete 2
```

#### 3. 数据持久化
所有数据会保存在你用户主目录下的 `.todo_rs.json` 文件中，重启程序、重启电脑都不会丢失，你可以直接打开查看JSON格式的待办数据。

---

## Rust 异步编程
异步编程是Rust高并发、高性能开发的核心，尤其适合IO密集型场景（网络请求、数据库操作、文件IO、Web服务等）。我们会从底层原理到工程实战，完全贴合你已掌握的所有权、Trait、生命周期、并发等基础，彻底搞懂Rust异步。

### 一、异步编程基础：核心概念与适用场景
#### 1. 同步 vs 异步 vs 多线程
先通过你熟悉的概念，搞清楚三者的核心区别：
| 模式       | 核心逻辑                                                     | 适用场景                                            | 优缺点                                                       |
| ---------- | ------------------------------------------------------------ | --------------------------------------------------- | ------------------------------------------------------------ |
| 同步编程   | 代码按顺序执行，遇到IO操作会**阻塞当前线程**，直到IO完成     | 计算密集型、简单逻辑                                | 优点：逻辑简单、易调试；缺点：IO密集场景下线程利用率极低，性能差 |
| 多线程编程 | 把阻塞的任务放到新线程执行，避免阻塞主线程                   | CPU密集型、少量并发任务                             | 优点：能利用多核CPU；缺点：线程创建/切换开销大，大量并发时资源耗尽，容易出现数据竞争 |
| 异步编程   | 遇到IO操作时，**不阻塞线程**，而是让出CPU去执行其他任务，IO完成后再回来继续执行 | IO密集型、高并发场景（Web服务、爬虫、数据库代理等） | 优点：线程利用率极高，极低的开销支持十万/百万级并发；缺点：有一定学习成本，有运行时约束 |

**举个通俗的例子**：
- 同步：你去餐厅吃饭，排队点餐，必须等前一个人点完、拿到餐，你才能点餐，全程堵死。
- 多线程：开10个点餐窗口，同时处理10个人，但是租窗口成本高，人多了还是要排队。
- 异步：1个服务员，你点完餐就去旁边等，服务员立刻给下一个人点餐，餐做好了再叫你取餐，1个服务员就能处理几百人的点餐，成本极低。

#### 2. Rust异步的核心设计哲学
Rust的异步和其他语言（JavaScript/Java/Go）有本质区别：
1. **零成本抽象**：异步代码的性能和手写的同步回调代码一致，没有额外的运行时开销。
2. **惰性执行**：Rust的`Future`（异步任务）只有被`await`时才会执行，不`await`的话，任务永远不会运行。
3. **无内置运行时**：Rust标准库只定义了异步的核心接口（`Future` Trait），没有提供异步运行时，由社区提供生产级运行时（最主流的是`tokio`）。
4. **编译期安全**：和同步代码一样，异步代码也会在编译期检查所有权、生命周期、数据竞争，杜绝并发bug。

---

### 二、Rust异步的基石：`Future` Trait
`Future` Trait是Rust异步的核心，所有异步任务、`async/await`语法都是基于它实现的，对应你之前学的Trait知识点。

#### 1. 标准库的`Future`定义
```rust
use std::pin::Pin;
use std::task::{Context, Poll};

/// 一个异步任务，代表一个未来会完成的操作
pub trait Future {
    /// 任务完成后输出的结果类型
    type Output;

    /// 检查任务是否完成，核心方法
    /// - Pin<&mut Self>：固定Future的内存地址，防止移动（后续讲解）
    /// - cx: &mut Context：任务上下文，包含Waker，用来通知运行时任务可以继续执行
    /// - 返回值Poll：任务的状态
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}

/// 任务的执行状态
pub enum Poll<T> {
    /// 任务已完成，包含返回结果
    Ready(T),
    /// 任务还未完成，需要等待
    Pending,
}
```

#### 2. 核心概念详解
##### （1）`poll` 方法
异步任务的执行本质，就是运行时不断调用`poll`方法，检查任务状态：
- 如果返回`Poll::Ready(result)`：任务完成，返回结果。
- 如果返回`Poll::Pending`：任务还没完成，运行时会把任务放到一边，等收到通知后再调用`poll`。

##### （2）`Waker` 机制
`Waker`是`Context`中的核心，解决了一个关键问题：运行时怎么知道任务可以继续执行了？
- 当任务返回`Pending`时，会把`Waker`保存下来。
- 当IO完成、任务可以继续执行时，调用`Waker.wake()`，通知运行时：“这个任务可以再次调用`poll`了”。
- 避免了运行时轮询所有任务，极大提升了性能。

##### （3）`Pin` 固定内存
`Pin`是用来固定`Future`的内存地址，防止它被移动。为什么需要？
- 异步任务中，`await`会产生跨调用的引用，比如你在`async fn`中创建一个变量，然后`await`时引用了这个变量。
- 如果`Future`被移动，这个引用的地址就会失效，导致悬垂引用。
- `Pin`会禁止`Future`被移动，保证跨`await`的引用始终有效，和你之前学的生命周期、内存安全知识点完全契合。

---

### 三、`async/await` 语法：异步编程的核心语法糖
`async/await`是Rust为了简化异步代码提供的语法糖，让你可以像写同步代码一样写异步代码，不用手动实现`Future` Trait。

#### 1. `async` 关键字
`async`可以修饰函数、代码块，它的核心作用是：**把代码包装成一个实现了`Future` Trait的类型**。
```rust
// async 函数：返回值是一个 impl Future<Output = String>
async fn hello_async() -> String {
    println!("Hello, async Rust!");
    "异步任务执行完成".to_string()
}
```
**关键注意点**：
- 调用`hello_async()`不会执行里面的代码，只会返回一个`Future`对象，任务是惰性的。
- 只有对这个`Future`调用`await`，才会执行里面的代码。

#### 2. `await` 关键字
`await`只能在`async`函数/代码块中使用，它的核心作用是：**暂停当前异步任务的执行，等待Future完成，然后获取返回结果**。
- 等待时，不会阻塞当前线程，而是让出CPU，让运行时执行其他异步任务。
- Future完成后，自动回到暂停的位置，继续执行后续代码。

#### 3. 最小可运行的异步示例
我们用最主流的异步运行时`tokio`，写一个完整的异步示例：
##### 第一步：添加依赖
```toml
# Cargo.toml
[dependencies]
tokio = { version = "1.0", features = ["full"] } # 引入tokio运行时
```

##### 第二步：编写异步代码
```rust
// 用tokio的宏标记主函数为异步入口
#[tokio::main]
async fn main() {
    println!("主任务开始执行");

    // 调用async函数，得到Future
    let future = hello_async();
    println!("Future已创建，还未执行");

    // await 执行Future，等待完成
    let result = future.await;
    println!("Future执行结果：{}", result);

    // 并发执行两个异步任务
    println!("\n===== 并发执行异步任务 =====");
    let task1 = async_task(1);
    let task2 = async_task(2);

    // 并发等待两个任务完成
    tokio::join!(task1, task2);
    println!("所有任务执行完成");
}

async fn hello_async() -> String {
    println!("异步任务正在执行");
    // 模拟IO等待：异步休眠1秒，不会阻塞线程
    tokio::time::sleep(tokio::time::Duration::from_secs(1)).await;
    "Hello, async Rust!".to_string()
}

async fn async_task(id: u32) {
    println!("任务 {} 开始执行", id);
    tokio::time::sleep(tokio::time::Duration::from_secs(1)).await;
    println!("任务 {} 执行完成", id);
}
```

##### 运行结果
```
主任务开始执行
Future已创建，还未执行
异步任务正在执行
Future执行结果：Hello, async Rust!

===== 并发执行异步任务 =====
任务 1 开始执行
任务 2 开始执行
任务 1 执行完成
任务 2 执行完成
所有任务执行完成
```
你会发现，两个任务同时执行，总共只花了1秒，而不是2秒，这就是异步并发的核心优势。

---

### 四、异步运行时：Tokio 深度讲解
Rust标准库没有提供异步运行时，`tokio`是Rust生态最主流、最稳定的生产级异步运行时，几乎所有Rust异步项目都基于它开发。

#### 1. Tokio 的核心组件
| 组件         | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| 多线程调度器 | 管理异步任务的调度，在多个线程上高效执行任务，默认使用和CPU核心数一致的工作线程 |
| IO驱动       | 封装了操作系统的异步IO接口（epoll/kqueue/IOCP），处理网络、文件等异步IO事件 |
| 异步同步原语 | 提供了异步版本的`Mutex`、`RwLock`、`Semaphore`、`channel`，适配异步场景 |
| 定时器       | 异步的`sleep`、超时、定时任务，不会阻塞线程                  |
| 任务管理     | 提供`spawn`创建异步任务，`JoinHandle`管理任务生命周期、取消任务 |

#### 2. Tokio 的两种运行时模式
- **多线程运行时**：默认模式，多线程调度，能利用多核CPU，适合高并发Web服务、后端服务。
  ```rust
  #[tokio::main] // 等价于多线程运行时
  async fn main() {}
  ```
- **单线程运行时**：所有任务都在一个线程执行，没有线程切换开销，适合GUI、嵌入式、低并发场景。
  ```rust
  #[tokio::main(flavor = "current_thread")]
  async fn main() {}
  ```

#### 3. Tokio 核心用法
##### （1）创建异步任务：`tokio::spawn`
`spawn`会创建一个独立的异步任务，交给运行时调度执行，返回`JoinHandle`，可以等待任务完成、获取返回结果。
```rust
#[tokio::main]
async fn main() {
    //  spawn 创建一个独立的异步任务
    let handle = tokio::spawn(async {
        println!("子任务开始执行");
        tokio::time::sleep(tokio::time::Duration::from_secs(1)).await;
        println!("子任务执行完成");
        42 // 返回结果
    });

    println!("主任务继续执行");

    // 等待子任务完成，获取结果
    let result = handle.await.unwrap();
    println!("子任务返回结果：{}", result);
}
```
**关键注意点**：`spawn`创建的任务要求实现`'static`生命周期，因为任务的生命周期可能超过创建它的函数，不能借用局部变量，必须转移所有权。

##### （2）异步通道：`tokio::sync::mpsc`
和之前学的同步通道类似，异步通道用于异步任务之间的消息传递，不会阻塞线程，只会让出CPU。
```rust
use tokio::sync::mpsc;

#[tokio::main]
async fn main() {
    // 创建异步通道，缓冲区大小为32
    let (tx, mut rx) = mpsc::channel(32);

    //  spawn 发送任务
    tokio::spawn(async move {
        for i in 0..5 {
            tx.send(i).await.unwrap(); // 异步发送，通道满了会等待
            println!("发送了：{}", i);
            tokio::time::sleep(tokio::time::Duration::from_millis(500)).await;
        }
    });

    // 接收消息
    while let Some(msg) = rx.recv().await { // 异步接收，通道空了会等待
        println!("收到了：{}", msg);
    }
}
```

##### （3）异步互斥锁：`tokio::sync::Mutex`
异步版本的`Mutex`，和同步`Mutex`的核心区别是：锁等待时不会阻塞线程，只会让出CPU，适合异步场景。
```rust
use tokio::sync::Mutex;
use std::sync::Arc;

#[tokio::main]
async fn main() {
    // 多任务共享的计数器
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    // 启动10个异步任务，每个给计数器加1
    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = tokio::spawn(async move {
            let mut num = counter.lock().await; // 异步加锁，等待时让出CPU
            *num += 1;
        });
        handles.push(handle);
    }

    // 等待所有任务完成
    for handle in handles {
        handle.await.unwrap();
    }

    println!("最终计数：{}", counter.lock().await); // 输出 10
}
```

---

### 五、异步编程实战：异步HTTP爬虫
我们用`tokio`+`reqwest`（异步HTTP客户端）实现一个并发爬虫，爬取多个网站的标题，直观感受异步高并发的优势。

#### 第一步：添加依赖
```toml
# Cargo.toml
[dependencies]
tokio = { version = "1.0", features = ["full"] }
reqwest = { version = "0.12", features = ["json"] }
scraper = "0.19" # HTML解析，提取标题
anyhow = "1.0"
```

#### 第二步：编写异步爬虫代码
```rust
use anyhow::Result;
use scraper::{Html, Selector};
use std::time::Instant;

/// 异步爬取单个网站的标题
async fn fetch_title(url: &str) -> Result<String> {
    // 异步发送HTTP GET请求，不会阻塞线程
    let response = reqwest::get(url).await?;
    // 异步读取响应体
    let html = response.text().await?;
    // 解析HTML，提取标题
    let document = Html::parse_document(&html);
    let selector = Selector::parse("title").unwrap();
    
    let title = document.select(&selector)
        .next()
        .map(|e| e.text().collect::<String>())
        .unwrap_or_else(|| "无标题".to_string());
    
    Ok(title)
}

#[tokio::main]
async fn main() -> Result<()> {
    // 要爬取的网站列表
    let urls = vec![
        "https://www.rust-lang.org",
        "https://github.com",
        "https://crates.io",
        "https://docs.rs",
        "https://tokio.rs",
    ];

    let start_time = Instant::now();
    let mut handles = vec![];

    // 为每个网站创建一个异步任务，并发爬取
    for url in urls {
        let handle = tokio::spawn(async move {
            match fetch_title(url).await {
                Ok(title) => println!("✅ {} -> {}", url, title),
                Err(e) => println!("❌ {} 爬取失败：{}", url, e),
            }
        });
        handles.push(handle);
    }

    // 等待所有爬取任务完成
    for handle in handles {
        handle.await?;
    }

    // 统计耗时
    println!("\n所有任务完成，总耗时：{:?}", start_time.elapsed());
    Ok(())
}
```

#### 运行结果
你会发现，5个网站的爬取几乎同时完成，总耗时只等于最慢的那个网站的响应时间，而不是5个网站的响应时间之和，这就是异步高并发的核心价值。

---

### 六、异步编程进阶：核心坑点与最佳实践
#### 1. 最常见的坑：在async fn中执行阻塞代码
**错误示例**：在异步函数中用同步的`sleep`，会阻塞整个线程，导致所有任务都被卡住：
```rust
// 错误！同步sleep会阻塞线程，破坏异步运行时
async fn bad_task() {
    std::thread::sleep(std::time::Duration::from_secs(1));
}
```
**正确做法**：
- 用异步的`sleep`：`tokio::time::sleep(...).await`
- 如果必须执行阻塞代码（比如CPU密集型计算、同步IO），用`tokio::task::spawn_blocking`，把阻塞代码放到专门的阻塞线程池中执行，不影响异步调度线程。

#### 2. 跨await的借用问题
异步任务中，跨`await`的引用必须满足`'static`生命周期，或者在整个`Future`的生命周期内都有效。
**错误示例**：
```rust
// 编译报错！引用的生命周期不够长
async fn bad_borrow() {
    let s = String::from("hello");
    let ref_s = &s;
    tokio::spawn(async move { // spawn要求'static生命周期
        println!("{}", ref_s);
    }).await.unwrap();
}
```
**正确做法**：转移所有权，而不是借用；或者把引用的生命周期延长到`'static`。

#### 3. 最佳实践
1. **不要滥用spawn**：只有真正需要独立并发执行的任务才用`spawn`，简单的异步调用直接`await`即可。
2. **优先使用异步版本的库**：文件IO、网络、数据库、加密等操作，都用异步版本的库，不要在异步代码中混用同步IO。
3. **控制锁的粒度**：异步锁的持有时间要尽可能短，不要跨`await`持有锁，避免死锁和性能问题。
4. **合理设置运行时**：CPU密集型任务用多线程运行时，IO密集型单线程也能有很好的性能。
5. **做好错误处理**：异步任务的panic不会导致整个程序崩溃，只会让任务终止，要做好错误捕获和处理。

---

## Axum 异步 Web 后端实战
Axum 是 Tokio 官方团队维护的 Rust Web 框架，是目前 Rust 生态的主流后端框架，核心优势：
- 与 Tokio 异步生态无缝兼容，极致性能
- 类型安全的设计，编译期杜绝绝大多数接口错误
- 无宏魔法，基于 Rust 原生 Trait 实现，灵活易扩展
- 完善的中间件、路由、提取器体系，适配生产级开发
- 与 Tower 生态完全兼容，可复用海量现成的中间件组件

我们将实现一个**博客系统后端 API**，覆盖生产级后端必备的所有核心能力：
1. 分层架构设计（路由层、Handler 层、服务层、数据层、模型层）
2. SQLite 数据库集成（sqlx 编译时 SQL 校验、自动迁移）
3. 用户注册/登录 + JWT 身份认证
4. 文章的 RESTful 全量 CRUD 接口
5. 统一错误处理、参数校验
6. 日志、跨域 CORS、认证中间件
7. 完整的接口测试方案

### 步骤1：项目初始化与依赖配置
#### 1.1 创建项目
```bash
cargo new axum-blog-api
cd axum-blog-api
```

#### 1.2 配置 `Cargo.toml` 完整依赖
所有依赖均为当前最新稳定版，每个依赖的作用都对应你之前学的知识点：
```toml
[package]
name = "axum-blog-api"
version = "0.1.0"
edition = "2021"
description = "Rust Axum 生产级异步博客后端"
license = "MIT"

[dependencies]
# Axum Web 核心框架，基于 Tokio 异步运行时
axum = { version = "0.7", features = ["macros", "ws"] }
# Tokio 异步运行时，对应之前学的异步编程知识点
tokio = { version = "1.0", features = ["full"] }
# 数据库操作：编译时 SQL 校验，异步 IO，支持 SQLite
sqlx = { version = "0.7", features = ["runtime-tokio", "sqlite", "macros", "chrono", "json"] }
# JWT 身份认证，用于用户登录校验
jsonwebtoken = "9.3"
# 密码哈希，用于用户密码加密存储
bcrypt = "0.15"
# 自定义错误类型，对应之前学的错误处理知识点
thiserror = "1.0"
# 通用错误处理
anyhow = "1.0"
# 序列化/反序列化，对应之前学的 serde 知识点
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
# 时间处理
chrono = { version = "0.4", features = ["serde", "local", "utc"] }
# 结构化日志，对应 tokio 异步日志体系
tracing = { version = "0.1", features = ["full"] }
tracing-subscriber = { version = "0.3", features = ["env-filter", "fmt"] }
# 接口参数校验
validator = { version = "0.18", features = ["derive"] }
# 跨域 CORS 中间件
tower-http = { version = "0.5", features = ["cors", "trace", "timeout"] }
# 密码学随机数，用于 JWT 密钥
rand = { version = "0.8", features = ["std_rng"] }
```

#### 1.3 安装 sqlx 命令行工具
sqlx-cli 用于管理数据库迁移、编译时 SQL 校验，全局安装：
```bash
cargo install sqlx-cli --no-default-features --features sqlite
```

### 步骤2：项目分层架构设计
我们采用生产级标准的分层架构，职责清晰，便于维护和扩展，创建如下目录结构：
```
axum-blog-api/
├── migrations/          # 数据库迁移文件目录（sqlx 自动管理）
├── src/
│   ├── main.rs          # 程序入口：服务启动、初始化
│   ├── config.rs        # 全局配置：JWT密钥、数据库地址、端口等
│   ├── error.rs         # 统一错误处理：自定义错误类型
│   ├── model.rs         # 数据模型：数据库表结构、请求/响应结构体
│   ├── db.rs            # 数据库层：连接池、数据库操作封装
│   ├── service.rs       # 服务层：核心业务逻辑
│   ├── handler.rs       # Handler 层：接口请求处理、参数解析
│   ├── middleware.rs    # 中间件：JWT认证、日志、请求校验
│   └── router.rs        # 路由层：接口路由注册、分组
├── Cargo.toml
└── .env                 # 环境变量配置
```

### 步骤3：核心模块代码实现
我们按依赖顺序逐个实现模块，每段代码都有详细注释，关联之前的知识点。

#### 3.1 环境变量与全局配置 `src/config.rs`
管理全局配置，从环境变量加载，避免硬编码：
```rust
use std::env;
use anyhow::Result;
use chrono::Duration;

/// 全局应用配置
#[derive(Debug, Clone)]
pub struct AppConfig {
    /// 服务监听端口
    pub server_port: u16,
    /// 数据库连接地址
    pub database_url: String,
    /// JWT 加密密钥
    pub jwt_secret: String,
    /// JWT 过期时间（默认7天）
    pub jwt_expire_duration: Duration,
}

impl AppConfig {
    /// 从环境变量加载配置
    pub fn from_env() -> Result<Self> {
        // 加载 .env 文件中的环境变量
        dotenvy::dotenv().ok();

        Ok(Self {
            server_port: env::var("SERVER_PORT")
                .unwrap_or_else(|_| "3000".to_string())
                .parse()?,
            database_url: env::var("DATABASE_URL")
                .context("DATABASE_URL 环境变量未配置")?,
            jwt_secret: env::var("JWT_SECRET")
                .unwrap_or_else(|_| rand::random::<[u8; 32]>().iter().map(|b| format!("{:02x}", b)).collect()),
            jwt_expire_duration: Duration::days(7),
        })
    }
}
```
创建根目录的 `.env` 配置文件：
```env
# .env 文件
SERVER_PORT=3000
DATABASE_URL=sqlite:blog.db
JWT_SECRET=your_secure_jwt_secret_key_here_change_it_in_production
```

#### 3.2 统一错误处理 `src/error.rs`
基于 `thiserror` 自定义错误类型，实现 Axum 要求的 `IntoResponse` Trait，统一处理所有接口错误，对应之前学的错误处理知识点：
```rust
use axum::{
    http::StatusCode,
    response::{IntoResponse, Response},
    Json,
};
use serde_json::json;
use thiserror::Error;

/// 应用全局错误枚举
#[derive(Error, Debug)]
pub enum AppError {
    /// 400 客户端参数错误
    #[error("参数校验失败: {0}")]
    ValidationError(String),

    /// 401 未授权
    #[error("未授权: {0}")]
    UnauthorizedError(String),

    /// 403 禁止访问
    #[error("禁止访问: {0}")]
    ForbiddenError(String),

    /// 404 资源不存在
    #[error("资源不存在: {0}")]
    NotFoundError(String),

    /// 409 资源冲突
    #[error("资源冲突: {0}")]
    ConflictError(String),

    /// 500 服务器内部错误
    #[error("服务器内部错误: {0}")]
    InternalError(#[from] anyhow::Error),

    /// 数据库错误
    #[error("数据库操作失败: {0}")]
    DatabaseError(#[from] sqlx::Error),

    /// JWT 处理错误
    #[error("JWT 处理失败: {0}")]
    JwtError(#[from] jsonwebtoken::errors::Error),

    /// 密码哈希错误
    #[error("密码处理失败: {0}")]
    PasswordError(#[from] bcrypt::BcryptError),
}

/// 实现 Axum 的 IntoResponse Trait，将错误转为 HTTP 响应
impl IntoResponse for AppError {
    fn into_response(self) -> Response {
        // 匹配错误类型，设置对应的 HTTP 状态码和错误信息
        let (status_code, message) = match self {
            AppError::ValidationError(msg) => (StatusCode::BAD_REQUEST, msg),
            AppError::UnauthorizedError(msg) => (StatusCode::UNAUTHORIZED, msg),
            AppError::ForbiddenError(msg) => (StatusCode::FORBIDDEN, msg),
            AppError::NotFoundError(msg) => (StatusCode::NOT_FOUND, msg),
            AppError::ConflictError(msg) => (StatusCode::CONFLICT, msg),
            AppError::InternalError(e) => {
                tracing::error!("服务器内部错误: {:?}", e);
                (StatusCode::INTERNAL_SERVER_ERROR, "服务器内部错误".to_string())
            }
            AppError::DatabaseError(e) => {
                tracing::error!("数据库错误: {:?}", e);
                (StatusCode::INTERNAL_SERVER_ERROR, "数据库操作失败".to_string())
            }
            AppError::JwtError(e) => {
                tracing::warn!("JWT 错误: {:?}", e);
                (StatusCode::UNAUTHORIZED, "无效的身份凭证".to_string())
            }
            AppError::PasswordError(e) => {
                tracing::error!("密码处理错误: {:?}", e);
                (StatusCode::INTERNAL_SERVER_ERROR, "密码处理失败".to_string())
            }
        };

        // 构建统一的 JSON 错误响应
        let body = Json(json!({
            "code": status_code.as_u16(),
            "message": message,
            "success": false
        }));

        (status_code, body).into_response()
    }
}

/// 定义全局 Result 类型别名，简化代码
pub type AppResult<T> = Result<T, AppError>;
```

#### 3.3 数据模型定义 `src/model.rs`
定义数据库表对应的结构体、接口请求/响应结构体，用到 serde 序列化、validator 参数校验、生命周期等知识点：
```rust
use chrono::{DateTime, Utc};
use serde::{Deserialize, Serialize};
use validator::Validate;
use jsonwebtoken::{EncodingKey, DecodingKey};

// ====================== 用户模型 ======================
/// 数据库用户表结构体
#[derive(Debug, Clone, Serialize, Deserialize, sqlx::FromRow)]
pub struct User {
    /// 用户唯一ID
    pub id: i64,
    /// 用户名（唯一）
    pub username: String,
    /// 邮箱（唯一）
    pub email: String,
    /// 密码哈希（不返回给前端）
    #[serde(skip_serializing)]
    pub password_hash: String,
    /// 创建时间
    pub created_at: DateTime<Utc>,
    /// 更新时间
    pub updated_at: DateTime<Utc>,
}

/// 用户注册请求体
#[derive(Debug, Deserialize, Validate)]
pub struct RegisterRequest {
    /// 用户名，长度3-20
    #[validate(length(min = 3, max = 20, message = "用户名长度必须在3-20之间"))]
    pub username: String,
    /// 邮箱格式校验
    #[validate(email(message = "邮箱格式不正确"))]
    pub email: String,
    /// 密码，长度6-32
    #[validate(length(min = 6, max = 32, message = "密码长度必须在6-32之间"))]
    pub password: String,
}

/// 用户登录请求体
#[derive(Debug, Deserialize, Validate)]
pub struct LoginRequest {
    /// 用户名/邮箱
    pub account: String,
    /// 密码
    pub password: String,
}

/// 用户登录/注册响应体
#[derive(Debug, Serialize)]
pub struct AuthResponse {
    /// 用户信息
    pub user: UserInfo,
    /// JWT 令牌
    pub token: String,
}

/// 脱敏的用户信息（返回给前端）
#[derive(Debug, Serialize, Deserialize)]
pub struct UserInfo {
    pub id: i64,
    pub username: String,
    pub email: String,
    pub created_at: DateTime<Utc>,
}

impl From<User> for UserInfo {
    /// 从 User 转为脱敏的 UserInfo，自动去除敏感字段
    fn from(user: User) -> Self {
        Self {
            id: user.id,
            username: user.username,
            email: user.email,
            created_at: user.created_at,
        }
    }
}

// ====================== JWT 模型 ======================
/// JWT 载荷（Claims）
#[derive(Debug, Serialize, Deserialize)]
pub struct JwtClaims {
    /// 用户ID
    pub sub: i64,
    /// 用户名
    pub username: String,
    /// 过期时间（时间戳）
    pub exp: usize,
    /// 签发时间
    pub iat: usize,
}

/// JWT 配置
#[derive(Debug, Clone)]
pub struct JwtConfig {
    pub encoding_key: EncodingKey,
    pub decoding_key: DecodingKey,
    pub expire_duration: chrono::Duration,
}

// ====================== 文章模型 ======================
/// 数据库文章表结构体
#[derive(Debug, Clone, Serialize, Deserialize, sqlx::FromRow)]
pub struct Article {
    /// 文章唯一ID
    pub id: i64,
    /// 文章标题
    pub title: String,
    /// 文章内容
    pub content: String,
    /// 作者ID
    pub author_id: i64,
    /// 是否发布
    pub is_published: bool,
    /// 浏览量
    pub view_count: i64,
    /// 创建时间
    pub created_at: DateTime<Utc>,
    /// 更新时间
    pub updated_at: DateTime<Utc>,
}

/// 创建文章请求体
#[derive(Debug, Deserialize, Validate)]
pub struct CreateArticleRequest {
    #[validate(length(min = 1, max = 100, message = "标题长度必须在1-100之间"))]
    pub title: String,
    #[validate(length(min = 1, message = "文章内容不能为空"))]
    pub content: String,
    #[serde(default)]
    pub is_published: bool,
}

/// 更新文章请求体
#[derive(Debug, Deserialize, Validate)]
pub struct UpdateArticleRequest {
    #[validate(length(min = 1, max = 100, message = "标题长度必须在1-100之间"))]
    pub title: Option<String>,
    #[validate(length(min = 1, message = "文章内容不能为空"))]
    pub content: Option<String>,
    pub is_published: Option<bool>,
}

/// 文章列表查询参数
#[derive(Debug, Deserialize)]
pub struct ArticleQuery {
    /// 页码，默认1
    pub page: Option<u64>,
    /// 每页数量，默认10
    pub page_size: Option<u64>,
    /// 只查询指定作者的文章
    pub author_id: Option<i64>,
    /// 只查询已发布的文章
    pub published_only: Option<bool>,
}

/// 分页响应体
#[derive(Debug, Serialize)]
pub struct PageResponse<T> {
    /// 数据列表
    pub items: Vec<T>,
    /// 总条数
    pub total: i64,
    /// 当前页码
    pub page: u64,
    /// 每页数量
    pub page_size: u64,
    /// 总页数
    pub total_pages: u64,
}

// ====================== 通用响应体 ======================
/// 通用成功响应体
#[derive(Debug, Serialize)]
pub struct SuccessResponse<T> {
    pub code: u16,
    pub message: String,
    pub success: bool,
    pub data: T,
}

impl<T> SuccessResponse<T> {
    /// 构建成功响应
    pub fn new(data: T, message: &str) -> Self {
        Self {
            code: 200,
            message: message.to_string(),
            success: true,
            data,
        }
    }
}
```

#### 3.4 数据库层 `src/db.rs`
封装数据库连接池、数据库操作，基于 sqlx 异步数据库库，对应异步编程、错误处理知识点：
```rust
use sqlx::sqlite::{SqlitePool, SqlitePoolOptions};
use crate::{config::AppConfig, model::*, AppResult};
use chrono::Utc;

/// 数据库连接池包装
#[derive(Debug, Clone)]
pub struct DbPool(pub SqlitePool);

impl DbPool {
    /// 初始化数据库连接池
    pub async fn init(config: &AppConfig) -> AppResult<Self> {
        let pool = SqlitePoolOptions::new()
            .max_connections(10)
            .connect(&config.database_url)
            .await?;

        // 执行数据库迁移（自动创建表、更新结构）
        sqlx::migrate!("./migrations")
            .run(&pool)
            .await?;

        tracing::info!("数据库连接池初始化成功，迁移已执行");
        Ok(Self(pool))
    }

    /// 获取内部的 SqlitePool 引用
    pub fn inner(&self) -> &SqlitePool {
        &self.0
    }
}

// ====================== 用户相关数据库操作 ======================
impl DbPool {
    /// 根据用户名或邮箱查询用户
    pub async fn get_user_by_account(&self, account: &str) -> AppResult<Option<User>> {
        let user = sqlx::query_as!(
            User,
            r#"SELECT * FROM users WHERE username = ? OR email = ? LIMIT 1"#,
            account,
            account
        )
        .fetch_optional(self.inner())
        .await?;

        Ok(user)
    }

    /// 根据用户ID查询用户
    pub async fn get_user_by_id(&self, user_id: i64) -> AppResult<Option<User>> {
        let user = sqlx::query_as!(
            User,
            r#"SELECT * FROM users WHERE id = ? LIMIT 1"#,
            user_id
        )
        .fetch_optional(self.inner())
        .await?;

        Ok(user)
    }

    /// 创建新用户
    pub async fn create_user(&self, username: &str, email: &str, password_hash: &str) -> AppResult<User> {
        let now = Utc::now();
        let user = sqlx::query_as!(
            User,
            r#"
            INSERT INTO users (username, email, password_hash, created_at, updated_at)
            VALUES (?, ?, ?, ?, ?)
            RETURNING *
            "#,
            username,
            email,
            password_hash,
            now,
            now
        )
        .fetch_one(self.inner())
        .await?;

        Ok(user)
    }

    /// 检查用户名或邮箱是否已存在
    pub async fn check_user_exists(&self, username: &str, email: &str) -> AppResult<bool> {
        let exists = sqlx::query!(
            r#"SELECT EXISTS(SELECT 1 FROM users WHERE username = ? OR email = ?) as "exists: bool""#,
            username,
            email
        )
        .fetch_one(self.inner())
        .await?
        .exists;

        Ok(exists)
    }
}

// ====================== 文章相关数据库操作 ======================
impl DbPool {
    /// 创建文章
    pub async fn create_article(&self, req: &CreateArticleRequest, author_id: i64) -> AppResult<Article> {
        let now = Utc::now();
        let article = sqlx::query_as!(
            Article,
            r#"
            INSERT INTO articles (title, content, author_id, is_published, created_at, updated_at)
            VALUES (?, ?, ?, ?, ?, ?)
            RETURNING *
            "#,
            req.title,
            req.content,
            author_id,
            req.is_published,
            now,
            now
        )
        .fetch_one(self.inner())
        .await?;

        Ok(article)
    }

    /// 根据ID查询文章
    pub async fn get_article_by_id(&self, article_id: i64) -> AppResult<Option<Article>> {
        let article = sqlx::query_as!(
            Article,
            r#"SELECT * FROM articles WHERE id = ? LIMIT 1"#,
            article_id
        )
        .fetch_optional(self.inner())
        .await?;

        Ok(article)
    }

    /// 分页查询文章列表
    pub async fn list_articles(&self, query: &ArticleQuery) -> AppResult<(Vec<Article>, i64)> {
        let page = query.page.unwrap_or(1);
        let page_size = query.page_size.unwrap_or(10);
        let offset = (page - 1) * page_size;

        // 构建动态查询条件
        let mut where_clauses = Vec::new();
        if let Some(author_id) = query.author_id {
            where_clauses.push(format!("author_id = {}", author_id));
        }
        if query.published_only.unwrap_or(false) {
            where_clauses.push("is_published = 1".to_string());
        }
        let where_sql = if where_clauses.is_empty() {
            "1=1".to_string()
        } else {
            where_clauses.join(" AND ")
        };

        // 查询总条数
        let total = sqlx::query!(
            format!(r#"SELECT COUNT(*) as "total: i64" FROM articles WHERE {}"#, where_sql).as_str()
        )
        .fetch_one(self.inner())
        .await?
        .total;

        // 分页查询数据，按创建时间倒序
        let articles = sqlx::query_as!(
            Article,
            format!(
                r#"SELECT * FROM articles WHERE {} ORDER BY created_at DESC LIMIT ? OFFSET ?"#,
                where_sql
            ).as_str(),
            page_size,
            offset
        )
        .fetch_all(self.inner())
        .await?;

        Ok((articles, total))
    }

    /// 更新文章
    pub async fn update_article(&self, article_id: i64, req: &UpdateArticleRequest) -> AppResult<Article> {
        let now = Utc::now();
        let mut update_clauses = Vec::new();
        let mut params = Vec::new();

        if let Some(title) = &req.title {
            update_clauses.push("title = ?");
            params.push(title.as_str());
        }
        if let Some(content) = &req.content {
            update_clauses.push("content = ?");
            params.push(content.as_str());
        }
        if let Some(is_published) = req.is_published {
            update_clauses.push("is_published = ?");
            params.push(if is_published { "1" } else { "0" });
        }
        update_clauses.push("updated_at = ?");
        params.push(&now.to_rfc3339());

        let sql = format!(
            r#"UPDATE articles SET {} WHERE id = ? RETURNING *"#,
            update_clauses.join(", ")
        );

        let mut query = sqlx::query_as::<_, Article>(&sql);
        for param in params {
            query = query.bind(param);
        }
        query = query.bind(article_id);

        let article = query.fetch_one(self.inner()).await?;
        Ok(article)
    }

    /// 增加文章浏览量
    pub async fn increment_article_view(&self, article_id: i64) -> AppResult<()> {
        sqlx::query!(
            r#"UPDATE articles SET view_count = view_count + 1 WHERE id = ?"#,
            article_id
        )
        .execute(self.inner())
        .await?;

        Ok(())
    }

    /// 删除文章
    pub async fn delete_article(&self, article_id: i64) -> AppResult<()> {
        sqlx::query!(
            r#"DELETE FROM articles WHERE id = ?"#,
            article_id
        )
        .execute(self.inner())
        .await?;

        Ok(())
    }
}
```

#### 3.5 创建数据库迁移文件
sqlx 用迁移文件管理数据库表结构，执行以下命令创建迁移文件：
```bash
sqlx migrate add init_tables
```
执行后会在 `migrations/` 目录下生成一个 `.sql` 文件，打开它，写入创建用户表和文章表的SQL：
```sql
-- migrations/xxxxxx_init_tables.sql
-- 创建用户表
CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL UNIQUE,
    email TEXT NOT NULL UNIQUE,
    password_hash TEXT NOT NULL,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);

-- 创建文章表
CREATE TABLE IF NOT EXISTS articles (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    title TEXT NOT NULL,
    content TEXT NOT NULL,
    author_id INTEGER NOT NULL,
    is_published INTEGER NOT NULL DEFAULT 0,
    view_count INTEGER NOT NULL DEFAULT 0,
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL,
    FOREIGN KEY (author_id) REFERENCES users(id) ON DELETE CASCADE
);

-- 创建索引
CREATE INDEX IF NOT EXISTS idx_articles_author_id ON articles(author_id);
CREATE INDEX IF NOT EXISTS idx_articles_created_at ON articles(created_at DESC);
```

#### 3.6 服务层 `src/service.rs`
封装核心业务逻辑，解耦 Handler 层和数据库层，用到异步、错误处理、所有权知识点：
```rust
use crate::{config::AppConfig, db::DbPool, model::*, AppResult, AppError};
use chrono::Utc;
use jsonwebtoken::{Algorithm, Header};
use bcrypt::{hash, verify, DEFAULT_COST};

/// 认证服务
#[derive(Debug, Clone)]
pub struct AuthService {
    pool: DbPool,
    jwt_config: JwtConfig,
}

impl AuthService {
    pub fn new(pool: DbPool, config: &AppConfig) -> Self {
        let jwt_config = JwtConfig {
            encoding_key: jsonwebtoken::EncodingKey::from_secret(config.jwt_secret.as_bytes()),
            decoding_key: jsonwebtoken::DecodingKey::from_secret(config.jwt_secret.as_bytes()),
            expire_duration: config.jwt_expire_duration,
        };

        Self { pool, jwt_config }
    }

    /// 用户注册
    pub async fn register(&self, req: RegisterRequest) -> AppResult<AuthResponse> {
        // 1. 校验参数
        req.validate()?;

        // 2. 检查用户是否已存在
        let exists = self.pool.check_user_exists(&req.username, &req.email).await?;
        if exists {
            return Err(AppError::ConflictError("用户名或邮箱已被注册".to_string()));
        }

        // 3. 密码哈希加密
        let password_hash = hash(req.password.as_bytes(), DEFAULT_COST)?;

        // 4. 创建用户
        let user = self.pool.create_user(&req.username, &req.email, &password_hash).await?;

        // 5. 生成 JWT 令牌
        let token = self.generate_jwt(&user)?;

        Ok(AuthResponse {
            user: UserInfo::from(user),
            token,
        })
    }

    /// 用户登录
    pub async fn login(&self, req: LoginRequest) -> AppResult<AuthResponse> {
        // 1. 查询用户
        let user = self.pool.get_user_by_account(&req.account).await?
            .ok_or_else(|| AppError::UnauthorizedError("用户名或密码错误".to_string()))?;

        // 2. 校验密码
        let is_valid = verify(&req.password, &user.password_hash)?;
        if !is_valid {
            return Err(AppError::UnauthorizedError("用户名或密码错误".to_string()));
        }

        // 3. 生成 JWT 令牌
        let token = self.generate_jwt(&user)?;

        Ok(AuthResponse {
            user: UserInfo::from(user),
            token,
        })
    }

    /// 生成 JWT 令牌
    fn generate_jwt(&self, user: &User) -> AppResult<String> {
        let now = Utc::now();
        let expire = now + self.jwt_config.expire_duration;

        let claims = JwtClaims {
            sub: user.id,
            username: user.username.clone(),
            exp: expire.timestamp() as usize,
            iat: now.timestamp() as usize,
        };

        let token = jsonwebtoken::encode(
            &Header::new(Algorithm::HS256),
            &claims,
            &self.jwt_config.encoding_key,
        )?;

        Ok(token)
    }

    /// 验证 JWT 令牌，返回载荷
    pub fn verify_jwt(&self, token: &str) -> AppResult<JwtClaims> {
        let claims = jsonwebtoken::decode::<JwtClaims>(
            token,
            &self.jwt_config.decoding_key,
            &jsonwebtoken::Validation::new(Algorithm::HS256),
        )?
        .claims;

        Ok(claims)
    }
}

/// 文章服务
#[derive(Debug, Clone)]
pub struct ArticleService {
    pool: DbPool,
}

impl ArticleService {
    pub fn new(pool: DbPool) -> Self {
        Self { pool }
    }

    /// 创建文章
    pub async fn create_article(&self, req: CreateArticleRequest, author_id: i64) -> AppResult<Article> {
        req.validate()?;
        let article = self.pool.create_article(&req, author_id).await?;
        Ok(article)
    }

    /// 获取文章详情
    pub async fn get_article(&self, article_id: i64, increment_view: bool) -> AppResult<Article> {
        let article = self.pool.get_article_by_id(article_id).await?
            .ok_or_else(|| AppError::NotFoundError("文章不存在".to_string()))?;

        // 增加浏览量
        if increment_view {
            self.pool.increment_article_view(article_id).await?;
        }

        Ok(article)
    }

    /// 分页查询文章列表
    pub async fn list_articles(&self, query: ArticleQuery) -> AppResult<PageResponse<Article>> {
        let page = query.page.unwrap_or(1);
        let page_size = query.page_size.unwrap_or(10);
        let (items, total) = self.pool.list_articles(&query).await?;
        let total_pages = (total as f64 / page_size as f64).ceil() as u64;

        Ok(PageResponse {
            items,
            total,
            page,
            page_size,
            total_pages,
        })
    }

    /// 更新文章
    pub async fn update_article(
        &self,
        article_id: i64,
        req: UpdateArticleRequest,
        operator_id: i64
    ) -> AppResult<Article> {
        req.validate()?;

        // 检查文章是否存在，校验权限
        let article = self.get_article(article_id, false).await?;
        if article.author_id != operator_id {
            return Err(AppError::ForbiddenError("你没有权限修改这篇文章".to_string()));
        }

        let updated_article = self.pool.update_article(article_id, &req).await?;
        Ok(updated_article)
    }

    /// 删除文章
    pub async fn delete_article(&self, article_id: i64, operator_id: i64) -> AppResult<()> {
        // 检查文章是否存在，校验权限
        let article = self.get_article(article_id, false).await?;
        if article.author_id != operator_id {
            return Err(AppError::ForbiddenError("你没有权限删除这篇文章".to_string()));
        }

        self.pool.delete_article(article_id).await?;
        Ok(())
    }
}
```

#### 3.7 中间件 `src/middleware.rs`
实现 JWT 认证中间件，基于 Axum 的 `FromRequestParts` Trait，用于保护需要登录的接口，用到 Trait、异步、错误处理知识点：
```rust
use axum::{
    async_trait,
    extract::FromRequestParts,
    http::{request::Parts, header::AUTHORIZATION, StatusCode},
    RequestPartsExt,
};
use crate::{model::JwtClaims, service::AuthService, AppError};

/// 认证用户提取器，用于需要登录的接口
#[derive(Debug, Clone)]
pub struct AuthUser {
    pub id: i64,
    pub username: String,
    pub claims: JwtClaims,
}

/// 实现 FromRequestParts Trait，从请求中提取认证用户
#[async_trait]
impl<S> FromRequestParts<S> for AuthUser
where
    S: Send + Sync,
    AuthService: FromRequestParts<S>,
{
    type Rejection = AppError;

    async fn from_request_parts(parts: &mut Parts, state: &S) -> Result<Self, Self::Rejection> {
        // 1. 从状态中获取 AuthService
        let auth_service = AuthService::from_request_parts(parts, state).await
            .map_err(|_| AppError::InternalError(anyhow::anyhow!("认证服务未初始化")))?;

        // 2. 从请求头中获取 Authorization 头
        let auth_header = parts.headers.get(AUTHORIZATION)
            .ok_or_else(|| AppError::UnauthorizedError("缺少身份凭证".to_string()))?
            .to_str()
            .map_err(|_| AppError::UnauthorizedError("无效的身份凭证格式".to_string()))?;

        // 3. 解析 Bearer 令牌
        let token = auth_header.strip_prefix("Bearer ")
            .ok_or_else(|| AppError::UnauthorizedError("身份凭证格式错误，必须是 Bearer 格式".to_string()))?;

        // 4. 验证 JWT 令牌
        let claims = auth_service.verify_jwt(token)?;

        // 5. 返回认证用户信息
        Ok(Self {
            id: claims.sub,
            username: claims.username.clone(),
            claims,
        })
    }
}
```

#### 3.8 Handler 层 `src/handler.rs`
实现接口的请求处理逻辑，接收请求、调用服务层、返回响应，用到 Axum 提取器、异步、错误处理知识点：
```rust
use axum::{
    extract::{Path, Query, State},
    http::StatusCode,
    Json,
};
use crate::{
    model::*,
    service::{AuthService, ArticleService},
    AppResult, AuthUser,
};

// ====================== 应用状态（全局共享） ======================
/// 应用全局状态，所有 Handler 都可以提取
#[derive(Debug, Clone)]
pub struct AppState {
    pub auth_service: AuthService,
    pub article_service: ArticleService,
}

// ====================== 认证相关 Handler ======================
/// 用户注册接口
pub async fn register(
    State(state): State<AppState>,
    Json(req): Json<RegisterRequest>,
) -> AppResult<(StatusCode, Json<SuccessResponse<AuthResponse>>)> {
    let res = state.auth_service.register(req).await?;
    Ok((
        StatusCode::CREATED,
        Json(SuccessResponse::new(res, "注册成功"))
    ))
}

/// 用户登录接口
pub async fn login(
    State(state): State<AppState>,
    Json(req): Json<LoginRequest>,
) -> AppResult<Json<SuccessResponse<AuthResponse>>> {
    let res = state.auth_service.login(req).await?;
    Ok(Json(SuccessResponse::new(res, "登录成功")))
}

/// 获取当前登录用户信息接口
pub async fn get_current_user(
    auth_user: AuthUser,
    State(state): State<AppState>,
) -> AppResult<Json<SuccessResponse<UserInfo>>> {
    let user = state.auth_service.pool.get_user_by_id(auth_user.id).await?
        .ok_or_else(|| AppError::NotFoundError("用户不存在".to_string()))?;
    Ok(Json(SuccessResponse::new(UserInfo::from(user), "获取成功")))
}

// ====================== 文章相关 Handler ======================
/// 创建文章接口（需要登录）
pub async fn create_article(
    auth_user: AuthUser,
    State(state): State<AppState>,
    Json(req): Json<CreateArticleRequest>,
) -> AppResult<(StatusCode, Json<SuccessResponse<Article>>)> {
    let article = state.article_service.create_article(req, auth_user.id).await?;
    Ok((
        StatusCode::CREATED,
        Json(SuccessResponse::new(article, "文章创建成功"))
    ))
}

/// 获取文章详情接口
pub async fn get_article(
    Path(article_id): Path<i64>,
    State(state): State<AppState>,
) -> AppResult<Json<SuccessResponse<Article>>> {
    let article = state.article_service.get_article(article_id, true).await?;
    Ok(Json(SuccessResponse::new(article, "获取成功")))
}

/// 分页获取文章列表接口
pub async fn list_articles(
    Query(query): Query<ArticleQuery>,
    State(state): State<AppState>,
) -> AppResult<Json<SuccessResponse<PageResponse<Article>>>> {
    let res = state.article_service.list_articles(query).await?;
    Ok(Json(SuccessResponse::new(res, "获取成功")))
}

/// 更新文章接口（需要登录，仅作者可修改）
pub async fn update_article(
    auth_user: AuthUser,
    Path(article_id): Path<i64>,
    State(state): State<AppState>,
    Json(req): Json<UpdateArticleRequest>,
) -> AppResult<Json<SuccessResponse<Article>>> {
    let article = state.article_service.update_article(article_id, req, auth_user.id).await?;
    Ok(Json(SuccessResponse::new(article, "文章更新成功")))
}

/// 删除文章接口（需要登录，仅作者可删除）
pub async fn delete_article(
    auth_user: AuthUser,
    Path(article_id): Path<i64>,
    State(state): State<AppState>,
) -> AppResult<Json<SuccessResponse<()>>> {
    state.article_service.delete_article(article_id, auth_user.id).await?;
    Ok(Json(SuccessResponse::new((), "文章删除成功")))
}

/// 健康检查接口
pub async fn health_check() -> &'static str {
    "OK"
}
```

#### 3.9 路由层 `src/router.rs`
注册接口路由，分组管理，配置中间件：
```rust
use axum::{
    Router,
    routing::{get, post, put, delete},
};
use tower_http::cors::{CorsLayer, Any};
use crate::handler::*;

/// 创建应用路由
pub fn create_router(state: AppState) -> Router {
    // 配置 CORS 跨域中间件，开发环境允许所有来源
    let cors = CorsLayer::new()
        .allow_origin(Any)
        .allow_methods(Any)
        .allow_headers(Any);

    // 公开路由，不需要登录
    let public_routes = Router::new()
        .route("/health", get(health_check))
        .route("/auth/register", post(register))
        .route("/auth/login", post(login))
        .route("/articles", get(list_articles))
        .route("/articles/:id", get(get_article));

    // 私有路由，需要登录认证
    let private_routes = Router::new()
        .route("/auth/me", get(get_current_user))
        .route("/articles", post(create_article))
        .route("/articles/:id", put(update_article))
        .route("/articles/:id", delete(delete_article))
        .with_state(state.clone());

    // 合并路由，添加全局中间件
    Router::new()
        .merge(public_routes)
        .merge(private_routes)
        .with_state(state)
        .layer(cors)
        .layer(tower_http::trace::TraceLayer::new_for_http())
}
```

#### 3.10 程序入口 `src/main.rs`
初始化配置、日志、数据库、服务，启动 HTTP 服务：
```rust
use axum::Server;
use std::net::SocketAddr;
use tracing::Level;

mod config;
mod error;
mod model;
mod db;
mod service;
mod middleware;
mod handler;
mod router;

use config::AppConfig;
use db::DbPool;
use handler::AppState;
use router::create_router;
use service::{AuthService, ArticleService};

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    // 1. 初始化日志系统
    tracing_subscriber::fmt()
        .with_max_level(Level::INFO)
        .with_target(false)
        .compact()
        .init();

    // 2. 加载配置
    let config = AppConfig::from_env()?;
    tracing::info!("配置加载完成，服务端口：{}", config.server_port);

    // 3. 初始化数据库连接池
    let pool = DbPool::init(&config).await?;

    // 4. 初始化服务
    let auth_service = AuthService::new(pool.clone(), &config);
    let article_service = ArticleService::new(pool.clone());
    let app_state = AppState {
        auth_service,
        article_service,
    };

    // 5. 创建路由
    let app = create_router(app_state);

    // 6. 启动 HTTP 服务
    let addr = SocketAddr::from(([0, 0, 0, 0], config.server_port));
    tracing::info!("服务启动成功，监听地址：http://{}", addr);

    Server::bind(&addr)
        .serve(app.into_make_service())
        .await?;

    Ok(())
}
```

### 步骤4：启动服务与接口测试
#### 4.1 启动服务
```bash
# 开发模式启动
cargo run

# 生产模式编译启动
cargo build --release
./target/release/axum-blog-api
```
启动成功后，服务会监听 `http://0.0.0.0:3000`，数据库迁移会自动执行，生成 `blog.db` SQLite 数据库文件。

#### 4.2 接口测试示例（curl 命令）
```bash
# 1. 健康检查
curl http://localhost:3000/health

# 2. 用户注册
curl -X POST http://localhost:3000/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","email":"test@example.com","password":"123456"}'

# 3. 用户登录（返回的 token 用于后续认证接口）
curl -X POST http://localhost:3000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"account":"testuser","password":"123456"}'

# 4. 创建文章（替换 YOUR_TOKEN 为登录返回的 token）
curl -X POST http://localhost:3000/articles \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"title":"我的第一篇Rust博客","content":"Axum太好用了！","is_published":true}'

# 5. 获取文章列表
curl http://localhost:3000/articles?page=1&page_size=10

# 6. 获取文章详情
curl http://localhost:3000/articles/1

# 7. 更新文章
curl -X PUT http://localhost:3000/articles/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"title":"更新后的标题","is_published":false}'

# 8. 删除文章
curl -X DELETE http://localhost:3000/articles/1 \
  -H "Authorization: Bearer YOUR_TOKEN"
```

---

## Rust + WebAssembly 全栈开发：从入门到实战

WebAssembly（Wasm）是一种可在现代浏览器中运行的二进制指令格式，它以接近原生的速度执行代码，同时保持平台无关性和安全性。Rust 凭借零成本抽象、内存安全、出色的工具链，成为开发 WebAssembly 的首选语言。本教程从零开始，带你掌握 Rust + Wasm 全栈开发的核心技能，并通过一个高性能 Markdown 实时渲染器融会贯通。

---

### 优势一览

- **极致的运行速度**：接近原生 C/C++，远快于 JavaScript。
- **内存安全**：在编译期消除内存泄漏、悬垂指针、缓冲区溢出等问题。
- **极小的包体积**：通过优化后的内存分配器和编译器选项，生成精简的 `.wasm` 文件。
- **与 JavaScript 无缝互操作**：可自由调用 Web API、JS 函数，双向传递复杂数据。
- **完善的开发生态**：`wasm-pack`、`wasm-bindgen`、`web-sys` 提供了开箱即用的开发体验。

---

### 第1步：环境搭建

#### 1.1 安装 Rust wasm 编译目标

```bash
rustup target add wasm32-unknown-unknown
```

该命令为 Rust 编译器添加生成 WebAssembly 二进制文件的能力。

#### 1.2 安装 wasm-pack

`wasm-pack` 是 Rust Wasm 的一站式构建工具，负责编译、打包、生成 JS 胶水代码，甚至发布到 npm。

```bash
cargo install wasm-pack
```

#### 1.3 安装本地服务器（可选）

为了在本地预览项目，需要一个静态文件服务器（因为 ES 模块必须通过 HTTP 加载）。推荐使用轻量快速的 `miniserve`：

```bash
cargo install miniserve
```

你也可以使用 `npx serve`、`python -m http.server` 等替代方案。

---

### 第2步：核心概念速览

在动手写代码前，先理解几个贯穿始终的核心概念。

| 概念                     | 作用                                                         |
| ------------------------ | ------------------------------------------------------------ |
| `wasm32-unknown-unknown` | 编译目标：生成 `.wasm` 文件，不依赖任何操作系统或运行时。    |
| `wasm-bindgen`           | 桥梁库，负责 Rust 与 JavaScript 之间的函数导入/导出、类型转换、内存管理。 |
| `web-sys`                | 浏览器 Web API 的 Rust 绑定，让你能在 Rust 中直接操作 DOM、Canvas、Fetch、Console 等。 |
| `wee_alloc`              | 专为 Wasm 设计的轻量级内存分配器，可显著减小包体积（约 10KB）。 |
| `serde`                  | 序列化/反序列化框架，使复杂数据结构能在 Rust 和 JS 之间透明传递。 |

---

### 第3步：最小示例 —— Hello World

让我们从一个能够双向交互的极简项目开始，体验完整的开发流程。

#### 3.1 创建项目

```bash
cargo new --lib wasm-hello-world
cd wasm-hello-world
```

#### 3.2 配置 `Cargo.toml`

```toml
[package]
name = "wasm-hello-world"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib"]        # 编译为 C 动态库，适配 Wasm 环境

[dependencies]
wasm-bindgen = { version = "0.2", features = ["serde-serialize"] }
web-sys = { version = "0.3", features = ["console", "Document", "HtmlElement", "Window"] }
serde = { version = "1.0", features = ["derive"] }
wee_alloc = { version = "0.4", optional = true }

[features]
default = ["wee_alloc"]         # 默认启用体积优化
```

#### 3.3 编写 Rust 代码：`src/lib.rs`

这段代码展示了函数导出、复杂对象传递、调用浏览器 API 等核心能力。

```rust
// 启用超轻量内存分配器，减小 wasm 体积
#[cfg(feature = "wee_alloc")]
#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

use wasm_bindgen::prelude::*;
use serde::{Serialize, Deserialize};

// ── 1. 基础函数导出 ─────────────────────────────
/// 两数相加（JS 可直接调用）
#[wasm_bindgen]
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

/// 带字符串处理的问候函数
#[wasm_bindgen]
pub fn greet(name: &str) -> String {
    format!("Hello, {}! 来自 Rust Wasm 的问候 🦀", name)
}

// ── 2. 复杂对象导出（结构体 + 方法） ─────────────
#[wasm_bindgen]
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct User {
    #[wasm_bindgen(skip)]        // 该字段不直接暴露给 JS
    pub id: i64,
    pub username: String,
    pub email: String,
}

#[wasm_bindgen]
impl User {
    /// JS 端 new User(...) 的构造器
    #[wasm_bindgen(constructor)]
    pub fn new(id: i64, username: String, email: String) -> Self {
        Self { id, username, email }
    }

    pub fn get_info(&self) -> String {
        format!("用户ID：{}，用户名：{}，邮箱：{}", self.id, self.username, self.email)
    }

    /// 显式访问被 skip 的字段
    pub fn get_id(&self) -> i64 {
        self.id
    }
}

// ── 3. 调用浏览器 API ───────────────────────────
/// 在浏览器控制台输出日志
#[wasm_bindgen]
pub fn log_to_console(message: &str) {
    web_sys::console::log_1(&JsValue::from_str(message));
}

/// 创建 DOM 元素并添加到页面
#[wasm_bindgen]
pub fn add_element_to_page(tag: &str, text: &str) -> Result<(), JsValue> {
    let window = web_sys::window().ok_or_else(|| JsValue::from_str("no window"))?;
    let document = window.document().ok_or_else(|| JsValue::from_str("no document"))?;
    let element = document.create_element(tag)?;
    element.set_text_content(Some(text));
    document.body()
        .ok_or_else(|| JsValue::from_str("no body"))?
        .append_child(&element)?;
    Ok(())
}

// ── 4. Wasm 加载后自动执行的入口 ────────────────
#[wasm_bindgen(start)]
pub fn main() {
    log_to_console("🎉 Rust Wasm 模块加载成功！");
    let _ = add_element_to_page("h1", "Rust + WebAssembly 实战");
}
```

#### 3.4 编译、运行、测试

```bash
# 1) 编译 Wasm，生成 pkg/ 目录
wasm-pack build --target web

# 2) 启动本地服务器
miniserve . --index index.html -p 8080
```

项目根目录下放置以下 `index.html`：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Wasm Hello World</title>
</head>
<body>
    <div>
        <input type="number" id="num1" value="10"> +
        <input type="number" id="num2" value="20"> =
        <span id="result">?</span>
        <button onclick="calculate()">计算</button>
    </div>
    <div style="margin-top:20px">
        <input type="text" id="name" placeholder="你的名字">
        <button onclick="sayHello()">打招呼</button>
        <p id="greet-result"></p>
    </div>
    <div style="margin-top:20px">
        <button onclick="createUser()">创建用户</button>
        <p id="user-info"></p>
    </div>

    <script type="module">
        import init, {
            add, greet, User, log_to_console, add_element_to_page
        } from './pkg/wasm_hello_world.js';

        await init();   // 初始化 Wasm 模块（必须）

        window.calculate = () => {
            const a = +document.getElementById('num1').value;
            const b = +document.getElementById('num2').value;
            document.getElementById('result').textContent = add(a, b);
        };
        window.sayHello = () => {
            const name = document.getElementById('name').value;
            document.getElementById('greet-result').textContent = greet(name);
        };
        window.createUser = () => {
            const user = new User(1, "wasm_user", "rust@example.com");
            document.getElementById('user-info').textContent = user.get_info();
            add_element_to_page("p", `用户创建成功：${user.get_info()}`);
        };
    </script>
</body>
</html>
```

访问 `http://localhost:8080`，测试加减乘除（呃，只有加法）、打招呼、创建用户等功能。打开浏览器控制台，你还能看到 Rust 打印的日志和自动插入的标题。

---

### 第4步：实战项目 —— Markdown 实时渲染器

现在我们把所学串联起来，实现一个左侧编辑、右侧实时预览的高性能 Markdown 渲染器。Rust 负责 Markdown → HTML 的解析，前端仅负责 DOM 更新，极大的减轻了 JS 线程压力。

#### 4.1 创建项目并配置依赖

```bash
cargo new --lib wasm-markdown-renderer
cd wasm-markdown-renderer
```

`Cargo.toml`：

```toml
[package]
name = "wasm-markdown-renderer"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib"]

[dependencies]
wasm-bindgen = "0.2"
web-sys = { version = "0.3", features = ["console"] }
pulldown-cmark = { version = "0.10", features = ["html", "syntax-highlighting"] }
wee_alloc = { version = "0.4", optional = true }

[features]
default = ["wee_alloc"]
```

> **说明**：`pulldown-cmark` 是一个高性能、符合 CommonMark 标准的 Markdown 解析库。开启 `syntax-highlighting` 会为代码块生成 `class="language-xxx"`，方便前端高亮库直接处理。

#### 4.2 Rust 核心逻辑

`src/lib.rs`：

```rust
#[cfg(feature = "wee_alloc")]
#[global_allocator]
static ALLOC: wee_alloc::WeeAlloc = wee_alloc::WeeAlloc::INIT;

use wasm_bindgen::prelude::*;
use pulldown_cmark::{Parser, Options, html};

/// 将 Markdown 字符串转换为 HTML
#[wasm_bindgen]
pub fn markdown_to_html(markdown: &str) -> String {
    let mut options = Options::empty();
    options.insert(Options::ENABLE_TABLES);          // 表格
    options.insert(Options::ENABLE_FOOTNOTES);       // 脚注
    options.insert(Options::ENABLE_STRIKETHROUGH);   // ~~删除线~~
    options.insert(Options::ENABLE_TASKLISTS);       // - [x] 任务列表
    options.insert(Options::ENABLE_SMART_PUNCTUATION);// 智能引号

    let parser = Parser::new_ext(markdown, options);
    let mut html_output = String::new();
    html::push_html(&mut html_output, parser);
    html_output
}

/// Wasm 模块加载成功后自动执行
#[wasm_bindgen(start)]
pub fn main() {
    web_sys::console::log_1(&"✅ Markdown 渲染器 Wasm 已就绪".into());
}
```

#### 4.3 前端页面（集成代码高亮）

我们将使用 `highlight.js` 在浏览器端对代码块进行语法高亮。Rust 生成的 HTML 中已经包含 `language-*` 类名，无需再做额外处理。

`index.html`：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Rust Wasm Markdown 实时渲染器</title>
    <!-- highlight.js 主题 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/styles/github.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; height: 100vh; display: flex; flex-direction: column; }
        .header { background: #24292e; color: white; padding: 1rem 2rem; }
        .container { flex: 1; display: flex; overflow: hidden; }
        .editor-panel, .preview-panel { flex: 1; overflow: auto; }
        .editor-panel { border-right: 1px solid #e1e4e8; }
        #editor { width: 100%; height: 100%; border: none; padding: 1rem; font-family: 'Monaco', 'Menlo', monospace; font-size: 16px; line-height: 1.6; resize: none; outline: none; }
        #preview { padding: 1rem; line-height: 1.6; }
        /* 简化版 GitHub 风格 Markdown 样式 */
        #preview h1 { font-size: 2em; border-bottom: 1px solid #eaecef; padding-bottom: 0.3em; }
        #preview blockquote { padding: 0 1em; color: #6a737d; border-left: 0.25em solid #dfe2e5; margin: 0.5rem 0; }
        #preview code { background: rgba(27,31,35,0.05); padding: 0.2em 0.4em; border-radius: 3px; font-family: 'Monaco', 'Menlo', monospace; }
        #preview pre { background: #f6f8fa; padding: 1em; border-radius: 6px; overflow: auto; }
        #preview pre code { background: none; padding: 0; }
        #preview table { border-collapse: collapse; width: 100%; margin: 0.5rem 0; }
        #preview table th, #preview table td { border: 1px solid #dfe2e5; padding: 0.5em 1em; }
        #preview table th { background: #f6f8fa; font-weight: 600; }
    </style>
</head>
<body>
    <div class="header"><h1>🦀 Rust Wasm Markdown 渲染器</h1></div>
    <div class="container">
        <div class="editor-panel">
            <textarea id="editor" placeholder="输入 Markdown 文本...">
# Rust Wasm Markdown 渲染器

这是一个基于 **Rust WebAssembly** 实现的高性能 Markdown 实时渲染器。

## 功能特性
- 实时渲染，性能远优于纯 JavaScript 方案
- 表格、脚注、任务列表、~~删除线~~ 全支持

```rust
fn main() {
    println!("Hello, Wasm!");
}
```

- [x] 学习 Rust Wasm
- [ ] 发布到 npm
            </textarea>
        </div>
        <div class="preview-panel">
            <div id="preview"></div>
        </div>
    </div>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/11.9.0/highlight.min.js"></script>
    <script type="module">
        import init, { markdown_to_html } from './pkg/wasm_markdown_renderer.js';

        // 初始化 Wasm 模块
        await init();
            
        const editor = document.getElementById('editor');
        const preview = document.getElementById('preview');
            
        function render() {
            const md = editor.value;
            // 调用 Rust 函数获取原始 HTML
            preview.innerHTML = markdown_to_html(md);
            // 手动触发 highlight.js 对所有代码块进行高亮
            preview.querySelectorAll('pre code').forEach((block) => {
                hljs.highlightElement(block);
            });
        }
            
        editor.addEventListener('input', render);
        render();  // 首次渲染
    </script>
        </body>
        </html>
```

#### 4.4 编译与运行

```bash
wasm-pack build --target web
miniserve . --index index.html -p 8080
```

打开浏览器，输入 Markdown 文本，右侧会立刻展现渲染结果，代码块高亮自动完成。你可以在开发者工具的性能面板中看到，Wasm 函数的调用开销极低，即便处理长文本文档也毫无压力。

---

### 第5步：进阶主题

掌握基础后，可以进一步探索以下方向：

#### 5.1 与前端框架集成

- **React / Vue / Svelte**：均可通过异步加载 Wasm 模块，调用导出的函数。配合 `useEffect` / `onMounted` 等生命周期即可。
- **Webpack / Vite**：利用 `wasm-pack` 输出到 npm 包，再用 `import()` 动态加载。部分打包器已内置 Wasm 支持。

#### 5.2 性能优化与体积控制

- **`wee_alloc`**：必选，可节省约 10KB。
- **`wasm-opt`**：来自 Binaryen 工具集的优化器，能进一步缩小 `.wasm` 体积，运行 `wasm-opt -Os input.wasm -o output.wasm`。
- **LTO**（链接时优化）：在 `Cargo.toml` 中设置 `[profile.release] lto = true`，Rust 编译器会进行更激进的优化。

#### 5.3 调试与错误处理

- **控制台日志**：使用 `web_sys::console::log_1()` 或 `wasm_bindgen` 的 `console.log` 绑定。
- **Panic 调试**：默认情况下，Wasm 中的 panic 只会输出一条模糊消息。可以启用 `console_error_panic_hook` 将完整调用栈打印到浏览器控制台。
  ```rust
  #[wasm_bindgen(start)]
  pub fn run() {
      console_error_panic_hook::set_once();
  }
  ```
- **浏览器 DevTools**：现代浏览器已支持 Wasm 断点调试，可在 `Sources` 面板中看到反汇编或源码（需带 DWARF 调试信息）。

#### 5.4 测试

`wasm-bindgen-test` 允许你在浏览器或 Node.js 环境中运行 Rust 测试。

```bash
cargo install wasm-bindgen-cli
wasm-pack test --headless  # 在无头浏览器中运行
```

#### 5.5 发布到 npm

```bash
wasm-pack build --target bundler --out-name my-wasm-lib
wasm-pack publish
```

前端开发者便可通过 `npm install my-wasm-lib` 像使用普通 npm 包一样使用你的 Rust Wasm 模块。

