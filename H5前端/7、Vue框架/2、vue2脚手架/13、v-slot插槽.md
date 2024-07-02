# 插槽

1. 作用：让父组件可以向子组件指定位置插入html结构，也是一种组件间通信的方式，适用于 **父组件 ===> 子组件** 。

1. 分类：默认插槽、具名插槽、作用域插槽

# 默认插槽：

```html
父组件中：
        <Category>    <!--Category子组件的名称-->
           <div>html结构1</div>
        </Category>
子组件中：
        <template>
            <div>
               <!-- 定义插槽 -->  <!-- 新版本是v-solt-->
               <slot>插槽默认内容...</slot>    
            </div>
        </template>
```

# 具名插槽：

```html
父组件中：
        <Category>
            <template slot="center">
              <div>html结构1</div>
            </template>
​
            <template v-slot:footer>
               <div>html结构2</div>
            </template>
        </Category>
        
子组件中：
        <template>
            <div>
               <!-- 定义插槽 -->
                <!-- 新版本是v-solt-->
               <slot name="center">插槽默认内容...</slot>
               <slot name="footer">插槽默认内容...</slot>
            </div>
        </template>
```

# 作用域插槽：

1. 理解：数据在子组件的自身，但根据数据生成的结构需要父组件来决定。（games数据在Category组件中，但使用数据所遍历出来的结构由App组件决定）

1. 具体编码：

```html
父组件中：
        <Category>
            <!-- scope也支持解构赋值，比如：scope="{games}"-->
            <!--Vue2.6 以后使用 solt-scope-->
            <template scope="scopeData"><!--必须要使用template标签和scope属性，scopeData表示的就是作用域-->    
                <!-- 生成的是ul列表 -->
                <ul>
                    <li v-for="g in scopeData.games" :key="g">{{g}}</li>
                    <!--scopeData.games：表示的就是scopeData作用域下的games变量-->
                </ul>
            </template>
        </Category>
​
        <Category>
            <template slot-scope="scopeData">
                <!-- 生成的是h4标题 -->
                <h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
            </template>
        </Category>
子组件中：
        <template>
            <div>
                <slot :games="games"></slot>
            </div>
        </template>
        
        <script>
            export default {
                name:'Category',
                props:['title'],
                //数据在子组件自身
                data() {
                    return {
                        games:['红色警戒','穿越火线','劲舞团','超级玛丽']
                    }
                },
            }
        </script>
```