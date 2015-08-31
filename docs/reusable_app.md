#高级教程：编写可重用的应用
这高级教程起始于[教程6](https://docs.djangoproject.com/en/1.8/intro/tutorial06/)之后。我们将目光转向了网络调查到一个独立的你可以在新项目中重复使用，并与其他人分享的独立Python包。


如果您最近还没有完成教程1-6，我们鼓励你复习那些以便你的示例项目相符于下面的描述。


##可重用性问题




这是一个很大的工作来设计，构建，测试和维护的Web应用程序。许多Python和Django的项目共享这普通的问题。，如果我们能节省一些重复这个工作，那岂不是伟大的吗？




可重用性存在于Python中.你可以在自己的Python程序使用[Python包索引（PyPI）](https://pypi.python.org/pypi)很大的范围。检查出的[Django包](https://www.djangopackages.com/)为现有的可重复使用的应用程序，你可以将你的项目进行合并。 Django本身也只是一个Python包。这意味着，你可以利用现有的Python包或者Django的应用程序，并撰写他们到自己的Web项目。你只需要编写使你的项目独特的部分即可。


比方说，你已经开始一个需要像一个我们一直在民意测验应用程序的新项目。如何使这个应用程序的可重用？幸运的是，你已经在路上了。在[教程3](https://docs.djangoproject.com/en/1.8/intro/tutorial03/)中，我们看到了我们如何从项目级别的URL配置使用**包括**。在本教程中，我们将采取进一步的措施，以使应用程序很容易在新项目中使用，并准备发布给别人安装和使用。



> **包？应用？**
>
> 一个Python包提供了易于重用分组相关的Python代码的方式。包中包含一个或多个文件的Python代码（也被称为“模块”）。
>
>一个包可以**导入foo.bar**或**从foo导入到bar中**。对于一个目录（如**polls**）形成一个包，它必须包含一个特殊的文件**__init__.PY**，即使这个文件是空的。
>
>Django应用程序仅仅是一个Python包，是专门用于在Django项目中使用。应用程序可以使用常见的Django的约定，例如**模型**，**测试**，*URL* 和 **视图**模块。
>
>后来我们使用术语包装来描述做一个Python包使得容易为他人安装的过程。我们知道它是有点混乱的。


##你的项目和你可重用的应用程序

在前面的教程后，我们的项目应该是这样的：

```
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





您在[教程2](https://docs.djangoproject.com/en/1.8/intro/tutorial02/)中创建的**mysite/模板**和投票/在[教程3](https://docs.djangoproject.com/en/1.8/intro/tutorial03/)的**polls/templates**。现在也许是更清楚为什么我们选择了对项目和应用程序单独的模板目录：一切是投票应用是在调查的一部分。它使应用程序自包含的，且更容易落入一个新的项目。




**polls**目录现在可以被复制到一个新的Django项目，并立即重新使用。这不是充分准备，虽然公布。为此，我们需要打包的应用程序，以方便他人进行安装。



##安装一些先决条件
Python打包的当前状态是有点不清楚的各种工具。在本教程中，我们将使用[setuptools](https://pypi.python.org/pypi/setuptools)的建立我们的包。这是推荐的打包工具（与**分叉**合并）。我们也将使用PIP安装和卸载它。你现在应该安装这两个软件包。如果您需要帮助，您可以参考一下[如何使用PIP安装Django](https://docs.djangoproject.com/en/1.8/topics/install/#installing-official-release)。您可以安装**setuptools**的方式相同。


##包装你的应用




Python包装是指在一个特定的格式，可以很容易地安装和使用的准备你的应用程序。Django本身包装很喜欢这样。对于一个小的应用程序，如投票，这个过程是不太困难。

1.首先，创建一个**polls**的父目录，你的Django项目外。调用此目录**django-polls**。

>
>选择你应用的名字
>
>当选择一个名称为您的包装，检查资源如PyPI上避免命名与现有的包冲突。来在**Django -**作为模块名称来创建一个封装分发通常很有用。这帮助别人寻找Django程序识别你的应用Django特定。


>
>应用标签（即，这条路径的应用程序包的最后一部分）必须在**installed_apps**是独一无二的。避免使用相同的标签如Django [contrib packages](https://docs.djangoproject.com/en/1.8/ref/contrib/)，例如**认证**，**管理员**，或**信息**。



2.移动**polls 目录到 **django-polls**目录下
3.创建文件 **django-polls/README.rst**  带着接下来的内容。


```python
#django-polls/README.rst

=====

Polls

=====


Polls is a simple Django app to conduct Web-based polls. For each
question, visitors can choose between a fixed number of answers.

Detailed documentation is in the "docs" directory.

Quick start

-----------

1. Add "polls" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = (
        ...
        'polls',
    )

2. Include the polls URLconf in your project urls.py like this::

    url(r'^polls/', include('polls.urls')),

3. Run `python manage.py migrate` to create the polls models.

4. Start the development server and visit http://127.0.0.1:8000/admin/
   to create a poll (you'll need the Admin app enabled).

5. Visit http://127.0.0.1:8000/polls/ to participate in the poll.

```





4.创建一个**django-polls/LICENSE**文件。选择许可证超出了本指南的范围，但它可以说，没有一个许可证公开发布的代码是没有用的。Django和许多Django兼容的应用程序是在BSD许可下发布的；然而，你可以自由选择你自己的许可证。只是要注意你的许可选择会影响谁能使用你的代码。


5.接下来我们将创建一个**setup.py**文件提供了有关如何创建和安装的应用程序。此文件的一个完整的解释已经超出了本文的范围，但它有一个很好的 [setuptools docs]()。创建具有下列内容的文件被**django-polls/setup.py**：




```python
#django-polls/setup.py

import os
from setuptools import setup

with open(os.path.join(os.path.dirname(__file__), 'README.rst')) as readme:
    README = readme.read()

# allow setup.py to be run from any path
os.chdir(os.path.normpath(os.path.join(os.path.abspath(__file__), os.pardir)))

setup(
    name='django-polls',
    version='0.1',
    packages=['polls'],
    include_package_data=True,
    license='BSD License',  # example license
    description='A simple Django app to conduct Web-based polls.',
    long_description=README,
    url='http://www.example.com/',
    author='Your Name',
    author_email='yourname@example.com',
    classifiers=[
        'Environment :: Web Environment',
        'Framework :: Django',
        'Intended Audience :: Developers',
        'License :: OSI Approved :: BSD License', # example license
        'Operating System :: OS Independent',
        'Programming Language :: Python',
        # Replace these appropriately if you are stuck on Python 2.
        'Programming Language :: Python :: 3',
        'Programming Language :: Python :: 3.2',
        'Programming Language :: Python :: 3.3',
        'Topic :: Internet :: WWW/HTTP',
        'Topic :: Internet :: WWW/HTTP :: Dynamic Content',
    ],
)

```


6.只有Python模块和包默认包中包含。包括额外的文件，我们需要创建一个**manifest.in**文件。包医生称在上一步中讨论更多的细节，这个文件。包括模板，和我们的**readme.rst**许可文件，创建具有下列内容的文件 **django-polls
/ manifest.in **：




```python
#django-polls/MANIFEST.in

include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
```


7.这是可选的，但建议，包括详细的文档与您的应用程序。创建一个空目录/文件以后文件**django-polls/docs**。添加一个额外的线**django-polls// manifest.in **:


```python
recursive-include docs *
```

注意，文件目录不在你的计划中包括除非你添加一些文件,很多Django程序还提供文档在线通过网站如[readthedocs.org](https://readthedocs.org/)。


8.尝试建立**python setup.py sdist包**（运行在**django/polls**）。这将创建一个目录名**dist**和建立你的新包，**django-polls-0.1.tar.gz**。

在包的更多信息，参见Python教程[Tutorial on Packaging and Distributing Projects](https://packaging.python.org/en/latest/distributing.html).


##Using your own package

自从我们把**polls**
目录从项目中移走了，它就不再工作了。我们现在通过安装新的**django/polls**包解决这个。




>
>作为一个用户库安装
>
>以下步骤安装**django/polls**作为用户库。每个用户安装有很多好处，在安装包系统，如可使用的系统，您没有管理员访问，以及防止包装影响系统服务和其他用户的机器。
>
>请注意，每个用户的设备仍然可以影响系统的工具，运行该用户的行为，所以**virtualenv**是一个更强大的解决方案（见下图）。


1.要安装这个包，用到pip（你已经安装的了吧，对吗？）


```python
pip install --user django-polls/dist/django-polls-0.1.tar.gz

```


2.幸运的是，你的Django项目现在应该可以正常工作了。再次运行服务器以确认此。


3.想卸载这个包，用pip


```python
pip uninstall django-polls

```


##发布你的应用



现在，我们已经进行了封装和测试django-polls，它准备与世界分享！如果这不只是一个例子，你现在可以：
- 给朋友发电子邮件。 -

- 上传你的网站上的包。- 
- 后包在公共库，如[包索引（PyPI](https://pypi.python.org/pypi).[packaging.python.org](https://packaging.python.org/en/latest/)有这样[好的教程](https://packaging.python.org/en/latest/distributing.html#uploading-your-project-to-pypi)。- 



##用virtualenx安装python包






早些时候，我们将投票应用程序安装为用户库。这有一些缺点：
- 修改用户库可以影响你的系统的其他Python软件。- 
- 你将无法运行多个版本的这个包（或其他人用相同的名字）。- 



通常，这些情况仅出现一次你维持一些Django项目。当他们这样做时，最好的办法是使用[virtualenv](http://www.virtualenv.org/)。这个工具允许你维护多个分离的Python环境，每一个都有自己的图书馆和包的命名空间复制。



















