# 14.metaclass元类

### 1.前戏

对象是类实例化的结果

```python
class Foo(object):
    pass

# 第1步：调用Foo的__new__方法创建空对象。
# 第2步：调用Foo的__init__方法对对象进行初始化。
obj = Foo()
```

类在默认情况下是由type创建出来的

```python
class Foo(object):
    v1 = 123
    
    def func(self):
        return 69
```

```python
Foo = type("Foo",(object,),{"v1":123,"func":lambda self:69})
```

这两种情况创建类，是完全等价的



### 2. metaclass

在定义类时加入metaclass指定当前类的创造者

默认情况下，metaclass=type

```python
# 由type创建Foo类型
class Foo(object):
    pass
```

```python
# 由MyType创建Foo类型
class Foo(object,metaclass=MyType):
    pass
```



#### 2.1 指定元类(metaclass)来创建类

```python
class MyType(type):
    def __init__(cls, *args, **kwargs):
        print("第2步：初始化类成员：", args, **kwargs)
        super().__init__(*args, **kwargs)

    def __new__(cls, *args, **kwargs):
        new_cls = super().__new__(cls, *args, **kwargs)
        print("第1步：创建类：", new_cls)
        return new_cls

    def __call__(cls, *args, **kwargs):
        print("第3步：创建对象&初始化对象", cls)

        # 1.调用自己那个类的 __new__ 方法去创建对象
        new_object = cls.__new__(cls, *args, **kwargs)

        # 2.调用你自己那个类 __init__放发去初始化
        cls.__init__(new_object, *args, **kwargs)
        return new_object


class Foo(metaclass=MyType):
    v1 = 123

    def func(self):
        pass


obj = Foo()
```



#### 2.2 在MyType元类中自定义类变量

```python
class MyType(type):
    def __new__(cls, name, bases, attrs):
        # print(name, bases, attrs)
        del attrs['v1']
        attrs['dex'] = "xxxx"
        xx = super().__new__(cls, name, bases, attrs)
        return xx


# Foo = MyType("Foo", (object,), {"v1": 123, "func": lambda self: 999})
class Foo(object, metaclass=MyType):
    v1 = 123

    def func(self):
        pass


print(Foo.func)
print(Foo.dex)
```



> 元类在DjangoRestframework序列化器有应用。

