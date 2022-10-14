```
vue3感觉主要是对于数据双向绑定的优化

1. setup
        在beforeCreate之前执行一次，此时组件对象还没有创建
        ===> this是undefined
        ===> 所有composition API相关回调函数中也都不可以使用

        参数：
            setup(props, context) / setu(props, (attrs, slots, emit))
            props:配置声明且传入了的所有属性的对象
            attrs:包含没有在props配置声明的属性的对象（this.$attrs）
            slots:包含所有传入的插槽内容的对象，(this.$slots)
            emit:用来分发自定义时间的函数(this.$emit)

        返回值：
            一般返回一个对象：为模板提供数据
            返回值与data函数合并为组件对象属性
            返回对象中方法与Methods合并为组件对象的方法


2. ref 对象仅有一个 .value property，指向该内部值，但页面展示进绑定对象原值

3. reactive 深层响应式转换
        reactive 将解包所有深层的 refs，同时维持 ref 的响应性
        当将 ref 分配给 reactive property 时，ref 将被自动解包

ref用来处理基本数据类型和reactive用来处理对象
当然，ref里也可以直接放对象
内部实现逻辑不同
ref内部是通过getter/setter实现对数据的劫持
reactive是使用proxy来实现对对象内部所有的数据劫持，并通过Reflect操作对象内部数据

vue3响应式对象的处理逻辑：
    将目标对象变成代理对象，通过反射进行代理
    user = {
        name: 'xh',
        age: 18
    }
    const proxy = new Proxy(user, {
        get(target, prop) {
            return Reflect.get(target, prop)
        }
        set(target, prop, val) {
            return Reflect.set(target, prop, val)
        }
        deleteProperty(target, prop) {
            return Reflect.deleteProperty(target, prop)
        }
    })

toRefs() 无论多深都响应式
toRef()  为源响应式数据对象上某个属性创建一个ref对象，二者内部操作同一个数据值，更新两者同步(区别于ref，是不互相影响的)在传递参数props时对原对象做解构时特别有用
toRaw() 将响应式对象变为普通对象
markRaw() 一般使用组件时，将其永远声明为非响应式变量，提升性能

总结ref,reactive,toRef,toRefs区别
ref仅仅是转换为响应式对象（基本数据类型），reactive是转换一个对象，toRef（为已经有响应式数据的对象上的某个属性创建一个ref对象），toRefs就是批量的toRef（某个响应式对象创建所有的ref对象）

isRef() 判断是否是响应式数据
Ref: 写法 let message: Ref<string> = ref('hello world')
shallowRef: 节省性能，对于对象这一层进行响应式而内部数据没有响应式，可以通过triggerRef强制更新dom(不建议)
customRef: 自定义数据绑定（用于需要在数据发生响应时做出对应操作使用）
shallowReactive: 节省性能，只对内部第一层进行响应式处理(仅仅对于dom挂载之后)

注意：ref触发视图更新，会导致其他数据也更新

4. 计算属性 computed,返回一个ref对象，会缓存对象
        如果只传入一个函数，表示get
        const name = computed(() => xx+yy)
        如果传入对象，表示get/set
        const fullName2 = computed({
            get() {
                return user.firstName + '_' + user.lastName
            }
            set(val: string) {
                const names = val.split('_')
                user.firstName = names[0]
                user.lastName = names[1]
            }
        })

5. watch(obj, ({prop}) => {}, {edit}) / watchEffect()会默认监视一次
        当监听非响应式数据时，采用匿名函数形式 () => xx
    只能监听响应式内容数据对象，如果想监听所有，开启deep: true设置

    watchEffect返回一个函数，可以停止该监听
    watchEffect参数oninvalidate可以在监听之前do something，
    const stop = watchEffect((oninvalidate) => {
        oninvalidate(() => {
            console.log('before') // 可以在监听之前do something
        })
    })
    cosnt stopWatchEffect = () => stop()

defineProp<{obj}>() 传递参数
withDefaults(
    defineProp<{obj}>(), {对象实例}
)
defineExpose<{}>() 暴露给父组件使用

异步组件，一般与之配合的有Suspense组件
const A = defineAsyncComponent(() => import('../../xx') )
<Suspense>
    <template #default>
        组件
    </template>
    <template #fallback>
        loading
    </template>
</Suspense>

一种能够将我们的模板渲染至指定DOM节点
<Teleport to="body">
    <Loading></Loading>
</Teleport>
```
