# Flask Web 开发

## Chapter 1 & Chapter 2

- Python, 修饰器是 Python 语言的标准特性，可以使用不同的方式修改函数的行为。惯常用法是使用修饰器`把函数注册为事件的处理程序`。
- Flask，`@app.route(/user/<int:id>)` 尖括号中为 URL 的动态部分，此处，`int` 表示只匹配路由中使用 int 的动态部分。还有 `float`，`path`，其中 `path` 也是字符串，但不把斜杆视作分隔符，而当作动态片段的一部分
- Flask，Web 服务器启动后，会进入轮询，等待并处理请求（我以为是类似中断的方式呢）
- Flask，使用 *context* 让特定的变量在一个线程中全局可访问，而不干扰其他线程。（比如`request`)
- 多线程 Web 服务器会创建一个线程池，再从线程池中选择一个线程用于处理接收到的请求。

![Flask Context Global Variables](flask_context_global_variables.png)

- Flask 在分发请求之前激活（或推送）程序和请求上下文，请求处理完成后再将其删除。
- ，Flask 会在程序的 URL 映射中查找请求的 URL。`URL 映射`是 URL 和视图函数之间的对应关系。Flask 使用 `app.route` 修饰器或者非修饰器形式的 `app.add_url_rule()` 生成映射。

```python
app.url_map  # 查看 Flask 程序的 URL 映射
```

- 请求钩子 - 请求钩子函数和视图函数之间共享数据一般使用上下文全局变量 g
    - `before_first_request`：注册一个函数，在处理第一个请求之前运行。
    - `before_request`：注册一个函数，在每次请求之前运行。
    - `after_request`：注册一个函数，如果没有未处理的异常抛出，在每次请求之后运行。
    - `teardown_request`：注册一个函数，即使有未处理的异常抛出，也在每次请求之后运行。
- `重定向` 的特殊响应类型，没有页面文档，仅告诉浏览器一个新地址用以加载新页面。`return redirect("http://kissg.me")` 或 `return make_response("", 302, {"Location": "http://kissg.me"})`
- `abort` - 生成特殊的响应，用于处理错误。不会把控制权交还给调用它的函数，而是抛出异常把控制权交给 Web 服务器。

## Chapter 3

- Flask 提供的 `render_template` 函数把 Jinja2 模板引擎集成到了程序中。 `render_template` 函数的第一个参数是模板的文件名。随后的参数都是`键值对`,表示模板中变量对应的`真实值`。
- 访问数据库添加用户，生成响应并返回给浏览器。这两个过程分别称为`业务逻辑`和`表现逻辑`。
- 默认情况下，Flask 在程序文件夹中的 `templates` 子文件夹中寻找模板。
- Jinja2 使用 `{{ xx }}` 作为占位符，告诉模板引擎这个位置的值从渲染模板时使用的数据中获取
- Jinja2 能识别所有类型的变量,甚至是一些复杂的类型,例如列表、字典和对象
- 模板可以使用`过滤器`修改变量，格式如 `{{ name | capitalize }}`
- `safe` 过滤器，在渲染时不转亦。默认情况下,出于安全考虑,Jinja2 会转义所有变量。
- 除了 `if` 和 `for`，Jinja2 还支持`宏(macro)`，宏类似于 Python 代码中的函数。为了重复使用宏,可以将其保存在单独的文件中,然后在需要使用的模板中导入
- 需要在多处重复使用的模板代码片段可以写入单独的文件,再包含在所有模板中,以避免重复。另一种复用代码的方式是`模板继承`
- Bootstrap 是客户端框架,因此不会直接涉及服务器。服务器需要做的只是提供引用了 Bootstrap 层叠样 式表(CSS) 和 JavaScript 文件 的 HTML 响应, 并在 HTML、CSS 和JavaScript 代码中实例化所需组件
- 最常见的错误代码有两个:404,`客户端请求未知页面或路由`时显示;500,`有未处理的异常`时显示。
- 返回的渲染后的模板，是 `response` 的 body
- 错误处理函数也返回响应，并返回与错误对应的数字状态码
- 生成连接程序内不同路由的链接时,使用相对地址就足够了。如果要生成在浏览器之外使用的链接,则必须使用绝对地址。
- `url_for()` - 生成动态地址时, 将动态部分作为关键字参数传入；任何额外参数添加到查询字符串
- 日期本地化 - 在服务器上只使用 UTC 时间，把时间单位发送给 Web 浏览器,转换成当地时间,然后渲染

```html
{% macro render_comment(comment) %}
<li>{{ comment }}</li>
{% endmacro %}
<ul>
{% for comment in comments %}
{{ render_comment(comment) }}
{% endfor % }
</ul>
```
## Chapter 4

- `request.form` 能获取 POST 提交的表达
- 跨站请求伪造(CSRF), 恶意网站把请求发送到被攻击者已登录的其他网站时就会引发 CSRF 攻击
- `SECRET_KEY` 配置变量是通用密钥,可在 Flask 和多个第三方扩展中使用
- 使用 Flask-WTF 时,`每个 Web 表单都由一个继承自 Form 的类表示`。这个类定义表单中的一组字段,`每个字段都用对象表示`。字段对象可附属一个或多个验证函数。验证函数用来验证用户提交的输入值是否符合要求。

![Flask-WTF](flask-wtf-support.png)

- 刷新页面时浏览器会重新发送之前已经发送过的最后一个请求, 如果这个请求是一个包含表单数据的 POST 请求,刷新页面后会再次提交表单. 基于这个原因,最好别让 Web 程序把 POST 请求作为浏览器发送的最后一个请求
    - 解决方法: 将 `重定向` 作为 POST 请求的响应, 而不用常规响应. 重定向是一种特殊的响应,响应内容是 URL,而不是包含 HTML 代码的字符串。浏览器收到这种响应时,会向重定向的 URL 发起 GET 请求,显示页面的内容。由于最后一个请求是 GET 请求, 刷新命令能正常使 (`POST / Redict / GET`)
- 上述方法会带来另一个问题: 程序处理 POST 请求时,使用 form.name.data 获取用户输入的名字,可是一旦这个请求结束,数据也就丢失了。因为这个 POST 请求使用重定向处理,所以程序需要保存输入的名字,这样重定向后的请求才能获得并使用这个名字,从而构成真正的响应
- 程序可以把数据存储在用户`会话`中,在请求之间`记住`数据。用户会话是一种`私有存储`,存在于每个连接到服务器的客户端中
- 默认情况下,用户会话保存在客户端 cookie 中,使用设置的 `SECRET_KEY` 进行加密签名
- 请求完成后,有时需要让用户知道状态发生了变化, 可以用确认消息. 警告或错误提醒.
- `flask.flash()` 在发给客户端的下一个响应中显示一个消息
- 仅调用 flash() 函数不能把消息显示出来, 需要使用模板来渲染这些消息. Flask 将 `get_flashed_messages()` 函数开放给了模板, 用来获取并渲染消息

## Chapter 5

- 关系性数据库, 使用结构化查询语言
- 选择数据库框架时, 要考虑的:
    - 易用性
    - 性能
    - 可移植性
    - 框架集成度
- Flask-SQLAlchemy, 使用 URL 指定数据库
- 程序使用的数据库 URL 必须保存到 Flask 配置对象的 SQLALCHEMY_DATABASE_URI 键中

![SQLAlchemy Support](SQLAlchemy support.png)

- `relationship` 代表了关系的面向对象视角. 第一个参数表明关系的另一端是哪个 Model, 若 Model 未定义, 使用字符串指定; `backref` 参数为另一端 Model 添加一个属性, 从而定义反向关系.
- 大多数情况下, db.relationship() 都能自行找到关系中的外键,但有时却无法决定把哪一列作为外键

- 数据库会话 (`事务`) 能保证数据库的一致性。提交操作使用原子方式把会话中的对象全部写入数据库。如果在写入会话的过程中发生了错误,整个会话都会失效。如果你始终把相关改动放在会话中提交,就能避免因部分更新导致的数据库不一致性。
- 数据库会话也可`回滚`。调用 db.session.rollback() 后,添加到数据库会话中的所有对象都会还原到它们在数据库时的状态
- `Model.query.all()` - 查询所有记录
- `Model.query.filter_by(condition)` - 过滤器, 更精确的查询
- `str(Model.query.filter_by(condition))` - 将 query 对象转成字符串, 查看原生 SQL 查询语句

![SQLAlchemy filters](sqlalchemy-filter.png)

![SQLAlchemy Execcutors](sqlalchemy-executor.png)

- 使用 `Flask-Migrate` 做数据库迁移. 它是对 `Alembic` 的轻量包装.
- 在 Alembic 中,数据库迁移用迁移脚本表示。脚本中有两个函数,分别是 upgrade() 和downgrade() 。 upgrade() 函数把迁移中的改动应用到数据库中, downgrade() 函数则将改动删除。
- 可以使用 revision 命令手动创建 Alembic 迁移,也可使用 migrate 命令自动创建。自动创建的迁移不一定总是正确的,有可能会漏掉一些细节。自动生成迁移脚本后一定要进行检查

## Chapter 6

- 异步发送邮件
- 将执行异步发送邮件的作业交给 Celery 任务队列

## Chapter 7

- 程序经常需要设定多个配置, 最好的例子就是开发, 测试和生产环境使用不同的数据库. (用 config.py 来管理配置)
- templates 和 static 文件夹是程序包的一部分,因此这两个文件夹被移到了 app 中
- `延时创建程序实例`, 把创建过程移到可显式调用的`工厂函数`中. 不仅可以给脚本留出配置程序的时间,还能够创建多个程序实例
- 蓝本和程序类似,也可以定义路由。不同的是, 在蓝本中定义的路由处于休眠状态,直到蓝本注册到程序上后,路由才真正成为程序的一部分
- 在蓝本中编写错误处理程序稍有不同,如果使用 errorhandler 修饰器,那么只有蓝本中的错误才能触发处理程序。要想注册程序全局的错误处理程序,必须使用 app_errorhandler
- 在蓝本中编写视图函数主要有两点不同:第一,和前面的错误处理程序一样,路由修饰器由蓝本提供;第二, url_for() 函数的用法不同.
- Flask 会为蓝本中的全部端点加上一个命名空间,这样就可以在不同的蓝本中使用相同的端点名定义视图函数,而不会产生冲突
- `顶级目录`下启动程序
- `单元测试` - `setUp()` 和 `tearDown()` 方法分别在各测试前后运行. 并且名字以 `test_` 开头的函数都作为测试执行

## Chapter 8

- 对于不同的程序功能,我们要使用不同的蓝本,这是保持代码整齐有序的好方法。
- render_template() 函数会首先搜索程序配置的模板文件夹,然后再搜索蓝本配置的模板文件夹
- 注册蓝本时使用的 `url_prefix` 是可选参数, 如果使用了这个参数,注册后蓝本中定义的所有路由都会加上指定的前缀
- 用户登录程序后,他们的认证状态要被记录下来,这样浏览不同的页面时才能记住这个状态
- 在生产服务器上,登录路由必须使用安全的 HTTP,从而加密传送给服务器的表单数据。如果没使用安全的 HTTP,登录密令在传输过程中可能会被截取,在服务器上花再多的精力用于保证密码安全都无济于事。
- 每个程序都可以决定用户确认账户之前可以做哪些操作。比如,允许未确认的用户登录,但只显示一个页面,这个页面要求用户在获取权限之前先确认账户。
- 对蓝本来说, `before_request` 钩子只能应用到属于蓝本的请求上. 想要在蓝本中使用针对程序全局请求的钩子,必须使用 `before_app_request` 修饰器
- 如果 `before_request` 或 `before_app_request` 的回调返回响应或重定向,Flask 会直接将其发送至客户端,而不会调用请求的视图函数。因此,这些回调可在必要时拦截请求。

## Chapter 10

- `before_app_request`, 在每个请求之前调用. 可用于拦截请求, 也可以做如: 更新用户最后一次登录时间的操作
- 用户资料的编辑分两种情况。最显而易见的情况是,用户要进入一个页面并在其中输入自己的资料,而且这些内容显示在自己的资料页面上。还有一种不太明显但也同样重要的情况,那就是要让管理员能够编辑任意用户的资料
- WTForms 的实例必须在其 `choices` 中设置选项. 选项必须是一个由元组组成的列表,各元组都包含两个元素:选项的标识符和显示在控件中的文本字符串
- `Gravatar` 能将头像和电子邮件联系起来. 这需要, 用户到 `http://gravatar.com` 注册, 然后上传图片. 生成头像 URL 时, 要计算电子邮件的 MD 值

## Chapter 11

- 维护两个完全相同的 HTML 片段副本不是个好主意, Jinja2 提供的 `include()` 指令就非常有用, 复用模板
- 一种区分独立模板与局部模板的方式是通过文件名, 习惯上, 局部模板使用前缀 `_`
- 在 Web 浏览器中,内容多的网页需要花费更多的时间生成、下载和渲染,所以网页内容变多会降低用户体验的质量. 解决方案是: `分页`显示数据, 进行段式渲染
- 手动添加数据库记录浪费时间而且很麻烦,所以最好能使用自动化方案. 有很多生成虚拟数据的 Python 包, 比如 `PorgeryPy`, `faker`
- 严格来讲, 项目的 `requirements.txt` 应只包含真正的依赖. 对于开发过程中使用的包, 如 `faker`, 用 `requirements` 目录来区分保存生产环境的依赖和开发环境的依赖
- `requirements.txt` 使用 `-r` 参数来导入其他文本的内容. 应用软件识别 `-r`
- Flask-SQLAlchemy 的 `paginate()` 方法, 返回页查询
- `paginate()` 方法的返回值是一个 `Pagination` 类对象. 可用于在模板中生成分页链接,因此将其作为参数传入了模板
- Jinja2 宏的参数列表中不用加入 `**kwargs` 即可接收关键字参数
- Jinja2 宏类似于函数
- 富文本支持
    - PageDown - 使用 JavaScript 实现的客户端 Markdown 到 HTML 的转换程序。
    - Flask-PageDown - 为 Flask 包装的 PageDown,把 PageDown 集成到 Flask-WTF 表单中。
    - Markdown - 使用 Python 实现的服务器端 Markdown 到 HTML 的转换程序。
    - Bleach - 使用 Python 实现的 HTML 清理器。
- Flask-PageDown 扩展定义了一个 PageDownField 类,这个类和 WTForms 中的 TextAreaField 接口一致 (`接口一致的重要性`)
- Markdown 格式的文本, 在 POST 时只发送纯文本, 页面中显示的 HTML 预览会被丢掉。和表单一起发送生成的 HTML 预览有安全隐患,因为攻击者轻易就能修改 HTML 代码,让其和 Markdown 源不匹配,然后再提交表单。
- 在服务器上使用 `Markdown` 将提交的表单 (纯文本) 转换成 HTML, 再用 `Bleach` 进行清理, 确保只包含几个允许使用的 HTNL 标签.
- 把 Markdown 格式的博客文章转换成 HTML 的过程可以在 `_posts.html` 模板中完成,但这么做效率不高,因为每次渲染页面时都要转换一次。为了避免重复工作,可在创建博客文章时做一次性转换。转换后的博客文章 HTML 代码缓存在 Post 模型的一个新字段中,在模板中可以直接调用。文章的 Markdown 源文本还要保存在数据库中,以防需要编辑。
- 每篇文章都要有一个专页,使用唯一的 URL 引用
- 与博客文章相关的最后一个功能是文字编辑器,它允许用户编辑自己的文章。博客文章编辑器显示在单独的页面中。在这个页面的上部会显示文章的当前版本,以供参考

## Chapter 14

- `Flask-RESTful` 提供了 `Resource` 基类，它定义了对于给定的一个 URL 的一个或多个 HTTP 方法的路由
- `add_resource` 函数通过使用一个给定的 `endpoint` 将路由注册到框架上。最好显式地给出 `endpoint`
- `flask_restful.reqparse.RequestParser` 使用类似 `ArgParse`，它默认搜索 `request.values` 域，因此字段位置有时候需要设置，如字段来自 `request.json`，则添加参数 `location=json`
- `Flask-RESTful` 会自动将数据转成 JSON
- `Flask-RESTful` 的 `Resource` 类继承自 Flask 的 `MethodView` 类，可以通过定义 `decorators` 类变量添加装饰器
- `Flask-RESTful` ＋`Blueprints`:

from flask_restful import Api, Resource, url_for

app = Flask(__name__)
api_bp = Blueprint('api', __name__)
api = Api(api_bp)

class TodoItem(Resource):
    def get(self, id):
        return {'task': 'Say "Hello, World!"'}

api.add_resource(TodoItem, '/todos/<int:id>')
app.register_blueprint(api_bp)
```

- REST 的 6 个特征
    - C-S - 客户端与服务器之间必须有明确的界限
    - 无状态 - 客户端发出的请求中必须包含所有必要的信息。服务器不能在两次请求之间保存客户端的任何状态
    - 缓存 - 服务器发出的响应可以标记为可缓存或不可缓存，这样出于优化目的，客户端（或客户端和服务器之间的中间服务）可以使用缓存。
    - 接口统一 - 客户端访问服务器资源时使用的协议必须一致，定义良好，且已经标准化。（REST　Web　服务最常用的统一接口是　
    - 系统分层 - 在客户端和服务器之间可以按需插入代理服务器、缓存或网关，以提高性能、稳定性和
伸缩性
    - 按需代码 - 客户端可以选择从服务器上下载代码，在客户端的环境中执行。
- `资源`是 REST 架构的核心概念。URL 的内容无关紧要，只要资源 URL 只表示唯一的资源即可。表示资源集合的 URL 习惯是在末尾加一个 `/`，表示目录
- Flask 会特殊对待末端带有斜线的路由。如果客户端请求的 URL 的末端没有斜线，而唯一匹配的路由末端有斜线，Flask 会自动响应一个重定向，转向末端带斜线的 URL。反之则不会重定向
- REST 架构不要求必须为一个资源实现所有的请求方法。如果资源不支持客户端使用的请求方法，响应的状态码为 405，返回“不允许使用的方法”。Flask 会自动处理这种错误。
- 在设计良好的 REST API 中,客户端只需知道几个顶级资源的 URL,其他资源的 URL 则从响应中包含的链接上发掘
- 客户端程序和服务器上的程序是独立开发的，有时甚至由不同的人进行开发。因此，Web 服务的容错能力要比一般的 Web 程序强,而且还要保证旧版客户端能继续使用。通常的解决办法是`使用版本区分 Web 服务所处理的 URL`
- `request.json` - 获得请求中包含的 JSON 数据；`jsonify()` 将 Python 字典转为包含 JSON 的响应
- REST API 相关的路由是一个自成一体的程序子集，为了更好地组织代码，最好将路由放到独立的 Blueprint 中。
    - Flask 用 蓝图（blueprints） 的概念来在一个应用中或跨应用制作应用组件和支持通用的模式
    - 一个 Blueprint 对象与 Flask 应用对象的工作方式很像，但它确实不是一个应用，而是一个描述如何构建或扩展应用的 蓝图 。Flask uses a concept of blueprints for making application components and supporting common patterns within an application or across applications.
- 为所有客户端生成适当响应的一种方法是，在错误处理程序中根据客户端请求的格式改写响应，这种技术称为`内容协商`。
- Web 服务中使用 cookie 有点不现实,因为 Web 浏览器之外的客户端很难提供对 cookie 的支持

