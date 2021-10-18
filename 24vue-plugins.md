---
title: 24 Vue 插件(vue-router + axios)
date: 2021-03-05 15:55:17
tags: [Vuejs, VueRouter, Axios]
categories: Vuejs
---

## vue-router路由

### 1.1基本用法

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
    <p>
      <!-- 使用<router-link>组件进行导航 -->
      <!-- 通过传入to属性指定链接 -->
      <!-- <router-link>默认渲染成一个a标签 -->
      <router-link to="/a">a</router-link>
      <router-link to="/b">b</router-link>
      <router-link to="/c">c</router-link>
    </p>
    <!-- 路由出口，路由匹配到的组件渲染到这里 -->
    <router-view></router-view>
  </div>

  <script src="../vue.js"></script>
  <script src="../vue-router.js"></script>

  <script>
    // 定义路由组件，可以从其他文件导入
    var a = {
      template: `<div>页面A</div>`
    }
    let b = {
      template: `<div>页面B</div>`
    }
    let c = {
      template: `<div>页面C</div>`
    }

    // 定义路由，每个路由映射一个组件
    let routes = [
      {path:'/a', component: a},
      {path:'/b', component: b},
      {path:'/c', component: c}
    ]

    // 创建router实例，传入配置参数
    let router = new VueRouter({
      routes  // 相当于 routes：routes 简写
    })

    // 注入路由
    let vm = new Vue({
      el: '#app',
      router
    })
  </script>
</body>
</html>
```

### 1.2路由动态匹配

实际开发中，经常需要将某种模式匹配到的所有路由全部映射到同一个组件。

例如，对于所有不同 ID 的用户，都需要使用同一个组件 User 来渲染

|             模式              |      匹配路径       |           $route.params            |
| :---------------------------: | :-----------------: | :--------------------------------: |
|        /user/:username        |     /user/test      |         {username: 'test'}         |
| /user/:username/post/:post_id | /user/test/post/123 | {username: 'test', post_id: '123'} |

*除了 $route.params 以外， $route 对象还提供了其他有用的信息，如： $route.query （如果 URL 中有查询参数）、$route.hash 等*

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
    <p>
      <!-- 使用<router-link>组件进行导航 -->
      <!-- 通过传入to属性指定链接 -->
      <!-- <router-link>默认渲染成一个a标签 -->
      <router-link to="/a">页面a</router-link>
      <router-link to="/b">页面b</router-link>
      <router-link to="/c">页面c</router-link>
      <router-link to="/user/1">用户1</router-link>
    </p>
    <!-- 路由出口，路由匹配到的组件渲染到这里 -->
    <router-view></router-view>
  </div>

  <script src="../vue.js"></script>
  <script src="../vue-router.js"></script>

  <script>
    // 定义路由组件，可以从其他文件导入
    var a = {
      template: `<div>页面A</div>`
    }
    let b = {
      template: `<div>页面B</div>`
    }
    let c = {
      template: `<div>页面C</div>`
    }
    let user = {
      // 参数可用通过 this.$route.params 获取
      template: `<div>用户ID：{{$route.params.id}}</div>`
    }
    let info = {
      template: `<div>所有 info- 开头的路径</div>`
    }

    // 定义路由，每个路由映射一个组件
    let routes = [
      {path:'/a', component: a},
      {path:'/b', component: b},
      {path:'/c', component: c},
      {path:'/user/:id', component: user},
      {path:'/info-*', component: info},  // 使用通配符*，匹配所有 info- 开头的路径
    ]

    // 创建router实例，传入配置参数
    let router = new VueRouter({
      routes  // 相当于 routes：routes 简写
    })

    // 注入路由
    let vm = new Vue({
      el: '#app',
      router
    })
  </script>
</body>
</html>
```

### 1.3嵌套路由

有些界面通常是由多层嵌套的组件组合而成，例如，二级导航菜单。

这是需要使用嵌套路由。

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
    <router-link to="/music">音乐</router-link>
    <router-link to="/movies">电影</router-link> 
    <router-view></router-view>   <!-- 最顶层的出口 -->
  </div>


  <script src="../vue.js"></script>       <!-- 注意要先导入vue.js后导入vue-router.js，否则会报错 -->
  <script src="../vue-router.js"></script>

  <script>
    let music = {
      template: `
      <div>
        <ul>
          <router-link to="/music/zjl">周杰伦</router-link>
          <router-link to="/music/xs">许嵩</router-link>
        </ul>
        <router-view></router-view>
      </div>
      `
    }
    let movies = {
      template: `
      <div>
        <ul>
          <router-link to="/movies/china">中国</router-link>
          <router-link to="/movies/usa">美国</router-link>
        </ul>
        <router-view></router-view>
      </div>
      `
    }

    let routes = [
      {path: '', component: music},   // 默认跳转到 music
      
      {
        path: '/music', 
        component: music,
        children: [       // 定义子路由
          {
            path: 'zjl',
            component: {
              template: `<h2>青花瓷。。。</h2>`
            }
          },
          {
            path: 'xs',
            component: {
              template: `<h2>庐州月。。。</h2>`
            }
          }
        ]
      },
      
      {
        path: '/movies',
        component: movies,
        children: [       // 定义子路由
          {
            path: 'china',
            component: {
              template: `<h2>霸王别姬。。。</h2>`
            }
          },
          {
            path: 'usa',
            component: {
              template: `<h2>阿甘正传。。。</h2>`
            }
          }
        ]
      },
    ]

    let router = new VueRouter({
      routes
    })

    let vm = new Vue({
      el: '#app',
      router
    })
  </script>
</body>
</html>
```

### 1.4命名路由

可以为较长的路由设置别名

```javascript
let router = new VueRouter({
    routes: [
        path: '/user/:id',
        name: 'user',
        component: User
    ]
})
```

设置别名之后，要想链接到该路径，可用为 router-link 的 to 属性传入一个对象

`<router-link :to="{name: 'user', params: {id:1}}">用户</router-link>`

或者

`router.push({name: 'user', params: {id: 123}})`

### 1.5编程式导航

#### 1.5.1router.push()

使用 <router-link> 创建 <a> 标签可用定义导航链接。

除此之外，还可以使用 router 的实例方法 push() 实现导航的功能。

在 Vue 实例内部可以通过 $router 访问路由实例，因此通过 this.$router.push 即可实现页面跳转。

该方法的参数可以是一个字符串路径，或者是一个描述地址的对象。

```javascript
// 跳转到字符串表示的路径
this.$router.push('home')

// 跳转到指定路径
this.$router.push({path: 'home'})

// 跳转到指定命名的路由
this.$router.push({name: 'user'})

// 跳转到指定路径并带有查询参数， /home?id=2
this.$router.push({path: 'home', query: {id: '2'}})

// 跳转到指定命名的路由并带有参数， /user/123
this.$router.push({name:'user', params: {userID}})	// userID = 123

// 这里的params不生效， /user
this.$router.push({path: '/user', params: {userID}})

/* 如果提供了path参数，则通过params传递的参数会被忽略。但可以通过query传递参数。*/

// 如果给path提供参数，可以采用如下写法， user/123
this.$router.push({path: `/user/${userID}`}) // userID = 123
```

*从功能上看，点击 <router-link :to='...'> 等同于调用 router.push(...)，都是会向 history 栈中加入一个新的记录，所以当用户点击浏览器的 “后退” 按钮时，会返回到之前的 URL*

#### 1.5.2router.replace()

- 完整用法

  `router.replace(location, onComplete?, onAbort?)`

- router.replace() 和 router.push() 很像，唯一不同的是，它不会向 history 添加记录，而是自动替代当前记录

- <router-link :to='...' replace>  == `router.replace(...)`

#### 1.5.3router.go()

- 调用方法

  `router.go(n)`

- 在 history 记录中前进或后退多少步，类似 `window.history.go(n)`

### 1.6命名视图

*有时一个工程需要同时（同级）展示多个视图，但是页面并不复杂或者不需要重新编写新的页面进行嵌套时，就可以使用命名视图，通过组合不同名称的视图展示相关的页面。*

有些页面分为顶部、左侧导航栏和主内容三个部分，这种情况下需要将每个部分 定义为一个视图。

为了在界面中同时展示多个视图，需要为每个视图（router-view）进行命名，通过名字进行对应组件的渲染。

在界面中可以有多个单独命名的视图，而不是只有一个单独的出口。

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
    <router-link to="/home">home</router-link>
    <router-view  name="left"></router-view>
    <router-view name="center"></router-view>
    <router-view name="right"></router-view>
  </div>

  <script src="../vue.js"></script>
  <script src="../vue-router.js"></script>

  <script>
    let Left = {
      template: `<div>Left</div>`
    }
    let Center = {
      template: `<div>Center</div>`
    }
    let Right = {
      template: `<div>Right</div>`
    }

    let routes = [
      {
        path: '/home',
        components: {   // 注意，components必须是复数，因为有多个组件
          left: Left,
          center: Center,
          right: Right
        }
      }
    ]

    let router = new VueRouter({
      routes
    })

    let vm = new Vue({
      el: '#app',
      router
    })
  </script>
</body>
</html>
```

### 1.7重定向和别名

```javascript
let router = new VueRouter({
    routes: [
        {
            // 当访问路径 /a 时，会用 /b 替换 /a
            path: '/a',
            redirect: '/b'
        }
    ]
})
```

重定向的目标也可以是一个命名的路由：

```javascript
let router = new VueRouter({
    routes: [
        {
            path: '/a',
            redirect: {name: 'user'}	// 重定向到名称为user的路由
        }
    ]
})
```

别名：

```javascript
const router = new VueRouter({
    routes: [
        // /a的别名是/b,意味着当用户访问/b时，URL会保持为/b，但是路由匹配为/a，仍然显示/a对应的视图
        {path: '/a', component: A, alias: '/b'}
    ]
})
```

别名的功能让开发者可以自由将 UI 结构映射到任意的 URL 上，而不受限于配置的路由结构

### 1.8路由组件传递数据

当组件a完成某些操作后，要跳转到其他组件，同时需要传递一些参数，那么应该如何传递？

- 使用 $route.params

  - 这种方法简单方便
  - 但是在组件中使用 $route 会使组件和对应的路由高度耦合，传递的参数只能在某些特定的 URL 上使用，限制了其灵活性。

- 通过 props 解耦

  ​	![](https://gitee.com/gainmore/imglib/raw/master/img/20210312114703.png)

- 注意，如果路由包含命名视图，必须分别为每个命名视图添加 props

![](https://gitee.com/gainmore/imglib/raw/master/img/20210312114845.png)

- 这样开发者可以在任何地方使用该组件，使该组件更易于**复用**和**测试**

### 1.9History 模式

- vue-router 默认使用 Hash 模式

  - Hash模式是用URL的Hash值模拟一个完整的URL，所以**当URL改变时，页面不会重新加载**
  - 且这种模拟的 URL 很长、没有固定含义

- History模式

  - 这种模式充分利用 history.pushState 的 API 完成 URL 跳转，所以**URL改变，页面自动跳转**

  - 用法：

    ```javascript
    const router = new VueRouter({
        mode: 'history',
        routes: [...]
    })
    ```


### 1.10导航守卫

- 官网：https://router.vuejs.org/zh/guide/advanced/navigation-guards.html
- 参考：https://zhuanlan.zhihu.com/p/54112006

---

> *正如其名，*`*vue-router*`*提供的导航守卫主要用来通过跳转或取消的方式守卫导航。*
>
> 简单来说，导航守卫就是路由跳转过程中的一些钩子函数，在每一个过程中都有一个函数，这些函数能让你处理一些其他的事情，这就是导航守卫。

#### 1.10.1全局守卫

> 全局守卫：
>
> - 是指路由实例上直接操作的钩子函数。
> - 它的特点是所有路由配置的组件都会触发，即只要触发路由就会触发这些钩子函数。
> - 钩子函数执行顺序：beforeEach、beforeResolve（2.5+）、afterEach。

##### beforeEach

在路由跳转前触发，参数包括to,from,next三个，这个钩子作用主要是用于登录验证，也就是路由还没跳转提前告知，以免跳转了再通知就为时已晚。

```javascript
    let router = new VueRouter({
      routes
    })

    router.beforeEach((to, from, next) => {
      // 路由跳转前触发，一般用于登录验证
      alert('要跳转了哦（beforeEach）')
    })
```

##### beforeResolve

​     作用和beforeEach类似，也是路由跳转前触发，参数也是to,from,next三个，和beforeEach区别官方解释为：

> 区别是在导航被确认之前，**同时在所有组件内守卫和异步路由组件被解析之后**，解析守卫就被调用。

​	 **即在 beforeEach 和 组件内beforeRouteEnter 之后，afterEach之前调用。**

```javascript
    let router = new VueRouter({
      routes
    })
    router.beforeResolve((to, from, next) => {
      alert('beforeResolve')
      next()
    })
```

##### afterEach

在路由跳转完成后触发，参数包括to,from, 没有next,

它发生在beforeEach和beforeResolve之后，beforeRouteEnter（组件内守卫）之前。

#### 1.10.2路由独享守卫

> 路由独享守卫：
>
> - 指在单个路由配置时设置的钩子函数
> - 目前只有一个钩子函数beforeEnter

##### beforeEnter

- 和beforeEach完全相同，如果两个都设置则在beforeEach之后紧随执行，参数to、from、next

```javascript
    let routes = [
      {
        path:'/admin/:admin_id',
        component: admin,
        props: true,
        beforeEnter(to, from, next) {
          alert('admin（beforeEnter）')
        }
      },
    ]
```

#### 1.10.3组件内守卫

> 组件内守卫：
>
> - 指在组件内执行的钩子函数，类似于组件内的生命周期，相当于为配置路由的组件添加的生命周期钩子函数。
> - 钩子函数执行顺序：beforeRouteEnter、beforeRouteUpdate、beforeRouteLeave

```javascript
    let user = {
      template: `<div>用户ID：{{user_id}}</div>`,
      props: ['user_id'],
      beforeRouteEnter(to, from, next) {
      	// 在渲染该组件的对应路由被 confirm 前调用
    	// 不！能！获取组件实例 `this`
    	// 因为当守卫执行前，组件实例还没被创建
      },
      beforeRouteUpdate(to, from, next) {
        // 在当前路由改变，但是该组件被复用时调用
        // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
        // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
        // 可以访问组件实例 `this`
      },
      beforeRouteLeave(to, from) {
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 `this`
      }
    }
```

##### beforeRouteEnter

- 路由进入之前调用，参数包括to，from，next

- 该钩子在全局守卫beforeEach和独享守卫beforeEnter之后，全局beforeResolve和全局afterEach之前调用

- **注意该守卫内访问不到组件的实例，也就是this为undefined，因为它在beforeCreate生命周期前触发**

- `beforeRouteEnter` 守卫 **不能** 访问 `this`，因为守卫在导航确认前被调用，因此即将登场的新组件还没被创建。

  不过，你可以通过传一个回调给 `next`来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

  ```javascript
      let user = {
        template: `<div>用户ID：{{user_id}}</div>`,
        props: ['user_id'],
        beforeRouteEnter(to, from, next) {
          console.log(this.$el);  // undefined
          next(vm => {
            console.log(vm.$el);  // <div>用户ID：{{user_id}}</div>
          })
        }
      }
  ```

- 注意 `beforeRouteEnter` 是支持给 `next` 传递回调的唯一守卫。对于 `beforeRouteUpdate` 和 `beforeRouteLeave` 来说，`this` 已经可用了，所以**不支持**传递回调，因为没有必要了。

  ```javascript
  beforeRouteUpdate (to, from, next) {
    // just use `this`
    this.name = to.params.name
    next()
  }
  ```

- 注意next中函数的调用是在mounted之后，这是为了确保能对组件实例的完整访问

##### beforeRouteUpdate

- 参数包括to，from，next。

- 在当前**路由改变**时，并且**该组件被复用**时调用，可以通过this访问实例。

- 何为路由改变？何为组件被复用？
  - 对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，组件实例会被复用，该守卫会被调用
  - 当前路由query变更时，该守卫会被调用

##### beforeRouteLeave

- 这个离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 `next(false)` 来取消。

```javascript
beforeRouteLeave (to, from, next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

#### 1.10.4参数解析

##### to(Route)

- 即将要进入的目标路由对象

##### from(Route)

- 当前导航正要离开的路由对象

##### next(Function)

 一定要调用该方法来 **resolve** 这个钩子（结束钩子函数，resolve表示处理成功）。

执行效果依赖 `next` 方法的调用参数：

- **`next()`**
  - 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 **confirmed** (确认的)。
- **`next(false)`**
  - 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 `from` 路由对应的地址。
- **`next('/')` 或者 `next({ path: '/' })`**
  - 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 `next` 传递任意位置对象，且允许设置诸如 `replace: true`、`name: 'home'` 之类的选项以及任何用在 [`router-link` 的 `to` prop](https://router.vuejs.org/zh/api/#to) 或 [`router.push`](https://router.vuejs.org/zh/api/#router-push) 中的选项。
- **`next(error)`**
  -  如果传入 `next` 的参数是一个 `Error` 实例，则导航会被终止且该错误会被传递给 [`router.onError()`](https://router.vuejs.org/zh/api/#router-onerror) 注册过的回调。

**确保 `next` 函数在任何给定的导航守卫中都被严格调用一次。它可以出现多于一次，但是只能在所有的逻辑路径都不重叠的情况下，否则钩子永远都不会被解析或报错**。这里有一个在用户未能验证身份时重定向到 `/login` 的示例：

```javascript
// BAD
router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  // 如果用户未能验证身份，则 `next` 会被调用两次
  next()
})

// GOOD
router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  else next()
})
```

#### 1.10.5完整导航解析流程

![](https://gitee.com/gainmore/imglib/raw/master/img/20210312153321.png)

![](https://gitee.com/gainmore/imglib/raw/master/img/20210312153406.png)

## axios实现Ajax请求

### 2.1特点

- 从浏览器中创建 XMLHttpRequest
- 从 Node.js 发出 HTTP 请求
- 支持 Promise API
- 拦截请求和响应
- 转换请求和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防止 CSRF / XSRF

### 2.2GET请求

使用 axios 发送 GET 请求有两种格式：

- `axios(options)`

  - 采用这种格式需要把发送请求的所有配置选项写在 options 参数中

    ```javascript
    axios({
        method: 'get',	// 请求方式
        url: '/user',	// 请求的url
        params: {name: 'jack', age: 20}	// 传递的参数
    })
    ```

- `axios.get(url[,options])`

  - url：请求的服务器 URL

  - options：发送请求的配置选项

    ```javascript
    axios.get('server.php', {
        params: {	// 传递的参数
            name: 'jack',
            age: 20
        }
    })
    ```

使用 axios 无论发送 GET 请求还是 POST 请求，在发送请求后都需要使用回调函数对请求的结果进行处理。

.then 方法用于处理请求成功的结果，

.catch 方法用于处理请求失败的结果。

```javascript
axios.get('server.php', {
    params: {
        name: 'jack',
        age: 20
    }
}).then((response) => {
    console.log(response.data)
}).catch((error) => {
    console.log(error)
})
```

>这两个回调函数都有各自独立的作用域，直接在函数内使用 this 不能访问到 Vue 实例，
>
>这时只要在回调函数的后面添加 .bind(this) 就能解决这个问题

```javascript
watch: {
    username(val) {
        axios.get('user.json')
        .then((response) => {
            this.data...
        }).bind(this)
    }
}
```

> 运行 axios 代码需要在服务器中，否则会抛出异常。推荐使用 Apache 作为 Web 服务器。

### 2.3POST请求

同样由两种格式：

- `axios(options)`

   ```javascript
  axios({
      method: 'post',
      url: '/user',
      data: 'name=jack&age=20'
  })
  ```

- `axios.post(url, data, [options])`

  ```javascript
  axios.post('server.php', 'name=jack&age=20')
  ```

> axios 采用 POST 方式请求数据时，数据传递的方式有很多种，其中最简单的一种是将传递的参数写成 URL 查询字符串的方式，如：'name=jack&age=20'