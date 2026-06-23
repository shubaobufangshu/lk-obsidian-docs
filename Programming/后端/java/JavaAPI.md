# Java API 学习文档

## Java API 概述
Java API（Application Programming Interface，应用程序编程接口）是 JDK 提供的预定义类、接口和方法的集合，封装了常用功能（如字符串处理、集合操作、IO 流、网络通信等），开发者可直接调用，无需重复实现，大幅提升开发效率。

核心常用包：
- `java.lang`：Java 核心基础包（如 `Object`、`String`、`Math`），自动导入。
- `java.util`：工具包（如集合框架、日期时间、随机数）。
- `java.io`：输入输出流包（如文件读写、流操作）。
- `java.net`：网络编程包（如 Socket、URL）。
- `java.time`：Java 8+ 日期时间包（替代旧的 `Date`、`Calendar`）。


## java.lang 包（核心基础包）
### 2.1 Object 类（所有类的根类）
`Object` 是 Java 中所有类的父类，任何类都直接或间接继承自 `Object`，提供了对象通用的基础方法。

#### 常用方法
| 方法名                       | 功能说明                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| `boolean equals(Object obj)` | 比较两个对象是否“相等”（默认比较内存地址，子类通常重写为比较内容）。 |
| `int hashCode()`             | 返回对象的哈希码（与 `equals` 配合重写，保证“相等对象哈希码相同”）。 |
| `String toString()`          | 返回对象的字符串表示（默认返回“类名@哈希码十六进制”，子类通常重写）。 |
| `void wait()`                | 使当前线程等待，直到其他线程调用该对象的 `notify()` 或 `notifyAll()`。 |
| `void notify()`              | 唤醒在此对象监视器上等待的单个线程。                         |
| `void notifyAll()`           | 唤醒在此对象监视器上等待的所有线程。                         |

#### 代码示例
```java
public class ObjectDemo {
    public static void main(String[] args) {
        // 1. toString() 示例
        Object obj = new Object();
        System.out.println(obj.toString()); // 输出类似：java.lang.Object@1b6d3586

        // 2. equals() 示例（默认比较地址）
        String s1 = new String("hello");
        String s2 = new String("hello");
        System.out.println(s1 == s2);       // false（比较地址）
        System.out.println(s1.equals(s2));  // true（String重写了equals，比较内容）
    }
}
```


### 2.2 String 类（不可变字符串）
`String` 表示**不可变的字符序列**，一旦创建，其内容无法修改（修改会创建新对象）。

#### 构造方法
| 构造方法                  | 功能说明                                     |
| ------------------------- | -------------------------------------------- |
| `String()`                | 创建空字符串。                               |
| `String(String original)` | 根据已有字符串创建新字符串。                 |
| `String(char[] value)`    | 根据字符数组创建字符串。                     |
| `String(byte[] bytes)`    | 根据字节数组创建字符串（使用平台默认编码）。 |

#### 常用方法
| 方法名                                                       | 功能说明                                     |
| ------------------------------------------------------------ | -------------------------------------------- |
| `int length()`                                               | 返回字符串长度。                             |
| `char charAt(int index)`                                     | 返回指定索引位置的字符（索引从 0 开始）。    |
| `String substring(int beginIndex)`                           | 截取从 `beginIndex` 到末尾的子串。           |
| `String substring(int begin, int end)`                       | 截取从 `begin`（含）到 `end`（不含）的子串。 |
| `boolean equals(Object anObject)`                            | 比较字符串内容是否相等。                     |
| `boolean equalsIgnoreCase(String str)`                       | 忽略大小写比较字符串内容。                   |
| `int indexOf(String str)`                                    | 返回子串第一次出现的索引，未找到返回 -1。    |
| `int lastIndexOf(String str)`                                | 返回子串最后一次出现的索引。                 |
| `String[] split(String regex)`                               | 根据正则表达式分割字符串，返回字符串数组。   |
| `String replace(CharSequence target, CharSequence replacement)` | 替换字符串中的指定子串。                     |
| `char[] toCharArray()`                                       | 将字符串转为字符数组。                       |
| `String toLowerCase()`                                       | 转为小写。                                   |
| `String toUpperCase()`                                       | 转为大写。                                   |
| `boolean startsWith(String prefix)`                          | 判断是否以指定前缀开头。                     |
| `boolean endsWith(String suffix)`                            | 判断是否以指定后缀结尾。                     |

#### 代码示例
```java
public class StringDemo {
    public static void main(String[] args) {
        String str = "Hello,Java API";

        // 1. 长度、字符访问
        System.out.println("长度：" + str.length());          // 输出：13
        System.out.println("索引5的字符：" + str.charAt(5));  // 输出：,

        // 2. 截取子串
        System.out.println("截取从6开始：" + str.substring(6));        // 输出：Java API
        System.out.println("截取6-10：" + str.substring(6, 10));      // 输出：Java

        // 3. 查找、分割
        System.out.println("Java的索引：" + str.indexOf("Java"));      // 输出：6
        String[] arr = str.split(",");
        System.out.println("分割后：" + arr[0] + " | " + arr[1]);     // 输出：Hello | Java API

        // 4. 替换、转换
        System.out.println("替换Java为Python：" + str.replace("Java", "Python"));
        System.out.println("转大写：" + str.toUpperCase());             // 输出：HELLO,JAVA API
    }
}
```


### 2.3 StringBuilder 与 StringBuffer（可变字符串）
`String` 不可变，频繁修改会创建大量临时对象，效率低；`StringBuilder` 和 `StringBuffer` 是**可变字符串**，内部通过可变数组实现，修改效率高。

#### 两者对比
| 特性     | StringBuilder        | StringBuffer       |
| -------- | -------------------- | ------------------ |
| 线程安全 | 非线程安全（无同步） | 线程安全（有同步） |
| 效率     | 高                   | 低                 |
| 适用场景 | 单线程环境           | 多线程环境         |

#### 常用方法（两者通用）
| 方法名                                         | 功能说明                                              |
| ---------------------------------------------- | ----------------------------------------------------- |
| `StringBuilder append(String str)`             | 追加字符串到末尾（支持多种类型：int、char、数组等）。 |
| `StringBuilder insert(int offset, String str)` | 在指定索引位置插入字符串。                            |
| `StringBuilder delete(int start, int end)`     | 删除从 `start`（含）到 `end`（不含）的字符。          |
| `StringBuilder reverse()`                      | 反转字符串。                                          |
| `String toString()`                            | 转为 `String` 对象。                                  |

#### 代码示例
```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello");

        // 1. 追加
        sb.append(" ").append("Java");
        System.out.println("追加后：" + sb);  // 输出：Hello Java

        // 2. 插入
        sb.insert(5, ",");
        System.out.println("插入后：" + sb);  // 输出：Hello, Java

        // 3. 删除
        sb.delete(5, 6);
        System.out.println("删除后：" + sb);  // 输出：Hello Java

        // 4. 反转
        sb.reverse();
        System.out.println("反转后：" + sb);  // 输出：avaJ olleH

        // 5. 转为String
        String str = sb.toString();
    }
}
```


### 2.4 包装类（基本类型的对象表示）
Java 是面向对象语言，但 8 种基本类型（`byte`、`short`、`int`、`long`、`float`、`double`、`char`、`boolean`）不是对象，包装类将其封装为对象，便于在集合、泛型中使用。

#### 基本类型与包装类对应关系
| 基本类型 | 包装类    |
| -------- | --------- |
| byte     | Byte      |
| short    | Short     |
| int      | Integer   |
| long     | Long      |
| float    | Float     |
| double   | Double    |
| char     | Character |
| boolean  | Boolean   |

#### 自动装箱与拆箱
Java 5+ 支持自动装箱（基本类型→包装类）和自动拆箱（包装类→基本类型），简化代码。

#### 常用方法（以 Integer 为例）
| 方法名                                | 功能说明                                          |
| ------------------------------------- | ------------------------------------------------- |
| `static Integer valueOf(int i)`       | 将 int 转为 Integer 对象。                        |
| `static Integer valueOf(String s)`    | 将字符串转为 Integer 对象（字符串需为数字格式）。 |
| `int intValue()`                      | 将 Integer 转为 int（拆箱）。                     |
| `static int parseInt(String s)`       | 将字符串直接转为 int。                            |
| `static String toBinaryString(int i)` | 将 int 转为二进制字符串。                         |
| `static String toHexString(int i)`    | 将 int 转为十六进制字符串。                       |

#### 代码示例
```java
public class WrapperClassDemo {
    public static void main(String[] args) {
        // 1. 自动装箱与拆箱
        Integer i1 = 100;  // 自动装箱：int → Integer
        int i2 = i1;        // 自动拆箱：Integer → int

        // 2. 字符串与int转换
        String str = "123";
        int num = Integer.parseInt(str);  // 字符串→int
        System.out.println("字符串转int：" + num);  // 输出：123
        String newStr = Integer.toString(num);  // int→字符串

        // 3. 进制转换
        System.out.println("10的二进制：" + Integer.toBinaryString(10));  // 输出：1010
        System.out.println("10的十六进制：" + Integer.toHexString(10));    // 输出：a
    }
}
```


### 2.5 Math 类（数学工具类）
`Math` 提供常用的数学运算方法，所有方法都是静态的，直接通过类名调用。

#### 常用方法
| 方法名                                  | 功能说明                                 |
| --------------------------------------- | ---------------------------------------- |
| `static double abs(double a)`           | 返回绝对值。                             |
| `static double ceil(double a)`          | 向上取整（返回大于等于参数的最小整数）。 |
| `static double floor(double a)`         | 向下取整（返回小于等于参数的最大整数）。 |
| `static long round(double a)`           | 四舍五入。                               |
| `static double max(double a, double b)` | 返回两个数的最大值。                     |
| `static double min(double a, double b)` | 返回两个数的最小值。                     |
| `static double pow(double a, double b)` | 返回 a 的 b 次方。                       |
| `static double sqrt(double a)`          | 返回平方根。                             |
| `static double random()`                | 返回 0.0（含）到 1.0（不含）的随机数。   |

#### 代码示例
```java
public class MathDemo {
    public static void main(String[] args) {
        System.out.println("绝对值：" + Math.abs(-10.5));          // 输出：10.5
        System.out.println("向上取整：" + Math.ceil(3.1));          // 输出：4.0
        System.out.println("向下取整：" + Math.floor(3.9));         // 输出：3.0
        System.out.println("四舍五入：" + Math.round(3.5));         // 输出：4
        System.out.println("最大值：" + Math.max(10, 20));          // 输出：20
        System.out.println("2的3次方：" + Math.pow(2, 3));          // 输出：8.0
        System.out.println("平方根：" + Math.sqrt(16));              // 输出：4.0
        System.out.println("随机数：" + Math.random());              // 输出类似：0.123456
    }
}
```


### 2.6 System 类（系统级操作类）
`System` 提供系统级的操作和属性，所有方法都是静态的。

#### 常用字段
| 字段名                   | 功能说明                         |
| ------------------------ | -------------------------------- |
| `static PrintStream out` | 标准输出流（用于控制台输出）。   |
| `static InputStream in`  | 标准输入流（用于控制台输入）。   |
| `static PrintStream err` | 标准错误流（用于输出错误信息）。 |

#### 常用方法
| 方法名                                                       | 功能说明                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `static long currentTimeMillis()`                            | 返回当前时间与 1970 年 1 月 1 日 00:00:00 的毫秒差（用于计时）。 |
| `static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)` | 数组复制（src：源数组；srcPos：源数组起始位置；dest：目标数组；destPos：目标数组起始位置；length：复制长度）。 |
| `static void exit(int status)`                               | 退出 JVM（status=0 表示正常退出，非 0 表示异常退出）。       |

#### 代码示例
```java
public class SystemDemo {
    public static void main(String[] args) {
        // 1. 计时示例
        long start = System.currentTimeMillis();
        // 模拟耗时操作
        for (int i = 0; i < 1000000; i++);
        long end = System.currentTimeMillis();
        System.out.println("耗时：" + (end - start) + " 毫秒");

        // 2. 数组复制
        int[] src = {1, 2, 3, 4, 5};
        int[] dest = new int[5];
        System.arraycopy(src, 0, dest, 0, src.length);
        System.out.print("目标数组：");
        for (int num : dest) System.out.print(num + " ");  // 输出：1 2 3 4 5
    }
}
```


## java.util 包（工具包）
### 3.1 集合框架（Collection & Map）
集合框架是 Java 提供的用于存储和操作对象的容器，相比数组，集合长度可变、功能更丰富。核心分为 `Collection`（单列集合）和 `Map`（双列集合，键值对）两大体系。

#### 3.1.1 Collection 接口（单列集合根接口）
`Collection` 是所有单列集合的父接口，定义了集合通用的方法（如 `add`、`remove`、`contains`、`size` 等）。

##### 子接口 1：List（有序、可重复）
`List` 集合元素有序（按插入顺序存储）、可重复，支持通过索引访问元素。

| 实现类       | 特点说明                                                     |
| ------------ | ------------------------------------------------------------ |
| `ArrayList`  | 基于动态数组实现，**查询快、增删慢**，线程不安全，是最常用的 List 实现。 |
| `LinkedList` | 基于双向链表实现，**增删快、查询慢**，线程不安全，还实现了 `Deque` 接口（可作为队列、栈使用）。 |

###### List 常用方法
| 方法名                           | 功能说明                               |
| -------------------------------- | -------------------------------------- |
| `boolean add(E e)`               | 添加元素到末尾。                       |
| `void add(int index, E element)` | 在指定索引位置插入元素。               |
| `E get(int index)`               | 获取指定索引位置的元素。               |
| `E remove(int index)`            | 删除指定索引位置的元素，返回被删元素。 |
| `int size()`                     | 返回集合元素个数。                     |
| `boolean contains(Object o)`     | 判断集合是否包含指定元素。             |
| `Iterator<E> iterator()`         | 获取迭代器，用于遍历集合。             |

###### 代码示例
```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class ListDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();

        // 1. 添加元素
        list.add("Java");
        list.add("Python");
        list.add(1, "C++");  // 在索引1插入
        System.out.println("添加后：" + list);  // 输出：[Java, C++, Python]

        // 2. 获取、修改元素
        System.out.println("索引1的元素：" + list.get(1));  // 输出：C++
        list.set(1, "Go");  // 修改索引1的元素
        System.out.println("修改后：" + list);  // 输出：[Java, Go, Python]

        // 3. 删除元素
        list.remove(0);  // 删除索引0的元素
        System.out.println("删除后：" + list);  // 输出：[Go, Python]

        // 4. 遍历（三种方式）
        // 方式1：普通for循环（仅List支持）
        System.out.print("普通for遍历：");
        for (int i = 0; i < list.size(); i++) {
            System.out.print(list.get(i) + " ");
        }
        // 方式2：增强for循环
        System.out.print("\n增强for遍历：");
        for (String s : list) System.out.print(s + " ");
        // 方式3：迭代器（所有Collection通用）
        System.out.print("\n迭代器遍历：");
        Iterator<String> it = list.iterator();
        while (it.hasNext()) {
            System.out.print(it.next() + " ");
        }
    }
}
```


##### 子接口 2：Set（无序、不可重复）
`Set` 集合元素无序（不保证插入顺序）、不可重复（通过 `equals` 和 `hashCode` 保证）。

| 实现类    | 特点说明                                                     |
| --------- | ------------------------------------------------------------ |
| `HashSet` | 基于哈希表实现，**存取速度快**，线程不安全，是最常用的 Set 实现。 |
| `TreeSet` | 基于红黑树实现，**元素可排序**（自然排序或自定义排序），线程不安全。 |

###### Set 常用方法
与 `List` 类似，但无索引相关方法（如 `get`、`add(int index)`）。

###### 代码示例
```java
import java.util.HashSet;
import java.util.Set;

public class SetDemo {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();

        // 1. 添加元素（不可重复）
        set.add("Java");
        set.add("Python");
        set.add("Java");  // 重复元素，添加失败
        System.out.println("集合内容：" + set);  // 输出：[Java, Python]（顺序不保证）

        // 2. 判断、删除
        System.out.println("是否包含Java：" + set.contains("Java"));  // 输出：true
        set.remove("Python");
        System.out.println("删除后：" + set);  // 输出：[Java]

        // 3. 遍历（增强for或迭代器）
        for (String s : set) System.out.println(s);
    }
}
```


#### 3.1.2 Map 接口（双列集合，键值对）
`Map` 存储“键（Key）-值（Value）”对，键唯一、值可重复，通过键可快速查找值。

| 实现类    | 特点说明                                                     |
| --------- | ------------------------------------------------------------ |
| `HashMap` | 基于哈希表实现，**存取速度快**，线程不安全，是最常用的 Map 实现。 |
| `TreeMap` | 基于红黑树实现，**键可排序**，线程不安全。                   |

###### Map 常用方法
| 方法名                                | 功能说明                                                  |
| ------------------------------------- | --------------------------------------------------------- |
| `V put(K key, V value)`               | 添加键值对（若键已存在，覆盖旧值，返回旧值）。            |
| `V get(Object key)`                   | 根据键获取值（键不存在返回 null）。                       |
| `V remove(Object key)`                | 根据键删除键值对，返回被删值。                            |
| `boolean containsKey(Object key)`     | 判断是否包含指定键。                                      |
| `boolean containsValue(Object value)` | 判断是否包含指定值。                                      |
| `int size()`                          | 返回键值对个数。                                          |
| `Set<K> keySet()`                     | 获取所有键的 Set 集合。                                   |
| `Collection<V> values()`              | 获取所有值的 Collection 集合。                            |
| `Set<Map.Entry<K, V>> entrySet()`     | 获取所有键值对的 Set 集合（`Map.Entry` 表示一个键值对）。 |

###### 代码示例
```java
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

public class MapDemo {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();

        // 1. 添加键值对
        map.put("Java", 100);
        map.put("Python", 90);
        map.put("Java", 95);  // 键已存在，覆盖旧值
        System.out.println("Map内容：" + map);  // 输出：{Java=95, Python=90}

        // 2. 获取、删除
        System.out.println("Java的值：" + map.get("Java"));  // 输出：95
        map.remove("Python");
        System.out.println("删除后：" + map);  // 输出：{Java=95}

        // 3. 遍历（两种常用方式）
        // 方式1：遍历键，再通过键获取值
        System.out.println("遍历键值对（方式1）：");
        Set<String> keys = map.keySet();
        for (String key : keys) {
            System.out.println(key + " → " + map.get(key));
        }
        // 方式2：遍历键值对（推荐，效率更高）
        System.out.println("遍历键值对（方式2）：");
        Set<Map.Entry<String, Integer>> entries = map.entrySet();
        for (Map.Entry<String, Integer> entry : entries) {
            System.out.println(entry.getKey() + " → " + entry.getValue());
        }
    }
}
```


### 3.2 日期时间 API
#### 3.2.1 Java 8+ 新 API（java.time 包，推荐）
旧的 `Date`、`Calendar` 存在线程不安全、设计混乱等问题，Java 8 引入 `java.time` 包（基于 Joda-Time 设计），线程安全、API 清晰易用。

##### 核心类
| 类名                | 功能说明                                    |
| ------------------- | ------------------------------------------- |
| `LocalDate`         | 表示日期（年-月-日，无时区）。              |
| `LocalTime`         | 表示时间（时-分-秒，无时区）。              |
| `LocalDateTime`     | 表示日期时间（年-月-日 时-分-秒，无时区）。 |
| `DateTimeFormatter` | 日期时间格式化与解析。                      |

##### 常用方法
| 方法名                                                       | 功能说明                               |
| ------------------------------------------------------------ | -------------------------------------- |
| `static LocalDate now()`                                     | 获取当前日期。                         |
| `static LocalDate of(int year, int month, int day)`          | 指定年、月、日创建日期。               |
| `LocalDate plusDays(long days)`                              | 加指定天数（返回新对象，原对象不变）。 |
| `LocalDate minusMonths(long months)`                         | 减指定月数。                           |
| `int getYear()`                                              | 获取年份。                             |
| `Month getMonth()`                                           | 获取月份（返回 `Month` 枚举）。        |
| `int getDayOfMonth()`                                        | 获取日。                               |
| `String format(DateTimeFormatter formatter)`                 | 格式化日期时间为字符串。               |
| `static LocalDate parse(CharSequence text, DateTimeFormatter formatter)` | 将字符串解析为日期时间。               |

##### 代码示例
```java
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class JavaTimeDemo {
    public static void main(String[] args) {
        // 1. 获取当前日期、时间、日期时间
        LocalDate today = LocalDate.now();
        LocalDateTime now = LocalDateTime.now();
        System.out.println("今天：" + today);          // 输出类似：2026-04-08
        System.out.println("现在：" + now);            // 输出类似：2026-04-08T15:30:00

        // 2. 指定日期时间
        LocalDate date = LocalDate.of(2026, 10, 1);
        System.out.println("指定日期：" + date);        // 输出：2026-10-01

        // 3. 日期计算
        LocalDate tomorrow = today.plusDays(1);
        LocalDate lastMonth = today.minusMonths(1);
        System.out.println("明天：" + tomorrow);
        System.out.println("上个月今天：" + lastMonth);

        // 4. 获取日期字段
        System.out.println("年：" + today.getYear());
        System.out.println("月：" + today.getMonthValue());
        System.out.println("日：" + today.getDayOfMonth());

        // 5. 格式化与解析
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        // 格式化：日期时间→字符串
        String formatStr = now.format(formatter);
        System.out.println("格式化后：" + formatStr);  // 输出类似：2026-04-08 15:30:00
        // 解析：字符串→日期时间
        String str = "2026-10-01 12:00:00";
        LocalDateTime parseDateTime = LocalDateTime.parse(str, formatter);
        System.out.println("解析后：" + parseDateTime);
    }
}
```


### 3.3 Random 类（随机数生成）
`Random` 用于生成伪随机数，相比 `Math.random()`，功能更丰富（可生成指定范围的 int、long、double 等）。

#### 常用方法
| 方法名                   | 功能说明                                                     |
| ------------------------ | ------------------------------------------------------------ |
| `int nextInt()`          | 生成随机 int（范围：Integer.MIN_VALUE ~ Integer.MAX_VALUE）。 |
| `int nextInt(int bound)` | 生成 0（含）到 bound（不含）的随机 int。                     |
| `double nextDouble()`    | 生成 0.0（含）到 1.0（不含）的随机 double。                  |
| `boolean nextBoolean()`  | 生成随机 boolean。                                           |

#### 代码示例
```java
import java.util.Random;

public class RandomDemo {
    public static void main(String[] args) {
        Random random = new Random();

        // 1. 生成随机int
        System.out.println("随机int：" + random.nextInt());
        // 2. 生成0-9的随机int
        System.out.println("0-9随机数：" + random.nextInt(10));
        // 3. 生成1-100的随机int
        System.out.println("1-100随机数：" + (random.nextInt(100) + 1));
        // 4. 生成随机double
        System.out.println("0.0-1.0随机double：" + random.nextDouble());
        // 5. 生成随机boolean
        System.out.println("随机boolean：" + random.nextBoolean());
    }
}
```


## java.io 包（输入输出流）
IO 流用于处理数据的输入（读取）和输出（写入），按数据类型分为**字节流**（处理二进制数据，如图片、视频）和**字符流**（处理文本数据，如 txt 文件）。

### 4.1 字节流
#### 核心类
| 分类           | 输入流（读取）        | 输出流（写入）         |
| -------------- | --------------------- | ---------------------- |
| 基类           | `InputStream`         | `OutputStream`         |
| 文件流         | `FileInputStream`     | `FileOutputStream`     |
| 缓冲流（高效） | `BufferedInputStream` | `BufferedOutputStream` |

#### 代码示例：文件复制（字节流）
```java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class ByteStreamDemo {
    public static void main(String[] args) {
        // 源文件路径、目标文件路径
        String srcPath = "src.jpg";
        String destPath = "dest.jpg";

        // 使用 try-with-resources 自动关闭流（Java 7+）
        try (
            // 1. 创建字节输入流（带缓冲，提高效率）
            BufferedInputStream bis = new BufferedInputStream(new FileInputStream(srcPath));
            // 2. 创建字节输出流（带缓冲）
            BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream(destPath))
        ) {
            byte[] buffer = new byte[1024];  // 缓冲区，一次读取1024字节
            int len;  // 记录每次读取的字节数
            // 3. 循环读取、写入
            while ((len = bis.read(buffer)) != -1) {  // read返回-1表示读取结束
                bos.write(buffer, 0, len);  // 写入从0开始的len个字节
            }
            System.out.println("文件复制成功！");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```


### 4.2 字符流
#### 核心类
| 分类           | 输入流（读取）                           | 输出流（写入）   |
| -------------- | ---------------------------------------- | ---------------- |
| 基类           | `Reader`                                 | `Writer`         |
| 文件流         | `FileReader`                             | `FileWriter`     |
| 缓冲流（高效） | `BufferedReader`（支持 readLine() 读行） | `BufferedWriter` |

#### 代码示例：读写文本文件（字符流）
```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class CharStreamDemo {
    public static void main(String[] args) {
        String readPath = "read.txt";
        String writePath = "write.txt";

        try (
            // 1. 创建字符缓冲输入流（readLine() 一次读一行）
            BufferedReader br = new BufferedReader(new FileReader(readPath));
            // 2. 创建字符缓冲输出流
            BufferedWriter bw = new BufferedWriter(new FileWriter(writePath))
        ) {
            String line;
            // 3. 循环读取、写入
            while ((line = br.readLine()) != null) {  // readLine返回null表示读取结束
                bw.write(line);
                bw.newLine();  // 换行（跨平台兼容）
            }
            System.out.println("文本文件读写成功！");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```


## 如何使用官方 Java API 文档
官方文档是学习 Java API 最权威的资料，步骤如下：
1. **访问地址**：Oracle Java SE 文档（如 Java 17 文档：https://docs.oracle.com/en/java/javase/17/docs/api/）。
2. **搜索类/方法**：在搜索框输入类名（如 `String`）或方法名，点击进入类详情页。
3. **查看信息**：
   - 类顶部：类的功能说明、继承关系。
   - 构造方法：类的构造方法列表。
   - 方法摘要：所有方法的列表，点击方法名可查看详细说明（参数、返回值、异常）。


