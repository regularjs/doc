
# 起步

__注意:__

- 文档只针对0.6以上版本, 包括__regularjs__和__regular-state__两个包. 
- 文档主要针对单页路由系统做解释，不再介绍Regular组件的使用方式，请参考[Regular文档](http://regularjs.github.io/guide/zh/index.html)



## 安装

__npm__
```
npm install regular-state --save
npm install regularjs --save
```
_注意_: regular-state并没有依赖具体版本的Regular(使用的[peerDependencies](https://nodejs.org/en/blog/npm/peer-dependencies/)), 需要同时依赖



## 使用

__npm__
```javascript

const restate = require('regular-state')

const routes = {
    //略...
}

restate()
    .state(routes) //完成路由节点注册 
    .start({ html5: true }) // 启动路由

```

__直接引入__

```html
<!-- 这个文件在regular仓库的dist目录 -->
<script src='regular.js' />
<!-- 此文件在restate的根目录 -->
<script src='restate.pack.js' />
<script>
    // restate是全局变量
    restate(
        // ...略
    )
</script>
```


## 简单范例

**<a href='../example/start-1.html#/app/blog' target='_blank'>例子点此链接</a>**

代码如下(简单起见我们用ES6的模板字符串管理template)

```js

var App = Regular.extend({
   template: 
     `<div>
       <h2>主页</h2>
       <div>
         <a href='#/app/chat'>Go Chat</a>|
         <a href='#/app/blog'>Go Blog</a>
       </div>
       <div r-view ></div>
      </div>
     `
 })
 var Blog = Regular.extend({
   template: `
     <div>
       Blog Page 
     </div>
   `  
 })
 var Chat = Regular.extend({
   template: `
     <div>
       <h2>聊天室</h2>
     </div>
   `  
 });

 var manager = restate()
    //注册路由
     .state({
        'app': {
            view: App
        },
        'app.blog':{
            view: Blog
        },
        'app.chat':{
            view: Chat
        }
     }).start({ // 启动路由
       view: document.getElementById('app') //顶层容器节点
     }); 


```


__简要说明__

- restate方法返回一个manager实例，[路由相关控制](docs/core/jump.md)都要通过这个实例操作, 这个属性在启动后会注入到每个[节点模块](docs/core/view.md)作为`this.$state`属性
- 每层路由节点别名以 **'.'** 作为分割, `app`为`app.blog`的父节点，以此类推实现n级路由
- 将节点别名中的**'.'**替换为**'/'**即为默认匹配路径，但是我们可以[自定义匹配方式](docs/core/match.md)。
- 注册路由时，每个节点的View即一个Regular组件, 可以通过为组件实现[钩子函数](生命周期)以监听路由变化
- **`r-view`**这个指令代表子路由节点对应view的插入位置, 比如`app.blog`节点对应的父节点是`app`, 他会被插入到App组件模板对应的 `r-view`节点内.










