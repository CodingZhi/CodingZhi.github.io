# VUE

[代码参考](https://gitee.com/floatingdream1001/isses-pt-repo/tree/master/vue02)

## 1. nodejs:

nodejs是前段运行js环境。

npm 用于安装 具体组件：

npm设置淘宝源：

```cmd
npm config set registry http://registry.npmmirror.com
```

npm更新npm

```cmd
npm install -g npm
```



>Vue是一套用于构建用户界面的渐进式框架，发布于2014年2月。与其它大型框架不同的是，Vue被设计为可以自底向上逐层应用。Vue的核心库只关注视图层，不仅易于上手，还便于与第三方库(如: vue-router，vue-resource,vuex）或既有项目整合。

## 2. vue-cli

安装vue脚手架

```cmd
npm install -g @vue/cli
```

vue create 项目名
![image-20230523170321645](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523170321645.png)![image-20230523170615928](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523170615928.png)

![image-20230523171134252](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523171134252.png)

![image-20230523171219467](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523171219467.png)

![image-20230523171257139](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523171257139.png)

![image-20230523171946742](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523171946742.png)

![image-20230523171353247](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523171353247.png)

中途遇到的所有`yes/no`全部yes

![image-20230523171540169](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523171540169.png)




###### vue目录介绍

![image-20230523172132754](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523172132754.png)

###### 修改端口：vue.config.js中

```js
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  devServer: {
    port: 8888
  }
})

```

![image-20230523171735475](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523171735475.png)

###### json: 数据格式。XML:数据格式

> 四个符号： `{}对象 []数组 :属性分割, 逗号`
>
```js
let stulist = [{"name": "tom", "age": 10}, {"name": "jerry", "age": 20}]
```

ts与js区别，语法更规范

## 3. package.json

dist: 发布目录

`npm run build:` 打包，打成dist用于发布。

`npm run serve` : 启动

- dependencies:   依赖： 发布后也需要的。
  - element-UI
- devdependencies:  开发时依赖。
  - less-loader

![image-20230523125035243](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523125035243.png)

依赖：

![image-20230523125206257](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523125206257.png)

语法检查是否开启

![image-20230523125327900](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523125327900.png)

public下的index.html是真正的入口

![image-20230523125536070](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523125536070.png)

这里div中app关联是src下的main.js和APP.vue

main.js相当于vue程序入口

![image-20230523125733245](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523125733245.png)

vue里带着router,状态管理和渲染App(App.vue)，将App.vue里面内容渲染到Pubblic下index里div下的app中



## 4. 路由： 一个path对应一个页面（template）

```js
import '组件名' from 'xxx.vue'

。。。
  {
    path: '/my',
    name: 'my',
        // lazy-load:  用到时在加载
       component: function() {return  import('@/views/student/index.vue')}
       component: () => import('@/views/student/index.vue')
       // 非懒加载方式
       component: 组件名
   }

```

路由模式：

- 默认：  localhost:8888/##/path

- history:  历史     localhost:8888/path

  ```js
  
  const router = new VueRouter({
    // mode: 'history',
    base: process.env.BASE_URL,
    routes
  })
  
  ```



## 5. .vue文件：

- template: 只能有一个子（vue2.0）   3.0可以有多个子标签

  ```js
  <template>
    <div>
      <h1>{{ message }}</h1>
      <h1 v-html="message"></h1>
      <input v-model="message" />
    </div>
  </template>
  ```

- script:  javascript:

  ```js
  export default {
    name: "my",
    data() { // data返回值是   数据模型datamodel
      return {
        message: "hello world",
      };
    },
    // 钩子函数：生命周期函数
    created: function () {
      debugger;
      console.info("-------created--------");
    },
    
    mounted() {
      debugger;
      console.info("-------mounted--------");
    },
    
  };
  ```

- css:   scss

```scss
// lang:用scss-loading    scoped: 作用域当前页面
<style lang="scss" scoped>

</style>
```

## 6. 钩子函数：

- beforeCreate:   数据准备好之前： this.$data= undefined
- ※ created :    数据准备好。
- beforeMount :     视图未准备好 this.$el
- ※ mounted:     render 视图和数据渲染之后
- ....

![img](https://img-blog.csdnimg.cn/20210922162545771.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5bCP5LiR6ICM5bey44CC,size_20,color_FFFFFF,t_70,g_se,x_16)





## 7: MVVM框架 ；  mv-vm

前端的三大MV*模式：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210427223508942.png)

MVVM模式：
MVVM是Model-View-ViewModel的简写。它本质上就是MVC 的改进版。MVVM 就是将其中的View 的状态和行为抽象化，让我们将视图 UI 和业务逻辑分开。当下流行的MVVM框架有：Vue.js、AngularJS。

MVVM即模型-视图-视图模型。模型指的是后端传递的数据；视图指的是所看到的页面。视图模型是mvvm模式的核心，它是连接view和model的桥梁。它有两个方向：一是将模型转化成视图，即将后端传递的数据转化成所看到的页面。实现的方式是：数据绑定。二是将视图转化成模型，即将所看到的页面转化成后端的数据。实现的方式是：DOM 事件监听。这两个方向都实现的，我们称之为数据的双向绑定。

![在这里插入图片描述](https://img-blog.csdnimg.cn/6e397959429647d1b3420d7dc5c5d94f.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3phZzY2Ng==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/569e169f4e6b424f9798fe37261d269c.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3phZzY2Ng==,size_16,color_FFFFFF,t_70)

ViewModel的作用：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210427231401384.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3phZzY2Ng==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210427232621969.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3phZzY2Ng==,size_16,color_FFFFFF,t_70)

###### MVVM的组成部分：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210427225256811.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3phZzY2Ng==,size_16,color_FFFFFF,t_70)

## 8: vue指令；

- v-bind:   可以简写成 :
- v-model:  双向绑定：用在form中的元素（下拉框、复选框等等）
- v-html:  相当于 标签内容：等同于{{}}写在标签里
- v-for:  循环    v-for="(item, index) in list"     v-for="item in list"
- v-if :  判断 + v-else
- v-on: 事件：   v-on:click=“函数” , v-on可以简写成`@`

```vue
<template>
    <div>
        <h1>{{ message }}</h1>   <!--引入-->
        <h1 v-html="message"></h1>
        <!-- 双向改变 -->
        <input v-model="message" />
        <!-- v-bind : -->
        男<input type="radio" name="sex" v-bind:checked="flg"/>  <!--单选框--><!--加name避免重复选择-->
        女<input type="radio" name="sex" :checked="!flg"/>  <!--单选框-->  <!--:checked=v-bind:checked，负责加默认-->
        
        
        <!-- for  -->
        <ul>
            <li>中国</li>
            <li>日本</li>
            <li>印度</li>
            <!-- 用做循环 v-for item接数组数据，index序号-->
            <li v-for="(item,index) in datasets" :key="index">{{ index+'@'+item }}</li>
        </ul>

    
        <!-- if-else -->
        <div v-if="username">
            有username
        </div>
        <div v-else>
            没有username
        </div>

        <!-- v-on事件 -->
        <button v-on:click="haha">v-on:click</button>
        <button @click="haha">v-on:click</button>
        <!-- v-on:click与@click一样 -->


    </div>

</template>
<script>
export default{
    name:"my",
    //双向绑定
    data(){
        return{
            flg:"true",
            message: "Hello Vue",
            datasets:['中国',"日本","印度"],
            username:"aa"
        }
    },
    methods:{  //对象，放函数
        gaga:function(){

        },
        haha(){
            this.flg=!this.flg;
        } 
        //两种方式都可以
    },
    beforeCreate(){  //数据准备之前
        debugger
        console.info("--------beforeCreate-------------")//打印
    },
    created:function(){  //数据准备完之后
        debugger
        console.info("--------created-------------")
    },
    beforeMount(){
        debugger
        console.info("--------beforeUpdate-------------")
    },
    mounted(){   //全都准备完之后
        debugger
        console.info("--------mounted-------------")
    },
    beforeUpdate(){ //数据变更之前
        debugger
        console.info("--------beforeUpdate-------------")
    },
    updated(){   //数据变更之后
        debugger
        console.info("--------updated-------------")
    },

}
</script>
```

[具体内容看官网](https://cn.vuejs.org/guide/essentials/forms.html##checkbox)

计算属性与监控器在某些方面可以互相替代，只是computed不能异步请求而watch可以异步请求

###### 父传子

prop

###### 子传父

emit

###### 父子代码

**父**

```vue
<template>
    <div>
        <h1>{{ message }}</h1>   <!--引入-->
        <h1 v-html="message"></h1>
        <!-- 双向改变 -->
        <input v-model="message" />
        <!-- v-bind : -->
        男<input type="radio" name="sex" v-bind:checked="flg"/>  <!--单选框--><!--加name避免重复选择-->
        女<input type="radio" name="sex" :checked="!flg"/>  <!--单选框-->  <!--:checked=v-bind:checked，负责加默认-->
        
        
        <!-- for  -->
        <ul>
            <li>中国</li>
            <li>日本</li>
            <li>印度</li>
            <!-- 用做循环 v-for item接数组数据，index序号-->
            <li v-for="(item,index) in datasets" :key="index">{{ index+'@'+item }}</li>
        </ul>

    
        <!-- if-else -->
        <div v-if="username">
            有username
        </div>
        <div v-else>
            没有username
        </div>

        <!-- v-on事件 -->
        <button v-on:click="haha">v-on:click</button>
        <button @click="haha">v-on:click</button>
        <!-- v-on:click与@click一样 -->

        <!-- 监听器 -->
        <input type="text" v-model="firstname" placeholder="first name"/>
        <input type="text" v-model="lastname" placeholder="last name"/>
        <input type="text" v-model="fullname" placeholder="full name" ref="aaa"/>  <!--ref一般给组件用,它可以给此时这一行的样式起名字-->

        <hr>
        <my-tag :mylist="mylist" :defaultv="defaultv" @sendtofu="reserve"></my-tag><!-- :mylist是prop 而后面mylist是此时对应vue的mylist-->
        <!--父给子传:mylist="mylist" :defaultv="defaultv"-->
        <!--子给父传sendtofu,通过事件来接收-->

        <!-- 父接子传 -->
        <!-- <button @click="showvalue">getvalue</button> -->


    </div>

</template>
<script>
    //引入组件
import MyTag from '@/components/myTag.vue';
export default{
    //来引用components下的vue
    components:{
        //MyTag--->驼峰 my-tag
        MyTag:MyTag,
    },
    name:"my",
    //双向绑定
    data(){
        return{
            flg:"true",
            message: "Hello Vue",
            datasets:['中国',"日本","印度"],
            username:"aa",
            firstname:'',
            lastname:'',
            defaultv:"00001",
            // fullname:'', //可以被替代，通过计算出来
            datasets0:[{lable:'中国'},{lable:"日本"},{lable:"印度"}],
            mylist:[{value:"00001", label:"北京"},
            {value:"00002", label:"上海"},
            {value:"00003", label:"西安"},
            {value:"00004", label:"背景"},]
        
        }
    },
    //计算属性
    computed:{
        fullname(){
            return this.fullname=this.firstname+" "+this.lastname
        }
    },
    watch:{ //对象，放入监控的数据
        // firstname(n,o){
        //     this.fullname=this.firstname+" "+this.lastname
        // },
        // //深度监控加上deep
        // datasets0:{
        //     handler(n,o){

        //     },
        //     deep:true
        // },

        // lastname(n,o){
        //     this.fullname=this.firstname+" "+this.lastname
        // }
    },
    methods:{  //对象，放函数,触发
        gaga:function(){

        },
        haha(){
            this.flg=!this.flg;
        } ,
        //两种方式都可以
        // showvalue(){
        //     alert(this.defaultv) //弹窗
        // },
        reserve(ddd){
            // this.defaultv=ddd;
            alert(ddd)
        }
    },
    beforeCreate(){  //数据准备之前
        
        console.info("--------beforeCreate-------------")//打印
    },
    created:function(){  //数据准备完之后
        
        console.info("--------created-------------")
    },
    beforeMount(){
        
        console.info("--------beforeUpdate-------------")
    },
    mounted(){   //全都准备完之后
        console.info(this.$refs.aaa.style.background="##FF0000")
        console.info("--------mounted-------------")
    },
    beforeUpdate(){ //数据变更之前
        
        console.info("--------beforeUpdate-------------")
    },
    updated(){   //数据变更之后
        
        console.info("--------updated-------------")
    },

}
</script>
<!-- scoped只在当前界面有效 -->
<style lang="sass" scoped>   

</style>

```

**子**

```vue
<!-- 在使用中引用 -->
<template>
    <div>
        <select v-model="mt">
            <option value="00001">北京</option>
            <option value="00002">上海</option>
            <option value="00003">西安</option>
            <option value="00004">背景</option>
        </select>
        <br>
        <select value="defaultv" @change="showv" v-model="m1">
            <option v-for="(item,index) in mylist" :key="index" :value="item.value">{{ item.label }}</option>
        </select>
    </div>
</template>
<script>
export default{
    data(){
        return{
            mt:"00002",
            m1:this.defaultv,

        //     mylist:[{value:"00001", label:"北京"},
        //     {value:"00002", label:"上海"},
        //     {value:"00003", label:"西安"},
        //     {value:"00004", label:"背景"},]
        }
    },
    //从页面把列表传进来
    props:["mylist","defaultv"],
    methods:{
        showv(){
            //alert(this.defaultv),
            this.$emit('sendtofu',this.m1)//把defaultv传给sendtofu，用来给父接收
        }
    }

}
</script>
```

###### 最后结果

![image-20230523222634550](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230523222634550.png)

###### 卡槽

![image-20230524152630310](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230524152630310.png)

父：

```vue
			<template>
                匿名卡槽
            </template>
            <template slot="header">
                具名卡槽
    		</template>
```

子：

```vue
<slot name="header"></slot><!--卡槽-->
        <select value="defaultv" @change="showv" v-model="m1">
            <option v-for="(item,index) in mylist" :key="index" :value="item.value">{{ item.label }}</option>
        </select>
        <slot></slot>
```

![image-20230524153234713](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230524153234713.png)

###### vuex状态



![image-20230524210010366](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230524210010366.png)

**store下**

```js
import Vue from "vue";
import Vuex from "vuex";

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    //存数据
  },
  getters: {
    //获取数据
  },
  mutations: {
    //修改数据
  },
  actions: {
    //修改mutations，修改异步数据
  },
  modules: {
    //包含多个状态
  },
});
```

通过存数据，并获取数据

```js
state: {
    //存数据
    count:100
  },
  getters: {
    //获取数据
    getCount(state) {return state.count}
  },
```

获取数据

```vue
<h1>count:{{ this.$store.getters.getCount }}</h1>  <!--或者在设变量在data下获取，但无法时时更新-->
```

修改数据(自加)，在页面通过点击事件，激发

```js
  mutations: {
    //修改数据
    setCount(state){
      state.count++
    }
  },
```

异步修改数据(1秒后，通过mutations修改数据实现自加)

```js
  actions: {
    //修改mutations，修改异步数据
    ajexcount(state) {
      setTimeout(() => {
        state.commit("setCount")
      },1000)
    }
  },
```
**案例**

```vue
<template>
    <div>
        <h1>count:{{ this.$store.getters.getCount }}</h1>
        <el-input-number v-model="num" @change="handleChange" :min="1" :max="10" label="描述文字"></el-input-number>
        <el-button type="warning" icon="el-icon-star-off" circle @click="addadd">mutations</el-button><br>
        <el-button type="info" icon="el-icon-message" circle @click="aaddd1">actions</el-button>

    </div>
</template>
<script>
export default {
    data() {
        return {
            
            num: 1
        };
    },
    methods: {
        handleChange(value) {
            console.log(value);
        },
        addadd() {
            this.$store.commit("setCount")
        },
        aaddd1(){
            this.$store.dispatch("ajexcount")
        }

    
    }
};
</script>
```

![image-20230524210917860](https://cdn.staticaly.com/gh/FloatingDream1001/markdown_blog@main/2023/6/image-20230524210917860.png)
