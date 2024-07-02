# 代码示例：App.vue

```javascript
<template>
 <div>
  <h1 v-text="msg" ref="title"></h1>
  <button ref="btn" @click="showDOM">点我输出上方的DOM元素</button>
  <School ref="sch"/>
 </div>
</template>
<script>
 //引入School组件
 import School from './components/School.vue'
 export default {
  name:'App',
  components:{School},
  data() {
   return {
    msg:'欢迎学习Vue！'
   }
  },
  methods: {
   showDOM(){
    console.log(this.$refs.title) //真实DOM元素
    console.log(this.$refs.title.textContent)   拿到真实DOM元素中的所有文本内容
    console.log(this.$refs.title.textContent)   拿到真实DOM元素中的可见文本内容
    console.log(this.$refs.title.innerHTML)   拿到真实DOM元素中的HTML结构和内容
    console.log(this.$refs.btn) //真实DOM元素
    console.log(this.$refs.sch) //School组件的实例对象（vc）
   }
  },
 }
</script>
```