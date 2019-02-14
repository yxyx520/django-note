# 聚合
```
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()

class Publisher(models.Model):
    name = models.CharField(max_length=300)

class Book(models.Model):
    name = models.CharField(max_length=300)
    pages = models.IntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    rating = models.FloatField()
    authors = models.ManyToManyField(Author)
    publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
    pubdate = models.DateField()

class Store(models.Model):
    name = models.CharField(max_length=300)
    books = models.ManyToManyField(Book)
```
* 快速查询
  ```
  Book.objects.count() #书总类目数量
  Book.objects.filter(publisher__name='BaloneyPress').count() #publisher__name='BaloneyPress'书的数量
  ```
* Avg，Min，Max，Count，FloatField，Q
  ```
  from django.db.models import Avg,Min,Max,Count,FloatField,Q
  
  Book.objects.all().aggregate(Avg('price'))
  
  Book.objects.aggregate(price_diff=Max('price', output_field=FloatField()) - Avg('price'))

  above_5 = Count('book', filter=Q(book__rating__gt=5))
  
  Book.objects.aggregate(Avg('price'), Max('price'), Min('price'))
  {'price__avg': 34.35, 'price__max': Decimal('81.20'), 'price__min': Decimal('12.99')}
  ```
* `Book.objects.all().aggregate(Avg('price'))` 等价于 `Book.objects.aggregate(Avg('price'))`
* aggregate：取出一个集合的汇总数据，无法进行分组
  ```
  >>>Book.objects.aggregate(Count('authors'))
  3
  ```
* annotate：分组统计
  ```
  >>>q = Book.objects.annotate(Count('authors'))
  >>>q[0]
  <Book: The Definitive Guide to Django>
  >>>q[0].authors__count
  2
  ```
* aggregate()，注释的名称将自动从聚合函数的名称和要聚合的字段的名称派生。您可以通过在指定注释时提供别名来覆盖此默认名称：
  ```
  >>> q = Book.objects.annotate(num_authors=Count('authors'))
  >>> q[0].num_authors
  2
  >>> q[1].num_authors
  1
  ```
* order_by()：排序，按照书籍作者的数量排序
  ```
  Book.objects.annotate(num_authors=Count('authors')).order_by('num_authors')
  ```
* values()：作者会按名字分组，所以你只能得到不重名的作者分组的注解值。这意味着如果你有两个作者同名，那么他们原本各自的查询结果将被合并到同一个结果中;两个作者的所有评分都将被计算为一个平均分：
  ```
  Author.objects.values('name').annotate(average_rating=Avg('book__rating'))
  ```
* values()和annotate()顺序问题，value()在前则根据value进行分组，value在后则是限制输出字段
* annotate()和aggregate()联用,例如，如果要计算每本书的平均作者数，首先使用作者计数对书籍集进行注释，然后汇总该作者计数，引用注释字段：
  ```
  Book.objects.annotate(num_authors=Count('authors')).aggregate(Avg('num_authors'))
  ```
