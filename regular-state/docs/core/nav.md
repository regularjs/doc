# 跳转控制

一般开发者涉及到三种路由跳转方式

例子都以下述配置为例

```js
const routes = {
  'app': {
    view: Regular.extend({
      template: `<h2>App模块</h2><div r-view></div>`
    })
  },
  'app.blog': {
    url: 'blog/:id', //  /:id也是可以的
    view: Regular.extend({
      template: `<h3>Blog模块</h3><div r-view></div>`
    })
  },
  'app.blog.detail': {
    view: Regular.extend({
      template: `<h4>BlogDetail模块</h4>`
    })
  },
  'app.blog.edit': {
    view: Regular.extend({
      template: `<h4>BlogEdit模块</h4>`
    })
  }
}
```

## [API] `r-link`

`r-link`会根据是否以[`html5`模式启动](./boot.html)来判断设置的href格式


__example__

当`this.data.id` 为1.

```html

<!-- this.data.id = 1 -->
<a href='app.blog({id:id})'>A</a>

```


以hash模式启动时输出

```html
<a href="#/app/blog/1" >A</a>
```

以html5模式开启时会输出  

```html
<a data-autolink="data-autolink" href="/app/blog/1">A</a>
```
其中`data-autolink`是一个标记，使得系统知道这个链接需要单页内跳转


__当启动时设置了`root`__, `r-link`也会一并处理， 所以始终建议使用`r-link`去设置路径而不是手动拼装`href`属性


<a name="nav"></a>
## [API] `manager.nav`

跳转到指定路径，[url中匹配到的参数](#param)会合并到option, 并最终传给之前提到的`enter`, `leave`, `mount`函数.


__Usage__

`manager.nav(url[, option][, callback])`;



__Argument__

|Param|Type|Detail|
|--|--|--|
|url |String| 跳转url|
|option(optional) |Object|[路由option](#option). url参数会作为option的param参数. |
|callback(optional)|Function|跳转结束后，此函数会被调用|


__control option__

* option.silent: 如果传入silent, 则只有url路径会发生改变，但是不触发manager内部的状态改变, 即不会有enter, leave或mount触发
* option.replace: 如果replace === true, 之前历史的记录会被当前替换，即你无法通过浏览器的后退，回到原状态了

__Example__

`manager.nav("/app/blog/1?name=leeluolee", {data: 1}); `


最终传入到钩子函数的option会是 


`{param: {id: "1", name:"leeluolee"}, data: 1}`.




<a name="go"></a>
## [API] `manager.go`

跳转到特定状态, 与 [manager.nav](#nav) 非常相似，但是`manager.go` 使用状态别名来代替url路径进行跳转

__Usage__

`manager.go(stateName [, option][, callback])`;


__Arguments__

- stateName [String]:  目标状态别名

- option [Object]: 控制跳转行为  此参数会merge到[跳转参数option]()中

  - option.param: 

    nav与go的最大区别就在于param参数 

    如果你的路径中带有参数，则需要传入param来帮助encode函数推算出url路径


  - option.replace: 见[manager.nav](#nav)

  - option.encode: 

  默认是true, 如果encode是false. 则地址栏的url将不会发生变化，仅仅只是触发了内部状态的跳转. 当encode为true时， `regular-state`会使用[encode](../api.md) 函数去反推url路径并显示在location中.


- calback [Function]: 跳转结束后的回调函数

所有其它的option属性将会与param一起传入 `enter`, `leave` , `mount`中.


__Example__

```
manager.go('app.blog.detail', {param: {id:1, name: 'leeluolee'}});
```

地址会跳转到`#/app/blog/1?name=leeluolee`, 你可以发现未命名参数会直接拼接在url后方作为queryString 存在.


__在大型项目中始终推荐使用go代替nav来进行跳转， 这样在路由规则发生变更时，就无需做大量重构__

