# 路由注册

首先，我们需要通过restate方法获得一个manager实例

<a name="api-restate"></a>
### [API]`restate([conf])`

- __参数__
    + `conf.routes`: 如果传入routes配置, 会直接调用[state方法](#api-manager.state)注册

- __return__ Manager实例, Manager是Stateman的子类，改写了部分方法

`restate`即"regular-state"模块暴露的对外接口

```js
const restate = require('regular-state');

const manager = restate({
    routes: {
        'app.blog': {
            url: '/blog/:id',
            view: Blog
        }
    }
})

```

<a name="api-manager.state"></a>
### [API]`manager.state(routes)` or `manager.state(stateName[, config])`

可通过manager实例的[state](../api.html#state)方法注册路由节点


- __Usage__

注册多个路由节点

```js
manager.state({
    'app.blog': {
        title: '博客'
        url: '/blog/:id',
        view: Blog
    },
    'app.user': {
        url: '/user/:id',
        view: User
    }
})
```

或者只注册一个节点

```js
manager.state('app.blog', {
    url: '/blog/:id'
    view: Blog
})
```

- __Return__ manager 


当__第一个参数为字符串，第二参数空缺时__,方法成为get函数，此时返回路由节点

```js
manager.state('app.blog');// 返回 {view:Blog, url: '/blog/:id'}
```

- __Return__ state


上例的`app.blog`等字串我们称之为stateName, 它除了用作此路由节点的标示, 同时也暗示了各路由节点间的层级关系.

### config参数说明


####  config.view: 

一个『特殊』的Regular组件, 请参考[模块章节](./view.md)


<a name="url"></a>
####  config.url


默认情况下, 将点**.** 替换为 **/**, 即为此节点的默认匹配路径. 同时**regular-state**也支持通过`url`来配置匹配路径

每个state对应的捕获url是所有在到此状态路径上的state的结合. 比如`app.blog.detail` 是 `app`,`app.blog` 和`app.blog.detail`的路径结合


__Example__

```js

manager.state({
    'app':{view: App},
    'app.blog':{
        url: '/blog/:id',
        view: Blog
    },
    'app.blog.detail':{
        view: BlogDetail
    }
})
```

<a target='_blank' href='../../example/route-base.html#/app/blog/1/detail'>点此查看DEMO</a>

`app.blog.detail`的完整捕获路径就是`/app/blog/:id/detail`. 

__诚如你所见, 我们可以在url中定义我们的[路径参数](./option.md#match)__

_注意`/`缺少或多余不会影响到匹配, 比如`blog/:id` 和 `/blog/:id`是等价的_


- __ 绝对路径__

如果你不需要父级的url定义，你可以使用`^`符号来使用绝对路径


```js
manager.state({
    "app.blog.detail": {
        url: "^/detail/:id"
        view: 
    },
    "app.blog.detail.message": {
        view: BlogDetailMessage
    }

});
```

这样`app.blog.detail`的路径会直接变成 `/detail/:id`，子状态会被影响到也变为`/detail/:id/message`. 

- __空路径__: 放弃当前这级的路径配置

如果你传入`""`, 你会放弃当前url配置, 这样你的捕获路径会与父状态一致(不过匹配优先级更高)

<a target='_blank' href='../../example/route-advance.html'>查看空路径和绝对路径的DEMO</a>

```js

const routes = {
  'app': {
    url: '',
    view: Regular.extend({
      template: `<h2>App模块</h2><div r-view></div>`
    })
  },
  'app.index': {
    url: '',
    view: Regular.extend({
      template: `首页<a href='#/detail/1'>点击跳转到detail模块</a>`
    })
  },
  'app.blog': {
    view: Regular.extend({
      template: `<h3>Blog模块</h3><div r-view></div>`
    })
  },
  'app.blog.detail': {
    url: '^/detail/:id',
    view: Regular.extend({
      template: `<h4>BlogDetail模块</h4>`
    })
  }
}
```

其中`app`和`app.index`使得空路径可以直接定位到`app.index`. 同时`app.blog.detail`使用了绝对路径, 使得不加'/blog'前缀也能指定进行定位

__多种路径支持使得基于Regular-state的单页系统划分不再受url所限，而真正的可以通过页面组织来规划我们的应用__

- config.title

配置`document.title` 

- config.ssr

当开启了SSR渲染后， 我们也可以通过`ssr`参数来[关闭当前节点的服务端渲染](../ssr/config.md) 





