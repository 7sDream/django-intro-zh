# 快速安装指南

你需要先安装 Django 才可以使用它。我们有一份[完整安装指南](https://docs.djangoproject.com/en/1.8/topics/install/)，它涵盖了所有可能遇到的问题。你正在阅读的这个简易版本可以帮助你完成一个简单、最小化的安装。

## 安装 Python

作为一个 Python Web 框架，Django 依赖 Python。从[ Django 适用于哪些版本的 Python ](https://docs.djangoproject.com/en/1.8/faq/install/#faq-python-version-support)可以获取更多信息。较新版本的 Python 内置一个轻量级的数据库 SQLite，所以你暂时不需要配置数据库。

可以从[ Python 的官方网站](https://www.python.org/download/)或者系统的包管理工具获取到最新版的 Python。

> **Jython 上的 Django**
>
> 如果你使用的是 Jython（一种 Java 平台的 Python 实现），你需要遵循一些额外的步骤。查看[在 Jython 上运行 Django ](https://docs.djangoproject.com/en/1.8/howto/jython/)获取详细信息。

你可以在终端下输入命令 **python** 来验证是否已经安装 Python；你应该看到下面的信息：

```bash
Python 3.3.3 (default, Nov 26 2013, 13:33:18)
[GCC 4.8.2] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

## 配置数据库

只有当你需要使用“大型”数据库例如 PostgreSQL、MySQL 或 Oracle 时，才需要这一步。若要安装这些数据库，请参考[数据库安装信息](https://docs.djangoproject.com/en/1.8/topics/install/#database-installation)。

## 删除旧版本的 Django

如果你是从旧版本的 Django 升级安装，你将需要[在安装新版本之前卸载旧版本的 Django](https://docs.djangoproject.com/en/1.8/topics/install/#removing-old-versions-of-django)。

## 安装 Django

你可以使用下面三个简单的方式来安装 Django：

- 通过[操作系统所提供的发行包](https://docs.djangoproject.com/en/1.8/misc/distributions/)安装。对于那些所使用的操作系统提供了 Django 安装包的人来说，这是最快捷的安装方法。
- [安装官方正式发布的版本](https://docs.djangoproject.com/en/1.8/topics/install/#installing-official-release)。对大多数用户来说是最好的方式。
- [安装最新的开发版](https://docs.djangoproject.com/en/1.8/topics/install/#installing-development-version)。这对于那些想要尝试最新最棒的特性而不担心运行崭新代码的用户来说是最好的。你可能会遇到 bug，但报告 bug 有助于 Django 的开发。此外，第三方包的很可能不兼容最新的开发版。

> **务必参考与你所使用的 Django 版本相对应的文档！**
>
> 如果采用了前两种方式进行安装，你需要注意在文档中标明**在开发版中新增**的标记。这个标记表明这个特性仅适用开发版的 Django，而他们可能在当前版本无法工作。

## 验证安装

如果想验证是否成功安装了 Django，可以在终端输入 **python**。然后在 Python 提示符下，尝试导入 Django：

```python
>>> import django
>>> print(django.get_version())
1.8
```

你所安装的可能是其他版本的 Django 。

## 安装完成

安装完成！ - 现在你可以[开始通过实例学习](http://django-intro-zh.readthedocs.org/zh_CN/latest/part1/)了。
