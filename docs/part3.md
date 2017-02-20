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

在你上网的过程中，很可能看见过像这样美丽的 URL：“ME2/Sites/dirmod.asp?sid=&type=gen&mod=Core+Pages&gid=A6CD4967199A42D9B65B1B”。别担心，Django 里的 URL 要比这优雅的多！

一个 URL 模式定义了某种 URL 的基本格式——举个例子：

**/newsarchive/&lt;year&gt;/&lt;month&gt;/**

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

下一步是要在根 URLconf 文件中指定我们创建的 **polls.urls** 模块。在 **mysite/urls.py** 文件里插入一个 **[include()](include)**， 如下：

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

当 Django 找到匹配的正则表达式时，就会调用这个视图函数。调用时传递的第一个参数是一个 **[HttpRequest][HttpRequest]** 对象，后续的参数是所有被正则表达数捕捉的部分。如果正则式使用简单捕获，捕获结果会作为位置参数传递；如果使用名字捕获，捕获结果会作为关键字参数传递。我们稍后会给出例子。

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

 当某人请求你网站的某一页面时——比如说，“/polls/34/”，Django 将会载入 **mysite.urls** 模块，因为配置项 **[ROOT_URLCONF](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-ROOT_URLCONF)** 说要载入它。然后 Django 寻找名为 **urlpatterns** 变量并且按序遍历正则表达式。我们使用的 **[include()][include]** 函数只是简单的引用其他的 URLconf 文件。请注意 **[include()][include]** 函数的正则表达式不包含 **$** 符号（匹配字符串结尾）但是结尾有斜线。当 Django 遇到一个 **[include()][include]**，它砍掉被正则表达式匹配上的部分，并把剩余的字符串发送个作为参数的 URLconf 做进一步处理。

我们设计 **[include()][include]** 函数是为了让 URLconf 也能轻松的「即插即用」。因为 polls 应用有他自己的 URLconf（**polls/url.py**)，他们能够被放在“/polls/”下、“/fun_polls/”下、“/comment/polls/”下，或者其他任何路径下，这个应用都能够正常工作。

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

## 写一个真正有用的视图

每个视图必须要做的只有两件事：返回一个包含被请求页面内容的 **[HttpResponse][HttpResponse]** 对象，或者抛出一个异常，比如 **[Http404][Http404]**。至于你还想干些什么，都随你。

你的视图可以从数据库里读取记录，可以使用一个模板引擎（比如 Django 自带的，或者其他第三方的），可以生成一个 PDF 文件，可以输出一个 XML，创建一个 ZIP 文件，你可以做任何你想做的事，使用任何你想用的 Python 库。

Django 只是需要一个 **[HttpResponse][HttpResponse]**，或者一个异常。

因为 Django 自带的数据库 API 很方便（我们在[在第一部分(zh)](part1.md)里介绍过），所以我们试试在视图里使用它。我们在 **index()** 函数里插入了一些新内容，让它能展示数据库里以发布日期排序的最近五个投票问题，以空格分割：

```python
# polls/views.py

from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([p.question_text for p in latest_question_list])
    return HttpResponse(output)

# Leave the rest of the views (detail, results, vote) unchanged
```

这里有个问题：页面的设计是硬编码在视图函数的代码里的。如果你想改变页面的样子，你需要编辑 Python 代码。所以让我们使用 Django 的模板系统，只要创建一个视图，就可以将页面的设计从代码中分离出来。

首先，在你的 **polls** 目录里创建一个 **templates** 目录。Django 将会在这个目录里查找模板文件。

你项目的 **[TEMPLATES](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-TEMPLATES)** 配置项描述了 Django 如何载入和渲染模板。默认的设置文件设置了 **DjangoTemplates** 后端，并将 **[APP_DIRS](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-TEMPLATES-APP_DIRS)** 设置成了 True。这一选项将会让 **DjangoTemplates** 在每个 **[INSTALLED_APPS](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-INSTALLED_APPS)** 文件夹中寻找“templates”子目录。这就是为什么尽管我们没有像在[第二部分(zh)](part2.md)中那样修改 **[DIRS](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-TEMPLATES-DIRS)** 设置，Django 也能正确找到 polls 的模板位置的原因。

> 组织模板
>
> 我们*可以*把所有的模板都放在一个大的模板目录里，并且它也将完美运行。然而，有些模板是专属于 polls 应用的，所以，不像我们在之前的教程中创建的管理界面模板。我们将这些模板放在应用的模板文件夹（**polls/templates**）中，而不放在项目的模板文件夹（**templates**)中。我们将在教程的[可重用的应用(zh)](reusable_app.md)这一节中详细讨论为什么要这样做。

在你刚刚创建的 **templates** 目录里，再创建一个目录 **polls**，然后在其中新建一个文件 **index.html**。换句话说，你的模板文件的路径应该是 **polls/templates/polls/index.html**。因为 Django 会寻找到对应的 **app_directiories**，所以你只需要使用 **polls/index.html** 就可以引用到这一模板了。

> 模板命名空间
>
> 虽然我们现在可以将模板文件直接放在 **polls/templates** 文件夹中（而不是再建立一个 **polls** 子文件夹），但是这样做不太好。Django 将会选择第一个匹配的模板文件，如果你有一个模板文件正好和另一个应用中的某个模板文件重名，Django 没有办法区分它们。我们需要帮助 Django 选择正确的模板，最简单的方法就是把他们放入各自的命名空间中，也就是把这些模板放入一个和自身应用重名的子文件夹里。

将下面的代码输入到刚刚创建的模板文件中：

```html
<!-- polls/templates/polls/index.html -->

{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

然后，让我们更新一下 **polls/views.py** 里的 **index** 视图：

```python
# polls/views.py

from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

上述代码的作用是，载入 **polls/index.html** 模板文件，并且向它传递一个上下文环境（context）。这个上下文是一个字典，它将模板内的变量映射为 Python 对象。

用你的浏览器访问“/polls/”，你将会看见一个无序列表，列出了我们在教程以中添加的 “What's up” 投票问题，它链接到这个投票的详情页。

## 一个快捷函数：render()

「载入模板，填充上下文，再返回由它生成的 HttpResponse 对象」是一个如此常用的操作流程。于是 Django 提供了一个快捷函数，我们用它来重写 **index()** 视图：

```python
# polls/views.py

from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

注意到，我们不再需要导入 **loader** 和 **HttpResponse**。不过如果你还有其他函数（比如说 **detail**，**results** 和 **vote**）需要用到它的话，就需要保持 HttpResponse 的导入。

**[render()](https://docs.djangoproject.com/en/1.8/topics/http/shortcuts/#django.shortcuts.render)** 函数的第一个参数是一个请求(HttpRequest)对象，第二个参数是需要载入的模板的名字。第三个参数是需要用于渲染模板的上下文字典，这个参数是可选的。函数返回一个 **[HttpResponse][HttpResponse]** 对象，内容为指定模板用指定上下文渲染后的结果。

## 抛出 404 错误

现在，我们来处理投票详情视图——它会显示指定投票的问题标题。下面是这个视图的代码：

```python
# polls/views.py

from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

这里有个新东西。如果指定问题 ID 所对应的问题不存在，这个视图就会抛出一个 **[Http404][Http404]** 异常。

我们稍后再讨论你需要在 **polls/details.html** 里输入什么，但是如果你想试试上面这段代码是否正常工作的话，你可以暂时把下面这段输进去：

```html
{{ question }}
```

这样你就能测试了。

## 一个快捷函数：get_object_of_404()

尝试用 **get()** 函数获取一个对象，如果不存在就抛出 **[Http404][Http404]** 错误也是一个普遍的流程。Django 也提供了一个快捷函数，下面是修改后的详情视图代码：

```python
# polls/views.py

from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

**[get_object_or_404()][get_object_or_404]** 函数的第一个参数是一个 Django 模型。在此之后可以有任意数量的关键字参数，他们会被直接传递给模型的 **get()** 函数。如果符合要求的对象不存在，此快捷函数将会抛出一个 **[Http404][Http404]** 异常。

> **设计哲学**
>
> 为什么我们使用辅助函数 **[get_object_or_404()][get_object_or_404]** 而不是自己捕获 **[ObjectDoesNotExist][ObjectDoesNotExist]** 异常呢？还有，为什么模型 API 不直接抛出 **[Http404][Http404]** 而是抛出 **[ObjectDoesNotExist][ObjectDoesNotExist]** 呢？
>
> 因为这样做会增加模型层和视图层的耦合性。指导 Django 设计的最重要的思想之一就是要保证松散耦合。一些受控的耦合将会被包含在 **[ django.shortcuts][django.shortcuts]** 模块中。

也有 **[get_list_or_404()][get_list_or_404]** 函数，工作原理和 **[get_object_or_404()][get_object_or_404]** 一样，除了 **get()** 函数被换成了 **filter()** 函数。如果列表为空的话会抛出 **[Http404][Http404]** 异常。

## 使用模板系统

回过头去看看我们的 **detail()** 视图。它向模板传递了上下文变量 **question**。下面是 **polls/detail.html** 模板里正式的代码：

```html
<!--- polls/templates/polls/detail.html -->

<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```

模板系统统一使用「点」符号来访问变量的属性。在示例 **{{ question.question_text }}** 中，首先 Django 尝试对 **Question** 对象使用字典查找（也就是使用obj.get(str)操作），如果失败了就尝试属性查找（也就是obj.str操作），结果是成功了。如果这一操作也失败的话，将会尝试列表查找（也就是obj[int]操作）。

在 **{% for %}** 循环中发生的函数调用：
**question.choice_set.all** 被解释为 Python 代码 **question.choice_set.all()**，将会返回一个可迭代的 **Choice** 对象，这一对象可以在 **{% for %}** 标签内部使用。

查看 **[模板指南](https://docs.djangoproject.com/en/1.8/topics/templates/)** 可以了解关于模板的更多信息。

## 去除模板中的硬编码 URL

还记得吗，我们在 **polls/index.html** 里编写投票链接时，链接是硬编码的：

```html
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

问题在于，硬编码和强耦合的链接，对于一个包含很多应用的项目来说，修改起来是十分困难的。然而，因为你在 **polls.urls** 的 **urls()** 函数中通过 name 参数为 URL 定义了名字，你可以使用 **{% url %}** 标签代替它：

```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

这个标签的工作方式是在 **polls.urls** 模块的 URL 定义中寻具有指定名字的条目。你可以回忆一下，具有名字“detail”的 URL 是在如下语句中定义的：

```python
...
# the 'name' value as called by the {% url %} template tag
url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
...
```

如果你想改变投票详情视图的 URL，比如想改成 **polls/specifics/12**，你不用在模板里修改任何东西，只要在 **polls/url.py** 里稍微修改一下就行：

```python
...
# added the word 'specifics'
url(r'^specifics/(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
...
```

## 为 URL 名称添加命名空间

教程项目只有一个应用，**polls**。在一个真实的 Django 项目中，可能会有五个，十个，二十个甚至更多应用。Django 如何分辨重名的 URL 呢？举个例子，**polls** 应用有 **detail** 视图，可能另一个博客应用也有同名的视图。Django 如何知道 **{% url %}** 标签到底对应哪一个应用的 URL 呢？

答案是：在根 URLconf 中添加命名空间。在 **mysite/urls.py** 文件中稍作修改，加上命名空间：

```python
# mysite/urls.py

from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls', namespace="polls")),
    url(r'^admin/', include(admin.site.urls)),
]
```

现在，编辑 **polls/index.html** 文件，从：

```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

修改成：

```html
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```


当你弄懂如何编写视图之后，就可以去看教程的[第四部分(en)](part4.md)，来学习关于表单处理和视图类的相关内容。


[HttpResponse]: https://docs.djangoproject.com/en/1.8/ref/request-response/#django.http.HttpResponse
[HttpRequest]: https://docs.djangoproject.com/en/1.8/ref/request-response/#django.http.HttpRequest
[include]: https://docs.djangoproject.com/en/1.8/ref/urls/#django.conf.urls.include
[Http404]: https://docs.djangoproject.com/en/1.8/topics/http/views/#django.http.Http404
[ObjectDoesNotExist]: https://docs.djangoproject.com/en/1.8/ref/exceptions/#django.core.exceptions.ObjectDoesNotExist
[get_object_or_404]: https://docs.djangoproject.com/en/1.8/topics/http/shortcuts/#django.shortcuts.get_object_or_404
[get_list_or_404]: https://docs.djangoproject.com/en/1.8/topics/http/shortcuts/#django.shortcuts.get_list_or_404
[django.shortcuts]: https://docs.djangoproject.com/en/1.8/topics/http/shortcuts/#module-django.shortcuts
