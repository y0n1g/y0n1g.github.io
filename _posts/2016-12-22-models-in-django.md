---
title: Django的模式
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
django的model提供了各种函数来与数据库交互. 这一层叫做ORM.  
有了这一层,对数据的增删改查就会比较方便.


+ 创建对象和保存

    fruit = Fruit.objects.create(name='Apple')
    _会自动保存!_
    fruit2 = Fruit(name='Pear')
    _注意,不会自动保存!_
    fruit.save()


## 表定义
每个models.Model,都会被Django创建一个表. 表的字段由models.Field参数来定义.Field有很多种,对应数据库的不同数据类型.
    
## 查询
通过Model的Manager,用QuerySet查询.每个model至少有一个Manager,默认叫做objects.

    Fruite.objects

+ 用all获取所有项

    Fruite.objects.all()

+ 用filter获取某种过滤条件的项*集合*

    Fruite.objects.filter(product_data__year=2016)
    Fruite.objects.filter(name__startswith='A')
    Fruite.objects.filter(name__endwith='r')
    Fruite.objects.filter(name__exact='Apple')
    _exact默认可以省略_
    Fruite.objects.filter(name__iexact='apple')
    _大小写忽略_
    Fruite.objects.filter(name__contains='melon')
    _LIKE_
    Fruite.objects.filter(name__icontains='melon')
    _大小写无关的LIKE_
    Fruite.objects.filter(name__contains='melon', product_data__year=2016)
    _多重过滤_

+ 用get获取一个对象.

    f = Fruite.objects.get(name='Apple')
    _注意,如果查询到多个值(MultipleObjectsReturned)匹配或者没有值(DoesNotExist)匹配,都会触发异常_
    _因此,应该用于 unique=True 的字段_

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

+ blank. 该字段是否可以为空. 不能完全用于日期,时间,数字等字段. True, 在admin界面,该字段必须设置. False, 可以把该字段留空. 
+ null. 是否允许字段为null.当日期,时间,数字等字段上blank设置为True时,必须把这个null也一起设置为True, 此时Django默认会用NULL来填写该字段.

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
+ EmailField(max_length=254, **options). 就算一个CharField,用EmailValidator验证输入.
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
+ DateField(auto_now=False, auto_now_add=False).  auto_now会在用方法Model.save()时每次更新数据时(但是其他方法不管用. 比如QuerySet.update()),自动设置为当前日期.可以用于'最近更新'这种日期戳.  auto_now_add在第一次创建时把日期设为现在. 注意,duto_now/auto_now_add/default三个值一起两两互斥,声明field时只能用其中一个.
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

