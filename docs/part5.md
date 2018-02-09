# 创建你的第一个 Django 项目， 第五部分

这一篇从 [第四部分（zh）](part4.md)结尾的地方继续讲起。我们在前几章成功的构建了一个在线投票应用，本章我们将为其创建一些自动化测试。

## 自动化测试简介

### **自动化测试是什么？**

测试，是用来检查代码正确性的一些简单的程序。

测试在不同的层次中都存在。有些测试只关注某个很小的细节（某个模型的某个方法的返回值是否满足预期？），而另一些测试可能检查对某个软件的一系列操作（某一用户输入序列是否造成了预期的结果？）。其实这和我们在教程的 [第二部分(zh)](part2.md)里做的并没有什么不同，我们使用 [**shell**][shell] 来测试某一方法的功能，或者运行某个应用并输入数据来检查它的行为。

真正不同的地方在于，自动化测试是由某个系统帮你自动完成的。当你创建好了一系列测试，每次修改应用代码后，就可以自动检查出修改后的代码是否还像你曾经预期的那样正常工作。你不需要花费大量时间来进行手动测试。

### **为什么你需要写测试**

但是，为什么需要测试呢？又为什么是现在呢？

你可能觉得学 Python/Django 对你来说已经很充实了，再学一些新东西的话看起来有点负担过重并且没什么必要。毕竟，我们的投票应用看起来已经完美工作了。写一些自动测试并不能让它工作的更好。如果写一个投票应用是你想用 Django 完成的唯一工作，那你确实没必要学写测试。但是如果你还想写更复杂的项目，现在就是学习测试写法的最好时机了。

#### **测试将节约你的时间**

在某种程度上，能够「判断出代码是否正常工作」的测试，就称得上是个令人满意的了。

在更加复杂的应用中，各种组件之间的交互可能会及其的复杂。改变其中某一组件的行为，也有可能会造成意想不到的结果。判断「代码是否正常工作」意味着你需要用大量的数据来完整的测试全部代码的功能，以确保你的小修改没有对应用整体造成破坏——这太费时间了。

尤其是当你发现自动化测试能在几秒钟之内帮你完成这件事时，就更会觉得手动测试实在是太浪费时间了。当某人写出错误的代码时，自动化测试还能帮助你定位错误代码的位置。

有时候你会觉得，和富有创造性和生产力的业务代码比起来，编写枯燥的测试代码实在是太无聊了，特别是当你知道你的代码完全没有问题的时候。

然而，编写测试还是要比花费几个小时手动测试你的应用，或者为了找到某个小错误而胡乱翻看代码要有意义的多。

#### **测试不仅能发现错误，而且能预防错误**

「测试是开发的对立面」，这种思想是不对的。

如果没有测试，整个应用的行为意图会变得更加的不清晰。甚至当你在看自己写的代码时也是这样，有时候你需要仔细研读一段代码才能搞清楚它有什么用。

而测试的出现改变了这种情况。测试就好像是从内部仔细检查你的代码，当有些地方出错时，这些地方将会变得很显眼——就算你自己没有意识到那里写错了。

#### **测试使你的代码更有吸引力**

你也许遇到过这种情况：你编写了一个绝赞的软件，但是其他开发者看都不看它一眼，因为它缺少测试。没有测试的代码不值得信任。 Django 最初开发者之一的 Jacob Kaplan-Moss 说过：“项目规划时没有包含测试是不科学的。”

其他的开发者希望在正式使用你的代码前看到它通过了测试，这是你需要写测试的另一个重要原因。

#### **测试有利于团队协作**

前面的几点都是从单人开发的角度来说的。复杂的应用可能由团队维护。测试的存在保证了协作者不会不小心破坏了了你的代码（也保证你不会不小心弄坏他们的）。如果你想作为一个 Django 程序员谋生的话，你必须擅长编写测试！

## **基本测试策略**

测试有几种不同的应用方法。

一些开发者遵循 [测试驱动开发（test-driven development）](https://en.wikipedia.org/wiki/Test-driven_development)的原则，他们在写代码之前先写测试。这种方法看起来有点反直觉，但事实上，这和大多数人日常的做法是相吻合的。我们会先描述一个问题，然后写代码来解决它。「测试驱动」的开发方法只是将问题的描述抽象为了 Python 的测试样例。

更普遍的情况是，一个刚接触自动化测试的新手更倾向于先写代码，然后再写测试。虽然提前写测试可能更好，但是晚点写起码也比没有强。

有时候很难决定从测试该哪里开始下手。如果你已经写了几千行 Python 代码了，选择从哪里开始写测试确实不怎么简单。如果是这种情况，那么在你下次修改代码（比如加新功能，或者修复 Bug）之前写个测试是比较合理且有效的。

所以，我们现在就开始写吧。

## 第一个测试

### 首先得有个 Bug

幸运的是，我们的投票（**polls**）应用现在就有一个小 Bug 需要被修复：我们的要求是如果 **Question** 是在一天之内发布的，**Question.was_published_recently()** 方法将会返回 **True**，然而现在这个方法在 **Question** 的 **pub_date** 字段比当前时间还晚时也会返回 **True**（这是个 Bug）。

你能从管理页面确认这个 Bug。创建一个发布日期是将来的投票，在投票列表里你会看到它被标明为最近发布（published recently）。

也可以从 [**shell**][shell] 里确认 Bug：

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

我们刚刚在 [**shell**][shell] 里做的测试也就是自动化测试应该做的工作。所以我们来把它改写成自动化的吧。

按照惯例，Django 应用的测试因该写在应用的 **tests.py** 文件里。测试系统会自动的在所有以 **test** 开头的文件里寻找并执行测试代码。

在 **polls** 应用的 **tests.py** 文件里输入以下代码：

```python
# polls/tests.py

import datetime

from django.utils import timezone
from django.test import TestCase

from .models import Question


class QuestionModelTests(TestCase):

    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() 应该对 pub_date 字段值是将来的那些问题返回 False。
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
```

我们创建了一个 **[django.test.TestCase][TestCase]** 的子类，并添加了一个方法。在此方法中我们创建了一个 **pub_date** 字段值在将来的 **Question** 实例，然后检查它的 **was_published_recently()** 方法的返回值——它应该是 False。

### 运行测试

在终端中，我们通过输入以下代码运行测试：

```bash
$ python manage.py test polls
```

你将会看到运行结果：

```bash
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionModelTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/path/to/mysite/polls/tests.py", line 16, in test_was_published_recently_with_future_question
    self.assertIs(future_question.was_published_recently(), False)
AssertionError: True is not False

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

发生了什么呢？以下是自动化测试的运行过程：

- **python manage.py test polls** 将会寻找 **poll** 应用里的测试代码
- 它找到了一个 [**django.test.TestCase**][TestCase] 的子类
- 它创建一个特殊的数据库供测试使用
- 它在类中寻找测试方法——以 **test** 开头的方法。
- 在 **test_was_published_recently_with_future_question** 方法中，它创建了一个 **pub_date** 值为未来第 30 天的 **Question** 实例。
- 然后使用 **assertIs()** 方法，发现 **was_published_recently()** 返回了 **True**，而我们希望它返回 **False**

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
System check identified no issues (0 silenced).
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
# polls/tests.py

def test_was_published_recently_with_old_question(self):
    """
    对于 pub_date 在一天以前的 Question，was_published_recently() 应该返回 False。
    """
    time = timezone.now() - datetime.timedelta(days=1, seconds=1)
    old_question = Question(pub_date=time)
    self.assertIs(old_question.was_published_recently(), False)

def test_was_published_recently_with_recent_question(self):
    """
    对于 pub_date 在一天之内的 Question，was_published_recently() 应该返回 True。
    """
    time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
    recent_question = Question(pub_date=time)
    self.assertIs(recent_question.was_published_recently(), True)
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

Django 提供了一个供测试使用的 [**Client**][Client] 来模拟用户和视图层代码的交互。我们能在 **tests.py** 甚至是 [**shell**][shell] 中使用它。

我们依照惯例从 [**shell**][shell] 开始，首先我们要做一些在 **tests.py** 里并不需要的准备工作。第一步是在 [**shell**][shell] 中配置测试环境：

```pycon
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
```

[**setup_test_environment()**][setup_test_environment] 安装了一个特殊的模板渲染器，它能让我们使用 response 的一些在正常情况下不可用的附加属性，比如 **response.context**。注意，这个方法并不会配置测试数据库，所以接下来的代码将会当前存在的数据库上运行，输出的内容可能由于数据库内容的不同而不同。如果你在 **settings.py** 中的 **TIME_ZONE** 设置不对，你还有可能得到意想不到的结果。如果你不记得之前是否设置过，那在继续下面的步骤之前先检查一下。

然后我们需要导入 client 类（在后续 **tests.py** 的实例中我们将会使用 [**django.test.TestCase**][TestCase] 类，这个类里包含了自己的 client 实例，所以不需要这一步）

```pycon
>>> from django.test import Client
>>> # 创建一个 Client 实例
>>> client = Client()
```

搞定了之后，我们可以要求 client 来为我们工作了：

```pycon
>>> # 获取 '/' 的响应
>>> response = client.get('/')
Not Found: /
>>> # 我们期望返回一个 404；如果你看到一个
>>> # “无效 HTTP_HOST_header” 错误 和 一个 400 响应，那你可能
>>> # 忘记了这之前要调用的 setup_test_environment()
>>> response.status_code
404
>>> # 访问 '/polls/' 的话，我们应该会得到一些有意义的响应
>>> # 我们使用 'reverse()' 方法而不是硬编码的 URL
>>> from django.urls import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
b'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#39;s up?</a></li>\n    \n    </ul>\n\n'
>>> response.context['latest_question_list']
<QuerySet [<Question: What's up?>]>
```

### 改善视图代码

现在的投票列表会显示将来的投票（**pub_date** 值是将来的那些）。我们来修复这个问题。

在教程的 [第四部分（zh）](part4.md)里，我们介绍了基于 [**ListView**][ListView] 的视图类：

```python
# polls/views.py

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """返回最近发布的五个投票"""
        return Question.objects.order_by('-pub_date')[:5]
```

我们需要改进 **get_queryset()** 方法，让他它能通过将 Question 的 pub_data 属性与 **timezone.now()** 相比较来判断是否应该显示此 Question。首先我们需要一行 import 语句：

```python
# polls/views.py

from django.utils import timezone
```

然后我们把 **get_queryset** 方法改写成下面这样：

```python
# polls/views.py

def get_queryset(self):
    """
    返回最近发布的五个投票（不包括那些被设置为在将来发布的）
    """
    return Question.objects.filter(
        pub_date__lte=timezone.now()
    ).order_by('-pub_date')[:5]
```

**Question.objects.filter(pub_date__lte=timezone.now())** 返回一个由 **pub_date** 小于或等于（也就是早于或等于） **timezone.now** 的 **Question** 组成的集合。

### 测试新视图

现在你可以通过创建一个将来发布的投票问题，然后执行 runserver，再在浏览器中检查主页里的列表来判断代码是否符合预期。你绝对不会愿意每次修改代码后都这么来一次的，所以让我们创建一些自动化测试吧。

在 **polls/tests.py** 里加入一句 import

```python
# polls/tests.py

from django.urls import reverse
```

然后我们写一个公用的快捷函数用于创建投票问题，再为视图创建一个测试类：

```python
# polls/test.py

def create_question(question_text, days):
    """
    创建一个以 question_text 为标题，pub_date 为 days 天之后的问题。
    days 为正表示将来，为负表示过去。
    """
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)


class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        """
        如果数据库里没有保存问题，应该显示一个合适的提示信息。
        """
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_past_question(self):
        """
        值 pub_date 是过去的，问题应该被显示在主页上。
        """
        create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_future_question(self):
        """
        值 pub_date 是将来的，问题不应该被显示在主页上。
        """
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])

    def test_future_question_and_past_question(self):
        """
        如果数据库里同时存有过去和将来的投票，那么只应该显示过去那些。
        """
        create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )

    def test_two_past_questions(self):
        """
        问题索引页应该可以显示多个问题。
        """
        create_question(question_text="Past question 1.", days=-30)
        create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question 2.>', '<Question: Past question 1.>']
        )
```

我们仔细分析一下上面的代码。

首先是一个快捷函数 **create_question**，它封装了创建问题（questions）的流程，减少了重复代码。

**test_no_questions** 方法里没有创建任何投票，它检查返回的网页上有没有 “No polls are available.” 这段消息和 **latest_question_list** 是否为空。注意到 [**django.test.TestCase**][TestCase] 类提供了一些额外的 assert 方法，在这个例子中，我们使用了 [**assertContains()**][assertContains] 和 [**assertQuerysetEqual()**][assertQuerysetEqual]。

在 **test_past_question** 方法中，我们创建了一个投票并检查它是否出现在列表中。

在 **test_future_questionn** 方法中，我们创建了一个 **pub_date** 在将来的投票。数据库会在每次调用测试方法之前被重置，所以第一个方法里创建的投票已经没了，所以此时我们希望看到的是一个没有任何投票的目录页。

剩下的那些也都差不多。实际上，测试就是假装一些管理员的输入，然后通过用户端的表现是否符合预期来判断新加入的改变是否破坏了原有的系统状态。

### 测试 DetailView

我们的工作似乎已经很完美了？不，还有一个问题：就算在将来发布的那些投票不会在目录页里出现，但是用户还是能够通过猜测 URL 的方式访问到他们。所以我们得在 DetailView 里增加一些约束：

```python
# polls/views.py

class DetailView(generic.DetailView):
    ...
    def get_queryset(self):
        """
        过滤掉现在不应该被发布的投票。
        """
        return Question.objects.filter(pub_date__lte=timezone.now())
```

当然，我们还要增加一些测试，用于确认过去的 **问题（Question ）**能被用户访问，而将来的则不能：

```python
# polls/tests.py

class QuestionDetailViewTests(TestCase):
    def test_future_question(self):
        """
        访问将来发布的问题详情页应该会收到一个 404 错误。
        """
        future_question = create_question(question_text='Future question.', days=5)
        url = reverse('polls:detail', args=(future_question.id,))
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)

    def test_past_question(self):
        """
        访问过去发布的问题详情页，页面上应该显示问题描述。
        """
        past_question = create_question(question_text='Past Question.', days=-5)
        url = reverse('polls:detail', args=(past_question.id,))
        response = self.client.get(url)
        self.assertContains(response, past_question.question_text)
```

### 更多的测试

我们应该给 **ResultsView** 也增加一个类似的 **get_queryset** 方法，并且为它创建测试。这和我们之前干的差不多，事实上，基本就是重复一遍。

我们还可以从各个方面改进应用，但是测试会一直伴随我们。比方说，在目录页上显示一个没有选项（**Choices**）的问题就没什么意义。我们可以检查并排除这样的 **问题（Questions）**。测试里则可以创建一个没有选项的问题，然后检查它是否被显示在目录上。当然也要创建一个有选项的投票，然后确认它确实被显示了。

恩，也许你想让登录的管理员能在目录上够看见未被发布的那些问题，但是其他的用户看不到。不管怎么说，如果你想要增加一个新功能，那么同时一定要为它编写测试。不过你是先写代码还是先写测试那就随你了。

在未来的某个时刻，你一定会去查看测试代码，然后开始怀疑：「这么多的测试不会使代码越来越复杂吗？」。别着急，我们马上就会谈到这一点。

## 测试那是越多越好

貌似我们的测试多的快要失去控制了。按照这样发展下去，测试代码就要变得比应用的实际代码还要多了。而且测试代码大多都是重复且不优雅的，特别是在和业务代码比起来的时候，这种感觉更加明显。

但是这没关系！就让测试代码继续肆意增长吧。大部分情况下，你写完一个测试之后就可以忘掉它了。在你继续开发的过程中，它会一直默默无闻地为你做贡献的。

但有时测试也需要更新。想象一下如果我们真的想让目录只显示有选项的那些投票，那么只前写的很多测试就都会失败。但是 *这也明确地告诉了我们哪些测试需要被更新*，所以增加的测试会自行测试向前的兼容性。

最坏的情况是，当你继续开发的时候，发现之前的一些测试现在看来是多余的。但是这也不是什么问题，冗余的测试也是件好事。

如果你对测试有个整体规划，那么它们就几乎不会变得混乱。下面有几条好的建议：

- 对于每个模型和视图都建立单独的测试类
- 每个测试方法之测试一个功能
- 给每个测试方法起个能描述其功能的名字

## 进一步测试

在本教程中，我们仅仅是了解了测试的基础知识。你能做的还有很多，而且世界上有很多有用的工具来帮你完成这些有意义的事。

举个例子，在我们上述的测试中，已经从代码逻辑和视图响应的角度检查了应用的输出，现在你可以从一个更加用户的角度来检查最终渲染出的 HTML 是否符合预期，使用 [Selenium](http://seleniumhq.org/) 可以很轻松的完成这件事。这个工具不仅可以测试 Django 框架里的代码，还可以检查其他部分，比如说你的 JavaScript。它假装成是一个正在和你站点进行交互的浏览器，就好像有个真人在访问网站一样！Django 它提供了 [**LiveServerTestCase**][LiveServerTestCase] 来和 Selenium 这样的工具进行交互。

如果你在开发一个很复杂的应用的话，你也许想在每次提交代码时自动运行测试，也就是我们所说的[持续整合](https://en.wikipedia.org/wiki/Continuous_integration)，这样的话就实现质量控制的自动化，起码是部分自动化。

一个找出代码中未被测试部分的方法是检查代码覆盖率。它有助于找出代码中的薄弱部分和无用部分。如果你无法测试一段代码，通常说明这段代码需要被重构或者删除。想知道代码覆盖率和无用代码的详细信息，请看文档 [和 coverage.py 结合使用][topics-testing-code-coverage]。

## 然后呢？

如果你想深入了解测试，就去看 [在 Django 中测试][testing]。

当你已经比较熟悉该如何测试 Django 的视图之后，就可以继续于读 [教程第六部分（zh）](part6.md)，来学习关于静态文件管理的相关知识。


[topics-testing-code-coverage]: https://docs.djangoproject.com/en/2.0/topics/testing/advanced/#topics-testing-code-coverage
[shell]: https://docs.djangoproject.com/en/2.0/ref/django-admin/#django-admin-shell
[TestCase]: https://docs.djangoproject.com/en/2.0/topics/testing/tools/#django.test.TestCase
[Client]: https://docs.djangoproject.com/en/2.0/topics/testing/tools/#django.test.Client
[setup_test_environment]: https://docs.djangoproject.com/en/2.0/topics/testing/advanced/#django.test.utils.setup_test_environment
[assertContains]: https://docs.djangoproject.com/en/2.0/topics/testing/tools/#django.test.SimpleTestCase.assertContains
[assertQuerysetEqual]: https://docs.djangoproject.com/en/2.0/topics/testing/tools/#django.test.TransactionTestCase.assertQuerysetEqual
[LiveServerTestCase]: https://docs.djangoproject.com/en/2.0/topics/testing/tools/#django.test.LiveServerTestCase
[ListView]: https://docs.djangoproject.com/en/2.0/ref/class-based-views/generic-display/#django.views.generic.list.ListView
[testing]: https://docs.djangoproject.com/en/2.0/topics/testing/
