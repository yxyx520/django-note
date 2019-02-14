# sql
* 获取某个模型类内的所有记录
  `Entry.objects.all()`
* 过滤器：
  ```
  Entry.objects.filter(pub_date__year=2006)
  Entry.objects.all().filter(pub_date__year=2006)
  ```
* 链接过滤器:最终结果是QuerySet包含标题以“What”开头的所有条目，这些条目在2005年1月30日和当天之间发布。
  `Entry.objects.filter(headline__startswith='What').exclude(pub_date__gte=datetime.date.today()).filter(pub_date__gte=datetime.date(2005, 1, 30))`
* filter(**kwargs)：返回QuerySet包含与给定查找参数匹配的新对象。
* exclude(**kwargs)：返回QuerySet包含与给定查找参数不匹配的新对象。
* get(**kwargs)：检索单个对象
  `one_entry = Entry.objects.get(pk=1)`
* all()：检索所有对象
  `one_entry = Entry.objects.all()`
* order_by(**kwargs)：按照输入的参数排序
* 限制查询长度，不支持负索引[-1]，不能对切片进行过滤操作
  ```
  Entry.objects.all()[:5]
  Entry.objects.all()[:10:2]
  ```
* 基本查找关键字参数采用表单field__lookuptype=value。（这是一个双重下划线）。例如：
  `Entry.objects.filter(pub_date__lte='2006-01-01')`
* 查找中指定的字段必须是模型字段的名称。但是有一个例外，如果ForeignKey你可以指定后缀的字段名称_id。在这种情况下，value参数应包含外部模型主键的原始值。例如：
  `Entry.objects.filter(blog_id=4)`
* exact：精确匹配，iexact不区分大小写
  ```
  Entry.objects.get(headline__exact="Cat bites dog")
  
  Blog.objects.get(id__exact=14) 等价于 Blog.objects.get(id=14)
  ```
* contains：包含，icontains不区分大小写
  `Entry.objects.get(headline__contains='Lennon')` 

