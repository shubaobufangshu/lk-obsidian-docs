# Java开发学习文档

## Java简介
Java是由Sun公司（后被Oracle收购）于1995年推出的高级编程语言，具有跨平台、面向对象、安全性高、多线程支持等核心特点。其“一次编写，到处运行”的特性源于Java虚拟机（JVM），代码编译为字节码后可在任何安装JVM的设备上执行。Java广泛应用于企业级Web开发、移动应用（Android）、大数据处理、分布式系统等领域，长期占据编程语言排行榜前列。

## 开发环境搭建
首先需要安装Java开发工具包（JDK），建议选择LTS（长期支持）版本，如JDK 11或JDK 17。安装完成后需配置环境变量：在系统变量中新建`JAVA_HOME`指向JDK安装路径，编辑`Path`变量添加`%JAVA_HOME%\bin`。验证安装可在命令行输入`java -version`和`javac -version`，若显示版本信息则配置成功。

集成开发环境（IDE）推荐使用IntelliJ IDEA或Eclipse，IntelliJ IDEA功能更强大且用户体验更佳，社区版免费即可满足基础开发需求。安装IDE后需配置项目SDK，指向已安装的JDK路径。

## Java 基础语法

### Java 程序的基本结构

#### 第一个 Java 程序：HelloWorld

Java 程序以类为基本组织单元，一个简单的 Java 程序示例如下：

```java
// 这是一个单行注释
/* 
   这是一个多行注释
   用于说明程序功能
*/
public class HelloWorld {
    // 文档注释，用于生成API文档
    public static void main(String[] args) {
        // 输出 Hello World 到控制台
        System.out.println("Hello World");
    }
}
```

- **类的定义**：`public class HelloWorld` 定义了一个公共类，类名 `HelloWorld` 必须与文件名完全一致（包括大小写）。
- **main 方法**：程序的入口，格式固定为 `public static void main(String[] args)`，JVM 会从该方法开始执行程序。
- **注释**：分为单行注释（`//`）、多行注释（`/* */`）和文档注释（`/** */`），用于解释代码，不参与编译。

#### 程序入口：main 方法

main 方法的各部分含义：

- `public`：公共访问修饰符，确保 JVM 可以访问该方法。
- `static`：静态修饰符，表示该方法属于类本身，无需创建对象即可调用。
- `void`：返回值类型，表示该方法无返回值。
- `String[] args`：字符串数组参数，用于接收命令行传入的参数。

### 数据类型

Java 数据类型分为**基本数据类型**和**引用数据类型**两大类。

#### 基本数据类型

Java 提供 8 种基本数据类型，用于存储简单值：

| 类型名    | 占用字节  | 取值范围                    | 默认值   | 说明                       |
| --------- | --------- | --------------------------- | -------- | -------------------------- |
| `byte`    | 1         | -128 ~ 127                  | 0        | 小整数类型，节省空间       |
| `short`   | 2         | -32768 ~ 32767              | 0        | 短整数类型                 |
| `int`     | 4         | -2^31 ~ 2^31-1              | 0        | 最常用的整数类型           |
| `long`    | 8         | -2^63 ~ 2^63-1              | 0L       | 长整数类型，值后加 `L`     |
| `float`   | 4         | 约 ±3.40282347E+38F         | 0.0f     | 单精度浮点数，值后加 `f`   |
| `double`  | 8         | 约 ±1.7976931348623157E+308 | 0.0d     | 双精度浮点数，默认浮点类型 |
| `char`    | 2         | 0 ~ 65535（Unicode 编码）   | '\u0000' | 单个字符，用单引号包裹     |
| `boolean` | 1（理论） | `true` 或 `false`           | `false`  | 布尔类型，仅表示真假       |

代码示例：

```java
// 整数类型
byte age = 25;
short salary = 30000;
int count = 100;
long population = 1400000000L;

// 浮点类型
float price = 19.9f;
double pi = 3.1415926535;

// 字符类型
char letter = 'A';
char ch = '\u4e2d'; // 表示汉字“中”

// 布尔类型
boolean isFlag = true;
```

#### 引用数据类型

引用数据类型用于存储对象的引用（内存地址），包括类、接口、数组等，默认值为 `null`（表示不指向任何对象）。后续章节会详细讲解类、数组等引用类型的使用。

#### 数据类型转换

##### 自动类型转换（隐式转换）

当小范围类型向大范围类型转换时，Java 会自动完成，无需手动操作：
`byte → short → int → long → float → double`
`char → int`

代码示例：

```java
int num = 100;
double dNum = num; // 自动将 int 转换为 double
System.out.println(dNum); // 输出 100.0
```

##### 强制类型转换（显式转换）

当大范围类型向小范围类型转换时，需要手动强制转换，可能会损失精度：
格式：`目标类型 变量名 = (目标类型) 原变量;`

代码示例：

```java
double dNum = 3.14;
int num = (int) dNum; // 强制将 double 转换为 int，小数部分被舍弃
System.out.println(num); // 输出 3
```

### 变量与常量

#### 变量的声明与使用

变量是程序中用于存储数据的容器，需先声明后使用：

- 声明格式：`数据类型 变量名;`
- 命名规范：采用小驼峰命名法（如 `userName`），见名知意，不能使用 Java 关键字。
- 初始化：声明后需赋值才能使用，可声明时直接赋值，或先声明后赋值。

代码示例：

```java
// 声明并初始化
int age = 25;
// 先声明后赋值
String name;
name = "张三";
System.out.println(name + " 的年龄是 " + age);
```

#### 变量的作用域

变量的作用域指变量的有效范围，分为两类：

- **成员变量**：定义在类中、方法外的变量，有默认值（基本类型为 0/false，引用类型为 null），作用域为整个类。
- **局部变量**：定义在方法或代码块中的变量，无默认值，必须手动初始化后使用，作用域为当前方法或代码块。

代码示例：

```java
public class VariableScope {
    int memberVar; // 成员变量，默认值为 0

    public void test() {
        int localVar; // 局部变量，无默认值
        // System.out.println(localVar); // 编译错误，局部变量未初始化
        localVar = 10;
        System.out.println(localVar); // 输出 10
        System.out.println(memberVar); // 输出 0
    }
}
```

#### 常量

常量是值不可改变的变量，使用 `final` 关键字修饰：

- 声明格式：`final 数据类型 常量名 = 值;`
- 命名规范：采用全大写下划线分隔（如 `MAX_SIZE`）。
- 注意：常量必须在声明时赋值，且赋值后不可修改。

代码示例：

```java
public class ConstantDemo {
    public static void main(String[] args) {
        final double PI = 3.14159;
        // PI = 3.14; // 编译错误，常量不可修改
        System.out.println("圆周率：" + PI);
    }
}
```

### 运算符

Java 提供了丰富的运算符，用于处理数据的运算。

#### 算术运算符

用于基本的数学运算：

| 运算符 | 说明       | 示例           | 结果 |
| ------ | ---------- | -------------- | ---- |
| `+`    | 加法       | `5 + 3`        | 8    |
| `-`    | 减法       | `5 - 3`        | 2    |
| `*`    | 乘法       | `5 * 3`        | 15   |
| `/`    | 除法       | `5 / 2`        | 2    |
| `%`    | 取余（模） | `5 % 2`        | 1    |
| `++`   | 自增       | `int a=5; a++` | 6    |
| `--`   | 自减       | `int a=5; a--` | 4    |

注意：

- 整数相除结果仍为整数，小数部分会被舍弃。
- 取余运算的结果符号与被除数一致（如 `5 % -2 = 1`，`-5 % 2 = -1`）。
- 自增/自减有前置和后置之分：
  - 前置（`++a`）：先自增/自减，再参与运算。
  - 后置（`a++`）：先参与运算，再自增/自减。

代码示例：

```java
int a = 5;
int b = ++a; // 前置：a 先变为 6，再赋值给 b
System.out.println("a=" + a + ", b=" + b); // 输出 a=6, b=6

int c = 5;
int d = c++; // 后置：c 先赋值给 d（d=5），再变为 6
System.out.println("c=" + c + ", d=" + d); // 输出 c=6, d=5
```

#### 关系运算符

用于比较两个值的大小关系，结果为 `boolean` 类型（`true` 或 `false`）：

| 运算符 | 说明     | 示例     | 结果  |
| ------ | -------- | -------- | ----- |
| `==`   | 等于     | `5 == 3` | false |
| `!=`   | 不等于   | `5 != 3` | true  |
| `>`    | 大于     | `5 > 3`  | true  |
| `<`    | 小于     | `5 < 3`  | false |
| `>=`   | 大于等于 | `5 >= 3` | true  |
| `<=`   | 小于等于 | `5 <= 3` | false |

代码示例：

```java
int age = 18;
boolean isAdult = age >= 18;
System.out.println("是否成年：" + isAdult); // 输出 true
```

#### 逻辑运算符

用于连接多个 `boolean` 表达式，结果仍为 `boolean` 类型：

| 运算符 | 说明       | 规则                                                         | 示例         |
| ------ | ---------- | ------------------------------------------------------------ | ------------ |
| `&&`   | 短路与     | 两边都为 `true`，结果才为 `true`；左边为 `false` 则右边不执行 | `5>3 && 2<4` |
| `||`   | 短路或     | 只要有一边为 `true`，结果就为 `true`；左边为 `true` 则右边不执行 | `5>3 || 2>4` |
| `!`    | 非（取反） | `true` 变 `false`，`false` 变 `true`                         | `!true`      |

代码示例：

```java
int score = 85;
// 判断成绩是否在 80-90 之间
boolean isGood = score >= 80 && score <= 90;
System.out.println("成绩是否良好：" + isGood); // 输出 true
```

#### 位运算符

用于对二进制位进行操作（了解即可，日常开发使用较少）：

| 运算符 | 说明           | 示例      | 结果（二进制） |
| ------ | -------------- | --------- | -------------- |
| `&`    | 按位与         | `5 & 3`   | 1（0001）      |
| `|`    | 按位或         | `5 | 3`   | 7（0111）      |
| `^`    | 按位异或       | `5 ^ 3`   | 6（0110）      |
| `~`    | 按位取反       | `~5`      | -6（补码）     |
| `<<`   | 左移           | `5 << 1`  | 10（1010）     |
| `>>`   | 右移（带符号） | `5 >> 1`  | 2（0010）      |
| `>>>`  | 右移（无符号） | `5 >>> 1` | 2（0010）      |

#### 赋值运算符

用于给变量赋值：

| 运算符 | 说明     | 示例     | 等价于      |
| ------ | -------- | -------- | ----------- |
| `=`    | 简单赋值 | `a = 5`  | -           |
| `+=`   | 加等于   | `a += 3` | `a = a + 3` |
| `-=`   | 减等于   | `a -= 3` | `a = a - 3` |
| `*=`   | 乘等于   | `a *= 3` | `a = a * 3` |
| `/=`   | 除等于   | `a /= 3` | `a = a / 3` |
| `%=`   | 取余等于 | `a %= 3` | `a = a % 3` |

注意：扩展赋值运算符隐含强制类型转换，例如：

```java
short s = 5;
s += 3; // 等价于 s = (short)(s + 3)，自动强制转换
```

#### 三元运算符

也叫条件运算符，是 Java 中唯一的三目运算符，用于简化 `if-else` 语句：
格式：`条件 ? 表达式1 : 表达式2;`
规则：如果条件为 `true`，执行表达式1；否则执行表达式2。

代码示例：

```java
int a = 5, b = 3;
// 求两个数的最大值
int max = a > b ? a : b;
System.out.println("最大值：" + max); // 输出 5
```

#### 运算符优先级

当表达式中包含多个运算符时，按优先级从高到低执行，优先级相同则从左到右执行（赋值运算符从右到左）。常用优先级顺序（从高到低）：

1. 括号：`()`
2. 单目运算符：`++`、`--`、`!`
3. 算术运算符：`*`、`/`、`%` → `+`、`-`
4. 关系运算符：`>`、`<`、`>=`、`<=` → `==`、`!=`
5. 逻辑运算符：`&&` → `||`
6. 三元运算符：`? :`
7. 赋值运算符：`=`、`+=`、`-=` 等

建议使用括号明确运算顺序，提高代码可读性。

### 控制流程

控制流程用于控制程序的执行顺序，分为条件语句、循环语句和跳转语句。

#### 条件语句

##### if-else 语句

用于根据条件选择执行不同的代码块，有三种形式：

**形式1：单分支 if**

```java
if (条件) {
    // 条件为 true 时执行的代码
}
```

**形式2：双分支 if-else**

```java
if (条件) {
    // 条件为 true 时执行的代码
} else {
    // 条件为 false 时执行的代码
}
```

**形式3：多分支 if-else if-else**

```java
if (条件1) {
    // 条件1为 true 时执行的代码
} else if (条件2) {
    // 条件2为 true 时执行的代码
} else {
    // 所有条件都为 false 时执行的代码
}
```

代码示例（判断成绩等级）：

```java
int score = 85;
if (score >= 90) {
    System.out.println("优秀");
} else if (score >= 80) {
    System.out.println("良好");
} else if (score >= 60) {
    System.out.println("及格");
} else {
    System.out.println("不及格");
}
// 输出 良好
```

##### switch 语句

用于根据一个固定值选择执行不同的代码块：

```java
switch (表达式) {
    case 值1:
        // 表达式等于值1时执行的代码
        break; // 跳出 switch，防止 case 穿透
    case 值2:
        // 表达式等于值2时执行的代码
        break;
    default:
        // 所有 case 都不匹配时执行的代码
}
```

- 表达式的类型：`byte`、`short`、`int`、`char`、枚举（Java 5+）、`String`（Java 7+）。
- case 穿透：如果某个 case 后没有 `break`，程序会继续执行后续 case 的代码，直到遇到 `break` 或 switch 结束。

代码示例（根据月份判断季节）：

```java
int month = 3;
switch (month) {
    case 3:
    case 4:
    case 5:
        System.out.println("春季");
        break;
    case 6:
    case 7:
    case 8:
        System.out.println("夏季");
        break;
    case 9:
    case 10:
    case 11:
        System.out.println("秋季");
        break;
    case 12:
    case 1:
    case 2:
        System.out.println("冬季");
        break;
    default:
        System.out.println("月份错误");
}
// 输出 春季
```

#### 循环语句

用于重复执行某段代码，直到满足退出条件。

##### for 循环

最常用的循环语句，适合已知循环次数的场景：

```java
for (初始化; 条件; 迭代) {
    // 循环体
}
```

执行流程：

1. 执行初始化语句（仅执行一次）。
2. 判断条件，若为 `true` 则执行循环体；若为 `false` 则结束循环。
3. 执行迭代语句（如 `i++`）。
4. 重复步骤 2-3。

代码示例（计算 1-100 的和）：

```java
int sum = 0;
for (int i = 1; i <= 100; i++) {
    sum += i;
}
System.out.println("1-100的和：" + sum); // 输出 5050
```

##### while 循环

适合未知循环次数的场景，先判断条件，再执行循环体：

```java
while (条件) {
    // 循环体
}
```

代码示例（猜数字游戏，简化版）：

```java
int target = 50;
int guess = 30;
while (guess != target) {
    System.out.println("猜的数字不对，继续猜");
    // 实际开发中可通过输入修改 guess
    guess = 50; // 模拟猜对
}
System.out.println("恭喜你猜对了！");
```

##### do-while 循环

与 while 类似，但先执行一次循环体，再判断条件，因此循环体至少执行一次：

```java
do {
    // 循环体
} while (条件);
```

代码示例：

```java
int i = 1;
do {
    System.out.println("i = " + i);
    i++;
} while (i <= 3);
// 输出 i=1, i=2, i=3
```

#### 跳转语句

##### break

用于跳出当前循环或 switch 语句：

```java
for (int i = 1; i <= 10; i++) {
    if (i == 5) {
        break; // 当 i=5 时，跳出整个循环
    }
    System.out.println("i = " + i);
}
// 输出 i=1 到 i=4
```

#### continue

用于跳过本次循环，继续下一次循环：

```java
for (int i = 1; i <= 10; i++) {
    if (i % 2 == 0) {
        continue; // 当 i 为偶数时，跳过本次循环
    }
    System.out.println("i = " + i);
}
// 输出 i=1, i=3, i=5, i=7, i=9
```

### 数组

数组是存储**相同类型数据**的容器，长度固定，在内存中连续存储。

#### 数组的声明

格式：

- 推荐：`数据类型[] 数组名;`
- 不推荐：`数据类型 数组名[];`（C 语言风格）

代码示例：

```java
// 声明一个 int 类型的数组
int[] nums;
// 声明一个 String 类型的数组
String[] names;
```

#### 数组的初始化

数组必须初始化后才能使用，初始化方式分为静态初始化和动态初始化。

##### 静态初始化

直接指定数组的元素值，系统自动计算长度：
格式：`数据类型[] 数组名 = {值1, 值2, ...};`
或 `数据类型[] 数组名 = new 数据类型[]{值1, 值2, ...};`

代码示例：

```java
// 静态初始化方式1
int[] nums = {1, 2, 3, 4, 5};
// 静态初始化方式2
String[] names = new String[]{"张三", "李四", "王五"};
```

##### 动态初始化

只指定数组的长度，系统自动为元素赋默认值（基本类型为 0/false，引用类型为 null）：
格式：`数据类型[] 数组名 = new 数据类型[长度];`

代码示例：

```java
// 动态初始化一个长度为 5 的 int 数组
int[] nums = new int[5];
// 动态初始化一个长度为 3 的 String 数组
String[] names = new String[3];
```

#### 数组的访问与遍历

##### 访问数组元素

通过**索引**（下标）访问数组元素，索引从 0 开始，范围为 `0 ~ 数组长度-1`：
格式：`数组名[索引]`

代码示例：

```java
int[] nums = {10, 20, 30};
System.out.println(nums[0]); // 输出 10
nums[1] = 200; // 修改索引为 1 的元素
System.out.println(nums[1]); // 输出 200
```

##### 获取数组长度

使用 `length` 属性获取数组长度：

```java
int[] nums = {1, 2, 3};
System.out.println("数组长度：" + nums.length); // 输出 3
```

##### 遍历数组

遍历数组有两种方式：

**方式1：普通 for 循环（可修改元素）**

```java
int[] nums = {10, 20, 30};
for (int i = 0; i < nums.length; i++) {
    System.out.println("nums[" + i + "] = " + nums[i]);
}
```

**方式2：增强 for 循环（for-each，只读，不可修改元素）**
Java 5+ 引入，用于简化数组和集合的遍历：

```java
int[] nums = {10, 20, 30};
for (int num : nums) {
    System.out.println("num = " + num);
}
```

#### 数组的常见操作

Java 提供了 `java.util.Arrays` 工具类，用于方便地操作数组：

| 方法名                          | 说明                       |
| ------------------------------- | -------------------------- |
| `Arrays.sort(数组)`             | 对数组进行升序排序         |
| `Arrays.toString(数组)`         | 将数组转换为字符串形式     |
| `Arrays.binarySearch(数组, 值)` | 二分查找指定值，返回索引   |
| `Arrays.fill(数组, 值)`         | 将数组所有元素填充为指定值 |

代码示例：

```java
import java.util.Arrays;

public class ArrayDemo {
    public static void main(String[] args) {
        int[] nums = {5, 2, 8, 1, 9};
        
        // 排序
        Arrays.sort(nums);
        System.out.println(Arrays.toString(nums)); // 输出 [1, 2, 5, 8, 9]
        
        // 二分查找（必须先排序）
        int index = Arrays.binarySearch(nums, 5);
        System.out.println("5的索引：" + index); // 输出 2
        
        // 填充
        Arrays.fill(nums, 10);
        System.out.println(Arrays.toString(nums)); // 输出 [10, 10, 10, 10, 10]
    }
}
```

#### 多维数组（以二维数组为例）

二维数组本质是数组的数组，即每个元素是一个一维数组。

##### 声明与初始化

```java
// 静态初始化
int[][] arr = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
// 动态初始化（指定行数和列数）
int[][] arr2 = new int[3][4];
// 动态初始化（只指定行数，列数后续指定）
int[][] arr3 = new int[3][];
arr3[0] = new int[2];
arr3[1] = new int[3];
```

##### 遍历二维数组

使用嵌套 for 循环：

```java
int[][] arr = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
for (int i = 0; i < arr.length; i++) {
    for (int j = 0; j < arr[i].length; j++) {
        System.out.print(arr[i][j] + " ");
    }
    System.out.println();
}
/* 输出：
1 2 3 
4 5 6 
7 8 9 
*/
```

### 方法

方法是完成特定功能的代码块，可重复调用，提高代码复用性。

#### 方法的定义与调用

##### 方法的定义

格式：

```java
修饰符 返回值类型 方法名(参数列表) {
    // 方法体
    return 返回值;
}
```

各部分说明：

- **修饰符**：如 `public`、`private`、`static` 等（暂时使用 `public static` 方便调用）。
- **返回值类型**：方法执行后返回的数据类型，无返回值则写 `void`。
- **方法名**：采用小驼峰命名法，见名知意。
- **参数列表**：方法执行时需要的参数，多个参数用逗号分隔，分为形参（定义时）和实参（调用时）。
- **return**：用于返回值并结束方法，若返回值类型为 `void`，可省略 `return`。

代码示例（定义一个求和方法）：

```java
public class MethodDemo {
    // 定义一个求和方法，接收两个 int 参数，返回它们的和
    public static int add(int a, int b) {
        int sum = a + b;
        return sum;
    }
}
```

##### 方法的调用

格式：`方法名(实参列表);`

代码示例：

```java
public class MethodDemo {
    public static int add(int a, int b) {
        return a + b;
    }

    public static void main(String[] args) {
        // 调用 add 方法，传入实参 10 和 20
        int result = add(10, 20);
        System.out.println("结果：" + result); // 输出 30
    }
}
```

#### 方法重载

方法重载是指在**同一个类中**，方法名**相同**，但**参数列表不同**（参数个数、类型、顺序不同）的现象，与返回值类型、修饰符无关。

代码示例（多个求和方法）：

```java
public class OverloadDemo {
    // 两个 int 求和
    public static int add(int a, int b) {
        return a + b;
    }

    // 三个 int 求和（参数个数不同）
    public static int add(int a, int b, int c) {
        return a + b + c;
    }

    // 两个 double 求和（参数类型不同）
    public static double add(double a, double b) {
        return a + b;
    }

    public static void main(String[] args) {
        System.out.println(add(10, 20)); // 调用第一个 add，输出 30
        System.out.println(add(10, 20, 30)); // 调用第二个 add，输出 60
        System.out.println(add(1.5, 2.5)); // 调用第三个 add，输出 4.0
    }
}
```

#### 方法的参数传递

Java 中方法的参数传递是**值传递**：

- 对于**基本数据类型**：传递的是值的副本，修改形参不影响实参。
- 对于**引用数据类型**：传递的是引用的副本（内存地址），修改形参指向的对象内容会影响实参，但重新赋值形参不影响实参。

代码示例（基本类型参数传递）：

```java
public class ParamPassDemo {
    public static void change(int num) {
        num = 100; // 修改形参
    }

    public static void main(String[] args) {
        int a = 10;
        change(a);
        System.out.println("a = " + a); // 输出 10，实参未被修改
    }
}
```

代码示例（引用类型参数传递）：

```java
public class ParamPassDemo2 {
    public static void change(int[] arr) {
        arr[0] = 100; // 修改形参指向的数组内容
        // arr = new int[]{4,5,6}; // 若重新赋值 arr，实参不受影响
    }

    public static void main(String[] args) {
        int[] nums = {1, 2, 3};
        change(nums);
        System.out.println("nums[0] = " + nums[0]); // 输出 100，实参被修改
    }
}
```

#### 可变参数

Java 5+ 引入可变参数，用于接收**个数不确定**的同类型参数，本质是数组：
格式：`数据类型... 参数名`
注意：可变参数必须作为方法的**最后一个参数**。

代码示例（可变参数求和）：

```java
public class VarArgsDemo {
    // 可变参数 nums 本质是 int[]
    public static int sum(int... nums) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        return sum;
    }

    public static void main(String[] args) {
        System.out.println(sum(1, 2)); // 输出 3
        System.out.println(sum(1, 2, 3, 4, 5)); // 输出 15
        System.out.println(sum()); // 输出 0
    }
}
```



## 面向对象编程
面向对象的核心思想是将现实世界的事物抽象为对象，通过类和对象来组织代码。类是对象的模板，定义了对象的属性（成员变量）和行为（方法）；对象是类的实例，通过`new`关键字创建。

封装是指将对象的属性和方法封装在一起，通过访问修饰符（`public`、`private`、`protected`、默认）控制访问权限，通常将属性设为`private`，通过`getter`和`setter`方法访问和修改。

继承是指子类继承父类的属性和方法，使用`extends`关键字实现，Java只支持单继承。子类可以重写父类的方法（需满足方法名、参数列表、返回值类型相同，访问修饰符不低于父类），通过`super`关键字调用父类的构造方法或成员。

多态是指同一个方法在不同对象上有不同表现，包括编译时多态（方法重载）和运行时多态（方法重写）。实现多态的前提是继承、方法重写和父类引用指向子类对象。

抽象类使用`abstract`关键字修饰，不能被实例化，可以包含抽象方法（无方法体）和具体方法，子类必须实现所有抽象方法（除非子类也是抽象类）。接口使用`interface`关键字修饰，是抽象方法的集合，Java 8后可以包含默认方法和静态方法，类通过`implements`关键字实现接口，支持多实现。

### 类与对象
面向对象的核心思想是将现实世界的事物抽象为对象，通过类和对象组织代码。**类是对象的模板**，定义了对象的属性（成员变量）和行为（方法）；**对象是类的实例**，通过`new`关键字创建。

#### 类的定义
类的基本结构包括成员变量和成员方法：
```java
// 定义一个“人”类
public class Person {
    // 属性（成员变量）：描述对象的特征
    String name;
    int age;

    // 行为（成员方法）：描述对象的功能
    public void eat() {
        System.out.println(name + "在吃饭");
    }

    public void sleep() {
        System.out.println(name + "在睡觉");
    }
}
```

#### 对象的创建与使用
通过`new`关键字创建对象，然后访问其属性和方法：
```java
public class ObjectDemo {
    public static void main(String[] args) {
        // 创建Person类的对象
        Person person = new Person();
        // 给属性赋值
        person.name = "张三";
        person.age = 25;
        // 调用方法
        person.eat();  // 输出：张三在吃饭
        person.sleep(); // 输出：张三在睡觉
    }
}
```

### 封装
封装是指将对象的属性和方法封装在一起，通过**访问修饰符**控制访问权限，隐藏内部实现细节，仅暴露必要的接口。通常将属性设为`private`，通过`getter`和`setter`方法访问和修改，保证数据安全性。

#### 访问修饰符
| 修饰符         | 同一类内 | 同一包内 | 不同包的子类 | 不同包的非子类 |
| -------------- | -------- | -------- | ------------ | -------------- |
| `public`       | ✅        | ✅        | ✅            | ✅              |
| `protected`    | ✅        | ✅        | ✅            | ❌              |
| 默认（无修饰） | ✅        | ✅        | ❌            | ❌              |
| `private`      | ✅        | ❌        | ❌            | ❌              |

#### getter与setter方法
通过`getter`获取属性，`setter`修改属性，可在方法中添加逻辑控制属性合法性：
```java
public class Person {
    // 属性设为private，外部无法直接访问
    private String name;
    private int age;

    // getter方法：获取name
    public String getName() {
        return name;
    }

    // setter方法：设置name
    public void setName(String name) {
        this.name = name; // this指代当前对象
    }

    // getter方法：获取age
    public int getAge() {
        return age;
    }

    // setter方法：设置age，添加合法性判断
    public void setAge(int age) {
        if (age > 0 && age < 150) {
            this.age = age;
        } else {
            System.out.println("年龄不合法");
        }
    }
}
```

### 继承
继承是指子类继承父类的属性和方法，使用`extends`关键字实现，实现代码复用。**Java只支持单继承**（一个类只能有一个直接父类），避免了多继承的菱形问题。

#### 继承的基本使用
```java
// 父类：动物
public class Animal {
    String name;

    public void eat() {
        System.out.println(name + "在吃东西");
    }
}

// 子类：狗，继承Animal
public class Dog extends Animal {
    // 子类特有属性
    String breed;

    // 子类特有方法
    public void bark() {
        System.out.println(name + "在汪汪叫");
    }
}

// 测试
public class InheritanceDemo {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.name = "旺财"; // 继承父类的属性
        dog.eat();         // 继承父类的方法
        dog.breed = "金毛";// 子类特有属性
        dog.bark();        // 子类特有方法
    }
}
```

#### 方法重写
子类可以重写父类的方法，实现特定功能。重写需满足：
- 方法名、参数列表、返回值类型与父类一致（Java 5+支持协变返回类型）；
- 访问修饰符不低于父类；
- 不能抛出比父类更宽泛的异常。

使用`@Override`注解可检查重写是否正确：
```java
public class Animal {
    public void eat() {
        System.out.println("动物在吃东西");
    }
}

public class Dog extends Animal {
    // 重写父类的eat方法
    @Override
    public void eat() {
        System.out.println("狗在吃骨头");
    }
}
```

#### super关键字
`super`用于指代父类对象，可：
- 调用父类的构造方法（必须在子类构造方法的第一行）；
- 调用父类的成员变量或方法。

```java
public class Animal {
    String name;

    public Animal(String name) {
        this.name = name;
    }
}

public class Dog extends Animal {
    String breed;

    public Dog(String name, String breed) {
        super(name); // 调用父类构造方法
        this.breed = breed;
    }

    public void show() {
        System.out.println("名字：" + super.name); // 调用父类属性
    }
}
```

### 多态
多态是指同一个方法在不同对象上有不同表现，分为**编译时多态**（方法重载）和**运行时多态**（方法重写）。

#### 运行时多态的实现前提
1. 继承：子类继承父类；
2. 方法重写：子类重写父类方法；
3. 父类引用指向子类对象：`父类类型 变量名 = new 子类类型();`

```java
public class Animal {
    public void makeSound() {
        System.out.println("动物发出声音");
    }
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("汪汪汪");
    }
}

public class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("喵喵喵");
    }
}

// 测试多态
public class PolymorphismDemo {
    public static void main(String[] args) {
        // 父类引用指向子类对象
        Animal dog = new Dog();
        Animal cat = new Cat();
        // 调用重写的方法，表现出不同行为
        dog.makeSound(); // 输出：汪汪汪
        cat.makeSound(); // 输出：喵喵喵
    }
}
```

### 抽象类与接口
#### 抽象类
使用`abstract`关键字修饰，不能被实例化，可以包含抽象方法（无方法体）和具体方法。子类必须实现所有抽象方法，除非子类也是抽象类。

```java
// 抽象类：动物
public abstract class Animal {
    String name;

    // 具体方法
    public void eat() {
        System.out.println(name + "在吃东西");
    }

    // 抽象方法：无方法体，子类必须实现
    public abstract void makeSound();
}

// 子类：狗
public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("汪汪汪");
    }
}
```

#### 接口
使用`interface`关键字修饰，是抽象方法的集合。Java 8后可以包含默认方法（`default`）和静态方法（`static`）。类通过`implements`关键字实现接口，支持多实现。

```java
// 接口：飞行能力
public interface Flyable {
    // 抽象方法（默认public abstract）
    void fly();

    // Java 8+默认方法
    default void glide() {
        System.out.println("滑翔");
    }

    // Java 8+静态方法
    static void stop() {
        System.out.println("停止飞行");
    }
}

// 类实现接口
public class Bird implements Flyable {
    @Override
    public void fly() {
        System.out.println("鸟在飞");
    }
}
```

#### 抽象类与接口的对比
| 特性      | 抽象类                 | 接口                         |
| --------- | ---------------------- | ---------------------------- |
| 关键字    | `abstract class`       | `interface`                  |
| 继承/实现 | 单继承（extends）      | 多实现（implements）         |
| 构造方法  | 有                     | 无                           |
| 成员变量  | 可包含各种类型         | 默认`public static final`    |
| 方法类型  | 抽象方法、具体方法     | 抽象方法、默认方法、静态方法 |
| 设计目的  | 代码复用，提取公共特征 | 定义行为规范，实现多态       |

## 异常处理
异常是程序运行时出现的错误，分为受检异常（Checked Exception）和非受检异常（Unchecked Exception）。受检异常需在代码中显式处理，如`IOException`；非受检异常包括运行时异常（`RuntimeException`）和错误（`Error`），如`NullPointerException`、`OutOfMemoryError`。

异常处理通过`try-catch-finally`语句实现，`try`块中放置可能抛出异常的代码，`catch`块捕获并处理异常，`finally`块中的代码无论是否发生异常都会执行（用于资源释放）。可以使用多个`catch`块捕获不同类型的异常，子类异常需放在父类异常之前。

`throws`关键字用于在方法声明中抛出异常，`throw`关键字用于在方法中手动抛出异常对象。自定义异常需继承`Exception`（受检）或`RuntimeException`（非受检）。

### 异常的概念与分类
异常是程序运行过程中出现的非正常情况（如文件不存在、数组越界、空指针访问等），会导致程序中断执行。Java将异常封装为对象，通过异常机制统一处理错误，提高程序的健壮性。

根据是否需要显式处理，异常分为两类：

#### 受检异常（Checked Exception）
编译时会被检查的异常，必须在代码中显式处理（捕获或抛出），否则编译不通过。通常由外部环境或操作导致，程序可预见且可恢复。
- 常见示例：`IOException`（输入输出异常）、`SQLException`（数据库异常）、`ClassNotFoundException`（类未找到异常）。

#### 非受检异常（Unchecked Exception）
编译时不检查的异常，无需显式处理。分为两类：
1. **运行时异常（RuntimeException）**：由程序逻辑错误导致，可通过代码优化避免。
   - 常见示例：`NullPointerException`（空指针异常）、`ArrayIndexOutOfBoundsException`（数组越界异常）、`IllegalArgumentException`（非法参数异常）。
2. **错误（Error）**：由JVM或系统层面问题导致，程序无法处理，通常会导致程序终止。
   - 常见示例：`OutOfMemoryError`（内存溢出错误）、`StackOverflowError`（栈溢出错误）。

### 异常处理的核心机制：try-catch-finally
Java通过`try-catch-finally`语句块处理异常，将可能出错的代码与错误处理代码分离。

#### try块
用于放置**可能抛出异常的代码**，若try块中无异常，跳过catch块直接执行后续代码；若有异常，立即跳转到对应的catch块。

#### catch块
用于**捕获并处理异常**，可定义多个catch块捕获不同类型的异常。
- 注意：子类异常需放在父类异常之前（否则子类异常会被父类catch块拦截，导致编译错误）。

#### finally块
用于**资源释放**（如关闭文件、数据库连接等），无论是否发生异常，finally块中的代码都会执行（即使try或catch块中有return语句）。

代码示例：
```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;

public class ExceptionDemo {
    public static void main(String[] args) {
        FileInputStream fis = null;
        try {
            // 可能抛出FileNotFoundException（IOException子类）
            File file = new File("test.txt");
            fis = new FileInputStream(file);
            // 可能抛出IOException
            int data = fis.read();
            System.out.println("读取到的数据：" + data);
        } catch (IOException e) { // 捕获IOException及其子类
            // 处理异常：打印异常信息
            e.printStackTrace();
            System.out.println("文件操作失败：" + e.getMessage());
        } finally {
            // 释放资源：关闭文件流
            try {
                if (fis != null) {
                    fis.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### throws与throw关键字
#### throws关键字
用于**在方法声明中抛出异常**，告诉方法调用者该方法可能抛出的异常类型，调用者需处理这些异常（捕获或继续抛出）。
- 格式：`修饰符 返回值类型 方法名(参数列表) throws 异常类型1, 异常类型2, ...`

代码示例：
```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;

public class ThrowsDemo {
    // 方法声明抛出IOException，调用者需处理
    public static void readFile() throws IOException {
        File file = new File("test.txt");
        FileInputStream fis = new FileInputStream(file);
        int data = fis.read();
        System.out.println("读取到的数据：" + data);
        fis.close();
    }

    public static void main(String[] args) {
        try {
            readFile(); // 调用抛出异常的方法，需捕获
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### throw关键字
用于**在方法中手动抛出异常对象**，通常用于业务逻辑不满足条件时抛出异常。
- 格式：`throw 异常对象;`

代码示例：
```java
public class ThrowDemo {
    // 定义一个除法方法，当除数为0时手动抛出异常
    public static int divide(int a, int b) {
        if (b == 0) {
            // 手动抛出IllegalArgumentException（运行时异常）
            throw new IllegalArgumentException("除数不能为0");
        }
        return a / b;
    }

    public static void main(String[] args) {
        try {
            int result = divide(10, 0);
            System.out.println("结果：" + result);
        } catch (IllegalArgumentException e) {
            System.out.println("操作失败：" + e.getMessage());
        }
    }
}
```

### 自定义异常
Java内置异常无法满足所有业务场景时，可自定义异常类。
- 步骤：
  1. 继承`Exception`（受检异常，需显式处理）或`RuntimeException`（非受检异常，无需显式处理）；
  2. 提供无参构造方法和带异常信息的有参构造方法。

代码示例：
```java
// 自定义受检异常：业务异常
public class BusinessException extends Exception {
    // 无参构造
    public BusinessException() {}

    // 带异常信息的有参构造
    public BusinessException(String message) {
        super(message);
    }
}

// 使用自定义异常
public class CustomExceptionDemo {
    // 模拟用户登录方法，用户名或密码错误时抛出业务异常
    public static void login(String username, String password) throws BusinessException {
        if (!"admin".equals(username)) {
            throw new BusinessException("用户名不存在");
        }
        if (!"123456".equals(password)) {
            throw new BusinessException("密码错误");
        }
        System.out.println("登录成功");
    }

    public static void main(String[] args) {
        try {
            login("admin", "123456"); // 登录成功
            login("user", "123456");  // 抛出用户名不存在异常
        } catch (BusinessException e) {
            System.out.println("登录失败：" + e.getMessage());
        }
    }
}
```

## 集合框架
Java集合框架位于`java.util`包下，主要分为`Collection`和`Map`两大接口体系。`Collection`接口下有`List`、`Set`、`Queue`等子接口。

`List`接口存储有序、可重复的元素，常用实现类有`ArrayList`和`LinkedList`。`ArrayList`基于动态数组实现，查询效率高，增删效率低；`LinkedList`基于双向链表实现，增删效率高，查询效率低。

`Set`接口存储无序、不可重复的元素，常用实现类有`HashSet`、`LinkedHashSet`和`TreeSet`。`HashSet`基于哈希表实现，存取速度快；`LinkedHashSet`维护元素的插入顺序；`TreeSet`基于红黑树实现，可对元素排序。

`Map`接口存储键值对（`key-value`），键不可重复，常用实现类有`HashMap`、`LinkedHashMap`和`TreeMap`。`HashMap`基于哈希表实现，是最常用的Map实现；`LinkedHashMap`维护键值对的插入顺序；`TreeMap`基于红黑树实现，可对键排序。

遍历集合可使用`Iterator`迭代器、增强for循环（`for-each`）或`forEach`方法（Java 8+）。

## IO流
Java IO流用于处理数据的输入输出，位于`java.io`包下。按数据流向分为输入流和输出流，按数据类型分为字节流和字符流。

字节流以字节为单位处理数据，适用于处理二进制文件（如图片、视频）。抽象基类为`InputStream`和`OutputStream`，常用实现类有`FileInputStream`、`FileOutputStream`、`BufferedInputStream`、`BufferedOutputStream`（缓冲流可提高读写效率）。

字符流以字符为单位处理数据，适用于处理文本文件。抽象基类为`Reader`和`Writer`，常用实现类有`FileReader`、`FileWriter`、`BufferedReader`、`BufferedWriter`。

Java NIO（New IO）位于`java.nio`包下，是更高效的IO方式，基于通道（`Channel`）和缓冲区（`Buffer`）实现，支持非阻塞IO。核心组件包括`Buffer`、`Channel`、`Selector`。

## 多线程
线程是程序执行的最小单位，Java多线程编程位于`java.lang`和`java.util.concurrent`包下。创建线程的方式有三种：继承`Thread`类并重写`run`方法、实现`Runnable`接口并实现`run`方法、实现`Callable`接口并实现`call`方法（支持返回值和抛出异常）。

线程的生命周期包括新建（New）、就绪（Runnable）、运行（Running）、阻塞（Blocked）、死亡（Dead）五种状态，通过`start`方法启动线程，`sleep`方法使线程休眠，`wait`方法使线程等待，`notify`或`notifyAll`方法唤醒等待的线程。

线程安全是多线程编程的核心问题，可通过`synchronized`关键字（同步方法或同步块）或`Lock`接口（常用实现类`ReentrantLock`）实现同步。`volatile`关键字可保证变量的可见性和禁止指令重排序，但不能保证原子性。

线程池可提高线程的管理和复用效率，位于`java.util.concurrent`包下，通过`Executors`工具类创建常用线程池，如`FixedThreadPool`、`CachedThreadPool`、`SingleThreadExecutor`，或直接使用`ThreadPoolExecutor`自定义线程池。

并发工具类包括`CountDownLatch`（计数器）、`CyclicBarrier`（循环屏障）、`Semaphore`（信号量）、`ConcurrentHashMap`（线程安全的HashMap）等。

## 反射机制
反射允许程序在运行时动态获取类的信息、创建对象、调用方法、访问属性，位于`java.lang.reflect`包下。获取`Class`对象的方式有三种：类名.class、对象.getClass()、Class.forName("全类名")。

通过`Class`对象可获取类的构造方法（`Constructor`）、方法（`Method`）、属性（`Field`），并可调用构造方法创建对象，调用方法执行操作，访问属性获取或设置值。反射在框架开发中广泛应用，如Spring的IoC容器。

## 注解
注解是Java 5引入的元数据，用于为代码提供额外信息，不直接影响代码执行。注解分为内置注解（如`@Override`、`@Deprecated`、`@SuppressWarnings`）和自定义注解。

自定义注解使用`@interface`关键字，需指定元注解：`@Target`（注解的作用目标，如类、方法、属性）、`@Retention`（注解的保留策略，如源码、类文件、运行时）、`@Documented`（是否包含在Javadoc中）、`@Inherited`（是否可被子类继承）。

注解可通过反射在运行时解析，获取注解信息并执行相应操作。

## Java 8及以后新特性
Lambda表达式是Java 8引入的函数式编程特性，用于简化匿名内部类的写法，格式为`(参数列表) -> {方法体}`。函数式接口是只包含一个抽象方法的接口，可使用`@FunctionalInterface`注解标记，常用函数式接口位于`java.util.function`包下，如`Predicate`、`Function`、`Consumer`、`Supplier`。

Stream API用于处理集合数据，支持链式调用，可进行过滤、映射、排序、聚合等操作，分为中间操作（如`filter`、`map`、`sorted`）和终端操作（如`collect`、`forEach`、`reduce`）。

`Optional`类用于解决空指针异常问题，可包装可能为`null`的对象，提供`isPresent`、`ifPresent`、`orElse`等方法安全处理空值。

日期时间API位于`java.time`包下，替代了旧的`Date`和`Calendar`类，核心类包括`LocalDate`（日期）、`LocalTime`（时间）、`LocalDateTime`（日期时间）、`DateTimeFormatter`（格式化）。

## 数据库编程
Java数据库连接（JDBC）是Java与数据库交互的标准API，位于`java.sql`和`javax.sql`包下。使用JDBC的步骤包括：加载驱动（`Class.forName("驱动类名")`，MySQL 8+驱动类为`com.mysql.cj.jdbc.Driver`）、获取连接（`DriverManager.getConnection(url, username, password)`）、创建语句对象（`Statement`或`PreparedStatement`，`PreparedStatement`可防止SQL注入且效率更高）、执行SQL语句（`executeQuery`用于查询，返回`ResultSet`；`executeUpdate`用于增删改，返回影响行数）、处理结果集、关闭资源（`ResultSet`、`Statement`、`Connection`）。

数据库连接池可提高连接的管理和复用效率，常用连接池有HikariCP、Druid、C3P0，HikariCP性能最优，Spring Boot默认使用HikariCP。

## Spring框架
Spring是一个轻量级的Java企业级开发框架，核心是控制反转（IoC）和面向切面编程（AOP）。IoC是指将对象的创建和依赖关系的管理交给Spring容器，实现了代码的解耦；DI（依赖注入）是IoC的实现方式，包括构造器注入、Setter注入、字段注入。

Spring容器的核心接口是`BeanFactory`和`ApplicationContext`，`ApplicationContext`是`BeanFactory`的子接口，功能更强大，常用实现类有`ClassPathXmlApplicationContext`（基于XML配置）、`AnnotationConfigApplicationContext`（基于注解配置）。

Bean的配置方式包括XML配置、注解配置（`@Component`、`@Service`、`@Repository`、`@Controller`）、Java配置（`@Configuration`、`@Bean`）。Bean的作用域包括`singleton`（单例，默认）、`prototype`（多例）、`request`、`session`、`globalSession`。

AOP是指将横切关注点（如日志、事务、安全）从业务逻辑中分离出来，通过动态代理实现。核心概念包括切面（`Aspect`）、连接点（`JoinPoint`）、切入点（`Pointcut`）、通知（`Advice`，包括前置通知、后置通知、环绕通知、异常通知、最终通知）、织入（`Weaving`）。AOP可通过XML配置或注解配置（`@Aspect`、`@Pointcut`、`@Before`、`@After`、`@Around`等）实现。

## Spring MVC
Spring MVC是基于Spring的Web框架，用于开发Web应用，核心是前端控制器`DispatcherServlet`。Spring MVC的处理流程包括：客户端发送请求、`DispatcherServlet`接收请求并调用`HandlerMapping`查找处理器、`HandlerAdapter`调用处理器（Controller）、Controller返回`ModelAndView`、`ViewResolver`解析视图、`DispatcherServlet`渲染视图并响应客户端。

Controller使用`@Controller`或`@RestController`注解（`@RestController`用于返回JSON数据），请求映射使用`@RequestMapping`、`@GetMapping`、`@PostMapping`等注解。参数绑定支持基本类型、`String`、POJO、`Map`、`Model`等，返回值支持`String`（视图名）、`ModelAndView`、`void`、JSON对象等。

## Spring Boot
Spring Boot是基于Spring的快速开发框架，核心特性是自动配置和起步依赖，可快速搭建独立的、生产级的Spring应用。自动配置是指Spring Boot根据项目中的依赖自动配置Spring应用，可通过`@EnableAutoConfiguration`或`@SpringBootApplication`注解启用（`@SpringBootApplication`包含`@Configuration`、`@EnableAutoConfiguration`、`@ComponentScan`）。

起步依赖是一组预定义的依赖集合，可简化Maven或Gradle配置，如`spring-boot-starter-web`用于Web开发，`spring-boot-starter-data-jpa`用于数据访问。Spring Boot应用的入口是`main`方法，使用`SpringApplication.run()`启动。

Spring Boot支持外部化配置，配置文件支持`properties`和`yaml`格式，默认配置文件为`application.properties`或`application.yml`，可通过`@Value`注解或`@ConfigurationProperties`注解注入配置属性。

## 项目管理工具
Maven是Java项目的构建和依赖管理工具，核心是`pom.xml`文件。`pom.xml`包含项目坐标（`groupId`、`artifactId`、`version`）、依赖配置、构建配置等。Maven的生命周期包括`clean`（清理）、`validate`（验证）、`compile`（编译）、`test`（测试）、`package`（打包）、`verify`（验证）、`install`（安装到本地仓库）、`deploy`（部署到远程仓库）。

Gradle是另一种项目管理工具，基于Groovy或Kotlin DSL，比Maven更灵活高效，构建脚本为`build.gradle`（Groovy）或`build.gradle.kts`（Kotlin）。

## 版本控制
Git是分布式版本控制系统，用于管理代码的版本和协作开发。常用Git命令包括：`git init`（初始化仓库）、`git clone`（克隆远程仓库）、`git add`（添加文件到暂存区）、`git commit`（提交到本地仓库）、`git status`（查看状态）、`git log`（查看提交历史）、`git branch`（分支管理）、`git checkout`（切换分支）、`git merge`（合并分支）、`git push`（推送到远程仓库）、`git pull`（拉取远程仓库）。

远程仓库托管平台有GitHub、GitLab、Gitee等，可通过SSH或HTTPS连接远程仓库。

## 测试
JUnit是Java单元测试框架，常用版本为JUnit 5（JUnit Jupiter）。JUnit 5的核心注解包括`@Test`（标记测试方法）、`@BeforeEach`（每个测试方法前执行）、`@AfterEach`（每个测试方法后执行）、`@BeforeAll`（所有测试方法前执行，需为静态方法）、`@AfterAll`（所有测试方法后执行，需为静态方法）。断言方法位于`org.junit.jupiter.api.Assertions`类，如`assertEquals`、`assertTrue`、`assertNotNull`。

Mockito是Java Mock框架，用于模拟依赖对象，隔离测试代码。常用注解包括`@Mock`（模拟对象）、`@InjectMocks`（注入模拟对象）、`@ExtendWith(MockitoExtension.class)`（启用Mockito）。常用方法包括`when().thenReturn()`（设置模拟对象的方法返回值）、`verify()`（验证模拟对象的方法是否被调用）。

## 进阶内容
JVM原理是Java开发的进阶知识，包括JVM内存结构（堆、栈、方法区、程序计数器、本地方法栈）、垃圾回收机制（GC算法：标记-清除、复制、标记-整理；GC收集器：Serial、Parallel、CMS、G1）、类加载过程（加载、验证、准备、解析、初始化）、类加载器（启动类加载器、扩展类加载器、应用类加载器、自定义类加载器）。

性能优化包括代码优化（如避免创建不必要的对象、使用合适的集合类、减少同步）、JVM调优（如调整堆大小、选择合适的GC收集器、设置GC参数）、数据库优化（如索引优化、SQL优化、分库分表）。

微服务是一种架构风格，将应用拆分为多个小型服务，每个服务独立部署和扩展。Spring Cloud是基于Spring Boot的微服务框架，提供服务发现（Eureka、Nacos）、配置中心（Spring Cloud Config、Nacos）、负载均衡（Ribbon、Spring Cloud LoadBalancer）、熔断降级（Hystrix、Sentinel）、网关（Spring Cloud Gateway）等组件。

设计模式是解决特定问题的最佳实践，常用设计模式包括单例模式、工厂模式、抽象工厂模式、策略模式、观察者模式、装饰器模式、代理模式、模板方法模式等。学习设计模式可提高代码的可维护性和可扩展性。

