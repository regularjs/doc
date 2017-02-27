# 跳转参数option


每个钩子函数都会接受到同样的option参数， 如

```js
const App = Regular.extend({
    template: '略',
    enter(option){ //进入路由节点的钩子函数
        
    },
    leave(option){ //离开路由节点的钩子函数

    },
    mount(option){ // 本模块参数有更新的钩子函数, enter时也会自动调用此钩子函数

    }
})

```


## option说明



<a name='param'></a>
### - option.param 路由参数

路由参数可以通过[`manager.decode`](../api.md)方法来进行测试, 


**你也可以直接使用[decode测试页面](../../example/route-decode.html)来测试**



####1.  命名参数但是未指定捕获pattern.

__Example__

Pattern`/contact/:id`可以匹配路径`/contact/1`, 并得到param`{id:1}`

事实上，所有的命名参数的默认匹配规则为`[-\$\w]+`. 当然你可以设置自定规则.


####2. 命名参数并指定的规则

命名参数紧接`(RegExp)`可以限制此参数的匹配(不要再使用子匹配). 

例如`/contact/:id(\\d+)`即代表只有数字是被允许匹配的


####3. 未命名的匹配规则


你可以捕获未命名的规则

__Example__

```sh
/contact/(friend|mate)/:id([0-9])/(message|option)
```

这个Pattern可以匹配`/contact/friend/4/message` 并获得参数 `{0: "friend", id: "4", 1: "message"}`

如你所见，未命名参数会以自增+1的方式放置在参数对象中.


#### 4. param in search

你当然也可以设置querystring . 以 `/contact/:id`为例.

输入`/contact/1?name=heloo&age=1`, 最终你会获得参数`{id:'1', name:'heloo', age: '1'}`. 


#### 5. 隐式 param

就像使用HTTP 请求一样， body参数不会显示在url上. 同样的, 利用一些小技巧可以让你在stateman实现隐式的参数传递. 换句话说， 你也可以传递非字符串型的参数了。


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



### - option.current 路由参数

代表目标路由节点

### - option.previous 路由参数

代表初始路由节点