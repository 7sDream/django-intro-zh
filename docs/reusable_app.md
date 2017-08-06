# 进阶内容：如何编写可重用的应用

本篇从 [教程第七部分（zh）](part7.md)结束的地方开始。我们将把之前写的调查应用转化为一个可在新项目中重复使用，和能分享给其他人的独立 Python 包。

如果您还没有完成教程 1-7，我们鼓励你把它们完成一遍，以便使你的项目和下面的教程相匹配。

## 可重用性问题

设计，构建，测试和维护 Web 应用程序需要大量的工作。许多 Python 和 Django 的项目都会面临这个问题。如果我们能节省一些重复工作的话，那一定会很棒。

可重用性深入 Python 的设计理念中。[Python 包索引（PyPI）](https://pypi.python.org/pypi)里有大量可以在你自己的程序中使用的包。查看[用于 Django 的包](https://www.djangopackages.com/)这个分类，里面包含着现有的可重用应用程序，你可以在自己的项目里使用它们。Django 本身也只是一个 Python 包。这意味着，你可以充分利用现有的 Python 包或者 Django 应用程序来帮助完成自己的 Web 项目。你只需要编写一些特殊的部分即可。

假设，你的新项目需要一个类似我们之前写过的那样的投票应用。如何使这个应用可重用呢？幸运的是，你已经在正确的路上了。在 [教程第三部分（zh）](part3.md)中，我们了解了如何从通过在项目级别的 URL 配置文件中使用 **include** 来使投票应用和主项目分离。在本教程中，我们将采取进一步的措施，使应用能很容易在新项目中使用，并发布给别人安装和使用。

> **包？应用？**
>
> Python 包（[package](https://docs.python.org/3/glossary.html#term-package)）提供了将 Python 代码按相关性分组的组织方式，这一方式提高了可重用性。包中包含一个或多个 Python 代码文件（也被称为“模块”）。
>
> 包可以通过 **import foo.bar** 或 **from foo import bar** 的方式被导入。对于由一个目录（如 **polls**）组织起来的包，目录里必须有一个特殊的文件 **__init__.py**，即使这个文件是空的。
>
> Django 应用也只是一个 Python 包，不过是专门用于 Django 项目的。应用一般遵循常见的 Django 的公约，例如会包含 **models（模型）**，**tests（测试）**，**urls（网址）** 和 **views（视图）** 等子模块。
>
> 之后我们使用术语 **打包** 来描述”制作一个能被其他人使用的 Python 包“的过程。这可能会有点令人迷糊。（译注：原文中这句的意思是后文将会把 package 同时作为动词和名词，可能造成阅读困难，但翻译成中文后并不会，因为我们有**打包**这个词 ^\_^。）

## 你的项目和你可重用的应用

在跟着做完前面的教程后，我们的项目应该是这样的：

```text
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
    polls/
        __init__.py
        admin.py
        migrations/
            __init__.py
            0001_initial.py
        models.py
        static/
            polls/
                images/
                    background.gif
                style.css
        templates/
            polls/
                detail.html
                index.html
                results.html
        tests.py
        urls.py
        views.py
    templates/
        admin/
            base_site.html
```

你在 [教程第七部分（zh）](part7.md)中创建了 **mysite/templates**，在 [教程第三部分（zh）](part3.md)中创建了 **polls/templates**。现在你也许能更清楚的了解为什么我们选择了将项目和应用的模板目录分开：所有投票应用所用到的东西都在应用里。这使应用完全包含自身，更容易被新的项目所使用。

**polls** 目录现在可以被复制到一个新的 Django 项目，并可以立即被重用。但是对发布来说，还有一些地方需要准备。我们需要打包的应用程序，以方便他人进行安装。

## 安装一些必备工具

Python 打包的解决方案目前有点混乱，因为有各种不同的工具。在本教程中，我们将使用 [setuptools](https://pypi.python.org/pypi/setuptools) 建立我们的包。这是推荐的打包工具（与**distribute**分支合并后）。可是使用 pip 来安装和卸载它。你现在应该安装这两个软件包。如果需要帮助，你可以参考[如何使用 pip 安装 Django](https://docs.djangoproject.com/en/1.11/topics/install/#installing-official-release)。您可以用相同的方式安装 **setuptools**。

## 打包你的应用

Python 的 *打包* 是指将你的应用制作成特定的格式，以便能被很方便地安装和使用。Django 本身就是按照这个规定打包的。对于一个小应用，比如我们的投票应用，这个过程不会太困难。

1.首先，在你的 Django 项目外为 **polls** 目录创建一个父目录， 命名为 **django-polls**。

> **选择你应用的名字**
>
> 当为你的包选择名称时，记得检查 PyPI 上的内容以避免与现有的包产生冲突。以 **django-** 作为模块名称前缀是很实用的，这将有助于想寻找 Django 应用的人来识别哪些包是用于 Django 的。
>
> 应用标签（即，以点分隔的模块路径的最后一部分）*必须* 在 [**INSTALLED_APPS**](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-INSTALLED_APPS) 里是独一无二的。避免和 Django [contrib package](https://docs.djangoproject.com/en/1.11/ref/contrib/)使用相同的标签，例如 **auth**，**admin**，或 **message**。

2.移动 **polls** 目录到 **django-polls**目录下

3.创建文件 **django-polls/README.rst**，写入下面的内容。

```rst
# django-polls/README.rst

=====
Polls
=====

Polls is a simple Django app to conduct Web-based polls. For each
question, visitors can choose between a fixed number of answers.

Detailed documentation is in the "docs" directory.

Quick start
-----------

1. Add "polls" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = [
        ...
        'polls',
    ]

2. Include the polls URLconf in your project urls.py like this::

    url(r'^polls/', include('polls.urls')),

3. Run `python manage.py migrate` to create the polls models.

4. Start the development server and visit http://127.0.0.1:8000/admin/
   to create a poll (you'll need the Admin app enabled).

5. Visit http://127.0.0.1:8000/polls/ to participate in the poll.
```

4.创建 **django-polls/LICENSE** 文件。选择许可证超出了本指南的范围，但可以说，没有许可证的公开发布的代码是 *没有用的*。Django 和许多 Django 兼容的应用程序是在 BSD 许可下发布的；然而，你可以自由选择你自己的许可证。只是要注意你的许可选择会影响到谁能使用你的代码。

5.接下来我们将创建一个 **setup.py** 文件，这个文件提供了有关如何创建和安装应用的细节。对此文件的完整的解释已经超出了本文的范围，但 [setuptools docs](http://pythonhosted.org/setuptools/setuptools.html) 文档对它有很棒的解释。创建包含以下内容的 **django-polls/setup.py** 文件：

```python3
# django-polls/setup.py

import os
from setuptools import find_packages, setup

with open(os.path.join(os.path.dirname(__file__), 'README.rst')) as readme:
    README = readme.read()

# 使 setup.py 能在任何地方运行
os.chdir(os.path.normpath(os.path.join(os.path.abspath(__file__), os.pardir)))

setup(
    name='django-polls',
    version='0.1',
    packages=find_packages(),
    include_package_data=True,
    license='BSD License',  # example license
    description='A simple Django app to conduct Web-based polls.',
    long_description=README,
    url='https://www.example.com/',
    author='Your Name',
    author_email='yourname@example.com',
    classifiers=[
        'Environment :: Web Environment',
        'Framework :: Django',
        'Framework :: Django :: X.Y',  # replace "X.Y" as appropriate
        'Intended Audience :: Developers',
        'License :: OSI Approved :: BSD License',  # example license
        'Operating System :: OS Independent',
        'Programming Language :: Python',
        # Replace these appropriately if you are stuck on Python 2.
        'Programming Language :: Python :: 3',
        'Programming Language :: Python :: 3.4',
        'Programming Language :: Python :: 3.5',
        'Topic :: Internet :: WWW/HTTP',
        'Topic :: Internet :: WWW/HTTP :: Dynamic Content',
    ],
)
```

6.生成的打包文件中默认只包含 Python 模块和包。如果想包括额外的文件，我们需要创建一个 **MANIFEST.in** 文件。上一步骤中提到的 setuptools 文档里有更多的细节介绍。为了包含**模板**、我们的 **README.rst** 和 **LICENSE** 文件，请创建包含以下内容的 **django-polls/MANIFEST.in** 文件：

```text
# django-polls/MANIFEST.in

include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
```


7.这是可选步骤，但是我们建议完成这个步骤。将应用的详细的文档一起打包进去。为以后的文档创建一个空目录 **django-polls/docs**，在 **django-polls/MANIFEST.in** 里添加一行：

```text
recursive-include docs *
```

注意，如果 **docs** 目录里没有文件，那么它是不会被打包的。很多 Django 应用还通过托管网站，如[readthedocs.org](https://readthedocs.org)，提供在线文档。

8.尝试通过 **python setup.py sdist** 命令进行打包（在 **django/polls** 目录里运行）。这将创建一个名为 **dist** 的目录，并在其中创建打包文件 **django-polls-0.1.tar.gz**。

关于打包的更多信息，参见 Python 教程：[Tutorial on Packaging and Distributing Projects](https://packaging.python.org/distributing/)。

## 使用你的包

自从我们把 **polls** 目录从项目中移走了，它就不再工作了。我们现在通过安装新的 **django/polls** 包解决这个问题。

> **作为用户库安装**
>
> 以下步骤将把 **django/polls** 安装为用户库。和全局安装相比，单用户安装有很多好处，比如可以在没有管理员权限的系统中使用，以及防止安装的包影响系统服务或机器上的其他用户。
>
> 请注意，单用户安装仍然可能影响到其他用户的系统的工具，所以**virtualenv** 是一个更强大的解决方案（见后文）。

1.要安装这个包，得用到pip（你已经安装的了吧，对吗？）

```bash
pip install --user django-polls/dist/django-polls-0.1.tar.gz
```

2.如果幸运的话，你的 Django 项目现在应该可以正常工作了。再次运行服务器以确认。

3.想卸载这个包，用pip。

```bash
pip uninstall django-polls
```

## 发布你的应用

现在，我们已经打包并测试了 **django-polls**，并准备与所有人共享它！如果这不是一个示例的话，你现在可以：

- 将这个包通过电子邮件发给朋友。
- 将包上传到你自己的网站上。
- 在公共库，如[Python 包索引（PyPI）](https://pypi.python.org/pypi)，上发布你的包。[packaging.python.org](https://packaging.python.org)上有[很棒的教程](https://packaging.python.org/distributing/#uploading-your-project-to-pypi)。

## 用 virtualenv 安装 Python 包

之前，我们将投票应用程序安装为用户库。这有一些缺点：
- 修改用户库可能影响你系统中的其他 Python 软件。
- 你将无法同时使用这个包的多个版本（或相同名字的其他包）。

通常，这些情况仅出现在你同时管理 多个 Django 项目时。当出现这种情况时，最好的办法是使用 [virtualenv](https://virtualenv.pypa.io)。这个工具允许你维护多个分离的 Python 环境，每一个复制了一份自己专用的库和包命名空间（package namespace）。
