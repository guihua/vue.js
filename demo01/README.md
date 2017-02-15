声明式渲染
---

Vue.js 的核心是一个允许你采用简洁的模板语法来声明式的将数据渲染进 DOM 的系统：
```
<div id="app">
    <p>{{message}}</p>
</div>
```

```
var app = new Vue({
    el: '#app',
    data: {
        message: 'Hello Vue.js'
    }
});
```

现在数据和 DOM 已经被绑定在一起，所有的元素都是响应式的。

打开你的浏览器的控制台，并修改 app.message，你将看到上例相应地更新。

除了绑定插入的文本内容，我们还可以采用这样的方式绑定 DOM 元素属性：
```
<div id="app">
    <span v-bind:title="message">
        Hover your mouse over me for a few seconds to see my dynamically bound title!
    </span>
</div>
```

```
var app = new Vue({
    el: '#app',
    data: {
        message: 'You loaded this page on ' + new Date()
    }
});
```

上面的 `v-bind` 属性被称为指令。指令带有前缀 `v-`，以表示它们是 Vue.js 提供的特殊属性。

这个指令的简单含义是说：将这个元素节点的 `title` 属性和 Vue 实例的 `message` 属性绑定到一起。

你再次打开浏览器的控制台输入 `app2.message = 'some new message'`，你就会再一次看到这个绑定了 `title` 属性的 HTML 已经进行了更新。