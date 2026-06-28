# Python turtle 库完整教程

## 一、turtle 简介
`turtle`（海龟绘图）是 Python 内置的标准图形库，通过"海龟"在画布上移动来绘制图形，源自 Logo 语言，适合入门编程与计算机图形学基础学习。

- **特点**：命令式绘图、逻辑直观、内置无需安装、支持动画与交互
- **应用**：绘制几何图形、分形图案、简单动画、教学演示

---

## 二、基础概念

### 2.1 核心元素
- **画布 (Screen)**：绘图区域，默认白色背景
- **画笔 (Turtle)**：绘图执行者，默认箭头形状
- **坐标系**：画布中心为原点 `(0,0)`，x 轴向右为正，y 轴向上为正
- **方向**：默认朝右（0°），逆时针为角度正方向
- **默认画布大小**：400*300
+ **画布原点**：窗口正中心 `(0, 0)`
+ **初始默认朝向**：**正东（向右，0°）**
+ 角度规则：**逆时针为角度增加**
	+ 直接设置海龟**绝对角度**，不受当前朝向影响 setheading (angle) /seth (angle)
	+ 在**当前朝向基础**逆时针转指定度数 left (deg) /lt (deg)
	+ 当前朝向基础顺时针转指定度数  当前朝向基础顺时针转指定度数
	+ `t.heading()`返回海龟当前绝对角度（浮点数）

| 角度          | 朝向  | 通俗描述 |
| ----------- | --- | ---- |
| 0°          | 东   | 右    |
| 90°         | 北   | 上    |
| 180°        | 西   | 左    |
| 270° / -90° | 南   | 下    |

### 标准角度对应方向
### 2.2 导入与初始化
```python
import turtle            # 导入库
t = turtle.Turtle()      # 创建画笔对象
screen = turtle.Screen() # 获取画布对象
screen.title("我的绘图") # 设置窗口标题
screen.setup(width=800, height=600) # 设置窗口大小
```
---
```python
turtle.setup(width, height, startx, starty)   # 设置窗口大小和位置
turtle.screensize(canvwidth, canvheight, bg)  # 设置画布尺寸及背景色
turtle.bgcolor("black")                       # 设置背景颜色
turtle.title("我的画布")                       # 窗口标题
turtle.done() / turtle.mainloop()             # 进入事件循环，保持窗口
```
### 退出

```python
ts = turtle.getscreen()
ts.getcanvas().postscript(file="drawing.eps")  # 保存为 EPS 矢量图
turtle.bye()   # 关闭窗口
```
## 三、基础移动命令

### 3.1 前进与后退
| 命令 | 说明 |
|---|---|
| `t.forward(distance)` / `t.fd(d)` | 沿当前方向前进 distance 像素 |
| `t.backward(distance)` / `t.bk(d)` | 沿反方向后退 distance 像素 |

```python
t.forward(100)  # 前进100像素
t.backward(50)  # 后退50像素
```

### 3.2 转向命令
| 命令 | 说明 |
|---|---|
| `t.left(angle)` / `t.lt(a)` | 左转 angle 度 |
| `t.right(angle)` / `t.rt(a)` | 右转 angle 度 |
| `t.setheading(angle)` / `t.seth(a)` | 设置绝对朝向角度 |

```python
t.left(90)    # 左转90度
t.right(45)   # 右转45度
t.seth(180)   # 朝向正左方
```

| 方法                          | 说明                  |
| --------------------------- | ------------------- |
| `forward(d)` / `fd(d)`      | 向前移动 d 像素           |
| `backward(d)` / `bk(d)`     | 向后移动 d 像素           |
| `right(a)` / `rt(a)`        | 右转 a 度              |
| `left(a)` / `lt(a)`         | 左转 a 度              |
| `goto(x, y)`                | 移动到绝对坐标             |
| `setx(x)` / `sety(y)`       | 设置 x 或 y 坐标         |
| `setheading(h)` / `seth(h)` | 设置朝向角度（0 东，90 北）    |
| `home()`                    | 回到原点 (0,0)，朝向东方     |
| `circle(r, extent, steps)`  | 画圆/弧：半径、角度、步数（画多边形） |
| `dot(size, color)`          | 绘制实心圆点              |

### 3.3 定位命令
| 命令 | 说明 |
|---|---|
| `t.goto(x, y)` / `t.setpos(x,y)` | 移动到绝对坐标 (x,y) |
| `t.setx(x)` | 只改变 x 坐标 |
| `t.sety(y)` | 只改变 y 坐标 |
| `t.home()` | 回到原点，朝向复位 |

```python
t.goto(100, 100)  # 移动到(100,100)
t.home()          # 回到原点
```

### 3.4 抬笔与落笔
| 命令 | 说明 |
|---|---|
| `t.penup()` / `t.pu()` | 抬笔，移动不画线 |
| `t.pendown()` / `t.pd()` | 落笔，移动时画线 |

```python
t.penup()
t.goto(200, 0)
t.pendown()
t.forward(100)
```
### 3.5位置与方向查询

```python
t.pos()      # 返回 (x,y)
t.xcor()     # 返回 x 坐标
t.ycor()     # 返回 y 坐标
t.heading()  # 返回当前朝向角度
t.distance(x, y)  # 到某点的距离
```

---

## 四、画笔属性控制

### 4.1 画笔粗细与速度
```python
t.pensize(width)    # 设置线条粗细，默认1
t.width(width)      # 同上，别名
t.speed(speed)      # 设置绘制速度 0~10
# 0 = 最快（无动画），1最慢，10较快，默认6
t.pendown() / t.pd()        # 落笔
t.penup() / t.pu()          # 抬笔
```

### 4.2 颜色设置
```python
t.pencolor(color)   # 设置画笔颜色
t.fillcolor(color)  # 设置填充颜色
# 同时设置画笔和填充色
t.color(pencolor, fillcolor)

turtle.colormode(1.0)    # RGB取值为 0.0~1.0
turtle.colormode(255)    # RGB取值为 0~255 整数
t.pencolor(255, 100, 0)  # 需先设为 255 模式
```

颜色支持三种写法：
- 颜色名：`"red"`, `"blue"`, `"green"`
- 十六进制：`"#FF0000"`
- RGB 元组：`(255, 0, 0)`（需先 `screen.colormode(255)`）

### 4.3 画笔形状
```python
t.shape("turtle")   # 海龟形状
# 可选："arrow"(默认), "turtle", "circle", "square", "triangle", "classic"
t.shapesize(x, y, outline)   # 拉伸宽度、长度、轮廓宽度
t.hideturtle()      # 隐藏海龟
t.showturtle()      # 显示
t.isvisible()       # 是否可见
t.clear()           # 清除当前海龟绘制，不移动位置
t.reset()           # 清除绘制并重置海龟状态
```

### 4.4 显示与隐藏
```python
t.hideturtle() / t.ht()  # 隐藏画笔
t.showturtle() / t.st()  # 显示画笔
t.isvisible()            # 返回是否可见
```
### 4.5多海龟与克隆

```python
t2 = turtle.Turtle()      # 第二只海龟
t2.color("blue")
clone = t.clone()         # 克隆当前海龟
```
---

## 五、填充与图形绘制

### 5.1 颜色填充
```python
t.begin_fill()          # 开始填充
# 绘制图形轮廓
t.circle(100)
t.end_fill()            # 结束填充，自动闭合
```

### 5.2 画圆与圆弧
```python
t.circle(radius)                # 画完整圆
t.circle(radius, extent=angle)  # 画圆弧，extent为角度
t.circle(radius, steps=n)       # 画圆内接n边形
```

示例：
```python
t.circle(100)           # 半径100的圆
t.circle(100, 180)      # 半圆
t.circle(100, steps=6)  # 正六边形
```

### 5.3 画点
```python
t.dot(size=None, color=None)  # 在当前位置画圆点
t.dot(20, "red")              # 红色圆点，直径20
```

---

## 六、画布控制

### 6.1 画布基本设置
```python
screen.bgcolor("white")       # 背景色
screen.title("绘图窗口")      # 窗口标题
screen.setup(width=800, height=600, startx=100, starty=100)
screen.screensize(canvwidth, canvheight, bg)  # 画布实际大小
```

### 6.2 清屏与重置
```python
t.clear()           # 清空画笔绘制内容，画笔位置不变
screen.clear()      # 清空整个画布
t.reset()           # 清空并重置画笔到初始状态
```

### 6.3 保持窗口
```python
turtle.done()       # 程序结束后保持窗口，必须放在最后
screen.mainloop()   # 同上
```

---

## 七、循环与分支绘制经典图案

### 7.1 正方形
```python
for _ in range(4):
    t.forward(100)
    t.right(90)
```

### 7.2 正多边形通用公式
```python
n = 6        # 边数
length = 80  # 边长
for _ in range(n):
    t.forward(length)
    t.right(360 / n)
```

### 7.3 螺旋线
```python
for i in range(100):
    t.forward(i * 2)
    t.right(90)
```

### 7.4 彩色螺旋
```python
colors = ["red", "orange", "yellow", "green", "blue", "purple"]
t.speed(0)
for i in range(360):
    t.pencolor(colors[i % 6])
    t.width(i / 100 + 1)
    t.forward(i)
    t.left(59)
```

### 7.5 五角星
```python
t.color("red")
t.begin_fill()
for _ in range(5):
    t.forward(200)
    t.right(144)
t.end_fill()
```
### 7.6递归分形树
```python
def tree(branch_len):
    if branch_len > 5:
        t.forward(branch_len)
        t.right(20)
        tree(branch_len - 15)
        t.left(40)
        tree(branch_len - 15)
        t.right(20)
        t.backward(branch_len)

t.left(90)
t.penup()
t.backward(100)
t.pendown()
tree(75)
```
### 7.7随机漫步

```python
import random
t.speed(0)
t.pensize(2)
colors = ["red","green","blue","orange","purple"]
for _ in range(200):
    t.color(random.choice(colors))
    t.forward(30)
    t.setheading(random.randint(0,3)*90)
```
---

## 八、进阶功能

### 8.1 写文字
```python
t.write(arg, move=False, align="left", font=("Arial", 12, "normal"))
```
- `arg`：要写的文字内容
- `align`：对齐方式 `left/center/right`
- `font`：字体三元组 (字体名, 字号, 样式)

```python
t.penup()
t.goto(0, 100)
t.write("Hello Turtle", align="center", font=("微软雅黑", 24, "bold"))
```

### 8.2 事件监听（鼠标与键盘）
```python
turtle.onclick(fun)       # 鼠标点击调用 fun(x,y)
turtle.onkey(fun, key)    # 按键调用 fun，需先 listen()
turtle.listen()           # 启动键盘监听
turtle.onscreenclick(fun) # 屏幕点击事件
```
具体代码实现
```python
# 鼠标点击移动画笔
def move(x, y):
    t.goto(x, y)

screen.onclick(move)  # 左键点击触发

# 键盘控制
def go_forward():
    t.forward(10)

screen.onkey(go_forward, "Up")  # 按上箭头键
screen.listen()                 # 开始监听键盘事件
```

### 8.3 动画控制
```python
screen.tracer(n)     # 每n次刷新一次画面，0=手动刷新
screen.update()      # 手动刷新画面，配合tracer(0)实现流畅动画
```

动画示例：
```python
screen.tracer(0)  # 关闭自动刷新
for i in range(360):
    t.clear()
    t.circle(100)
    t.left(1)
    screen.update()
```

### 8.4 多个画笔
```python
t1 = turtle.Turtle()
t2 = turtle.Turtle()
t1.color("red")
t2.color("blue")
t1.forward(100)
t2.left(90)
t2.forward(100)
```

### 8.5 状态保存与恢复
```python
t.position() / t.pos()   # 获取当前坐标
t.heading()              # 获取当前朝向
t.xcor()                 # 获取x坐标
t.ycor()                 # 获取y坐标
t.towards(x, y)          # 指向某点的角度
t.distance(x, y)         # 到某点的距离
```

---

## 九、分形图形案例

### 9.1 科赫雪花
```python
def koch(length, depth):
    if depth == 0:
        t.forward(length)
    else:
        koch(length/3, depth-1)
        t.left(60)
        koch(length/3, depth-1)
        t.right(120)
        koch(length/3, depth-1)
        t.left(60)
        koch(length/3, depth-1)

t.speed(0)
koch(300, 4)
```

### 9.2 分形树
```python
def tree(branch_len, level):
    if level == 0:
        return
    t.pensize(level)
    t.forward(branch_len)
    t.right(20)
    tree(branch_len - 15, level - 1)
    t.left(40)
    tree(branch_len - 15, level - 1)
    t.right(20)
    t.backward(branch_len)

t.speed(0)
t.left(90)
t.penup()
t.backward(200)
t.pendown()
tree(120, 8)
```

---

## 十、常用函数速查表

### 移动
`forward()`, `backward()`, `left()`, `right()`, `goto()`, `setx()`, `sety()`, `setheading()`, `home()`

### 画笔控制
`penup()`, `pendown()`, `pensize()`, `pencolor()`, `fillcolor()`, `color()`, `speed()`, `shape()`, `hideturtle()`, `showturtle()`

### 图形
`circle()`, `dot()`, `begin_fill()`, `end_fill()`

### 画布
`Screen()`, `bgcolor()`, `title()`, `setup()`, `clear()`, `reset()`, `tracer()`, `update()`, `mainloop()`, `done()`

### 文本与事件
`write()`, `onclick()`, `onkey()`, `listen()`

### 获取状态
`position()`, `heading()`, `xcor()`, `ycor()`, `isvisible()`

---

## 十一、常见问题与最佳实践

1. **窗口一闪而过**：末尾必须加 `turtle.done()` 或 `screen.mainloop()`
2. **绘图太慢**：使用 `t.speed(0)` 或 `screen.tracer(0)` 配合 `screen.update()`
3. **坐标超出画布**：用 `screen.setup()` 调整窗口，或 `screen.screensize()` 扩大画布
4. **中文显示乱码**：`write` 时指定支持中文的字体，如 `"微软雅黑"`, `"SimHei"`
5. **程序卡死**：避免死循环，动画用 `tracer` 控制刷新频率
6. **多个图形重叠**：合理使用 `penup()` 移动到新位置再 `pendown()`

需要我补充某个具体案例的完整代码，或者讲解更高级的 turtle 用法吗？