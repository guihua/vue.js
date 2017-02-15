用组件构建（应用）
---

组件系统是 Vue.js 另一个重要概念，因为它提供了一种抽象，让我们可以用独立可复用的小组件来构建大型应用。如果我们考虑到这点，几乎任意类型的应用的界面都可以抽象为一个组件树：

![组件](./components.png)

在 Vue 里，一个组件实质上是一个拥有预定义选项的一个 Vue 实例：
```
// Define a new component called todo-item
Vue.component('todo-item', {
    template: '<li>This is a todo</li>'
});
```

现在你可以在另一个组件模板中写入它：
```
<ol>
    <!-- Create an instance of the todo-item component -->
    <todo-item></todo-item>
</ol>
```

但是这样会为每个 todo 渲染同样的文本，这看起来并不是很酷。我们应该将数据从父作用域传到子组件。让我们来修改一下组件的定义，使得它能够接受一个 prop 字段：
```
Vue.component('todo-item', {
    // The todo-item component now accepts a
    // "prop", which is like a custom attribute.
    // This prop is called todo.
    props: ['todo'],
    template: '<li>{{ todo.text }}</li>'
});
```

现在，我们可以使用 `v-bind` 指令将 todo 传到每一个重复的组件中：
```
<div id="app">
    <ol>
        <todo-item v-for="item in groceryList" v-bind:todo="item"></todo-item>
    </ol>
</div>
```

```
Vue.component('todo-item', {
    props: ['todo'],
    template: '<li>{{ todo.text }}</li>'
});

var app = new Vue({
    el: '#app',
    data: {
        groceryList: [
            { text: 'Vegetables' },
            { text: 'Cheese' },
            { text: 'Whatever else humans are supposed to eat' }
        ]
    }
});
```