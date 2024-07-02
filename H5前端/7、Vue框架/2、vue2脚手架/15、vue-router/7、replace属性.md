### <router-link>的replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式

1. 浏览器的历史记录有两种写入方式：分别为push和replace，push是追加历史记录，replace是替换当前记录。路由跳转时候默认为push 。replace模式的原理就是浏览器的history中，永远只存一个历史记录。这样也就无法倒退和前进了。相当于无痕了。

1. 如何开启replace模式**：<router-link replace .......>News</router-link>**