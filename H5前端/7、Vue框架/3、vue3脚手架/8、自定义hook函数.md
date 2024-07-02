# 自定义hook函数

- 什么是hook？—— 本质是一个函数，把setup函数中使用的Composition API进行了封装。

- 类似于vue2.x中的mixin。

- 自定义hook的优势: 复用代码, 让setup中的逻辑更清楚易懂。

# 示例

## hooks目录

- usePoint.js

```javascript
<template>
 <h2>当前求和为：{{sum}}</h2>
 <button @click="sum++">点我+1</button>
 <hr>
 <h2>当前点击时鼠标的坐标为：x：{{point.x}}，y：{{point.y}}</h2>
</template>
<script>
 import {ref} from 'vue'
 import usePoint from '../hooks/usePoint'
 export default {
  name: 'Demo',
  setup(){
   //数据
   let sum = ref(0)
   let point = usePoint()
   
   //返回一个对象（常用）
   return {sum,point}
  }
 }
</script>
```

# Demo.vue

```javascript
<template>
 <h2>当前求和为：{{sum}}</h2>
 <button @click="sum++">点我+1</button>
 <hr>
 <h2>当前点击时鼠标的坐标为：x：{{point.x}}，y：{{point.y}}</h2>
</template>
<script>
 import {ref} from 'vue'
 import usePoint from '../hooks/usePoint'
 export default {
  name: 'Demo',
  setup(){
   //数据
   let sum = ref(0)
   let point = usePoint()
   
   //返回一个对象（常用）
   return {sum,point}
  }
 }
</script>
```