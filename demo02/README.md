条件与循环
---

### 条件控制
条件控制通过 `v-if` 指令进行：
```
<div id="app">
    <p v-if="seen">Now you see me</p>
</div>
```

```
var app = new Vue({
    el: '#app',
    data: {
        seen: true
    }
});
```

在控制台设置 `app3.seen = false`，你会发现页面上展示的文本消失了。


### 循环控制
循环控制通过指令 `v-for` 控制：
```
<div id="app">
    <ol>
        <li v-for="todo in todos">
            {{ todo.text }}
        </li>
    </ol>
</div>
```

```
var app = new Vue({
    el: '#app',
    data: {
        todos: [
            { text: 'Learn JavaScript' },
            { text: 'Learn Vue' },
            { text: 'Build something awesome' }
        ]
    }
});
```

在控制台里，输入 `app4.todos.push({ text: 'New item' })`，你会发现列表中多了一栏新内容。