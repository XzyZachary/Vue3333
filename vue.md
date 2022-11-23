vue3

### toRef
* ref 是对值类型创造响应式的方法  reactive 引用类型变成响应式
* toRef、toRefs 是延续引用类型响应式对象的方法
* unref 取消响应  如果是一个ref值，就返回当前value，如果不是就返回本身的自己 它其实就是下面语法糖的写法

### watchEffect
    watchEffect 相当于将 watch 的依赖源和回调函数合并，当任何你有用到的响应式依赖更新时，该回调函数便会重新执行。不同于 watch，watchEffect 的回调函数会被立即执行（即 { immediate: true }）

### effectScope

    在Vue的setup中，响应会在开始初始化的时候被收集，在实例被卸载的时候，响应就会自动的被取消追踪了，这时一个很方便的特性。但是，当我们在组件外使用或者编写一个独立的包时，这会变得非常麻烦。当在单独的文件中，我们该如何停止computed & watch的响应式依赖呢？

### shallowRef

    和 ref() 不同，浅层 ref 的内部值将会原样存储和暴露，并且不会被深层递归地转为响应式。只有对 .value 的访问是响应式的。shallowRef() 常常用于对大型数据结构的性能优化或是与外部的状态管理系

### teleport
    传送门 

### toRaw
    返回 reactive 或 readonly 代理的原始对象。这是一个转义口，可用于临时读取而不会引起代理访问/跟踪开销，也可用于写入而不会触发更改

### markRaw
    标记一个对象，使其永远不会转换为代理。返回对象本身。

### v-once
    Vue.js 提供了一个指令，以便只渲染一次元素和组件，并且跳过以后的更新。


hooks  
```typescript
function useToggle(init: boolean): [Ref<boolean>, () => void] {
	const state: boolean = ref<boolean>(init)
  const setState = () => state.value = !state.value
  
  return [state, setState]
}
```
```typescript
/* Make it work */
:global(body) {
    width: 100vw;
    height: 100vh;
    background-color: burlywood;
}
   
/* 还有一个插槽选择器 */
:slotted(selector){ }
 
/* deep选择器 */
:deep(selector){ }
```
```typescript
import { defineComponent, h } from 'vue';

export default defineComponent({
  name: 'MyButton',
  props: {
    disabled: {
      type: Boolean,
      default: false,
    },
  },
  emit: ['custom-click'],
  setup(props, { emit, slots }) {
    return () => h(
      'button',
      {
        disabled: props.disabled,
        onClick: () => emit('custom-click'),
      },
      slots.default?.()
    );
  },
});
```
```typescript
// 自定义组件

const VueJs = defineCustomElement({
    props: {
      message: String
    },
    render() {
      return h(
        'span',
        {},
        this.message
      )
    }
  })
```
```typescript

//   自定义ref
  function useDebouncedRef(value, delay = 200) {
    let timer;
    return customRef((track, trigger) => {
      return {
        get() {
          track()
          return value
        },
        set(newValue) {
          value = newValue
          clearTimeout(timer)
          timer = setTimeout(() => {
            trigger()
          }, delay)
        }
      }
    }) 
  }
```
```typescript

  // 自定义指令

  const VActiveStyle = {
    mounted(el: HTMLLiElment, binding: Binding) {
      const [ cssObj, fn ] = binding.value;
      watchEffect(() => {
         if (fn && fn()) {
            Object.keys(cssObj).forEach(key => {
              el.style[key] = unref(cssObj[key])
            })
         } else {
           Object.keys(cssObj).forEach(key => {
             el.style[key] = ''
           })
         }
      })
    }
  }
```
```typescript
  // v-mode简易版
  const VOhModel = {
    mounted(el: HTMLInputElement, binding: Binding) {
      const { value } = binding;
      const elValue = el.value;
      if (value && elValue !== value) {
        el.value = unref(value);
      }
      el.addEventListener(
        'input',
        (e: HTMLInputElement) => {
          const curValue = e.target.value;
          if (values && elValue !== curValue) {
            values.value = curValue;
          }
        },
        false
      );
    },
    unmounted(el: HTMLInputElement) {
      el.removeEventListener(
        'input',
        (e: HTMLInputElement) => {
          const curValue = e.target.value;
          if (values.value !== curValue) {
            values.value = curValue;
          }
        },
        false
      );
    },
  };

  ```
