# 一、 渲染问题

1. 小心层级过深导致渲染失败/监听失效

2. 不触发接口的表格删除操作，用于自定义内容回显问题

   如图所示：

   ![image-20211119144640756](/Users/xiaohang/Library/Application Support/typora-user-images/image-20211119144640756.png)

   问题：点击确定按钮，提示内容是表格内内容，代码逻辑

   ```js
   this.$refs.senseTable.remove(row)
   this.table.list.splice(this.table.list.indexOf(row), 1)
   this.updateDataItems(this.table.list)
   ```

   这种情况下会先去把表格删除指定行内容，导致popconfirm提示内容中会去取最新的数据回显（此处触发了vue双向绑定机制渲染了页面），所以造成了闪烁不好的视觉体验。

   思路：我把表格数据存起来，然后删除存起来的数据，最后把数据赋值给页面上的table即可（逻辑来自于接口请求直接覆盖原有数据的思路）

3. 运用子组件时渲染时机问题

   ```
   生命周期汇总：
   beforeCreate：在初始化实例之后，数据（data）和事件配置（event/watcher）之前调用
   							====> 此时data和el均为undefined
   created：在实例创建完成后被立即调用，完成以下操作：数据（data）、property、方法运算和事件配置（event/watcher）
   							====>	此时可进行数据赋值，但el与property均不可用
   beforeMounted：在挂载之前，相关render函数首次被调用
   							====>	此时el也被初始化了，但还没有渲染进数据，el的值是“虚拟”元素节点
   mounted：实例被挂载之后，el被新创建的vm.$el替换（如果根实例挂载到了一个文档内的元素上，当mounted被调用时，vm.$el也在文档内，可以对挂载的dom进行操作）
   							====>	注意：mounted不会保证所有的子组件都一起被挂载(异步)
   beforeUpdate：数据更新时调用，发生在虚拟DOM打补丁之前
   							====>	适合更新之前访问现有DOM，比如手动移除已添加的事件监听器
   updated：在由于数据更改导致虚拟DOM重新渲染和打补丁之后，实际情况一般使用computed或watcher函数来监听属性的变化，因为不能准确地判断是哪个属性值被改变
   activated：被Keep-alive缓存地组件激活时调用
   							====> 如果是Vue-router使用了Keep-alive缓存组件状态，这个时候created钩子不会被重复调用，这时候我们需要每次加载或切换状态进行某些操作，可使用activated钩子
   deactivated：Keep-alive缓存的组件停用时调用
   beforeDestory：实例被销毁之前调用
   							====> 此时实例任可被调用
   destoryed：实例销毁之后，对应Vue实例的所有指令都被解绑，所有的事件监听器被移除，所有的子实例也都被销毁
   errorCaptured：当捕获一个来自子孙组件的错误时被调用，接受三个参数：（错误对象，发生错误的组件实例，包含错误来源信息的字符串），如果返回false则阻止向上继续传播
   
   
   加载渲染顺序：
   parent beforeCreated
   parent created
   parent beforeMounted
   child beforeCreated
   child created
   child beforeMounted
   child mounted
   child activated
   parent mounted
   
   更新顺序：
   parent beforeUpdate
   child beforeUpdate
   child updated
   parent updated
   
   销毁顺序：
   parent beforeDestroy
   child beforeDestroy
   child destroyd
   parent destroyd
   
   deactivated 执行时机：
   	deactivated函数出发时间是在视图更新时出发（当视图更新才能知道Keep-alive组件被停用），所有有两种情况
   1.
   parent beforeUpdate
   ...
   child deactivated
   parent updated
   
   2.
   parent beforeDestroy
   parent deactivated
   child beforeDestroy
   child destroyd
   parent destroyd
   
   一般还是用 条件判断式/router-view判断
   1.条件判断式：v-if
   2.router-view：
   <keep-alive include="child">
        <router-view></router-view>
   </keep-alive>
   
   include 为每个子组件的name字段，不是文件名；如果 name 不可用，则匹配当前组件 components 配置中的注册名称。
   
   当匹配条件同时在 include 与 exclude 存在时，以 exclude 优先级最高。
   ```


4. 当一个 Vue 实例被创建时，它将 `data` 对象中的所有的 property 加入到 Vue 的**响应式系统**中。当这些 property 的值发生改变时，视图将会产生“响应”，即匹配更新为新的值

   ```
   如果vm.a = 'hi' a没有在实例创建时被绑定到data对象中，那么这个数据将不会是响应式数据，所以这时候你需要给data对象绑定初始值来达到目的
   ```

5. V-once 可以让节点只渲染一次，从而不会随着数据的改变而改变该节点

6. 父级 prop 的更新会向下流动到子组件中，但是反过来则不行

   ```
   单向数据流是Vue组件一个非常明显的特征，不应该在子组件中直接修改props的值
   
   如果传递的prop仅仅用作展示，不涉及修改，则在模板中直接使用即可
   如果需要对prop的值进行转化然后展示，则应该使用computed计算属性
   如果prop的值用作初始化，应该定义一个子组件的data属性并将prop作为其初始值（重点：数组和对象，修改子组件任然会改变父组件的值，源码使用浅拷贝实现的）
   ```



## 二、编码习惯

1. computed用来计算某些HTML上的逻辑判断

   方法也能达到同样的目的，但是**计算属性是基于它们的响应式依赖进行缓存的**，也就是说值不变，就不会重新计算，不会多次渲染

   ```
   computed: {
       // 计算属性的 默认写return只有getter
       reversedMessage: {
         // `this` 指向 vm 实例
         return this.message.split('').reverse().join('')
       }
     }
     
     computed: {
       // 计算属性的 getter setter
       reversedMessage: {
         get() {
         	return this.message.split('').reverse().join('')
         },
         set(val) {
         	this.xxx = val
         }
       }
     }
   ```

2. 防抖节流文件（都是对于短时间内大量重复请求的处理）

   闭包：只会执行return里面的函数体内容

   ```
   // 防抖、节流js文件
   export default {
     // 防抖 短时间内大量执行同一个函数，但只执行一次
     debounce(func, wait) {
       let timeout
       return function() {
         const context = this
         const args = [...arguments]
         if (timeout) clearTimeout(timeout)
         timeout = setTimeout(() => {
           func.apply(context, args)
         }, wait)
       }
     },
     // 节流 短时间内只执行一次此函数
     throttle(func, wait) {
       let timeout
       return function() {
         const context = this
         const args = arguments
         if (!timeout) {
           timeout = setTimeout(() => {
             timeout = null
             func.apply(context, args)
           }, wait)
         }
       }
     }
   }
   ```

   ```
   ant-formModel校验防抖设置
   validator: _.debounce(validateName, 500)
   ```
   
3. 三层组件时，使用$attrs（父=`v-bind="$attrs"`>孙）`inheritAttrs: false`与$listeners（孙=`v-on="$listeners"`>父）夸组件传值

4. 动态渲染（变换）组件 `v-bind:is`，这种特殊属性主要还是用于区分组件与原DOM元素区别

   ```vue
   <div id="app">
   <component v-bind:is="whichcomp"></component>
   <button v-on:click="choosencomp('a')">a</button>
   <button v-on:click="choosencomp('b')">b</button>
   <button v-on:click="choosencomp('c')">c</button>
   </div>
   
   
   var app=new Vue({
     el: '#app',
   	components:{
   		acomp:{
   		   template:`
   				<p>这里是组件A</p>
   			`
   			},
   		bcomp:{
   		   template:`
   				<p>这里是组件B</p>		`
   		},
   		ccomp:{
   			template:`
   				<p>这里是组件C</p>
   		`
   		}},
   	data:{whichcomp:""},
   	methods:{
   	   choosencomp:function(x){
   	   this.whichcomp=x+"comp"}
      }
   })
   ```

5. `.lazy`修饰符，在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 (除了[上述](https://cn.vuejs.org/v2/guide/forms.html#vmodel-ime-tip)输入法组合文字时)。你可以添加 `lazy` 修饰符，从而转为在 `change` 事件_之后_进行同步：

   ```HTML
   <!-- 在“change”时而非“input”时更新 -->
   <input v-model.lazy="msg">
   
   此外还有.trim(去空格)和.number(校验数据parseFloat())
   ```

6. props 传递&参数

   如果传参了但是没有接收的props，这些 attribute 会被添加到这个组件的根元素上：

   这就导致如果你传递了type: 'input' 之类的特殊字符会默认覆盖掉 

   ====>（inheritAttrs: false可以不继承到根元素上）

   然而：class/style 会合并

   ```vue
   props: {
   	propA: {
   		type: ['String','Number','Boolean','Array','Object','Date','Function','Symbol'],
   		required: true,
   		default: '',
   		validator: function (value) {
           // 这个值必须匹配下列字符串中的一个
           return ['success', 'warning', 'danger'].indexOf(value) !== -1
         }
   	}
   }
   ```

7. keep-alive 频繁切换又不想重新生成组件

   ```
   <!-- 失活的组件将会被缓存！-->
   <keep-alive>
     <component v-bind:is="currentTabComponent"></component>
   </keep-alive>
   ```

8. 组件间循环引用

   ```vue
   1.beforeCreate 时去注册它
   beforeCreate: function () {
     this.$options.components.TreeFolderContents = require('./tree-folder-				 	contents.vue').default
   }
   
   2.使用 webpack 的异步 import
   components: {
     TreeFolderContents: () => import('./tree-folder-contents.vue')
   }
   ```

9. 时刻提防`空数据` 到来的问题

   ```
   例如：JSON.parse(JSON.stringify(oldTreeItem))
   接受参数undefined就会有问题
   ```

   

## 三、VUE

1. v-model语法糖本质

   ```VUE
   一个组件上的 v-model 默认会利用名为 value 的 prop 和名为 input 的事件，但是像单选框、复选框等类型的输入控件可能会将 value attribute 用于不同的目的。model 选项可以用来避免这样的冲突：
   
   Vue.component('base-checkbox', {
     model: {
       prop: 'checked',
       event: 'change'
     },
     props: {
       checked: Boolean
     },
     template: `
       <input
         type="checkbox"
         v-bind:checked="checked"
         v-on:change="$emit('change', $event.target.checked)"
       >
     `
   })
   
   <base-checkbox v-model="lovingVue"></base-checkbox>
   
   这里的 lovingVue 的值将会传入这个名为 checked 的 prop。同时当 <base-checkbox> 触发一个 change 事件并附带一个新的值的时候，这个 lovingVue 的 property 将会被更新。
   ```

2. .sync语法糖（实现双向绑定）

   ```
   <text-document
     v-bind:title="doc.title"
     v-on:update:title="doc.title = $event"
   ></text-document>
   
   等价与：
   <text-document v-bind:title.sync="doc.title"></text-document>
   
   真正的双向绑定会带来维护问题
   加了.sync相当于默认绑定函数v-on:update:xxx="doc.xxx = $event"
   如果想更新使用函数this.$emit('update:xxx', newTitle)
   ```

   
