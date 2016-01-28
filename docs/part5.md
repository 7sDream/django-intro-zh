暂未翻译

官方文档[点此](https://docs.djangoproject.com/en/1.8/intro/tutorial05/)

---

# 创建你的第一个 Django 项目， 第五部分

这一篇从[第四部分（en）](part4.md)结尾的地方继续讲起。我们在前几章成功的构建了一个在线投票应用，在这一部分里我们将其创建一些自动化测试。

## 自动化测试简介

### 自动化测试是什么？

测试，是用来检查代码正确性的一些简单的程序。

测试在不同的层次中都存在。有些测试只关注某个很小的细节（某个模型的某个方法的返回值是否满足预期？），而另一些测试可能检查对莫个软件的一系列操作（某一用户输入序列是否造成了预期的结果？）。其实这和我们在教程的[第一部分(zh)](part1.md)里做的并没有什么不同，我们使用 **[shell][shell]** 来测试某一方法的功能，或者运行某个应用并输入数据来检查它的行为。

真正不同的地方在于，自动化测试是由某个系统帮你自动完成的。当你创建好了一系列测试，每次修改应用代码后，就可以自动检查出修改后的代码是否还像你曾经预期的那样正常工作。你不需要话费大量时间来进行手动测试。

### 为什么你需要写测试

但是，为什么需要测试呢？又为什么是现在呢？

你可能觉得学 Python/Django 对你来说已经很充实了，再学一些新东西的话看起来有点负担过重并且没什么必要。毕竟，我们的投票应用看起来已经完美工作了。写一些自动测试并不能让它工作的更好。如果写一个投票应用是你想用 Django 完成的唯一工作，那你确实没必要学写测试。但是如果你还想写更复杂的项目，现在就是学习测试写法的最好时机了。

### 测试将节约你的时间

在某种程度上，能够「判断出代码是否正常工作」的测试，就称得上是个令人满意的了。

在更加复杂的应用中，各种组件之间的交互可能会及其的复杂。改变其中某一组件的行为，也有可能会造成意想不到的结果。判断「代码是否正常工作」意味着你需要用大量的数据来完整的测试全部代码的功能，以确保你的小修改没有对应用整体造成破坏——这太费时间了。

尤其是当你发现自动化测试能在几秒钟之内帮你完成这件事时，就更会觉得手动测试实在是太浪费时间了。当某人写出错误的代码时，自动化测试还能帮助你定位错误代码的位置。

有时候你会觉得，和富有创造性和生产力的业务代码比起来，编写枯燥的测试代码实在是太无聊了，特别是当你知道你的代码完全没有问题的时候。

然而，编写测试还是要比花费几个小时手动测试你的应用，或者为了找到某个小错误而胡乱翻看代码要有意义的多。

### 测试不仅能发现错误，而且能预防错误

「测试是开发的对立面」，这种思想是不对的。

如果没有测试，整个应用的行为意图会变得更加的不清晰。甚至当你在看自己写的代码时也是这样，有时候你需要仔细研读一段代码才能搞清楚它有什么用。

而测试的出现改变了这种情况。测试就好像是从内部仔细检查你的代码，当有些地方出错时，这些地方将会变得很显眼——就算你自己没有意识到那里写错了。

### 测试使你的代码更加诱人

你也许遇到过这种情况：你编写了一个绝赞的软件，但是其他开发者看都不看它一眼，因为它缺少测试。没有测试的代码不值得信任。 Django 最初开发者之一的 Jacob Kaplan-Moss 说过：“项目规划时没有包含测试是不科学的。”

其他的开发者希望在正式使用你的代码前看到它通过了测试，这是你需要写测试的另一个重要原因。

### 测试有利于团队协作

前面的几点都是从单人开发的角度来说的。复杂的应用可能由团队维护。测试的存在保证了协作者不会不小心破坏了了你的代码（也保证你不会不小心弄坏他们的）。如果你想作为一个 Django 程序员谋生的话，你必须擅长编写测试！

## 基础测试策略

测试有几种不同的应用方法。

一些开发者遵循「测试驱动」的开发原则，他们在写代码之前先写测试。这种方法看起来有点反直觉，但事实上，这和大多数人日常的做法是相吻合的。我们会先描述一个问题，然后写代码来解决它。「测试驱动」的开发方法只是将问题的描述抽象为了 Python 的测试样例。

更普遍的情况是，一个刚接触自动化测试的新手更倾向于先写代码，然后再写测试。虽然提前写测试可能更好，但是晚点写起码也比没有强。

有时候很难决定从测试该哪里开始下手。如果你已经写了几千行 Python 代码了，选择从哪里开始写测试确实不怎么简单。如果是这种情况，那么在你下次修改代码（比如加新功能，或者修复 Bug）之前写个测试是比较合理且有效的。

所以，我们现在就开始写吧。

## 第一个测试

### 首先得有个 Bug

幸运的是，我们的投票应用现在就有一个小 Bug 需要被修复：我们的要求是如果 **Question** 是在一天之内发布的，**Question.was_published_recently()** 方法将会返回 **True**，然而现在这个方法在 **Question** 的 **pub_date** 字段比当前时间还晚时也会返回 **True**（这是个 Bug）。

你能从管理页面确认这个 Bug。创建一个发布日期是将来的投票，在投票列表里你会看到它被标明为最近发布（published recently）。

也可以从 **[shell][shell]** 里确认 Bug：

```pycon
>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
>>> # 创建一个 30 天后发布的投票
>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))
>>> # 检查它是否是最近发布的
>>> future_question.was_published_recently()
True
```

因为将来发生的是肯定不是最近发生的，所以代码明显是错误的。

### 写个测试来发现 Bug

我们刚刚在 **[shell][shell]** 里做的测试也就是自动化测试应该做的工作。所以我们来把它改写成自动化的吧。

按照惯例，Django 应用的测试因该卸载应用的 **test.py** 文件里。测试系统会自动的在所有以 **test** 开头的文件里寻找并执行测试代码。

在 **poll** 应用的 **test.py** 文件里输入以下代码：

```python
# polls/test.py

import datetime

from django.utils import timezone
from django.test import TestCase

from .models import Question


class QuestionMethodTests(TestCase):

    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() 应该对 pub_date 字段值是将来的那些问题返回 False。
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertEqual(future_question.was_published_recently(), False)
```

我们创建了一个 **[django.test.TestCase][TestCase]** 的子类，并添加了一个方法。在此方法中我们创建了一个 **pub_date** 字段值在将来的 **Question** 实例，然后检查它的 **was_published_recently()** 方法的返回值——它应该是 False。

### 运行测试

在终端中，我们通过输入以下代码运行测试：

```sh
$ python manage.py test polls
```

你将会看到运行结果：

```
Creating test database for alias 'default'...
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionMethodTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/path/to/mysite/polls/tests.py", line 16, in test_was_published_recently_with_future_question
    self.assertEqual(future_question.was_published_recently(), False)
AssertionError: True != False

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

发生了什么呢？以下是自动化测试的运行过程：

- **`python manage.py test polls`** 将会寻找 **poll** 应用里的测试代码
- 它找到了一个 **[django.test.TestCase][TestCase]** 的子类
- 它创建一个特殊的数据库供测试使用
- 它在类中寻找测试方法——以 **test** 开头的方法。
- 在 **test_was_published_recently_with_future_question** 方法中，它创建了一个 **pub_date** 值为未来第 30 天的 **Question** 实例。
- 然后使用 **assertEqual()** 方法，发现 **was_published_recently()** 返回了 **True**，而我们希望它返回 **False**

测试系统通知我们哪些测试样例失败了，和造成测试失败的代码所在的行号。

### 修复 Bug

我们现在知道了，问题出在当 **pub_date** 为将来时， **Question.was_published_recently()** 应该返回 **False**。我们去修改 models.py 里的方法，让它只在日期是过去的时候才返回 **True**：

```python
# polls/model.py

def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

然后我们重新运行测试：

```
Creating test database for alias 'default'...
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
Destroying test database for alias 'default'...
```

在出现 Bug 之后，我们编写了能够发现这个 Bug 的自动化测试。在修复 Bug 之后，我们的代码顺利的通过了测试。

将来，我们的应用可能会出现其他的问题，但是我们可以肯定的是，一定不会再次出现这个 Bug，因为只要简单的运行一遍测试，就会立刻收到警告。我们可以认为应用的这一小部分代码永远是安全的。

### 更全面的测试

我们已经搞定一小部分了，现在可以考虑全面的测试 **was_published_recently()** 这个方法以确定它的安全性，然后就可以把这个方法稳定下来了。事实上，在修复一个 Bug 时不小心引入另一个 Bug 会是非常令人尴尬的。

我们在上次写的类里再增加两个测试，来更全面的测试这个方法：

```python
# polls/test.py

def test_was_published_recently_with_old_question(self):
    """
    对于 pub_date 在一天以前的 Question，was_published_recently() 应该返回 False。
    """
    time = timezone.now() - datetime.timedelta(days=30)
    old_question = Question(pub_date=time)
    self.assertEqual(old_question.was_published_recently(), False)

def test_was_published_recently_with_recent_question(self):
    """
    对于 pub_date 在一天之内的 Question，was_published_recently() 应该返回 True。
    """
    time = timezone.now() - datetime.timedelta(hours=1)
    recent_question = Question(pub_date=time)
    self.assertEqual(recent_question.was_published_recently(), True)
```

现在，我们有三个测试来确保 **Question.was_published_recently()** 方法对于过去，最近，和将来的三种情况都返回正确的值。

再次申明，尽管 **polls** 现在是个非常简单的应用，但是无论它以后成长到多么复杂，要和其他代码进行怎样的交互，我们都能保证进行过测试的那些方法的行为永远是符合预期的。

## 测试视图

我们的投票应用对所有问题都一视同仁：它将会发布所有的问题，也包括那些 **pub_date** 字段值是未来的问题。我们应该改善这一点。将 **pub_date** 设置为将来应该被解释为这个问题将在所填写的时间点才被发布，而在之前是不可见的。

### 针对视图的测试

为了修复上述 Bug ，我们这次先编写测试，然后再去改代码。事实上，这是一个「测试驱动」开发模式的实例，但其实这两者的顺序不太重要。

在我们的第一个测试中，我们关注代码的内部行为。我们通过假装有用户使用浏览器访问被测试的应用来检查代码行为是否符合预期。

在我们动手之前，先看看需要用到的工具们。

### Django 测试工具之 Client

Django 提供了一个供测试使用的 **[Client][Client]** 来模拟用户和视图层代码的交互。我们能在 **test.py** 甚至是 **[shell][shell]** 中使用它。

我们依照惯例从 **[shell][shell]** 开始，首先我们要做一些在 **test.py** 里并不需要的准备工作。第一步是在 **[shell][shell]** 中配置测试环境：

```pycon
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
```

[setup_test_environment()](https://docs.djangoproject.com/en/1.8/topics/testing/advanced/#django.test.utils.setup_test_environment) 安装了一个特殊的模板渲染器，它能让我们使用 response 的一些在正常情况下不可用的附加属性，比如 **response.context**。注意，这个方法并不会配置测试数据库，所以接下来的代码将会当前存在的数据库上运行，输出的内容可能由于数据库内容的不同而不同。

然后我们需要导入 client 类（在后续 **test.py** 的实例中我们将会使用 **[django.test.TestCase][TestCase]** 类，这个类里包含了自己的 client 实例，所以不需要这一步）

```pycon
>>> from django.test import Client
>>> # 创建一个 Client 实例
>>> client = Client()
```

搞定了之后，我们可以要求 client 来为我们工作了：

```pycon
>>> # 获取 '/' 的响应
>>> response = client.get('/')
>>> # 访问这个地址应该会得到一个 404 状态码
>>> response.status_code
404
>>> # 访问 '/polls/' 的话，我们应该会得到一些有意义的响应
>>> # 我们使用 'reverse()' 方法而不是硬编码的 URL
>>> from django.core.urlresolvers import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
b'\n\n\n    <p>No polls are available.</p>\n\n'
>>> # 注意 - 如果你的 'settings.py' 里的 'TIME_ZONE' 设置的
>>> # 不正确的话，有可能得到不同的结果。如果你修改了它，则需要
>>> # 重新启动你的 shell 会话才会生效。
>>> from polls.models import Question
>>> from django.utils import timezone
>>> # 创建一个 Question 并保存
>>> q = Question(question_text="Who is your favorite Beatle?", pub_date=timezone.now())
>>> q.save()
>>> # 再次查看响应
>>> response = client.get('/polls/')
>>> response.content
b'\n\n\n    <ul>\n    \n        <li><a href="/polls/1/">Who is your favorite Beatle?</a></li>\n    \n    </ul>\n\n'
>>> # 如果下面的代码无法使用，那么你可能是没有执行我们之前说的
>>> # setup_test_environment() 方法
>>> response.context['latest_question_list']
[<Question: Who is your favorite Beatle?>]
```

### 改善视图代码

现在的投票列表会显示将来的投票（就是那些 **pub_date** 值是将来的问题）。我们来修复这个问题。

在教程的[第四部分(en)](part4.md)里，我们介绍了继承于 **[ListView](https://docs.djangoproject.com/en/1.8/ref/class-based-views/generic-display/#django.views.generic.list.ListView)** 的视图类：

```python
# polls/views.py

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """返回最近发布的五个投票"""
        return Question.objects.order_by('-pub_date')[:5]
```



[shell]: https://docs.djangoproject.com/en/1.8/ref/django-admin/#django-admin-shell
[TestCase]: https://docs.djangoproject.com/en/1.8/topics/testing/tools/#django.test.TestCase
[Client]: https://docs.djangoproject.com/en/1.8/topics/testing/tools/#django.test.Client
