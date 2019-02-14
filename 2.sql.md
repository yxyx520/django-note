# sql
```
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __str__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    n_comments = models.IntegerField()
    n_pingbacks = models.IntegerField()
    rating = models.IntegerField()

    def __str__(self):
        return self.headline
```
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
* 使用F对象进行跨字段比对
  ```
  from django.db.models import F
  Entry.objects.filter(authors__name=F('blog__name')) #跨字段比对
  Entry.objects.filter(n_comments__gt=F('n_pingbacks') * 2) #支持加减乘除幂运算
  from datetime import timedelta
  Entry.objects.filter(mod_date__gt=F('pub_date') + timedelta(days=3)) #返回在发布后超过3天内修改的所有条目
  ```
* pk主键查询
  ```
  Blog.objects.get(id__exact=14) # Explicit form
  Blog.objects.get(id=14) # __exact is implied
  Blog.objects.get(pk=14) # pk implies id__exact
  Blog.objects.filter(pk__in=[1,4,7])
  Blog.objects.filter(pk__gt=14)
  Entry.objects.filter(blog__id__exact=3) # Explicit form
  Entry.objects.filter(blog__id=3)        # __exact is implied
  Entry.objects.filter(blog__pk=3)        # __pk implies __id__exact
  ```
* 利用缓存减少对数据库的访问
  ```
  #全局查询使用缓存
  queryset = Entry.objects.all()
  print([p.headline for p in queryset]) # Evaluate the query set.
  print([p.pub_date for p in queryset]) # Re-use the cache from the evaluation.
  #切片查询不使用缓存
  queryset = Entry.objects.all()
  print(queryset[5]) # Queries the database
  print(queryset[5]) # Queries the database again
  #评估整个查询集之后使用缓存
  queryset = Entry.objects.all()
  [entry for entry in queryset] # Queries the database
  print(queryset[5]) # Uses cache
  print(queryset[5]) # Uses cache
  #评估整个查询集的操作
  [entry for entry in queryset]
  bool(queryset)
  entry in queryset
  list(queryset)
  ```
* 使用Q对象进行复杂查找
  ```
  Q(question__startswith='Who') | Q(question__startswith='What') #以Who或者What开头
  Q(question__startswith='Who') & Q(question__endwith='What') #以Who开头并且What结尾
  Q(question__startswith='Who') | ~Q(pub_date__year=2005) #以Who或者不等于2005年
  ``` 
  ```
  Poll.objects.get(
    Q(question__startswith='Who'),
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6))
  )
  等价于：
  SELECT * from polls WHERE question LIKE 'Who%'
    AND (pub_date = '2005-05-02' OR pub_date = '2005-05-06')
  ```
* 比较对象
  ```
  some_entry == other_entry
  some_entry.id == other_entry.id
  ```
* 删除对象
  ```
  Entry.objects.all().delete() #删除所有对象
  Entry.objects.filter(pub_date__year=2005).delete() #删除2005年发布的对象
  ```
* 复制实例(将pk设置为None，save())，如果是继承实例，则需要将pk和id都设置为None
  ```
  blog = Blog(name='My blog', tagline='Blogging is easy')
  blog.save() # blog.pk == 1
  
  blog.pk = None
  blog.save() # blog.pk == 2
  ```
* 一次更新多个对象
  ```
  Entry.objects.all().update(blog=b)
  Entry.objects.update(headline=F('blog__name')) #只能更新当前字段，不能引用链接，此条报错FieldError
  ```
* ForeignKey向下访问
  ```
  b = Blog.objects.get(id=1)
  b.entry_set.all() # Returns all Entry objects related to Blog.

  # b.entry_set is a Manager that returns QuerySets.
  b.entry_set.filter(headline__contains='Lennon') 
  b.entry_set.count()
  ```
* ForeignKey使用related_name覆盖名称
  ```
  blog = ForeignKey(Blog, on_delete=models.CASCADE, related_name='entries')
  b = Blog.objects.get(id=1)
  b.entries.all() # Returns all Entry objects related to Blog.
 
  # b.entries is a Manager that returns QuerySets.
  b.entries.filter(headline__contains='Lennon')
  b.entries.count()
  ```
