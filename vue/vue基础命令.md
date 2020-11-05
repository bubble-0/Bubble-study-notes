# vue
## vue简介
> Vue (读音 /vjuː/，类似于 view) 是一套用于构建用户界面的渐进式框架。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与现代化的工具链以及各种支持类库结合使用时， Vue 也完全能够为复杂的单页应用提供驱动。

## 环境安装
### 切换镜像
> npm默认的仓库地址是在国外网站，速度较慢,建议切换成淘宝的镜像工具nrm
```sh
# 全局安装 (-g代表全局安装)
npm install nrm -g

# 安装完毕之后查看仓库(带*的就是当前使用的仓库)
nrm ls

# 切换成淘宝的镜像源
nrm use taobao

# 测试速度
nrm test npm
```

### 创建工程
```sh
# 先创建一个空的工程 vuedemo 之后进入目录进行初始化,生成了package.json文件
cd vuedemo
npm init -y

# 安装vue 可以发现在node_modules多了一个vue目录
npm install vue --save
```

## 基础语法
首先准备一个html页面(小技巧:可以创建一个html文件,然后输入英文感叹号按tab键可以快速生成模板文件)

### 创建对象和v-model
> 每个 Vue 应用都是通过用 Vue 函数创建一个新的 Vue 实例开始的,在构造函数中传入一个对象，并且在对象中声明各种Vue需要的数据和方法包括：• el • data • methods
当Vue实例被创建时，它会尝试获取在data中定义的所有属性，用于视图的渲染，并且监视data中的属性变化，当data发生改变，所有相关的视图都将重新渲染，这就是“响应式“系统。
```js
//引入依赖
<script src="../node_modules/vue/dist/vue.js"></script>

//创建vue实例
//首先通过 new Vue()来创建Vue实例
//然后构造函数接收一个对象，对象中有一些属性：
// el：是element的缩写，通过id选中要渲染的页面元素，本例中是一个div
// data：数据，数据是一个对象，里面有很多属性，都可以渲染到视图中
<script>
    var app = new Vue({
        el: "#app",
        data: {
            name: "vue"
        }
    });
</script>

//标签
<div id = "app">
    <h1>Hello {{name}}</h1>
    <input type="text" v-model="name">
</div>

/**
 * 当我们改变name的值时,也会跟着变化
 */
```

### 事件处理
```js

<div id="app2">
    <input type="text" v-model="num"/>
    <input type="button" v-on:click="num++">+1</input>
</div>

<script>
    var app2 = new Vue({
        el : "#app2",
        data : {
            num : 1
        }
    });

</script>
```

### 方法
> Vue实例中除了可以定义data属性，也可以定义方法,并且在Vue实例的作用范围内使用。
```js
<script>
    var app3 = new Vue({
        el : "#app3",
        data : {
            num : 1
        },
        methods : {
            add : function() {
                num ++;
            }
        }
    });
</script>

//标签
<div id="app3">
    <input type="text" v-model="num">
    <input type="button" value="+!" v-on:click="add">
</div>
```

### 生命周期钩子
#### 生命周期
> 每个 Vue 实例在被创建时都要经过一系列的初始化过程 ：创建实例，装载模板，渲染模板等等。Vue为生命周期中的每个状态都设置了钩子函数（监听函数）。每当Vue实例处于不同的生命周期时，对应的函数就会被触发调用。

#### 钩子函数
> beforeCreated：我们在用Vue时都要进行实例化，因此，该函数就是在Vue实例化时调用，也可
以将他理解为初始化函数比较方便一点，在Vue1.0时，这个函数的名字就是init。
> created：在创建实例之后进行调用。
> beforeMount：页面加载完成，没有渲染。如：此时页面还是{{name}}
> mounted：我们可以将他理解为原生js中的window.onload=function({.,.}),或许大家也在用jquery，所以也可以理解为jquery中的$(document).ready(function(){….})，他的功能就是：在dom文档渲染完毕之后将要执行的函数，该函数在Vue1.0版本中名字为compiled。 此时页面中的{{name}}已被渲染成峰哥
> beforeDestroy：该函数将在销毁实例前进行调用 。
> destroyed：改函数将在销毁实例时进行调用。
> beforeUpdate：组件更新之前。
> updated：组件更新之后。

```js
<script>
    var app1 = new Vue({
        el : "#app1",
        data : {
            num : 1
        },
        methods : {
            add : function() {
                this.num ++;
            }
        },
        created : function() {
            this.num = 100;
            console.log("vue is create");
            console.log(this);
        }
    });
</script>

//标签
<div id="app1">
    <input type="text" v-model="num">
    <input type="button" value="+!" v-on:click="add">
</div>
```

### 常用指令
> 指令 (Directives) 是带有 v- 前缀的特殊特性。指令特性的预期值是：单个JavaScript 表达式。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。
#### 插值表达式
##### 花括号
> 格式: {{表达式}}
> - 说明:该表达式支持JS语法，可以调用js内置函数（必须有返回值）
> - 表达式必须有返回结果。例如 1 + 1，没有结果的表达式不允许使用，如：var a = 1 + 1;
> - 可以直接获取Vue实例中定义的数据或函数

##### 插值闪烁
> 使用{{}}方式在网速较慢时会出现问题。在数据未加载完成时，页面会显示出原始的 {{}} ，加载 完毕后才显示正确数据，我们称为插值闪烁。

#### v-text和v-html
> v-text：将数据输出到元素内部，如果输出的数据有HTML代码，会作为普通文本输出
> v-html：将数据输出到元素内部，如果输出的数据有HTML代码，会被渲染

```js
    var app = new Vue({
        el : "#app",
        data : {
            htmlstr : "<h1>hello vue</h1>"
        }
    });

    <div id="app">
        v-text:<spawn v-text="htmlstr"></spawn><br/>
        v-html:<spawn v-html="htmlstr"></spawn>
    </div>
```

#### v-model
> 刚才的v-text和v-html可以看做是单向绑定，数据影响了视图渲染，但是反过来就不行。v-model是双向绑定,视图（View）和模型（Model）之间会互相影响。
> 
> 既然是双向绑定,一定是可以再视图中修改数据,这样就限定了元素类型,目前v-model可以使用的元素有: input select textarea checkbox radio components（Vue中的自定义组件）,基本上除了最后一项，其它都是表单的输入项
> 
> 多个 CheckBox 对应一个model时，model的类型是一个数组，单个checkbox值默认 是boolean类型
> radio对应的值是input的value值
> text 和 textarea 默认对应的model是字符串
> select 单选对应字符串，多选对应也是数组

```js
    <div id="app">
        <span>你选择了{{language.join(",")}}</span>
        <br/>
        <input type="checkbox" v-model="language" value="vue"> vue <br/>
        <input type="checkbox" v-model="language" value="java"> java <br/>
        <input type="checkbox" v-model="language" value="scala"> scala <br/>
    </div>

    var app = new Vue({
    el : "#app",
    data : {
        language : []
    }
});
```
#### v-on
##### 基本使用
> v-on指令用于给页面元素绑定事件。
> 语法:v-on:事件名="js片段或函数名"

```js
    <div id="app">
        <h1>点击了{{num}}次</h1>
        <input type="button" v-on:click="jiayi" value="加一"  />
        <input type="button" @click="jiayi" value="加一"  />
        <input type="button" v-on:click="jianyi" value="减一"  />
        <input type="button" @click="jianyi" value="减一"  />
    </div>

    var app = new Vue({
        el : "#app",
        data : {
            num : 0
        },
        methods : {
            jiayi : function() {
                this.num ++
            },
            jianyi : function() {
                this.num --
            }
        }
    });
```
##### 事件修饰符
>在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。 为了解决这个问题，Vue.js 为 v-on 提供了事件修饰符。修饰符是由点开头的指令后缀来表示的。
>.stop ：阻止事件冒泡到父元素
>.prevent ：阻止默认事件发生*
>.capture ：使用事件捕获模式
>.self ：只有元素自身触发事件才执行。（冒泡或捕获的都不执行）
>.once ：只执行一次
```js

    <div id="app">
        <h1>点击了{{num}}次</h1>
        //浏览器默认事件不会发生
        <input type="button" v-on:contextmenu.prevent="jiayi" value="加一"  />
         //浏览器默认事件会发生
        <input type="button" @contextmenu="jiayi" value="加一"  />
        <input type="button" v-on:contextmenu.prevent="jianyi" value="减一"  />
        <input type="button" @contextmenu="jianyi" value="减一"  />
    </div>

    var app = new Vue({
        el : "#app",
        data : {
            num : 0
        },
        methods : {
            jiayi : function() {
                this.num ++
            },
            jianyi : function() {
                this.num --
            }
        }
    });
```
##### 按键修饰符
> 在监听键盘事件时，我们经常需要检查常见的键值。Vue 允许为 v-on 在监听键盘事件时添加按键修饰符
> 记住所有的 keyCode 比较困难，所以 Vue 为最常用的按键提供了别名：.enter .tab  .delete (捕获“删除”和“退格”键) .esc .space .up  .down .left .right
```js

    <div id="app">
        <h1>点击了{{num}}次</h1>
        <input type="button" @keyup.13="jiayi"  value="加一" /> <br/>
        <input type="button" v-on:keyup.left="jiayi" value="加一"/> <br/>

        <input type="button" @keyup.enter="jianyi" value="减一" /> <br/>
        <input type="button" v-on:keyup.esc="jianyi" value="减一" /> <br/>
    </div>

    var app = new Vue({
        el : "#app",
        data : {
            num : 0
        },
        methods : {
            jiayi : function() {
                this.num ++
            },
            jianyi : function() {
                this.num --
            }       
        }
    });
```
##### 组合按钮
> 可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器。
> .ctrl .alt .shift
```js
    <!-- alt + c -->
    <input type="button" @keyup.alt.67="clear" value="清空" />

    <!-- ctrl + click -->
    <input type="button" @click.ctrl="alter" value="点击"/>

    var app = new Vue({
        el : "#app",
        data : {
            num : 0
        },
        methods : {
            clear : function() {
                this.num = 0;
            },
            alter : function() {
                alert(this.num);
            }       
        }
    });
```
#### v-for遍历和遍历下标
> 遍历数据渲染页面是非常常用的需求，Vue中通过v-for指令来实现。
> 普通遍历 : v-for="item in items"
> 带下标遍历 : v-for="(item,index) in items"
> 对象遍历 : v-for="value in object"
>           v-for="(value,key) in object"
>           v-for="(value,key,index) in object"
```js

    <div id="app">
        <h1>v-for普通使用</h1>
        <ul>
            <li v-for="person in persons">
                id: {{person.id}} == name:{{person.name}} == age:{{person.age}}
            </li>        
        </ul>

        <h1>v-for遍历下标</h1>
        <ul>
            <li v-for="(item,index) in persons">
                第{{index}}个 == id:{{item.id}} == name:{{item.name}} == age:{{item.age}}
            </li>
        </ul>

        <h1>遍历对象value</h1>
        <ul>
            <li v-for="value in objmap">
                value : {{value}}
            </li>
        </ul>

        <h1>遍历对象value,key</h1>
        <ul>
            <li v-for="(value,key) in objmap">
                key : {{key}} == value : {{value}}
            </li>
        </ul>

        <h1>遍历对象value,key,index</h1>
                <ul>
            <li v-for="(value,key,index) in objmap">
                第{{index}}个 == key : {{key}} == value:{{value}}
            </li>
        </ul>
    </div>

    var app = new Vue({
        el : "#app",
        data : {
            persons : [
                {id : 1, name : "小1", age : 1},
                {id : 2, name : "小2", age : 2},
                {id : 3, name : "小3", age : 3},
                {id : 4, name : "小4", age : 4},
                {id : 5, name : "小5", age : 5},
                {id : 6, name : "小6", age : 6},
                {id : 7, name : "小7", age : 7},
            ],
            objmap : {
                id : 666,
                name : "古力娜扎"
            }
        }
    });
```

#### v-if和v-show
> v-if，顾名思义，条件判断。当得到结果为true时，所在的元素才会被渲染。
> 语法：v-if="布尔表达式"
> 当v-if和v-for出现在一起时，v-for优先级更高。也就是说，会先遍历，再判断条件。
> v-else 元素必须紧跟在带 v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。
> v-else-if ，顾名思义，充当 v-if 的“else-if 块”，可以连续使用
> v-show: 带有 v-show 的元素始终会被渲染并保留在 DOM 中。 v-show 只是简单地切换元素的
CSS 属性 display

```js
    <div id="app">
        <input type="button" @click="qiehuan" value="切换" />
        <h1 v-if="ashow">这个是 v-if </h1>
        <h1 v-else>这个是 v-else </h1>
        <h1 v-show="!ashow">这个是 v-show</h1>
    </div>

    var app = new Vue({
        el: "#app",
        data: {
            ashow: true
        },
        methods: {
            qiehuan : function () {
                this.ashow = !this.ashow;
            }
        }
    });
```

#### v-bind
>html属性不能使用双大括号形式绑定，只能使用v-bind指令。
>在将 v-bind 用于 class 和 style 时，Vue.js 做了专门的增强。表达式结果的类型除了字符串
之外，还可以是对象或数组
```js
<div id="app">
    <div v-bind:class="test1">111</div>
    <div v-bind:test="arr">222</div>
    <div v-bind:class="[test1,arr]">333</div>
</div>

vat app = new Vue({
    el : "#app",
    data : {
        test1: "aaaaaaa",
        arr: ["aa", "bb", "cc", "dd"]
    }
});
```

#### 计算属性
>计算属性本质就是方法，但是一定要返回数据。然后页面渲染时，可以把这个方法当成一个变量来使用。
>是计算属性是基于它们的依赖进行缓存的。计算属性只有在它的相关依赖发生 改变时才会重新求值。这就意味着只要 birthday 还没有发生改变，多次访问 birthday 计算属性会立即返回之前的计算结果，而不必再次执行函数
```js
<div id="app">
    <h1>您的生日是:{{birth}}</h1>
</div>

vat app = new Vue({
    el : "#app",
    data : {
       birthday : 1429032123201
    },
    computed : {
        birth() {{// 计算属性本质是一个方法，但是必须返回结果
            const d = new Date(this.birthday);
            return d.getFullYear() + "-" + d.getMonth() + "-" + d.getDay();
        }
    }
});
```








