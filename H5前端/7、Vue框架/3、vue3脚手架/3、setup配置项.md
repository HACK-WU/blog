# 拉开序幕的setup

1. 理解：Vue3.0中一个新的配置项，值为一个函数。

1. setup是所有**Composition API（组合API）** 表演的舞台 。

1. 组件中所用到的：数据、方法等等，均要配置在setup中。

1. setup函数的两种返回值：

1. 若返回一个对象，则对象中的属性、方法, 在模板中均可以直接使用。（重点关注！）

1. 若返回一个渲染函数：则可以自定义渲染内容。（了解）</span>

1. 注意点：

1. 尽量不要与Vue2.x配置混用


- Vue2.x配置（data、methos、computed...）中可以访问到setup中的属性、方法。

- 但在setup中不能访问到Vue2.x配置（data、methos、computed...）。

- 如果有重名, setup优先。

1. setup不能是一个async函数，因为返回值不再是return的对象, 而是promise, 模板看不到return对象中的属性。（后期也可以返回一个Promise实例，但需要Suspense和异步组件的配合）

# 2、代码示例

- App.Vue

```javascript
<template>
 <h1>一个人的信息</h1>
 <h2>姓名：{{name}}</h2>
 <h2>年龄：{{age}}</h2>
 <h2>性别：{{sex}}</h2>
 <h2>a的值是：{{a}}</h2>
 <button @click="sayHello">说话(Vue3所配置的——sayHello)</button>
 <br>
 <br>
 <button @click="sayWelcome">说话(Vue2所配置的——sayWelcome)</button>
 <br>
 <br>
 <button @click="test1">测试一下在Vue2的配置中去读取Vue3中的数据、方法</button>
 <br>
 <br>
 <button @click="test2">测试一下在Vue3的setup配置中去读取Vue2中的数据、方法</button>
</template>
<script>
 // import {h} from 'vue'
 export default {
  name: 'App',
  data() {
   return {
    sex:'男',
    a:100
   }
  },
  methods: {
   sayWelcome(){
    alert('欢迎来到尚硅谷学习')
   },
   test1(){
    console.log(this.sex)
    console.log(this.name)
    console.log(this.age)
    console.log(this.sayHello)
   }
  },
  //此处只是测试一下setup，暂时不考虑响应式的问题。
  async setup(){
   //数据
   let name = '张三'
   let age = 18
   let a = 200
   //方法
   function sayHello(){
    alert(`我叫${name}，我${age}岁了，你好啊！`)
   }
   function test2(){
    console.log(name)
    console.log(age)
    console.log(sayHello)
    console.log(this.sex)
    console.log(this.sayWelcome)
   }
   //返回一个对象（常用）
   return {
    name,
    age,
        a,
    sayHello,
    test2,
   }
   //返回一个函数（渲染函数）
   // return ()=> h('h1','尚硅谷')
  }
 }
</script>
```

# 2、setup的两个注意点

- setup执行的时机

- 在beforeCreate之前执行一次，this是undefined。

- setup的参数

- props：值为对象，包含：组件外部传递过来，且组件内部声明接收了的属性。

- context：上下文对象

- attrs: 值为对象，包含：组件外部传递过来，但没有在props配置中声明的属性, 相当于 this.$attrs。

- slots: 收到的插槽内容, 相当于 this.$slots。

- emit: 分发自定义事件的函数, 相当于 this.$emit。

## App.vue

```javascript
<template>
 <Demo @hello="showHelloMsg" msg="你好啊" school="尚硅谷">
  <template v-slot:qwe>
   <span>尚硅谷</span>
  </template>
  <template v-slot:asd>
   <span>尚硅谷</span>
  </template>
 </Demo>
</template>
<script>
 import Demo from './components/Demo'
 export default {
  name: 'App',
  components:{Demo},
  setup(){
   function showHelloMsg(value){
    alert(`你好啊，你触发了hello事件，我收到的参数是:${value}！`)
   }
   return {
    showHelloMsg
   }
  }
 }
</script>
```

## Demo.vue

```javascript
<template>
 <h1>一个人的信息</h1>
 <h2>姓名：{{person.name}}</h2>
 <h2>年龄：{{person.age}}</h2>
 <button @click="test">测试触发一下Demo组件的Hello事件</button>
</template>
<script>
 import {reactive} from 'vue'
 export default {
  name: 'Demo',
  props:['msg','school'],            
  emits:['hello'],    //Vue3新增的配置项，如果在父组件中在子组件中设置了自定义事件，在子组件中需要使用此配置项进行什么接收。
  setup(props,context){   
   //props是一个Poxy对象，其中封装了父组件传递过来的参数。是一个Proxy对象。
    //    接收props参数，需要使用props配置项，声明接收。
    //     在setup中无法使用this,可以使用content对象代替。
   // console.log('---setup---',props)
   // console.log('---setup---',context)
   // console.log('---setup---',context.attrs) //相当与Vue2中的$attrs
   // console.log('---setup---',context.emit) //触发自定义事件的。
   console.log('---setup---',context.slots) //插槽
   //数据
   let person = reactive({
    name:'张三',
    age:18
   })
   //方法
   function test(){
    context.emit('hello',666)
   }
   //返回一个对象（常用）
   return {
    person,
    test
   }
  }
 }
</script>
```