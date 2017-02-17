动态组件
===

多个组件可以使用同一个挂载点，然后动态地在它们之间切换。
```js
var app = new Vue({
    el: '#app',
    data: {
        currentView: 'home'
    },
    components: {
        home: {
            template: '<p>Welcome home!</p>'
        },
        posts: {
            template: '<p>Welcome posts!</p>'
        },
        archive: {
            template: '<p>Welcome archive!</p>'
        }
    }
});
```

使用保留的 `<component>` 元素，动态地绑定到它的 `is` 特性：
```html
<!-- 组件在 vm.currentview 变化时改变！ -->
<component v-bind:is="currentView"></component>
```

也可以直接绑定到组件对象上：
```js
var Home = {
    template: '<p>Welcome home!</p>'
};

var app = new Vue({
    el: '#app',
    data: {
        currentView: Home
    }
});
```


## keep-alive
如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。

为此可以添加一个 `keep-alive` 指令参数：
```html
<keep-alive>
    <component :is="currentView">
        <!-- 非活动组件将被缓存！ -->
    </component>
</keep-alive>
```