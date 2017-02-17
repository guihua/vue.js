杂项
===

## 编写可复用组件
在编写组件时，记住是否要复用组件有好处。一次性组件跟其它组件紧密耦合没关系，但是可复用组件应当定义一个清晰的公开接口。

Vue 组件的 API 来自三部分 - `props`, `events` 和 `slots`：

* **Props** 允许外部环境传递数据给组件

* **Events** 允许组件触发外部环境的副作用

* **Slots** 允许外部环境将额外的内容组合在组件中。

使用 `v-bind` 和 `v-on` 的简写语法，模板的缩进清楚且简洁：
```html
<my-component
    :foo="baz"
    :bar="qux"
    @event-a="doThis"
    @event-b="doThat">
    <img slot="icon" src="...">
    <p slot="main-text">Hello!</p>
</my-component>
```


## 子组件索引
尽管有 props 和 events ，但是有时仍然需要在 JavaScript 中直接访问子组件。为此可以使用 `ref` 为子组件指定一个索引 ID 。例如：
```html
<div id="parent">
    <user-profile ref="profile"></user-profile>
</div>
```

```js
var parent = new Vue({
    el: '#parent'
});
// 访问子组件
var child = parent.$refs.profile;
```

当 `ref` 和 `v-for` 一起使用时，`ref` 是一个数组或对象，包含相应的子组件。

> `$refs` 只在组件渲染完成后才填充，并且它是非响应式的。它仅仅作为一个直接访问子组件的应急方案——应当避免在模版或计算属性中使用 `$refs`。


## 异步组件
在大型应用中，我们可能需要将应用拆分为多个小模块，按需从服务器下载。为了让事情更简单， Vue.js 允许将组件定义为一个工厂函数，动态地解析组件的定义。Vue.js 只在组件需要渲染时触发工厂函数，并且把结果缓存起来，用于后面的再次渲染。例如：
```js
Vue.component('async-example', function (resolve, reject) {
    setTimeout(function () {
        // Pass the component definition to the resolve callback
        resolve({
            template: '<div>I am async!</div>'
        });
    }, 1000)
});
```

工厂函数接收一个 `resolve` 回调，在收到从服务器下载的组件定义时调用。也可以调用 `reject(reason)` 指示加载失败。这里 `setTimeout` 只是为了演示。怎么获取组件完全由你决定。

推荐配合使用 ：Webpack 的代码分割功能。
```js
Vue.component('async-webpack-example', function (resolve) {
    // 这个特殊的 require 语法告诉 webpack
    // 自动将编译后的代码分割成不同的块，
    // 这些块将通过 Ajax 请求自动下载。
    require(['./my-async-component'], resolve)
});
```

你可以使用 Webpack 2 + ES2015 的语法返回一个 `Promise resolve` 函数：
```js
Vue.component(
    'async-webpack-example',
    () => System.import('./my-async-component')
);
```


## 组件命名约定
当注册组件（或者 props）时，可以使用 kebab-case ，camelCase ，或 TitleCase 。Vue 不关心这个。
```js
// 在组件定义中
components: {
    // 使用 kebab-case 形式注册
    'kebab-cased-component': { /* ... */ },
    // register using camelCase
    'camelCasedComponent': { /* ... */ },
    // register using TitleCase
    'TitleCasedComponent': { /* ... */ }
}
```

在 HTML 模版中，请使用 kebab-case 形式：
```html
<!-- 在HTML模版中始终使用 kebab-case -->
<kebab-cased-component></kebab-cased-component>
<camel-cased-component></camel-cased-component>
<title-cased-component></title-cased-component>
```

当使用字符串模式时，可以不受 HTML 的 case-insensitive 限制。这意味实际上在模版中，你可以使用 camelCase 、 TitleCase 或者 kebab-case 来引用：
```html
<!-- 在字符串模版中可以用任何你喜欢的方式! -->
<my-component></my-component>
<myComponent></myComponent>
<MyComponent></MyComponent>
```

如果组件未经 slot 元素传递内容，你甚至可以在组件名后使用 `/` 使其自闭合：
```html
<my-component/>
```

当然，这只在字符串模版中有效。因为自闭的自定义元素是无效的 HTML ，浏览器原生的解析器也无法识别它。
