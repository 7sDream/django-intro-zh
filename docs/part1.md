# 创建你的第一个 Django 项目， 第一部分

来跟着实际项目学习 Django 吧。

在本教程中，我们将创建一个基础的投票网站。

它包含两个部分：

- 一个让公众查看投票内容并进行投票的公共站点。
- 一个能让你增加、修改和删除投票的管理界面。

我们假设你已经成功 [安装 Django（zh）](install.md)。如果你不清楚是否已经安装 Django 或不清楚安装的是哪个版本，请运行以下命令：

```bash
$ python -m django --version
```

如果已安装，你会看到安装的版本号；如果还没安装，你会看到错误提示：“No module named django”。

本教程的目标版本是 Django 1.11 和 Python 3.4 或更高版本。如果 Django 版本不匹配，你可以通过点击页面右下角的切换版本按钮来转到适合你版本的教程，或者你可以选择将 Django 升级到最新版本。如果你还在用 Python 2.7，你将需要对教程中的代码作一些微调，微调内容会被写在代码的注释里。

你可以查看文档 [快速安装指南（zh）](install.md) 来获得关于移除旧版本，安装新版本的建议。

> **哪里可以获得帮助：**
>
> 如果你在阅读或实践本教程中遇到困难，请发消息给 [django-users](https://docs.djangoproject.com/en/1.11/internals/mailing-lists/#django-users-mailing-list) 或加入IRC频道 [django on irc.freenode.net](irc://irc.freenode.net/django) 来与其他 Django 用户进行交流，他们也许能帮到你。

## 创建项目

如果这是你第一次使用 Django ，你还需要进行一些初始化设置。也就是说，你需要通过自动生成代码来建立一个 Django [项目](https://docs.djangoproject.com/en/1.11/glossary/#term-project)（一个 Django 项目实例需要的设置项集合，包括数据库配置， Django 选项和应用程序的具体设置）。

打开命令行，**cd** 切换到一个你想存放代码的目录，然后运行以下命令：

```bash
$ django-admin startproject mysite
```

这行代码将会在当前目录下创建一个 **mysite** 目录。如果命令不起作用，请看文档 [Problems running django-admin](https://docs.djangoproject.com/en/1.11/faq/troubleshooting/#troubleshooting-django-admin)。

> **注意**
>
> 你得避免使用 Python 或 Django 的内部保留字来命名你的项目。具体地说，你得避免使用像 **django**（会和 Django 自己冲突） 或 **test**（会和 Python 的内置模块冲突） 这样的名字。

> **我的代码该放在哪？**
>
> 如果你曾经是老式 PHP 程序员（没有使用过现代框架），你可能会习惯地把代码放在 Web 服务器的文档根目录（比如 **/var/www**）。但使用 Django 时你不用这样做。而且把所有 Python 代码放在 Web 服务器的根目录不是个好主意，因为这样会有风险。比如人们可能会在网站上看到你的代码。这不利于网站的安全。
>
> 你可以把代码放在文档根目录 **以外** 的地方，比如 **/home/mycode**。

让我们看看 [startproject](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-startproject) 这命令创建了什么：

```text
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```

这些目录和文件的用处是：

- 最外层的 **mysite/** 根目录只是你项目的容器， Django 不关心它的名字，你可以将它重命名为任何你喜欢的名字。
- **manage.py**：一个让你可以用各种方式管理该 Django 项目的命令行工具。你可以阅读 [django-admin and manage.py](https://docs.djangoproject.com/en/1.11/ref/django-admin/) 来获取关于 **manage.py** 的更多细节。
- 里面一层的 **mysite/** 目录就是你项目的实际 Python 包。它的名字就是当你引用它内部任何东西时需要用到的 Python 包名（比如：**mysite.urls**）。
- **mysite/\_\_init\_\_.py**：一个用于指明此目录是 Python 包的空白文件。（如果你刚开始学习 Python，请阅读 Python 官方文档中的 [more about packages](https://docs.python.org/3/tutorial/modules.html#tut-packages) 。）
- **mysite/settings.py**：该 Django 项目的配置文件。如果你想知道这个文件是如何工作的，请看文档 [Django settings][settings]。
- **mysite/urls.py**：该 Django 项目的 URL 声明，就像是你网站的“目录”。阅读 [URL dispatcher](https://docs.djangoproject.com/en/1.11/topics/http/urls/) 文档来获取更多关于 URL 的内容。
- **mysite/wsgi.py**：当你部署项目到一个兼容 WSGI 的服务器上时所需要的入口点。[How to deploy with WSGI](https://docs.djangoproject.com/en/1.11/howto/deployment/wsgi/) 文档内有更多关于这个文件的细节。

## 用于开发的服务器

让我们验证下项目是否可用。
切换到最外层的 **mysite** 目录下，如果你已经在这个目录下了，那就运行下面的命令：

```bash
$ python manage.py runserver
```

你会看到命令行输出：

```bash
Performing system checks...

System check identified no issues (0 silenced).

You have unapplied migrations; your app may not work properly until they are applied.
Run 'python manage.py migrate' to apply them.

August 02, 2017 - 15:50:53
Django version 1.11, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

> **注意**
> 现在请先忽略关于没有应用数据库迁移的警告，我们将在下一章解决它。

现在你已经开启了 Django 开发服务器 —— 一个纯 Python 编写的轻量级 Web 服务器。我们已经在 Django 里包含了这项功能，所以你可以快速的开发网站，而不用去配置生产环境的服务器（比如 Apache），直到你做好了网站并准备投入生产环境。

注意：**不要** 在任何与生产环境相关的地方使用这个开发服务器，因为这只是为了开发所需。
（我们的业务只是开发 Web 框架，而不是 Web 服务器）

现在服务器已经在运行了，在浏览器里访问 [http://127.0.0.1:8000/](http://127.0.0.1:8000/)。如果你看到一个写着 “Welcome to Django” 的令人赏心悦目的浅蓝色粉彩页面，那就是可以了！

> **更换端口**
>
> 默认情况下，[**runserver**][runserver] 命令会将服务器设置为监听本机内部 IP 的 8000 端口。
>
> 如果你想更换服务器监听的端口，请使用命令行参数。比如，让服务器监听 8080 端口：
>
```bash
$ python manage.py runserver 8080
```
>
> 如果你想更换服务器监听的 IP ，可以将它和端口号写在一起作为参数。比如，监听所有公网 IP（这尤其有用，当你想通过网络展示给其他人时），像这样：
>
```bash
$ python manage.py runserver 0:8000
```
>
> 0 是 0.0.0.0 的快捷方式。想了解更多关于这个用于开发的服务器的内容可以参考 [**runserver**][runserver]。

> **会自动重载的服务器**
>
> 用于开发的服务器在需要的情况下会对每一次的访问请求重新载入一遍 Python 代码。所以你不需要为了让修改的代码生效而频繁地重启服务器。但是像添加新文件这样的动作，不会触发自动重载，这时你就需要手动重启服务器了。

## 创建 Polls 投票应用

现在你的开发环境 —— 一个“项目” —— 已经设置好了，你可以开始工作了。

在 Django 中，每一个应用都是一个 Python 包，并且遵循着一定的约定。Django 自带的工具，可以帮你自动生成应用的基础目录结构，这样你就能专注于编写代码，而不是创建目录了。

> **项目 VS 应用**
>
> 项目和应用有什么区别？应用是一个专门做某件事的网络应用程序，比如博客系统、公共记录的数据库、或者简单的投票程序。项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用；而应用可以被很多个项目使用。

你的应用可以放在 [Python path](https://docs.python.org/3/tutorial/modules.html#tut-searchpath) 中的任何目录里。在本教程中，我们将直接在 **manage.py** 所在的目录里创建投票应用，这样它就能作为顶级模块被引入，而不是作为 **mysite** 的子模块。

请确定你现在处于 **manage.py** 所在的目录下，然后运行这行命令来创建一个应用：

```bash
$ python manage.py startapp polls
```

这将会创建一个 **polls** 目录，它的目录结构大致如下：

```text
polls/
    __init__.py
    admin.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

这个目录结构包括了投票应用的全部内容。

## 编写你的第一个视图

创建并打开 **polls/views.py**，然后写进 Python 代码： 

```python3
# polls/views.py
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

这也许是 Django 中最简单的视图了。为了调用这个视图，我们需要在它和一个 URL 之间做映射，这就需要 URLconf。

为了在 polls 目录下创建一个 URLconf，需要先创建 **urls.py** 文件，你的应用目录现在应该是这样的： 

```text
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    urls.py
    views.py
```

**polls/urls.py** 文件里包含以下代码：

```python3
# polls/urls.py
from django.conf.urls import url

from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```

下一步就是将根目录下的 URLconf 指向 **polls.urls** 模块。在 **mysite/urls.py** 中导入 **django.conf.urls.include**，并且在 **urlpatterns** 列表中插入 [**include()**][include]，像下面这样：

```python3
# mysite/urls.py
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', admin.site.urls),
]
```

[**include()**][include] 函数允许引用其他的 URLconf。要注意到 [**include()**][include] 函数并没有 **$**(字符串匹配结束)，取而代之的是尾部的一个斜杠。每当 Django 遇到 [**include()**][include]，它就会排除正则匹配的部分，并将剩下的字符串发送到引用的 URLconf 中做进一步的处理。

[**include()**][include] 背后的想法是想使得 URL 的即插即用变得简单。polls 是在它们自己的 URLconf 中（ **polls/urls.py** ），它们是可以被放在“/polls/”、 “/fun_polls/”、 “/content/polls/”、 或者其他任何路径，而应用仍然是可以运行的。

> **什么时候使用 [include()][include]**
>
> 当你要包含其他 URL 匹配模式时，你应该一直使用 **include()**。 **admin.site.urls** 在这里是一个例外。

> **不符合你所想看到的？**
>
> 如果你看到的是 **include(admin.iste.urls)** 而不是 **admin.site.urls**，可能是你正在使用的 Django 版本和本教程的目标版本（1.11）不一致。那你就需要切换到旧版本的教程或者是安装较新的 Django。

现在你已经将 **index** 视图和 URLconf 连接在一起了。让我们验证下是不是生效了，运行以下命令：

```bash
$ python manage.py runserver
```

在你的浏览器中打开 [http://localhost:8000/polls/](http://localhost:8000/polls/)。你应该能看到文字 “Hello, world. You’re at the polls index.” —— 这是你在 **index** 视图中定义的。

[**url**][url] 函数有四个参数，两个必需参数：**regex** 正则和 **view** 视图；两个选项参数：**kwargs**字典和 **name** 名字。在这点上，值得再看下这些参数到底是干什么的。

### [url][url]参数：regex

术语 “regex” 是正则表达式 “regular expression” 的缩写，是匹配字符串的一段语法，像这里例子的是 url 匹配模式。Django 从列表的第一个正则表达式开始，按顺序匹配请求的 URL，直到找到与之匹配的。

注意，这些正则表达式不会去匹配 GET 和 POST 请求的参数值，或者域名。比如 **https://www.example.com/myapp/** 这个请求，URLconf 会找 **myapp/**；**https://www.example.com/myapp/?page=3** 这个请求，URLconf 同样只会找 **mysqpp/**。

如果你需要正则表达式的帮助，可以看 [**Wikipedia’s entry**](https://en.wikipedia.org/wiki/Regular_expression) 和 关于 [**re**](https://docs.python.org/3/library/re.html#module-re) 模块的文档。还有，由 Jeffrey Friedl 写的书 《掌握正则表达式》 也是很棒的。实际上，你并不需要成为正则表达式方面的专家，你真正要会的是如何使用简单捕获模式。因为复杂的正则可能会有不尽人意的查找性能，所以你不太应该全依赖于正则匹配。

最后，一个性能注意点：这些正则表达式在 URLconf 模块加载后的第一时间就被编译了。它们都是非常快的（只要查找的不是特别复杂就像上面举例的）。

### [url][url] 参数：view

当 Django 发现一个正则表达式匹配时，Django 就会调用指定的视图函数，[**HttpRequest**][HttpRequest] 对象作为第一个参数，正则表达式捕获的值作为其他参数。如果正则使用简单捕获，值会作为位置参数传递；如果使用命名捕获，值会作为关键字传递。我们稍后会给出一个例子。

### [url][url] 参数：kwargs

任意的关键字参数都可以作为字典传递到目标视图。但我们不准备在本教程里使用 Django 的这个特性。

### [url][url] 参数：name

命名你的 URL 可以让你在 Django 的别处明白引用的是什么，特别是在模版里。这个强大的特性允许你在项目里对一个文件操作就能对 URL 模式做全局改变。

当你对基本的请求和响应流都明白时，你就可以阅读 [教程第二部分（zh）](part2.md)开始使用数据库了。


[settings]: https://docs.djangoproject.com/en/1.11/topics/settings/
[runserver]: https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-runserver
[url]: https://docs.djangoproject.com/en/1.11/ref/urls/#django.conf.urls.url
[include]: https://docs.djangoproject.com/en/1.11/ref/urls/#django.conf.urls.include
[HttpRequest]: https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpRequest