自定义事件
===

父组件是使用 props 传递数据给子组件，子组件要把数据传递回去，就需要通过自定义事件。


## 使用 `v-on` 绑定自定义事件
每个 Vue 实例都实现了事件接口(Events interface)，即：

* 使用 $on(eventName) 监听事件
* 使用 $emit(eventName) 触发事件

> Vue的事件系统分离自浏览器的 [EventTarget API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget)。尽管它们的运行类似，但是 `$on` 和 `$emit` 不是 `addEventListener` 和 `dispatchEvent` 的别名。

另外，父组件可以在使用子组件的地方直接用 v-on 来监听子组件触发的事件。

看 demo01 实现：
```html
<div id="app">
    <p>{{ total }}</p>
    <button-counter v-on:increment="incrementTotal"></button-counter>
    <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
```

```js
Vue.component('button-counter', {
    template: '<button v-on:click="increment">{{ counter }}</button>',
    data: function () {
        return {
            counter: 0
        }
    },
    methods: {
        increment: function () {
            this.counter += 1;
            // 触发事件
            this.$emit('increment');
        }
    },
});

new Vue({
    el: '#app',
    data: {
        total: 0
    },
    methods: {
        incrementTotal: function () {
            this.total += 1;
        }
    }
});
```

在本例中，子组件已经和它外部完全解耦了。它所做的只是触发一个父组件关心的内部事件。


### 给组件绑定原生事件
有时候，你可能想在某个组件的根元素上监听一个原生事件，可以使用 `.native` 修饰 `v-on`。
```
<my-component v-on:click.native="doTheThing"></my-component>
```


## 使用自定义事件的表单输入组件
自定义事件也可以用来创建自定义的表单输入组件，使用 `v-model` 来进行数据双向绑定。牢记：
```html
<input v-model="something">
```

仅仅是一个语法糖：
```html
<input v-bind:value="something" v-on:input="something = $event.target.value">
```

所以在组件中使用时，它相当于下面的简写：
```html
<custom-input v-bind:value="something" v-on:input="something = arguments[0]"></custom-input>
```

所以要让组件的 `v-model` 生效，它必须：

* 接受一个 `value` 属性
* 在有新的 `value` 时触发 input 事件










