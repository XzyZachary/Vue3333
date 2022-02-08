# Vue3333
Vue3333自学

# vue2的注意事项
在某些情况下，我们可能需要动态地创建一个方法函数，比如创建一个预置防抖的事件处理器：

```javascript
import { debounce } from 'lodash-es'

export default {
  methods: {
    // 使用 Lodash 的防抖函数
    click: debounce(function () {
      // ... 对点击的响应 ...
    }, 500)
  }
}
```
不过这种方法对于被重用的组件来说是有问题的，因为这个预置防抖的函数是 有状态的：它在运行时维护着一个内部状态。如果多个组件实例都共享这同一个预置防抖的函数，那么它们之间将会互相影响。

要保持每个组件实例的防抖函数都彼此独立，我们可以改为在 created 生命周期钩子中创建这个预置防抖的函数：
```javascript
export default {
  created() {
    // 每个实例都有了自己的预置防抖的处理函数
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // 最好是在组件卸载时
    // 清除掉防抖计时器
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... 对点击的响应 ...
    }
  }
}
```

# vue3响应式代理

为保证访问代理的一致性，对同一个对象调用 reactive() 会总是返回同样的代理，而对代理调用 reactive() 则会返回它自己

```javascript
const raw = {}
const proxy = reactive(raw)

// 代理和原始对象不是全等的
console.log(proxy === raw) // false

// 在同一个对象上调用 reactive() 会返回相同的代理
console.log(reactive(raw) === proxy) // true

// 在一个代理上调用 reactive() 会返回它自己
console.log(reactive(proxy) === proxy) // true

// 这个规则对深层级的对象也适用。依靠深层响应性，响应式对象内的深层级对象依然是代理
const proxy = reactive({})
const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

reactive() API 有两条限制：
1、仅对对象类型有效（对象、数组和 Map、Set 这样的集合类型），而对 string、number 和 boolean 这样的 基础类型 无效。
2、因为 Vue 的响应式系统是通过属性访问进行追踪的，因此我们必须始终保持对该响应式对象的引用。这意味着我们不可以随意地 “替换” 一个响应式对象






















