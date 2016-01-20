暂未翻译

官方文档[点此](https://docs.djangoproject.com/en/1.8/intro/tutorial03/)

---

# 创建你的第一个 Django 项目， 第三部分

这一篇从[第二部分（zh）](part2.md)结尾的地方继续讲起。我们将继续编写投票应用，并且聚焦于如何创建公用界面——也被称为“视图”。

## 设计哲学

Django 中的视图的概念是「一类具有相同功能和模板的网页的集合」。比如，在一个博客应用中，你可能会创建如下几个视图：

- 博客首页——展示最近的几项内容。
- 内容“详情”页——详细展示某项内容。
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
**/newsarchive/<year\>/<month\>/**

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

### url() 参数之 regex

术语 “regex” 通常作为 正则表达式（regular expression）的简写出现，它是一种用于匹配字符串的语法，我们用它来编写 URL 模式。Django 从第一个开始，按顺序依次使用列表里的正则表达式来尝试匹配请求的 URL，直到遇到一个可以匹配的表达式。

请注意这些正则式并不尝试匹配 GET 或 POST 的参数。也不匹配域名部分。举例说明，在用户请求 **http://www.example.com/myapp/** 时,URLconf 将会尝试匹配 **myapp/**。在请求 **http://www.example.com/myapp/?page=3** 时，也只会尝试匹配 **myapp/**。

如果你需要一些关于正则表达式的帮助，看看[维基百科的条目](https://en.wikipedia.org/wiki/Regular_expression)或者 [Python 的 re 模块的文档](https://docs.python.org/3/library/re.html#module-re)。另外，Jeffrey Friedl 的《Mastering Regular Expressions》这本书也很值得推荐。然而，在我们的实际使用中，你不不需要非常精通正则表达式，你只需要了解如何捕获简单的模式就够了。事实上，复杂的正则表达式的查找性能会更低，所以，最好不要过度依赖正则表达式的强大功能。

最后，做一些性能方面的说明：在 URLconf 第一次被加载的时候，那些正则表达式都会被自动编译。编译过后的正则表达式相当的快（只要他们不像上面提到的那样非常复杂的话）。

### url() 参数之 view

当 Django 找到匹配的正则表达式时，就会调用这个视图函数。调用时传递的第一个参数是一个 **[HttpRequest](https://docs.djangoproject.com/en/1.8/ref/request-response/#django.http.HttpRequest)** 对象，后续的参数是所有被正则表达数捕捉的部分。如果正则式使用简单捕获，捕获结果会作为位置参数传递；如果使用名字捕获，捕获结果会作为关键字参数传递。我们稍后会给出例子。

### url() 参数之 kwargs

任意个关键字参数可以作为一个字典传递给目标视图函数。在我们的教程中不会使用这一特性。

### url() 参数之 name

给你的 URL 起个名字，让你能在 Django 的模板里无二义性的引用到它。这个非常有用的特性允许你只用更改一个文件就能全局性的改变某个 URL。

## 编写更多视图

现在让我们向 **polls/views.py** 里添加更多视图。这些视图有一些不同，因为他们接收参数：

```python
# polls/views.py

def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

把这些新视图添加进 **polls.urls** 模块里，只要添加几个 url() 函数调用就行：

```python
# polls/urls.py

from . import views

urlpatterns = [
    # ex: /polls/
    url(r'^$', views.index, name='index'),
    # ex: /polls/5/
    url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
    # ex: /polls/5/results/
    url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results'),
    # ex: /polls/5/vote/
    url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
]
```

然后看看你的浏览器，如果你转到“/polls/34/”，Django 将会运行 **detail()** 方法并且展示你在 URL 里提供的问题 ID。再试试“/polls/34/results”和“/polls/34/vote/”——你将会看到暂时用于占位的结果和投票页。

 当某人请求你网站的某一页面时——比如说，“/polls/34/”，Django 将会载入 **mysite.urls** 模块，因为配置项 **ROOT_URLCONF** 说要载入它。然后 Django 寻找名为 **urlpatterns** 变量并且按序遍历正则表达式。我们使用的 **include()** 函数只是简单的引用其他的 URLconf 文件。请注意 **include()** 函数的正则表达式不包含 **$** 符号（匹配字符串结尾）但是结尾有斜线。当 Django 遇到一个 **include()**，它砍掉被正则表达式匹配上的部分，并把剩余的字符串发送个作为参数的 URLconf 做进一步处理。

我们设计 **include()** 函数是为了让 URLconf 也能轻松的「即插即用」。因为 polls 应用有他自己的 URLconf（**polls/url.py**)，他们能够被放在“/polls/”下、“/fun_polls/”下、“/comment/polls/”下，或者其他任何路径下，这个应用都能够正常工作。

下面的流程介绍了当用户访问“/polls/34/”时，系统里依次发生了什么：

- Django 找到匹配的正则表达式 '^polls/'
- 然后。Django 将会去除被匹配的部分（**polls/**）,然后发送剩下的文本——**“34/”**——给“polls.url”这个 URLconf 做进一步处理。然后找到匹配的正则表达式 **r'^(?P<question_id>[0-9]+)/$'**，随后用以下方式调用 **detail()** 函数：

```python
detail(request=<HttpRequest object>, question_id='34')
```

**question_id='34'** 这一部分是由 **(?P<question_id>[0-9+])** 产生的。使用括号来包围一部分模式，就可以“捕获”这部分所匹配到的文本，随后作为参数被传递给视图函数；**?P<question_id>** 用于定义匹配部分的名字；**[0-9]+** 是用于匹配一连串数字（也就是所有整数）的正则表达式。

因为 URL 模式本质上是正则表达式，所以不会有规定限制你如何使用它。对了，为每个 URL 加上不必要的东西，例如 **.html**，是没有必要的。不过如果你非要加的话，也是可以的：

```python
url(r'^polls/latest\.html$', views.index),
```

但是，别这样做，这太傻了。
