<a name="event"></a>
# 路由事件

系统内置了一个小型Emitter 来帮助实现事件驱动的开发, 我们可以使用`manager.on`、`manager.off`、`manager.emit`来注册/解绑/触发我们的自定义事件

```js

const App = Regular.extend({
    enter(){
        this.callback = function(){
            // blabla...
        }
        this.$state.on('some_event', this.callback)
    },
    leave(){
        // 记得解绑
        this.$state.off('some_event', this.callback)
    }
})

```


### 内建事件

**regular-state**从StateMan继承了一些有用的生命周期相关的内建事件

#### begin

每当跳转开始时触发， 回调会获得一个特殊的参数`evt`用来控制跳转.

你可以通过注册begin事件来阻止某次跳转

```js
    manager.on("begin", function(evt){
      if(evt.current.name === 'app.contact.message' && evt.previous.name.indexOf("app.user") === 0){
        evt.stop();
        alert(" nav from 'app.user.*' to 'app.contact.message' is stoped");
      }
    })

```


#### end

跳转结束后触发

```
manager.on('end', function(option){
  console.log(option.param) // 
})
```


查看 [option](./option.md) 获取更多关于option的信息


#### notfound: 

当发生目标未找到时触发,

__Tips__
你可以监听notfound事件，来将页面导向默认状态

__Example__

```js

manager.on("notfound", function(){
  this.go("app.contact");
})
```