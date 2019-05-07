# vue

```html
<p id="hw" :title="date" v-if="show">
    {{ message }}
</p>
```

```js
let app = new Vue({
    el:"#hw",
    data:{
        message: "测试",
        date: new Date().toLocaleString(),
        show:false
    }
});
```

1. `:title`是 `v-bind:title`的缩写,用于绑定标签属性
2. `v_if`用于查看标签是是否展示


```html
<div id="for">
    <ol>
        <li v-for="li in lis" @click="add">
            {{ li.text }}
        </li>
    </ol>
</div>
```

```js
let for = new Vue({
    el: "#for",
    data: {
        lis: [
            {text:"测试",show:true},
            {text:"测试1",show:false},
        ]
    },
    methods:{
        add:function () {
            this.lis.push({text:"牛逼",show:false});
        }
    }
});
```

1. `@click`是 `v-on:click`的缩写,用于绑定事件
2. `v-for`遍历莫个元素

> vue中不要使用`()=>{}`会改变`this`上下文

## 组件

```js
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

```html
<div id="cc">
    <ol>
        <todo-item :item="item"></todo-item>
    </ol>
</div>
```

```js
new Vue({
    el:"#cc",
    data:{
        item:{text:"cccd"}
    }
})
```