## 生命周期

生命周期简单理解即: __完成一次跳转，受影响路由节点的钩子函数的调用顺序__

例如我们定义了如下routes

```js
{
    'app': {
        view: App
    },
    'app.blog': {
        view: Blog
    },
    'app.blog.detail': {
        view: BlogDetail
    },
    'app.blog.edit': {
        view: BlogEdit
    }
}
```

[**『点击查看DEMO』**](../../example/lifecycle-base.html#app/blog/detail)

当我们切换url从 `app.blog.detail` 跳转到 `app.blog.edit` 时, 发生的钩子函数执行顺序如下

```shell
BlogDetail#leave
App#mount
Blog#mount
BlogEdit#enter
BlogEdit#mount
```

_注: 因为enter会自动调用mount，所以他们是成对出现的_



## 异步的钩子函数


当发生以下情况时，框架会认为这个钩子函数是需要异步执行的，会等待其完成后再进行下一步行动


#### 1. 钩子函数除了[路由参数option](./option.md)外, 还声明了第二个参数resolve『推荐使用』

```js

const Blog = Regular.extend({
    enter: (option, resolve){
        console.log('go next state after 3s')
        setTimeout(resolve, 3000)
    }
    // ... 略
})
```


⚠️请注意，如果声明了resolve但是没有调用，跳转会被block,切记 


#### 2. 钩子函数执行后返回了一个[Promise对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 

```js
const Blog = Regular.extend({
    leave: (option){
        console.log('leave after 3s')

        return new Promise(function(resolve){
            setTimeout(resolve, 3000)     
        })
    }
    // ... 略
})
```


<a href="../../example/lifecycle-async.html">『点此查看关于异步钩子函数的简单DEMO』</a>
        







