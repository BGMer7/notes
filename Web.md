# Web

### JQuery & Vue

jQuery是使用选择器（$）选取DOM对象，对其进行赋值、取值、事件绑定等操作，**其实和原生的HTML的区别只在于可以更方便的选取和操作DOM对象，而数据和界面是在一起的**。比如需要获取label标签的内容：`$("lable").val();`,**它还是依赖DOM元素的值**。 

Vue则是**通过Vue对象将数据和View完全分离开来了**。对数据进行操作不再需要引用相应的DOM对象，可以说数据和View是分离的，他们通过Vue对象这个vm实现相互的绑定，这也就是MVVM。



**场景一：**列表添加一个元素,下图为vue和jquery两种操作的代码，我们从中可以看出vue只需要向数据message里面push一条数据即可完成添加一个li标签的操作，而jquery则需要获取dom元素节点，并对dom进行添加一个标签的操作，如果dom结构特别复杂，或者添加的元素非常复杂，则代码会变得非常复杂且阅读性低。



vue写法

```html
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
</head>

<body>
    <div id="app">
        <ul>
            <!--根据数组数据自动渲染页面-->
            <li v-for="item in message">{{item}}</li>
        </ul>
        <button @click="add">添加数据</button>
    </div>
</body>

<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: ["第1条数据","第2条数据"],
            i:2
        },
        methods:{
            //向数组添加一条数据即可
            add:function(){
                this.i++
                this.message.push("第"+this.i+"条数据")
            }
        }
    })
</script>
```



jQuery写法

```html
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
</head>

<body>
    <div id="app">
        <ul id="list">
            <li>第1条数据</li>
            <li>第2条数据</li>
        </ul>
        <button id="add">添加数据</button>
    </div>

</body>

<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
<script>
    $(document).ready(function() {  
    var i=2;
    $('#add').click(function() { 
       i++; 
       //通过dom操作在最后一个li元素后手动添加一个标签
      $("#list").children("li").last().append("<li>第"+i+"条数据</li>")
    });  
  }); 
</script>
```

vue的核心代码通过一个for循环控制，`<li v-for="item in message">{{item}}</li>`，按钮绑定一个函数，只需要在message后面添加数据，vue会监听到**message的数据**发生了变化，对HTML元素重新渲染。

jQuery的核心代码先通过id选择器找到list，然后在list的子元素中找到最后一个，在最后一个的后面加上html语言，其中涉及到了DOM的很多逻辑关系，如果不熟悉页面元素设计的话，逻辑会非常难理顺。



**场景二：**控制按钮的显示隐藏，下图为vue和jquery两种操作的代码，我们从中可以看出vue只需要控制属性isShow的值为true和false即可，而jquery则还是需要操作dom元素控制按钮的显示和隐藏。



vue写法

```html
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
</head>

<body>
    <div id="app">
        <ul>
            <!--根据数组数据自动渲染页面-->
            <li v-for="item in message">{{item}}</li>
        </ul>
        <button @click="add" v-show="isShow">添加数据</button>
        <button @click="showButton">隐藏按钮</button>
    </div>
</body>

<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            message: ["第1条数据","第2条数据"],
            i:2,
            isShow:true
        },
        methods:{
            //向数组添加一条数据即可
            add:function(){
                this.i++
                this.message.push("第"+this.i+"条数据")
            },
            //控制isShow的值即可
            showButton:function(){
                this.isShow=false;
            }
        }
    })
</script>
```



jQuery写法

```html
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8" />
</head>

<body>
    <div id="app">
        <ul id="list">
            <li>第1条数据</li>
            <li>第2条数据</li>
        </ul>
        <button id="add">添加数据</button>
        <button id="showButton">隐藏按钮</button>
    </div>

</body>

<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
<script>
    $(document).ready(function() {  
    var i=2;
    $('#add').click(function() { 
       i++; 
       //通过dom操作在最后一个li元素后手动添加一个标签
      $("#list").children("li").last().append("<li>第"+i+"条数据</li>")
    });  
    //需要手动隐藏dom元素
    $("#showButton").click(function(){
        $("#add").hide()
    })
  }); 
</script>
```

vue通过更改这个vue元素的属性，数据更改之后重新渲染。

jQuery需要调用选择器之后使用api方法，再去通过js修改DOM的属性。



 vue适用的场景：复杂数据操作的后台页面，表单填写页面

jquery适用的场景：比如说一些html5的动画页面，一些需要js来操作页面样式的页面

然而二者也是可以结合起来一起使用的，vue侧重数据绑定，jquery侧重样式操作，动画效果等，则会更加高效率的完成业务需求

 

## ES6

### export default

ES6模块主要有两个功能：export和import
export用于对外输出本模块（一个文件可以理解为一个模块）变量的接口
import用于在一个模块中加载另一个含有export接口的模块。
也就是说使用export命令定义了模块的对外接口以后，其他JS文件就可以通过import命令加载这个模块（文件）。这几个都是ES6的语法。

**export default{}**这是在复用组件的时候用到的。假设我们写了一个单页面组件 A 文件，而在另一个文件 B 里面需要用到它，那么就要用 ES6 的 import/export 语法 ，在文件 A 中定义输出接口 export **，在文件 B 中引入 import** ，把引入的组件用起来，这样就可以复用组件 A 去配合文件 B 生成 html 页面了。

compute.js

```js
var muilt = function (val) {
  return val * 1000;
};

var divide = function (val) {
  return val / 1000;
};

export { muilt, divide };
```

index.html

```js
<script type="module">
    import { muilt, divide } from './compute.js';
    import add from './add.js';


    var vm = new Vue({
        el: '#computed_props',
        data: {
            km: 0,
            meters: 0
        },
        methods: {
        },
        computed: {
        },
        watch: {
            km: function (val) {
                this.km = val;
                this.meters = muilt(val);
            },
            meters: function (val) {
                this.km = divide(val);
                this.meters = val;
            }
        }
    });
    // $watch 是一个实例方法
    vm.$watch('km', function (newValue, oldValue) {
        // 这个回调将在 vm.kilometers 改变后调用
        document.getElementById("info").innerHTML = "修改前值为: " + oldValue + "，修改后值为: " + newValue;
    })
</script>
```























## Vue.js

























## vue-cli

脚手架顾名思义就是搭建工程的一个工具，脚手架有很多，vue-cli是其中的一种。用来帮助快速的搭建vue的开发环境。

我们可以想象自己不是在搭建开发环境，而是在做ppt。做ppt之前从头开始设定动画交互背景颜色字体大小排版等东西太烦了，因此我们可以选一个现成的模板，直接在现成的模板上进行修改。这样我们就可以直接开始刷墙或者开始装修房子，而不是从头开始打地基垒砖头，这样就可以将精力集中在开发上，而不是忙于框架等琐碎的事情。

对应上面的制作ppt来解释vue-cli的话：让你选择模板的功能其实就是cli的功能，你通过使用不同的命令来初始化你的项目（比如vue init webpack myproject），就是选择不同的模板; 至于webpack其实是你选择的模板里面包含了webpack这个包；这个webpack跟模板其实没有关联，就算你没有安装vue-cli， 你也可以直接用npm安装webpack。

真正包含vue脚手架（也就是帮助你快速搭建vue开发环境的工具）功能的安装包是vue-cli，你只有在全局安装vue-cli(npm install -g vue-cli)，你才能使用vue init命令，安装vue是无法使用vue命令的，vue-cli也不会参与你的代码组成。

总结下来，vue-cli只是**为了快速搭建环境**，和内容的设计编辑没有任何关系。



 ## webpack

webpack是一个模块打包工具。

**用vue项目来举例：**浏览器它是只认识js，不认识vue的。而我们写的代码后缀大多是.vue的，在每个.vue文件中都可能html、js、css甚至是图片资源；并且由于组件化，这些.vue文件之间还有错综复杂的关系。所以项目要被浏览器识别，我们就要使用webpack将它们打包成js文件以及相应的资源文件。

或者这么理解，我们以vue项目的形式编写项目逻辑，浏览器以他理解的方式来运行项目。webpack把我们的vue项目想表达的所有意图传递给浏览器让浏览器去运行。

















## Nodejs & npm

**实际上，npm就是前端模块的Maven。**

































## vue-router

### 懒加载

require: 运行时调用，理论上可以运用在代码的任何地方，
import：编译时调用，必须放在文件开头



vue的路由配置文件(routers.js)，一般使用import引入的写法，当项目打包时路由里的所有component都会打包在一个js中，在项目刚进入首页的时候，就会加载所有的组件，所以导致首页加载较慢，而用require会将component分别打包成不同的js，按需加载，访问此路由时才会加载这个js，所以就避免进入首页时加载内容过多。


vue-router中，require代替import解决vue项目首页加载时间过久的问题。



import写法

```javascript
import userCenter from '@/page/usercenter/index.vue'
export default [
    {
      path: '/userCenter',
      name: 'user-center',
      component: userCenter
    }
]
```



require写法

```javascript

export default [
    {
      path: '/userCenter',
      name: 'user-center',
      component: resolve => require(['@/page/userCenter/index.vue'], resolve)
    },
    {
      path: '/news/detail',
      name: 'news-detail',
      component: () => resolve => require(['@/page/news/detail.vue'], resolve)
    }
]
```









## vuex

















## Axios















