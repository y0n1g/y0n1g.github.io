---
title: Django的模式
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
django的model提供了各种函数来与数据库交互. 这一层叫做ORM.  
有了这一层,对数据的增删改查就会比较方便.
model屏蔽了对底层数据库的操作,为用户提供了统一的数据模型和接口.

# model

## 表定义
每个models.Model,都会被Django创建一个表. 表的字段由models.Field参数来定义.Field有很多种,对应数据库的不同数据类型.
    

## Field的共同属性(Attribute)
表的每一列都由不同的Field类定义. Filed定义了很多共同属性,可以在声明时直接指定.比如:

+ primary_key. 该表的主键.如果没有任何一列(field)设置,django会自动生存一个主键

    id = models.AutoField(primary_key=True)

+ verbose. 为列设置一个可读的名字. 默认放在可以第一个字段用位置传递. 但是对于n-n和1-n的情形必须用verbose明确指明,因为它们的第一个字段必须是模块类. 如果没有verbose,Ddjango会按照这个attribute名称自动生成一个,会吧_变为空格.

    first_name = models.CharField("person's first name", max_length=30)
    first_name = models.CharField(verbose="person's first name", max_length=30)

+ unique. True时限定某列值必须唯一.并且自动会为这列创建索引.
+ unique_for_date. 限定某列(DateField或DateTimeField)与本列一起唯一.即某个时刻或日期只有一个当前值.
+ unique_for_year/unique_for_month. 
+ db_column. 可以用来自定义数据库中的表名.不用的话django会自动生成
+ db_index. True时是将在这列上创建索引.
+ default. 默认值或者callabe object. 注意,不能是mutable对象,否则会指向同一个对象;解决方法是用函数返回一个mutable对象.

+ blank. 默认False.是否允许该字段为空,就是说添加一行数据时是否可以不设置这个值.对于不是必须的字段,最好设置为True. 这是设计字段时必须考虑的问题. 对于日期,时间,数字等字段,仅仅设置blank还不够,还需要null字段.  注意, 对于False的Field,每次update时都必须设置这个值! 在实际运行中, 没有设置会导致exception. 在apache环境中, 这个异常只能在apache的error_log中发现.
+ null. 是否允许日期,时间,数字等字段为null. 注意,不要用在基于 *string* 的类型上, 比如CharField和TextField. 当日期,时间,数字等字段上blank设置为True时,必须把这个null也一起设置为True, 此时Django默认会用NULL来填写该字段. 注意,GenericIPAddressField也需要跟日期时间一样对待,也需要设置这个值. 对于ForeignKey,也需要设置这个值. 是不是说,除了string以外其他字段需要时都得设置?

+ choice. 可以把字段变为html中的selector类型,从列表中选择.
+ editable. 默认为True.设置为False后,将不会出现在admin和任何ModelForm. 
+ validator. 为这列设置验证函数

## Field的类型

### 自动类型
+ AutoField. 是一个IntegerField,用于自增的ID.
+ BigAutoField. 类似AutoField,是一个64位值.
+ BigIntegerField. 类似IntegerField,64位,保证支持从-9223372036854775808到 9223372036854775807

### 其他
+ BinaryField.二进制类型. 可以存储字节数据. 注意不要用这个存储文件. 
+ EmailField(max_length=254, **options). 就是一个CharField,用EmailValidator验证输入.
+ GenericIPAddressField(protocol='both', unpack_ipv4=False, **options). 
+ SlugField(max_length=50, **options).报纸术语,只包含字符,数字,-和_.一般用于URL.
+ URLField(max_length=200)
+ UUIDField. 

### Bool
+ BooleanField. 默认值是None.如果要支持null,则使用NullBooleanField
+ NullBooleanField. 允许NULL值.null=True

### 文本
+ CharField(max_length=None). 不同的db支持度不一样.
+ TextField. max_length只会影响widget,即输入时用户界面的限制,而不会影响底层数据库. 如果要限制数据库的字段长度,用CharField
+ _CommaSeparatedIntegerField_ 在1.9后就失效了. 现在的建议方法是使用CharField,再加上validator=[validate_comma_separated_integer_list]

### 日期和时间
+ DateField(auto_now=False, auto_now_add=False).  auto_now会在用方法Model.save()时每次更新数据时(但是其他方法不管用. 比如QuerySet.update()),自动设置为当前日期.可以用于'最近更新'这种日期戳.  auto_now_add在第一次创建时把日期设为现在. 注意,auto_now/auto_now_add/default三个值一起两两互斥,声明field时只能用其中一个.
+ DateTimeField(auto_now=False, auto_now_add=False). 日期和时间.
+ DurationField. 用于存储时间长度.对应python的timedelta.
+ TimeField(auto_now=False, auto_now_add=False)

### 数字
+ DecimalField(max_digits=None, decimal_places=None, **options) 固定精度十进制数.
+ FloadField. 浮点数.
+ IntegerField. 整数,[-2147483648,2147483647].
+ PositiveIntegerField. [0,2147483647 ]
+ PositiveSmallIntegerField. [0, 32767]
+ SmallIntegerField. [-32768, 32767]

### 文件和路径
+ FileField(upload_to=None, max_length=100, **options). upload_to支持普通目录值和指定了时间格式的值.比如:

    class MyModel(models.Model):
        # file will be uploaded to MEDIA_ROOT/uploads
        upload = models.FileField(upload_to='uploads/')
        # or...
        # file will be saved to MEDIA_ROOT/uploads/2015/01/30
        upload = models.FileField(upload_to='uploads/%Y/%m/%d/')
    
upload_to也可以是callable.比如:

    def user_directory_path(instance, filename):
        # file will be uploaded to MEDIA_ROOT/user_<id>/<filename>
        return 'user_{0}/{1}'.format(instance.user.id, filename)

    class MyModel(models.Model):
        upload = models.FileField(upload_to=user_directory_path)
   
注意,在设置中,要定义MEDIA_ROOT位置. 
同时,不支持primary_key和unique.
数据库中存储的是对于MEDIA_ROOT的相对路径

+ ImageField(upload_to=None, height_field=None, width_field=None, max_length=100, **options), 集成子FileField,添加了验证上传文件为图片的功能. *需要Pillow库*
+ FieldFile. 当访问FileField时,Django返回一个FieldFile,用于访问底层file. 它是一个wrapper,对应python的各种file操作的API.
+ FilePathField(path=None, match=None, recursive=False, max_length=100, **options) 其实是个CharField,只是其值是有限的范围.

### 表的关系: 1-1/1-n/n-n关系字段
+ OneToOneField(othermodel, on_delete, parent_link=False). 用于1-1的关系.

+ ForeignKey(othermodel, on_delete, **options). 用于n-1的关系. othermodel就是作为外键的Model. 

on_delete支持的值:

    models.CASCADE - 模拟SQL的约束ON DELETE CASCADE
    PROTECT - 禁止引用的相关object删除, 触发ProtectedError异常
    SET_NULL - 只有当Null=True时,会被设置.
    SET_DEFAULT - 设置为默认值.
    SET()
可以创建自身引用的层级关系. 比如一个组可能是多个小组的结合.
models.ForeignKey('self', on_delete=models.CASCADE).
index在外键上是自动创建的.

limit_choices_to. 可以是一个集合,或者一个函数. 指明othermodel中,哪些是可以用的.

related_name. 反向解析时使用的名字. 同时也是related_query_name

related_query_name. 

to_field. 产生关系的字段,默认是primary key. 如果要自定义,则该字段必须设置unique=True

db_constraint. 是否为这个外键创建约束.

swappable.


+ ManyToManyField(othermodel, **options). 用于n-n的关系.多对多的关系简单来说就是两张表里的数据，每一行都可以对应另外一张表里的多行数据


# 数据库操作

## 创建对象和保存

    fruit = Fruit.objects.create(name='Apple')
    _会自动保存!_
    fruit2 = Fruit(name='Pear')
    _注意,不会自动保存!_
    fruit.save()

## 关系添加

使用add函数,参考Making queries

    >>> from blog.models import Entry
    >>> entry = Entry.objects.get(pk=1)
    >>> cheese_blog = Blog.objects.get(name="Cheddar Talk")
    >>> entry.blog = cheese_blog  # 这篇entry是cheese_blog, n-1
    >>> entry.save()

    >>> from blog.models import Author
    >>> joe = Author.objects.create(name="Joe")
    >>> entry.authors.add(joe)

    >>> john = Author.objects.create(name="John")
    >>> paul = Author.objects.create(name="Paul")
    >>> george = Author.objects.create(name="George")
    >>> ringo = Author.objects.create(name="Ringo")
    >>> entry.authors.add(john, paul, george, ringo)  # n-n

## 查询

通过Model的Manager,构造QuerySet查询,获取models的对象.每个model至少有一个Manager,默认叫做objects.
QuerySet是数据库的对象集合,可以用0个/1个/多个filter来过滤结果.就SQL而言,QuerySet等价于SELECT, 一个filter对应于一个WHERE或LIMIT等限定语句.

    Fruite.objects

完整的文档应该搜索 QuerySet API

   SomeModel.objects.filter(...).exists() # 检查过滤项是否为空. 


### 用all获取所有项

    Fruite.objects.all()

### 用filter获取某种过滤条件的项\*集合\*

最常用的两种过滤方法:

filter(): 返回符合过滤条件的结果

exclude(): 返回不符合过滤条件的结果.


    Fruite.objects.filter(product_data__year=2016)
    等价于
    Fruite.objects.all().filter(product_data__year=2016)
    Fruite.objects.filter(name__startswith='A')
    Fruite.objects.filter(name__endwith='r')
    Fruite.objects.filter(name__exact='Apple')
    Fruite.objects.filter(name='Apple')
    _exact默认可以省略_
    Fruite.objects.filter(name__iexact='apple')
    _大小写忽略_
    Fruite.objects.filter(name__contains='melon')
    _LIKE_
    Fruite.objects.filter(name__icontains='melon')
    _大小写无关的LIKE_

### 多重过滤

    Fruite.objects.filter(name__contains='melon', product_data__year=2016)
    _多重过滤_
    Entry.objects.filter(
       headline__startswith='What'
    ).exclude(
       pub_date__gte=datetime.date.today()
    ).filter(
       pub_date__gte=datetime(2005, 1, 30)
    )

### 用get获取一个对象.

当确认只有一个对象时,可以用get.

    f = Fruite.objects.get(name='Apple')
    _注意,如果查询到多个值(MultipleObjectsReturned)匹配或者没有值(DoesNotExist)匹配,都会触发异常_
    _因此,应该用于 unique=True 的字段_
    f = Fruite.objects.get(name__icontains='melon')

在获取了对象以后,就可以用f.name等方式获取所有属性的值. 同时,所有的对象都有一些通用函数可以使用.

+ 限制返回数量:

    Fruite.objects.all(origin='北京')[:30]
    Fruite.objects.all()[30:60]
    _负数索引不支持: [-1]_

+ 用order_by获取经过排序的对象

    Fruite.objects.order_by('origin')
    Fruite.objects.order_by('-origin')
    _逆向排序_
    Fruite.objects.order_by('origin', 'name')

+ 可以连锁查询

    Publisher.objects.filter(country="U.S.A.").order_by("-name")

+ 删除

    Fruite.objects.get(name='Apple').delete()
    _删除一个_
    Publisher.objects.filter(country="U.S.A.").delete()
    _删除很多_

## 查询时的一些有用的函数

### 返回queryset的函数

#### select_related()

    对于n-1或者1-1的关系,如果后续操作需要用到其他指向的object,可以再filter/get时直接用select_related这个函数预先查询其他object.这样可以减少db的访问次数.

#### prefetch_related()

    类似select_related,但是用于n-n的情况. 

#### defer()

    当filter返回的结果,Fields很长又不会使用时,就用defer告诉Django这些字段应该延迟获取.这是高级优化函数,只有在仔细分析了查询过程和处理过程时并且做了性能比较时,才考虑使用.
    
    Entry.objects.defer("headline", "body")
    # Defers both the body and headline fields.
    Entry.objects.defer("body").filter(rating=5).defer("headline")

#### only()

    类似defer,作用相反.

#### using()

    当有多个db可以使用时,用using来指明在哪个db上做操作.


#### select_for_update()
  
锁定相应行,事务(transaction)结束.
    
    entries = Entry.objects.select_for_update().filter(author=request.user)
    
其他新的事务会被阻塞.nowait=True可以不阻塞,不过在对queryset做evaluate时,冲突的行会报DatabaseError异常.  *mysql* 不支持nowait,传入nowait会导致DatabaseError异常.
    
貌似不能工作在automatic commit状态?

### 不返回queryset的函数

这些函数不使用cache.
    
#### get()
    
返回一行. 当确认只有一行时,也可以不参数.
    
    entry = Entry.objects.filter(...).exclude(...).get()
    
#### create()

创建一行. 如下两种方法一样:
    
    p = Person.objects.create(first_name="Bruce", last_name="Springsteen"
    
    p = Person(first_name="Bruce", last_name="Springsteen")
p.save(force_insert=True)
    
#### get_or_create()
    
#### update_or_create()

    obj, created = Person.objects.update_or_create(
        first_name='John', last_name='Lennon',
        defaults={'first_name': 'Bob'},
    )

#### bulk_create()

#### count()

满足queryset的数量.如果只需要查看总数,就用这个函数; 如果需要iterate每个元素,还是可以用len()

    # Returns the total number of entries in the database.
    Entry.objects.count()
    
    # Returns the number of entries whose headline contains 'Lennon'
    Entry.objects.filter(headline__contains='Lennon').count()

#### in_bulk()



#### iterator()

Django的queryset有cache的功能.而iterator不使用.

#### latest()

latest(field_name=None)

返回field_name字段(日期时间)最新的行.

    Entry.objects.latest('pub_date')

如果Meta中get_latest_by有设置,则field_name字段可以省略.
同get()一样,earliest()和latest()也可能返回DoesNotExist异常.

#### earliest()

#### first()

#### last()

#### aggregate()

各种聚合统计方法.

#### exists()

如果只检查存在性,就用这个函数.
    
    if some_queryset.exists():
    ...
    
如果在检查了存在以后,还要对内容做进一步的处理,就应该直接检查.

    if some_queryset:
    ...

#### update()

它会返回影响了的行数.

#### delete()


### Field查询

Field查询用于get(),filter()和exclude()三个函数, 对应于SQL的WHERE语句.

#### exact

    Entry.objects.get(id__exact=14)
    Entry.objects.get(id__exact=None)

    SELECT ... WHERE id = 14;
    SELECT ... WHERE id IS NULL;

#### iexact

大小写无关的exact


#### contains

#### icontains

#### in

在列表中.

    Entry.objects.filter(id__in=[1, 3, 4])

    SELECT ... WHERE id IN (1, 3, 4);

#### gt/gte/lt/lte

#### startswith/istartswith

#### endswith/iendswith

#### range/date/year/month/day/week_day/hour/minute/second/

#### isnull

#### regex/iregex


