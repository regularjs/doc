
#生命周期

在引入SSR后，原来的`enter`, `mount`, `leave`组成的生命周期就不够用了, 所以SSR版本的regular-state对生命周期做了一定的扩展，即引入了data钩子, data钩子与上述三个钩子函数不同，它调用发生在模块实例化之前，所以它的定义是与模块(即view)同级的.

```js
const routes = {
    'app': { 
        data(){
            return menus
        }
    },
    'app.blog': {
        data(option){
            // 如果返回的是promise， 框架会自动将resolve的数据注入到this.data
            return getBlog(option.param.id)
        },
        view: Blog
    },
    'app.blog.detail': {
        data(option, resolve){
            resolve({ name: 'leeluolee' })
        },
        view: BlogDetail
    }
}
```

以下例子以上述代码为例


## 引入SSR后的扩展流程

假如我们访问/app/blog/detail在服务端发生的情节是

1. 调用`app`的data方法得到对应data, 调用`app`模块的config方法, 最终得到组件初始数据. 组件App的模板会与此初始数据结合产出属于`app`节点的初始html
2. 从`app`的初始html中提取出r-view的位置, 在`app.blog`节点上重复第一步得到`app.blog`的初始数据和初始html, 并将其组装到`app`的r-view位置
3. 以此类推完成`app.blog.detail`的处理

当这个各路由节点组合而成首屏html和首屏data传递到浏览器端时. 组件重新激活的流程是

1. 根据配置的firstData参数从全局拿到首屏数据, 以下称其为globalData
2. 调用`new App({ mountNode: container, data: globalData.app )`方法激活第一层模块(container即你传入start的view参数)
3. 获得第一层模块`app`的r-view节点`appView`, `new Blog({mountNode: appView, data: globalData.app})` 激活第二层
4. 依此类推实现第三层的激活

除了首屏启动，后续就不再会有激活这一过程，所有跳转都将只是单页内的无刷新跳转，__但是data这一方法仍然会被调用__, 这是为了保证在单页内跳转到

所以当后续跳转时，比如app




__当然以上细节只是内部实现__,只是为了更好的帮助开发者理解。
  
