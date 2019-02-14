# sql
* 获取某个模型类内的所有记录
`Entry.objects.all()`
* 过滤器：
`
Entry.objects.filter(pub_date__year=2006)
Entry.objects.all().filter(pub_date__year=2006)
`
* 链接过滤器:最终结果是QuerySet包含标题以“What”开头的所有条目，这些条目在2005年1月30日和当天之间发布。
`Entry.objects.filter(headline__startswith='What').exclude(pub_date__gte=datetime.date.today()).filter(pub_date__gte=datetime.date(2005, 1, 30))`
* filter(**kwargs)：返回QuerySet包含与给定查找参数匹配的新对象。
* exclude(**kwargs)：返回QuerySet包含与给定查找参数不匹配的新对象。

