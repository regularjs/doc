# 入门范例


> _如果你没有接触过node, 就找个略懂的同事陪你共同阅读以下内容_


**regular-state**框架要完成服务端渲染(以下简称SSR)需要完成三步工作

1. 服务端: 单页系统根据『请求路径』按路由节点收集『首屏数据data』并构建出『首屏html字符串』,
2. 首屏数据data与首屏html一起发送到浏览器端
3. 首屏html被浏览器渲染成静态DOM结构, 并在前端根据首屏数据data将静态DOM重新激活，使其可以被数据驱动和响应事件


## 1. 服务端构建首屏数据和首屏html

服务端配置route和客户端一致(它们是一个基类的不同子类) , 但是模块引入要修改为 `regular-state/server`

```js
const restate = require('regular-state/server');
```

### manager.run(url)

__参数__

- url: 即被解析的单页路径

__返回__ Promise

- resolve(option): 如果成功匹配会resolve对应的html和首屏数据
    - option.html: 首屏html
    - option.data: 首屏数据，后续[关于data方法小节](#data)会提到
- reject(err): 如果err.code === 404 则证明这个路径和单页系统不匹配


做个简单的小例子吧. 新建一个app.js内容如下

```js

// app.js

const restate = require('regular-state/server');
const Regular = require('regularjs');

const App = Regular.extend({
   template: 
     `<div>
       <h2>主页</h2>
       <div r-view ></div>
      </div>
     `
 })
 const Blog = Regular.extend({
   template: `
      <h3>博客页</h3> 
      <div r-view ></div>
   `  
 })
 const Detail = Regular.extend({
   template: `
     <h4>详情:{name}</h4>
   `,
   enter(option){
      this.data.name = option.param.id 
   }
 });


const routes = {
  'app': {
    view: App
  },
  'app.blog': {
    url: 'blog/:id',
    view: Blog
  },
  'app.blog.detail': {
    view: Detail
  }
}


const manager = restate( { routes: routes });

// MATCH
manager.run('/app/blog/1/detail').then( function( ret ){
  console.log(ret.html)
})

// NOT_FOUND
manager.run('/path/not/found').catch( function(err){
  console.log(err)
})

```

命令行按步骤运行

```shell
npm install regular-state regularjs
node app.js
```


输出的`ret.html`如下

```html

<div>
  <h2>主页</h2>
  <div >
    <h3>博客页</h3>
    <div >
      <h4>详情:</h4>
    </div>
  </div>
</div>
```
同时输出的`ret.data` 如下

```
{
    'app': undefined,
    'app.blog': undefined,
    'app.blog.detail': undefined
}
```

而第二次调用由于无法匹配路由，将会被reject

```js
{code: 400, message: 'NOT FOUND'}
```


但是似乎结果与我们预期并不一样，因为Detail组件在enter修改了name数据却没有出现在首屏了`<h4>详情:{name}</h4>`, 这是因为

> __ 所有上一章节提到的`mount`、`enter`和`leave`事件都不会在服务端被调用, 而是延迟到浏览器端再运行，__

那如何组织首屏数据呢？


针对SSR，regular-state引入了一个与view同级的新的**节点配置项`data`** , 它必须是一个函数


## - data(option [, resolve]): 

__参数__

- option即跳转参数
- resolve(obj): 如果传入了第二个参数，则data被视为异步(与view函数处理方式一致)

当声明resolve时， 传入resolve的数据会被认为是首屏数据, 反之则会直接使用data函数的返回值

少说多做，我们直接修改一下上例的代码吧

```js

const routes = {
  'app': {
    view: App
  },
  'app.blog': {
    url: 'blog/:id',
    view: Blog
  },
  'app.blog.detail': {
    data(option, resolve){

      setTimeout(function(){
        resolve({
          name: 'leeluolee'
        })
      },50)
    },
    view: Detail
  }

}

```

重新运行

`html`变成了

```html
<div>
  <h2>主页</h2>
  <div >
    <h3>博客页</h3>
    <div >
      <h4>详情:leeluolee</h4>
    </div>
  </div>
</div>
```

`data`则变成了

```js
{ 
    'app': undefined,
    'app.blog': undefined,
    'app.blog.detail': { name: 'leeluolee' } 
}
```

_由于Regular的服务端渲染基于模板实现，所以输出干净和一般字符串模板无异， 不需要虚拟DOM那种xx-id的占位符._

## 传输

第二步是传递首屏data和html到浏览器端, 我们对例子做适当修改，使得公用部分(如路由配置和组件定义)可以在服务端和浏览器端同时使用。

为简单起见，我们不再引入webpack这种大型模块系统，而是手动将模块写成[UMD格式](https://github.com/umdjs/umd) 使其可以同时在浏览器和服务端被引用.


`route.js`

```js

(function (root, factory) {
    if (typeof module === 'object' && module.exports) {
        module.exports = factory();
    } else {
        root.routes = factory();
  }
}(this, function () {



    return routes
}));

```

`server.js`

```js
var http = require('http');
var routes = require('./routes');

// 
http.createServer( function(request, response){
  
  response.set()

})





```

`client.js`

```js

restate(routes).start({
  view: document.querySelector('#app'),
  ssr: true // 注意ssr开启默认就是html5模式， 我们需要重新设置html5
})

```







### 亟需改进的地方

- 引入webpack， 避免需要手动去构建umd模块
- 引入koa等web框架， 减少重复代码
- 引入ejs等后端模板， 避免后台逻辑代码和结构混杂


这些都在这个更完整的[SSR版本博客系统的例子里](https://github.com/regularjs/spa-example)进行了改进. 






