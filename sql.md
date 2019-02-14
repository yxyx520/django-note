# sql
* 获取某个模型类内的所有记录
  ```Entry.objects.all()```
* 过滤器：
  ```
  Entry.objects.filter(pub_date__year=2006)
  Entry.objects.all().filter(pub_date__year=2006)
  ```
* 链接过滤器:最终结果是QuerySet包含标题以“What”开头的所有条目，这些条目在2005年1月30日和当天之间发布。
  ```
  Entry.objects.filter(headline__startswith='What').exclude(pub_date__gte=datetime.date.today()).filter(pub_date__gte=datetime.date(2005, 1, 30))
  ```
* filter(**kwargs)：返回QuerySet包含与给定查找参数匹配的新对象。
* exclude(**kwargs)：返回QuerySet包含与给定查找参数不匹配的新对象。
* get(**kwargs)：检索单个对象
  ```
  one_entry = Entry.objects.get(pk=1)
  ```
* all()：检索所有对象
  ```
  one_entry = Entry.objects.all()
  ```
* order_by(**kwargs)：按照输入的参数排序
* 限制查询长度，不支持负索引[-1]，不能对切片进行过滤操作
  ```
  Entry.objects.all()[:5]
  Entry.objects.all()[:10:2]
  ```
* 基本查找关键字参数采用表单field__lookuptype=value。（这是一个双重下划线）。例如：
  ```
  Entry.objects.filter(pub_date__lte='2006-01-01')
  ```
* 查找中指定的字段必须是模型字段的名称。但是有一个例外，如果ForeignKey你可以指定后缀的字段名称_id。在这种情况下，value参数应包含外部模型主键的原始值。例如：
  ```
  Entry.objects.filter(blog_id=4)
  ```
* exact：精确匹配，iexact不区分大小写
  ```
  Entry.objects.get(headline__exact="Cat bites dog")
  Blog.objects.get(id__exact=14) 等价于 Blog.objects.get(id=14)
  ```
* contains：包含，icontains不区分大小写
  ```
  Entry.objects.get(headline__contains='Lennon')
  ```
* startswith,endswith：分别以搜索开始和结束。还有一些不区分大小写的版本叫做istartswith和 iendswith。
* 跨关系查找：要跨越关系，只需使用跨模型的相关字段的字段名称，用双下划线分隔，直到到达所需的字段。这个例子检索所有Entry与对象Blog，其name 为：'Beatles Blog'：
  ```
  Entry.objects.filter(blog__name='Beatles Blog')
  ```
* 跨越多值关系：类名__属性__表达式
  ```
  Blog.objects.filter(entry__headline__contains='Lennon', entry__pub_date__year=2008) #选择所有包含标题中包含“Lennon”且2008年发布的条目的博客（同时满足两个条件）
  Blog.objects.filter(entry__headline__contains='Lennon').filter(entry__pub_date__year=2008) #选择标题 中包含“Lennon”条目的所有博客以及 2008年发布的条目（满足两个条件中的一个）
  ```
* from django.db.models import F
  ```
  Entry.objects.filter(authors__name=F('blog__name')) #跨字段比对
  Entry.objects.filter(n_comments__gt=F('n_pingbacks') * 2) #支持加减乘除幂运算
  from datetime import timedelta
  Entry.objects.filter(mod_date__gt=F('pub_date') + timedelta(days=3)) #返回在发布后超过3天内修改的所有条目
  ```
* 
