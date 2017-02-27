

<a name="veiw"></a>
# view 节点模块

一个路由节点中最重要的就是其中的view字段，它是一个标准Regular组件


### 模块钩子函数

模块与一般的Regular组件的区别是, 这个节点可能实现了某些路由相关的__钩子函数__

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


- enter: 进入路由节点的钩子函数
- leave: 离开路由节点的钩子函数
- mount: 本模块参数有更新的钩子函数, enter时也会自动调用此钩子函数

对于`enter`, `leave`已经很清晰 ， `mount` 除了在enter之后会调用外， 在本模块还在激活状态，但路由参数发生改变时也会调用, 例如从`/blog?id=2` 切换为 `/blog?id=3`


__所有钩子函数都是可选的, 比如大部分情况你都只需实现mount即可，未实现环节会直接跳过__.


- 跳转参数option
所有钩子函数都接受同样的option参数, 我们会在[跳转参数]中进行介绍, 它可以帮助你获取


### manager的引用 `this.$state`

每个路由模块内部都有`$state`属性， 它就是这个单页系统的Manager实例， 你可以用它来进行跳转和事件监听等操作.

- 范例

```js
const Blog = Regular.extend({
    config(){
      this.$state.on('end', ()=>{
        this.updateMenu() //跳转结束后，更新导航菜单
      })
    },
    nav(){
      this.$state.go('app.blog', {
        param: {
          id: 100
        }
      })
    }
})
```

__注意,`$state`只会注入到模块内，如果其它组件有需求则需手动传入.__

```html
<menu manager={this.$state} />
```



<a name="async-view"></a>
## 异步模块注册


regular-state可依托模块系统实现异步加载的功能，使得模块加载延迟到路由节点命中之时，以webpack为例


```js

const App = require('./module/app')
const Blog = require('./module/blog')

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
           view: function(option, resolve){
                require.ensure([], function(){
                   resolve(require('./module/chat')) 
                })
           }
       }
    }).start({ // 启动路由
      view: document.getElementById('app') //顶层容器节点
    }); 
```

此时Chat模块对应的组件会被webpack打成另外一个bundle,只在需要时才会被加载，具体请参考[Webpack: Code split](https://webpack.github.io/docs/code-splitting.html)章节. 类似requirejs等上一代模块工具也支持异步加载的功能


<a name="r-view"></a>
## r-view指令

r-view代表『子路由模块』插入的位置, 当路由节点被到达，会将其插入到这个容器节点内

```js
const routes = {
  'app': {
    view: Regular.extend({
      template: `<h2>App模块</h2><div r-view></div>`
    })
  },
  'app.blog': {
    view: Regular.extend({
      template: `<h3>Blog模块</h3><div r-view></div>`
    })
  },
  'app.blog.detail': {
    view: Regular.extend({
      template: `<h4>BlogDetail模块</h4>`
    })
  }
}


```

当访问`app.blog.detail`时,得到的DOM结构如下所示

```html
<h2>App模块</h2>
<div>
  <h3>Blog模块</h3>
  <div>
    <h4>BlogDetail模块</h4>
  </div>
</div>
```

以此类推.



<a name="detached-view"></a>
## 实现一个游离路由节点(Detached State)

通过某种小技巧，我们可以实现脱离[r-view](#r-view)限制的路由节点，作者称之为游离路由节点(Detached State)

<a  href="../../example/view-detached.html#/app/blog" target="_blank">例子在此</a>: 注意页面title变化

```js

var title = '你进入了一个游离节点。'
var manager = restate()
    //注册路由
     .state({
        'app': {
            view: App
        },
        'app.blog':{
            view: Blog
        },
        'app.detached':{
            view: Regular.extend({
              enter(){
                this.oldTitle = document.title;
                var i = 0;
                this.tid = setInterval( function(){
                  if( i >= title.length) i = 0;
                  document.title = title[i++]

                }.bind(this), 1000)
              },
              leave(){
                document.title = this.oldTitle;
                clearInterval(this.tid);
              }
            }) 
        }
     }).start({ // 启动路由
       view: document.getElementById('app') //顶层容器节点
     }); 
```



_游离路由节点, 如果引入了状态(如创建了DOM节点), 请在leave进行清理._

<a name="cahced"></a>
## 节点模块的缓存机制

**默认情况下**, 到达过的节点模块会被缓存, 也就是说它们第二次进入后不会再被重新初始化, 而是使用上次的实例重启enter->mount 流程


__如果你主动在leave销毁此组件, 则下次进入后会重新实例化(意味着config和init也会重新调用)__, 再进入enter->mount流程. 


```js
const Blog = Regular.extend({
  enter(){
   if(this.name) alert('blog是缓存的节点')
   this.name = 'blog';
  }
})

const Chat = Regular.extend({
  enter(){
   if(this.name) alert('chat是缓存的节点')
   this.name = 'chat'
  },
  leave(){
   this.destroy(); //主动销毁此组件
  }  
});
```


**[※点击查看范例](../../example/lifecycle-cache.html#/app/chat)**: 切换`blog`和`chat`模块你会发现只有blog是复用的实例







