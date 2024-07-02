# ref函数

- 作用: 定义一个响应式的数据

- 语法: const xxx = ref(initValue) 

- 创建一个包含响应式数据的

**引用对象（reference对象，简称ref对象）**

- JS中操作数据： xxx.value

- 模板中读取数据: 不需要.value，直接：<div>{{xxx}}</div>

- 备注：

- 接收的数据可以是：基本类型、也可以是对象类型。

- 基本类型的数据：响应式依然是靠Object.defineProperty()的get与set完成的。

- 对象类型的数据：内部 ***“ 求助 ”*** 了Vue3.0中的一个新函数—— reactive函数。

- reactive函数中，封住了对proxy的具体实现。

## App.vue

```javascript
<template>
 <h1>一个人的信息</h1>
 <h2>姓名：{{name}}</h2>
 <h2>年龄：{{age}}</h2>
 <h3>工作种类：{{job.type}}</h3>
 <h3>工作薪水：{{job.salary}}</h3>
 <button @click="changeInfo">修改人的信息</button>
</template>
<script>
 import {ref} from 'vue'
 export default {
  name: 'App',
  setup(){
   //数据
   let name = ref('张三')
   let age = ref(18)
   let job = ref({
    type:'前端工程师',
    salary:'30K'
   })
   //方法
   function changeInfo(){
    // name.value = '李四'
    // age.value = 48
    console.log(job.value)
    // job.value.type = 'UI设计师'
    // job.value.salary = '60K'
    // console.log(name,age)
   }
   //返回一个对象（常用）
   return {
    name,
    age,
    job,
    changeInfo
   }
  }
 }
</script>
```

# reactive函数

- 作用: 定义一个

**对象类型**

- 语法：const 代理对象= reactive(源对象)接收一个对象（或数组），返回一个**代理对象（Proxy的实例对象，简称proxy对象）**

- reactive定义的响应式数据是“深层次的”。

- 内部基于 ES6 的 Proxy 实现，通过代理对象操作源对象内部数据进行操作。

## App.vue

```javascript
<template>
 <h1>一个人的信息</h1>
 <h2>姓名：{{person.name}}</h2>
 <h2>年龄：{{person.age}}</h2>
 <h3>工作种类：{{person.job.type}}</h3>
 <h3>工作薪水：{{person.job.salary}}</h3>
 <h3>爱好：{{person.hobby}}</h3>
 <h3>测试的数据c：{{person.job.a.b.c}}</h3>
 <button @click="changeInfo">修改人的信息</button>
</template>
<script>
 import {reactive} from 'vue'
 export default {
  name: 'App',
  setup(){
   //数据
   let person = reactive({
    name:'张三',
    age:18,
    job:{
     type:'前端工程师',
     salary:'30K',
     a:{
      b:{
       c:666
      }
     }
    },
    hobby:['抽烟','喝酒','烫头']
   })
   //方法
   function changeInfo(){
    person.name = '李四'
    person.age = 48
    person.job.type = 'UI设计师'
    person.job.salary = '60K'
    person.job.a.b.c = 999
    person.hobby[0] = '学习'
   }
   //返回一个对象（常用）
   return {
    person,
    changeInfo
   }
  }
 }
</script>
```

# reactive对比ref

- 从定义数据角度对比：

- ref用来定义：**基本类型数据**。

- reactive用来定义：**对象（或数组）类型数据**。

- 备注：ref也可以用来定义**对象（或数组）类型数据**, 它内部会自动通过reactive转为**代理对象**。

- 从原理角度对比：

- ref通过Object.defineProperty()的get与set来实现响应式（数据劫持）。

- reactive通过使用**Proxy**来实现响应式（数据劫持）, 并通过**Reflect**操作**源对象**内部的数据。

- 从使用角度对比：

- ref定义的数据：操作数据**需要**.value，读取数据时模板中直接读取**不需要**.value。

- reactive定义的数据：操作数据与读取数据：**均不需要**.value。