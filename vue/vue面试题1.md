<!--
 * @Descripttion: 
 * @version: 1.0
 * @Author: 
 * @Date: 2021-12-31 16:46:18
 * @LastEditors: YingJie Xing
 * @LastEditTime: 2021-12-31 16:46:18
 * @FilePath: /personalNotes/vue/vue面试题1.md
 * Copyright 2021 YingJie Xing, All Rights Reserved. 
-->
## Vue相关 

### 什么是数据响应式？怎么实现的？明显缺点？

```
vue会对这个数据监听处理，它会通过这个Object.defineProperty()来对数据进行处理，使它变成setter，getter形式，这样就可以监听变化，同时可以在这个数据变化的时候在ui上展示更新变化之后的数据。
缺点：不能监听数组，做有效处理。vue3可以。还有就是一开始没有后面新加的。但是可以用set方法来添加监听。
```

### Vue 的双向数据绑定原理是什么？

>```shell
>采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。
>```

### Vue的优点？

>```
>1、轻量级框架，大小只有几十kb、简单易学
>2、保留了angular的特点，双向数据绑定数据操作方便
>3、组件化开发实现了html的封装和重用，在构建单页面有独特优势
>4、虚拟DOM操作非常消耗性能，不使用原生DOM操作，换了种方式，就操作DOM而言相比react性能有更到优势
>```

### Vue实现组件通信方式？

```
1.props  // 一般是实现父传子，隔代麻烦
2.vue自定义事件  // 只适合子传父
3.vuex  // 对组件关系没有限制，方便
具体实现：vuex 是一个状态管理工具，主要解决大中型复杂项目的数据共享问题，主要包括state,actions,mutations,getters 和 modules 5 个要素，主要流程：组件通过 dispatch 到 actions，actions 是异步操作，再 actions中通过 commit 到 mutations，mutations 再通过逻辑操作改变 state，从而同步到组件，更新其数据状态。
4.slot  // 专门用于实现父传子传递带数据的标签
5.消息订阅与发布  // 可以实现任意关系组件间通信
```

### Vue父组件向子组件传递数据？

```
通过props
```

### Vue兄弟之间传递数据？

```
1、通过事件中心：可以通过一个vue实例Bus作为媒介，要相互通信的兄弟组件之中，都引入Bus，之后通过分别调用Bus事件触发$emit和监听$on来实现组件之间的通信和参数传递
Bus.$emit('name', 'hello');
Bus..$on('name',(value)=>{
  this.value=value;
});
2、子传父，父传子
```

### 子组件向父组件传递数据？

```
$emit()方法
```

### v-show和v-if的区别？

```
v-show是通过css中的display设置为none，控制隐藏只会编译一次
v-if是动态的向DOM树内添加或删除DOM元素，v-if不停的销毁和创建比较消耗性能
如果要频繁切换某个节点用v-show（切换开销小，初始开销大），如果不需要用v-if（初始渲染开销小，切换开销大）
```

### 如何让css只在当前组件中起作用？

```
在组件的style中添加scoped
```

### keep-alive的作用是什么?

```
keep-alive是vue内置的一个 组件，可以使被包含的组件保留状态，避免重新渲染
```

### Vue中如何获取DOM

```
ref="domName", 使用this.$refs.domName
```

### 说出几种vue当中的指令和它的法？

```
v-model双向数据绑定
v-for循环
v-if v-show显示与隐藏
v-on绑定事件、v-once:只绑定一次
```

### vue-loader是什么？用途？

```
vue-loader是vue文件的一个加载器，将template/js/style/转换成js模块。
用途：js可以写ES6、style样式可以写scss、less、template可以价jade等

```

### 为什么使用key?

```
需要使用key来给每个节点做一个唯一表示，vue自带的Diff算法就可以正确的识别此节点。
作用主要为了高效的更新虚拟DOM;

- key是为Vue中的vnode标记的唯一id,通过这个key,我们的diff操作可以更准确、更快速
- diff算法的过程中,先会进行新旧节点的首尾交叉对比,当无法匹配的时候会用新节点的key与旧节点进行比对,然后超出差异.
- diff程可以概括为：oldCh和newCh各有两个头尾的变量StartIdx和EndIdx，它们的2个变量相互比较，一共有4种比较方式。如果4种比较都没匹配，如果设置了key，就会用key进行比较，在比较的过程中，变量会往中间靠，一旦StartIdx>EndIdx表明oldCh和newCh至少有一个已经遍历完了，就会结束比较,这四种比较方式就是首、尾、旧尾新头、旧头新尾.

- 准确: 如果不加key,那么vue会选择复用节点(Vue的就地更新策略),导致之前节点的状态被保留下来,会产生一系列的bug.
- 快速: key的唯一性可以被Map数据结构充分利用,相比于遍历查找的时间复杂度O(n),Map的时间复杂度仅仅为O(1).
```

### v-model的使用？

```
用于表单数据的双向绑定，这个背后有两个操作：
1、v-bind绑定value属性
2、v-on指令给当前元素绑定input事件
 {{username}} 
 <input :value="username" @input="username=$event.target.value">
```

### vue-cli创建的项目中src目录每个文件夹和文件的用法？

```
assets存放静态资源
components是存放组件
router定义路由相关的配置
app.vue是一个应用主组件
main.js入口文件
```

### 分别阐述computed和watch的使用场景

```
computed：
   当一个属性受多个属性影响的时候就需要用到computed
   典型例子：购物车结算的时候
watch：
   当一条数据影响多条数据的时候就需要用到watch,监听事件变化
   典型例子：搜索数据
```

### v-on可以监听多个方法？

```
v-on="{ input: onInput, focus: onFocus, blur: onBlur }"
```

### $nextTick的使用？

```
当你修改data的值然后马上获取这个dom元素的值，是不能获取到更新后的值。
可以用$nextTick这个回调，让修改后的data值渲染更新到dom之后在获取，才能成功。
在created()钩子函数执行的时候DOM 其实并未进行任何渲染，而此时进行DOM操作并无作用，而在created()里使用this.$nextTick()可以等待dom生成以后再来获取dom对象.
```

### $nextTick和$set的不同使用场景？

```
当你修改data的值然后马上获取这个dom元素的值,并使用该dom值做点什么,如项目需求一个轮播图swiper-slide一次同时显示个数为5，小于5个时不显示左右箭头，就需要先渲染数据再判断有几个slide显示了。但是我们是不能立刻获取到更新后的值。
这时候可以用$nextTick这个回调，让修改后的data值渲染更新到dom之后在获取，才能成功。
而vm.$set的使用场景是：
1.某一个data属性为对象新增一个属性
2.改变数组的长度,交换数组的顺序,利用索引修改数据
在这两种情况下我们更改数据层的信息后，视图层是不会实时刷新的，即不是响应式。
另外VUE包装了观察数组的变异方法,它们能触发视图的更新:
push()
pop()
shift()
unshift()
splice()
sort()
reverse()
如果不适用上述的数组变异方法，这时候我们可以使用vm.$set来强制实现视图层的刷新。
```

### 为什么组件中data必须是一个函数？

```
数据以函数的的形式定义，这样在组件复用的时候，就都会返回一份新的data,每个组件实例都有自己独立的数据空间，不会造成数据混乱。而对象定义的形式，就是所有组件共用一个data,这样改一个全都改了。
```

### 如何理解渐进式框架?

```
根据需要来选择层级,没有强主张，你可以只使用我的一部分
```

### Vue中双向数据绑定是如何实现的？

```
vue双向绑定时通过数据劫持，结合 发布订阅模式的方式来实现的，也就是说数据和视图同步，数据变化，视图也跟着变化，数据也随之发送变化
核心：原理是观察者observer通过Object.defineProperty()来劫持到各个属性的getter setter，在数据变动的时候，会被observer观察到，会通过Dep通知数据的订阅者watcher，之后进行相应的视图上面的变化。
```

### 单页面应用和多页面应用区别及优缺点？

```
单页面缺点：不利于seo，导航不可用（不能使用浏览器的前进后退功能），如果一定要实现前进后退，需要自己建立堆栈管理。初次加载耗时多，
```

### v-if和v-for的优先级？

```
当v-if一起使用时，v-for优先级高，要一起使用v-if应该放到外面
永远不要把 v-if 和 v-for 同时用在同一个元素上。
将 users替换为一个计算属性 (比如 activeUsers)，让其返回过滤后的列表
将：
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>


换成：

<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
computed: {
  activeUsers: function () {
    return this.users.filter(function (user) {
      return user.isActive
    })
  }
}

```

### Vue常用的修饰符?

```
.stop防止事件冒泡，等同于event.stopPropagation()
.prevent阻止默认行为，等同于event.preventDefault()
.self只触发自己范围内的事件，不包含子元素
.once只触发一次
```

### Vue的两个核心点？

```
数据驱动、组件系统

数据驱动：ViewModel，保证数据和视图一致
组件系统：应用类UI可以看作全部是由组件树构成的
```

### Vue和Jq的区别？

```
jq是使用选择器 $() 选取DOM对象，对其进行赋值、取值、事件绑定等操作，和原生的html的区别在于可以方便选取操作DOM对象，而数据和界面是在一起的。
vue是通过对象将数据和View分离，进行数据操作不再需要应用相对应的DOM对象，他们通过Vue对象这个vm实现相互的绑定，这就是MVVM。
```

### Vue里面router-link在电脑上有用，在安卓上没反应怎么解决？

```
有babel问题，安装babel polypill插件解决
```

### Vue中注册在router-link上事件无效解决方法

```
使用@click.native  因为router-link会阻止click事件，native指直接监听一个原生事件。
```

### Vue闪动问题？

```
[v-cloak] { display: none; }
```

### Vue更新数组时触发视图更新的方法？

```
push()、pop()、shift()、unshift()、splice()、sort()、reverse()
```

### 什么是生命周期，作用？

```
每个Vue实例在被创建时都要经过一系列的初始化过程，例如需要设置数据监听、编译模板、将实例挂载到DOM并在数据变化时更新DOM。这一过程会运行一些生命周期钩子函数，这给用户在不同阶段添加自己的代码机会。例如：如果要通过某些插件操作DOM节点，如果想在页面渲染完后弹出广告，那我们最早可在mounted中进行。
```

### 第一次页面加载会触发哪几个钩子函数？

```
beforeCreate、created、beforeMount、mounted
```

### 钩子函数具体适合那些场景？

>```shell
>beforeCreate：在new一个vue实例后，只有一些默认的生命周期钩子和默认事件，其他的东西都还没有创建。在beforeCreate生命周期执行的时候，data和methods中的数据都还没有初始化。不能在这个阶段使用data中的数据和methods中的方法。
>create：data和methods都已经被初始化好了，如果要调用methods中的方法，或者操作data中的数据，最早可以在这个阶段中操作。
>beforeMount：执行到这个钩子函数的时候，内存中已经编译好了模板，但是还没有挂载到页面中，此时，页面还是旧的
>mounted：执行到这个钩子函数的时候，就表示Vue实例已经初始化完成了。此时组件脱离了创建阶段，进入到运行阶段。如果想要通过插件操作页面上的DOM节点，最早可以在这个阶段中进行。
>beforeUpdate：执行到这个钩子函数时，页面中的显示数据还是旧的，data中的数据时更新后的，页面还没有和最新的数据保持同步。
>updated：页面显示的数据和data中的数据已经保持同步了，都是最新的
>beforeDestory：Vue实例从运行阶段进入到销毁阶段，这个时候上所有的data和methods，指令，过滤器...都处于可用状态。还没有真正被销毁。
>destoryed：这个时候上所有的data和methods，指令，过滤器...都处于不可用状态。组件已经被销毁。
>```

### created和mounted的区别？

```
created：在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图。
mounted：在模板渲染成html后调用，通常是初始化页面完成后，在对html和dom节点进行操作。
```

### watch、computed和methods的区别

```
computed 计算属性 计算结果会缓存,只有当依赖值改变才会重新计算

watch 监听属性 一个值的改变  需要另一个值的改变而改变,结果不会缓存

methods 事件方法 调用一次，执行一次,结果不会缓存
```

### vue获取数据在哪个周期函数？

```
在created/beforeMount/mounted皆可
但如果你要操作dom，那么肯定mounted时候才能操作
```

### 请详细说下你对vue生命周期的理解？

```
总共分8个阶段：创建前/后  载入前/后  更新前/后  销毁前/后

创建前/后：在beforeCreated阶段，vue实例的挂载元素$el（节点）和数据对象data都为undefined，还未初始化。在created阶段，vue实例的数据对象data有了，$el还没有。

载入前/后：在beforeMount阶段，vue实例的$el和data都初始化了，但还是挂载之前虚拟的DOM节点，data.message还未替换。
在mounted阶段，vue实例挂载完成，data.message成功渲染。

更新前/后：在data变化时，会触发beforeUpdate和updata方法。

销毁前/后：在执行destroy方法后，对data的改变不会触发周期函数，说明此时vue实例已经解除了事件监听以及dom的绑定，但dom结构依然存在。
```

### MVVM框架是什么？

vue是实现了双向数据绑定的mvvm框架，当视图改变更新模型层，当模型层该变更新视图层。在vue中，使用了双向绑定技术，就是view的变化能实时让Model发送变化，而Model的变化也能实时更新到view。

#### vue弹窗后如何禁止滚动条滚动？

```
vue中提供 @touchmove.prevent 方法可以完美解决这个问题,如果不是使用Vue的话，可以给body添加overflow:hidden属性解决
<div class="dialog" @touchmove.prevent ></div>

弹层显示时调用 stopMove()停止页面滚动 ，弹层消失时调用 Move()开启页面滚动
//停止页面滚动
stopMove(){
 let m = function(e){e.preventDefault();};
 document.body.style.overflow='hidden';
 document.addEventListener("touchmove",m,{ passive:false });//禁止页面滑动
},
//开启页面滚动
Move(){
 let m =function(e){e.preventDefault();};
 document.body.style.overflow='';//出现滚动条
 document.removeEventListener("touchmove",m,{ passive:true });
}
```

### vue-cli中自定义指令的使用？

```
bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 。
componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
unbind：只调用一次，指令与元素解绑时调用。
```

### vue中对对象更改检测的注意事项？

```js
Vue不能检测对象属性的添加或删除：
var vm = new Vue({
  data: {
    a: 1
  }
})
// vm.a 现在是响应式的
vm.b = 2 // vm.b 不是响应式的

对于已经创建的实例，vue不能动态添加根级别的响应式属性。但是，可以使用 Vue.set(object, key, value)  方法向嵌套对象添加响应式属性。例如
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
可以使用set来添加一个新的age属性到嵌套到对象中
this.$set(obj, key, value)
Vue.set(vm.userProfile, 'age', 21)
或者 vm.$set(vm.userProfile, 'age', 27)  // vm.$set只是全局Vue.set的别名 
```

### vue子组件如何调用父组件的方法？

```js
1、直接在子组件中通过  this.$parent.事件名  来调用父组件的方法
2、在子组件里用$emit向父组件触发一个事件，父组件监听这个事件就行了
3、父组件把方法传入子组件中，在子组件里直接调用这个方法。
props: {
  fatherMethod: {
    type: Function,
    default: null
  }
},
methods: {
  childMethod() {
    if (this.fatherMethod) {
        this.fatherMethod();
      }
    }
  }
```

## vue-router

### vue-router是什么？它有那些组件？

```
vue用来写路由的一个插件。router-link、router-view
```

### vue-router有哪几种导航钩子?

```js
① 全局导航钩子：一般用来判断权限，以及页面丢失时需要执行的操作；
const router = new VueRouter({ ... });
router.beforeEach((to, from, next) => {
    // do someting
});
1.to: Route，代表要进入的目标，它是一个路由对象

2.from: Route，代表当前正要离开的路由，同样也是一个路由对象

3.next: Function，这是一个必须需要调用的方法，而具体的执行效果则依赖 next 方法调用的参数

     beforeEach（）每次路由进入之前执行的函数。
     afterEach（）每次路由进入之后执行的函数。
     beforeResolve（）2.5新增
② 单个路由（实例钩子）即单个路由独享的导航钩子，它是在路由配置上直接进行定义的：
cont router = new VueRouter({
    routes: [
        {
            path: '/file',
            component: File,
            beforeEnter: (to, from ,next) => {
                // do someting
            }
        }
    ]
});
     beforeEnter（）
     beforeLeave（）
③ 组件路由钩子：
    beforeRouteEnter（）
    beforeRouteLeave（）
    beforeRouteUpdate（）
    他们是直接在路由组件内部直接进行定义的
    const File = {
    template: `<div>This is file</div>`,
    beforeRouteEnter(to, from, next) {
        // do someting
        // 在渲染该组件的对应路由被 confirm 前调用
    },
    beforeRouteUpdate(to, from, next) {
        // do someting
        // 在当前路由改变，但是依然渲染该组件是调用
    },
    beforeRouteLeave(to, from ,next) {
        // do someting
        // 导航离开该组件的对应路由时被调用
    }
}
```

### active-class是哪个组件的属性？

```
vue-router模块的router-link组件中的属性，用来设置链接激活时使用的类名。
```

### vue-router实例方法？ vue-router的动态路由传参？怎么传递参数与获取传过来的值？

```js
实例方法：push、replace、go
replace和push都能实现跳转的效果，但是区别在于：唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

定义：在router目录下的index.js文件中，对path属性加上 /:id。

1、params 方式   // 相当于ajax的post请求
配置
{
  path:"/detail",
  name:"detail",
  component:home
}
传递
this.$router.push({
   name:"/detail",  // 一定要使用name匹配
   params:{
   name:'nameValue',
   code:10011
  }
});
接受
this.$route.params.code    
params类似于post===>浏览器地址栏中不显示参数


2、query 方式
配置
{
  path:"/detail",
  name:"detail",   // 可以不需要
  component:home
}
传递
this.$router.push({
   path:"/detail",  // 用path匹配，也可以用name
   query:{
   name:'nameValue',
   code:10011
  }
});
接受
this.$route.query.code    
query类似于ajax的get传参==>浏览器地址栏中显示参数
3、
<router-link :to="{ A: 'xxx', query: { xx:'xxx' }}" />
<router-link :to="{ A: 'xxx', parmas: { xx:'xxx' }}" />

4、props的值还可以为对象类型props: { user:''}
配置中
{
  path: '/article/:articleId',
  name: 'article',
  component: () => import('../views/article/'),
  props: true
},
组件中
props: {
  articleId: {
    type: [Object, String, Number],
    required: true
  }
},
```

### params、query两者区别？

```js
this.$router.push() 方法中，params传值必须有name属性，也可以有path属性，不然取不到值。
this.$router.push() 方法中，query传值与name和path属性无必然联系，和其中哪个属性搭配都可以传值。
params传值在url上是看不到传递的参数的。
通过query传值，跳到另一个页面的时候，刷新还是可以取到传递过来的值，而params就会重置，取不到值。

最近有一个需求，比如详情页，要求按F5刷新完后数据还是能正常展示，详情页是在created后用ID请求。
如果是用query 传递过来的id，在this.$route，上会一直存在。
但是如果用params的时候，如果不做别的配置，直接在路由跳转的时间加params，F5刷新数据可能就不存在了。
如果一定要用params也可以，在router文件的 path 后面 + “/:id”，这样页面F5刷新后ID还是在router中。
如果是单独的详情页这样也是可以的，但是如果新增和编辑都是跳转同一个路由呢，这样就会报错了，因为编辑要请求详情，就需要ID，但是新增的时候是没有ID的
这时候就需要   path 后面 + “/:id?”，也就是id后面加一个“？”，和正则的意思一样，可有可无。这样就不会报错了。
个人还是建议用 query ，因为它不需要改变 path规则。
```

### $route和$router的区别？

```
$route为当前router信息对象。里面可以获取到当前路由的name,path,query,params等
$router是VueRouter的实例，使用$router.push方法可以导航到不同的url。返回上一个历史history用$router.go(-1)
```

### vue-router如何响应路由参数的变化？

```js
1、监听器
watch: { // watch的第一种写法
    $route (to, from) {
        console.log(to)
        console.log(from)
    }
},
watch: { // watch的第二种写法
    $route: {
        handler (to, from){
            console.log(to)
            console.log(from)
        },
        // 深度观察监听
        deep: true
    }
},

watch: { // watch的第三种写法
    '$route':'getPath'
},
methods: {
    getPath(to, from){
        console.log(this.$route.path);
    }
},


2、导航守卫
```

### 怎么复用一个组件？

```

```

### 路由懒加载？

```

```

## vuex

### vuex是什么？怎么使用？功能场景？

```
定义：Vuex是一个专为Vue.js应用程序开发的状态管理模式。它采用集中式储存管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

使用场景：需要构建一个中大型单页应用，您很可能会考虑如何更好的在组件外部管理状态，Vuex将会成为自然而然的选择。单页面应用中，组件之间的状态，音乐播放，登陆状态，加入购物车。

优点：当你在state中定义了一个数据之后，可以在所在项目中的任何一个组件里进行获取、进行修改、并且你的修改可以得到全局的响应变更。

 Vuex的运行机制：在组件中通过this.$store.dispatch来调用actions中的方法，在action中通过commit来调用mutations中的方法，在mutations的方法中操作state中的数据，数据只要更新就会立即响应到组件上

 核心：
      ①state：定义初始数据。
      ②mutations：更改Vuex的store中的状态的唯一方法是提交mutation
      ③getters：可以对state 进行计算操作，它就是 store 的计算属性虽然在组件内也可以做计算属性，但是 getters 可以在多给件之间复用如果一个状态只在一个组件内使用，是可以不用 getters。
      ④actions：异步操作初始数据，其实就是调用mutations里面的方法。
      ⑤module：面对复杂的应用程序，当管理的状态比较多时；我们需要将vuex的store对象分割成模块(modules)。
```

### vuex有哪几种属性？

```
State、Getter、Mutation、Action、Module
state => 基本数据
getter => 从基本数据派生出来的数据
mutations => 提交更新数据的方法，同步
actions => 包裹mutation，使之可以异步
module => 模块化vuex
```

### vue.js中ajax请求代码应该写在组件的methods中还是vuex中的actions中？

```
如果请求未来的数据是不是要被其他组件公用，仅仅在请求的组件内使用，就不要存放在vuex的state里。
```

**1.vue优点？**
答：轻量级框架：只关注视图层，是一个构建数据的视图集合，大小只有几十kb；
简单易学：国人开发，中文文档，不存在语言障碍 ，易于理解和学习；
双向数据绑定：保留了angular的特点，在数据操作方面更为简单；
组件化：保留了react的优点，实现了html的封装和重用，在构建单页面应用方面有着独特的优势；
视图，数据，结构分离：使数据的更改更为简单，不需要进行逻辑代码的修改，只需要操作数据就能完成相关操作；
虚拟DOM：dom操作是非常耗费性能的， 不再使用原生的dom操作节点，极大解放dom操作，但具体操作的还是dom不过是换了另一种方式；
运行速度更快:相比较与react而言，同样是操作虚拟dom，就性能而言，vue存在很大的优势。
**2.vue父组件向子组件传递数据？**
答：通过props
**3.子组件像父组件传递事件？**
答：$emit方法
**4.v-show和v-if指令的共同点和不同点？**
答: 共同点：都能控制元素的显示和隐藏；
不同点：实现本质方法不同，v-show本质就是通过控制css中的display设置为none，控制隐藏，只会编译一次；v-if是动态的向DOM树内添加或者删除DOM元素，若初始值为false，就不会编译了。而且v-if不停的销毁和创建比较消耗性能。
总结：如果要频繁切换某节点，使用v-show(切换开销比较小，初始开销较大)。如果不需要频繁切换某节点使用v-if（初始渲染开销较小，切换开销比较大）。
**5.如何让CSS只在当前组件中起作用？**
答：在组件中的style前面加上scoped
**6.<keep-alive></keep-alive>的作用是什么?**
答:keep-alive 是 Vue 内置的一个组件，可以使被包含的组件保留状态，或避免重新渲染。
**7.如何获取dom?**
答：ref="domName" 用法：this.$refs.domName
**8.说出几种vue当中的指令和它的用法？**
答：v-model双向数据绑定；
v-for循环；
v-if v-show 显示与隐藏；
v-on事件；v-once: 只绑定一次。
**9. vue-loader是什么？使用它的用途有哪些？**
答：vue文件的一个加载器，将template/js/style转换成js模块。
用途：js可以写es6、style样式可以scss或less、template可以加jade等
**10.为什么使用key?**
答：需要使用key来给每个节点做一个唯一标识，Diff算法就可以正确的识别此节点。
作用主要是为了高效的更新虚拟DOM。
**11.axios及安装?**
答：请求后台资源的模块。npm install axios --save装好，
js中使用import进来，然后.get或.post。返回在.then函数中如果成功，失败则是在.catch函数中。
**12.v-modal的使用。**
答：v-model用于表单数据的双向绑定，其实它就是一个语法糖，这个背后就做了两个操作：
v-bind绑定一个value属性；
v-on指令给当前元素绑定input事件。
**13.请说出vue.cli项目中src目录每个文件夹和文件的用法？**
答：assets文件夹是放静态资源；components是放组件；router是定义路由相关的配置; app.vue是一个应用主组件；main.js是入口文件。
**14.分别简述computed和watch的使用场景**
答：computed:
　　　　当一个属性受多个属性影响的时候就需要用到computed
　　　　最典型的栗子： 购物车商品结算的时候
watch:
　　　　当一条数据影响多条数据的时候就需要用watch
　　　　栗子：搜索数据
**15.v-on可以监听多个方法吗？**
答：可以，栗子：<input type="text" v-on="{ input:onInput,focus:onFocus,blur:onBlur, }">。
**16.$nextTick的使用**
答：当你修改了data的值然后马上获取这个dom元素的值，是不能获取到更新后的值，
你需要使用$nextTick这个回调，让修改后的data值渲染更新到dom元素之后在获取，才能成功。
**17.vue组件中data为什么必须是一个函数？**
答：因为JavaScript的特性所导致，在component中，data必须以函数的形式存在，不可以是对象。
　　组建中的data写成一个函数，数据以函数返回值的形式定义，这样每次复用组件的时候，都会返回一份新的data，相当于每个组件实例都有自己私有的数据空间，它们只负责各自维护的数据，不会造成混乱。而单纯的写成对象形式，就是所有的组件实例共用了一个data，这样改一个全都改了。
**18.渐进式框架的理解**
答：主张最少；可以根据不同的需求选择不同的层级；
**19.Vue中双向数据绑定是如何实现的？**
答：vue 双向数据绑定是通过 数据劫持 结合 发布订阅模式的方式来实现的， 也就是说数据和视图同步，数据发生变化，视图跟着变化，视图变化，数据也随之发生改变；
核心：关于VUE双向数据绑定，其核心是 Object.defineProperty()方法。原理是观察者observer通过Object.defineProperty()来劫持到各个属性的getter setter，在数据变动的时候，会被observer观察到，会通过Dep通知数据的订阅者watcher，之后进行相应的视图上面的变化。
**20.单页面应用和多页面应用区别及优缺点**
答：单页面应用（SPA），通俗一点说就是指只有一个主页面的应用，浏览器一开始要加载所有必须的 html, js, css。所有的页面内容都包含在这个所谓的主页面中。但在写的时候，还是会分开写（页面片段），然后在交互的时候由路由程序动态载入，单页面的页面跳转，仅刷新局部资源。多应用于pc端。
多页面（MPA），就是指一个应用中有多个页面，页面跳转时是整页刷新
单页面的优点：
用户体验好，快，内容的改变不需要重新加载整个页面，基于这一点spa对服务器压力较小；前后端分离；页面效果会比较炫酷（比如切换页面内容时的专场动画）。
单页面缺点：
不利于seo；导航不可用，如果一定要导航需要自行实现前进、后退。（由于是单页面不能用浏览器的前进后退功能，所以需要自己建立堆栈管理）；初次加载时耗时多；页面复杂度提高很多。
**21.v-if和v-for的优先级**
答：当 v-if 与 v-for 一起使用时，v-for 具有比 v-if 更高的优先级，这意味着 v-if 将分别重复运行于每个 v-for 循环中。所以，不推荐v-if和v-for同时使用。
如果v-if和v-for一起用的话，vue中的的会自动提示v-if应该放到外层去。
**22.assets和static的区别**
答：相同点：assets和static两个都是存放静态资源文件。项目中所需要的资源文件图片，字体图标，样式文件等都可以放在这两个文件下，这是相同点
不相同点：assets中存放的静态资源文件在项目打包时，也就是运行npm run build时会将assets中放置的静态资源文件进行打包上传，所谓打包简单点可以理解为压缩体积，代码格式化。而压缩后的静态资源文件最终也都会放置在static文件中跟着index.html一同上传至服务器。static中放置的静态资源文件就不会要走打包压缩格式化等流程，而是直接进入打包好的目录，直接上传至服务器。因为避免了压缩直接进行上传，在打包时会提高一定的效率，但是static中的资源文件由于没有进行压缩等操作，所以文件的体积也就相对于assets中打包后的文件提交较大点。在服务器中就会占据更大的空间。
建议：将项目中template需要的样式文件js文件等都可以放置在assets中，走打包这一流程。减少体积。而项目中引入的第三方的资源文件如iconfoont.css等文件可以放置在static中，因为这些引入的第三方文件已经经过处理，我们不再需要处理，直接上传。
**23.vue常用的修饰符**
答：.stop：等同于JavaScript中的event.stopPropagation()，防止事件冒泡；
.prevent：等同于JavaScript中的event.preventDefault()，防止执行预设的行为（如果事件可取消，则取消该事件，而不停止事件的进一步传播）；
.capture：与事件冒泡的方向相反，事件捕获由外到内；
.self：只会触发自己范围内的事件，不包含子元素；
.once：只会触发一次。
**24.vue的两个核心点**
答：数据驱动、组件系统
数据驱动：ViewModel，保证数据和视图的一致性。
组件系统：应用类UI可以看作全部是由组件树构成的。
**25.vue和jQuery的区别**
答：jQuery是使用选择器（$）选取DOM对象，对其进行赋值、取值、事件绑定等操作，其实和原生的HTML的区别只在于可以更方便的选取和操作DOM对象，而数据和界面是在一起的。比如需要获取label标签的内容：$("lable").val();,它还是依赖DOM元素的值。
Vue则是通过Vue对象将数据和View完全分离开来了。对数据进行操作不再需要引用相应的DOM对象，可以说数据和View是分离的，他们通过Vue对象这个vm实现相互的绑定。这就是传说中的MVVM。
**26. 引进组件的步骤**
答: 在template中引入组件；
在script的第一行用import引入路径；
用component中写上组件名称。
**27.delete和Vue.delete删除数组的区别**
答：delete只是被删除的元素变成了 empty/undefined 其他的元素的键值还是不变。Vue.delete 直接删除了数组 改变了数组的键值。
**28.SPA首屏加载慢如何解决**
答：安装动态懒加载所需插件；使用CDN资源。
**29.Vue-router跳转和location.href有什么区别**
答：使用location.href='/url'来跳转，简单方便，但是刷新了页面；
使用history.pushState('/url')，无刷新页面，静态跳转；
引进router，然后使用router.push('/url')来跳转，使用了diff算法，实现了按需加载，减少了dom的消耗。
其实使用router跳转和使用history.pushState()没什么差别的，因为vue-router就是用了history.pushState()，尤其是在history模式下。
**30. vue slot**
答：简单来说，假如父组件需要在子组件内放一些DOM，那么这些DOM是显示、不显示、在哪个地方显示、如何显示，就是slot分发负责的活。
**31.你们vue项目是打包了一个js文件，一个css文件，还是有多个文件？**
答：根据vue-cli脚手架规范，一个js文件，一个CSS文件。
**32.Vue里面router-link在电脑上有用，在安卓上没反应怎么解决？**
答：Vue路由在Android机上有问题，babel问题，安装babel polypill插件解决。
**33.Vue2中注册在router-link上事件无效解决方法**
答： 使用@click.native。原因：router-link会阻止click事件，.native指直接监听一个原生事件。
**34.RouterLink在IE和Firefox中不起作用（路由不跳转）的问题**
答: 方法一：只用a标签，不适用button标签；方法二：使用button标签和Router.navigate方法
**35.axios的特点有哪些**
答：从浏览器中创建XMLHttpRequests；
node.js创建http请求；
支持Promise API；
拦截请求和响应；
转换请求数据和响应数据；
取消请求；
自动换成json。
axios中的发送字段的参数是data跟params两个，两者的区别在于params是跟请求地址一起发送的，data的作为一个请求体进行发送
params一般适用于get请求，data一般适用于post put 请求。
**36.请说下封装 vue 组件的过程？**
答：1. 建立组件的模板，先把架子搭起来，写写样式，考虑好组件的基本逻辑。(os：思考1小时，码码10分钟，程序猿的准则。)

  　　2. 准备好组件的数据输入。即分析好逻辑，定好 props 里面的数据、类型。
  　　3. 准备好组件的数据输出。即根据组件逻辑，做好要暴露出来的方法。
  　　4. 封装完毕了，直接调用即可
       **37.params和query的区别**
       答：用法：query要用path来引入，params要用name来引入，接收参数都是类似的，分别是this.$route.query.name和this.$route.params.name。
       url地址显示：query更加类似于我们ajax中get传参，params则类似于post，说的再简单一点，前者在浏览器地址栏中显示参数，后者则不显示
       注意点：query刷新不会丢失query里面的数据
       params刷新 会 丢失 params里面的数据。
       **38.vue初始化页面闪动问题**
       答：使用vue开发时，在vue初始化之前，由于div是不归vue管的，所以我们写的代码在还没有解析的情况下会容易出现花屏现象，看到类似于{{message}}的字样，虽然一般情况下这个时间很短暂，但是我们还是有必要让解决这个问题的。
       首先：在css里加上[v-cloak] {
       display: none;
       }。
       如果没有彻底解决问题，则在根元素加上style="display: none;" :style="{display: 'block'}"
       **39.vue更新数组时触发视图更新的方法**
       答:push()；pop()；shift()；unshift()；splice()； sort()；reverse()
       **40.vue常用的UI组件库**
       答：Mint UI，element，VUX
       **41.vue修改打包后静态资源路径的修改**
       答：cli2版本：将 config/index.js 里的 assetsPublicPath 的值改为 './' 。
       build: {
       ...
       assetsPublicPath: './',
       ...
       }
       cli3版本：在根目录下新建vue.config.js 文件，然后加上以下内容：（如果已经有此文件就直接修改）
       module.exports = {
       publicPath: '', // 相对于 HTML 页面（目录相同） }
       生命周期函数面试题
       **1.什么是 vue 生命周期？有什么作用？**
       答：每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做 生命周期钩子 的函数，这给了用户在不同阶段添加自己的代码的机会。（ps：生命周期钩子就是生命周期函数）例如，如果要通过某些插件操作DOM节点，如想在页面渲染完后弹出广告窗， 那我们最早可在mounted 中进行。
       **2.第一次页面加载会触发哪几个钩子？**
       答：beforeCreate， created， beforeMount， mounted
       **3.简述每个周期具体适合哪些场景**
       答：beforeCreate：在new一个vue实例后，只有一些默认的生命周期钩子和默认事件，其他的东西都还没创建。在beforeCreate生命周期执行的时候，data和methods中的数据都还没有初始化。不能在这个阶段使用data中的数据和methods中的方法
       create：data 和 methods都已经被初始化好了，如果要调用 methods 中的方法，或者操作 data 中的数据，最早可以在这个阶段中操作
       beforeMount：执行到这个钩子的时候，在内存中已经编译好了模板了，但是还没有挂载到页面中，此时，页面还是旧的
       mounted：执行到这个钩子的时候，就表示Vue实例已经初始化完成了。此时组件脱离了创建阶段，进入到了运行阶段。 如果我们想要通过插件操作页面上的DOM节点，最早可以在和这个阶段中进行
       beforeUpdate： 当执行这个钩子时，页面中的显示的数据还是旧的，data中的数据是更新后的， 页面还没有和最新的数据保持同步
       updated：页面显示的数据和data中的数据已经保持同步了，都是最新的
       beforeDestory：Vue实例从运行阶段进入到了销毁阶段，这个时候上所有的 data 和 methods ， 指令， 过滤器 ……都是处于可用状态。还没有真正被销毁
       destroyed： 这个时候上所有的 data 和 methods ， 指令， 过滤器 ……都是处于不可用状态。组件已经被销毁了。
       **4.created和mounted的区别**
       答：created:在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图。
       mounted:在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作。
       **5.vue获取数据在哪个周期函数**
       答：一般 created/beforeMount/mounted 皆可.
       比如如果你要操作 DOM , 那肯定 mounted 时候才能操作.
       **6.请详细说下你对vue生命周期的理解？**
       答：总共分为8个阶段创建前/后，载入前/后，更新前/后，销毁前/后。
       创建前/后： 在beforeCreated阶段，vue实例的挂载元素$el和**数据对象**data都为undefined，还未初始化。在created阶段，vue实例的数据对象data有了，$el还没有。
       载入前/后：在beforeMount阶段，vue实例的$el和data都初始化了，但还是挂载之前为虚拟的dom节点，data.message还未替换。在mounted阶段，vue实例挂载完成，data.message成功渲染。
       更新前/后：当data变化时，会触发beforeUpdate和updated方法。
       销毁前/后：在执行destroy方法后，对data的改变不会再触发周期函数，说明此时vue实例已经解除了事件监听以及和dom的绑定，但是dom结构依然存在。
       vue路由面试题
       **1.mvvm 框架是什么？**
       答：vue是实现了双向数据绑定的mvvm框架，当视图改变更新模型层，当模型层改变更新视图层。在vue中，使用了双向绑定技术，就是View的变化能实时让Model发生变化，而Model的变化也能实时更新到View。
       **2.vue-router 是什么?它有哪些组件**
       答：vue用来写路由一个插件。router-link、router-view
       **3.active-class 是哪个组件的属性？**
       答：vue-router模块的router-link组件。children数组来定义子路由
       **4.怎么定义 vue-router 的动态路由? 怎么获取传过来的值？**
       答：在router目录下的index.js文件中，对path属性加上/:id。 使用router对象的params.id。
       **5.vue-router 有哪几种导航钩子?**
       答：三种，
       第一种：是全局导航钩子：分为前置守卫、后置钩子。如前置钩子：router.beforeEach(to,from,next)，作用：跳转前进行判断拦截。
       第二种：组件内的钩子：beforeRouteEnter 不能获取组件实例 this，因为当守卫执行前，组件实例被没有被创建出来，剩下两个钩子则可以正常获取组件实例 this，但是并不意味着在 beforeRouteEnter 中无法访问组件实例，我们可以通过给 next 传入一个回调来访问组件实例。
       const File = {

    template: `<div>This is file</div>`,
    beforeRouteEnter(to, from, next) {
        // do someting
        // 在渲染该组件的对应路由被 confirm 前调用
    },
    beforeRouteUpdate(to, from, next) {
        // do someting
        // 在当前路由改变，但是依然渲染该组件是调用
    },
    beforeRouteLeave(to, from ,next) {
        // do someting
        // 导航离开该组件的对应路由时被调用
    }

第三种：单独路由独享组件
cont router = new VueRouter({
    routes: [
        {
            path: '/file',
            component: File,
            beforeEnter: (to, from ,next) => {
                // do someting
  }

**6.$route 和 $router 的区别**
答：$router是VueRouter的实例，在script标签中想要导航到不同的URL,使用$router.push方法。返回上一个历史history用$router.go(-1)
$route为当前router跳转对象。里面可以获取当前路由的name,path,query,parmas等。
**7.vue-router的两种模式**
答:hash模式：即地址栏 URL 中的 # 符号；
history模式：window.history对象打印出来可以看到里边提供的方法和记录长度。利用了 HTML5 History Interface 中新增的 pushState() 和 replaceState() 方法。（需要特定浏览器支持）。
**8.vue-router实现路由懒加载（ 动态加载路由 ）**
答:三种方式
第一种：vue异步组件技术 ==== 异步加载，vue-router配置路由 , 使用vue的异步组件技术 , 可以实现按需加载 .但是,这种情况下一个组件生成一个js文件。
第二种：路由懒加载(使用import)。
// import Add from './components/goods/Add.vue'
const Add = () => import(/* webpackChunkName: "GoodsList_Add" */ './components/goods/Add.vue')
第三种：webpack提供的require.ensure()，vue-router配置路由，使用webpack的require.ensure技术，也可以实现按需加载。这种情况下，多个路由指定相同的chunkName，会合并打包成一个js文件。
vuex常见面试题
**1.vuex是什么？怎么使用？哪种功能场景使用它？**
答：vue框架中状态管理。在main.js引入store，注入。
新建了一个目录store.js，….. export 。
场景有：单页应用中，组件之间的状态。音乐播放、登录状态、加入购物车
**2.vuex有哪几种属性？**
答：有五种，分别是 State、 Getter、Mutation 、Action、 Module
state => 基本数据(数据源存放地)
getters => 从基本数据派生出来的数据
mutations => 提交更改数据的方法，同步！
actions => 像一个装饰器，包裹mutations，使之可以异步。
modules => 模块化Vuex
**3.Vue.js中ajax请求代码应该写在组件的methods中还是vuex的actions中？**
答：如果请求来的数据是不是要被其他组件公用，仅仅在请求的组件内使用，就不需要放入vuex 的state里。
如果被其他地方复用，这个很大几率上是需要的，如果需要，请将请求放入action里，方便复用。