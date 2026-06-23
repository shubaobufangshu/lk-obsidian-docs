# Python知识总结
<u>linkon</u>
~ 环境配置:
+ 根目录
+ Scripts\  目录

~ 虚拟环境

+ 创建虚拟环境
	python -m venv env
+ 激活环境
  source env/bin/activate	mac
  env\Scripts\activate.bat	windows
+ 关闭环境
	deactivate

~ 打包

[pyinstall](#pyinstall)

~ 换源

+ 临时更换源
  + `清华源:pip install x -i https://pypi.tuna.tsinghua.edu.cn/simple`
+ 永久更换源
  + `pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple`

anacobda汉化：conda install anaconda-navigator-localization=zh_CN

Windows：`%USERPROFILE%\.anaconda\navigator\condarc
`
jupyterlab ：汉化：conda install -c conda-forge jupyterlab-language-pack-zh-CN

```
navigator_language: zh_CN
```
[基础知识](#基础知识)
	[标识符](#标识符)
	[关键字](#关键字)
	[数据类型](#数据类型)
	[运算符号](#运算符号)
	[控制语句](#控制语句)
	[格式化输入输出](#格式化输入输出)
	[序列](#序列)
	[函数](#函数)
	[面向对象](#面向对象)
	[异常](#异常)
	[模块与包](#模块与包)
	[Python服务](#Python服务)
	[文件操作](#文件操作)
[过度知识](#过度知识)
	[Os](#Os)
	[Window](#Window)
	[re](#re)
	[Time](#Time)
	[Tqdm](#Tqdm)
	[flask](#flask)
	[Requests](#Requests)
	[Pyside6](#Pyside6)
	[Mysql](#Mysql)
	[Subprocess](#Subprocess)
	[Pyinstaller](#Pyinstaller)
[高级模块](#高级模块)
	[pandas](#pandas)
	[Pygame](#Pygame)
	[Pillow](#Pillow)
	[Torchvision](#Torchvision)
	[matplotlib](#matplotlib)
	[Numpy](#Numpy)
	



## 基础知识
###  标识符
~ 命名规则
+ 首字符只能是字母（a-z/A-Z）或下划线（_），不能是数字
+ 后续字符可加数字（0-9）
+ 区分大小写
+ 不能用 Python 关键字
+ 小写 + 下划线命名 或者  驼峰式命名
### 关键字
#### 查看python关键字
```python
import keyword
print(keyword.kwlist)
```
#### python关键字
```python
['False', 'None', 'True', 'and', 'as', 'assert', 'async', 'await', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```
### 数据类型
#### 查看python数据类型
```python
type(数据或者变量)
```
+ int:整数
+ float:浮点数
+ bool:布尔值
+ str:字符串
+ list:列表
+ tuple:元组
+ set:集合
+ dict:字典
+ complex:复数
+ bytes:字节串
+ bytearray:可变字节串
+ frozenset:不可变集合
+ memoryview:内存视图
+ NoneType:空值
+ ellipsis:省略号

#### python数据类型格式符号
+ %s:字符串
+ %d:有符号的十进制整数 
+ %f:浮点数
+ %c:字符
+ %u:无符号十进制整数
+ %o:八进制整数
+ %x:十六进制整数（小写ox）
+ %X:十六进制整数（大写OX）
+ %e:科学计数法（小写'e'）
+ %E:科学计数法（大写'E'）
+ %g %f和%e的简写
+ %G %f和%E的简写 
#### python数据类型转换
+ int(x [,base ]) 	将x转换为一个整数
+ float(x )		将x转换为一个浮点数
+ complex(real [,imag ])	创建一个复数，real为实部，imag为虚部
+ str(x )	将对象 x 转换为字符串
+ repr(x )	将对象 x 转换为表达式字符串
+ eval(str )	用来计算在字符串中的有效Python表达式,并返回一个对象
+ tuple(s )		将序列 s 转换为一个元组
+ list(s )		将序列 s 转换为一个列表
+ chr(x )	将一个整数转换为一个Unicode字符
+ ord(x )	将一个字符转换为它的ASCII整数值
+ hex(x )	将一个整数转换为一个十六进制字符串
+ oct(x )	将一个整数转换为一个八进制字符串
+ bin(x )	将一个整数转换为一个二进制字符串

#### 运算符号
~ 算数运算符:	
+ `+加,-减,*乘,/除,//整除,%取余,**指数,()小括号`
~ 赋值运算符:	
`=赋值`
~复合赋值运算符:	
+ `+=加法赋值运算符`	
+ `-=减法赋值运算符`	
+ `*=乘法赋值运算符`	
+ `/=除法赋值运算符`	
+ `//=整除赋值运算符`	
+ `%=	取余赋值运算符`	
+ `**=	幂赋值运算符`

~比较运算符

+ ` ==	判断相等。如果两个操作数的结果相等，则条件结果为真(True)，否则条件结果为假(False)`

+ `!=	不等于。如果两个操作数的结果不相等，则条件为真(True)，否则条件结果为假(False)`
+ `>	运算符左侧操作数结果是否大于右侧操作数结果，如果大于，则条件为真，否则为假`
+ `<	运算符左侧操作数结果是否小于右侧操作数结果，如果小于，则条件为真，否则为假`
+ `>=	运算符左侧操作数结果是否大于等于右侧操作数结果，如果大于，则条件为真，否则为假`
+`<=	运算符左侧操作数结果是否小于等于右侧操作数结果，如果小于，则条件为真，否则为假`
~ 逻辑运算符
+ `and	x and y	布尔"与"：如果 x 为 False，x and y 返回 False，否则它返回 y 的值。	True and False， 返回 False。`
+ `or		x or y	布尔"或"：如果 x 是 True，它返回 True，否则它返回 y 的值。	False or True， 返回 True。`
+ `not	not x	布尔"非"：如果 x 为 True，返回 False 。如果 x 为 False，它返回 True`
### 控制语句
#### 条件控制语句
```python
If 条件表达式:
代码块
elif 条件表达式:
代码块
............
else:
代码块
```
```python
三目运算
值1 if 条件 else 值2
```
#### 循环控制语句
```python
while 条件表达式:
   	条件成立重复执行的代码
```
```python
for 临时变量 in 序列:
	重复执行的代码
```
退出循环
```python
break退出整个循
```
```python
continue退出本次循环，继续执行下一次重复执行的代码
```
else使用
while和for都可以配合else使用
else下方缩进的代码含义：当循环正常结束后执行的代码
break终止循环不会执行else下方缩进的代码
continue退出循环的方式执行else下方缩进的代码
### 格式化输入输出
#### 输入
```python
input()
```
#### 输出
```python
print()
```
+ r字符原样输出:		
`(r’’)`
+ f格式化输出:
`f’输出变量值{变量}’)`
+ 格式化输出:(“字符+占位符%s”&’值’):%s字符串 %d整数 %f 浮点数
+ Format:(‘字符+{0},{1}’.format(‘值1’,’值2’))
### 序列
+ 切片:序列名[开始位置下标:结束位置下标:步长]
+ 取值:序列名[索引]
+ find()查找其内部元素
+ index()返回指定数据所在位置的下标
+ 修改:序列名[索引]=元素
+ len(序列)	计算容器中元素个数
+ del 或 del()	删除
+ max(序列)	返回容器中元素最大值
+ min(序列)	返回容器中元素最小值
+ range(start, end, step)	生成从start到end的数字，步长为 step，供for循环使用
+ enumerate()	函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出
+ 数据和数据下标，一般用在 for 循环当中。
+ 容器类型转换:
	+ 转元组:tuple(元素)
	+ 转列表:list(元素)
	+ 转序列:set(元素)
+ 列表推导式:

```python
list1 = [i for i in range(10)]
list1 = [i for i in range(0, 10, 2)]
list1 = [i for i in range(10) if i % 2 == 0]
list1 = [(i, j) for i in range(1, 3) for j in range(3)]
list1 = ['name', 'age', 'gender']
list2 = ['Tom', 20, 'man']
[xx for xx in range()]
```

+ 字典推导式:

```python
dict1 = {i: i**2 for i in range(1, 5)}  # {1: 1, 2: 4, 3: 9, 4: 16}
dict1 = {list1[i]: list2[i] for i in range(len(list1))}
{xx1: xx2 for ... in ...}
```

+ 集合推导式

```python
{xx for xx in ...}
```

+ While

```python
while i < 10:
list1.append(i)
i += 1

```

+ For:


```python
for i in range(10):
   list1.append(i)
```

#### 字符串’字符’

+ replace()替换:		  
```python
字符串序列.replace(旧子串, 新子串, 替换次数)
```
+ split()按照指定字符分割字符串:	  字符串序列.split(分割字符, num)
```python
字符串序列.split(分割字符, num)
```
+ join()合并字符串:				  
```python
字符或子串.join(多字符串组成的序列)
```
+ capitalize()字符串第一个字符转大写:
```python
 字符串序列.capitalize()
```
+ title()字符串每个单词首字母转大写: 
```python
字符串序列.title()
```
+ lower()将字符串中大写转小写:	 
```python
 字符串序列.lower()
```
+ upper()将字符串中小写转大写:	  
```python
字符串序列.upper()
```

+ startswith()检查字符串是否是以指定子串开头	  
```txt
是则返回 True，否则返回 False。如果设置开始和结束位置下标，则在指定范围内检查:
```
```python
字符串序列.startswith(子串, 开始位置下标, 结束位置下标)
```
+ endswith()检查字符串是否是以指定子串结尾	  
```txt
是则返回 True，否则返回 False。如果设置开始和结束位置下标，则在指定范围内检查:
```
```python
endswith(子串, 开始位置下标, 结束位置下标)
```
+ isalpha()如果字符串至少有一个字符并且所有字符都是字母
```txt
是则返回 True, 否则返回 False:
```
```python
符串序列.isalpha()
```
+ isdigit()如果字符串只包含数字

```txt
是则返回 True 否则返回 False:
```
```python
字符串序列.isdigit()
```

+ isalnum()如果字符串至少有一个字符并且所有字符都是字母或数字

```txt
是则返 回 True,否则返回 False:
```
```python
字符串序列.isalnum()
```
+ isspace()如果字符串中只包含空白

```txt
是则返回 True，否则返回 False:
```
```python
字符串序列.isspace()
```

#### 列表[元素,元素,元素]
+ count()统计指定数据在当前列表中出现的次数:
```python
列表序列.count()
```

+ len()：访问列表长度，即列表中数据的个数
```python
列表序列.len()
```
+ in：判断指定数据在某个列表序列，如果在返回True，否则返回False
+ append()：列表结尾追加数据。
```python
列表序列.append(元素)
```
+ extend()：列表结尾追加数据，如果数据是一个序列，则将这个序列的数据逐一添加到列表。
```python
列表序列.extend(元素)
```
+ insert()：指定位置新增数据。
```python
列表序列.insert(位置下标,元素)
```
+ del()删除:
```python
del 目标
del 列表序列[索引]
```
+ pop()：删除指定下标的数据(默认为最后一个)，并返回该数据:列表序列.
```python
pop(位置下标)
```
+ remove()：移除列表中某个数据的第一个匹配项:
```python
列表序列.remove(元素)
```
+ clear()：清空列表:
```python
列表序列.clear()
```

+ reverse()逆置:
```python
列表序列.reverse()
```

+ sort()排序
`reverse表示排序规则，reverse = True 降序， reverse = False 升序（默认）:`
```python
列表序列.sort( key=None, reverse=False)
```

+ copy()复制：
```python
列表序列.copy()
```

#### 元组(元素,元素,元素)

+ count()统计指定数据在当前列表中出现的次数:

```python
元组序列.count()
```

+ len()：访问列表长度，即列表中数据的个数。

```python
元组序列.len()
```

+ in：判断指定数据在某个列表序列，如果在返回True，否则返回False

#### 字典{v:k,v:k}

+ 增加:

```python
字典序列[key]=值    
如果key存在则修改这个key对应的值；如果key不存在则新增此键值对。
```

+ 删除:

```python
del() / del：删除字典或删除字典中指定键值对。
```

+ 清空字典:

```python
字典序列.clear()
```

+ 修改:
```python
字典序列[key]=值
```
+ 查找
```python
:字典序列.get(key, 默认值)
如果当前查找的key不存在则返回第二个参数(默认值)，如果省略第二个参数，则返回None。
```
+ Key:
```python
字典序列.key() 
```
+ Values:
```python
字典序列.Values()
```
#### 集合{元素,元素,元素}元素互异
+ 创建:
```python
set()
```
+ 增加数据
```
add():集合.add(元素)
update(), 追加的数据是序列。:集合.update(序列)
```
+ 删除数据
```
emove()，删除集合中的指定数据，如果数据不存在则报错。集合.remove(元素)

discard()，删除集合中的指定数据，如果数据不存在也不会报错。集合.discard(元素)

pop()，随机删除集合中的某个数据，并返回这个数据。集合.discard()
```
### 函数
#### 定义
```python
def 函数名(参数):
    print("hello")
    代码
    return 值
    // 返回值
```
#### 调用
```python
函数名(参数)
```
#### 返回值

```python
return 表达式1, 表达式2...
```
#### 说明文档help(函数名)

```python
def 函数名(参数):
    """ 说明文档的位置 """
    代码
    ......
```
#### 说明
+ 函数的参数
	函数调用的时候可以传入真实数据，增大函数的使用的灵活性
	+ 关键字参数
		函数调用，通过“键=值”形式加以指定。可以让函数更加清晰、容易使用，同时也清除了参数的顺序需求。
		```python
		 key=value
		```
		函数调用时，如果有位置参数时，位置参数必须在关键字参数的前面，但关键字参数之间不存在先后顺序。
	+ 缺省参数
		缺省参数也叫默认参数，用于定义函数，为参数提供默认值，调用函数时可不传该默认参数的值（注意：所有位置参数必须出现在默认参数前，包括函数定义和调用）。
		函数调用时，如果为缺省参数传值则修改默认参数值；否则使用这个默认值。
	+ 不定长参数
		不定长参数也叫可变参数。用于不确定调用的时候会传递多少个参数(不传参也可以)的场景。此时，可用包裹(packing)位置参数，或者包裹关键字参数，来进行参数传递，会显得非常方便。
	+ 包裹位置传递
	
	  ```python
	  def user_info(*args):
	      print(args)
	      //传进的所有参数都会被args变量收集，它会根据传进参数的位置合并为一个元组(tuple)，args是元组类型，这就是包裹位置传递。
	  ```
	+ 包裹关键字传递
	```python
	  def user_info(**kwargs):
          print(kwargs)
	      //无论是包裹位置传递还是包裹关键字传递，都是一个组包的过程
	```
+ 形参
	函数定义时书写的参数(非真实数据)
+ 实参：函数调用时书写的参数(真实数据)
	+ 位置参数:
		形参和实参的个数和书写顺序必须一致
		函数调用时书写的参数(真实数据)
+ 函数的返回值
	作用：函数调用后，返回需要的计算结果
+ 引用
  我们可以用id()来判断两个变量是否为同一个值的引用。 我们可以将id值理解为那块内存的地址标识
+ 可变与不可变类型
	所谓可变类型与不可变类型是指：数据能够直接进行修改，如果能直接修改那么就是可变，否则是不可变.

```python
+ 可变类型
	+ 列表
	+ 字典
	+ 集合
+ 不可变类型
	+ 整型
	+ 浮点型
	+ 字符串
	+ 元组
```
#### 递归
+ 递归是一种编程思想
+ 递归的特点
  + 函数内部自己调用自己
  + 必须有出口
#### Lambda表达式
如果一个函数有一个返回值，并且只有一句代码，可以使用 lambda简化。
```python
lambda 参数列表 ： 表达式
```
+ lambda表达式的参数可有可无，函数的参数在lambda表达式中完全适用。
+ lambda表达式能接收任何数量的参数但只能返回一个表达式的值。
+ 直接打印lambda表达式，输出的是此lambda的内存地址
#### 内置函数
+ abs(),函数可以完成对数字求绝对值计算。
+ map(func, lst)，将传入的函数变量func作用到lst变量的每个元素中，并将结果组成新的列表(Python2)/迭代器(Python3)返回。
+ reduce(func，lst)，其中func必须有两个参数。每次func计算的结果继续和序列的下一个元素做累积计算。
  注意：reduce()传入的参数func必须接收2个参数。
+ filter(func, lst)函数用于过滤序列, 过滤掉不符合条件的元素, 返回一个 filter 对象。如果要转换为列表, 可以使用 list() 来转换
### 面向对象
+ 面向对象是一种抽象化的编程思想，面向对象就是将编程当成是一个事物，对外界来说，事物是直接使用的，不用去管他内部的情况。而编程就是设置事物能够做什么事
+ 在面向对象编程过程中，有两个重要组成部分：类 和 对象
+ 类和对象的关系：用类去创建一个对象。
+ 类:类是对一系列具有相同==特征==和==行为==的事物的统称，是一个==抽象的概念==，不是真实存在的事物。
+ 特征即是属性
  行为即是方法
+ 对象是类创建出来的真实存在的事物
+ 开发中，先有类，再有对象。
+ 在Python中，所有类默认继承object类，object类是顶级类或基类；其他子类叫做派生类。不由任意内置类型派生出的类，称之为经典类
#### 定义类与实例化
##### 定义类
```python
class 类名(继承类):// 默认继承object
    代码
    ......
```
##### 实例化
```python
对象名 = 类名()
//添加和获取对象属性:对象属性既可以在类外面添加和获取，也能在类里面添加和获取。
对象名.属性名 = 值
//类外面添加对象属性语法
语法:对象名.属性名
//类外面获取对象属性
语法:self.属性名
//类里面获取对象属性
```
##### self
self指的是调用该函数的对象
打印对象和self得到的结果是一致的，都是当前对象的内存中存储地址。

#### 魔法方法
在Python中，`__xx__()`的函数叫做魔法方法，指的是具有特殊功能的函数
+ `__init__()初始化方法:`
```python
__init__()方法，在创建一个对象时默认被调用，不需要手动调用
__init__(self)中的self参数，不需要开发者传递，python解释器会自动把当前的对象引用传递过去。
带参数的__init__()一个类可以创建多个对象，传参数可以对不同的对象设置不同的初始化属性
```
+ `__str__()`
```
当使用print输出对象的时候，默认打印对象的内存地址。如果类定义了__str__方法，那么就会打印从在这个方法中 return 的数据。
```
+ `__del__()`
```
当删除对象时，python解释器也会默认调用__del__()方法。
```
#### 继承
当一个类有多个父类的时候，默认使用第一个父类的同名属性和方法。
+ 重写
	子类和父类具有同名属性和方法，默认使用子类的同名属性和方法。
	super()调用父类方法,注意：使用super() 可以自动查找父类。调用顺序遵循 __mro__ 类属性的顺序。比较适合单继承使用。
+ 私有权限
	在Python中，可以为实例属性和方法设置私有权限，即设置某个实例属性或实例方法不继承给子类。
	设置私有权限的方法：在属性名和方法名 前面 加上两个下划线 __:私有属性和私有方法只能在类里面访问和修改。
	获取和修改私有属性值:在Python中，一般定义函数名get_xx用来获取私有属性，定义set_xx用来修改私有属性值。
#### 多态
多态指的是一类事物有多种形态，（一个抽象类有多个子类，因而多态的概念依赖于继承）。
多态是一种使用对象的方式，子类重写父类方法，调用不同子类对象的相同父类方法，可以产生不同的执行结果
好处：调用灵活，有了多态，更容易编写出通用的代码，做出通用的编程，以适应需求的不断变化！
```
实现步骤：
定义父类，并提供公共方法
定义子类，并重写父类方法
传递子类对象给调用者，可以看到不同子类执行效果不同
```
#### 类与实例化对象
##### 类属性和实例属性
```
类属性:
	类属性就是 类对象 所拥有的属性，它被 该类的所有实例对象 所共有。
类属性可以使用 类对象 或 实例对象 访问。

类属性的优点:
	记录的某项数据 始终保持一致时，则定义类属性。
	实例属性要求每个对象为其单独开辟一份内存空间来记录数据，而类属性为全类所共有 ，仅占用一份内存，更加节省内存空间。

修改类属性:
	类属性只能通过类对象修改，不能通过实例对象修改，如果通过实例对象修改类属性，表示的是创建了一个实例属性。
```
#### 类方法与静态方法
##### 类方法
	需要用装饰器@classmethod来标识其为类方法，对于类方法，第一个参数必须是类对象，一般以cls作为第一个参数。
+ 类方法使用场景:
	当方法中 需要使用类对象 (如访问私有类属性等)时，定义类方法:类方法一般和类属性配合使用
##### 静态方法
	需要通过装饰器@staticmethod来进行修饰，静态方法既不需要传递类对象也不需要传递实例对象（形参没有self/cls）。静态方法 也能够通过 实例对象 和 类对象 去访问。
+ 静态方法使用场景
	当方法中 既不需要使用实例对象(如实例对象，实例属性)，也不需要使用类对象 (如类属性、类方法、创建实例等)时，定义静态方法,取消不需要的参数传递，有利于 减少不必要的内存占用和性能消耗
### 异常
#### 异常概念
触犯了编译器规则引起的报错
```python
//捕获全部异
try:
    可能发生错误的代码
except:
    如果出现异常执行的代码
     
//使用基本类
try:
    print(num)
except Exception as result:
    print(result)
     
// 捕获指定异常
try:
    可能发生错误的代码
except 异常类型:
    如果捕获到该异常类型执行的代码
捕获多个指定异常


//完整写法
try:
    f = open('test.txt', 'r')
except Exception as result:
    f = open('test.txt', 'w')
else:
    print('没有异常，真开心')
finally:
    f.close()
```
#### 自定义异常
+ 1 自定义异常类
```python
class 异常类类名(Exception):
    代码
    # 设置抛出异常的描述信息
    def __str__(self):
      return ...
```
+ 2 抛出异常
```python
raise 异常类名()
```
+ 3 捕获异常
```python
except Exception...
```
### 模块与包
Python 模块(Module)，是一个 Python 文件，以 .py 结尾，包含了 Python 对象定义和Python语句。
模块能定义函数，类和变量，模块里也能包含可执行的代码。
#### 导入模块
```python
import 模块名
from 模块名 import 功能名
from 模块名 import *
import 模块名 as 别名
from 模块名 import 功能名 as 别名
```
注意事项
如果使用from .. import ..或from .. import *导入多个模块的时候，且模块内有同名功能。当调用这个同名功能的时候，调用到的是后面导入的模块的功能。
`__all__`
如果一个模块文件中有__all__变量，当使用from xxx import *导入时，只能导入这个列表中的元素。
#### 制作模块
在Python中，每个Python文件都可以作为一个模块，模块的名字就是文件的名字。也就是说自定义模块名必须要符合标识符命名规则。
```
定义模块:新建一个Python文件，命名,定义函数
测试模块:在实际开中，当一个开发人员编写完一个模块后，为了让模块能够在项目中达到想要的效果，这个开发人员会自行在py文件中添加一些测试信息.
def testA(a, b):
  print(a + b)
\# 只在当前文件中调用该函数，其他导入的文件内不符合该条件，则不执行testA函数调用
if __name__ == '__main__':
  testA(1, 1)
```
#### 模块定位与寻找
+ 模块定位顺序
  当导入一个模块，Python解析器对模块位置的搜索顺序是：
  当前目录
  如果不在当前目录，Python则搜索在shell变量PYTHONPATH下的每个目录。
  如果都找不到，Python会察看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/
  模块搜索路径存储在system模块的sys.path变量中。变量里包含当前目录，PYTHONPATH和由安装过程决定的默认目录。
  注意
  自己的文件名不要和已有模块名重复，否则导致模块功能无法使用
  使用from 模块名 import 功能的时候，如果功能名字重复，调用到的是最后定义或导入的功能。
#### 导入包
包将有联系的模块组织在一起，即放到同一个文件夹下，并且在这个文件夹创建一个名字为__init__.py 文件，那么这个文件夹就称之为包。
##### 导入包
```python
import 包名.模块名*
包名.模块名.目标
```
```python
在__init__.py文件中添加__all__ = []，控制允许导入的模块列表。
from my_package import *
```
### Python服务
#### 浏览器HTTP服务器
+ 启动浏览器服务器构建工具
```python
python -m http.server 8080
//打开浏览器，访问 http://localhost:8080 即可看到页面。
```
#### pip-python包管理工具
##### 安装包
+ 安装最新版本：
`pip install <包名>`
+ 安装指定版本
`pip install <包名>==版本号`
+ 安装最低版本（及以上）
`pip install <包名>>=最低版本`
+ 从本地文件安装
```python
pip install /path/to/package.whl  # 本地whl文件
pip install /path/to/package.tar.gz  # 本地源码包
```
+ 升级已安装的包
`pip install --upgrade <包名> `
+ 卸载包
`pip uninstall <包名> `
+ 查看已安装的包
`pip list`
+ 列出可升级的包
`pip list --outdated`
+ 查看单个包的详细信息（包括版本、依赖、安装路径等）
`pip show <包名>`
+ 导出依赖到文件
`pip freeze > requirements.txt`
+ 从依赖文件安装
`pip install -r requirements.txt`
+ 清理缓存
```bash
pip cache purge  # 清空所有缓存
pip cache remove <包名>  # 仅删除指定包的缓存
```
#### socketserver - 通用网络服务框架
#### xmlrpc.server - RPC 服务框架
#### logging - 日志服务
#### configparser - 配置文件解析服务
### 文件操作
#### 打开文件
``` python
//打开文件
open(name, mode)
```
```txt
name：是要打开的目标文件名的字符串(可以包含文件所在的具体路径)。
mode：设置打开文件的模式(访问模式)：只读、写入、追加等。
```
上下文
```python
Whith open(name, mode):
```
#### 打开模式
```
r	以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。
rb	以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。这是默认模式。
r+	打开一个文件用于读写。文件指针将会放在文件的开头。
rb+	以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。
w	打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
wb	以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
w+	打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
wb+	以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。
a	打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。
ab	以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。
a+	打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。
ab+	以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。
```
#### 写入
```python
文件对象 = open('test.txt', 'w')
文件对象.write('内容')
```
#### 关闭
``` 
文件对象.close()
```
#### 读取
```python
文件对象.read(num)

```
```
num表示要从文件中读取的数据的长度（单位是字节），如果没有传入num，那么就表示读取文件中所有的数据。
readlines()
readlines可以按照行的方式把整个文件中的内容进行一次性读取，并且返回的是一个列表，其中每一行的数据为一个元素。
readline()
readline()一次读取一行内容。
seek()
作用：用来移动文件指针。
文件对象.seek(偏移量, 起始位置)
起始位置：
0：文件开头
1：当前位置
2：文件结尾
```
## 过度知识
### Os
##### 导入

`import os`

##### 使用

os.函数名()

+ 基础 

```python
//文件重命名:
    os.rename(目标文件名, 新文件名)
//删除文件:
    os.remove(目标文件名)
//新建文件夹:
    os.mkdir(文件夹名字)
//删除文件夹:
    os.rmdir(文件夹名字)
//获取当前文件地址:
    os.getcwd()
//修改默认目录:
    os.chdir(目录)
//获取目录列表:o
	s.listdir(目录)
```

+ Sys模块

```python
//与Python解释器交互。
//常用属性：
sys.argv：命令行参数。
sys.version：Python版本信息
```




### pyinstall
#### 常用参数

| 参数                   | 说明                                                       |
| ---------------------- | ---------------------------------------------------------- |
| `-F`/`--onefile`       | 单文件模式                                                 |
| `-D`/`--onedir`        | 单目录模式（默认）                                         |
| `-n 名称`              | 指定输出文件名                                             |
| `-i 图标路径`          | 设置可执行文件图标（.ico 格式，Windows）                   |
| `-w`/`--windowed`      | 隐藏控制台（GUI 程序用）                                   |
| `-c`/`--console`       | 显示控制台（默认，命令行程序用）                           |
| `--add-data "源;目标"` | 添加非代码文件（Windows 用`;`，macOS/Linux 用`:`分隔路径） |
| `--hidden-import 模块` | 手动指定隐藏导入模块（解决 ModuleNotFoundError）           |