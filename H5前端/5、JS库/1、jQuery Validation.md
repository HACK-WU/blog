# jQuery Validation 的参考文档

2023/5/17 18:09:50

jQuery Validation 是一个用于表单验证的 jQuery 插件，可以用于客户端数据校验，通过防止用户在提交表单之前输入不正确的信息来提高表单的可用性。以下是 jQuery Validation 的参考文档：

1.  官方网站：<https://jqueryvalidation.org/>

以上是 jQuery Validation 的参考文档，其中包括官方网站、中文文档、GitHub 仓库、API 文档、规则和选项文档、实例文档、扩展文档以及问题和解决方案，供开发者参考。

![](https://raw.githubusercontent.com/Chanzhaoyu/chatgpt-web/main/src/assets/avatar.jpg)

2023/5/17 18:16:38

详细说明一下jQuery Validation的使用方法

2023/5/17 18:17:42

使用 jQuery Validation 进行表单验证可以分为以下几个步骤：

1.  引入 jQuery 和 jQuery Validation 插件：

```
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script src="https://cdn.jsdelivr.net/jquery.validation/1.16.0/jquery.validate.min.js"></script>

```

1.  编写 HTML 表单

```
<form id="my-form" method="post">
  <label for="username">用户名：</label>
  <input type="text" name="username" id="username" required>

  <label for="password">密码：</label>
  <input type="password" name="password" id="password" required>

  <input type="submit" value="提交">
</form>

```

1.  初始化 jQuery Validation 插件

```
$(document).ready(function(){
  $("#my-form").validate({
    rules: {
      username: "required", // 用户名必填
      password: "required", // 密码必填
    },
    messages: {
      username: "请输入用户名",
      password: "请输入密码",
    }
  });
});

```

1.  在 rules 属性中设置每个表单字段的验证规则，messages 属性中设置每个表单字段的错误提示信息。更多可用的验证规则可以参考 jQuery Validation 的文档。
2.  最后，绑定 submit 事件并执行表单验证：

```
$(document).ready(function(){
  $("#my-form").validate({
    rules: {
      username: "required", // 用户名必填
      password: "required", // 密码必填
    },
    messages: {
      username: "请输入用户名",
      password: "请输入密码",
    },
    submitHandler: function(form) { // 表单验证通过后提交表单
      form.submit();
    }
  });
});

```

以上就是 jQuery Validation 的基本使用方法，可以根据实际需求进行修改和扩展。在实际使用中，也可以通过自定义验证器来满足更复杂的验证需求。

![](https://raw.githubusercontent.com/Chanzhaoyu/chatgpt-web/main/src/assets/avatar.jpg)

2023/5/17 18:18:49

常用的验证规则有哪些呢？

2023/5/17 18:19:13

jQuery Validation 提供的常用验证规则如下：

1.  required：必填字段验证。
2.  remote：通过 Ajax 请求来检查输入值。
3.  email：验证电子邮件格式。
4.  url：验证 URL 格式。
5.  date：验证日期格式。
6.  dateISO：验证 ISO 格式的日期（如：2018-01-01）。
7.  number：验证数字格式。
8.  digits：验证整数格式。
9.  creditcard：验证信用卡号格式。
10. equalTo：验证两个输入框的内容是否相同。
11. maxlength：限制最大长度。
12. minlength：限制最小长度。
13. rangelength：设置长度区间。
14. range：设置大小区间。
15. max：设置最大值。
16. min：设置最小值。

以上是 jQuery Validation 中比较常用的验证规则，更多可用的规则可以参考 jQuery Validation 的文档。同时，也可以通过自定义验证器来满足更复杂的验证需求。

***

好的，下面是一些简单的代码示例，演示如何使用 jQuery Validation 插件：

1.  实时验证表单输入

HTML 代码：

```
htmlCopy Code<form id="register-form">
  <div>
    <label for="email">电子邮件地址：</label>
    <input type="email" name="email" id="email" required>
    <span class="error-message"></span>
  </div>
  <div>
    <label for="password">密码：</label>
    <input type="password" name="password" id="password" required>
    <span class="error-message"></span>
  </div>
  <button type="submit">注册</button>
</form>

```

JavaScript 代码：

```
javascriptCopy Code// 初始化 jQuery Validation 插件
$('#register-form').validate({
  // 指定规则
  rules: {
    email: {
      required: true,
      email: true
    },
    password: {
      required: true,
      minlength: 6
    }
  },
  // 指定错误提示信息
  messages: {
    email: {
      required: '请输入您的电子邮件地址',
      email: '请输入有效的电子邮件地址'
    },
    password: {
      required: '请输入您的密码',
      minlength: '密码长度至少为 6 个字符'
    }
  },
  // 实时验证
  onkeyup: function(element) {
    $(element).valid();
  }
});

```

当用户在注册界面输入电子邮件地址和密码时，jQuery Validation 插件会实时验证输入的格式是否正确。如果输入不符合规则，则会在相应的输入框下显示错误提示信息。

1.  统一的错误提示

HTML 代码：

```
htmlCopy Code<form id="contact-form">
  <div>
    <label for="name">姓名：</label>
    <input type="text" name="name" id="name" required>
  </div>
  <div>
    <label for="email">电子邮件地址：</label>
    <input type="email" name="email" id="email" required>
  </div>
  <div>
    <label for="message">留言：</label>
    <textarea name="message" id="message" required></textarea>
  </div>
  <button type="submit">发送</button>
</form>

```

JavaScript 代码：

```
javascriptCopy Code// 初始化 jQuery Validation 插件
$('#contact-form').validate({
  // 指定规则
  rules: {
    name: 'required',
    email: {
      required: true,
      email: true
    },
    message: 'required'
  },
  // 指定错误提示信息
  messages: {
    name: '请输入您的姓名',
    email: {
      required: '请输入您的电子邮件地址',
      email: '请输入有效的电子邮件地址'
    },
    message: '请填写留言内容'
  },
  // 统一的错误提示
  errorPlacement: function(error, element) {
    var container = element.parents('.form-group');
    container.append(error);
  }
});

```

在联系表单中，使用了相同的错误提示容器，当用户提交留言时，如果有任何错误，则会显示在容器中的相应位置。

这里使用了 `.form-group`，表示表单字段集合的 CSS 类名。

1.  表单提交前验证

HTML 代码：

```
htmlCopy Code<form id="upload-form">
  <div>
    <label for="file">请选择要上传的文件：</label>
    <input type="file" name="file" id="file" required>
    <span class="error-message"></span>
  </div>
  <button type="submit">上传</button>
</form>

```

JavaScript 代码：

```
javascriptCopy Code// 初始化 jQuery Validation 插件
$('#upload-form').validate({
  // 指定规则
  rules: {
    file: {
      required: true,
      extension: 'pdf|doc|docx'
    }
  },
  // 指定错误提示信息
  messages: {
    file: {
      required: '请选择要上传的文件',
      extension: '只能上传 PDF、Word 文档文件'
    }
  },
  // 表单提交前验证
  submitHandler: function(form) {
    // 如果验证通过，则可以执行表单的提交操作
    form.submit();
  }
});

```

当用户在上传表单中选择了文件并点击上传按钮时，jQuery Validation 插件会在表单提交前对文件进行验证，如果文件格式不符合规则，则会显示相应的错误提示信息。

如果通过验证，则可以执行表单的提交操作。

1.  自定义验证规则

HTML 代码：

```
htmlCopy Code<form id="custom-validation-form">
  <div>
    <label for="username">用户名：</label>
    <input type="text" name="username" id="username" required>
    <span class="error-message"></span>
  </div>
  <div>
    <label for="password">密码：</label>
    <input type="password" name="password" id="password" required>
    <span class="error-message"></span>
  </div>
  <button type="submit">登录</button>
</form>

```

JavaScript 代码：

```
javascriptCopy Code// 添加自定义验证规则
$.validator.addMethod('username_format', function(value, element) {
  return /^[\w]+$/.test(value);
}, '用户名只能包含字母、数字、下划线');

// 初始化 jQuery Validation 插件
$('#custom-validation-form').validate({
  // 指定规则
  rules: {
    username: {
      required: true,
      minlength: 3,
      maxlength: 20,
      username_format: true
    },
    password: {
      required: true
    }
  },
  // 指定错误提示信息
  messages: {
    username: {
      required: '请输入您的用户名',
      minlength: '用户名长度至少为 3 个字符',
      maxlength: '用户名长度不能超过 20 个字符',
    },
    password: {
      required: '请输入您的密码'
    }
  }
});

```

在登录表单中，添加了自定义的验证规则 `username_format`，用于确保用户名只包含字母、数字和下划线。

在指定规则时，使用了自定义验证方法，并为其指定了错误提示信息。

1.  动态验证

HTML 代码：

```
htmlCopy Code<form id="dynamic-validation-form">
  <div>
    <label for="email">电子邮件地址：</label>
    <input type="email" name="email" id="email" required>
    <span class="error-message"></span>
  </div>
  <button type="button" id="add-field">添加电话号码</button>
  <button type="submit">完成</button>
</form>

```

JavaScript 代码：

```
javascriptCopy Code// 初始化 jQuery Validation 插件
$('#dynamic-validation-form').validate({
  // 指定规则
  rules: {
    email: {
      required: true,
      email: true
    }
  },
  // 指定错误提示信息
  messages: {
    email: {
      required: '请输入您的电子邮件地址',
      email: '请输入有效的电子邮件地址'
    }
  }
});

// 添加新的验证字段
var i = 1;
$('#add-field').click(function() {
  var field = $('<div><label for="phone-' + i + '">电话号码 ' + i + '：</label><input type="text" name="phone-' + i + '" id="phone-' + i + '" required><span class="error-message"></span></div>');
  $('#dynamic-validation-form').append(field);

  // 动态验证
  $('#phone-' + i).rules('add', {
    minlength: 11,
    maxlength: 11,
    digits: true
  });
  i++;
});

```

在动态表单验证中，为表单添加了一个按钮，用于动态地添加电话号码输入框。在添加新的验证字段时，需要通过 `rules()` 方法来为其添加相应的规则。

***

是的，jQuery Validation 插件还有很多其他高级的用法：

1.  自定义错误提示样式

通过 `errorElement` 和 `errorClass` 选项，可以自定义错误提示信息的外观。例如：

```
javascriptCopy Code$('#register-form').validate({
  errorElement: 'div',  // 错误提示信息使用 div 元素
  errorClass: 'error',  // 错误提示信息的 CSS 类名为 error
  // 其他选项和规则省略
});

```

这样指定后，在表单验证失败时，插件会在相应的输入框下面生成一个 `<div>` 元素，并添加 `error` 类名，以便于你使用 CSS 进行样式定制。

1.  输入框焦点切换时验证

通过 `onfocusout` 选项，可以在输入框焦点失去时进行验证。例如：

```
javascriptCopy Code$('#register-form').validate({
  onfocusout: function(element) {
    $(element).valid();
  },
  // 其他选项和规则省略
});

```

这样指定后，在用户输入完毕并移开焦点时，插件会自动对输入框进行验证。

1.  动态修改验证规则

通过 `rules()` 方法和 `resetForm()` 方法，可以动态地修改表单验证规则。例如：

```
javascriptCopy Code// 初始化 jQuery Validation 插件
$('#register-form').validate({
  rules: {
    email: {
      required: true,
      email: true
    },
    password: {
      required: true,
      minlength: 6
    }
  },
  messages: {
    // 省略错误提示信息
  }
});

// 修改规则
$('#email').rules('remove', 'email');  // 移除原有的 email 规则
$('#email').rules('add', {
  required: true,
  remote: {
    url: '/check-email-availability',
    type: 'post',
    data: {
      email: function() {
        return $('#email').val();
      }
    }
  }
});

// 重置表单
$('#register-form').validate().resetForm();

```

这样指定后，可以先使用 `remove()` 方法移除原有的 email 验证规则，再使用 `add()` 方法添加一个新的 remote 验证规则（用于检查电子邮件地址是否已经被注册），然后通过 `resetForm()` 方法重置表单，并使其重新与插件关联。

1.  异步验证

通过 `remote` 规则和 `ajax` 方法，可以实现异步验证。例如：

```
javascriptCopy Code$('#register-form').validate({
  rules: {
    email: {
      required: true,
      email: true,
      remote: {
        url: '/check-email-availability',  // 后端验证接口
        type: 'post',  // 数据发送方式
        data: {  // 发送的数据
          email: function() {
            return $('#email').val();
          }
        }
      }
    },
    password: {
      required: true,
      minlength: 6
    }
  },
  messages: {
    email: {
      required: '请输入您的电子邮件地址',
      email: '请输入有效的电子邮件地址',
      remote: '该电子邮件地址已被注册'
    },
    password: {
      required: '请输入您的密码',
      minlength: '密码长度至少为 6 个字符'
    }
  }
});

```

这样指定后，在用户输入电子邮件地址时，插件会发送一个异步请求到后端验证接口（`url`），传递当前电子邮件地址，并使用 `remote` 规则进行验证。如果后端返回的数据不符合规则，则会在表单下方显示相应的错误提示信息。

1.  自定义验证方法

通过 `addMethod()` 方法，可以自定义验证方法，用于检查输入值是否符合特定的条件。例如：

```
javascriptCopy Code$.validator.addMethod('phone', function(value, element) {
  return /^1[3-9]\d{9}$/.test(value);
}, '请输入有效的手机号码');

```

这样指定后，在验证规则中可以使用 `phone` 方法进行验证，例如：

```
javascriptCopy Code$('#register-form').validate({
  rules: {
    phone: {
      required: true,
      phone: true
    }
  },
  messages: {
    phone: {
      required: '请输入您的手机号码',
      phone: '请输入有效的手机号码'
    }
  }
});

```

这样指定后，在用户输入手机号码时，插件会使用 `phone` 方法进行验证，如果不符合规则，则会在相应的输入框下显示错误提示信息。

以上是一些 jQuery Validation 插件的高级用法，你可以根据具体情况选择适合自己的方式，用于进行表单验证。
