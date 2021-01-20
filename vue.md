# Vue
## Vue全家桶
### Vue基础指令
### Vue高级指令
+ solt插槽
>vue2.6.0之后采用v-soltv语法取代了之前的solt、 solt-scope语法
1. 匿名插槽
```javascript
    // comp1
    <div>
        <solt></solt>
    </div>
    // parent
    <comp1>hello</copm1>
```
2. 具名插槽
```javascript
    // comp2
    <div>
        <solt></solt>
        <solt name="content"></solt>
    </div>
    // parent
    <comp2>
        <template v-solt:default>默认内容</template>
        <template v-solt:content>content内容</template>
    </copm2>
```
3. 作用域插槽
```javascript
    // comp2
    <div>
        <solt :foo="foo"></solt>
    </div>
    // parent foo数据来源与子组件
    <comp3>
        <template v-solt:default="ctx">
            {{ctx:foo}}
        </template>
    </copm3>
```
+ 递归组件
>递归组件可以在自己的模版中调用自身的组件
```javascript
    // Node 组件
    <template>
        <div>
            <h3>{{data.title}}</h3>
            <Node v-for="d in data.children" :key="d.id" :data="d"></Node>
        </div>
    </template>
    <script>
        export default {
            name: 'Node',
            props: {
                data: {
                    type: object,
                    require: true,
                }
            }
        }
    </script>  
```

### Vue动画与自定义指令
### Vue组件基础
### 组件通信
+ 父子组件：props属性
```javascript
    <Helloworld msg="hello">
```
+ 子父组件：refs引用 
```javascript
    <Helloworld ref="hw">

    this.$refs.hw.xx
```
+ 父子组件：自定义事件 
```javascript
    // child
    this.$emit('eventname', msg)
    // paeent
    <Parent @eventname={$event} />
```
+ 兄弟组件传值：通过共同的祖辈组件 
```javascript
    // comp1
    this.$parent.$on('foo', handle);
    // comp2
    this.$parent.$emit('foo');
```
+ 祖先给后代传值：provide/inject 
```javascript
    // 祖先组件
    provide(){
        return {'foo': foo}
    }
    //后代组件
    inject: ['foo']
```
+ 后代给祖先传值：dispatch 逐层往上面派发
+ 任意组件传值：事件总线或者Vuex
```javascript
    // 事件总线, 实践中 Vue对象已经实现了事件总线
    Class Bus{
        constructor(){
            this.clallbacks = {}
        }

        $on(name, fn){
            this.clallbacks[name] = this.clallbacks[name] || [];
            this.clallbacks[name].push(fn);
        }

        $.emit(name, args){
            if(this.clallbacks[name]){
               this.clallbacks[name].forEach(cb => cb(args)); 
            }
        }
    }

    Vue.prototype.$bus = new Bus();
    this.$bus.$on('foo', handle);
    this.$bus.$emit('foo', name);
```
    Vue插件与过滤器
    vue-router
    Vuex状态管理
    Vue Devtools
    Element-UI组件库
### Vue组件化设计
#### 提示框(全局组件实现)
+ Vue实例之外单独存在、挂在body上面，通过js动态创建

#### 表单(Form)
+ v-model实现
+ inheritAttrs和 $attrs


#### 递归组件实现：树
#### 全家桶源码实战
    Vue源码解读
    手写Vue
    Vue-router源码实战
    Vuex源码实战
#### TypeScript
    ts介绍
    ts环境配置
    ts编译命令
    类型系统、接口
    类型深入、函数、类
    泛型、装饰器、模块系统
    高级主图
#### 服务端渲染SSR
    原生SSR
    SSR框架nuxt.js实战