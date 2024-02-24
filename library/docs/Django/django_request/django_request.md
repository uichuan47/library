# 13.djangoRequest对象

[官方文档🎈](https://docs.djangoproject.com/zh-hans/3.2/ref/request-response/#httpresponse-objects)



HTTP应用的信息是通过**请求报文**和**响应报文**传递的

django将客户端发送的请求报文的信息封装成了HttpRequest对象

该对象由 HttpRequest 类创建，

每一个请求都会生成一个 HttpRequest 对象，django会将这个对象自动传递给响应的视图函数，一般视图函数约定俗成地使用 **request** 参数承接这个对象。

```python
def login(request):
    ...
    ...
    return ...
```



request对象中封装的属性

```
	django将请求报文中的请求行、首部信息、内容主体封装成 HttpRequest 类中的属性。除了特殊说明的之外，其他均为只读的
```

#### 1.HttpRequest.scheme

一个字符串，代表请求的方案，一般为 ‘http’ 或 ‘https’。

#### 2.HttpRequest.body

　　一个字符串，代表请求报文的主体。在处理非 HTTP 形式的报文时非常有用，例如：二进制图片、XML等。

　　但是，如果要处理表单数据的时候，推荐还是使用 HttpRequest.POST 。

　　另外，我们还可以用 python 的类文件方法去操作它，详情参考 HttpRequest.read() 。

#### 3.HttpRequest.path

字符串，标识请求的路径组件，不包含域名，

这里的值匹配的是路由系统中的路径



#### 4.HttpRequest.method

字符串，用来表示请求使用的HTTP方法，

```python
def index(request):
    if request.method == "GET":
        return render(request,"xx.html")
    if request.method == "POST":
        ...
        return JsonResponse({'status':True,'detail':'xxx'})
```



#### 5.HttpRequest.encoding

一个字符串，表示提交的数据的编码方式（如果为 `None` 则表示使用 `DEFAULT_CHARSET` 的设置，默认为 'utf-8'）。这个属性是**可写**的，你可以修改它来修改访问表单数据使用的编码。

接下来对属性的任何访问（例如从 `GET` 或 `POST` 中读取数据）将使用新的 `encoding` 值。如果你知道表单数据的编码不是 `DEFAULT_CHARSET` ，则使用它。



#### 6.HttpRequest.GET

一个类似字典的对象，包涵HTTP GET的所有参数，[QueryDict](https://www.cnblogs.com/scolia/p/5634591.html)对象



#### 7.HttpRequest.POST

一个类似于字典的对象，如果请求中包含表单数据，则将这些数据封装成 `QueryDict` 对象。



#### 8.HttpRequest.COOKIE

得到标准的python字典，包涵所有cookie，键和值都是字符串



#### 9.HttpRequest.FILES

类似于字典的对象，包涵所有的上传文件信息。

`FILES` 中的每个键为`<input type="file" name="" />` 中的`name`，值则为对应的数据。



#### 10.HttpRequest.META

标准python字典，包含所有的HTTP首部

- `CONTENT_LENGTH` —— 请求的正文的长度（是一个字符串）。
- `CONTENT_TYPE` —— 请求的正文的MIME 类型。
- `HTTP_ACCEPT` —— 响应可接收的Content-Type。
- `HTTP_ACCEPT_ENCODING` —— 响应可接收的编码。
- `HTTP_ACCEPT_LANGUAGE` —— 响应可接收的语言。
- `HTTP_HOST` —— 客服端发送的HTTP Host 头部。
- `HTTP_REFERER` —— Referring 页面。
- `HTTP_USER_AGENT` —— 客户端的user-agent 字符串。
- `QUERY_STRING` —— 单个字符串形式的查询字符串（未解析过的形式）。
- `REMOTE_ADDR` —— 客户端的IP 地址。
- `REMOTE_HOST` —— 客户端的主机名。
- `REMOTE_USER` —— 服务器认证后的用户。
- `REQUEST_METHOD` —— 一个字符串，例如`"GET"` 或`"POST"`。
- `SERVER_NAME` —— 服务器的主机名。
- `SERVER_PORT` —— 服务器的端口（是一个字符串）。



