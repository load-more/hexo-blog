---
title: 22 Vue 组件
date: 2021-03-04 18:42:23
tags: [Vuejs, Component, Slot, Mixin]
categories: Vuejs
---

## 注册组件

### 1.1注册全局组件

`Vue.component(tagName, options)`

- tagName
  - 组件名称
  - W3C规范：字母全部小写并包含一个连字符 "-"
- options
  - 可以是 `Vue.extend()` 创建的一个组件构造器
  - 也可以是组件的选项对象

> 全局组件需要在创建的根实例之前注册，这样才能使组件在实例中调用

**使用组件构造器**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-component></my-component>
  </div>
  <script src="../vue.js"></script>
  <script>
    // 创建组件构造器
    let myCpn = Vue.extend({
      template: '<h1>注册全局组件</h1>'
    })
    // 注册全局组件
    Vue.component('my-component', myCpn)
    // 创建根实例
    let vm = new Vue({
      el: '#app'
    })
  </script>
</body>
</html>
```

**使用组件的选项对象**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-component></my-component>
  </div>
  <script src="../vue.js"></script>
  <script>
    // 注册全局组件
    Vue.component('my-component', {
      template: '<h1>注册全局组件</h1>'
    })
    // 创建根实例
    let vm = new Vue({
      el: '#app'
    })
  </script>
</body>
</html>
```

组件的模板只能有一个根元素。如果模板内容有多个元素，可以将模板的内容包含在一个父元素内。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-component></my-component>
  </div>
  <script src="../vue.js"></script>
  <script>
    // 注册全局组件
    Vue.component('my-component', {
      template: `
      <div>
        <h1>注册全局组件</h1>
        <h2>这是内容1</h2>  
        <h2>这是内容2</h2>  
        <h2>这是内容3</h2>  
      </div>
      `
    })
    // 创建根实例
    let vm = new Vue({
      el: '#app'
    })
  </script>
</body>
</html>
```

**注意，组件选项对象中的data和Vue实例选项对象中的data赋值是不同的。一个组件的data选项必须是一个函数，而不是一个对象。这样的好处是每个实例都可以维护一份被返回对象的独立复制。简单来说，函数有块级作用域，每个组件的data不会共用一块地址。**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-component></my-component>
    <my-component></my-component>
    <my-component></my-component>
  </div>
  <script src="../vue.js"></script>
  <script>
    // 注册全局组件
    Vue.component('my-component', {
      template: `
      <div>
        <button @click='count++'>点击了{{count}}次</button>
      </div>
      `,
      data() {
        return {
          count: 0
        }
      }
    })
    // 创建根实例
    let vm = new Vue({
      el: '#app'
    })
  </script>
</body>
</html>
```



### 1.2注册局部组件

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-component></my-component>
    <my-component></my-component>
    <my-component></my-component>
  </div>
  <script src="../vue.js"></script>
  <script>
    let Child = {
      template: `
      <div>
        <button @click='count++'>点击了{{count}}次</button>
      </div>
      `,
      data() {
        return {
          count: 0
        }
      }
    }
    // 创建根实例
    let vm = new Vue({
      el: '#app',
      components: {
        // 注册局部组件
        'my-component': Child
      }
    })
  </script>
</body>
</html>
```

局部注册的组件只能在其父组件中使用，而无法在其他组件中使用。



## 数据传递

### 2.1父传子（props）

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-cpn message='这是消息' title='这是标题'></my-cpn>
  </div>
  <script src="../vue.js"></script>
  <script>
    let child = {
      template: `
      <div>
        <h2>{{title}}</h2>  
        <p>{{message}}</p>  
      </div>
      `,
      props: ['title', 'message'] // 接收父组件的静态数据
    }

    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': child
      }
    })
  </script>
</body>
</html>
```

#### 2.1.1props的大小写

由于HTML中的属性是不区分大小写的，所以浏览器会把所有大写字符解释成小写字符。

```javascript
  <div id="app">
    <my-cpn cMessage='这是消息' cTitle='这是标题'></my-cpn> 
  </div>
  <script src="../vue.js"></script>
  <script>
    let child = {
      template: `
      <div>
        <h2>{{ctitle}}</h2>  
        <p>{{cmessage}}</p>  
      </div>
      `,
      // props: ['cTitle', 'cMessage']    HTML属性的大写会转为小写，所以不能有大写字母
      props: ['ctitle', 'cmessage']
    }
```

如果 props 中的变量名是**驼峰式命名**，则调用组件的标签中使用**连字符分隔的方式命名属性**

```javascript
<div id="app">
    <my-cpn c-message='这是消息' c-title='这是标题'></my-cpn> <!-- 短横线分隔 -->
  </div>
  <script src="../vue.js"></script>
  <script>
    let child = {
      template: `
      <div>
        <h2>{{cTitle}}</h2>  
        <p>{{cMessage}}</p>  
      </div>
      `,
      props: ['cTitle', 'cMessage'] // 驼峰式命名
    }
```

#### 2.1.2动态传递props

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <!-- 如果不动态绑定属性，那么传入的会被当成一个字符串，而不是data中的数据 -->
    <my-cpn :c-message='myMsg' :c-title='myTitle'></my-cpn> <!-- 父传子 -->
  </div>
  <script src="../vue.js"></script>
  <script>
    let child = {
      template: `
      <div>
        <h2>{{cTitle}}</h2>  
        <p>{{cMessage}}</p>  
      </div>
      `,
      props: ['cTitle', 'cMessage'] // 接收父组件的数据
    }

    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': child
      },
      data() {
        return {
          myTitle: '这是标题',  // 父组件的数据
          myMsg: 'Hello World!'
        }
      }
    })
  </script>
</body>
</html>
```

> 如果props传递的是一个对象或是数组，那么它是按引用传递的。在组件内修改这个对象或数组本身将会影响父组件的状态

在传递对象类型的数据时，如果要将一个对象的所有属性都作为 props 传入，可用使用不带参数的 v-bind

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-cpn v-bind='info'></my-cpn> <!-- 父传子 -->
  </div>
  <script src="../vue.js"></script>
  <script>
    let child = {
      template: `
      <div>
        <p>{{name}}</p>  
        <p>{{gender}}</p>  
        <p>{{age}}</p>  
      </div>
      `,
      props: ['name', 'gender', 'age'] // 接收父组件的数据
    }

    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': child
      },
      data() {
        return {
          info: {
            name: 'Tony',
            gender: 'male',
            age: 22
          }
        }
      }
    })
  </script>
</body>
</html>
```

#### 2.1.3props 验证

组件可以为 Prop 指定验证要求。使用验证时，props 接收的是一个对象，而不是一个字符串数组。

- 基础类型检测

  - 允许参数为指定的一种类型

    ```javascript
    props: {
        propA: String
    }
    ```

  - 参数类型有：String、Number、Boolean、Array、Object、Date、Function、Symbol，也可以为 null 和 undefined，表示任意类型均可

- 多种类型

  - 允许参数为多种类型之一

    ```javascript
    props: {
        propA: [String, Number, Array]
    }
    ```

- 参数必须

  - 参数必须有值且为指定类型

    ```javascript
    props: {
        propA: {
            type: String,
            required: true
        }
    }
    ```

- 参数默认

  - 参数具有默认值

    ```javascript
    props: {
        propA: {
            type: Boolean,
            default: true
        }
    }
    ```

  - 注意，如果参数类型为数组或对象，则其默认值需要通过函数返回值的形式赋值

    ```javascript
    props: {
        propA: {
            type: Array,
            default() {
                return []
            }
        },
        propB: {
            type: Object,
            default() {
                return {}
            }
        }
    }
    ```

- 自定义验证函数

  - 根据验证函数验证参数的值是否符合要求

    ```javascript
    props: {
        propA: {
            validator(value) {
                return value > 0	// propA必须大于0
            }
        }
    }
    ```

在开发环境中，如果 Prop 验证失败， Vue 将产生一个控制台的警告

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-cpn :id='id' :age='age'></my-cpn> <!-- 父传子 -->
  </div>
  <script src="../vue.js"></script>
  <script>
    let child = {
      template: `
      <div>
        <p>{{id}}</p>  
        <p>{{hobbies}}</p>  
      </div>
      `,
      props: {
        id: {
          type: Number,
          required: true
        },
        hobbies: {
          type: [Object, Array],
          default() {
            return []
          }
        },
        age: {
          type: Number,
          validator(val) {
            return val > 18   // 年龄必须大于18，否则报错
          }
        }
      }
    }

    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': child
      },
      data() {
        return {
          id: 123,
          hobbies: {
            name: 'Tom',
            age: 11
          },
          age: 115
        }
      }
    })
  </script>
</body>
</html>
```

### 2.2子传父（自定义事件）

#### 2.2.1自定义事件的监听和触发

父组件可以像处理原生DOM事件一样通过 v-on 监听子组件实例的自定义事件，而子组件可以通过调用内建的 $emit() 方法并传入事件名称来触发自定义事件

`vm.$emit(eventName, [...args])`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app" :style='{fontSize: fontSize + "px"}'>
    海内存知己，天涯若比邻。
    <my-cpn @enlarge='enlarge'></my-cpn> <!-- 监听自定义事件 -->
  </div>
  <script src="../vue.js"></script>
  <script>
    let child = {
      template: `
      <div>
        <button @click='btnClick'>放大</button>
      </div>
      `,
      methods: {
        btnClick() {
          this.$emit('enlarge')   // 触发自定义事件
        }
      }
    }

    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': child
      },
      data () {
        return {
          fontSize: 16  
        }
      },
      methods: {
        enlarge() {
          this.fontSize += 2
        }
      }
    })
  </script>
</body>
</html>
```

#### 2.2.2组件绑定原生事件

如果想在某个组件的根元素上监听一个原生事件，可以使用 v-on 的 .native 修饰符。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app" >
    <!-- <my-cpn @click='btnClick'></my-cpn>   如果不加 .native ,无法触发响应函数 -->
    <my-cpn @click.native='btnClick'></my-cpn> <!-- 给组件根元素绑定原生事件 -->
  </div>
  <script src="../vue.js"></script>
  <script>
    let child = {
      template: `
      <div>
        <h2>Click me</h2>
        <button>Click me</button>
      </div>
      `
    }

    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': child
      },
      data () {
        return {
          fontSize: 16  
        }
      },
      methods: {
        btnClick() {
          alert('Hello World')
        }
      }
    })
  </script>
</body>
</html>
```



## 插槽

### 3.1基本用法

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-cpn>
      <h3>这也是模板22</h3>	<!-- 这里的内容会替代 slot -->
      <h4>这也是模板33</h4>  <!-- 这里的内容会替代 slot -->
    </my-cpn>
  </div>
  <script src="../vue.js"></script>
  <script>
    let cpn = {
      template: `
      <div>
        <h2>这是模板</h2>
        <slot></slot>   <!-- 如果没有slot，my-cpn标签之间的所有内容都不会传递过来 -->
      </div>
      `
    }
    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': cpn
      }
    })
  </script>
</body>
</html>
```

### 3.2编译作用域

父组件和子组件有各自的编译作用域，两者之间的数据是不能混用的。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-cpn>
      {{message}}	// 由于父组件没有设置message，所以不会有结果
    </my-cpn>
  </div>
  <script src="../vue.js"></script>
  <script>
    let cpn = {
      template: `
      <div>
        <slot></slot>   <!-- 如果没有slot，my-cpn标签之间的所有内容都不会传递过来 -->
      </div>
      `,
      data () {
        return {
          message: 'Hello world' // 这是子组件的message，只有子组件能调用
        }
      }
    }
    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': cpn
      }
    })
  </script>
</body>
</html>
```

### 3.3后备内容

有些时候需要为一个插槽设置具体的后备内容（即默认内容），当没有提供内容时，会显示插槽的后备内容；提供了内容时，会覆盖后备内容。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-cpn>

    </my-cpn>
  </div>
  <script src="../vue.js"></script>
  <script>
    let cpn = {
      template: `
      <div>
        <slot>
          <h4>这是后备内容</h4>  
        </slot>
      </div>
      `,
      data () {
        return {
          message: 'Hello world'
        }
      }
    }
    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': cpn
      }
    })
  </script>
</body>
</html>
```

### 3.4具名插槽

有些时候需要在组件模板上使用多个插槽，这种情况下需要用到 <slot> 元素的 name 属性。

通过这个属性定义的插槽叫做“具名插槽”。在向具名插槽提供内容的时候，可以在一个 <template> 元素上使用 v-slot 指令，并将插槽的名称作为 v-slot 指令的参数。这样， <template> 元素中所有内容都会传入响应的插槽。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <my-cpn>
      <!-- <template>            没有带v-slot，将视为默认插槽的内容 -->
      <template v-slot:default>    <!-- 当然也可以加上隐式name的属性值default -->
        <p>这是默认插槽</p>
      </template>

      <template v-slot:title>
        <h2>这是标题</h2>
      </template>
      <template v-slot:content>
        <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Reprehenderit cupiditate unde maiores ratione eos sit suscipit doloribus quo quas architecto nisi fuga odit enim beatae, optio tempore mollitia placeat nam.</p>
      </template>
      <template v-slot:footer>
        <h4>这是结尾</h4>
      </template>

      <template slot="test">  <!-- 也可以写成这样的形式，这是以前的写法 -->
        <p>这是旧用法</p>
      </template>
    </my-cpn>
  </div>
  <script src="../vue.js"></script>
  <script>
    let cpn = {
      template: `
      <div>
        <slot name='title'></slot>
        <slot name='content'></slot>
        <slot name='footer'></slot>
        <slot></slot>  <!-- 默认插槽 -->
        <slot name='test'></slot>
      </div>
      `,
      data () {
        return {
          message: 'Hello world'
        }
      }
    }
    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': cpn
      }
    })
  </script>
</body>
</html>
```

### 3.5作用域插槽

有时候需要让插槽内容访问子组件中的数据，为了让子组件的数据能在父级的插槽内容中可用，可将子组件的数据作为一个<slot>元素的属性并对其进行绑定。

绑定在<slot>元素上的属性称为**插槽Prop**，然后在父级作用域中，可以为v-slot设置一个包含所有插槽Prop对象的名称

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app" >
    <my-cpn v-slot:title='slotProps'>  <!-- 接收插槽里所有的动态参数，封装成一个slotProps对象 -->
      标题：{{slotProps.title}}
      消息：{{slotProps.msg}}
    </my-cpn>
  </div>
  <script src="../vue.js"></script>
  <script>
    let cpn = {
      template: `
    <div>
      <slot name='title' :msg='message' :title='title'></slot> <!-- 将子组件的数据动态绑定到msg和title中 -->
    </div>
    `,
      data() {
        return {
          message: '这是子组件中的数据',
          title: '这是子组件中的标题'
        }
      }
    }
    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': cpn
      }
    })
  </script>
</body>
</html>
```



## 混入

### 4.1基本用法

**混入**是一种为组件提供可复用功能的非常灵活的方式。

混入对象可用包含任意的组件选项。

当组件使用混入对象时，混入对象中的所有选项将被混入该组件本身的选项中。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <div id="app">
    <my-cpn></my-cpn>
  </div>
  <script src="../vue.js"></script>
  <script>
    let myMixin = {   // 混入对象，这里的所有选项都会并入cpn组件中
      data() {
        return {
          title: '这是混入对象的title'
        }
      },
      methods: {
        show() {
          document.write('这是混入对象的show()')
        }
      }
    }

    let cpn = {
      mixins: [myMixin],
      template: `
      <div>
        <h2>{{title}}</h2>    <!-- 调用混入对象的属性 -->
      </div>
      `,
      created() {
        this.show()   // 调用混入对象的方法
      }
    }



    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': cpn
      }
    })
  </script>
</body>

</html>
```

### 4.2选项合并

当组件和混入对象包含同名项时，这些选项会以适当的方式进行合并。

- 数据对象在内部会进行递归合并，如果混入对象的数据和组件的数据发生冲突，**以组件数据优先**。

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>
  
  <body>
    <div id="app">
      <my-cpn></my-cpn>
    </div>
    <script src="../vue.js"></script>
    <script>
      let myMixin = {   // 混入对象，这里的所有选项都会并入cpn组件中
        data() {
          return {
            title: '这是混入对象的title',   // 和组件里的title冲突，以组件title为准
            message: '这是混入对象的message'  // 组件没有message，并入组件里
          }
        }
      }
  
      let cpn = {
        mixins: [myMixin],
        template: `
        <div>
          <h2>{{title}}</h2>   
          <p>{{message}}</p> 
        </div>
        `,
        data() {
          return {
            title: '这是组件的title'
          }
        }
      }
  
  
  
      let vm = new Vue({
        el: '#app',
        components: {
          'my-cpn': cpn
        }
      })
    </script>
  </body>
  
  </html>
  ```

- 同名钩子函数将混合成一个数组，因此都会被调用。注意，混入对象的钩子函数会先调用

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>
  
  <body>
    <div id="app">
      <my-cpn></my-cpn>
    </div>
    <script src="../vue.js"></script>
    <script>
      let myMixin = {   // 混入对象，这里的所有选项都会并入cpn组件中
        data() {
          return {
            title: '这是混入对象的title',   // 和组件里的title冲突，以组件title为准
            message: '这是混入对象的message'  // 组件没有message，并入组件里
          }
        },
        created () {
          document.write('调用了混入对象的钩子函数' + '<br>')	// 先被调用
        }
      }
  
      let cpn = {
        mixins: [myMixin],
        template: `
        <div>
          <h2>{{title}}</h2>   
          <p>{{message}}</p> 
        </div>
        `,
        data() {
          return {
            title: '这是组件的title'
          }
        },
        created () {
          document.write('调用了组件的钩子函数') // 后被调用
        }
      }
  
  
  
      let vm = new Vue({
        el: '#app',
        components: {
          'my-cpn': cpn
        }
      })
    </script>
  </body>
  
  </html>
  ```

- 值为对象的选项，如：methods、components、directives，将被合并为同一个对象。**如果两个对象的键名冲突，则取组件对象的键值对**

  ```html
  <!DOCTYPE html>
  <html lang="en">
  
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>
  
  <body>
    <div id="app">
      <my-cpn></my-cpn>
    </div>
    <script src="../vue.js"></script>
    <script>
      let myMixin = {   // 混入对象，这里的所有选项都会并入cpn组件中
        data() {
          return {
            title: '这是混入对象的title',   // 和组件里的title冲突，以组件title为准
            message: '这是混入对象的message'  // 组件没有message，并入组件里
          }
        },
        methods: {
          show() {
            document.write('混入对象里的show()<br>')
          },
          showMsg() {
            document.write('混入对象里的showMsg()<br>')
          }
        }
      }
  
      let cpn = {
        mixins: [myMixin],
        template: `
        <div>
          <h2>{{title}}</h2>   
          <p>{{message}}</p> 
        </div>
        `,
        data() {
          return {
            title: '这是组件的title'
          }
        },
        methods: {
          show() {
            document.write('组件里的show()<br>')
          }
        },
        created () {
          this.show()
          this.showMsg()
        }
      }
  
  
  
      let vm = new Vue({
        el: '#app',
        components: {
          'my-cpn': cpn
        }
      })
    </script>
  </body>
  
  </html>
  ```

### 4.3全局混入

混入对象也可以全局注册，但要谨慎使用。

一旦使用全局混入对象，它将会影响到所有之后创建的Vue实例。

如果使用恰当，就可以自定义选项注入处理逻辑。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <div id="app">
    <my-cpn></my-cpn>
  </div>
  <script src="../vue.js"></script>
  <script>
    // 注册一个全局混入对象
    Vue.mixin({
      created () {
        let myOption = this.$options.myOption
        if (myOption) {
          document.write(myOption)
        }
      }
    })
    // 在组件里自定义一个选项
    let cpn = {
      myOption: '这是自定义选项'
    }
    // 创建根实例
    let vm = new Vue({
      el: '#app',
      components: {
        'my-cpn': cpn
      }
    })
  </script>
</body>

</html>
```

> 使用全局混入对象一定要谨慎，因为它会影响到每个创建的 Vue 实例。
>
> 在大多数情况下，全局混入只应用于自定义选项。

## 动态组件

### 5.1基本用法

根据条件在不同组件间进行动态切换。

通过使用 Vue.js 中的 <component> 元素，动态绑定到它的 is 属性，根据 is 属性的值判断使用哪个组件。

动态组件经常应用在路由控制或者选项卡切换中。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <div id="app">
    <button @click='current="music"'>音乐</button>
    <button @click='current="movies"'>电影</button>
    <button @click='current="sports"'>运动</button>
    <component :is="current"></component>
  </div>

  <script src="../vue.js"></script>
  
  <script>
    // 创建根实例
    let vm = new Vue({
      el: '#app',
      data () {
        return {
          current: 'music'  
        }
      },
      components: {
        music: {
          template: `
          <div>
            <h2>这是“音乐”组件</h2>
          </div>
          `
        },
        movies: {
          template: `
          <div>
            <h2>这是“电影”组件</h2>
          </div>
          `
        },
        sports: {
          template: `
          <div>
            <h2>这是“运动”组件</h2>
          </div>
          `
        }
      }
    })
  </script>
</body>

</html>
```

### 5.2keep-alive

在多个组件之间进行切换的时候，有时需要保持这些组件的状态，将切换后的状态保留在内存里，以避免重复渲染。

可用用一个 <keep-alive> 元素将动态组件包裹起来。

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <div id="app">
    <button @click='current="music"'>音乐</button>
    <button @click='current="movies"'>电影</button>
    <button @click='current="sports"'>运动</button>
    <keep-alive include="">
      <component :is="current"></component>
    </keep-alive>
  </div>

  <script src="../vue.js"></script>
  
  <script>
    // 创建根实例
    let vm = new Vue({
      el: '#app',
      data () {
        return {
          current: 'music'  
        }
      },
      components: {
        music: {
          template: `
          <div>
            <h2>这是“音乐”组件</h2>
            <div>
              <button @click='cMusic="classical"'>古典音乐</button>
              <button @click='cMusic="popular"'>流行音乐</button>
              <button @click='cMusic="nation"'>民族音乐</button>
              <component :is="cMusic"></component>
            </div>
          </div>
          `,
          data() {
            return {
              cMusic: 'classical'
            }
          },
          components: {
            classical: {
              template: `<div><h3>古典音乐</h3></div>`
            },
            popular: {
              template: `<div><h3>流行音乐</h3></div>`
            },
            nation: {
              template: `<div><h3>民族音乐</h3></div>`
            }
          }
        },
        movies: {
          template: `
          <div>
            <h2>这是“电影”组件</h2>
          </div>
          `
        },
        sports: {
          template: `
          <div>
            <h2>这是“运动”组件</h2>
          </div>
          `
        }
      }
    })
  </script>
</body>

</html>
```

