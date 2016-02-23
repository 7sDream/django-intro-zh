# 创建你的第一个 Django 项目， 第六部分

这一篇从[第五部分（zh）](part5.md)结尾的地方继续讲起。再上一节中我们为网络投票程序编写了测试，而现在我们要为它加上样式和图片。

除了服务端生成的 HTML 以外，网络应用通常需要一些其他的文件——比如图片，脚本和样式表——来帮助渲染网络页面。在 Django 中，我们把这些文件统称为“静态文件”。

对于小项目来说，这个问题没什么大不了的，因为你可以把这些静态文件随便放在哪，只要服务程序能够找到它们就行。然而在大项目——特别是由好几个应用组成的大项目——中，处理不同应用所需要的静态文件的工作就显得有点麻烦了。

这就是 **django.contrib.staticfiles** 存在的意义：它将各个应用的静态文件（和一些你指明的目录里的文件）统一收集起来，这样一来，在生产环境中，这些文件就会集中在一个便于分发的地方。

## 自定义应用的界面和风格

首先，在 **polls** 目录下创建一个 **static** 目录。 Django 将会从这里收集静态文件，就像 Django 在 **polls/templates** 里寻找模板一样。

Django 的 **[STATICFILES_FINDERS](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-STATICFILES_FINDERS)** 设置项是一个列表，它包含了若干个知道如何从不同地点寻找静态文件的搜寻器。其中之一是 **AppDirectoriesFinder**，它会从 **INSTALLED_APPS** 中各个应用的 “static” 子目录中寻找文件，比如刚刚创建的 **polls/static**。 之前的管理页面也使用了相同的目录结构来管理静态文件。

在刚创建的 **static** 目录下创建一个 **polls** 目录，再在其中新建文件 **style.css**。也就是说，你的样式文件的位置是 **polls/static/polls/style.css**。由于 **AppDirectoriesFinder** 的存在，你可以在 Django 中使用 **polls/style.css** 来引用这个文件，就像我们引用模板文件那样。

> 静态文件命名空间
>
> 和模板一个样，虽然我们可以直接把样式表放在 **polls/static** 目录下（而不是再创建一个 **polls** 子目录），但是这并不是个好主意。Django 将会使用它找到的第一个和名称项匹配文件，当你在另一个应用里也有一个同名的文件的话，Django 将无法区分它们。我们想让 Django 能够找到正确的文件，最简单的方法就是使用命名空间。也就是把静态文件放到一个和应用同名的子目录中。

将以下代码写入样式表（**polls/static/polls/style.css**）中：

```css
/* polls/static/polls/style.css */

li a {
    color: green;
}
```

下一步，在 **polls/templates/polls/index.html** 的顶部加入以下内容：

```html
<!-- polls/templates/polls/index.html -->

{% load staticfiles %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
```

**{% load staticfiles %}** 语句会从 **staticfiles** 模板库里导入 **[{% static %}](https://docs.djangoproject.com/en/1.8/ref/contrib/staticfiles/#std:templatetag-staticfiles-static)** 模板标签。这个标签会把对静态文件的引用转化为绝对地址。

这就是你需要做的所有工作了。现在重新载入 **http://localhost:8000/polls/**，你会看到问题链接变成了绿色（Django 使用的样式），这说明样式表已经正常工作了。

## 添加背景图片

接下来，我们要为图片新建一个子目录。在 **polls/static/polls** 目录下创建 **images** 目录。然后在其中放入背景图片 background.gif。也就是说，背景图片路径为 **polls/static/polls/images/background.gif**。

然后，在样式表文件（polls/static/polls/style.css）中加入：

```css
/* polls/static/polls/style.css */

body {
    background: white url("images/background.gif") no-repeat right bottom;
}
```

重新加载 http://localhost:8000/polls/ 你将会看到背景图片被放置在了页面的右下角。

> 注意！
>
> 显然 {% static %} 模板标签是无法在静态文件中使用的，比如样式表文件，因为它们不是 Django 生成的。在静态文件中，你应该使用相对路径来互相引用。因为这样的话，你可以通过改变 **[STATIC_URL](https://docs.djangoproject.com/en/1.8/ref/settings/#std:setting-STATIC_URL)**（**static** 模块用它来生成 URL）达到改变静态文件地址的目的，而不需要在大量的文件中逐一修改引用文件的地址。

这些都只是**基础**。关于静态文件的各种设置，和框架中其余和静态文件有关的部分，请参考 **[the static files howto](https://docs.djangoproject.com/en/1.8/howto/static-files/)** 和 **[the staticfiles reference](https://docs.djangoproject.com/en/1.8/ref/contrib/staticfiles/)**。另外，**[Deploying static files](https://docs.djangoproject.com/en/1.8/howto/static-files/deployment/)** 讨论了在真实的服务器上组织静态文件的方法。

## 然后呢？

初学者教程就到此结束辣！你可以访问文章 **[接下来如何学习](whats_next.md)** 来决定下一步的学习方向。

如果你很熟系 Python 的打包机制，并且感兴趣如何将投票应用转化为一个“可重用的应用”，请看文章 **[高级内容：编写可重用的应用](reusable_app.md)**。
