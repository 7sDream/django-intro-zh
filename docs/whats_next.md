# 接下来如何学习？

看来您已经阅读完所有的[介绍文档](https://docs.djangoproject.com/en/1.8/intro/)（introductory material），并且决定继续使用Django。但是我们前面只是进行了概括性的介绍（事实上，即使您全部浏览完，也只是涉及了所有文档5%左右的内容）。

所以接下来是？

没错，我们一直专注于通过实践来提升自身能力。基于这一点考虑，您应该已经掌握足够的知识，可以直接开始自己的个人项目。当您需要相关帮助时，届时再查询文档。

我们倾注了大量精力来使Django官方文档更加实用，易于阅读和尽可能地完备。本文其他部分更多的是关于Django官方文档的使用方法，这样您可以最有效地利用它。

（是的，这篇文档是关于如何使用Django官方文档，请放心我们不会再为此撰写一份新文档。）

## 如何查询文档

Django拥有着大量的文档 – 大概在450，000单词左右 – 因此有时候查询您所需要的内容并非易事。一些查看文档的好地方是[搜索页面](https://docs.djangoproject.com/en/1.8/search/)（Search Page）和[目录](https://docs.djangoproject.com/en/1.8/genindex/)(Index）。

或者您可以完整浏览！

## 文档是如何组织的

Django官方文档的主体内容可以根据满足不同的需要分解成不同的块（chunks）：

- [介绍文档](https://docs.djangoproject.com/en/1.8/intro/)(introductory material) 通常是为刚接触Django或者Web开发的人所设计的。它并不涉及太多高深的内容，但相对地提供了一个宏观的概览视角帮助培养如何使用Django进行开发的“感觉”。

- 另一方面，[主题指南](https://docs.djangoproject.com/en/1.8/topics/)(topic guides)对Django的每一块进行了深入讲解。主题指南包括了对于Django的[模型系统](https://docs.djangoproject.com/en/1.8/topics/db/)(model system）, [模板引擎](https://docs.djangoproject.com/en/1.8/topics/templates/)(template engine）, [表单框架](https://docs.djangoproject.com/en/1.8/topics/forms/)(forms framework）以及更多内容的完整指导。

这里可能是您花费时间最多的地方；如果您动手实践了这些指导文档包含的内容，那么您应该对Django非常熟悉了。

- Web开发涉猎范围广，但是并不深入——遇到的问题可能跨越了不同的领域，为此我们撰写了一系列 [how-to guides](https://docs.djangoproject.com/en/1.8/howto/)文档来回答常见诸如"How do I..?"（我该如何）这类的问题。在这部分的文档里，您可以找到关于[使用Django生成PDF文档](https://docs.djangoproject.com/en/1.8/howto/outputting-pdf/)（generating PDFs with Django），[如何编写通用模板标签](https://docs.djangoproject.com/en/1.8/howto/custom-template-tags/)(writing custom template tags)等等的内容。

对于常见问题的答案可以在 [FAQ](https://docs.djangoproject.com/en/1.8/faq) 找到。

- 指导文档和How-To文档并没有完全覆盖到Django中的每一个类、函数、方法---那样的话内容会太多，不利于学习。实际上，每个类、函数、方法还有模块的实现细节都记录在[参考部分](https://docs.djangoproject.com/en/1.8/ref/)(reference)中。那里才是当你需要查找函数细节或是其他具体细节的地方。

- 如果您对于部署Django项目到公共网络感兴趣的话，我们文档也提供了一些关于各种部署设置的[指导](https://docs.djangoproject.com/en/1.8/howto/deployment/)(several guides)，包括您所需要关注的[部署清单](https://docs.djangoproject.com/en/1.8/howto/deployment/checklist/)(deployment checklist)。

- 最后，有一些"特殊"的文档通常与大多数开发者无关，比如[发行记录](https://docs.djangoproject.com/en/1.8/releases/)(release notes) 以及针对于那些想为Django项目贡献力量的开发人员的[内部文档](https://docs.djangoproject.com/en/1.8/internals/)，此外还包括了一些[不好分类的杂散文档](https://docs.djangoproject.com/en/1.8/misc/)(few other things that simply don’t fit elsewhere)。

## 文档是如何更新的

正如Django代码每天都在开发和改进，我们的文档也在不断完善中。我们改进文档的理由如下：

- 修正文档内容，例如语法/排版错误。
- 为有需要扩展的章节增加内容和可能的示例。
- 记录之前尚未归档的Django特性。（这样的功能列表正在萎缩，但仍然存在）。
- 当Django增加新特性，以及Django API 或者 behavior 发生改变时，会补充相关内容到文档中。

Django 文档和其代码使用同一份版本控制进行管理。它位于我们 Git 仓库的[docs](https://github.com/django/django/tree/master/docs)目录下。在Git仓库中，每一份在线文档都是独立的文本文件。

## 哪里可以获取文档

您可以通过多种方式阅读Django文档，根据优先级顺序排列如下：

### 在线获取

最新版本的Django官方文档位于 [https://docs.djangoproject.com/en/dev/](https://docs.djangoproject.com/en/dev/)。这些HTML页面是通过源代码控制的文本文件自动生成的。这就意味着它们涉及了Django“最新和最好”的方面——包括了最新的修正和新添加的内容，以及对于只面向使用Django最新版本的用户开放的新特性讨论。（参考下文“版本之间的差异”）

我们鼓励您通过[工单系统](https://code.djangoproject.com/newticket?component=Documentation)(ticket system)提交更改，修正和建议以促进文档质量的改善。Django的开发人员会积极地关注工单系统，并根据您的反馈来改善文档。

请注意，无论如何，提交的工单应该和文档密切相关，而不是涉及技术支持的问题。如果您需要额外的技术支持，请试试 [django user](https://docs.djangoproject.com/en/1.8/internals/mailing-lists/#django-users-mailing-list) 邮件列表或者 [#django IRC channel](irc://irc.freenode.net/django)。

### 纯文本

如果是想离线阅读或者仅仅只是为了方便，您可以直接阅读 Django 的纯文本文档。

如果您正在使用Django的官方发行版本，请注意对应发行版的说明文档都包含在了代码压缩包（tarball）的 **`docs/`**的目录下。

如果您正在使用开发版本的 Django(又名"trunk")，请注意 **`docs/`** 目录包含了所有的文档。您可以通过 **`git checkout`** 来获取最新更新。

一个稍微有点技术含量的查看文档的方法是通过 Unix 系统的 **`grep`** 命令来查找关键字搜索文档。例如，这将会展示 Django 文档中每一处提及"**`max_length`**"的地方：

```
$ grep -r max_length /path/to/django/docs/
```

### 下载为本地HTML文件

您也可以通过以下简单的步骤获取到官方文档的本地HTML文件：

- Django 的文档使用了[Sphinx](http://sphinx-doc.org/)的文档系统来进行从纯文本到 HTML的转换。您需要通过 Sphinx 官网下载安装包，或者通过 **`pip`**方式进行安装：

```
$ pip install Sphinx
```

- 然后使用文档目录下的**`Makefile`**进行纯文本到HTML格式的转换：

```
cd path/to/django/docs
$ make html
```

进行此操作，您需要安装 [GUN Make](http://www.gnu.org/software/make/)。

如果您是在 Windows 系统的环境下，也可以选择使用目录中的批处理文件：

```
cd path\to\django\docs
make.bat html
```

- 最后生成的HTML格式的文档会位于 **`docs/_build/html`**中。

#### 注意

Django 文档的生成需要 Sphinx 版本大于0.6或者更新的版本，但我们更建议直接使用 Sphinx 1.0.2 或者更新的版本。

## 版本之间的差异

正如前面提及的那样，我们 Git 仓库中的文档包含了许多"最新最好"(latest and greatest)的改变。这些改变通常包括了Django 开发版本新增的一些特性。因此出于以上理由，有必要说明我们在处理不同Django版本文档时所遵循的准则。

我们遵循着如下准则：

- 在 *djangoproject.com* 呈现的内容始终是Git仓库最新文档的HTML版本。这些文档总是对应Django官方最新版本，再加上在最新框架发布以来的特性的更改或者添加的内容。

- 当Django的开发版本中添加新特性的时候，我们会尽可能在同一份git提交中同步更新文档。

- 为了在文档中区分特性的更改或者添加，我们使用了如下说明文字：“`New in version X.Y`”，意思是将出现在接下来的发行版本中（因此，还处在开发的阶段中）。

- 在某些情况下，文档的修复和改进可能会在开发人员的同意下回迁到最后的发行分支。然而，一旦某个版本的Django不再被支持，那么对应版本的文档也会停止进一步的更新。

- [主文档Web页面](https://docs.djangoproject.com/en/dev/)(main documentation Web page)包含着所有旧版本的文档。请务必确保您阅读的文档版本对应着您正在使用的Django！
