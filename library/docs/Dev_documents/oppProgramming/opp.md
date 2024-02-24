# 9.面向对象程序设计

**三大特性**

- 封装
- 继承
- 多态



### 1.对象和self

在每个类中都可以定义个特殊的：`__init__ 初始化方法 `，在实例化类创建对象时自动执行，即：`对象=类()`。

```python
class Message:

    def __init__(self, content):
        self.data = content

    def send_email(self, email):
        data = "给{}发邮件，内容是：{}".format(email, self.data)
        print(data)

    def send_wechat(self, vid):
        data = "给{}发微信，内容是：{}".format(vid, self.data)
        print(data)

# 对象 = 类名() # 自动执行类中的 __init__ 方法。

# 1. 根据类型创建一个对象，内存的一块 区域 。
# 2. 执行__init__方法，模块会将创建的那块区域的内存地址当self参数传递进去。    往区域中(data="注册成功")
msg_object = Message("注册成功")

msg_object.send_email("uichuan_47@163.com") # 给uichuan_47@163.com发邮件，内容是：注册成功
msg_object.send_wechat("uichuan") # 给uichuan发微信，内容是：注册成功
```

面向对象的思想：将一些数据封装到对象中，在执行方法时，再去对象中获取。

函数式的思想：函数内部需要的数据均通过参数的形式传递。



- self，本质上就是一个参数。这个参数是Python内部会提供，其实本质上就是调用当前方法的那个对象。
- 对象，基于类实例化出来”一块内存“，默认里面没有数据；经过类的 `__init__`方法，可以在内存中初始化一些数据。



### 2.常见成员

在编写面向对象相关代码时，最常见成员有：

- 实例变量，属于对象，只能通过对象调用。
- 绑定方法，属于类，通过对象调用 或 通过类调用。

```python
class Person:

    def __init__(self, n1, n2):
        # 实例变量
        self.name = n1
        self.age = n2
	
    # 绑定方法
    def show(self):
        msg = "我叫{}，今年{}岁。".format(self.name, self.age)
        print(msg)

    def all_message(self):
        msg = "我是{}人，我叫{}，今年{}岁。".format(Person.country, self.name, self.age)
        print(msg)

    def total_message(self):
        msg = "我是{}人，我叫{}，今年{}岁。".format(self.country, self.name, self.age)
        print(msg)
```



### 3.三个特性

#### 3.1 封装

封装主要体现在两个方面：

- 将同一类方法封装到了一个类中，例如上述示例中：匪徒的相关方法都写在Terrorist类中；警察的相关方法都写在Police类中。
- 将数据封装到了对象中，在实例化一个对象时，可以通过`__init__`初始化方法在对象中封装一些数据，便于以后使用。



#### 3.2 继承

传统的理念中有：儿子可以继承父亲的财产。

在面向对象中也有这样的理念，即：子类可以继承父类中的方法和类变量（不是拷贝一份，父类的还是属于父类，子类可以继承而已）。

```
父类
子类

基类
派生类
```



#### 3.3 多态

- Python默认支持多态（这种方式称之为鸭子类型），最简单的基础下面的这段代码即可。

```
def func(arg):
    v1 = arg.copy() # 浅拷贝
    print(v1)

func("武沛齐")
func([11,22,33,44])
```



### 4.成员

成员

- 变量
    - 实例变量
    - 类变量
- 方法
    - 绑定方法
    - 类方法
    - 静态方法
- 属性

- 成员修饰符
- 对象嵌套
- 特殊成员

#### 4.1变量

- 实例变量，属于对象，每个对象中各自维护自己的数据。
- 类变量，属于类，可以被所有对象共享，一般用于给对象提供公共数据（类似于全局变量）。



#### 4.2 方法

- 绑定方法，默认有一个self参数，由对象进行调用（此时self就等于调用方法的这个对象）【对象&类均可调用】
- 类方法，默认有一个cls参数，用类或对象都可以调用（此时cls就等于调用方法的这个类）【对象&类均可调用】
- 静态方法，无默认参数，用类和对象都可以调用。【对象&类均可调用】



#### 4.3 属性

属性其实是由绑定方法 + 特殊装饰器 组合创造出来的，让我们以后在调用方法时可以不加括号，例如：

```python
class Foo(object):

    def __init__(self, name):
        self.name = name

    def f1(self):
        return 123

    @property
    def f2(self):
        return 123


obj = Foo("uichuan")

v1 = obj.f1()
print(v1)

v2 = obj.f2
print(v2)
```



#### 4.4 成员修饰符

Python中成员的修饰符就是指的是：公有、私有。

- 公有，在任何地方都可以调用这个成员。
- 私有，只有在类的内部才可以调用改成员（成员是以两个下划线开头，则表示该成员为私有）。



#### 4.5 对象嵌套

基于面向对象进行编程时，对象之间可以存在各种各样的关系，例如：组合、关联、依赖等（Java中的称呼），用大白话来说就是各种嵌套。



#### 4.6 特殊成员

在Python的类中存在一些特殊的方法，这些方法都是 `__方法__` 格式，这种方法在内部均有特殊的含义，接下来我们来讲一些常见的特殊成员：

- `__init__`，初始化方法


```python
class Foo(object):
    def __init__(self, name):
        self.name = name


obj = Foo("武沛齐")
```



- `__new__`，构造方法


```python
class Foo(object):
    def __init__(self, name):
        print("第二步：初始化对象，在空对象中创建数据")
        self.name = name

    def __new__(cls, *args, **kwargs):
        print("第一步：先创建空对象并返回")
        return object.__new__(cls)


obj = Foo("武沛齐")
```

​	创建新对象的本质：

​	先在类的内部执行`__new__`方法创建空对象并返回

​	第二步才会调用`__init__`方法在空对象中创建数据

- `__call__`


```python
class Foo(object):
    def __call__(self, *args, **kwargs):
        print("执行call方法")


obj = Foo()
obj()
```



- `__str__`


```python
class Foo(object):

    def __str__(self):
        return "哈哈哈哈"


obj = Foo()
data = str(obj)
print(data)
```



- `__dict__`


```python
class Foo(object):
    def __init__(self, name, age):
        self.name = name
        self.age = age


obj = Foo("武沛齐",19)
print(obj.__dict__)
```





- `__getitem__`、`__setitem__`、`__delitem__`


```python
class Foo(object):

    def __enter__(self):
        print("进入了")
        return 666

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("出去了")


obj = Foo()
with obj as data:
    print(data)
```



- `__enter__`、`__exit__`

```python
class Foo(object):

    def __enter__(self):
        print("进入了")
        return 666

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("出去了")


obj = Foo()
with obj as data:
    print(data)
```



- `__add__` 等。


```python
class Foo(object):
    def __init__(self, name):
        self.name = name

    def __add__(self, other):
        return "{}-{}".format(self.name, other.name)


v1 = Foo("alex")
v2 = Foo("sb")

# 对象+值，内部会去执行 对象.__add__方法，并将+后面的值当做参数传递过去。
v3 = v1 + v2
print(v3)
```



- `__iter__`

    - 迭代器


```python
# 迭代器类型的定义：
    1.当类中定义了 __iter__ 和 __next__ 两个方法。
    2.__iter__ 方法需要返回对象本身，即：self
    3. __next__ 方法，返回下一个数据，如果没有数据了，则需要抛出一个StopIteration的异常。
	官方文档：https://docs.python.org/3/library/stdtypes.html#iterator-types
        
# 创建 迭代器类型 ：
	class IT(object):
        def __init__(self):
            self.counter = 0

        def __iter__(self):
            return self

        def __next__(self):
            self.counter += 1
            if self.counter == 3:
                raise StopIteration()
            return self.counter

# 根据类实例化创建一个迭代器对象：
    obj1 = IT()
    
    # v1 = obj1.__next__()
    # v2 = obj1.__next__()
    # v3 = obj1.__next__() # 抛出异常
    
    v1 = next(obj1) # obj1.__next__()
    print(v1)

    v2 = next(obj1)
    print(v2)

    v3 = next(obj1)
    print(v3)


    obj2 = IT()
    for item in obj2:  # 首先会执行迭代器对象的__iter__方法并获取返回值，一直去反复的执行 next(对象) 
        print(item)
        
迭代器对象支持通过next取值，如果取值结束则自动抛出StopIteration。
for循环内部在循环时，先执行__iter__方法，获取一个迭代器对象，然后不断执行的next取值（有异常StopIteration则终止循环）。
```



- 生成器


```python
# 迭代器类型的定义：
    1.当类中定义了 __iter__ 和 __next__ 两个方法。
    2.__iter__ 方法需要返回对象本身，即：self
    3. __next__ 方法，返回下一个数据，如果没有数据了，则需要抛出一个StopIteration的异常。
	官方文档：https://docs.python.org/3/library/stdtypes.html#iterator-types
        
# 创建 迭代器类型 ：
	class IT(object):
        def __init__(self):
            self.counter = 0

        def __iter__(self):
            return self

        def __next__(self):
            self.counter += 1
            if self.counter == 3:
                raise StopIteration()
            return self.counter

# 根据类实例化创建一个迭代器对象：
    obj1 = IT()
    
    # v1 = obj1.__next__()
    # v2 = obj1.__next__()
    # v3 = obj1.__next__() # 抛出异常
    
    v1 = next(obj1) # obj1.__next__()
    print(v1)

    v2 = next(obj1)
    print(v2)

    v3 = next(obj1)
    print(v3)


    obj2 = IT()
    for item in obj2:  # 首先会执行迭代器对象的__iter__方法并获取返回值，一直去反复的执行 next(对象) 
        print(item)
        
迭代器对象支持通过next取值，如果取值结束则自动抛出StopIteration。
for循环内部在循环时，先执行__iter__方法，获取一个迭代器对象，然后不断执行的next取值（有异常StopIteration则终止循环）。
```



- 可迭代对象


```python
# 如果一个类中有__iter__方法且返回一个迭代器对象 ；则我们称以这个类创建的对象为可迭代对象。

class Foo(object):
    
    def __iter__(self):
        return 迭代器对象(生成器对象)
    
obj = Foo() # obj是 可迭代对象。

# 可迭代对象是可以使用for来进行循环，在循环的内部其实是先执行 __iter__ 方法，获取其迭代器对象，然后再在内部执行这个迭代器对象的next功能，逐步取值。
for item in obj:
    pass
```



```python
class IT(object):
    def __init__(self):
        self.counter = 0

    def __iter__(self):
        return self

    def __next__(self):
        self.counter += 1
        if self.counter == 3:
            raise StopIteration()
        return self.counter


class Foo(object):
    def __iter__(self):
        return IT()


obj = Foo() # 可迭代对象


for item in obj: # 循环可迭代对象时，内部先执行obj.__iter__并获取迭代器对象；不断地执行迭代器对象的next方法。
    print(item)
```
