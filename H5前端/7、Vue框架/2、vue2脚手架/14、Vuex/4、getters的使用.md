# 1、gettters的使用

1. 概念：当state中的数据需要经过加工后再使用时，可以使用getters加工。

1. 在store.js中追加getters配置

```javascript
......
​
const getters = {
    bigSum(state){
        return state.sum * 10
    }
}
​
//创建并暴露store
export default new Vuex.Store({
    ......
    getters
})
```

1. 组件中读取数据：$store.getters.bigSum

# **2、求和案例：**

## main.js

```javascript
//引入Vue
import Vue from 'vue'
//引入App
import App from './App.vue'
//引入插件
import vueResource from 'vue-resource'
//引入store
import store from './store'
//关闭Vue的生产提示
Vue.config.productionTip = false
//使用插件
Vue.use(vueResource)
//创建vm
new Vue({
   el:'#app',
   render: h => h(App),
   store,
   beforeCreate() {
      Vue.prototype.$bus = this
   }
})
```

## stroe目录(Vuex)

- index.js

```javascript
//该文件用于创建Vuex中最为核心的store
import Vue from 'vue'
//引入Vuex
import Vuex from 'vuex'
//应用Vuex插件
Vue.use(Vuex)
//准备actions——用于响应组件中的动作
const actions = {
   /* jia(context,value){
      console.log('actions中的jia被调用了')
      context.commit('JIA',value)
   },
   jian(context,value){
      console.log('actions中的jian被调用了')
      context.commit('JIAN',value)
   }, */
   jiaOdd(context,value){
      console.log('actions中的jiaOdd被调用了')
      if(context.state.sum % 2){
         context.commit('JIA',value)
      }
   },
   jiaWait(context,value){
      console.log('actions中的jiaWait被调用了')
      setTimeout(()=>{
         context.commit('JIA',value)
      },500)
   }
}
//准备mutations——用于操作数据（state）
const mutations = {
   JIA(state,value){
      console.log('mutations中的JIA被调用了')
      state.sum += value
   },
   JIAN(state,value){
      console.log('mutations中的JIAN被调用了')
      state.sum -= value
   }
}
//准备state——用于存储数据
const state = {
   sum:0 //当前的和
}
//准备getters——用于将state中的数据进行加工
const getters = {
   bigSum(state){
      return state.sum*10
   }
}
//创建并暴露store
export default new Vuex.Store({
   actions,
   mutations,
   state,
   getters
})
```

## **App.vue**

```javascript
<template>
 <div>
  <Count/>
 </div>
</template>
<script>
 import Count from './components/Count.vue'
 export default {
  name:'App',
  components:{Count},
  mounted() {
   // console.log('App',this)
  },
 }
</script>
```

## Count.vue

```javascript
<template>
 <div>
  <h1>当前求和为：{{$store.state.sum}}</h1>
  <h3>当前求和放大10倍为：{{$store.getters.bigSum}}</h3>
  <select v-model.number="n">
   <option value="1">1</option>
   <option value="2">2</option>
   <option value="3">3</option>
  </select>
  <button @click="increment">+</button>
  <button @click="decrement">-</button>
  <button @click="incrementOdd">当前求和为奇数再加</button>
  <button @click="incrementWait">等一等再加</button>
 </div>
</template>
<script>
 export default {
  name:'Count',
  data() {
   return {
    n:1, //用户选择的数字
   }
  },
  methods: {
   increment(){
    this.$store.commit('JIA',this.n)
   },
   decrement(){
    this.$store.commit('JIAN',this.n)
   },
   incrementOdd(){
    this.$store.dispatch('jiaOdd',this.n)
   },
   incrementWait(){
    this.$store.dispatch('jiaWait',this.n)
   },
  },
  mounted() {
   console.log('Count',this.$store)
  },
 }
</script>
<style lang="css">
 button{
  margin-left: 5px;
 }
</style>
```