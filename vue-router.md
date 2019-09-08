# Vue Router

## 简介
Vue Router 是 Vue.js 官方的路由管理器，它的主要功能包含：
- 嵌套的路由/视图表
- 模块化的、基于组件的路由配置
- 路由参数、查询、通配符
- 基于 Vue.js 过渡系统的视图过渡效果
- 细粒度的导航控制
- 带有自动激活的 CSS class 的链接
- HTML5 历史模式或 hash 模式，在 IE9 中自动降级
- 自定义的滚动条行为

## 动态路由

- 路径参数
路径参数使用冒号":"标记，当匹配到一个路由时，参数值会被设置到 this.$route.params，可以在每个组件内使用。可以在一个路由中设置多段路径参数。

**注意:当使用路由参数时，例如从 /user/foo 导航到 /user/bar，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用。**

- 捕获所有路由
如果想匹配任意路径，可以使用通配符"*"。当使用通配符路由时，请确保含有通配符的路由应该放在最后。

- 高级匹配模式
参见[[path-to-regex]](https://github.com/pillarjs/path-to-regexp)

- 匹配优先级
有时候，同一个路径可以匹配多个路由，此时，匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高。

## 嵌套路由

嵌套路由使用children属性配置，参见如下：
```
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```
children 配置就是像 routes 配置一样的路由配置数组，所以可嵌套多层路由。
此时，基于上面的配置，当你访问 /user/foo 时，User 的出口是不会渲染任何东西，这是因为没有匹配到合适的子路由。如果你想要渲染点什么，可以提供一个 空的 子路由：
```
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id', component: User,
      children: [
        // 当 /user/:id 匹配成功，
        // UserHome 会被渲染在 User 的 <router-view> 中
        { path: '', component: UserHome },   //缺省路由

        // ...其他子路由
      ]
    }
  ]
})
```

## 编程导航

- router.push(location, onComplete?, onAbort?）
除了使用\<router-link\>创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。

**注意：在 Vue 实例内部，你可以通过 $router 访问路由实例。因此你可以调用 this.$router.push**

该方法的参数可以是一个字符串路径，或者一个描述地址的对象。例如：
```
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: '123' }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```
**注意：如果目的地和当前路由相同，只有参数发生了改变 (比如从一个用户资料到另一个 /users/1 -> /users/2)，你需要使用 beforeRouteUpdate 来响应这个变化**

- router.go(n)
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步。
```
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)
```
**注意：如果history记录不够用，则跳转失败。**

## 命名路由
创建 Router 实例的时候，可以在 routes 配置中给某个路由设置名称。

```
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',    // 路由名称
      component: User
    }
  ]
})
```

## 命名视图
有时候想同时 (同级) 展示多个视图，而不是嵌套展示，例如创建一个布局，有 sidebar (侧导航) 和 main (主内容) 两个视图，这个时候命名视图就派上用场了。你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。

```
<router-view class="sidebar"></router-view>
<router-view class="main" name="main"></router-view>
<router-view class="foot" name="foot"></router-view>
```
一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。
```
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: SideBar,
        main: Main,
        foot: Footer
      }
    }
  ]
})
```

## 重定向
使用router完成重定向
```
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/foo' },
    { path: '/b', redirect: { name: 'bar' }},
    { path: '/c', redirect: to => {
      // 方法接收 目标路由 作为参数
      // return 重定向的 字符串路径/路径对象
    }
  ]
})
```

## 导航守卫
通常使用导航守卫来实现用户未认证时跳转到登录页的功能。

- 全局前置守卫
```
router.beforeEach((to, from, next) => {
  // ...
})
```

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于等待中。
每个守卫方法接收三个参数：

to: Route: 即将要进入的目标 路由对象

from: Route: 当前导航正要离开的路由

next: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。

next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。

next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。

next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在 router-link 的 to prop 或 router.push 中的选项。

next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。

- 全局后置钩子
```
router.afterEach((to, from) => {
  // ...
})
```