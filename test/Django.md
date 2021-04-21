# Django

[toc]

## 1. 创建项目

> 1. 创建项目命令：django-admin startproject mysite
>    - 外层的`mysite/`目录与Django无关，只是你项目的容器，可以任意重命名。
>    - `manage.py`：一个命令行工具，管理Django的交互脚本。
>    - 内层的`mysite/`目录是真正的项目文件包裹目录，它的名字是你引用内部文件的Python包名，例如：`mysite.urls`。
>    - `mysite/__init__.py`:一个定义包的空文件。
>    - `mysite/settings.py`:项目的配置文件。
>    - `mysite/urls.py`:路由文件，所有的任务都是从这里开始分配，相当于Django驱动站点的目录。
>    - `mysite/wsgi.py`:一个基于WSGI的web服务器进入点，提供底层的网络通信功能，通常不用关心。
>    - `mysite/asgi.py`：一个基于ASGI的web服务器进入点，提供异步的网络通信功能，通常不用关心。
> 2. 启动开发服务器命令：python manage.py runserver 8000或者设置pycharm manage.py启动参数： runserver 127.0.0.1:8000后使用python manage.py runserver

## 2. 创建应用

> 1. 创建polls应用目录命令： python manage.py startapp polls
>
> 2. Pycharm创建app的图形按钮命令：python manage.py startapp polls
>
> 3. 编写视图：polls/views.py，编写代码
>
>    ```python
>    from django.http import HttpResponse
>    
>    def index(request):
>        return HttpResponse("这里是liujiangblog.com的投票站点")
>    ```
>
> 4. 调用视图： polls下新建一个urls.py，编写代码
>
>    ```python
>    from django.urls import path
>    
>    from . import views
>    
>    urlpatterns = [
>        path('', views.index, name='index'),
>    ]
>    ```
>
>    在主urls.py文件mysite/urls.py中添加`urlpattern`条目，指向我们刚才建立的polls这个app独有的urls.py文件，需要导入include模块
>
>    ```python
>    from django.contrib import admin
>    from django.urls import include, path
>    
>    urlpatterns = [
>        path('polls/', include('polls.urls')),
>        path('admin/', admin.site.urls),
>    ]
>    ```
>
>    include语法相当于多级路由，它把接收到的url地址去除与此项匹配的部分，将剩下的字符串传递给下一级路由urlconf进行判断
>
> 5. path()方法
>
>    一个路由配置模块就是一个urlpatterns列表，列表的每个元素都是一项path，每一项path都是以path()的形式存在。
>
>    path()方法可以接收4个参数，其中前2个是必须的：`route`和`view`，以及2个可选的参数：`kwargs`和`name`。
>
>    route：route 是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 urlpatterns 的第一项path开始，按顺序依次匹配列表中的项，直到找到匹配的项，然后执行该条目映射的视图函数或下级路由，其后的条目将不再继续匹配。因此，url路由执行的是短路机制，path的编写顺序非常重要！route不会匹配 GET 和 POST 参数或域名。
>
>    view: view指的是处理当前url请求的视图函数。当Django匹配到某个路由条目时，自动将封装的`HttpRequest`对象作为第一个参数，被“捕获”的参数以关键字参数的形式，传递给该条目指定的视图view。
>
>    kwargs: 任意数量的关键字参数可以作为一个字典传递给目标视图。
>
>    name: 对你的URL进行命名，让你能够在Django的任意处，尤其是模板内显式地引用它。这是一个非常强大的功能，相当于给URL取了个全局变量名，不会将url匹配地址写死。

## 3. 数据库配置

> 1. `mysite/settings.py`配置文件是整个Django项目的设置中心。
>
>    1. 1默认的数据库配置：
>
>    ```python
>    DATABASES = {
>        'default': {
>            'ENGINE': 'django.db.backends.sqlite3',
>            'NAME': BASE_DIR / 'db.sqlite3',
>        }
>    }
>    ```
>
>    - ENGINE（引擎）：可以是`django.db.backends.sqlite3`、`django.db.backends.postgresql`、`django.db.backends.mysql`、`django.db.backends.oracle`，当然其它的也行。
>    - NAME（数据库名称）：类似Mysql数据库管理系统中用于保存项目内容的数据库的名字。如果你使用的是默认的SQLite3，那么数据库将作为一个文件将存放在你的本地机器内，此时的NAME应该是这个文件的完整绝对路径包括文件名，默认情况下该文件储存在你的项目根目录下。
>
>       1.2 TIME_ZONE: 时区设置，Asia/Shanghai为国内时区
>
>       1.3 注意settings文件中顶部的`INSTALLED_APPS`设置项。它列出了所有的项目中被激活的Django应用（app）。你必须将你自己创建的app注册在这里。每个应用可以被多个项目使用，并且可以打包和分发给其他人在他们的项目中使用。
>
>    默认情况，`INSTALLED_APPS`中会自动包含下列条目，它们都是Django自动生成的：
>
>    - django.contrib.admin：admin管理后台站点
>    - django.contrib.auth：身份认证系统
>    - django.contrib.contenttypes：内容类型框架
>    - django.contrib.sessions：会话框架
>    - django.contrib.messages：消息框架
>    - django.contrib.staticfiles：静态文件管理框架
>
>    上面的那些应用会默认被启动，并且也需要建立一些数据库表，所以在使用它们之前我们要在数据库中创建这些表。使用命令创建数据表：python manage.py migrate
>
>    migrate命令将遍历`INSTALLED_APPS`设置中的所有项目，在数据库中创建对应的表，并打印出每一条动作信息。

## 4. 创建模型

> 编辑`polls/models.py`文件，具体代码如下：
>
> ```python
> from django.db import models
> 
> 
> class Question(models.Model):
>     question_text = models.CharField(max_length=200)
>     pub_date = models.DateTimeField('date published')
> 
> 
> class Choice(models.Model):
>     question = models.ForeignKey(Question, on_delete=models.CASCADE)
>     choice_text = models.CharField(max_length=200)
>     votes = models.IntegerField(default=0)
> ```
>
> 每一个类都是`django.db.models.Model`的子类。每一个字段都是`Field`类的一个实例，例如用于保存文本数据的CharField和用于保存时间类型的DateTimeField，它们告诉Django每一个字段保存的数据类型。
>
> 每一个Field实例的名字就是字段的名字（如： question_text 或者 pub_date ）。在你的Python代码中会使用这个值，你的数据库也会将这个值作为表的列名。
>
> 一些Field类必须提供某些特定的参数。例如CharField需要你指定`max_length`。这不仅是数据库结构的需要，同样也用于数据验证功能。
>
> 有必填参数，当然就会有可选参数，比如在votes里我们将其默认值设为0.
>
> 最后请注意，我们使用`ForeignKey`定义了一个外键关系。它告诉Django，每一个Choice关联到一个对应的Question（注意要将外键写在‘多数’的一方）。Django支持通用的数据关系：一对一，多对一和多对多。

## 5. 启用模型

> 要将应用添加到项目中，需要在`INSTALLED_APPS`设置中增加指向该应用的配置文件的链接
>
> ```python
> INSTALLED_APPS = [
> 'polls.apps.PollsConfig',
> 'django.contrib.admin',
> 'django.contrib.auth',
> 'django.contrib.contenttypes',
> 'django.contrib.sessions',
> 'django.contrib.messages',
> 'django.contrib.staticfiles',
> ]
> # 大多数情况下。简写成'polls'就可以：
> INSTALLED_APPS = [
> 'polls',
> 'django.contrib.admin',
> 'django.contrib.auth',
> 'django.contrib.contenttypes',
> 'django.contrib.sessions',
> 'django.contrib.messages',
> 'django.contrib.staticfiles',
> ]
> ```
>
> 添加完后，需要运行命令： python manage.py makemigrations polls
>
> 会看到类似下面的提示：
>
> ```
> Migrations for 'polls':
>   polls\migrations\0001_initial.py
>     - Create model Question
>     - Create model Choice
> ```
>
> 通过运行`makemigrations`命令，Django 会检测你对模型文件的修改，也就是告诉Django你对模型有改动，并且你想把这些改动保存为一个“`迁移(migration)`”。
>
> `migrations`是Django保存模型修改记录的文件，这些文件保存在磁盘上。polls/migrations/0001_initial.py
>
> `sqlmigrate`的命令可以展示SQL语句： python manage.py sqlmigrate polls 0001
>
> 运行`python manage.py check`命令，可以检查项目中的错误，并不实际进行迁移或者链接数据库的操作。
>
> 运行migrate命令，在数据库中进行真正的表操作。
>
> ```
> $ python manage.py migrate
> Operations to perform:
>     Apply all migrations: admin, auth, contenttypes, polls, sessions
> Running migrations:
>     Rendering model states... DONE
>     Applying polls.0001_initial... OK
> ```
>
> migrate命令对所有还未实施的迁移记录进行操作，本质上就是将你对模型的修改体现到数据库中具体的表中。Django通过一张叫做`django_migrations`的表，记录并跟踪已经实施的migrate动作，通过对比获得哪些迁移尚未提交。
>
> 迁移的功能非常强大，允许你随时修改你的模型，而不需要删除或者新建你的数据库或数据表，在不丢失数据的同时，实时动态更新数据库。需要记住**修改模型时的操作分三步**：
>
> - 在models.py中修改模型；
> - 运行`python manage.py makemigrations`为改动创建迁移记录文件；
> - 运行`python manage.py migrate`，将操作同步到数据库。

## 6. 模型自带的API

> 进入Python交互环境，尝试使用Django提供的数据库访问API。要进入Python的shell，请输入命令：python manage.py shell
>
> 调用`manage.py`参数能将`DJANGO_SETTINGS_MODULE`环境变量导入，它将自动按照`mysite/settings.py`中的设置，配置好你的python shell环境，这样就可以导入和调用任何你项目内的模块了。
>
> 或者也可以这样，先进入一个纯净的python环境，然后启动Django，具体如下：
>
> ```python
> >>> import django
> >>> django.setup()
> ```
>
> 当你进入shell后，尝试一下下面的API吧
>
> ```python
> >>> from polls.models import Question, Choice # 导入我们写的模型类
> 
>     # 现在系统内还没有questions对象
>     >>> Question.objects.all()
>     <QuerySet []>
> 
>     # 创建一个新的question对象
>     # Django推荐使用timezone.now()代替python内置的datetime.datetime.now()
>     # 这个timezone就来自于Django的依赖库pytz
>     from django.utils import timezone
>     >>> q = Question(question_text="What's new?", pub_date=timezone.now())
> 
>     # 你必须显式的调用save()方法，才能将对象保存到数据库内
>     >>> q.save()
> 
>     # 默认情况，你会自动获得一个自增的名为id的主键
>     >>> q.id
>     1
> 
>     # 通过python的属性调用方式，访问模型字段的值
>     >>> q.question_text
>     "What's new?"
>     >>> q.pub_date
>     datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)
> 
>     # 通过修改属性来修改字段的值，然后显式的调用save方法进行保存。
>     >>> q.question_text = "What's up?"
>     >>> q.save()
> 
>     # objects.all() 用于查询数据库内的所有questions
>     >>> Question.objects.all()
>     <QuerySet [<Question: Question object>]>
> ```
>
> 上面的<Question: Question object>是一个不可读的内容展示，你无法从中获得任何直观的信息，为此我们需要一点小技巧，让Django在打印对象时显示一些我们指定的信息。
>
> 返回`polls/models.py`文件，修改一下question和Choice这两个类，代码如下：
>
> ```python
> from django.db import models
> 
> class Question(models.Model):
>     # ...
>     def __str__(self):
>         return self.question_text
> 
> class Choice(models.Model):
>     # ...
>     def __str__(self):
>         return self.choice_text
> ```
>
> 这个技巧不但对你打印对象时很有帮助，在你使用Django的admin站点时也同样有帮助。
>
> 另外，这里我们再自定义一个模型的方法，用于判断问卷是否最近时间段内发布度的：
>
> ```python
> # polls/models.py
> 
> import datetime
> 
> from django.db import models
> from django.utils import timezone
> 
> 
> class Question(models.Model):
>     # 是否在当前发布的问卷
>     def was_published_recently(self):
>         return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
> ```
>
> 请注意上面分别导入了两个关于时间的模块，一个是python内置的datetime，一个是Django工具包提供的timezone。
>
> 保存修改后，我们重新启动一个新的python shell，再来看看其他的API:
>
> ```python
> >>> from polls.models import Question, Choice
> 
> # 先看看__str__()的效果，直观多了吧？
> >>> Question.objects.all()
> <QuerySet [<Question: What's up?>]>
> 
> # Django提供了大量的关键字参数查询API
> >>> Question.objects.filter(id=1)
> <QuerySet [<Question: What's up?>]>
> >>> Question.objects.filter(question_text__startswith='What')
> <QuerySet [<Question: What's up?>]>
> 
> # 获取今年发布的问卷
> >>> from django.utils import timezone
> >>> current_year = timezone.now().year
> >>> Question.objects.get(pub_date__year=current_year)
> <Question: What's up?>
> 
> # 查询一个不存在的ID，会弹出异常
> >>> Question.objects.get(id=2)
> Traceback (most recent call last):
> ...
> DoesNotExist: Question matching query does not exist.
> 
> # Django为主键查询提供了一个缩写：pk。下面的语句和Question.objects.get(id=1)效果一样.
> >>> Question.objects.get(pk=1)
> <Question: What's up?>
> 
> # 看看我们自定义的方法用起来怎么样
> >>> q = Question.objects.get(pk=1)
> >>> q.was_published_recently()
> True
> 
> # 显示所有与q对象有关系的choice集合，目前是空的，还没有任何关联对象。
> >>> q.choice_set.all()
> <QuerySet []>
> 
> # 创建3个choices.
> >>> q.choice_set.create(choice_text='Not much', votes=0)
> <Choice: Not much>
> >>> q.choice_set.create(choice_text='The sky', votes=0)
> <Choice: The sky>
> >>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)
> 
> # Choice对象可通过API访问和他们关联的Question对象
> >>> c.question
> <Question: What's up?>
> 
> # 同样的，Question对象也可通过API访问关联的Choice对象
> >>> q.choice_set.all()
> <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
> >>> q.choice_set.count()
> 3
> 
> # API会自动进行连表操作，通过双下划线分割关系对象。连表操作可以无限多级，一层一层的连接。
> # 下面是查询所有的Choices，它所对应的Question的发布日期是今年。（重用了上面的current_year结果）
> >>> Choice.objects.filter(question__pub_date__year=current_year)
> <QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
> 
> # 使用delete方法删除对象
> >>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
> >>> c.delete()
> ```

## 7. admin后台管理站点

> Django最大的优点之一，就是体贴的为你提供了一个基于项目model创建的一个后台管理站点admin。这个界面只给站点管理员使用，并不对大众开放。虽然admin的界面可能不是那么美观，功能不是那么强大，内容不一定符合你的要求，但是它是免费的、现成的，并且还是可定制的，有完善的帮助文档。
>
> （如果你对admin的界面美观有切实需求，可以尝试使用simpleui库，不要用xadmin）
>
> 1. 创建管理员用户
>
>    通过下面的命令，创建一个可以登录admin站点的用户:
>
>    ```
>    python manage.py createsuperuser
>    ```
>
>    根据提示输入对应的用户名，邮箱，密码。
>
>    ```
>    Password: **********
>    Password (again): *********
>    Superuser created successfully.
>    ```
>
> 2. 启动开发服务器
>
>    执行runserver命令启动服务器后，在浏览器访问`http://127.0.0.1:8000/admin/`。
>
>    在实际环境中，为了站点的安全性，我们一般不能将管理后台的url随便暴露给他人，不能用`/admin/`这么简单的路径。
>
>    可以将根url路由文件`mysite/urls.py`中`admin.site.urls`对应的表达式，换成你想要的，比如：
>
>    \``` from django.contrib import admin from django.urls import path
>
>    urlpatterns = [ path('polls/', include('polls.urls')), path('control/', admin.site.urls), ] ```
>
>    这样，我们必须访问`http://127.0.0.1:8000/control/`才能进入admin界面。
>
> 3. 进入站点
>
>    利用刚才建立的admin账户，登陆admin
>
>    当前只有两个可编辑的模型：Groups和Users。它们是`django.contrib.auth`模块提供的身份认证框架内的模型。
>
> 4. 注册投票应用
>
>    打开`polls/admin.py`文件，加入下面的内容：
>
>    ```python
>    from django.contrib import admin
>    from .models import Question
>    
>    admin.site.register(Question)
>    ```









