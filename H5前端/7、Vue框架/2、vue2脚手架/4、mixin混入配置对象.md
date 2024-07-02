**代码示例：**

> **组件School.vue和Student.vue 中都引入了mixin.js 中的method和data(){} 配置的相关内容。**


# School.vue

```html
<template>
 <div>
  <h2 @click="showName">学校名称：{{name}}</h2>
  <h2>学校地址：{{address}}</h2>
 </div>
</template>
<script>
 //引入一个hunhe
  import {hunhe,hunhe2} from '../mixin'
 export default {
  name:'School',
  data() {
   return {
    name:'尚硅谷',
    address:'北京',
    x:666
   }
  },
   mixins:[hunhe,hunhe2],
 }
</script>
```

# Student.vue

```javascript
<template>
 <div>
  <h2 @click="showName">学生姓名：{{name}}</h2>
  <h2>学生性别：{{sex}}</h2>
 </div>
</template>
<script>
  import {hunhe,hunhe2} from '../mixin'
 export default {
  name:'Student',
  data() {
   return {
    name:'张三',
    sex:'男'
   }
  },
   mixins:[hunhe,hunhe2]
 }
</script>
```

# mixin.js 

```javascript
export const hunhe = {
   methods: {
      showName(){
         alert(this.name)
      }
   },
   mounted() {
      console.log('你好啊！')
   },
}
export const hunhe2 = {
   data() {
      return {
         x:100,
         y:200
      }
   },
}
```