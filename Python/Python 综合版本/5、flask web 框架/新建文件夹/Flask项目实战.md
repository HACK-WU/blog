# 架构的搭建

- config.py
```python
# 数据库的配置
HOSTNAME = '10.1.88.88'
PORT     = '3306'
DATABASE = 'egkjqa'
USERNAME = 'root'
PASSWORD = '123456'
DB_URI = 'mysql+pymysql://{}:{}@{}:{}/{}?charset=utf8'.format(USERNAME,PASSWORD,HOSTNAME,PORT,DATABASE)

SQLALCHEMY_DATABASE_URI = DB_URI
SQLALCHEMY_TRACK_MODIFICATIONS = True

SECRET_KEY = "SDFASDFASDFASDFASDFASDF"
```

- exts.py

```python
from flask_sqlalchemy import SQLAlchemy
db = SQLAlchemy()
```

- app.py

```python
from flask import Flask
import config
from exts import db
app = Flask(__name__)
app.config.from_object(config)
db.init_app(app)
```

- blueprints
  - \_\_init\_\_.py
  - qa.py
  - user.py

- \_\_init\_\_.py

```python
from .qa import bp as qa_bp
from .user import bp as user_bp
```

- qa.py

```python
from flask import Blueprint
bp = Blueprint("qa", __name__, url_prefix="/")

@bp.route("/")
def index():
    return "首页"
```

- user.py

```python
from flask import Blueprint
bp = Blueprint("user", __name__, url_prefix="/user")

@bp.route("/login")
def login():
    return "登陆"
```

- app.py

```python
from flask import Flask
import config
from exts import db
from blueprints import qa_bp, user_bp

app = Flask(__name__)
app.config.from_object(config)
db.init_app(app)

app.register_blueprint(qa_bp)
app.register_blueprint(user_bp)

if __name__ == '__main__':
    app.run()

```

![image-20211215131803881](Flask项目实战/image-20211215131803881.png)

![image-20211215131820263](Flask项目实战/image-20211215131820263.png)

# base.html页面实现

- bootstrap

  - bootstrap.4.6.min.css(官网给的参考用法，可以选择下载下来)

  ```html
  
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css" integrity="sha384-zCbKRCUGaJDkqS1kPbPd7TveP5iyJE0EjAuZQTgFLD2ylzuqKfdKlfG/eSrtxUkn" crossorigin="anonymous">
  
  ```

  

- templates
  - base.html
  - index.html

base.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}
    {% endblock %}</title>

    <link rel="stylesheet" href="{{ url_for("static", filename = "bootstrap/bootstrap.4.6.min.css") }}">

    {% block head %}
    {% endblock %}
</head>
<body>
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container">
      <a class="navbar-brand" href="#">英格问答</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav mr-auto">
      <li class="nav-item active">
        <a class="nav-link" href="#">首页 <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">发布问答</a>
      </li>
      <li class="nav-item ml-5">
          <form class="form-inline my-2 my-lg-0">
              <input class="form-control mr-sm-2" type="search" placeholder="关键字" aria-label="Search">
              <button class="btn btn-outline-success my-2 my-sm-0" type="submit">搜索</button>
          </form>
      </li>

    </ul>
    <ul class="navbar-nav ">
      <li class="nav-item ">
        <a class="nav-link" href="#">登陆 <span class="sr-only">(current)</span></a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">注册</a>
      </li>

    </ul>
  </div>
  </div>
</nav>
<div class="container">
    {% block body %}
    {% endblock %}
</div>

</body>
</html>
```

index.html

```html
{%  extends "base.html" %}
{% block title %}
    英格问答首页
{% endblock %}

{% block head %}

{% endblock %}

{% block body %}
<h1>
首页
</h1>
{% endblock %}
```

- qa.py

```python
from flask import Blueprint, render_template
bp = Blueprint("qa", __name__, url_prefix="/")

@bp.route("/")
def index():
    return render_template("index.html")
```

![image-20211215142227126](Flask项目实战/image-20211215142227126.png)

# 注册登陆页面

- user.py

```python 
from flask import Blueprint, render_template
bp = Blueprint("user", __name__, url_prefix="/user")

@bp.route("/login")
def login():
    return render_template("login.html")

@bp.route("/register")
def register():
    return render_template("register.html")
```
- static
  - css
    - init.css

```css
body{
    background-color: #f3f3f3;
}
```

- base.html(增加一个css样式，增加两个url_for反转函数)

```python
  <link rel="stylesheet" href="{{ url_for("static", filename = "css/init.css") }}">



		<li class="nav-item ">
          <a class="nav-link" href="{{ url_for("user.login") }}">登陆 <span class="sr-only">(current)</span></a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{{ url_for("user.register") }}">注册</a>
        </li>
```

- login.html

```html
{% extends "base.html" %}
{% block title %}
  英格问答首页
{% endblock %}

{% block head %}

{% endblock %}

{% block body %}
  <div class="row mt-4">
    <div class="col"></div>
    <div class="col">
      <form>
        <div class="form-group">
          <label for="exampleInputEmail1">邮箱地址</label>
          <input type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp">
          <small id="emailHelp" class="form-text text-muted">我们不会把邮箱用于其他用途</small>
        </div>
        <div class="form-group">
          <label for="exampleInputPassword1">密码</label>
          <input type="password" class="form-control" id="exampleInputPassword1">
        </div>
        <button type="submit" class="btn btn-primary btn-block">立即登陆</button>
      </form>
    </div>
    <div class="col"></div>
  </div>

{% endblock %}
```

- register.html

```html
{%  extends "base.html" %}
{% block title %}
    英格问答首页
{% endblock %}

{% block head %}

{% endblock %}

{% block body %}
  <div class="row mt-4">
    <div class="col"></div>
    <div class="col">
      <form>
        <div class="form-group">
          <label for="exampleInputEmail1">邮箱地址</label>
          <input type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp">
          <small id="emailHelp" class="form-text text-muted">我们不会把邮箱用于其他用途</small>
        </div>

        <div class="form-group">
          <label for="exampleInputEmail1">获取验证码</label>
          <div class="input-group">
            <input type="text" class="form-control" >
          <div class="input-group-append">
             <button class="btn btn-outline-secondary" type="button">获取验证码</button>
          </div>
          </div>
        </div>

        <div class="form-group">
          <label for="exampleInputPassword1">密码</label>
          <input type="password" class="form-control" id="exampleInputPassword1">
        </div>

        <div class="form-group">
          <label for="exampleInputPassword1">确认密码</label>
          <input type="password" class="form-control" >
        </div>

        <button type="submit" class="btn btn-primary btn-block">立即注册</button>
      </form>
    </div>
    <div class="col"></div>
  </div>

{% endblock %}
```

![image-20211215150659478](Flask项目实战/image-20211215150659478.png)

![image-20211215150758936](Flask项目实战/image-20211215150758936.png)

# 注册登陆功能

## 邮箱配置

- pip install Flask-Mail



- config.py

```python
# 邮箱配置
# 项目中用的是QQ邮箱
MAIL_SERVER = "smtp.qq.com"
MAIL_PORT = 465
MAIL_USE_TLS = False
MAIL_USE_SSL = True
MAIL_DEBUG = True
MAIL_USERNAME = "2177780569@qq.com"
MAIL_PASSWORD = "znndrwwhkcr...."
MAIL_DEFAULT_SENDER = "2177780569@qq.com"
```

- exts.py

```python
from flask_mail import Mail
mail = Mail()
```

- app.py

```python
from exts import db, mail
mail.init_app(app)
```

- user.py

```python
from exts import mail
from flask_mail import Message
@bp.route("/mail")
def my_mail():
    message = Message(
        subject="邮箱测试",
        recipients=["441732861@qq.com"],
        body = "这是一篇测试邮件",
    )
    mail.send(message)
    return "success!"
```

![image-20211215155502966](Flask项目实战/image-20211215155502966.png)

## 数据库配置

- models.py

```python
from exts import db
from datetime import datetime
class EmailCpatchaModel(db.Model):
    __tablename__ = "email_cattcha"
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    email = db.Column(db.String(100), nullable=False,unique=True)
    captcha = db.Column(db.String(10), nullable=False)
    create_time = db.Column(db.DateTime, default=datetime.now)
```

- user.py

```python 
from models import EmailCpatchaModel
```

- app.py

```python
from flask_migrate import Migrate

migrate = Migrate(app, db)
```

- 终端

```
flask db init
flask db migrate
flask db upgrade

```

## 验证码存储

- user.py

```python
from flask import Blueprint, render_template, request
from exts import mail,db
from flask_mail import Message
from models import EmailCpatchaModel
import string
import random
import datetime


@bp.route("/captcha")
def get_captcha():
    email = request.args.get("email")
    print(email)
    my_string = string.digits + string.ascii_letters
    captcha = "".join(random.sample(my_string, 4))
    print(captcha)
    if email:
        message = Message(
            subject="验证码",
            recipients=[email],
            body=f"英格科技问答，您的注册验证码是：{captcha}, 请不要告诉任何人哦",
        )
        mail.send(message)
		
        # 定义验证码数据
        captcha_model = EmailCpatchaModel.query.filter_by(email=email).first()
        if captcha_model:
            captcha_model.captcha = captcha
            captcha_model.create_time = datetime.now()
            db.session.commit()
        else:
            captcha_model = EmailCpatchaModel(email=email, captcha=captcha)
            db.session.add(captcha_model)
            db.session.commit()
        return "success!"
    else:
        return "没有传递邮箱"
```

## 注册功能

- UserModel.py

```python
class UserModel(db.Model):
    __tablename__ = "user"
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    username = db.Column(db.String(150), nullable=False,unique=True)
    password = db.Column(db.String(200), nullable=False)
    email = db.Column(db.String(100), nullable=False,unique=True)
    join_time = db.Column(db.DateTime, default=datetime.now)
```

- 终端

```
flask db init
flask db migrate
flask db upgrade
```

- blueprints
  - forms.py

```python
import wtforms
from wtforms.validators import length,email,EqualTo
from models import EmailCaptchaModel,UserModel

class RegisterForm(wtforms.Form):
    username = wtforms.StringField(validators=[length(min=3,max=20)])
    captcha = wtforms.StringField(validators=[length(min=4,max=4)])
    password = wtforms.StringField(validators=[length(min=6,max=20)])
    password_confirm = wtforms.StringField(validators=[EqualTo("password")])
    email = wtforms.StringField(validators=[email()])

    def validate_captcha(self, field):
        captcha = field.data
        email = self.email.data
        captcha_model = EmailCaptchaModel.query.filter_by(email = email).first()
        if not captcha_model or captcha_model.captcha.lower() != captcha.lower():
            print("邮箱验证码错误")
            raise wtforms.ValidationError("邮箱验证码错误")

    def validate_email(self,field):
        email = field.data
        user_model = UserModel.query.filter_by(email=email).first()
        if user_model:
            print("邮箱已经被注册过")
            raise wtforms.ValidationError("邮箱已经被注册过")
```

- user.py

```python
from werkzeug.security import generate_password_hash

@bp.route("/register", methods=["GET", "POST"])
def register():
    if request.method == "GET":
        return render_template("register.html")
    else:
        form = RegisterForm(request.form)
        if form.validate():
            email = form.email.data
            username = form.username.data
            password = form.password.data
			hash_password = generate_password_hash(password)
            user = UserModel(email=email, username=username, password=hash_password)
            db.session.add(user)
            db.session.commit()
            return redirect(url_for("user.login"))
        else:
            print("validate 失败")
            return redirect(url_for("user.register"))
```

- register.html

![image-20211216162519201](Flask项目实战/image-20211216162519201.png)

![image-20211216162906088](Flask项目实战/image-20211216162906088.png)



### 发送验证码

- static
  - js
    - register.js
  - jquery
    - jquery.3.6.min.js
      - https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js

- jquery.3.6.min.js

```javascript
// 来自https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.min.js
```

- register.html

```html
{% block head %}
  <script src=" {{ url_for("static", filename = "jquery/jquery.3.6.min.js") }}"></script>
  <script src=" {{ url_for("static", filename = "js/register.js") }}"></script>
{% endblock %}
--------------------------------------------------------------------------------------------------------
             <button class="btn btn-outline-secondary" type="button" id="captcha-btn">获取验证码</button>

```

- user.py

```python
@bp.route("/captcha", methods=["POST"])
def get_captcha():
    email = request.form.get("email")

---------------------------------------------------------------------------------------------------------
        # code：200，成功的、正常的请求
    	return jsonify({"code": 200})
    else:
        # code：400，客户端错误
        return jsonify({"code": 400})
```

- register.js

```javascript
function bindCaptchaBtnClick(){
    $("#captcha-btn").on("click", function (event){
        var email = $("input[name='email']").val();
        if(!email){
            alert("请先输入邮箱");
            return ;
        }
        //通过js发送网络请求：ajax。Async JavaScript And XML（JSON）
        $.ajax({
            url: "/user/captcha",
            method: "POST",
            data: {
                "email": email
            },
            success: function (res){
                var code = res["code"];
                if (code == 200){
                    alert("验证码发送成功");
                }else {
                    alert(res["message"]);
                }
            }
        })
    });
}

//等网页文档所有元素加载完成之后再执行
$(function (){
   bindCaptchaBtnClick();
});
```

- register.js 增加了验证码倒计时功能

```javascript
function bindCaptchaBtnClick(){
    $("#captcha-btn").on("click", function (event){
        var $this = $(this);
        var email = $("input[name='email']").val();
        if(!email){
            alert("请先输入邮箱");
            return ;
        }
        //通过js发送网络请求：ajax。Async JavaScript And XML（JSON）
        $.ajax({
            url: "/user/captcha",
            method: "POST",
            data: {
                "email": email
            },
            success: function (res){
                var code = res["code"];
                if (code == 200){
                    //取消点击事件
                    $this.off("click");
                    //开始倒计时
                    var countDown = 60;
                    var timer = setInterval(function (){
                        countDown -= 1;
                        if (countDown > 0){
                            $this.text(countDown+"秒后重新发送");
                        }else {
                            $this.text("获取验证码");
                            //重新执行一下这个函数，重新绑定点击事件
                            bindCaptchaBtnClick()
                            //如果不需要倒计时了，那么就要记得清楚倒计时，否则会一直执行下去
                            clearInterval(timer)
                        }
                    },1000)
                    alert("验证码发送成功");
                }else {
                    alert(res["message"]);
                }
            }
        })
    });
}

//等网页文档所有元素加载完成之后再执行
$(function (){
   bindCaptchaBtnClick();
});
```



实现效果

![image-20211222143455808](Flask项目实战/image-20211222143455808.png)

## 登陆功能

### 登陆基本功能

- login.html

```html
{% block body %}
  <div class="row mt-4">
    <div class="col"></div>
    <div class="col">
      <form action="{{ url_for("user.login")}}" method="post">
        <div class="form-group">
          <label for="exampleInputEmail1">邮箱地址</label>
          <input type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp" name="email">
        </div>
        <div class="form-group">
          <label for="exampleInputPassword1">密码</label>
          <input type="password" class="form-control" id="exampleInputPassword1" name="password">
        </div>
        <button type="submit" class="btn btn-primary btn-block">立即登陆</button>
      </form>
    </div>
    <div class="col"></div>
  </div>

{% endblock %}
```

- forms.py

```python
class LoginForm(wtforms.Form):
    email = wtforms.StringField(validators=[email()])
    password = wtforms.StringField(validators=[length(min=6,max=20)])
```

- login.py

```python

@bp.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "GET":
        return render_template("login.html")
    else:
        form = LoginForm(request.form)
        if form.validate():
            email = form.email.data
            password = form.password.data
            user = UserModel.query.filter_by(email=email).first()
            if user and check_password_hash(user.password, password):
                session["user_id"] = user.id
                return redirect("/")
            else:
                return redirect(url_for("user.login"))
        else:
            return redirect(url_for("user.login"))
```

### 登陆错误提示

- login.py 增加了登陆错误提示

```python
@bp.route("/login", methods=["GET", "POST"])
def login():
    if request.method == "GET":
        return render_template("login.html")
    else:
        form = LoginForm(request.form)
        if form.validate():
            email = form.email.data
            password = form.password.data
            user = UserModel.query.filter_by(email=email).first()
            if user and check_password_hash(user.password, password):
                session["user_id"] = user.id
                return redirect("/")
            else:
                flash("邮箱密码不匹配！！")
                return redirect(url_for("user.login"))
        else:
            flash("邮箱或密码格式错误！！")
            return redirect(url_for("user.login"))
```

- login.html

```html
        <div class="form-group">
          {% for message in get_flashed_messages() %}
            <div>
            <div class="text-danger">{{ message }}</div>
            </div>
          {% endfor %}
        </div>
        <div class="form-group">
          <button type="submit" class="btn btn-primary btn-block">立即登陆</button>
        </div>
```





实现效果

![image-20211222151737964](Flask项目实战/image-20211222151737964.png)

![image-20211222151724673](Flask项目实战/image-20211222151724673.png)



## 访问控制

### 登陆成功进行渲染

- app.py

```python
# 在所有请求之前会执行的函数
@app.before_request
def before_request():
    user_id = session.get("user_id")
    if user_id:
        try:
            user = UserModel.query.get(user_id)
            # 给g绑定一个user属性，这个属性就是user这个变量
            # setattr(g, "user", user)
            g.user = user
        except:
            g.user = None
# 请求来了 -> before_request -> 视图函数 -> 摄图函数中返回模板 -> context_processor
# 渲染的所有的模板都会执行这个函数
@app.context_processor
def context_processor():
    if hasattr(g, "user"):
        return {"user": g.user}
    else:
        return {}

if __name__ == '__main__':
    app.run()
```

- base.html

```html
		{% if user %}
          <li class="nav-item ">
          <span class="nav-link">{{ user.username }}</span>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">退出登陆</a>
          </li>
        {% else %}
          <li class="nav-item ">
            <a class="nav-link" href="{{ url_for("user.login") }}">登陆 <span class="sr-only">(current)</span></a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="{{ url_for("user.register") }}">注册</a>
          </li>
        {% endif %}
```

### 退出登陆

- user.py

```python
@bp.route("/logout")
def logout():
    # 清楚所有的数据
    session.clear()
    return redirect(url_for("user.login"))
```

- base.html

```html
            <a class="nav-link" href="{{ url_for("user.logout") }}">退出登陆</a>
```

### 装饰器功能

在需要用到的功能之前增加装饰器，用来判断是否登陆

- decorators.py

```python
from flask import g, redirect, url_for
from functools import wraps

def login_required(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        if hasattr(g, "user"):
            return func(*args, **kwargs)
        else:
            return redirect(url_for("user.login"))
    return wrapper
```

- qa.py

```python
from decorators import login_required

@bp.route("/question/public")
@login_required
def public_question():
    return render_template("public_question.html")
```

- public_question.html

```html
{% extends "base.html" %}
{% block title %}发布问答{% endblock %}
{% block body %}
<h1>发布问答</h1>
{% endblock %}
```

- base.html

```html
		          <a class="nav-link" href="/">首页 <span class="sr-only">(current)</span></a>


		   		  <a class="nav-link" href="{{ url_for("qa.public_question") }}">发布问答</a>
```

# 发布问答

## 发布问题

- public_question.html

```html
{% extends "base.html" %}
{% block title %}发布问答{% endblock %}
{% block body %}
<div class="row" style="margin-top: 20px">
<div class="col"></div>
<div class="col-6">
  <h1 style="text-align: center">发布问答</h1>
  <form action="{{ url_for("qa.public_question") }}" method="post">
    <div class="form-group">
      <input type="text" name="title" class="form-control" placeholder="请输入标题">
    </div>
    <div class="form-group">
      <textarea name="content"  class="form-control" rows="10" placeholder="请输入内容"></textarea>
    </div>
    <div class="form-group" style="text-align: right">
      <button class="btn btn-primary">发布</button>
    </div>
  </form>
</div>
<div class="col"></div>
</div>
{% endblock %}
```

![image-20211224152503695](Flask项目实战/image-20211224152503695.png)

- froms.py

```python
class QuestionForm(wtforms.Form):
    title = wtforms.StringField(validators=[length(min=3, max=150)])
    content = wtforms.StringField(validators=[length(min=4)])
```

- models.py

```python
class QuestionModel(db.Model):
    __tablename__ = "question"
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    title = db.Column(db.String(150), nullable=False)
    content = db.Column(db.Text, nullable=False)
    author_id = db.Column(db.Integer, db.ForeignKey("user.id"))
	create_time = db.Column(db.DateTime, default=datetime.now)
    
    author = db.relationship("UserModel", backref="questions")
    
-------------------------------------------------------------------------------------------------------
flask db migrate
flask db upgrade
```

- qa.py

```python

@bp.route("/question/public", methods=["GET", "POST"])
@login_required
def public_question():
    if request.method == "GET":
        return render_template("public_question.html")
    else:
        form = QuestionForm(request.form)
        if form.validate():
            title = form.title.data
            content = form.content.data
            question = QuestionModel(title=title, content=content, author=g.user)
            db.session.add(question)
            db.session.commit()
            return redirect("/")
        else:
            flash("标题或者内容格式错误！！")
            return redirect(url_for("qa.public_question"))
```



![image-20211224152528131](Flask项目实战/image-20211224152528131.png)

- public_question.html

```html
{% extends "base.html" %}
{% block title %}发布问答{% endblock %}
{% block body %}
  <div class="row" style="margin-top: 20px">
    <div class="col"></div>
    <div class="col-6">
      <h1 style="text-align: center">发布问答</h1>
      <form action="{{ url_for("qa.public_question") }}" method="post">
        <div class="form-group">
          <input type="text" name="title" class="form-control" placeholder="请输入标题">
        </div>
        <div class="form-group">
          <textarea name="content" class="form-control" rows="10" placeholder="请输入内容"></textarea>
        </div>
        {% for message in get_flashed_messages() %}
          <div>
            <div class="text-danger">{{ message }}</div>
          </div>
        {% endfor %}
        <div class="form-group" style="text-align: right">
          <button class="btn btn-primary">发布</button>
        </div>
      </form>
    </div>
    <div class="col"></div>
  </div>
{% endblock %}
```

![image-20211224152719130](Flask项目实战/image-20211224152719130.png)

## 展示问答

- static
  - css
    - index.css

```css
.question-ul li {
    padding: 10px;
    overflow: hidden;
    display: flex;
    background-color: #fff;
    border-bottom: 1px solid #eee;
}

.side-question {
    flex-basis: 38px ;
    height: 100%;
}

.side-question-avatar {
    width: 38px;
    height: 38px;
    border-radius: 3px;
}

.question-main {
    flex: 1;
    width: 660px;
    margin-left: 10px;
    overflow: hidden;
}

.question-title a {
    color: #259;
    font-size: 14px;
    font-weight: 900;
}

.question-authon {
    font-size: 12px;
    margin-top: 5px;
}

.question-content {
    margin-top: 5px;
    font-size: 12px;
}

.question-detail {
    text-align: right;
    margin-top: 10px;
}

.question-detail .question-author {
    margin-right: 10px;
}
```

- templates
  - index.html

```html
{% extends "base.html" %}
{% block title %}
  英格问答首页
{% endblock %}

{% block head %}
  <link rel="stylesheet" href="{{ url_for( 'static', filename = 'css/index.css') }}" class="rel">
{% endblock %}

{% block body %}
  <div class="row" style="margin-top: 20px">
    <div class="col"></div>
    <div class="col-8">
      <ul class="question-ul">
        <li>
          <div class="side-question">
            <img src="{{ url_for("static", filename="images/avatar.png") }}" alt="" class="side-question-avatar">
          </div>
          <div class="question-main">
            <div class="question-title"><a href="#">钢铁是怎样练成的</a></div>
            <div class="question-content">XXX</div>
            <div class="question-detail">
              <span class="question-author">bbj1030</span>
              <span class="question-time">2020-12-24</span>
            </div>
          </div>
        </li>
      </ul>
    </div>
    <div class="col"></div>
  </div>
{% endblock %}
```

![image-20211224160107908](Flask项目实战/image-20211224160107908.png)

- qa.py

```python
@bp.route("/")
def index():
    questions = QuestionModel.query.order_by(db.text("-create_time")).all()
    return render_template("index.html", questions=questions)
```

- index.html   使用数据库的数据对首页进行渲染

```html
{% extends "base.html" %}
{% block title %}
  英格问答首页
{% endblock %}

{% block head %}
  <link rel="stylesheet" href="{{ url_for( 'static', filename = 'css/index.css') }}" class="rel">
{% endblock %}

{% block body %}
  <div class="row" style="margin-top: 20px">
    <div class="col"></div>
    <div class="col-8">
      <ul class="question-ul">
        {% for question in questions %}
          <li>
          <div class="side-question">
            <img src="{{ url_for("static", filename="images/avatar.png") }}" alt="" class="side-question-avatar">
          </div>
          <div class="question-main">
            <div class="question-title"><a href="#">{{ question.title }}</a></div>
            <div class="question-content">{{ question.content }}</div>
            <div class="question-detail">
              <span class="question-author">b{{ question.author.username }}</span>
              <span class="question-time">{{ question.create_time }}</span>
            </div>
          </div>
        </li>
        {% endfor %}
      </ul>
    </div>
    <div class="col"></div>
  </div>
{% endblock %}
```

## 问答详情页面

- index.html

```html
            <div class="question-title"><a href="{{ url_for("qa.question_detail", question_id=question.id) }}">{{ question.title }}</a></div>
```

- qa.py

```python
@bp.route("/question/<int:question_id>")
def question_detail(question_id):
    question = QuestionModel.query.get(question_id)
    return render_template("detail.html", question=question)
```

- static
  - css
    - detail.css

```css
.page-title{
    text-align: center;
}

.question-info {
    text-align: center;
}

.comment-group-title {
    margin-top: 20px;
}

.form-container {
    width: 500px;
    margin-top: 10px;
    text-align: right;
}

.comment-group li {
    border-bottom: 1px solid #eee;
    padding: 10px 0;
}

.comment-group li .user-info {
    height: 40px;
    line-height: 40pX;
    color: #9b9b9b;
    font-size: 16px;
}

.user-info .avatar {
    height: 40px;
    width: 40px;
    float: left;
    border-radius: 50%;
}

.user-info .username {
    margin-left: 10px;
}
.user-info .create-time {
    float: right;
}
.comment-contenti {
    margin-left: 50px;
}

```

- detail.html

```html
{% extends "base.html" %}

{% block title %}问答详情{% endblock %}

{% block head %}
  <link rel="stylesheet" href="{{ url_for("static", filename="css/detail.css") }}">
{% endblock %}

{% block body %}
  <div class="row" style="margin-top: 20px">
    <div class="col"></div>
    <div class="col-8" style="background-color: #fff;">
      <h3 class="page-title">钢铁是怎样练成的</h3>
      <p class="question-info">
        <span>作者：尼古拉</span>
        <span>时间：2020-12-24</span>
      </p>
      <hr>
      <p class="question-content">xxx</p>
      <hr>
    </div>
    <div class="col"></div>
  </div>
{% endblock %}
```

![image-20211224164357292](Flask项目实战/image-20211224164357292.png)

# 评论功能

## 评论页面

- models.py

```python

class AnswerModel(db.Model):
    __tablename__ = "answer"
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    content = db.Column(db.Text, nullable=False)
    create_time = db.Column(db.DateTime, default=datetime.now)
    question_id = db.Column(db.Integer,db.ForeignKey("question.id"))
    author_id = db.Column(db.Integer,db.ForeignKey("user.id"))

    question = db.relationship("QuestionModel", backref = "answers")
    author = db.relationship("UserModel", backref="answers")
-------------------------------------------------------------------------------------------------------
flask db migrate
flask db upgrade
```

- detail.css

```css
.comment-group{
    list-style: none;
    padding-left: 0;
}
```

- detail.html

```html
{% extends "base.html" %}

{% block title %}{{ question.title }}{% endblock %}

{% block head %}
  <link rel="stylesheet" href="{{ url_for("static", filename="css/detail.css") }}">
{% endblock %}

{% block body %}
  <div class="row" style="margin-top: 20px">
    <div class="col"></div>
    <div class="col-8" style="background-color: #fff;">
      <h3 class="page-title">{{ question.title }}</h3>
      <p class="question-info">
        <span>作者：{{ question.author.username }}</span>
        <span>时间：{{ question.create_time }}</span>
      </p>
      <hr>
      <p class="question-content">{{ question.content }}</p>
      <hr>
      <h4 class="comment-group-title">评论（10）：</h4>
      <form action="#" method="post">
        <input type="hidden" name="question_id" value="{{ question.id }}">
        <div class="form-group">
          <input type="text" placeholder="请填写评论" class="form-control">
        </div>
        <div class="form-group" style="text-align: right">
          <button class="btn btn-primary">评论</button>
        </div>
      </form>
      <ul class="comment-group">
        <li>
          <div class="user-info">
            <img src="{{ url_for("static", filename="images/avatar.png") }}" alt="" class="avatar">
            <span class="username">bbj1030</span>
            <span class="create-time">2020-12-24</span>
          </div>
          <p class="comment-content">我觉得挺好的啊</p>
        </li>
      </ul>

    </div>
    <div class="col"></div>
  </div>
{% endblock %}
```



![image-20211224173450138](Flask项目实战/image-20211224173450138.png)

##  评论

- forms.py

```py
class AnswerForm(wtforms.Form):
    content = wtforms.StringField(validators=[length(min=1)])
```

- qa.py

```python

@bp.route("/answer/<int:question_id>", methods=["GET", "POST"])
def answer(question_id):
    form = AnswerForm(request.form)
    if form.validate():
        content = form.content.data
        answer_model = AnswerModel(content=content, author = g.user,question_id = question_id)
        db.session.add(answer_model)
        db.session.commit()
        return redirect(url_for("qa.question_detail", question_id = question_id))
    else:
        print(11111)
        flash("表单验证失败！！")
        return redirect(url_for("qa.question_detail", question_id=question_id))

```

- detail.html

```html
{% extends "base.html" %}

{% block title %}{{ question.title }}{% endblock %}

{% block head %}
  <link rel="stylesheet" href="{{ url_for("static", filename="css/detail.css") }}">
{% endblock %}

{% block body %}
  <div class="row" style="margin-top: 20px">
    <div class="col"></div>
    <div class="col-8" style="background-color: #fff;">
      <h3 class="page-title">{{ question.title }}</h3>
      <p class="question-info">
        <span>作者：{{ question.author.username }}</span>
        <span>时间：{{ question.create_time }}</span>
      </p>
      <hr>
      <p class="question-content">{{ question.content }}</p>
      <hr>
      <h4 class="comment-group-title">评论（10）：</h4>
      <form action="{{ url_for("qa.answer", question_id=question.id) }}" method="post">
        <div class="form-group">
          <input type="text" placeholder="请填写评论" name="content" class="form-control">
        </div>
        <div class="form-group" style="text-align: right">
          <button class="btn btn-primary">评论</button>
        </div>
      </form>
      <div class="form-group">
        {% for message in get_flashed_messages() %}
          <div>
            <div class="text-danger">{{ message }}</div>
          </div>
        {% endfor %}
      </div>
      <ul class="comment-group">
        <li>
          <div class="user-info">
            <img src="{{ url_for("static", filename="images/avatar.png") }}" alt="" class="avatar">
            <span class="username">bbj1030</span>
            <span class="create-time">2020-12-24</span>
          </div>
          <p class="comment-content">我觉得挺好的啊</p>
        </li>
      </ul>

    </div>
    <div class="col"></div>
  </div>
{% endblock %}
```

![image-20211224175215939](Flask项目实战/image-20211224175215939.png)

### 渲染评论页面

- detail.html

```html
{% extends "base.html" %}

{% block title %}{{ question.title }}{% endblock %}

{% block head %}
  <link rel="stylesheet" href="{{ url_for("static", filename="css/detail.css") }}">
{% endblock %}

{% block body %}
  <div class="row" style="margin-top: 20px">
    <div class="col"></div>
    <div class="col-8" style="background-color: #fff;">
      <h3 class="page-title">{{ question.title }}</h3>
      <p class="question-info">
        <span>作者：{{ question.author.username }}</span>
        <span>时间：{{ question.create_time }}</span>
      </p>
      <hr>
      <p class="question-content">{{ question.content }}</p>
      <hr>
      <h4 class="comment-group-title">评论（{{ question.answers|length }}）：</h4>
      <form action="{{ url_for("qa.answer", question_id=question.id) }}" method="post">
        <div class="form-group">
          <input type="text" placeholder="请填写评论" name="content" class="form-control">
        </div>
        <div class="form-group" style="text-align: right">
          <button class="btn btn-primary">评论</button>
        </div>
      </form>

      <div class="form-group">
        {% for message in get_flashed_messages() %}
          <div>
            <div class="text-danger">{{ message }}</div>
          </div>
        {% endfor %}
      </div>

      <ul class="comment-group">
        {% for answer in question.answers %}
          <li>
          <div class="user-info">
            <img src="{{ url_for("static", filename="images/avatar.png") }}" alt="" class="avatar">
            <span class="username">{{ answer.author.username }}</span>
            <span class="create-time">{{ answer.create_time }}</span>
          </div>
          <p class="comment-content">{{ answer.content }}</p>
        </li>
        {% endfor %}
      </ul>

    </div>
    <div class="col"></div>
  </div>
{% endblock %}
```

- models.py

```py
class AnswerModel(db.Model):
    __tablename__ = "answer"
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    content = db.Column(db.Text, nullable=False)
    create_time = db.Column(db.DateTime, default=datetime.now)
    question_id = db.Column(db.Integer,db.ForeignKey("question.id"))
    author_id = db.Column(db.Integer,db.ForeignKey("user.id"))

    question = db.relationship("QuestionModel", backref = db.backref("answers", order_by=create_time.desc()))
    author = db.relationship("UserModel", backref="answers")
```

# 搜索功能

- qa.py

```python
@bp.route("/search")
def search():
    q = request.args.get("q")
    # filter是直接使用字段名称
    # filter_by是使用模型.字段名称
    questions = QuestionModel.query.filter(or_(QuestionModel.title.contains(q), QuestionModel.content.contains(q))).order_by(db.text("-create_time"))
    return render_template("index.html", questions = questions)

```

- base.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>{% block title %}
  {% endblock %}</title>

  <link rel="stylesheet" href="{{ url_for("static", filename = "bootstrap/bootstrap.4.6.min.css") }}">
  <link rel="stylesheet" href="{{ url_for("static", filename = "css/init.css") }}">

  {% block head %}
  {% endblock %}
</head>
<body>
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <div class="container">
    <a class="navbar-brand" href="#">英格问答</a>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent"
            aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav mr-auto">
        <li class="nav-item active">
          <a class="nav-link" href="/">首页 <span class="sr-only">(current)</span></a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="{{ url_for("qa.public_question") }}">发布问答</a>
        </li>
        <li class="nav-item ml-5">
          <form class="form-inline my-2 my-lg-0" method="get" action="{{ url_for("qa.search") }}">
            <input class="form-control mr-sm-2" type="search" placeholder="关键字" aria-label="Search" name="q">
            <button class="btn btn-outline-success my-2 my-sm-0" type="submit">搜索</button>
          </form>
        </li>

      </ul>
      <ul class="navbar-nav ">
        {% if user %}
          <li class="nav-item ">
          <span class="nav-link">{{ user.username }}</span>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="{{ url_for("user.logout") }}">退出登陆</a>
          </li>
        {% else %}
          <li class="nav-item ">
            <a class="nav-link" href="{{ url_for("user.login") }}">登陆 <span class="sr-only">(current)</span></a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="{{ url_for("user.register") }}">注册</a>
          </li>
        {% endif %}

      </ul>
    </div>
  </div>
</nav>
<div class="container">
  {% block body %}
  {% endblock %}
</div>

</body>
</html>
```

