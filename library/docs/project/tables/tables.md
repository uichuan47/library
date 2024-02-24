# 1.表结构设计测试（v1）



**所有表结构不涉及物理删除**

```python
class ActiveBaseModel(models.Model):
    active = models.SmallIntegerField(verbose_name='状态', default=1, choices=((1, '激活'), (0, '删除'),))

    class Meta:
        abstract = True
```



这里的`ActiveBaseModel`类继承了`models.Model`

之后的建表的类将继承`ActiveBaseModel`类，同时继承`models.Model`和`ActiveBaseModel`中的方法和属性

- 管理员

```python
class Administrator(models.Model):
    username = models.CharField(verbose_name='管理员用户名', max_length=32, db_index=True)
    password = models.CharField(verbose_name='管理员密码', max_length=64)
    mobile = models.CharField(verbose_name='手机号', max_length=11)
    email = models.CharField(verbose_name='邮箱', max_length=32, null=True, blank=True)
    create_date = models.DateTimeField(verbose_name='创建日期', auto_now_add=True)

    def __str__(self):
        return self.username
```



- 用户

```python
class Customer(ActiveBaseModel):
    username = models.CharField(verbose_name='用户名', max_length=32, db_index=True)
    password = models.CharField(verbose_name='密码', max_length=64)
    mobile = models.CharField(verbose_name='手机号', max_length=11)
    create_date = models.DateTimeField(verbose_name='创建日期', auto_now_add=True)

    def __str__(self):
        return self.username
```



- 文章

```python
class Article(ActiveBaseModel):
    title = models.CharField(verbose_name="标题", max_length=64)
    author = models.ForeignKey(verbose_name='作者', to="Customer", on_delete=models.CASCADE)
    create_date = models.DateTimeField(verbose_name='创建日期', auto_now_add=True)
    content = models.CharField(verbose_name='内容', null=True, blank=True, max_length=256)
    media_path = models.CharField(verbose_name='媒体路径', max_length=128)

    def __str__(self):
        return self.title
```





在此处先设计简单的表结构，

测试版本中，需要满足的需求包括

- 用户登陆/注册

- 用户&管理员对内容的增删改查

- 管理员数据查询

    

