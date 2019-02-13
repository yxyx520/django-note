# 模型

* 每个模型都是一个 Python 的类，这些类继承 django.db.models.Model
* 把app添加到INSTALLED_APPS之后，执行migrations和migrate才有效
* Charfield类型字段必须添加max_length参数来限制VARCHAR长度
* 字段的通用参数：

** null：默认False，设置为True，当字段为空，数据库将字段设置为NULL
** blank：默认False，设置为True则允许字段为空
** choices：该参数接收一个可迭代的列表或元组（基本单位为二元组）。如果指定了该参数，在实例化该模型时，该字段只能取选项列表中的值。每个二元组的第一个值会储存在数据库中，而第二个值将只会用于显示作用,要获取该字段二元组中相对应的第二个值，使用 get_FOO_display() 方法:

```
YEAR_IN_SCHOOL_CHOICES = (
    ('FR', 'Freshman'),
    ('SO', 'Sophomore'),
    ('JR', 'Junior'),
    ('SR', 'Senior'),
    ('GR', 'Graduate'),
)
```

** default：字段默认值，可以是可调用的字段
** primary_key：设置为True则为主键，主键是只读字段，无法修改，修改相当于新建数据
** unique：设置为True，则该字段的值唯一
** verbose_name：备注名，不填则以变量命名并将下划线替换为空格

* 设置自增主键：

`id = models.AutoField(primary_key=True)`

* 多对一：ForeignKey，多对多：ManyToManyField，一对一：OneToOneField
* Meta类：model中不是数据库中数据的部分，用于人们阅读，比如verbose_name,verbose_name_plural,ordering
* objects：model最重要的属性，model控制，包含增删改查数据库的操作方法：create,delete,update,get,all等
* @property：把一个方法变成属性调用
* __str__()：一个神奇的方法，将所有的对象用字符串表示
* get_absolute_url()：告诉django如何计算对象的url
* super().method()：调用父类的方法

