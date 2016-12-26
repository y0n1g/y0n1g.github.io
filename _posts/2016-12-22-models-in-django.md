---
title: Django的模式
layout: post
category: [tech]
tags: [django,python]
---
{% include JB/setup %}
django的model提供了各种函数来从数据库获取数据.  


+ 创建对象和保存

    fruit = Fruit.objects.create(name='Apple')
    _会自动保存!_
    fruit2 = Fruit(name='Pear')
    _注意,不会自动保存!_
    fruit.save()
    
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

+ 用get获取一个对象

    Fruite.objects.get(name='Apple')
    _注意,查到多个值(MultipleObjectsReturned)匹配或者没有值(DoesNotExist)匹配,都会触发异常_

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


## Field的字段
表的每一列都由不同的Field类定义. Filed定义了很多共同属性,可以在声明时直接指定.比如:

+ primary_key. 该表的主键.如果没有任何一列(field)设置,django会自动生存一个主键.
+ db_index. 是否在这列上创建索引.
+ unique. True时限定某列值必须唯一.并且自动会为这列创建索引.
+ unique_for_date. 限定某列(DateField或DateTimeField)与本列一起唯一.
+ db_column. 可以用来自定义数据库中的表名.不用的话django会自动生成

+ default. 默认值或者callabe object
+ verbose. 为列设置一个可读的名字. 默认可以第一个字段用位置传递. 但是对于n-n和1-n的情形必须用verbose明确指明,因为它们的第一个字段必须是模块类.

+ blank. 该字段是否可以为空,不能用于日期,时间,数字等字段. True, 在admin界面,该字段必须设置. False, 可以把该字段留空
+ null. 是否允许字段为null.当日期,时间,数字等字段上blank设置为True时,必须把这个null也一起设置为True.

+ choice. 可以把字段变为html中的selector类型,从列表中选择.
+ editable. 设置为True后,将不会出现在admin和任何ModelForm. 
+ validator. 为这列设置验证函数

## Field的类型

### Bool
+ BooleanField. 默认值是None.如果要支持null,则使用NullBooleanField
+ NullBooleanField. 允许NULL值.null=True

### 文本
+ CharField(max_length=None). 不同的db支持度不一样.
+ TextField. max_length只会影响widget,即输入时用户界面的限制,而不会影响底层数据库. 如果要限制数据库的字段长度,用CharField
+ CommaSeparatedIntegerField. 在1.9后就失效了. 现在的建议方法是使用CharField,再加上validator=[validate_comma_separated_integer_list]

### 日期和时间
+ DateField(auto_now=False, auto_now_add=False).  auto_now会在每次更新数据是(save()),自动设置为当前日期.可以用于'最近更新'这种日期戳.  auto_now_add在第一次创建时把日期设为现在. 注意,这两个参数和default值一起,两两互斥,声明field时只能用其中一个.
+ DateTimeField(auto_now=False, auto_now_add=False). 日期和时间.
+ DurationField. 对应python的timedelta.
+ TimeField(auto_now=False, auto_now_add=False)

### 数字
+ DecimalField(max_digits=None, decimal_places=None, **options) 固定精度十进制数.
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
+ ImageField(upload_to=None, height_field=None, width_field=None, max_length=100, **options), 集成子FileField,添加了验证上传文件为图片的功能.
+ FilePathField(path=None, match=None, recursive=False, max_length=100, **options)

### 其他
+ EmailField(max_length=254, **options). 就算一个CharField,用EmailValidator验证输入.
+ GenericIPAddressField(protocol='both', unpack_ipv4=False, **options). 
+ URLField(max_length=20)
+ UUIDField. 

### 1-1/1-n/n-n关系字段
+ OneToOneField(othermodel, on_delete, parent_link=False). 用于1-1的关系.
+ ForeignKey(othermodel, on_delete, **options). 用于n-1的关系. on_delete支持的值:models.CASCADE,PROTECT,SET_NULL,SET_DEFAULT,SET()
+ ManyToManyField(othermodel, **options). 用于n-n的关系.多对多的关系简单来说就是两张表里的数据，每一行都可以对应另外一张表里的多行数据

