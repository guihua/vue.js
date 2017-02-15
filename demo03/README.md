处理用户输入
---

为了让用户和你的应用进行互动，我们可以用 `v-on` 指令绑定一个监听事件用于调用我们 Vue 实例中定义的方法：
```
<div id="app">
    <p>{{ message }}</p>
    <button v-on:click="reverseMessage">Reverse Message</button>
</div>
```

```
var app = new Vue({
    el: '#app',
    data: {
        message: 'Hello Vue.js!'
    },
    methods: {
        reverseMessage: function () {
            this.message = this.message.split('').reverse().join('')
        }
    }
});
```

在 `reverseMessage` 方法中，我们在没有接触 DOM 的情况下更新了应用的状态 - 所有的 DOM 操作都由 Vue 来处理，你写的代码只需要关注基本逻辑。

Vue 也提供了 `v-model` 指令，它使得在表单输入和应用状态中做双向数据绑定变得非常轻巧。
```
<div id="app">
    <p>{{ message }}</p>
    <input v-model="message">
</div>
```

```
var app = new Vue({
    el: '#app',
    data: {
        message: 'Hello Vue.js!'
    }
});
```
在 input 中输入后，对应的文本会进行实时变更。