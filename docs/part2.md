# 创建你的第一个 Django 项目， 第二部分

这一篇从[第一部分（zh）](part1.md)结尾的地方继续讲起。本节我们将继续写 Web 投票应用，并主要关注 Django 提供的自动生成的管理页面（admin site）。

> **设计哲学**
>
> 为你的员工和客户创建一个用于添加、修改和删除网站内容的管理页面是一项乏味的工作，而且不需要太多的创造力。因为这些原因，Django 提供完全自动地为模型创建管理接口的功能。
>
> Django 产生于一个公众页面和内容发布者页面完全分离的新闻类站点的开发过程中。站点管理人员使用管理系统来添加新闻、事件和体育时讯等，这些添加的内容被显示在公众页面上。Django 通过为站点管理人员创建统一的内容编辑界面解决了这个问题。
>
> 管理界面不是为了网站的访问者，而是为管理者准备的。

## 创建管理员账户

首先，我们得创建一个能登录管理页面的用户。请运行下面的命令：

```bash
$ python manage.py createsuperuser
```

输入你想使用的用户名，然后回车。

```
Username: admin
```

接着，你会被提示要求输入邮箱：

```
Email address: admin@example.com
```

最后一步是输入密码。你会被要求输入两次密码，第二次的目的是为了确认第一次输入的确实是你想要的密码。

```
Password: ********
Password(again): ********
Superuser created successfully.
```

## 启动用于开发的服务器

Django 的管理界面默认就是启用的。让我们启动开发服务器，看看它到底是什么样的。

再次调用在[教程第一部分（zh）](part1.md)中使用的运行开发服务器的命令：

```bash
$ python manage.py runserver
```

现在，打开浏览器，转到你本地域名的“/admin/”目录，比如“http://127.0.0.1:8000/admin”。你应该会看见管理员登录界面：

![Django admin login screen](img/admin01.png)

因为翻译功能默认是开着的，所以登录界面可能会使用你的语言，取决于你的浏览器设置和是否 Django 已被翻译成你的语言。

> 和你看到的不一样？
>
> 如果在这一步，你看到的不是上图所示的界面，而是一个错误页面，就像下面这样：
>
```text
ImportError at /admin/
cannot import name patterns
...
```
>
> 那么你可能使用了和教程不匹配的 Django 版本。你可以切换到旧版本的教程，或者选择把 Django 升级到较新版本。

## 进入管理页面

现在，试着使用你在上一步中创建的超级用户来登录。然后你将会看到 Django 管理页面的索引页：

![Django admin index page](img/admin02.png)

你将会看到几种可编辑的内容：组和用户。它们是 **django.contrib.auth** 提供的，这是 Django 开发的验证框架。

## 向管理页面中加入投票应用

但是我们的投票应用在哪呢？它没在索引页面里显示。

只需要做一件事：我们得告诉管理页面，问题（Question）对象需要被管理。打开 **polls/admin.py** 文件，把它编辑成下面这样：

```python3
# polls/admin.py

from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

## 体验便捷的管理功能

现在我们向管理页面注册了问题（Question）类。Django 知道它应该被显示在索引页里：

![Django admin index page, now with polls displayed](img/admin03t.png)

点击“Question”。现在看到是问题（Question）对象的列表。这个界面会显示所有数据库里的问题（Question）对象，你可以选择一个来修改。这里现在有我们在上一部分中创建的“What's up?”问题。

![Polls change list page](img/admin04t.png)

点击“What's up?”来编辑这个问题（Question）对象：

![Editing form for question object](img/admin05t.png)

有些事情需要注意：

- 这个表单是从问题（Question）模型中自动生成的。
- 不同的字段类型（**日期时间字段（DateTimeField）**、**字符字段（CharField）**）会生成对应的 HTML 输入控件。每个类型的字段都知道它们该如何在管理页面里显示自己。
- 每个 **日期时间字段（DateTimeField）** 都有 JavaScript 写的快捷按钮。日期有转到今天（Today）的快捷按钮和一个弹出式日历界面。时间有设为现在（Now）的快捷按钮和一个列出常用时间的方便的弹出式列表。

页面的底部提供了几个选项：

- 保存（Save） - 保存改变，然后返回对象列表。
- 保存并继续编辑（Save and continue editing） - 保存改变，然后重新载入当前对象的修改界面。
- 保存并新增（Save and add another） - 保存改变，然后添加一个新的空对象并载入修改界面。
- 删除（Delete） - 显示一个确认删除页面。

如果显示的“发布日期（Date Published）”和你在教程第一部分里创建它们的时间不一致，这意味着你可能没有正确的设置 **[TIME_ZONE](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-TIME_ZONE)**。改变设置，然后重新载入页面看看是否显示了正确的值。

通过点击“今天”（Today）和“现在（Now）”按钮改变“发布日期（Date Published）”。然后点击“保存并继续编辑（Save and add another）”按钮。然后点击右上角的“历史（History）”按钮。你会看到一个列出了所有通过 Django 管理页面对当前对象进行的改变的页面，其中列出了时间戳和进行修改操作的用户名：

![History page for question object](img/admin06t.png)

## 自定义管理表单

先花几分钟惊叹一下你不用写任何代码就拥有了管理界面这个奇迹吧。通过使用 **admin.site.register(Question)** 注册问题（Question）类，Django 能构造一个出一个默认的表单样式。通常，你会想自定义表单的样式和作用。你能通过在注册对象时增加一些选项来达到这一目的。

来看看如何改变表单中字段的顺序。把 **admin.site.register(Question)** 替换成下面的代码：

```python3
# polls/admin.py

from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'question_text']

admin.site.register(Question, QuestionAdmin)
```

当你想改变管理页面的某些选项时，步骤一般是这样的：创建模型管理对象（Model admin object），然后把它当作第二个参数传递给 **admin.site.register()** 函数。

上面的代码会使“发布日期（Publication date）”出现在“问题说明（Question text）”字段之前。

![Fields have been reordered](img/admin07.png)

如果只有两个字段的话感觉不出什么，但是当表单有十几个字段时，选择一个直观的顺序是个可以提升用户体验细节。

如果真的有十几个字段的话，你可能想将它们分组，也即分为多个字段集（fieldsets）：

```python3
# polls/admin.py

from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]

admin.site.register(Question, QuestionAdmin)
```

fieldsets 列表里的元组的第一个元素是分组名。下面是表单现在的样子：

![Form has fieldsets now](img/admin08t.png)

你能自由的为每个分组分配 HTML Class。Django 提供了 **折叠（collapse）** 类，属于这个类的分组将会初始化为折叠形式。如果你有一个很长的表单，而且有些字段并不常用的话，这个功能将会很有用。

```python3
# polls/admin.py

from django.contrib import admin

from .models import Question


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]

admin.site.register(Question, QuestionAdmin)
```

![Fieldset is initially collapsed](img/admin09.png)

## 添加关联对象

现在我们已经可以管理问题（Question）了。但是每个问题（Question）都有多个选项（Choices），而且管理页面里并没有 Choices。

不过马上就有了。

有两个方法来解决这个问题。第一种是像注册问题（Question）一样去注册选项（Choices）。这很简单：

```python3
# polls/admin.py

from django.contrib import admin

from .models import Choice, Question
# ...
admin.site.register(Choice)
```

现在，Django 的管理页面里就有“选项（Choices）”了。“添加选项（Add choices）”的表单看起来就像这样：

![Choice admin page](img/admin10.png)

在这个表单中，“问题（Question）”字段表现为一个包含所有数据库里储存着的问题（question）的选择框。Django 知道 **外键** 应该被表示为选择框。现在，列表里只有一个问题（question）。

“问题（Question）”旁边的“添加（Add Another）”按钮也值得注意一下。每一个 **外键** 字段都会有这个按钮。当你点击它时，会弹出一个带有“添加问题（Add question）”表单的窗口。如果使用它添加了问题（Question）并点击了“保存（Save）”，Django 会将问题（Question）对象储存在数据库中然后动态的将其加入到之前的“添加选项”表单中的问题（Question）选择框中。

但是，说实话，这样添加选项（Choices）到数据库中是非常低效的。如果当你添加一个问题（Question）时能够同时为它加上几个选项的话那就再好不过了。来看看怎么实现：

删掉 **选项（Choices）** 模型的 **register()** 语句，然后编辑 **问题（Question）** 的注册代码：

```python3
# polls/admin.py

from django.contrib import admin

from .models import Choice, Question


class ChoiceInline(admin.StackedInline):
    model = Choice
    extra = 3


class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]

admin.site.register(Question, QuestionAdmin)
```

增加的代码将会告诉 Django：“**Choice（选项）** 对象将会在 **问题（Question）** 的管理界面里被编辑。默认显示3个选项字段以供编辑。”

重新打开“添加问题（Add question）”页面：

![Add question page now has choices on it](img/admin11t.png)

现在有3个可以添加相关选项（choices）的单元——“3”是由代码里的 extra 所规定的——并且当你每次进入一个已经创建好的对象的修改界面时，总会多出另外三个单元让你可以添加选项。

在三个添加选项单元的最下方有一个“添加一个选项（Choices）”按钮。但你点击它时，上方会增加一个添加选项的单元。如果你想删掉添加的单元，可以点击单元右上角的X。请注意：你无法移除初始的那三个单元。下面这张图片显示新增了一个单元的效果：

![Additional slot added dynamically](img/admin15t.png)

还有个小问题。显示所有的相关的选项（Choices）单元实在是太占屏幕空间了。由于这个原因，Django 提供了表格式的视图：你只需要改一下 **ChoiceInline** 的定义就行了：

通过使用 **TabularInline** （而不是 **StackedInline**），相关的对象将会用更紧凑的，表格式的格式显示：

![Add question page now has more compact choices](img/admin12t.png)

多出来的“删除？（Delete?）”列可以用来删除通过“添加选项（Add Another Choices）”按钮添加的数据，尽管它们已经被储存了。

## 自定义对象列表

现在 Django 的对象修改页面已经很棒了，现在我们来调整一下对象列表页 - 也就是显示所有问题（Question）对象的页面。

目前它是这样的：

![Polls change list page](img/admin04t.png)

默认情况下，Django 使用 **str()** 方法来显示对象。但有时如果我们显示一些其他的字段会很有用。为此，我们可以使用 **list_display** 选项，它是由需要被显示的字段名组成的元组，这些字段将会作为额外的列显示在列表中。

```python3
# polls/admin.py

class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date')
```

为了方便检索，我们把在教程第一部分中自定义的 **was_published_recently** 方法也加入进来：

```python3
# polls/admin.py

class QuestionAdmin(admin.ModelAdmin):
    # ...
    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

现在，问题（Question）的对象列表如下图：

![Polls change list page, updated](img/admin13t.png)

通过点击表头，你可以让列表按所选的属性进行排序 - 除了 **was_published_recently** 函数，因为 Django 不支持通过随便定义的方法的输出结果进行排序。**was_published_recently** 方法所在列的列名默认使用了方法名（下划线被替换为空格），显示的值是对应方法返回值的字符串形式。

你能够通过给方法增加属性来扩展功能，看下面的示例：

```python3
# polls/models.py

class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
    was_published_recently.admin_order_field = 'pub_date'
    was_published_recently.boolean = True
    was_published_recently.short_description = 'Published recently?'
```

想了解更多的方法选项，请看文档：**[list_display](https://docs.djangoproject.com/en/1.8/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display)**。

再次编辑 **polls/admin.py** 文件。我们要为问题（Question）的对象列表增加一个新功能：通过 **list_filter** 过滤对象。向 **QuestionAdmin** 中添加以下内容：

```python3
list_filter = ['pub_date']
```

上面代码的作用是为对象列表添加了一个“快速过滤”侧边栏，可以通过 **pub_date** 字段来挑选问题（Question）。

过滤的类型取决于你要通过哪种字段进行过滤。因为 **pub_date** 是个 **日期时间字段（DateTimeField）**，所以 Django 会自动提供合适的过滤方法：“任何时候（Any date）”、“今天（Today）”、“过去7天（Past 7 days）”、“本月（This month）”、“今年（This year）”。

这个功能搞定了，让我们加一些搜索功能：

```python3
search_fields = ['question_text']
```

这将会在对象列表的顶部加一个搜索框。当有人键入内容时，Django 会搜索 **question_text** 字段。你想搜索多少字段都行 - 尽管搜索过程使用的是数据库查询语句里的 **LIKE** 关键字，但限制一下搜索结果数量将会让数据库搜索更加轻松。

默认情况下，Django 每100个对象为一页。

对象列表分页、搜索框、过滤器、根据时间分组和根据表头排序可以完美结合，一起工作。

## 自定义管理页面样式

显然，左上角有个“Django 管理系统（Django administration）”看起来非常滑稽，它其实只是个占位文本而已。

你可以很容易的改变它 - 通过使用 Django 模板系统。Django 管理页面是 Django 自身提供的，它的界面使用的是 Django 的模板系统。

### 自定义项目模板

在你的项目文件夹（**manage.py** 所在的文件夹）里创建一个 **templates** 目录。模板（Template）可以放在 Django 拥有访问权的任何地方。（Django 和执行启动服务器操作的用户拥有相同的权限。）但是，把模板放在项目目录是个惯例，遵守它有好处。

打开设置文件（记住，是 **mysite/settings.py**），向 **TEMPLATES** 设置中添加 **DIRS** 选项：

```python3
# mysite/settings.py

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

**DIRS** 是在载入 Django 模板时会搜索的文件系统上的目录集合。

现在在 **templates** 里创建 **admin** 目录，然后从 Django 代码目录里的默认 Django 管理页面模板目录（**django/contirb/admin/templates**）中将 **admin/base_site.html** 模板复制到新建的目录里。

> **Django 代码目录在哪**
>
> 如果你不知道 Django 代码目录在哪，尝试以下命令：
>
```
$ python -c "
import sys
sys.path = sys.path[1:]
import django
print(django.__path__)"
```

然后，只要把 **{{ site_header|default:_('Django administration') }}**（包括大括号）替换为你想要的字符就行了。你应该把它编辑成大概这个样子：

```html+django
{% block branding %}
<h1 id="site-name"><a href="{% url 'admin:index' %}">Polls Administration</a></h1>
{% endblock %}
```

我们只是用这个例子来教你如何覆盖模板。在实际项目里，使用 **django.contrib.admin.AdminSite.site_header** 属性来设置自定义标题会更简单。

模板文件里含有很多像 **{% block branding %}** 和 **{{ title }}** 这样的文本。**{%** 和 **{{** 标签是 Django 模板语言的一部分。当 Django 渲染 **admin/base_site.html** 时，模板语言将会被执行，执行的结果是产生一个 HTML 文档。如果你现在还不理解模板的话，别担心，教程的第三部分将会详细的介绍模板语言。

Django 管理界面的所有默认模板都能够被覆盖。如果你想覆盖它们的话，就像你覆盖 **base_site.html** 一样，把原模板复制到你的目录中，然后修改它即可。

### 自定义应用模板

敏锐的读者将会思考：如果 **DIRS** 目录维持默认的空值的话，Django 怎么知道去哪找默认的模板呢？答案是，因为 **APP_DIRS** 被设置为 **True**，Django 会自动搜索每个应用目录下的 **templates/** 子目录，将它们作为最后的备用选择。（别忘了，**django.contrib.admin** 也是个应用）

我们的投票应用并不太复杂，不需要自定义管理页面。但是如果它慢慢变成一个复杂的应用，为了增加某些功能需要修改 Django 的管理页面标准模板，这时候，比起项目模板，修改 *应用* 模板会是更明智的选择。通过这种方式，你可以在新项目中使用投票应用并且保证它还能找到所需要的自定义模板。

查看 [template load documentation](https://docs.djangoproject.com/en/1.8/topics/templates/#template-loading) 来获取有关 Django 如何寻找模板的细节信息。

## 自定义管理页面索引页

类似的。你有可能想自定义 Django 管理页面索引页的样式。

默认情况下，索引页会按字母顺序显示出所有在 **INSTALL_APPS** 里并且已经向 admin 注册过的应用。你可能想对这个界面做一些大的修改。但是，要记住，索引页可能是众多管理页面里最重要的一个，它需要是简单且易用的，

需要自定义的是 **admin/index.html**。（和上面自定义 **admin/base_site.html** 的步骤类似 - 从默认模板目录复制一份到自定义模板目录）打开这个文件，你会看见它使用了一个叫做 **app_list** 的变量。这个变量包含所有被激活的 Django 应用。除了使用这个变量，你还可以使用你认为合适的任何方法，把特定对象的管理页面地址硬编在模板里。再说一遍，如果你现在还不理解模板的话，别担心，教程的第三部分将会详细的介绍模板语言。

当熟悉了管理页面之后，你就可以开始阅读[教程的第三部分（zh）](part3.md) ，开始开发投票应用的公众界面了。
