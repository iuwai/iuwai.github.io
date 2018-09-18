---
title: Django-route
date: 2018-03-10 16:24:29
categories: Python
tags: ["Django", "路由系统"]
---
Django路由系统:动态路由、指定参数名、默认参数
<!-- more -->
## Django路由系统
### 动态路由
urls.py
```python
from django.conf.urls import patterns, include,url
from django.contrib import admin
from web.views import index, login, list
urlpatterns = patterns('',
    url(r'^list/(\d*)/$', list),  #一个参数
    url(r'^list/(\d*)/(\d*)/$', list),  #两个参数，按顺序获取
    url(r'^list/(?P<name>\d*)/(?P<id>\d*)/$', list),  #指定参数名,获取参数时名字必须为name, id
    url(r'^list/(?P<name>\d*)/$', list, {'id':222}),  #默认参数值
)
```

views.py
```python
def list(request, id1, id2):
    print id
    print id1, id2
    return HttpResponse()
```
指定参数名：
```python
def list(request, id, name):
    print name, id
    return HttpResponse()
```
请求链接：http://localhost:9000/list/100         #一个参数
请求链接：http://localhost:9000/list/100/1000    #两个参数
注：请求链接最后必须要加/,如果不加，urls配置中必须要加上终止符：/$,这样请求时会自动加上/