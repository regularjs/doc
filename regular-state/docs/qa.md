# 常见问题QA


#### 1. 为何开启html5后，强刷页面发生404页面没有找到?

开启html5后，需要在服务端将所有页面路由转发到一个页面. 以koa为例,只要在**合适的位置**增加一个中间件即可

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


#### 2. 如何传递不显示在url上的参数

除了param之外，所有__option中的其它参数都不会反应在url上__，如

```js
// 
manager.go('app.blog', {
    param: {
        id: 1000
    },
    blog: {
        title: '类似POST传递的参数'
    } 
})

// Blog模块

var Blog = Regular.extend({
    mount(option){
        console.log(option.blog.title) // == '类似POST传递的参数'
    }
})

```


#### 3. 设计上为何不直接用url作为节点名，既然url也有分层的特性?

url无法满足前端单页应用的设计需要, 基于与url解耦的state分层(其中是**'.'** 还是**'/'**分割并不最重要), 可以更好的让我们可以按界面组织来设计App. 几个例子

**- url变化后，代码也无需修改**

一般来讲 `app` , `app.blog` , `app.blog.detail`这样类似的层级结构是不变的，比如原始请求我们通过querystring来标记参数进行访问
如`/app/blog/detail?id=100`, 对应的**跳转代码和r-link**就是

```js
this.$state.go('app.blog.detail', {param: {id: 100}})

//r-link
<a r-link='app.blog.detail({id:100})' >go</a>
```

假如我们后来需要将id置入url, 例如 `app.blog`的url修改`/blog/:id/detail` , 我们需要做的仅仅只是做如下修改即可

```diff
const routes = {
  'app': {
+   url: '',
    view: App
  },
  'app.blog': {
+   url: 'blog/:id',
    view: Blog
  }
}
```

__※这是一种对重构友好的方案__

__- url无法满足自定义的需求 __

基于状态的可以基于[绝对/相对/空路径等](./docs/core/route.md#url)对url做任意维度的修改，即层级模块间的URL可以自由组合的， 这个大大减少我们的设计成本. 可以将精力完全放在设计模块层级关系上.



#### 5. enter时在manager(`this.$state`)绑定的事件，为什么退出没有解绑

manager是个独立的emitter, 模块本身并不知道manager的事件绑定, 所以需要在leave时，将你在manager中绑定的事件进行清理

```js

const Blog = Regular.extend({
  enter(){
    this.callback = function(){

    }
    this.$state.on('custom', this.callback)
  },
  leave(){
    this.$state.off('custom', this.callback)
  }  
})

```


#### 4. 开启SSR后，首次注入页面的数据具有XSS风险?

通过后台`managr.run(path)`返回的对象包含首次渲染各节点的初始数据data, ** 注意  这个data是没有处理过xss的**， 你需要手动进行清理，最简单的方法就是直接调用regular的safeStringify方法， 如

```js
try{
    result = yield manager.run(this.url);   
}catch(e){
    // 略
}

if(result){
    var safeDataString = require('regularjs/server').safeStringify(result.data)    
}


```

#### 5. 开启SSR后报错为何出现了显示前后端渲染不匹配

![ssr报错](../assets/ssr-err.jpg)

如果出现这种问题，即代码浏览器收到的首屏html和最终由Regular激活的组件结构不匹配，有以下几种可能

1. 直接输出 Date类型，如{date} 
   由于JSON.stringify(服务端数据传递) 和  date.toString(模板渲染)的表现并不一致, 所以会导致节点不匹配，做个简单例子
   ```js
   var date = new Date;

   console.log(date.toString()) // Wed Feb 22 2017 15:24:37 GMT+0800 (CST)
   console.log(JSON.stringify(date)); //2017-02-22T07:24:37.599Z
   ```
   直接增加一个[format过滤器](http://regularjs.github.io/guide/zh/basic/filter.html#-format)即可, 如`{date|format}`
2. 服务端的时区设置与客户端不一致
   这种情况将导致传输的时间在服务端和客户端节点不一致, 建议这种情况，时间类的字段直接format处理或转换为时间戳(timestamp)再参与渲染和输出

3. 服务端/客户端代码不同步
   重启服务器或使用类似 [devtool](https://github.com/Jam3/devtool) 这种支持文件变动后刷新的工具





