# 12.DjangoQueryDict对象

[官方文档🎈](https://docs.djangoproject.com/zh-hans/3.2/ref/request-response/#querydict-objects)



在 `HttpRequest` 对象中,属性 GET 和 POST 得到的都是 `django.http.QueryDict` 所创建的实例。

这是一个 django 自定义的类似字典的类，用来处理同一个键带多个值的情况。

```python
def welcome(request):
    # current_name = request.resolver_match.url_name
    print(type(request.GET),request.GET)
```

```
<class 'django.http.request.QueryDict'> <QueryDict: {}>
[13/Sep/2022 01:26:20] "GET /login/ HTTP/1.1" 200 133
```

**QueryDict继承Python 的Dict，拓展了Python字典的一些功能**



在 python 原始的字典中，当一个键出现多个值的时候会发生冲突，只保留最后一个值。

而在 HTML 表单中，通常会发生一个键有多个值的情况，例如 `<select multiple>` （多选框）就是一个很常见情况。



#### 1.QueryDict.`__init__`(query_string=None,mutable=False,encoding=None)

这是一个构造函数，其中`query_string`需要一个字符串

**QueryDict源码** 其中的init初始化方法

```python
    def __init__(self, query_string=None, mutable=False, encoding=None):
        super().__init__()
        self.encoding = encoding or settings.DEFAULT_CHARSET
        query_string = query_string or ''
        parse_qsl_kwargs = {
            'keep_blank_values': True,
            'encoding': self.encoding,
            'max_num_fields': settings.DATA_UPLOAD_MAX_NUMBER_FIELDS,
        }
        if isinstance(query_string, bytes):
            # query_string normally contains URL-encoded data, a subset of ASCII.
            try:
                query_string = query_string.decode(self.encoding)
            except UnicodeDecodeError:
                # ... but some user agents are misbehaving :-(
                query_string = query_string.decode('iso-8859-1')
        try:
            for key, value in parse_qsl(query_string, **parse_qsl_kwargs):
                self.appendlist(key, value)
        except ValueError as e:
            # ValueError can also be raised if the strict_parsing argument to
            # parse_qsl() is True. As that is not used by Django, assume that
            # the exception was raised by exceeding the value of max_num_fields
            # instead of fragile checks of exception message strings.
            raise TooManyFieldsSent(
                'The number of GET/POST parameters exceeded '
                'settings.DATA_UPLOAD_MAX_NUMBER_FIELDS.'
            ) from e
        self._mutable = mutable
```



#### 2.QueryDict.`__getitem__`(key)

返回给出的key的**值**，如果具有多个值，则getitem返回最后（最新）的值，

key不存在，raise `django.utils.datastructures.MultiValueDictKeyError`

Python KeyError的子类，所以可以捕获**KeyError**



#### 3.QueryDict.`__setitem__`(key,value)

设置给出的key的值为value（一个列表，只有一个元素）



#### 4.QueryDict.contains(key)

如果key存在，返回True



#### 5.QueryDict.get(key,default)

与getitem类似，不同点是key不存在时，返回一个默认值



#### 6.QueryDict.setdefault(key,default)

类似标准字典的`setdefault()` 方法，只是它在内部使用的是`__setitem__()`。

也就是说，当key已经存在时，返回其值，key不存在时，返回default，同时添加 key 和 default到对象中。



#### 7.QueryDict.update(other_dict)

接受一个QueryDict或标准python字典，类似标准字典的update()方法

它附加到当前字典项的后面



#### **8.QueryDict.items()**

　　类似标准字典的`items()` 方法，返回一个由键值组成的元祖的列表。但是它使用的是和`__getitem__` 一样返回最新的值的逻辑。

例如：

```
>>> q = QueryDict('a=1&a=2&a=3')
>>> q.items()
[('a', '3')]
```

#### **9.QueryDict.iteritems()**

　　类似标准字典的`iteritems()` 方法，返回一个迭代对象。类似 `QueryDict.items()`，它使用的是和`QueryDict.__getitem__()` 一样的返回最新的值的逻辑。

 

#### **10.QueryDict.iterlists()**

　　类似`QueryDict.iteritems()`，返回一个包含键值对的元祖(key, value)迭代对象 ，value 是一个包括所有 key 的值的列表。

 

#### **11.QueryDict.values()**

　　类似标准字典的`values()` 方法，但是它使用的是和`__getitem__` 一样返回最新的值的逻辑。也就是返回一个所有键对应的最新值的列表。

例如：

```
>>> q = QueryDict('a=1&a=2&a=3')
>>> q.values()
['3']
```

 

 

#### **12.QueryDict.itervalues()**

　　类似`QueryDict.values()`，只是它返回的是一个迭代器。

 

#### **13.QueryDict.copy()**

　　返回对象的副本，使用Python 标准库中的`copy.deepcopy()`。此副本是可变的即使原始对象是不可变的。

 

#### **14.QueryDict.getlist(key, default)**

　　以Python 列表形式返回所请求的键的数据。如果键不存在并且没有提供默认值，则返回空列表。它保证返回的是某种类型的列表，除非默认值不是列表。

 

#### **15.QueryDict.setlist(key, list_)**

　　为给定的键设置`list_`（与`__setitem__()` 不同)，可以设置一个多元素的列表。

 

#### **16.QueryDict.appendlist(key, item)**

　　将项追加到内部与键相关联的列表中。

 

#### **17.QueryDict.setlistdefault(key, default_list)**

　　类似`setdefault`，除了它接受一个列表而不是单个值。

 

#### **18.QueryDict.lists()**

　　类似`items`，只是它将字典中的每个成员作为列表。也就是说，列表中的每个元素，都是由键和对应的值列表组成的二元元祖。

例如：

```
>>> q = QueryDict('a=1&a=2&a=3')
>>> q.lists()
[('a', ['1', '2', '3'])]
```

 

 

#### **19.QueryDict.pop(key)**

　　返回给定键的值的**列表**，并从字典中移除它们。如果键不存在，将引发`KeyError`。

例如 ︰

```
>>> q = QueryDict('a=1&a=2&a=3', mutable=True)
>>> q.pop('a')
['1', '2', '3']
```

 

 

#### **20.QueryDict.popitem()**

　　删除字典**任意**一个成员（因为没有顺序的概念），并返回二值元组，包含键和键的所有值的列表。在一个空的字典上调用时将引发`KeyError`。

例如 ︰

```
>>> q = QueryDict('a=1&a=2&a=3', mutable=True)
>>> q.popitem()
('a', ['1', '2', '3'])
```

 

 

#### **21.QueryDict.dict()**

　　返回`QueryDict` 的`dict` 表示形式。对于`QueryDict` 中的每个(key, list)对 ，`dict` 将有(key, item) 对，其中item 是列表中的一个元素，使用与`QueryDict.__getitem__()`相同的逻辑，也就是最新的：

```
>>> q = QueryDict('a=1&a=3&a=5')
>>> q.dict()
{'a': '5'}
```

 

 

#### **22.QueryDict.urlencode([safe])**

　　从数据中返回查询字符串格式。

例如：

```
>>> q = QueryDict('a=2&b=3&b=5')
>>> q.urlencode()
'a=2&b=3&b=5'
```

 

　　可选地，urlencode 可以传递不需要编码的字符。（这意味着要进行 url 编码）

例如︰

```
>>> q = QueryDict(mutable=True)
>>> q['next'] = '/a&b/'
>>> q.urlencode(safe='/')
'next=/a%26b/'
```

 

