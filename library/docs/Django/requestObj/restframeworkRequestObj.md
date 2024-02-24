# 2.restframework Request对象

DRF中的request对象是对django中的request对象进行的再次封装



### 1.前戏：

#### 1.1 opp

```python
class Foo(object):
    def __init__(self,name,age):
        self.name = name
        self.age = age
        
    def show(self):
        return 123
    
    def __getattr__(self,item):
        print('---->',item)
        return 999
```

`__getattr__`方法会在对象调用不存在的成员时触发

```python
class Foo(object):
    def __init__(self,name,age):
        self.name = name
        self.age = age
        
    def show(self):
        return 123
    
    def __getattrbute__(self,item):
        print('---->',item)
        return 999
```

`__getattrbute__`将接管调用的操作，`object`基类中`__getattrbute__`

方法定义了基础的成员运算方法

不论成员/方法是否存在，`__getattrbute__`都会执行。      

在基类的`__getattrbute__`方法中，可以做如下操作

- 返回一些值
- 如果访问不存在的成员时，raise errro

- 其他的处理规则



#### 1.2 分析

```python
class HttpRequest(object):
    def __init__(self):
        pass
    
    def v1(self):
        print('v1')
        
    def v2(self):
        print('v2')
        
class Request(object):
    def __init__(self,request,xx):
        self._request = request
        self.xx = xx
    # 对象中有的成员，不会出发
    # 对象中没有的成员，才会触发
    def __getattr__(self, attr):
        try:
            # 对象中没有的成员，去参数中传入的对象找
            # 这里的寻找方式自定义
            return getattr(self._request, attr)
        except AttributeError:
            return self.__getattribute__(attr)
        
request = HttpRequest()
req.v1()
req.v2()

request. = Request(req,111)
request.xx
request._request.v1()
request._request.v2()
```



前戏结束🎈



### 2.源码分析

```python
class request:

    def __init__(self, request, parsers=None, authenticators=None,
                 negotiator=None, parser_context=None):
        assert isinstance(request, HttpRequest), (
            'The `request` argument must be an instance of '
            '`django.http.HttpRequest`, not `{}.{}`.'
            .format(request.__class__.__module__, request.__class__.__name__)
        )

        self._request = request
        self.parsers = parsers or ()
        self.authenticators = authenticators or ()
        self.negotiator = negotiator or self._default_negotiator()
        self.parser_context = parser_context
        self._data = Empty
        self._files = Empty
        self._full_data = Empty
        self._content_type = Empty
        self._stream = Empty

        if self.parser_context is None:
            self.parser_context = {}
        self.parser_context['request'] = self
        self.parser_context['encoding'] = request.encoding or settings.DEFAULT_CHARSET

        force_user = getattr(request, '_force_auth_user', None)
        force_token = getattr(request, '_force_auth_token', None)
        if force_user is not None or force_token is not None:
            forced_auth = ForcedAuthentication(force_user, force_token)
            self.authenticators = (forced_auth,)

    # 这里的__getattr__方法与前戏中的用法一致        
    def __getattr__(self, attr):
        """
        If an attribute does not exist on this instance, then we also attempt
        to proxy it to the underlying HttpRequest object.
        """
        try:
            return getattr(self._request, attr)
        except AttributeError:
            return self.__getattribute__(attr)


class APIView(View)
    # dispatch中传入的request对象是django的request对象
    def dispatch(self, request, *args, **kwargs):
        """
        `.dispatch()` is pretty much the same as Django's regular dispatch,
        but with extra hooks for startup, finalize, and exception handling.
        """
        self.args = args
        self.kwargs = kwargs
        # 
        request = self.initialize_request(request, *args, **kwargs)
        # 从此开始的request是经过drf封装过后的request
        self.request = request
        self.headers = self.default_response_headers  # deprecate?

        try:
            self.initial(request, *args, **kwargs)

            # Get the appropriate handler method
            if request.method.lower() in self.http_method_names:
                # 通过反射获取get/post/put/delete等方法
                handler = getattr(self, request.method.lower(),
                                  self.http_method_not_allowed)
            else:
                handler = self.http_method_not_allowed
            # 执行相应的方法
            response = handler(request, *args, **kwargs)

        except Exception as exc:
            response = self.handle_exception(exc)

        self.response = self.finalize_response(request, response, *args, **kwargs)
        return self.response
```

