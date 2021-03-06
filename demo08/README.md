使用 Slot 分发内容
===

在使用组件时，常常要像这样组合它们：
```html
<app>
    <app-header></app-header>
    <app-footer></app-footer>
</app>
```

注意两点：

1. `<app>` 组件不知道它的挂载点会有什么内容。挂载点的内容是由 `<app>` 的父组件决定的。

2. `<app>` 组件很可能有它自己的模版。

为了让组件可以组合，我们需要一种方式来混合父组件的内容与子组件自己的模板。这个过程被称为 **内容分发** (或 “transclusion” 如果你熟悉 Angular)。Vue.js 实现了一个内容分发 API ，参照了当前 [Web 组件规范草案](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md)，使用特殊的 `<slot>` 元素作为原始内容的插槽。


## 编译作用域
在深入内容分发 API 之前，我们先明确内容的编译作用域。

假定模板为：
```html
<child-component>
    {{ message }}
</child-component>
```

`message` 应该绑定到父组件的数据，还是绑定到子组件的数据？答案是父组件。

组件作用域简单地说是：**父组件模板的内容在父组件作用域内编译；子组件模板的内容在子组件作用域内编译**。

一个常见错误是试图在父组件模板内将一个指令绑定到子组件的属性/方法：
```html
<!-- 无效 -->
<child-component v-show="someChildProperty"></child-component>
```

假定 `someChildProperty` 是子组件的属性，上例不会如预期那样工作。父组件模板不应该知道子组件的状态。

如果要绑定子组件内的指令到一个组件的根节点，应当在它的模板内这么做：
```js
Vue.component('child-component', {
    // 有效，因为是在正确的作用域内
    template: '<div v-show="someChildProperty">Child</div>',
    data: function () {
        return {
            someChildProperty: true
        }
    }
});
```

类似地，分发内容是在父组件作用域内编译。


## 单个 Slot
除非子组件模板包含至少一个 `<slot>` 插口，否则父组件的内容将会被 **丢弃**。当子组件模板只有一个没有属性的 slot 时，父组件整个内容片段将插入到 slot 所在的 DOM 位置，并替换掉 slot 标签本身。

最初在 `<slot>` 标签中的任何内容都被视为 **备用内容**。备用内容在子组件的作用域内编译，并且只有在宿主元素为空，且没有要插入的内容时才显示备用内容。

假定 `my-component` 组件有下面模板：
```html
<div>
    <h2>我是子组件的标题</h2>
    <slot>
        只有在没有要分发的内容时才会显示。
    </slot>
</div>
```

父组件模版：
```html
<div>
    <h1>我是父组件的标题</h1>
    <my-component>
        <p>这是一些初始内容</p>
        <p>这是更多的初始内容</p>
    </my-component>
</div>
```

渲染结果：
```html
<div>
    <h1>我是父组件的标题</h1>
    <div>
        <h2>我是子组件的标题</h2>
        <p>这是一些初始内容</p>
        <p>这是更多的初始内容</p>
    </div>
</div>
```


## 具名 Slot
`<slot>` 元素可以用一个特殊的属性 name 来配置如何分发内容。多个 slot 可以有不同的名字。具名 slot 将匹配内容片段中有对应 slot 特性的元素。

仍然可以有一个匿名 slot ，它是默认 slot ，作为找不到匹配的内容片段的备用插槽。如果没有默认的 slot ，这些找不到匹配的内容片段将被抛弃。

例如，假定我们有一个 app-layout 组件，它的模板为：
```html
<div class="container">
    <header>
        <slot name="header"></slot>
    </header>
    <main>
        <slot></slot>
    </main>
    <footer>
        <slot name="footer"></slot>
    </footer>
</div>
```

父组件模版：
```html
<app-layout>
    <h1 slot="header">这里可能是一个页面标题</h1>
    <p>主要内容的一个段落。</p>
    <p>另一个主要段落。</p>
    <p slot="footer">这里有一些联系信息</p>
</app-layout>
```

渲染结果为：
```html
<div class="container">
    <header>
        <h1>这里可能是一个页面标题</h1>
    </header>
    <main>
        <p>主要内容的一个段落。</p>
        <p>另一个主要段落。</p>
    </main>
    <footer>
        <p>这里有一些联系信息</p>
    </footer>
</div>
```

在组合组件时，内容分发 API 是非常有用的机制。


## 作用域插槽
作用域插槽是一种特殊类型的插槽，用作使用一个（能够传递数据到）可重用模板替换已渲染元素。

在子组件中，只需将数据传递到插槽，就像你将 prop 传递给组件一样：
```html
<div class="child">
    <slot text="hello from child"></slot>
</div>
```

在父级中，具有特殊属性 scope 的 `<template>` 元素，表示它是作用域插槽的模板。scope 的值对应一个临时变量名，此变量接收从子组件中传递的 prop 对象：
```html
<div class="parent">
    <child>
        <template scope="props">
            <span>hello from parent</span>
            <span>{{ props.text }}</span>
        </template>
    </child>
</div>
```

如果我们渲染以上结果，得到的输出会是：
```html
<div class="parent">
    <div class="child">
        <span>hello from parent</span>
        <span>hello from child</span>
    </div>
</div>
```

作用域插槽更具代表性的用例是列表组件，允许组件自定义应该如何渲染列表每一项：
```html
<my-awesome-list :items="items">
    <!-- 作用域插槽也可以在这里命名 -->
    <template slot="item" scope="props">
        <li class="my-fancy-item">{{ props.text }}</li>
    </template>
</my-awesome-list>
```

列表组件的模板：
```html
<ul>
    <slot name="item" v-for="item in items" :text="item.text">
        <!-- fallback content here -->
    </slot>
</ul>
```