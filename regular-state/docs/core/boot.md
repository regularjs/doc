
# 启动路由

上述章节其实都已经提到了`manager.start`, 即是用来启动单页系统的方法

_事实上 启动之后仍然可以通过`manager.state`注册新的路由节点_

## manager.start 

启动路由，即regular-state开始响应浏览器的nav事件.

__Usage__

`manager.start(option)`

__option__


|Param|Type|Detail|
|--|--|--|
|html5 |Boolean|(default false)  是否开启html5支持, 即使用pushState等API代替hash设置|
|root |String|(default '/') 程序根路径，影响到你使用html5模式的表现|
|prefix| String |配置hash模式前缀, 例如你可以传入'!'来达到`#!/contact/100`的hash表现}|


__Example__

```js
manager.start({
  "html5": true,
  "prefix": "!",
  "root": "/root" //the app is begin with '/root'
})

```

## 自动升级降级策略


如果你在不支持html5 pushState的浏览器开启了`html=true`, manager会自动降级到hash的路由. 

就如同上例的配置.

1. 如果我们在不支持html5(pushState相关)的浏览器访问`/root/app`, manager会降级到hash的路由，并自动定向到`/root#/app`的初始状态.

2. 如果我们在__支持html5__的浏览器使用__`/root#/app`__, manager同样会使用history的路由路径，并自动返回到 `/root/app`的初始状态.





## 启动html5模式，后台路由需要统一定位到单页入口页面

启动html5模式时，事实上浏览器端希望所有SPA范围内的页面都指向同一页面资源，然后由前端接管路由，否则当你进行了多次跳转后，刷新浏览器就会出现404，因为会重新过服务端的路由, 所以在后台路由需要做一定的配置，以koa为例

```js
const rPathTest = /^\/(blog|chat|$)/

app.use(function *(next){
    // 首页或/blog和/chat后续页面
    if(rPathTest.test( this.path ) ){
        yield this.render('index')
    }else{
        yield next;
    }
})
```

你也可以修改nginx等前端服务端配置做对应的匹配转发.


