暂未翻译

官方文档[点此](https://docs.djangoproject.com/en/1.8/intro/tutorial03/)

---

# 创建你的第一个 Django 项目， 第三部分

这一篇从[第二部分（zh）](part2.md)结尾的地方继续讲起。我们将继续编写投票应用，并且聚焦于如何创建公用界面——也被称为“视图”。

## 设计哲学

Django 中的视图的概念是「一类具有相同功能和模板的网页的集合」。比如，在一个博客应用中，你可能会创建如下几个视图：

- 博客首页——展示最近的几项内容。
- 内容“详情”页——
- 以年为单位的归档页——展示选中的年份里各个月份创建的内容。
- 以月为单位的归档页——展示选中的月份里各天创建的内容。
- 以天为单位的归档页——展示选中天里创建的所有内容。
- 评论处理器——用于响应为一项内容添加评论的操作。

而在投票应用中，我们需要下列几个视图：

- 问题索引页——展示最近的几个投票问题。
- 问题详情页——展示某个投票的问题和不带结果的选项列表。
- 问题结果页——展示某个投票的结果。
- 投票处理器——用于响应用户为某个问题的特定选项投票的操作。

在 Django 中，网页和其他内容都是从视图派生而来。每一个视图表现为一个简单的 Python 函数（或者说方法，如果是在基于类的视图里的话）。Django 将会根据用户请求的 URL 来选择使用哪个视图（更准确的说，是根据 URL 中域名之后的部分）。

在你上网的过程中，很可能看见过像这样的 URL：“ME2/Sites/dirmod.asp?sid=&type=gen&mod=Core+Pages&gid=A6CD4967199A42D9B65B1B”。别担心，Django 里的 URL 要比这优雅的多！

一个 URL 模式定义了某种 URL 的基本格式——举个例子：
**/newsarchive/<year>/<month>/**

为了将 URL 和视图关联起来，Django 使用了 “URLconfs” 来配置。URLconf 将 URL 模式（表现为一个正则表达式）映射到视图。

本教程只会介绍 URLconf 的基础内容，你可以看看 **[django.core.urlresolvers](https://docs.djangoproject.com/en/1.8/ref/urlresolvers/#module-django.core.urlresolvers)** 以获取更多内容。

## 编写第一个视图

让我们开始编写第一个视图吧。打开 **polls/view.py**，把下面这些 Python 代码输入进去：

```python
# polls/views.py

from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")

```

这是 Django 中最简单的视图。如果想看见效果，我们需要将一个 URL 映射到它——这就是我们需要 URLconf 的原因了。

为了创建 URLconf，请在 polls 目录里新建一个 **url.py** 文件。你的应用目录现在看起来应该是这样：

```text
polls/
    __init__.py
    admin.py
    models.py
    tests.py
    urls.py
    views.py
```

在 **polls/urls.py** 中，输入如下代码：

```python
# polls/urls.py

from django.conf.urls import url

from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]

```

下一步是要在根 URLconf 文件中指定我们创建的 **polls.urls** 模块。在 **mysite/urls.py** 文件里插入一个 **[include()](https://docs.djangoproject.com/en/1.8/ref/urls/#django.conf.urls.include)**， 如下：

```python
# mysite/urls.py

from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', include(admin.site.urls)),
]

```

> **和你看到的不一样？**
>
> 如果拟在 **urlpatterns** 的定义前面看到了 **admin.autodiscover()** 语句，你可能使用了和教程不匹配的 Django 版本。你可以切换到旧版本的教程或者升级 Django。

你现在把 **index** 视图添加进了 URLconf。用你的浏览器访问 [http://localhost:8000/polls/](http://localhost:8000/polls/)，你应该能够看见 “Hello, world. You're at the polls index.”，这是你在 **index** 视图中定义的。

**url()** 函数接收四个参数，两个是必需的： **regex** 和 **view**，还有两个是可选的：**kwargs** 和 **name**。现在，我们花点时间来讲讲这些参数的作用。
