# 创建你的第一个 Django 项目， 第三部分

这一篇从 [第二部分（zh）](part2.md)结尾的地方继续。我们将继续编写投票应用，并且着力于如何创建公用接口 —— “视图”。

## 总概

Django 中的视图的概念是「一类具有相同功能和模板的网页的集合」。比如，在一个博客应用中，你可能会创建如下几个视图：

- 博客首页 —— 展示最近的一些记录。
- 内容详情页 —— 详细展示某项内容。
- 以年为单位的归档页 —— 展示选中的年份里各个月份创建的内容。
- 以月为单位的归档页 —— 展示选中的月份里各天创建的内容。
- 以天为单位的归档页 —— 展示选中天里创建的所有内容。
- 评论页 —— 用于响应为一项内容添加评论的操作。

而在投票应用中，我们需要下列几个视图：

- 问题索引页 —— 展示最近的几个投票问题。
- 问题详情页 —— 展示某个投票的问题和不带结果的选项列表。
- 问题结果页 —— 展示某个投票的结果。
- 投票页 —— 用于响应用户为某个问题的特定选项投票的操作。

在 Django 中，网页和其他内容都是从视图派生而来。每一个视图表现为一个简单的 Python 函数（或者说方法，如果是在基于类的视图里的话）。Django 将会根据用户请求的 URL 来选择使用哪个视图（更准确的说，是根据 URL 中域名之后的部分）。

在你上网的过程中，很可能看见过像这样美丽的 URL：“ME2/Sites/dirmod.asp?sid=&type=gen&mod=Core+Pages&gid=A6CD4967199A42D9B65B1B”。别担心，Django 里的 URL 要比这优雅的多！

一个 URL 模式定义了某种 URL 的基本格式 —— 举个例子：

**/newsarchive/&lt;year&gt;/&lt;month&gt;/**

为了将 URL 和视图关联起来，Django 使用了 “URLconfs” 来配置。URLconf 将 URL 模式（表现为一个正则表达式）映射到视图。

本教程只会介绍 URLconf 的基础内容，你可以查看 [**django.core.urlresolvers**][module-django.urls] 以获取更多内容。

## 编写更多的视图

现在让我们添加更多的视图进 **polls/view.py**，这些视图都有点不一样，因为它们都接受一个参数：

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

要把这些新视图添加进 **polls.urls** 模块里，只需添加几个 **url()** 函数调用：

```python
# polls/urls.py

from django.conf.urls import url

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

然后在你的浏览器里转到 “/polls/34/”，Django 将会运行 **detail()** 方法并展示你在 URL 里提供的问题 ID。再试试 “/polls/34/results” 和 “/polls/34/vote/” —— 你将会看到暂时用于占位的结果和投票页。

 当某人请求你网站的某一页面时——比如说，“/polls/34/”，Django 将会载入 **mysite.urls** 模块，因为配置项 [**ROOT_URLCONF**][ROOT_URLCONF] 说要载入它。然后 Django 寻找名为 **urlpatterns** 变量并且按序遍历正则表达式。Django 找到匹配的正则表达式 **'^polls/'** 
然后 Django 将会去除被匹配的部分（**polls/**）,然后发送剩下的文本 —— **“34/”** —— 给 “polls.urls” 这个 URLconf 做进一步处理。然后找到匹配的正则表达式 **r'^(?P<question_id>[0-9]+)/$'**，随后用以下方式调用 **detail()** 函数：

```python
detail(request=<HttpRequest object>, question_id='34')
```

**question_id='34'** 这一部分是由 **(?P<question_id>[0-9+])** 产生的。使用括号来包围一部分模式，就可以“捕获”这部分所匹配到的文本，随后作为参数被传递给视图函数；**?P<question_id>** 用于定义匹配部分的名字；**[0-9]+** 是用于匹配一连串数字（也就是所有整数）的正则表达式。

因为 URL 模式本质上是正则表达式，所以不会有规定限制你如何使用它们。还有，没必要为每个 URL 加上不必要的东西，例如 **.html**。不过如果你非要加的话，也是可以的：

```python
url(r'^polls/latest\.html$', views.index),
```

但是，别这样做，这太傻了。

## 写一个真正有用的视图

每个视图必须要做的只有两件事：返回一个包含被请求页面内容的 [**HttpResponse**][HttpResponse] 对象，或者抛出一个异常，比如 [**Http404**][Http404]。至于你还想干些什么，都随你。

你的视图可以从数据库里读取记录，可以使用一个模板引擎（比如 Django 自带的，或者其他第三方的），可以生成一个 PDF 文件，可以输出一个 XML，创建一个 ZIP 文件，你可以做任何你想做的事，使用任何你想用的 Python 库。

Django 只是需要一个 [**HttpResponse**][HttpResponse]，或者一个异常。

因为 Django 自带的数据库 API 很方便（我们在 [在第二部分（zh）](part2.md)里介绍过），所以我们试试在视图里使用它。我们在 **index()** 函数里插入了一些新内容，让它能展示数据库里按发布日期倒序的最近五个投票问题，以逗号分割：

```python
# polls/views.py

from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

# 暂时不管剩下的视图 (detail, results, vote)
```

这里有个问题：页面的设计是硬编码在视图函数的代码里的。如果你想改变页面的样子，你就要编辑 Python 代码。所以让我们使用 Django 的模板系统，只要创建一个视图，就可以将页面的设计从代码中分离出来。

首先，在你的 **polls** 目录里创建一个 **templates** 目录。Django 将会在这个目录里查找模板文件。

你项目的 [**TEMPLATES**][TEMPLATES] 配置项描述了 Django 如何加载和渲染模板。默认的设置文件设置了 **DjangoTemplates** 后端，并将 [**APP_DIRS**][APP_DIRS] 设置成了 **True**。这一选项将会让 **DjangoTemplates** 在每个 [**INSTALLED_APPS**][INSTALLED_APPS] 中激活的应用文件夹中寻找 “templates” 子目录。

**templates** 目录建好后，在里面再创建一个目录 **polls**，在这个 **polls** 的目录里再建立一个文件 **index.html**。换句话说，你的模版路径应该是 **polls/templates/polls/index.html**。由于 **app_directories** 模版加载器的工作原理如上所述，所以你使用简单的 **polls/index.html** 就能引用这模版了。

> 模板命名空间
>
> 虽然我们现在可以将模板文件直接放在 **polls/templates** 文件夹中（而不是再建立一个 **polls** 子文件夹），但这不是个好办法。Django 将会选择第一个匹配的模板文件，如果你有一个模板文件正好和另一个应用中的某个模板文件重名，Django 没有办法区分它们。我们需要 Django 选择正确的模板，最简单的方法就是把他们放入各自的命名空间中，也就是把这些模板放入一个和自身应用重名的子文件夹里。

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

为了使用这个模版，让我们更新一下在 **polls/views.py** 里的 **index** 视图：

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

上述代码的作用是，加载 **polls/index.html** 模板，并且向它传递一个上下文环境（context）。这个上下文是一个字典，它将模板内的变量映射为 Python 对象。

在你的浏览器访问 “/polls/”，你将会看见一个无序列表，列出了我们在 [教程二（zh）](part2.md)中添加的 “What's up” 投票问题，它链接到这个问题的详情页。

## 一个快捷函数：[render()][render]

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

注意，我们不再需要导入 **loader** 和 **HttpResponse**。不过如果你还有其他函数（比如说 **detail**，**results** 和 **vote**）需要用到它的话，就需要保持 HttpResponse 的导入。

[**render()**][render] 函数把请求(HttpRequest)对象作为第一个参数，加载的模版名字作为第二个参数，用于渲染模板的上下文字典作为可选的第三个参数。函数返回一个 [**HttpResponse**][HttpResponse] 对象，内容为指定模板用指定上下文渲染后的结果。

## 抛出 404 错误

现在，让我们来处理下投票详情视图 —— 它会显示指定投票的问题标题。下面是这个视图的代码：

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

这里有个新概念：如果指定问题 ID 所对应的问题不存在，这个视图就会抛出一个 [**Http404**][Http404] 异常。

我们稍后再讨论你需要在 **polls/details.html** 里输入什么，但是如果你想试试上面这段代码是否正常工作的话，你可以暂时把下面这段输进去：

```html
<!-- polls/templates/polls/details.html -->

{{ question }}
```

这样你就能测试了。

## 一个快捷函数：[get_object_of_404()][get_object_or_404]

 「用 [**get()**][get] 函数获取对象，抛出 [**Http404**][Http404]错误」也是一个常用流程。Django 也提供了一个快捷函数，下面是重写的 **detail()** 视图：

```python
# polls/views.py

from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

[**get_object_of_404()**][get_object_or_404] 函数的第一个参数是一个 Django 模型。在此之后可以有任意个的关键字参数，他们会被直接传递给模型的 [**get()**][get] 函数。如果对象并不存在，此快捷函数将会抛出一个 [**Http404**][Http404] 异常。

> **设计哲学**
>
> 为什么我们使用辅助函数 [**get_object_of_404()**][get_object_or_404] 而不是自己捕获 [**ObjectDoesNotExist**][ObjectDoesNotExist] 异常呢？或者，为什么模型 API 不直接抛出 [**Http404**][Http404] 而是抛出 [**ObjectDoesNotExist**][ObjectDoesNotExist] 呢？
>
> 因为这样做会增加模型层和视图层的耦合度。指导 Django 设计的最重要的思想之一就是要保证松散耦合。一些受控的耦合将会被包含在 [**django.shortcuts**][django.shortcuts] 模块中。

也有 [**get_list_of_404()**][get_object_or_404] 函数，工作原理和 [**get_object_of_404()**][get_object_or_404] 一样，除了 [**get()**][get] 函数被换成了 [**filter()**][filter] 函数。如果列表为空的话会抛出 [**Http404**][Http404] 异常。

## 使用模板系统

回过头去看看我们的 **detail()** 视图。它向模板传递了上下文变量 **question**。下面是 **polls/detail.html** 模板里的代码：

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

在 [**{% for %}**][templatetag-for] 循环中发生的函数调用：
**question.choice_set.all** 被解释为 Python 代码 **question.choice_set.all()**，将会返回一个可迭代的 **Choice** 对象，这一对象可以在[**{% for %}**][templatetag-for] 标签内部使用。

查看 [模板指南][templates] 可以了解关于模板的更多信息。

## 去除模板中的硬编码 URL

还记得吗，我们在 **polls/index.html** 里编写投票链接时，链接是硬编码的：

```html
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

问题在于，硬编码和强耦合的链接，对于一个包含很多应用的项目来说，修改起来是十分困难的。然而，因为你在 **polls.urls** 的 **urls()** 函数中通过 name 参数为 URL 定义了名字，你可以使用 **{% url %}** 标签代替它：

```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

这个标签的工作方式是在 **polls.urls** 模块的 URL 定义中寻具有指定名字的条目。你可以回忆一下，具有名字 “detail” 的 URL 是这样被定义的：

```python
...
# name 变量被 {% url %} 标签调用
url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
...
```

如果你想改变投票详情视图的 URL，比如想改成 **polls/specifics/12/**，你不用在模板里修改任何东西，只要在 **polls/urls.py** 里稍微修改一下就行：

```python
...
# 增加 specifics
url(r'^specifics/(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
...
```

## 为 URL 名称添加命名空间

本教程的项目只有一个应用，**polls**。在一个真实的 Django 项目中，可能会有五个，十个，二十个甚至更多应用。Django 如何分辨重名的 URL 呢？举个例子，**polls** 应用有 **detail** 视图，可能另一个博客应用也有同名的视图。Django 如何知道 **{% url %}** 标签到底对应哪一个应用的 URL 呢？

答案是：在 URLconf 中添加命名空间。在 **polls/urls.py** 文件中添加 **app_name** 变量作为应用的命名空间：

```python
# polls/urls.py

from django.conf.urls import url

from . import views

app_name = 'polls'
urlpatterns = [
    url(r'^$', views.index, name='index'),
    url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
    url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results'),
    url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
]
```

现在，修改 **polls/index.html** 模版文件，从：

```html
<!--- polls/templates/polls/index.html -->

<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

修改成：

```html
<!--- polls/templates/polls/index.html -->

<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```


当你弄懂如何编写视图之后，就可以去看教程的 [教程第四部分（zh）](part4.md)，来学习关于表单处理和视图类的相关内容。


[ROOT_URLCONF]: https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-ROOT_URLCONF
[TEMPLATES]: https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-TEMPLATES
[APP_DIRS]: https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-TEMPLATES-APP_DIRS
[INSTALLED_APPS]:https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-INSTALLED_APPS
[templates]: https://docs.djangoproject.com/en/1.11/topics/templates/
[module-django.urls]: https://docs.djangoproject.com/en/1.11/ref/urlresolvers/#module-django.urls
[filter]: https://docs.djangoproject.com/en/1.11/ref/models/querysets/#django.db.models.query.QuerySet.filter
[templatetag-for]: https://docs.djangoproject.com/en/1.11/ref/templates/builtins/#std:templatetag-for
[render]: https://docs.djangoproject.com/en/1.11/topics/http/shortcuts/#django.shortcuts.render
[HttpResponse]: https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpResponse
[HttpRequest]: https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpRequest
[Http404]: https://docs.djangoproject.com/en/1.11/topics/http/views/#django.http.Http404
[ObjectDoesNotExist]: https://docs.djangoproject.com/en/1.11/ref/exceptions/#django.core.exceptions.ObjectDoesNotExist
[get_object_or_404]: https://docs.djangoproject.com/en/1.11/topics/http/shortcuts/#django.shortcuts.get_object_or_404
[get_list_or_404]: https://docs.djangoproject.com/en/1.11/topics/http/shortcuts/#django.shortcuts.get_list_or_404
[django.shortcuts]: https://docs.djangoproject.com/en/1.11/topics/http/shortcuts/#module-django.shortcuts
[get]: https://docs.djangoproject.com/en/1.11/ref/models/querysets/#django.db.models.query.QuerySet.get