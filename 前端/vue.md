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

## 属性计算

相比方法带有缓存
相比监控节省开支  
默认只有`get`,可以设置set

```html
<div id="get">
    <p>{{message}}</p>
    <p>{{nowTimeMessage}}</p>
</div>
```

```js
new Vue({
    el:"#get",
    data:{
        message: "测试"
    },
    computed:{
        nowTimeMessage:function () {
            return    this.message+new Date().getTime()
        }
    }
})
```

- set的例子

```html
<div id="get">
    <p>{{message}}</p>
    <p>{{nowTimeMessage}}</p>
    <button @click="setMessage">测试set</button>
</div>
```

```js
new Vue({
    el:"#get",
    data:{
        message: "测试"
    },
    computed:{
        nowTimeMessage:{
            get:function () {
                return    this.message+new Date().getTime()
            },
            set:function (newValue) {
                this.message=newValue;
            }
        }
    },
    methods:{
        setMessage:function () {
            this.nowTimeMessage="测试SET"
        }
    }
})
```

## 属性监听

```html
<div id="model_test">
    <p>{{message}}</p>
    <input v-model="test" />
</div>
```

```js
new Vue({
    el:"#model_test",
    data:{
        message:"测试",
        test:""
    },
    watch:{
        test:function (newValue, oldValue) {
            this.message="变化中.........."
            console.log("变化中")
            setTimeout(()=>{
                this.message="测试"
            },2000)
        }
    }
})
```

## class与Style

```css
.cssTest{
    color: #cccccc;
}
```

```html
<div id="classBind2">
    <p :class="{ cssTest: isActive }">{{message}}</p>
    <button  @click="btn">取反</button>
</div>
```

```js
new Vue({
    el:"#classBind2",
    data:{
        message:"测试文本",
        isActive:false
    },
    //钩子
    created:function(){
        console.log("初始化");
    },
    methods:{
        btn:function () {
            console.log(this.isActive)
            this.isActive=!this.isActive
        }
    }
})
```

> 其他语法见官网

## v-if

会复用已经渲染的在组件,指定`key`属性可以重新渲染  
`v-show`必定会渲染,只是样式上的隐藏

```html
<div id="if_test">
    <template v-if="test==1">
        用户名 <input type="text" placeholder="用户名">
    </template>
    <template v-else-if="test==2">
        邮箱 <input type="text" placeholder="邮箱">
    </template>
    <template v-else>
        不知道 <input type="text" placeholder="不知道" key="重新渲染">
    </template>
    <button @click="qh">切换</button>
</div>

```

```js
new Vue({
    el:"#if_test",
    data:{
        test:true,
    },
    methods:{
        qh:function () {
            if (this.test < 3) {
                this.test++;
            }else {
                this.test=1;
            }
        }
    }
})
```
