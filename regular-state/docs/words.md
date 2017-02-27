# 概念词汇索引

- **manager**: 即路由管理器，为restate函数返回的实例, manager最终会被注入到所有模块的`this.$state`属性下.
- **组件**: 这里提到的组件默认都是[Regular组件](https://github.com/regularjs/regular)
- **[路由节点]()**
    传入state函数的每一个key-value对，称之为一个路由节点, 例如
    ```js
     'app': {
        view: App
     }
    ```

- **[模块](./core/view.md)**: 即注册在路由节点的view,它们是可能实现了部分生命周期钩子函数的组件
- **[钩子函数](./core/view.md#hook)**: 模块可以实现钩子函数以监听感兴趣的跳转阶段
- **[异步钩子](./core/view.md#asyc)**: 系统会等待异步钩子函数执行完毕再进入下一步骤
- **[游离路由节点](.core/view.md#detached)**: 即其view不再受r-view的限制. 甚至可以完全没有结构
- **[节点模块缓存](.core/view.md#cached)**: 节点模块被实例化后(即首次访问)会被缓存，你可以手动清理这个缓存
- **[相关指令](#)**
    - **[r-view]()**: 代表子模块插入的容器节点
    - **[r-link]()**: 路由link链接生成
- **[生命周期](./core/lifecycle.md)**: 一次跳转完成的逻辑，主要是相关『钩子函数』调用和事件触发
- **[跳转参数 `option`](./core/option.md)**: 路由参数是传入所有钩子函数的首参数
    ```js
    const App = Regular.extend({
        enter( option ){ // 其它类似
            // option 即跳转参数
            // option.param 是路由参数
        }
    })
    ```
    它包含以下几个关键部分 
    ```js
    option.param// 路由匹配参数
    option.path// 当前跳转路径
    option.current// 目标跳转路由节点
    option.previous// 跳转前路由节点
    ```

- **[路由参数 `param`](./core/option.md#match)**

    路由参数即跳转参数的param字段. 即根据[路由节点url配置](.core/view.md#url)捕获到的路径参数
- **[ssr](./ssr/README.md) **

    ssr 不是阴阳师中的SSR, 它指的是 ** Server Side Rendering 即服务端渲染.** 通过以下方式调用可以按ssr的方式去启动单页应用(即初始HTML已经通过服务端渲染完毕)
    ```js
    manager.start({
        ssr: true
        // 其它参数略
    })
    ```
    每个路由节点都可以配置为非ssr渲染，此时渲染会延迟到客户端进行, 例如

    ```js
    'app': {
        ssr: false, // 配置为非ssr渲染
        view: App
    }
    ```






