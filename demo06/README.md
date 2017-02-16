Prop
---


### 使用 Prop 传递数据
组件实例的作用域是 **孤立的**，这意味着不能并且不应该在子组件的模板内直接引用父组件的数据。可以使用 props 把数据传给子组件。

prop 是父组件用来传递数据的一个自定义属性，子组件需要显式地用 `props` 选项声明 “prop”：
```js
Vue.component('child-component', {
    // 声明 props
    props: ['message'],
    // 就像 data 一样，prop 可以用在模板内
    // 同样也可以在 vm 实例中像 “this.message” 这样使用
    template: '<span>{{ message }}</span>'
});
```

然后向它传入一个普通字符串：
```html
<child message="父组件和子组件之间的参数传递!"></child>
```


### camelCase vs. kebab-case
HTML 特性不区分大小写。

当使用非字符串模版时，prop 的名字形式会从 camelCase 转为 kebab-case（短横线隔开）：
```js
Vue.component('child-component', {
    props: ['myMessage'],
    // camelCase in JavaScript
    template: '<span>{{ myMessage }}</span>'
});
```

```html
<!-- kebab-case in HTML -->
<child-component my-message="父组件和子组件之间的参数传递"></child-component>
```

再次说明，如果你使用字符串模版，不用在意这些限制。


### 动态 Prop
类似于用 `v-bind` 绑定 HTML 特性到一个表达式，也可以用 `v-bind` 动态绑定 props 的值到父组件的数据中。每当父组件的数据变化时，该变化也会传导给子组件：
```html
<div id="app">
    <input type="text" v-model="parentMsg" placeholder="Please Enter">
    <br>
    <child-component v-bind:my-message="parentMsg"></child-component>
</div>
```

使用 `v-bind` 的缩写语法通常更简单：
```html
<child-component :my-message="parentMsg"></child-component>
```


### 字面量语法 vs 动态语法
字面量语法：
```html
<!-- 传递了一个字符串"1" -->
<comp some-prop="1"></comp>
```

动态语法：
```html
<!-- 传递实际的数字 -->
<comp v-bind:some-prop="1"></comp>
```

前者是一个字面 prop ，它的值以字符串 "1" 而不是以实际的数字传下去；后者的值被当作 JavaScript 表达式计算。


### 单向数据流
prop 是单向绑定的：当父组件的属性变化时，将传导给子组件，但是不会反过来。这是为了防止子组件无意修改了父组件的状态——这会让应用的数据流难以理解。

另外，每次父组件更新时，子组件的所有 prop 都会更新为最新值。这意味着你不应该在子组件内部改变 prop 。如果你这么做了，Vue 会在控制台给出警告。

通常有两种改变 prop 的情况：

1. prop 作为初始值传入，子组件之后只是将它的初始值作为本地数据的初始值使用；

2. prop 作为需要被转变的原始值传入。

更确切的说这两种情况是：

1.定义一个局部 data 属性，并将 prop 的初始值作为局部数据的初始值。
```js
props: ['initialCounter'],
data: function () {
    return { counter: this.initialCounter }
}
```

2.定义一个 computed 属性，此属性从 prop 的值计算得出。
```js
props: ['size'],
computed: {
    normalizedSize: function () {
        return this.size.trim().toLowerCase()
    }
}
```


### Prop 验证
组件可以为 props 指定验证要求。如果未指定验证要求，Vue 会发出警告。当组件给其他人使用时这很有用。

prop 是一个对象而不是字符串数组时，它包含验证要求：
```js
Vue.component('example', {
    props: {
        // 基础类型检测 （`null` 意思是任何类型都可以）
        propA: Number,
        // 多种类型
        propB: [String, Number],
        // 必传且是字符串
        propC: {
            type: String,
            required: true
        },
        // 数字，有默认值
        propD: {
            type: Number,
            default: 100
        },
        // 数组／对象的默认值应当由一个工厂函数返回
        propE: {
            type: Object,
            default: function() {
                return {
                    message: 'hello'
                }
            }
        },
        // 自定义验证函数
        propF: {
            validator: function(value) {
                return value > 10
            }
        }
    }
});
```

`type` 可以是下面原生构造器：

* String
* Number
* Boolean
* Function
* Object
* Array

`type` 也可以是一个自定义构造器，使用 `instanceof` 检测。

当 prop 验证失败了，如果使用的是开发版本会抛出一条警告。