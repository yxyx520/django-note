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

* 设置自增主键：

`id = models.AutoField(primary_key=True)`


