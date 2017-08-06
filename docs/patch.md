# 为 Django 编写你的第一个补丁

## 介绍

对反馈社区有点兴趣？也许你发现了 Django 的一个 bug，然后你想修复它，或者你为 Django 想添加一些小特性。

回馈 Django 本身就是看到自己所关心的问题的最佳方式。也许初看会吓到你，但这实际上是很简单的。我们将带你走一遍整个流程，好让你可以从例子中学习。

## 本教程的目标对象？

> **参见**
>
> 如果你正在为如何提交补丁，寻找参考指南，可以看[提交补丁](https://docs.djangoproject.com/en/1.11/internals/contributing/writing-code/submitting-patches/)文档。

在本教程里，我们期望你至少能基本明白 Django 是如何工作的。这意味着你应该顺利地读完了[实例教程](index.md)。还有，你应该对 Python 本身也有了很好地了解。如果没有，可以在线看看这本有趣的 [Dive Into Python](www.diveintopython3.net)，它面向 Python 新手程序员。

不熟悉版本控制系统和 Trac 的人会发现本教程及其链接的信息刚刚好够开始。但是，如果你打算定期为 Django 做贡献，你可能会想了解更多关于这些不同工具的知识。

在大多数情况下，本教程会尽可能多地进行解释，以确保受众能接受。

> **哪里可以获得帮助：**
>
> 如果你在阅读或实践本教程中遇到困难，请发消息给 [django-users](https://docs.djangoproject.com/en/1.11/internals/mailing-lists/#django-users-mailing-list) 或加入IRC频道 [django on irc.freenode.net](irc://irc.freenode.net/django) 来与其他 Django 用户进行交流，他们也许能帮到你。

## 本教程覆盖了哪些知识点

首先我们会带你走完为 Django 贡献补丁的流程。在本教程结束时，你应该对工具和流程都有了基本认识。特别地，我们将会介绍以下内容：

- 安装 Git
- 如何下载 Django 开发版的拷贝
- 运行 Django 的测试套件
- 为你的补丁编写测试程序
- 为你的补丁编写代码
- 测试你的不行
- 提交 Pull Request
- 哪里可以查看更多信息

一旦你完成了本教程，你就可以去看完[为 Django 做贡献文档](https://docs.djangoproject.com/en/1.11/internals/contributing/)的剩下部分了。那里有很多信息，而且是那些想成为定期贡献者必看的。如果你有问题，你也可能在那得到答案。

> 需要 **Python3**！
>
> 本教程假定你用的就是 Python3。请到[Python 官网下载页](https://www.python.org/downloads/)或者你系统的包管理安装最新版的 Python3。

> **给 Windows 用户**
>
> 给 Windows 安装 Python 时，请确保把 python.exe 添加进了 Path，这样可以直接在命令行直接使用。

## 行为守则

作为一名贡献者，你可以帮助我们保持 Django 社区的开放和包含。请阅读和遵守我们的[Code of Conduct](https://www.djangoproject.com/conduct/)

## 安装 Git

本教程里，为了下载到最新的开发版 Django 和生成你修改过的补丁文件，你将需要安装 Git。

为了确认你是否安装了 Git，可以在命令行输入 git。如果提示说命令找不到，那你就需要下载安装了，[Git 下载页](https://git-scm.com/download)。

> **给 Windows 用户**
>
> 给 Windows 安装 Git 时，请确保给 “Git Bash” 打勾了，这样 Git 可以用它自己的 shell 运行。本教程假定你已经安装了它。

如果你对 Git 不太熟悉，你可以在命令行输入 **git help** 获取更多命令信息。

## 下载 Django 开发版的拷贝

第一步就是得到 Django 源码的拷贝。首先[在 GitHub 里 fork Django项目](https://github.com/django/django/fork)。然后在命令行里，使用 **cd** 切换到你想放 Django 本地拷贝的目录里。

使用下面的命令下载 Django 源码仓库：

```bash
$ git clone git@github.com:YourGitHubName/django.git
```

现在你有了一份 Django 的本地拷贝，你可以安装它，就像使用 **pip** 安装其他包那样。最方便的方式是使用 *虚拟环境（virtual environment）*（或者 virtualenv），这是 Python 内置的功能，允许您为每个项目单独设立已安装软件包的目录，让它们不会相互干扰。

最好是把你所以的虚拟环境（virtualenvs）都放在一个目录下，比如在你的 home 目录下的 **.virtualenvs/** 目录。如果还没创建：

```bash
$ mkdir ~/.virtualenvs
```

现在，运行以下命令创建新的虚拟环境（virtualenvs）：

```bash
$ python3 -m venv ~/.virtualenvs/djangodev
```

路径就是新的虚拟环境，而它会被保存在你的电脑里。

> **给 Windows 用户**
>
> 如果你在 Windows 下使用 Git 的 shell，使用内置的 **venv** 模块会无效，由于启动脚本是为系统 shell 创建的（**.bat**）和 PowerShell (**.ps1**)。使用 **virtualenv** 包代替 venv 模块：
> ```bash
> $ pip install virtualenv
> $ virtualenv ~/.virtualenvs/djangodev
> ```

> **给 Ubuntu 用户**
>
> 在一些 Ubuntu 版本中，上面的命令可能会失败。
> 
> 使用 **virtualenv** 包代替，确认你安装了 pip3：
> ```bash
> $ sudo apt-get install python3-pip
> # 如果下面的命令因为没有权限出错了，那就加上 sudo
> $ pip3 install virtualenv
> $ virtualenv --python=`which python3` ~/.virtualenvs/djangodev
> ```

最后一步就是让你的虚拟环境（virtualenvs）生效：

```bash
$ source ~/.virtualenvs/djangodev/bin/activate
```

如果 **source** 命令无效，你可以试试用一个 “点” 代替：

```bash
$ . ~/.virtualenvs/djangodev/bin/activate
```

> **给 Windows 用户**
>
> 为了在 Windows 上让虚拟环境（virtualenvs）生效，运行下面命令：
> ```bash
> $ source ~/virtualenvs/djangodev/Scripts/activate
> ```

无论什么时候，当你打开一个新的终端窗口时都要激活一下虚拟环境（virtualenvs）。为了方便这种情况，[virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/) 是很有用的工具。

从现在开始，你用 **pip** 安装的任何东西都会被安装进你新的的虚拟环境（virtualenvs），这会隔绝其他环境和系统包。同样，当前激活的虚拟环境（virtualenvs）的名字会在命令行前面显示，这可以让你知道使用的是哪个虚拟环境。

继续，安装之前克隆（clone）下来的 Django 拷贝：

```bash
$ pip install -e /path/to/your/local/clone/django/
```

安装的 Django 现在指向了你的本地拷贝。你将马上看到你做的任何修改，这对你编写第一个补丁很有帮助。

## 回滚到先前的 Django 版本

本教程里，我们将会使用 [#24788](https://code.djangoproject.com/ticket/24788) 任务（ticket）作为例子学习，所以在任务（ticket）补丁应用之前，我们将回退到 Django 的历史版本。这让我们可以走完所有步骤，包括从 scratch 中写编写补丁，还有运行 Django 的测试套件。

**请记住，为了下面的教程，我们将使用 Django 的主干旧版本，当你编写自己的补丁时，您应该始终使用 Django 当前的开发版本**

> **注意**
>
> 这个任务（ticket）的补丁已经由 Paweł Marczewski 编写了，也已经被应用到了 Django 中，[commit 4df7e8483b2679fc1cba3410f08960bac6f51115](https://github.com/django/django/commit/4df7e8483b2679fc1cba3410f08960bac6f51115)。因此，我们将使用的是 Django 先前的版本，[commit 4ccfc4439a7add24f8db4ef3960d02ef8ae09887](https://github.com/django/django/commit/4ccfc4439a7add24f8db4ef3960d02ef8ae09887)。

切换到 Django 的根目录（那里有 **django，docs，tests，AUTHORS** 等），你可以检查下这个旧版本 Django：

```bash
$ git checkout 4ccfc4439a7add24f8db4ef3960d02ef8ae09887
```

## 第一次运行 Django 的测试套件

这非常重要：当向 Django 贡献时，你修改的代码不会将 bug 引到 Django 的其他地方。一个检查 Django 还可以工作的方法就是在你修改之后运行 Django 的测试套件。如果所有的测试都通过了，你就有理由确认你的修改不会完全破坏 Django。如果你之前没有运行过测试套件，最好在这之前运行熟悉下它的输出大概是什么样的。

在运行之前，**cd** 切换到 Django 的 **tests/** 目录，安装它的依赖关系：

```bash
$ pip install -r requirements/py3.txt
```

如果你再安装过程中遇到错误，你的系统可能有一些 Python 包的依赖关系缺失。查询一下安装失败的包的文档，或者在网上搜索一下你遇到的这个错误信息。

现在我们准备好运行测试套件了。如果你用的是 GNU/Linux，macOS 或者其他 Unix 风格的系统，运行：

```bash
$ ./runtests.py
```

现在坐下来歇会。Django 整个测试套件有超过 9,600 个不同的测试，所以需要 5 到 15 分钟的时间运行，这取决于你电脑的运算速度。

当 Django 测试套件运行的时候，你可以看到一段显示着每个正在运行的测试的状态的字符流。**E** 表示在测试期间抛出了一个错误，**F** 表示一个测试的断言（assertions）失败。这两者都可以被认为测试失败了。同时，**x** 和 **s** 分别表示预期失败和跳过测试。“点” 表示通过测试。

跳过测试是由于运行测试时缺少额外的库；可以查看[运行所有测试](https://docs.djangoproject.com/en/1.11/internals/contributing/writing-code/unit-tests/#running-unit-tests-dependencies)所需要的一系列的依赖，确认和你修改的相关的一些测试依赖被安装了（本教程我们什么都不需要安装）。一些测试是特别针对特别的后台数据库的，如果不对那后台进行测试，就会被跳过。SQLite 是默认设置的后台数据库。如果是使用其他的后台数据库，为了运行测试，可以查看[使用其他设置模块](https://docs.djangoproject.com/en/1.11/internals/contributing/writing-code/unit-tests/#running-unit-tests-settings)。

一旦测试完毕，你应该会收到一条关于测试套件是通过了还是失败了的信息。由于你还没有对 Django 的代码做修改，所以整个测试套件 **应该是（should）** 通过。如果你得到的是失败或者错误的信息，请确保你之前走的所有步骤都正确。可以查看[运行单元测试](https://docs.djangoproject.com/en/1.11/internals/contributing/writing-code/unit-tests/#running-unit-tests)获得更多信息。如果你正在使用 Python3.5+，将会有些与不被赞成的警告（deprecation warnings）相关的失败，那些你可以忽略掉。这些失败已经被 Django 修复了。

注意，最新的 Django 主干（trunk）版本不总是稳定的。当开发版本遇上主干（trunk）版本，你可以检查 [Django 的持续集成构建（Django’s continuous integration builds）](https://djangoci.com)来确认是特别针对你的机器的失败还是已经存在在 Django 官方构建里的。如果你点击去看一个特别的构建，你可以看到 “配置矩阵（Configuration Matrix）” 显示着由于 Python 版本和数据库后台问题出现的失败。

> **注意**
>
> 在本教程和我们正在工作的任务（ticket）里，测试使用 SQLite 足够了，但是，[运行其他数据库进行测试](https://docs.djangoproject.com/en/1.11/internals/contributing/writing-code/unit-tests/#running-unit-tests-settings)是可能的（有时是必要的）。

## 为你的补丁创建一个分支

在修改之前，先为任务（ticket）创建一个新的分支：

```bash
$ git checkout -b ticket_24788
```

你可以为这个分支选择任何你想要的名字，例如 “ticket_24788”。在这个分支做的所有修改都是特别针对这个任务的，并不会影响到我们先前克隆下来的主要代码。

## 为你的任务编写一些测试

在大多数情况下，被 Django 接收进来的补丁都必须测试过。针对 bug 修复的补丁，这意味着要编写回退测试代码来确保 bug 不会在 Django 的后续版本再次出现。一个回退测试应该是这样的：当 bug 存在时会失败，当 bug 被修复时会通过。对于包含新特性的补丁，你的测试需要能保证新特性是能正常工作的。当新特性不存在是，它们也是要失败的；一旦被应用又是能通过的。

一个好方法是：首先在修改代码前，编写你的新测试。这种开发风格被称为[测试驱动开发（test-driven development）](https://en.wikipedia.org/wiki/Test-driven_development)，而且既能被应用在整个项目又能应用在单个补丁。在编写完你的测试后，运行一下它们以确保它们确实是失败的（由于你还没有修复 bug 或者新增特性）。如果你的新测试没有失败，你就需要修复一下这些测试好让它们失败。毕竟，无论 bug 是否存在，回退测试都会通过，那在防止 bug 重新出现这事上是非常没有帮助的。

现在让我们动手做下实例。

## 为任务 #24788 编写一些测试

任务 #24788 推荐加点小功能：给 Form 类指定类级别（class level）属性加 **前缀（prefix）**的功能：

```text
[…] forms which ship with apps could effectively namespace themselves such
that N overlapping form fields could be POSTed at once and resolved to the
correct form.
```

为了解决这个任务，我们将会为 **BaseForm** 类添加前缀属性。当实例化这个类时，传递一个前缀给 **__init__()** 方法也将会给被创建的实例设置这个前缀。但如果不传递一个前缀（或者传递 **None**）将会使用类级别的前缀。在我们修改之前，我们准备编写一些测试来验证我们的修改函数没有问题，在将来也没有问题。

切换到 Django 的 **tests/forms_tests/tests/** 文件夹里，打开 **test_forms.py** 文件。在第 1674 行 **test_forms_with_null_boolean** 函数之前添加以下代码：

```python3
# tests/forms_tests/tests/test_forms.py

def test_class_prefix(self):
    # 前缀也可以在类级别指定
    class Person(Form):
        first_name = CharField()
        prefix = 'foo'

    p = Person()
    self.assertEqual(p.prefix, 'foo')

    p = Person(prefix='bar')
    self.assertEqual(p.prefix, 'bar')
```

这个新的测试：检查 [设置一个类级别前缀] 是否如预期一样地工作；并且在创建实例时传递一个 **前缀（prefix）**参数，看是否也工作。

> **但这个测试的东西看起来特别困难……**
>
> 