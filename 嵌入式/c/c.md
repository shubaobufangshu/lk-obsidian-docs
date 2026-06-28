# 嵌入式C语言教程

## 一、嵌入式C语言概述

### 1.1 嵌入式C与标准C的关系
嵌入式C基于ANSI C标准，针对嵌入式系统（单片机、DSP、ARM等）进行扩展，核心差异在于：
- **直接操作硬件**：寄存器、内存映射IO、中断
- **资源受限**：RAM/Flash小、主频低、无操作系统
- **实时性要求**：中断响应、任务调度有严格时序
- **编译器扩展**：`__irq`、`__packed`、`section`等关键字

### 1.2 嵌入式C开发流程
编写C代码 → 交叉编译 → 链接生成可执行文件 → 烧录到目标板 → 调试

---

## 二、数据类型与内存模型

### 2.1 基本数据类型（32位平台为例）

| 类型 | 位数 | 取值范围 | 嵌入式常用场景 |
|------|------|----------|----------------|
| `char` | 8 | -128~127 | 字符、小整数 |
| `unsigned char` / `uint8_t` | 8 | 0~255 | 寄存器字节操作 |
| `short` / `int16_t` | 16 | -32768~32767 | 16位ADC采样值 |
| `unsigned short` / `uint16_t` | 16 | 0~65535 | 16位寄存器 |
| `int` / `int32_t` | 32 | -2G~2G | 通用整数运算 |
| `unsigned int` / `uint32_t` | 32 | 0~4G | 32位寄存器、地址 |
| `long` | 32 | 同int | 少用，避免移植问题 |
| `float` | 32 | 单精度浮点 | 无FPU时运算极慢 |
| `double` | 64 | 双精度浮点 | 嵌入式极少使用 |

**最佳实践**：优先使用 `<stdint.h>` 中的定长类型 `uint8_t`、`uint16_t`、`uint32_t`，保证跨平台一致性。

### 2.2 内存四区模型
嵌入式程序加载后内存分布（从低地址到高地址）：

1. **代码区（Text）**：存放程序指令，只读，位于Flash
2. **只读数据区（RO Data）**：`const`常量、字符串常量，位于Flash
3. **读写数据区（RW Data）**：初始化非0的全局变量/静态变量，程序启动时从Flash复制到RAM
4. **零初始化数据区（ZI Data）**：未初始化或初始化为0的全局变量，启动时自动清0，位于RAM
5. **栈（Stack）**：局部变量、函数调用现场，由编译器自动分配释放，向下增长
6. **堆（Heap）**：`malloc`动态分配内存，向上增长，嵌入式慎用

### 2.3 const关键字
```c
const uint8_t table[] = {1, 2, 3, 4};  // 存放在Flash，不占RAM
```
- 嵌入式中大量查表数据加`const`，节省宝贵的RAM资源
- `const`修饰指针：
  - `const uint8_t *p`：指向的数据不可改，指针可改
  - `uint8_t * const p`：指针不可改，指向的数据可改
  - `const uint8_t * const p`：都不可改

---

## 三、指针与硬件操作

### 3.1 指针核心概念
指针存放的是**内存地址**，嵌入式中地址对应寄存器、外设、RAM。

```c
uint32_t *p;          // 定义32位整型指针
p = (uint32_t *)0x40020000;  // 指向某外设基地址
*p = 0x12345678;      // 向该地址写入数据（操作寄存器）
```

### 3.2 寄存器操作宏定义
嵌入式最常用的写法：
```c
// 寄存器地址映射
#define PERIPH_BASE     ((uint32_t)0x40000000)
#define GPIOA_BASE      (PERIPH_BASE + 0x0800)
#define GPIOA_ODR       (*(volatile uint32_t *)(GPIOA_BASE + 0x0C))

// 操作寄存器
GPIOA_ODR |= (1 << 5);   // 置位第5位（引脚输出高电平）
GPIOA_ODR &= ~(1 << 5);  // 清零第5位（引脚输出低电平）
GPIOA_ODR ^= (1 << 5);   // 翻转第5位
```

### 3.3 指针与数组
- 数组名是指向数组首元素的常量指针
- `arr[i]` 等价于 `*(arr + i)`

```c
uint8_t buf[10];
uint8_t *p = buf;
*(p+2) = 0xFF;  // 等价于 buf[2] = 0xFF
```

### 3.4 函数指针
常用于回调函数、中断向量表、状态机：
```c
// 定义函数指针类型
typedef void (*callback_t)(uint32_t event);

// 注册回调
void register_callback(callback_t cb);
```

---

## 四、位操作（嵌入式核心技能）

### 4.1 基本位运算

| 运算符 | 含义 | 常用场景 |
|--------|------|----------|
| `&` | 按位与 | 清零特定位、读取某一位 |
| `|` | 按位或 | 置位特定位 |
| `^` | 按位异或 | 翻转特定位 |
| `~` | 按位取反 | 配合与操作清零 |
| `<<` | 左移 | 乘以2^n、构造位掩码 |
| `>>` | 右移 | 除以2^n、提取高位 |

### 4.2 常用位操作宏
```c
// 置位：将reg的第n位置1
#define SET_BIT(reg, n)    ((reg) |= (1U << (n)))

// 清零：将reg的第n位清0
#define CLR_BIT(reg, n)    ((reg) &= ~(1U << (n)))

// 翻转：将reg的第n位取反
#define TOG_BIT(reg, n)    ((reg) ^= (1U << (n)))

// 读取：获取reg的第n位值（0或1）
#define GET_BIT(reg, n)    (((reg) >> (n)) & 1U)

// 写入：将reg的第n位设为val（0或1）
#define WRITE_BIT(reg, n, val) \
    ((val) ? SET_BIT(reg, n) : CLR_BIT(reg, n))
```

### 4.3 位段（Bit Field）
结构体中按位定义字段，常用于寄存器映射，节省内存：
```c
typedef struct {
    uint32_t EN     : 1;    // 第0位：使能
    uint32_t MODE   : 2;    // 第1-2位：模式
    uint32_t SPEED  : 2;    // 第3-4位：速度
    uint32_t RES    : 27;   // 保留位
} CR_REG_Type;

// 访问方式
CR_REG_Type *cr = (CR_REG_Type *)0x40020000;
cr->EN = 1;
cr->MODE = 0b10;
```
**注意**：位段的内存布局由编译器决定，跨平台可移植性差，操作硬件寄存器时推荐直接用位运算。

---

## 五、volatile关键字

### 5.1 作用
告诉编译器该变量可能在**当前代码执行流之外**被修改，禁止编译器优化该变量的读写。

### 5.2 必须使用volatile的三种场景
1. **外设寄存器**：硬件随时可能改变寄存器值
2. **中断服务函数中修改的全局变量**：中断异步触发
3. **多线程/多任务共享的全局变量**

### 5.3 示例
```c
// 错误写法：编译器可能优化掉循环，直接判断为假
uint8_t flag = 0;
void wait_flag(void) {
    while(flag == 0);  // 编译器可能优化成死循环或直接跳过
}

// 正确写法
volatile uint8_t flag = 0;
void wait_flag(void) {
    while(flag == 0);  // 每次都从内存读取flag的值
}

// 寄存器定义必须加volatile
#define REG_DATA  (*(volatile uint32_t *)0x40000000)
```

---

## 六、中断编程

### 6.1 中断服务函数（ISR）特点
- 无参数、无返回值
- 执行时间尽可能短，不做耗时操作
- 共享变量加`volatile`
- 调用的函数要考虑重入性

### 6.2 典型写法
```c
volatile uint32_t g_tick = 0;

// 定时器中断服务函数（编译器扩展关键字__irq）
void SysTick_Handler(void) {
    g_tick++;  // 仅做计数，复杂逻辑放主循环
}

// 主循环中使用
void main(void) {
    uint32_t last_tick = 0;
    while(1) {
        if (g_tick - last_tick >= 1000) {
            last_tick = g_tick;
            do_periodic_task();  // 每秒执行一次
        }
    }
}
```

### 6.3 中断临界区
访问共享资源时，需关中断保护：
```c
// 进入临界区：关中断
__disable_irq();
// 操作共享资源
g_shared_data++;
// 退出临界区：开中断
__enable_irq();
```

---

## 七、内存管理

### 7.1 栈（Stack）
- 由编译器自动分配，存放局部变量、函数参数、返回地址
- 空间有限（通常几KB），溢出会导致程序跑飞
- 嵌入式避免在栈上开大数组

**栈溢出常见原因**：
- 局部数组过大
- 函数调用层级过深（递归）
- 中断嵌套层级多

### 7.2 堆（Heap）与动态内存
- `malloc/free`从堆分配释放
- **嵌入式慎用动态内存**：
  - 内存碎片导致后续分配失败
  - 分配时间不确定，影响实时性
  - 容易内存泄漏

**替代方案**：
- 预分配静态数组
- 使用内存池（固定大小块分配）
- 环形缓冲区

### 7.3 static关键字
三种用法：
1. **修饰局部变量**：延长生命周期，只初始化一次，函数退出值保留
   ```c
   void counter(void) {
       static int cnt = 0;  // 只在第一次调用时初始化
       cnt++;
   }
   ```
2. **修饰全局变量**：限制作用域仅当前文件，其他文件不可见
3. **修饰函数**：限制作用域仅当前文件，防止命名冲突

---

## 八、常用嵌入式编程模式

### 8.1 状态机
处理复杂逻辑时序，替代大段`if-else`

```c
typedef enum {
    STATE_IDLE,
    STATE_WAIT_KEY,
    STATE_PROCESS,
    STATE_DONE
} state_t;

state_t state = STATE_IDLE;

void state_machine(void) {
    switch(state) {
        case STATE_IDLE:
            if (start_condition()) state = STATE_WAIT_KEY;
            break;
        case STATE_WAIT_KEY:
            if (key_pressed()) state = STATE_PROCESS;
            break;
        case STATE_PROCESS:
            process_data();
            state = STATE_DONE;
            break;
        case STATE_DONE:
            state = STATE_IDLE;
            break;
    }
}
```

### 8.2 环形缓冲区（Ring Buffer）
用于串口、SPI等数据收发，解耦生产与消费

```c
#define RING_SIZE  128
typedef struct {
    uint8_t buf[RING_SIZE];
    uint16_t head;   // 写指针
    uint16_t tail;   // 读指针
} ring_buf_t;

// 写入一个字节
void ring_put(ring_buf_t *rb, uint8_t data) {
    uint16_t next = (rb->head + 1) % RING_SIZE;
    if (next != rb->tail) {  // 未满
        rb->buf[rb->head] = data;
        rb->head = next;
    }
}

// 读取一个字节
int ring_get(ring_buf_t *rb, uint8_t *data) {
    if (rb->head == rb->tail) return -1;  // 空
    *data = rb->buf[rb->tail];
    rb->tail = (rb->tail + 1) % RING_SIZE;
    return 0;
}
```

### 8.3 宏定义与条件编译
```c
// 调试打印，量产时屏蔽
#define DEBUG 1
#if DEBUG
    #define LOG(fmt, ...)  printf("[DEBUG] " fmt, ##__VA_ARGS__)
#else
    #define LOG(fmt, ...)  ((void)0)
#endif

// 平台适配
#ifdef STM32F1
    #define UART_BASE  0x40013800
#elif defined(STM32F4)
    #define UART_BASE  0x40004400
#endif
```

---

## 九、代码优化与可靠性

### 9.1 代码效率优化
1. **减少函数调用**：频繁调用的小函数用`static inline`
2. **用数组代替指针运算**：编译器优化更好
3. **避免浮点运算**：无FPU时用定点数替代
4. **查表法**：复杂计算预先生成表格
5. **变量尽量用32位**：32位CPU原生支持，8/16位反而可能额外指令

### 9.2 可靠性编程
1. **变量初始化**：全局变量默认0，局部变量必须手动初始化
2. **边界检查**：数组访问检查下标，防止越界
3. **看门狗**：定期喂狗，防止程序跑飞
4. **默认分支**：`switch`必须有`default`
5. **断言`assert`**：调试阶段检查参数合法性

### 9.3 常见坑点
- **未初始化的局部变量**：栈上随机值，行为不可预测
- **整型溢出**：运算结果超出类型范围
- **隐式类型转换**：无符号与有符号混合运算的陷阱
- **指针越界**：数组访问超范围，破坏其他内存
- **竞态条件**：主循环和中断同时修改同一变量

---

## 十、工程结构最佳实践

### 10.1 文件分层
```
project/
├── main.c          // 主程序入口
├── hal/            // 硬件抽象层
│   ├── gpio.c
│   ├── uart.c
│   └── timer.c
├── app/            // 应用逻辑
│   ├── task1.c
│   └── task2.c
├── include/        // 头文件
│   ├── hal_gpio.h
│   └── platform.h
└── bsp/            // 板级支持包
    └── board_config.h
```

### 10.2 头文件规范
- 必须加头文件保护宏，防止重复包含
- 只声明不定义（不写函数实现、不定义变量）
- 包含所需的头文件，减少依赖

```c
#ifndef __UART_H__
#define __UART_H__

#include <stdint.h>

void uart_init(uint32_t baudrate);
void uart_send_byte(uint8_t data);

#endif
```

---

## 学习路线建议
1. 掌握C语言基础语法
2. 学习单片机寄存器操作（GPIO、UART、定时器）
3. 理解中断机制与编程
4. 掌握常用外设驱动编写（SPI、I2C、ADC）
5. 学习实时操作系统（RT-Thread、FreeRTOS）
6. 研究通信协议（Modbus、CAN、TCP/IP）