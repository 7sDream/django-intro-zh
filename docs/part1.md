# 创建你的第一个 Django 项目， 第一部分

来跟着实际项目学习 Django 吧。

在这个教程中，我门将创建一个基础的投票网站。

它包含两个部分：

- 一个让公众查看投票内容并进行投票的公共站点。
- 一个能让你增加、修改和删除投票的管理界面。

我们假设你已经成功的 [安装 Django（zh）](install.md)。如果你不清楚是否已经安装 Django 或不清楚安装的版本，请尝试以下命令：

```bash
$ python -c "import django; print(django.get_version())"
```

如果这行命令输出了一个版本号，证明你已经安装了此版本的 Django；如果你得到的是一个“No module named django”的错误提示，则表示你还未安装。

本教程的目标版本是 Django 1.8 和 Python 3.2 或更高版本。如果 Django 版本不匹配，你可以通过点击页面右下角的切换版本按钮来转到适合你版本的教程，或者你可以选择将 Django 升级到最新的版本。如果你使用的是 Python 2.7，那么需要对教程中的代码作一些微调，如何调整将会写在代码的注释里。

你可以查看文档 [快速安装指南（zh）](install.md) 来获得关于移除旧版本，安装新版本的流程和建议。

> **哪里可以获得帮助：**
>
> 如果你在阅读或实践本教程中遇到困难，请发消息给 [django-users](https://docs.djangoproject.com/en/1.8/internals/mailing-lists/#django-users-mailing-list) 或加入IRC频道 [django on irc.freenode.net](irc://irc.freenode.net/django) 来和其他的 Django 用户交流，他们也许能帮到你。

## 创建项目

如果这是你第一次使用 Django 的话，你需要一些初始化设置。也就是说，你需要一些自动生成的代码（即一个 Django 项目实例需要的设置项集合，包括 Django 配置和应用程序设置）来配置一个 Django [项目](https://docs.djangoproject.com/en/1.8/glossary/#term-project)。

打开命令行，**cd** 到一个你想放置你代码的目录，然后运行以下命令：

```bash
$ django-admin startproject mysite
```

这行代码将会在当前目录下创建一个 **mysite** 目录。如果命令失败了，文档 [Problems running django-admin](https://docs.djangoproject.com/en/1.8/faq/troubleshooting/#troubleshooting-django-admin) 可能能给你提供帮助。

> **注意**
>
> 你得避免使用 Python 或 Django 的内部保留字来命名你的项目。具体地说，你得避免使用像 **django**（会和 Django 自己产生冲突） 或 **test**（会合 Python 的内置模块产生冲突） 这样的名字。

> **我的代码该放在哪？**
>
> 如果你曾经是老式 PHP 程序员（没有使用过现代框架），你可能会习惯于把代码放在 Web 服务器的文档根目录（诸如 **/var/www**）。当使用 Django 时不需要这样做。把所有 Python 代码放在 Web 服务器的根目录不是个好主意，因为这样会有风险。比如会提高人们在网站上看到你的代码的可能性。这不利于网站的安全。
>
> 把你的代码放在文档根目录 **以外** 的某些地方吧，比如 **/home/mycode**。

让我们看看 **startproject** 创建了些啥：

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
- **manage.py**：一个让你用各种方式管理 Django 项目的命令行工具。你可以阅读 [django-admin and manage.py](https://docs.djangoproject.com/en/1.8/ref/django-admin/) 来获取关于 **manage.py** 的更多细节。
- 里面一层的 **mysite/** 目录包含你的项目，它是一个纯 Python 包。它的名字就是当你引用它内部任何东西时需要用到的 Python 包名。
- **mysite/\_\_init\_\_.py**：一个用于指明此目录是 Python 包的空白文件。（如果你刚开始学习 Python，请阅读 Python 官方文档中的 [more about packages](https://docs.python.org/tutorial/modules.html#packages) 部分。）
- **mysite/settings.py**：Django 项目的配置文件。如果你想知道这个文件是如何工作的，请看文档 [Django settings](https://docs.djangoproject.com/en/1.8/topics/settings/)。
- **mysite/urls.py**：Django 项目的 URL 声明，就像是你网站的“目录”。阅读 [URL dispatcher](https://docs.djangoproject.com/en/1.8/topics/http/urls/) 文档来获取更多关于 URL 的内容。
- **mysite/wsgi.py**：当你部署项目到一个兼容 WSGI 的服务器上时所需要的入口点。[How to deploy with WSGI](https://docs.djangoproject.com/en/1.8/howto/deployment/wsgi/) 文档内有更多关于这个文件的细节。

## 数据库配置

现在，打开 **mysite/settings.py**。这是包含着 Django 项目设置的 Python 模块。

通常，这个配置文件将会使用 SQLite 作为默认数据库。如果你不熟悉数据库，或者只是想尝试一下 Django，使用 SQLite 会是最简单的选择。 Python 已经内置了 SQLite 支持，所以你不需要安装任何其他东西就可以使用它。当你开始第一个真正的项目时，你可能倾向于从一开始就使用更强大的数据库，比如 PostgreSQL，来避免中途切换数据库这个令人头痛的问题。

如果你想使用其他数据库，你需要安装合适的 [database bindings](https://docs.djangoproject.com/en/1.8/topics/install/#database-installation)，然后改变设置文件中 **DATABASE 'default'** 项目中的一些键值：

- **ENGINE** - 可选值有 **'django.db.backends.sqlite3'**、**'django.db.backends.postgresql.psycopg2'**、**'django.db.backends.mysql'** 和 **'django.db.backends.oracle'**。这里还有一些其他的 [可用后端](https://docs.djangoproject.com/en/1.8/ref/databases/#third-party-notes)。
- **NAME** - 数据库的名称。如果使用的是 SQLite，数据库将是你电脑上的一个文件，在这种情况下，**NAME** 应该是此文件的绝对路径，包括文件名。默认值（**os.path.join(BASE_DIR, 'db.sqlite3')**）将会把数据库文件储存在项目的根目录。

如果你不是使用 SQLite，则必须添加一些额外设置，比如 **USER**、**PASSWORD**、**HOST** 等等。想了解更多数据库设置方面的内容，请看文档：**[DATABASE](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-DATABASES)**。

> **注意**
>
> 如果你使用 PostgreSQL 或 MySQL，请确认在使用前已经创建了数据库。你可以通过在你的数据库交互式命令行中使用“**CREATE DATABASE database_name;**”命令来完成这件事。
>
> 如果你使用 SQLite，那么你不需要在使用前做任何事 - 数据库会在需要的时候自动创建。

在你编辑 **mysite/settings.py** 时，可以顺便把 **TIME_ZONE** 设置为你的时区。

同时，关注一下文件头部的 **INSTALLED_APPS** 设置项，这里包括了会在你项目中启用的所有 Django 应用。应用能在多个项目中使用，你也可以打包并且发布应用，让别人使用它们。

通常，**INSTALLED_APPS** 默认包括了以下 Django 的自带应用：

- **django.contrib.admin** - 管理员界面。你将会在 [教程的第二部分（zh）](part2.md) 使用它。
- **django.contrib.auth** - 验证系统。
- **django.contrib.contenttypes** - 内容类型框架。
- **django.contrib.sessions** - 会话框架。
- **django.contrib.messages** - 消息框架。
- **django.contrib.staticfiles** - 管理静态文件的框架。

这些应用被默认启用是为了给常规项目提供方便。

默认开启的某些应用需要至少一个数据表，所以，在使用他们之前需要在数据库中创建一些表。请执行以下命令：

```bash
$ python manage.py migrate
```

**migrate** 命令检查 **INSTALLED_APPS** 设置，为其中的每个应用创建需要的数据表，至于具体会创建什么，这取决于你的 **mysite/settings.py** 设置文件和每个应用的数据库迁移文件（我们稍后会介绍这个）。这个命令所执行的每个迁移操作都会在终端中显示出来。如果你感兴趣的话，运行你数据库的命令行工具，并输入 **\\dt**（PostgreSQL），**SHOW TABLES**（MySQL）或者 **.schema**（SQLite）来看看 Django 到底创建了哪些表。

> **写给极简主义者**
>
> 就像之前说的，为了方便大多数项目，我们默认激活了一些应用，但并不是每个人都需要它们。如果你不需要某个或某些应用，你可以在运行 **`migrate`** 前毫无顾虑的从 **INSTALLED_APPS** 里注释或者删除掉它们。**migrate** 命令只会为在 **INSTALLED_APPS** 里声明了的应用进行数据库迁移。

## 用于开发的简易服务器

让我们来确认一下你的 Django 项目是否真的创建成功了。如果你的当前目录不是外层的 **mysite** 目录的话，请切换到此目录，然后运行下面的命令：

```bash
$ python manage.py runserver
```

你应该会看到如下输出：

```text
Performing system checks...

0 errors found
August 22, 2015 - 15:50:53
Django version 1.8, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

你刚刚启动的是 Django 自带的用于开发的简易服务器，它是一个用纯 Python 写的轻量级的 Web 服务器。我们将这个服务器内置在 Django 中是为了让你能快速的开发出想要的东西，因为你不需要进行配置生产级别的服务器（比如 Apache）方面的工作，除非你已经准备好投入生产环境了。

现在是个提醒你的好时机：**千万不要** 将这个服务器用于和生产环境相关的任何地方。这个服务器只是为了开发而设计的。（我们在 Web 框架方面是专家，在 Web 服务器方面并不是。）

现在这个小服务器已经开始工作了，尝试用你的浏览器访问 http://127.0.0.1:8000。不出意外的话，你将会看见令人赏心悦目的浅蓝色 “Django 欢迎页”，这代表你的项目已经创建成功了。

> **更换端口**
>
> 默认情况下，**`runserver`** 命令会将服务器设置为监听本机内部 IP 的 8000 端口。
>
> 如果你想更换服务器的端口，请使用命令行参数。举个例子，下面的命令会使服务器监听 8080 端口：
>
```bash
$ python manage.py runserver 8080
```
>
> 如果你想改变监听的 IP 的话，可以将它和端口号写在一起作为参数。所以，监听所有公网 IP（在你想将你的工作通过你的网络共享给其他人时尤其有用）的命令是：
>
```bash
$ python manage.py runserver 0.0.0.0:8000
```
>
> 关于这个简易服务器的完整信息可以在 [runserver](https://docs.djangoproject.com/en/1.8/ref/django-admin/#django-admin-runserver) 文档中找到。

> **会自动重新加载的服务器**
>
> 用于开发的服务器在需要的情况下会对每一次的访问请求重新载入一遍 Python 代码。所以你不需要为了让修改的代码生效而频繁的重新启动服务器。然而，一些动作，比如添加新文件，将不会触发自动重新加载，这时你得自己手动重启服务器。

## 创建模型

现在你的开发环境——这个项目——已经设置好了，你可以开始工作了。

在 Django 中，每一个应用都是一个 Python 包，并且遵循着一定的约定。Django 自带一个工具，可以帮你生成应用的基础目录结构，这样你就能专心的聚焦于写代码，而不是安排目录了。

> **项目 VS 应用**
>
> 项目和应用有啥区别？应用是一个专门做某件事的网络应用程序 - 比如博客系统，或者公共记录的数据库，或者简单的投票程序。项目则是一个网站使用的配置和应用的集合。项目可以包含很多个应用。应用可以被很多个项目使用。

你的应用可以放在 **`Python path`** 中的任何目录里。在这个教程中，我们将直接在 **manage.py** 所在的目录里创建投票应用，这样它就能够被当做顶级模块被引入，而不是作为 **mysite** 的子模块。

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

在 Django 里写一个数据库驱动的 Web 应用的第一步是定义模型 - 也就是数据库结构设计和附加的其他元数据。

> **设计哲学**
>
> 模型是真实数据的简单明确的描述。它包含了储存的数据所必要的字段和行为。Django 遵循 [DRY 原则](https://docs.djangoproject.com/en/1.8/misc/design-philosophies/#dry)。它的目标是你只需要定义数据模型，然后其他的杂七杂八代码你都不用关心，它们会自动从模型生成。
>
> 来介绍一下迁移 - 举个例子，不像 Ruby On Rails，Django 的迁移代码是由你的模型文件自动生成的，它本质上只是个历史记录，Django 可以用它来进行数据库的滚动更新，通过这种方式使其能够和当前的模型匹配。

在这个简单的投票应用中，需要创建两个模型：问题（Question）和选项（Choice）。问题模型包括问题描述和发布时间。选项模型有两个字段，选项描述和当前得票数。每个选项属于一个问题。

这些概念可以通过一个简单的 Python 类来描述。按照下面的例子来编辑 **polls/models.py** 文件：

```python3
# polls/models.py

from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

代码非常直白。每个模型被表示为 **django.db.models.Model** 类的子类。每个模型有一些类变量，它们都表示模型里的一个数据库字段。

每个字段都是 **Field** 类的实例 - 比如，字符字段被表示为 **CharField**，日期时间字段被表示为 **DateTimeField**。这将告诉 Django 每个字段要处理的数据类型。

每个 **Field** 类实例变量的名字（例如 **question_text** 或 **pub_date**）也是字段名，所以最好使用对机器友好的格式。你将会在 Python 代码里使用它们，而数据库会将它们作为列名。

你可以使用可选的选项来为 **Field** 定义一个人类可读的名字。这个功能在很多 Django 内部组成部分中都被使用了，而且作为文档的一部分。如果某个字段没有提供此名称，Django 将会使用对机器友好的名称（也就是变量名）。在上面的例子中，我们只为 **Question.pub_date** 定义了对人类友好的名字。对于模型内的其他字段，他们的机器友好名也会被作为人类友好名使用。

定义某些 **Field** 类实例需要参数。例如 **CharField** 需要一个 **max_length** 参数。这个参数的用处不止于用来定义数据库结构，也用于验证数据，我们稍后将会看到这方面的内容。

**Field** 能够接收多个可选参数；在上面的例子中：我门将 **votes** 的 **default** ，也就是默认值，设为0。

注意在最后，我们使用 **ForeignKey** 定义了一个关系。这将告诉 Django，每个 **Choice** 对象都关联到一个 **Question** 对象。Django 支持所有常用的数据库关系：多对一、多对多和一对一。

## 使用模型

上面的一小段用于创建模型的代码给了 Django 很多信息，通过这些信息，Django 可以：

- 为这个应用创建数据库结构（生成 **CREATE TABLE** 语句）。
- 创建可以与 **Question** 和 **Choice** 对象进行交互的 Python 数据库 API。

但是首先得把 **polls** 应用安装到我们的项目里。

> **设计哲学**
>
> Django 应用是“可插拔”的。你可以在多个项目中使用同一个应用。除此之外，你还可以发布自己的应用，因为它们并不会被绑定到当前安装的 Django 上。

再次编辑 **mysite/settings.py**，改变 **INSTALLED_APPS** 设置，使其包含字符串 **polls**。它现在看起来应该像这样：

```python3
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'polls',
)
```

现在你的 Django 项目会包含 **polls** 应用。接着运行下面的命令

```bash
$ python manage.py makemigrations polls
```

你将会看到类似于下面这样的输出：

```text
Migrations for 'polls':
  0001_initial.py:
    - Create model Question
    - Create model Choice
    - Add field question to choice
```

通过 **migrations** 命令，Django 会检测你对模型文件的修改，并且把修改的部分储存为一次 *迁移*。

迁移是 Django 对于模型定义（也就是你的数据库结构）的变化的储存形式 - 没那么玄乎，它们其实也只是一些你磁盘上的文件。如果你想的话，你可以阅读一下你模型的迁移数据，它被储存在 **polls/migrations/0001_initial.py** 里。别担心，你不需要每次都阅读迁移文件，但是它们被设计成人类可读的形式，这是为了便于你手动修改它们。

Django 有一个自动执行数据库迁移并同步管理你的数据库结构的命令 - 这个命令是 **migrate**，我们马上就会接触它 - 但是首先，让我们看看迁移命令会执行哪些 SQL 语句。**sqlmigrate** 命令接收一个迁移的名称，然后返回给你对应的 SQL：

```bash
$ python manage.py sqlmigrate 0001
```

你将会看到类似下面这样的输出（我把输出重组成了人类可读的格式）：

```text
BEGIN;
CREATE TABLE "polls_choice" (
    "id" serial NOT NULL PRIMARY KEY,
    "choice_text" varchar(200) NOT NULL,
    "votes" integer NOT NULL
);
CREATE TABLE "polls_question" (
    "id" serial NOT NULL PRIMARY KEY,
    "question_text" varchar(200) NOT NULL,
    "pub_date" timestamp with time zone NOT NULL
);
ALTER TABLE "polls_choice" ADD COLUMN "question_id" integer NOT NULL;
ALTER TABLE "polls_choice" ALTER COLUMN "question_id" DROP DEFAULT;
CREATE INDEX "polls_choice_7aa0f6ee" ON "polls_choice" ("question_id");
ALTER TABLE "polls_choice"
  ADD CONSTRAINT "polls_choice_question_id_246c99a640fbbd72_fk_polls_question_id"
    FOREIGN KEY ("question_id")
    REFERENCES "polls_question" ("id")
    DEFERRABLE INITIALLY DEFERRED;

COMMIT;
```

请注意以下几点：

- 输出的内容和你使用的数据库有关，上面的输出示例使用的是 PostgreSQL。
- 数据库的表名是由应用名（**polls**）和模型名的小写形式（**question** 和 **choice**）连接而来。（如果需要，你可以自定义此行为。）
- 主键（ID）会被自动创建。（当然，你也可以自定义。）
- 默认的，Django 会在外键字段名后追加字符串“\_id”。（同样，这也可以自定义。）
- 外键关系由 **FOREIGN KEY** 生成。你不用关心 **DEFERRABLE** 部分，它只是告诉 PostgreSQL，请在事务全都执行完之后再创建外键关系。
- 生成的 SQL 语句是为你所用的数据库定制的，所以那些和数据库有关的字段类型，比如 **auto_increment**（MySQL）、**serial**（PostgreSQL）和 **integer primary key autoincrement**（SQLite），Django 会帮你自动处理。那些和引号相关的事情 - 例如，是使用单引号还是双引号 - 也一样会被自动处理。
- **sqlmigrate** 命令并没有真正在你的数据库中的执行迁移 - 它只是把命令输出到屏幕上，让你看看 Django 认为需要执行哪些 SQL 语句。这在你想看看 Django 到底准备做什么，或者当你是数据库管理员，需要写脚本来批量处理数据库时会很有用。

如果你感兴趣，你也可以试试运行 **`python manage.py check`** ;这个命令帮助你检查项目中的问题，并且在检查过程中不会对数据库进行任何操作。

现在，再次运行 **migrate** 命令，在数据库里创建新定义的模型的数据表：

```text
$ python manage.py migrate
Operations to perform:
  Synchronize unmigrated apps: staticfiles, messages
  Apply all migrations: admin, contenttypes, polls, auth, sessions
Synchronizing apps without migrations:
  Creating tables...
    Running deferred SQL...
  Installing custom SQL...
Running migrations:
  Rendering model states... DONE
  Applying <migration name>... OK
```

**migrate** 命令选中所有还没有执行过的迁移（Django 通过在数据库中创建一个特殊的表 **django_migrations** 来跟踪执行过哪些迁移）并应用在数据库上 - 也就是将你对模型的更改同步到数据库结构上。

迁移是非常强大的功能，它能让你在开发过程中持续的改变数据库结构而不需要重新删除和创建表 - 它专注于使数据库平滑升级而不会丢失数据。我们会在后面的教程中更加深入的学习这部分内容，现在，你只需要记住，改变模型需要这三步：

- 编辑 **models.py** 文件，改变模型。
- 运行 `python manage.py makemigrations` 为模型的改变生成迁移文件。
- 运行 `python manage.py migrate` 来应用数据库迁移。

数据库迁移被分解成生成和应用两个命令是为了让你能够在代码控制系统上提交迁移数据并使其能在多个应用里使用；这不仅仅会让开发更加简单，也给别的开发者和生产环境中的使用带来方便。

通过阅读文档 [Django-admin documentation](https://docs.djangoproject.com/en/1.8/ref/django-admin/)，你可以获得关于 **manage.py** 工具的更多信息。

## 初试 API

现在让我们进入交互式 Python 命令行，尝试一下 Django 为你创建的各种 API。通过以下命令打开 Python 命令行：

```bash
$ python manage.py shell
```

我们使用这个命令而不是简单的使用“Python”是因为 **manage.py** 会设置 **DJANGO_SETTINGS_MODULE** 环境变量，这个变量会让 Django 根据 **mysite/settings.py** 文件来设置 Python 包的导入路径。

> **我就是不想用 manage.py**
>
> 如果你不想使用 **manage.py** ，没问题，你只要手动将 **DJANGO_SETTINGS_MODULE** 环境变量设置为 **mysite.settings** 就行。打开一个普通的 Python 命令行，然后输入以下命令来配置 Django：
>
```pycon
>>> import django
>>> django.setup()
```
>
> 如果抛出了 **AttributeError**，那么说明你使用的 Django 版本可能和本教程不匹配。你可以切换到旧版本的教程或者把 Django 升级至最新版本。
>
> 你必须在 **manage.py** 所在目录中运行 **python** 命令，或者确保这个目录在 Python path 里，因为只有这样 import mysite 才能被正确的执行。
>
> 阅读文档 [Django-admin documentation](https://docs.djangoproject.com/en/1.8/ref/django-admin/) 获取更多信息。

当你成功进入命令行后，来试试 [数据库 API](https://docs.djangoproject.com/en/1.8/topics/db/queries/) 吧：

```pycon
>>> from polls.models import Question, Choice   # 导入刚刚创建的模型类

# 现在没有 Question 对象
>>> Question.object.all()
[]

# 创建新 Question
# 在 settings 文件里，时区支持被设为开启状态，所以
# pub_date 字段要求一个带有时区信息（tzinfo）
# 的 datetime 数据。请使用 timezone.now() 代替
# datetime.datetime.now()，这样就能获取正确的时间。
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# 想将对象保存到数据库中，必须显式的调用 save()。
>>> q.save()

# 现在它被分配了一个 ID。注意有可能你的结果是“1L”而不是“1”，
# 这取决于你在使用哪种数据库。这不是什么大问题；只是表明
# 你所用的数据库后端倾向于将整数转换为 Python 的
# long integer 对象。
>>> q.id
1

# 通过属性来获取模型字段的值
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# 通过改变属性值来改变模型字段，然后调用 save()。
>>> q.question_text = "What's up"
>>> q.save()

# objects.all() 显示数据库中所有 question。
>>> Question.objects.all()
[<Question: Question object>]
```

等等。用 \<Question: Question object\> 表示这个对象没什么意思，它无法告诉我们这个对象的详细信息。让我们通过编辑 **Question** 模型的代码（**polls/models.py** 文件），给 **Question** 和 **Choice** 增加 **\_\_str\_\_()** 方法来改善这个问题：

```python3
# polls/models.py

from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):  # Python 2 请改为 __unicode__
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):  # Python 2 请改为 __unicode__
        return self.choice_text
```

给模型增加 **\_\_str\_\_()** 方法是很重要的，这不仅仅能给你在命令行里使用带来方便，Django 自动生成的 admin 里也使用这个方法来表示对象。

> **\_\_str\_\_ 还是 \_\_unicode\_\_?**
>
> Python 3 环境下，很简单，用 \_\_str\_\_()。
>
> 在 Python 2 中，你需要定义 \_\_unicode\_\_() 方法来返回一个 **unicode** 值。Django 模型有一个默认的 \_\_str\_\_() 方法来调用 \_\_unicode\_\_() 并且将返回值转换为 UTF-8 字节串。也就是说，unicode(p) 将会返回 Unicode 字符串，而 str(p) 将会返回字符被 UTF-8 编码之后字节串。Python 所作的恰恰相反：对象有一个调用 \_\_str\_\_() 并将其解释为 ASCII 字符的 \_\_unicode\_\_() 方法。这些不同之处可能会造成冲突。
>
> 如果你不想管这些乱七八糟的事，很简单，使用 Python 3 吧。

我们还可以向模型里添加普通的 Python 方法，让我们加一个试试看：

```python3
# polls/models.py

import datetime

from django.db import models
from django.utils import timezone


class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

新加入的 `import datetime` 和 `from django.utils import timezone` 分别导入了 Python 的标准 datetime 模块和 Django 中和时区相关的 django.utils.timezone 工具模块。如果你不太熟悉 Python 中的时区处理，看看 [time zone support docs](https://docs.djangoproject.com/en/1.8/topics/i18n/timezones/) 文档吧。

保存文件然后通过 `python manage.py shell` 命令再次打开 Python 交互式命令行：

```pycon
>>> from polls.models import Question, Choice

# 确认 __str__() 正常工作了。
>>> Question.objects.all()
[<Question: What's up?>]


# Django 提供了丰富的数据库查找 API，
# 通过关键字参数就能轻松使用。
>>> Question.objects.filter(id=1)
[<Question: What's up?>]
>>> Question.objects.filter(question_text__startswith='What')
[<Question: What's up?>]

# 获取今年发布的问题
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# 查找一个不存在的 ID 将会引发异常
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# 通过主键来查找数据是非常常见的需求，所以 Django
# 为这种需求专门制定了一个参数。
# 以下代码等同于 Question.objects.get(id=1)。
>>> Question.objects.get(pk=1)
<Question: What's up?>

# 确认我们自定义的方法正常工作。
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# 给这个问题添加几个选项。create 函数会创建一个新的
# Choice 对象，执行 INSERT 语句，将 Choice 添加到
# Question 的选项列表中，最后返回刚刚创建的
# Choice 对象。Django 创建了一个集合 API 来使你可以从
# 外键关系的另一方管理关联的数据。
# （例如，可以获取问题的选项列表）
>>> q = Question.objects.get(pk=1)

# 显示所有和当前问题关联的选项列表，现在是空的。
>>> q.choice_set.all()
[]

# 创建三个选项。
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice 对象能通过 API 获取关联到的 Question 对象。
>>> c.question
<Question: What's up?>

# 反过来，Question 对象也可以获取 Choice 对象
>>> q.choice_set.all()
[<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]
>>> q.choice_set.count()
3

# 查找 API 的关键字参数可以自动调用关系函数。
# 只需使用双下划线来分隔关系函数。
# 只要你想，这个调用链可以无限长。
# 例如查找所有「所在问题的发布日期是今年」的选项
# （重用我们之前创建的 'current_year' 变量）
>>> Choice.objects.filter(question__pub_date__year=current_year)
[<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]

# 试试删除一个选项，使用 delete() 函数。
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
```

阅读 [Accessing related objects](https://docs.djangoproject.com/en/1.8/ref/models/relations/) 文档可以获取关于数据库关系的更多内容。想知道关于双下划线的更多用法，参见 [Field Lookup](https://docs.djangoproject.com/en/1.8/topics/db/queries/#field-lookups-intro) 文档。数据库 API 的所有细节可以在 [Database API reference](https://docs.djangoproject.com/en/1.8/topics/db/queries/) 文档中找到。

当你熟悉了数据库 API 之后，你就可以开始阅读 [第二部分（zh）](part2.md) 了，下一部分我们将会学习 Django 自动生成的 admin 管理界面。
