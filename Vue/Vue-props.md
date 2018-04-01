# Vue 组件的通信

## 组件定义
```
// 全局定义
Vue.component('my-lists', {
    // options
})
```

定义后，可以在`template`模版中使用`<my-list></mylist>`，此时会将**组件**中定义的内容显示出来。
```
<template>
    <div>
        <my-list></mylist>
    </div>
</template>
```

通过例子来看一下
```
<body>
    <div id='app'>
        <my-list></my-list>
    </div>
    
</body>
<script>
    Vue.component('my-list', {
        template: `
            <ul>
                <li>上海</li>
                <li>北京</li>
                <li>南京</li>
            </ul>
        `
    })
    new Vue({
        el: '#app'
    })

</script>
```

页面会显示出我们定义的`my-list`的组件内容，` `` `这个符号是ES6中的模版字符串，想详细了解的话，可以看[MDN上关于模版字符串的说明](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/template_strings)。


如果不需要全局定义的话，那可以在Vue实例中局部定义
```
var myList = {
    template: `
        <ul>
            <li>上海</li>
            <li>北京</li>
            <li>南京</li>
        </ul>
    `
}
new Vue({
    el: '#app', // 挂载
    components: {
        'my-list': myList
    }
})
```

## 组件内的`data`
上面是简单的组件，如果我们想自己通过变量，来控制列表的展示，这个时候，我们组件内部定义`data`。我们来重新写`my-list`组件:

```
var citys = ['上海', '北京', '广州']
Vue.component('my-list', {
    template: `
        <ul>
            <li v-for='(item, index) in citys' :key='index'>{{item}}</li>
        </ul>
    `,
    data () {
        return {
            citys: citys
        }
    }
})
new Vue({
    el: '#app'
})
```

有一点要注意，`data`必须是一个函数，将值通过函数返回。如果是一个变量:
```
var citys = ['上海', '北京', '广州']
Vue.component('my-list', {
    template: `
        <ul>
            <li v-for='(item, index) in citys' :key='index'>{{item}}</li>
        </ul>
    `,
    data: {
        citys: citys
    }
})

```
会导致一个问题，多个相同组件同时使用时，它们的`data`是共享的，会出现相互影响的情况。类似于**浅拷贝**的这种情况

```
var a = {name: 'a'}
var b = a
var c = a

console.log(b.name, c.name) // a a

console.log(b.name, c.name) // a a

b.name = 'b'
console.log(b.name) // b
console.log(c.name) // b

// b是copy a的引用，所以b改变了的同时会影响a
// 组件内的data同理，所以将data的值以函数返回，以创建不同的对象，而不是共享一个

var a = function () {
    return {
        name: 'a'
    }
}

var b = a() // b是新对象
var c = a() // c也是新对象

b.name = 1
console.log(c.name) // a

```

## 父子组件的通信
1. 父组件通过传递`Props`给子组件
2. 子组件获取到父组件传递的值，执行一些列操作后，通过 `emit` 触发事件，告诉父组件发生了什么
3. 父组件可以见通过`v-on`/`@`监听这个事件

以上述的`my-list`为例子，实现当点击城市名称时，会在下方显示点击的城市：
```
<body>
    <div id='app'>
        <my-list 
            :citys='citys'
            @select='getCity'></my-list>

        <p>{{selCity}}</p>
    </div>
    
</body>
<script>
Vue.component('my-list', {
    template: `
        <ul>
            <li 
                v-for='(item, index) in citys' 
                :key='index'
                @click='select(item)'>{{item}}</li>
        </ul>
    `,
    props: ['citys'],
    methods: {
        select (city) {
            this.$emit('select', city)
        }
    }
})
new Vue({
    el: '#app',
    data () {
        return {
            citys: ['上海', '北京', '广州'],
            selCity: ''
        }
    },
    methods: {
        getCity (city) {
            this.selCity = city
        }
    }
})
</script>
```
- `:citys` 动态绑定`props`的用户，`:`是`v-bind的缩写`；如果传递的是一个静态的值，则可以直接写成`citys="['上海'，'北京']"`
- `@select` 父组件监听子组件触发的事件`this.$emit('select', city)`; `@`是`v-on`的缩写；如果子组件触发了`select`，会响应父组件的'getCity'函数

我们需要注意的是，父子组件通信是**单向**的，即父组件传递给子组件的`props`发生变化时，子组件也会接收到这种变化，但是如果子组件改变`props`的值时，不会反馈给父组件，防止修改了父组件的状态，导致整个的数据流难以理解，如果修改了，Vue也会发出警告。
`props`对于子组件来说应该是只读的。有两种情况下，我们会想改变`props`:
- props传递进来后，我们需要对props进行处理，然后输出到页面上，这种情况下，可以通过`computed`来计算处理后的变量
```
    computed: {
        newCitys () {
            this.citys.filter((ele) => {
                return ele !== '上海'
            }) 
        }
    }
```

- 作为一个初始值，后续需要对其进行修改，计算等；这种情况下，可以将`props`赋值给自身的属性
```
data () {
    return {
        initCitys: this.citys
    }
}

```

如果我们想修改`props`的值，并将变化反馈给父组件，除了`$emit`触发事件 + 父组件监听事件这种方式外，还有一种较为简单的方式，通过`sync`操作符
```
// 父组件
<child-component :name.sync='name'></child>

// 子组件想要能够修改name，可以这么做
this.$emit('update:name', 'newVal')

```

上述我们是直接传递`props`，我们可以在子组件中通过定义`props`的**格式**来对`props`进行简单的验证，以下一段验证代码来自[官网](https://cn.vuejs.org/v2/guide/components.html#%E4%BB%80%E4%B9%88%E6%98%AF%E7%BB%84%E4%BB%B6%EF%BC%9F)，允许偷个懒= _=||
```
Vue.component('example', {
  props: {
    // 基础类型检测 (`null` 指允许任何类型)
    propA: Number,
    // 可能是多种类型
    propB: [String, Number],
    // 必传且是字符串
    propC: {
      type: String,
      required: true
    },
    // 数值且有默认值
    propD: {
      type: Number,
      default: 100
    },
    // 数组/对象的默认值应当由一个工厂函数返回
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```



## 多个同级组件间的通信
在复杂项目中，可能会出现多个同级的组件需要通信，而仅仅是简单的父子组件通信，这个时候怎么去做呢？

### 通过新建一个Vue实例，作为事件总线
```
var emitEvent = new Vue()

// 在不同的组件中，通过触发事件，监听事件来实现通信
emitEvent.$emit('event-name', val)
emitEvent.$on('event-name', funciton (val) {

})

```

### 统一状态管理，可以在项目中使用`Vuex`
> [Vuex 官网](https://vuex.vuejs.org/zh-cn/)

`Vuex` 是一个专门为`Vue.js`开发状态管理软件。
Vuex中有几个核心的概念
- State   存储状态，类似于data属性

- Getter 从`state`中派生出一些属性，类似于computed

- Mutation 更改`state`的属性，`state`属性是无法直接通过赋值进行更改的，需要通过`Mutation`定义的函数来进行赋值，提交的是`state`

- Actions `Mutation`必须是**同步**的函数，所以在Actions中处理异步的操作，并且提交的是`Mutation`

- Module 当项目很大的时候，需要根据不同的功能分割成不同的模块，每次需要的时候，只需要引用需要的模块即可


比如一个项目中使用Vuex，首先安装Vuex `npm install vuex --save`，通过脚手架`vue-cli`，一般默认自带`Vuex`

Vuex.store的目录结构可以是这样的
```
store
  - index.js  // 统一模块的index
  - modules
      - a.js // 模块 a
      - b.js // 模块 b

```

store/index.js
```
import Vue from 'vue'
import Vuex from 'vuex'

import a from './modules/a'
import b from './modules/b'

Vue.use(Vuex)

export default new Vuex.Store({
  modules: {
    a,
    b
  }
})

```

store/modules/a.js
```
export default {
  namespaced: true, // 必要的
  state: {
    data: null
  },
  mutations: {
    setData(state, data) {
        state.data = data
    }
  },
  actions: {
      getData (context, cb) {
        // 假设执行的从服务端获取的数据
        http.post(url, {name: 'b'}, function (res) {
            // 提交mutation操作
            context.commit('setData', res)

            // 如果有回调函数的话
            typeof cb === 'funciton' && cb(res)
        })
      }
      
  
  }
}
```

store/modules.b.js 类似于a，在此就不举例子了。

在组件中如何是使用呢？

首先导入Vuex提供的方法
```
// components/a.vue

// 首先，确定需要哪些功能
// 如果只需要state中的数据
import { mapState } from 'vuex'

// 如果只需要state中的数据 和 mutations操作
import { mapState, mapMutations } from 'vuex'

// 如果还对Actions有需求
import { mapState, mapMutations, mapActions } from 'vuex'
// 需要什么导入什么即可

// 小tips： 可以先写 from 'vuex',再写{}中的，编辑器会自动不全
```

然后导入我们在Store中定义的属性和方法
```
# 使用 a 模块，并且把a模块中的data赋值给aData
computed: {
    ...mapState('a', {
        aData: state => state.data
    })
    ...mapGetters('a', {
        xxx: state => state.xxx
    })
}

# 导入方法
methods: {
    ...mapMutations('a', [
      'setData'
    ])

    ...mapActions('a', [
      'getData'
    ])
}
```

使用属性和方法
```
// 属性和方法通过this调用
this.aData

// 重新赋值
this.setData('aaa')

// 传入回调函数
this.getData(function (res) {
    console.log(res)
})
```

上面的代码中我们使用`...` Javascript的扩展操作符和 `aData: state => state.data`的箭头函数，如果对这两项有不了解的，可以查阅链接
> [扩展操作符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_operator)

> [箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

感谢，如果有什么写的不好的地方，请联系作者，比心～


