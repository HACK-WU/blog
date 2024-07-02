# 1、map方法的使用

1. mapState方法：

- 用于帮助我们映射state中的数据为计算属性

```javascript
computed: {
    //借助mapState生成计算属性：sum、school、subject（对象写法）
     ...mapState({sum:'sum',school:'school',subject:'subject'}),
         
    //借助mapState生成计算属性：sum、school、subject（数组写法）
    ...mapState(['sum','school','subject']),
},
```

1. mapGetters方法：

- 用于帮助我们映射getters中的数据为计算属性

```javascript
computed: {
    //借助mapGetters生成计算属性：bigSum（对象写法）
    ...mapGetters({bigSum:'bigSum'}),
​
    //借助mapGetters生成计算属性：bigSum（数组写法）
    ...mapGetters(['bigSum'])
},
```

1. mapActions方法：

- 用于帮助我们生成与actions对话的方法，即：包含$store.dispatch(xxx)的函数

```javascript
methods:{
    //靠mapActions生成：incrementOdd、incrementWait（对象形式）
    ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
​
    //靠mapActions生成：incrementOdd、incrementWait（数组形式）
    ...mapActions(['jiaOdd','jiaWait'])
}
```

1. mapMutations方法：

- 用于帮助我们生成与mutations对话的方法，即：包含$store.commit(xxx)的函数

```javascript
methods:{
    //靠mapActions生成：increment、decrement（对象形式）
    ...mapMutations({increment:'JIA',decrement:'JIAN'}),
    
    //靠mapMutations生成：JIA、JIAN（对象形式）
    ...mapMutations(['JIA','JIAN']),
}
```

> 备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。


# 2、求和案例_mapMutations与mapActions

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
   sum:0, //当前的和
   school:'尚硅谷',
   subject:'前端'
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

## App.vue

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
  <h1>当前求和为：{{sum}}</h1>
  <h3>当前求和放大10倍为：{{bigSum}}</h3>
  <h3>我在{{school}}，学习{{subject}}</h3>
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
 import {mapState,mapGetters} from 'vuex'
 export default {
  name:'Count',
  data() {
   return {
    n:1, //用户选择的数字
   }
  },
  computed:{
   //靠程序员自己亲自去写计算属性
   /* sum(){
    return this.$store.state.sum
   },
   school(){
    return this.$store.state.school
   },
   subject(){
    return this.$store.state.subject
   }, */
   //借助mapState生成计算属性，从state中读取数据。（对象写法）
   // ...mapState({he:'sum',xuexiao:'school',xueke:'subject'}),
   //借助mapState生成计算属性，从state中读取数据。（数组写法）
   ...mapState(['sum','school','subject']),
   /* ******************************************************************** */
   /* bigSum(){
    return this.$store.getters.bigSum
   }, */
   //借助mapGetters生成计算属性，从getters中读取数据。（对象写法）
   // ...mapGetters({bigSum:'bigSum'})
   
   //借助mapGetters生成计算属性，从getters中读取数据。（数组写法）
   ...mapGetters(['bigSum'])
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
   const x = mapState({he:'sum',xuexiao:'school',xueke:'subject'})
   console.log(x)
  },
 }
</script>
<style lang="css">
 button{
  margin-left: 5px;
 }
</style>
```

# 3、求和案例_mapMutations与mapActions

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

## stroe目录(vuex)

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
   sum:0, //当前的和
   school:'尚硅谷',
   subject:'前端'
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

## App.vue

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
  <h1>当前求和为：{{sum}}</h1>
  <h3>当前求和放大10倍为：{{bigSum}}</h3>
  <h3>我在{{school}}，学习{{subject}}</h3>
  <select v-model.number="n">
   <option value="1">1</option>
   <option value="2">2</option>
   <option value="3">3</option>
  </select>
  <button @click="increment(n)">+</button>
  <button @click="decrement(n)">-</button>
  <button @click="incrementOdd(n)">当前求和为奇数再加</button>
  <button @click="incrementWait(n)">等一等再加</button>
 </div>
</template>
<script>
 import {mapState,mapGetters,mapMutations,mapActions} from 'vuex'
 export default {
  name:'Count',
  data() {
   return {
    n:1, //用户选择的数字
   }
  },
  computed:{
   //借助mapState生成计算属性，从state中读取数据。（对象写法）
   // ...mapState({he:'sum',xuexiao:'school',xueke:'subject'}),
   //借助mapState生成计算属性，从state中读取数据。（数组写法）
   ...mapState(['sum','school','subject']),
   /* ******************************************************************** */
   //借助mapGetters生成计算属性，从getters中读取数据。（对象写法）
   // ...mapGetters({bigSum:'bigSum'})
   
   //借助mapGetters生成计算属性，从getters中读取数据。（数组写法）
   ...mapGetters(['bigSum'])
  },
  methods: {
   //程序员亲自写方法
   /* increment(){
    this.$store.commit('JIA',this.n)
   },
   decrement(){
    this.$store.commit('JIAN',this.n)
   }, */
   //借助mapMutations生成对应的方法，方法中会调用commit去联系mutations(对象写法)
   ...mapMutations({increment:'JIA',decrement:'JIAN'}),
   //借助mapMutations生成对应的方法，方法中会调用commit去联系mutations(数组写法)
   // ...mapMutations(['JIA','JIAN']),
   /* ************************************************* */
   //程序员亲自写方法
   /* incrementOdd(){
    this.$store.dispatch('jiaOdd',this.n)
   },
   incrementWait(){
    this.$store.dispatch('jiaWait',this.n)
   }, */
   //借助mapActions生成对应的方法，方法中会调用dispatch去联系actions(对象写法)
   ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   //借助mapActions生成对应的方法，方法中会调用dispatch去联系actions(数组写法)
   // ...mapActions(['jiaOdd','jiaWait'])
  },
  mounted() {
   const x = mapState({he:'sum',xuexiao:'school',xueke:'subject'})
   console.log(x)
  },
 }
</script>
<style lang="css">
 button{
  margin-left: 5px;
 }
</style>
```