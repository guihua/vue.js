Vue.js 组件
---

组件（Component）是 Vue.js 最强大的功能之一。

组件可以扩展 HTML 元素，封装可重用的代码。在较高层面上，组件是自定义元素， Vue.js 的编译器为它添加特殊功能。在有些情况下，组件也可以是原生 HTML 元素的形式，以 is 特性扩展。


### 注册组件
要注册一个全局组件，你可以使用 `Vue.component(tagName, options)`。
```
Vue.component('my-component', {
    // 选项
});
```

组件在注册之后，便可以在父实例的模块中以自定义元素 `<my-component></my-component>` 的形式使用。

```
<div id="example">
    <my-component></my-component>
</div>
```

> 要确保在初始化根实例 **之前** 注册了组件。

```
Vue.component('my-component', {
    template: '<div>My first vue.js component!</div>'
});

var app = new Vue({
    el: '#app'
});
```


### 局部注册
不必在全局注册每个组件。

通过使用组件实例选项注册，可以使组件仅在另一个实例/组件的作用域中可用：
```
var Child = {
    template: '<div>A custom componnet!</div>'
}

var app = new Vue({
    el: '#app',
    components: {
        // <my-component> 将只在父模板可用
        'my-component': Child
    }
});
```

使用：
```
<div id="app">
    <my-component></my-component>
</div>
```

这种封装也适用于其它可注册的 Vue 功能，如指令。


### `data` 必须是函数
使用组件时，大多数可以传入到 Vue 构造器中的选项可以在注册组件时使用，有一个例外： `data` 必须是函数。

```
<div id="app">
    <simple-counter></simple-counter>
    <simple-counter></simple-counter>
    <simple-counter></simple-counter>
</div>
```

```
var data = {
    counter: 0
};

Vue.component('simple-counter', {
    template: '<button v-on:click="counter += 1">{{ counter }}</button>',
    // data 是一个函数，因此 Vue 不会警告，
    // 但是我们为每一个组件返回了同一个对象引用
    data: function () {
        return data;
    }
})

var app = new Vue({
    el: '#app'
});
```

由于这三个组件共享了同一个 `data` ， 因此增加一个 `counter` 会影响所有组件！我们可以通过为每个组件返回新的 `data` 对象来解决这个问题：
```
return {
    counter: 0
}
```