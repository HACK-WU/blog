# 1、main.js

```javascript
//引入Vue
import Vue from 'vue'
//引入App
import App from './App.vue'
//引入插件
import vueResource from 'vue-resource'
//关闭Vue的生产提示
Vue.config.productionTip = false
//使用插件
Vue.use(vueResource)
//创建vm
new Vue({
   el:'#app',
   render: h => h(App),
   beforeCreate() {
      Vue.prototype.$bus = this
   }
})
```

# 2、App.vue

```javascript
<template>
 <div>
  <Count/>
 </div>
</template>
<script>
 import Count from './components/Count'
 export default {
  name:'App',
  components:{Count},
 }
</script>
```

# 3、Count.vue

```javascript
<template>
 <div>
  <h1>当前求和为：{{sum}}</h1>
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
    sum:0 //当前的和
   }
  },
  methods: {
   increment(){
    this.sum += this.n
   },
   decrement(){
    this.sum -= this.n
   },
   incrementOdd(){
    if(this.sum % 2){
     this.sum += this.n
    }
   },
   incrementWait(){
    setTimeout(()=>{
     this.sum += this.n
    },500)
   },
  },
 }
</script>
<style lang="css">
 button{
  margin-left: 5px;
 }
</style>
```