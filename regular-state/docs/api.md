
# API清单

API根据知识结构散落在各个章节，不方便查找，本章提供了快速索引清单

__注意__

_**※** 标记的含义是此Manager实例方法与Stateman的一致(Manager为Stateman的子类), 所以部分函数会直接链接到[Stateman文档](https://leeluolee.github.io/stateman)__



## API列表

- **[restate](./core/route.md#api-restate)**: 实例一个Manager对象
    - return Manager
- **[manager.start](./core/boot.md#api-manager.start)**: 启动路由
- **[manager.state](./core/route.md#api-manager.state)**: 注册路由
- **[※manager.go](./core/boot.md#api-manager.start)**: 根据别名跳转
- [※manager.nav](./core/boot.md#api-manager.start): 根据url跳转
- **[※manager.on](https://leeluolee.github.io/stateman/?doc=API&lang=zh#stateman-文档-api-statemanon)**: 绑定事件
- **[※manager.off](https://leeluolee.github.io/stateman/?doc=API&lang=zh#stateman-文档-api-statemanoff)**: 解绑事件
- [※manager.emit](https://leeluolee.github.io/stateman/?doc=API&lang=zh#stateman-文档-api-statemanemit): 手动触发事件
- 指令
    - **[r-link](./core/view.md#r-link)**: 跳转链接便利生成指令
    - **[r-view](./core/view.md#r-view)**: 标志子模块的插入位
- **[内建事件](./docs/core/event.md)**
    - begin: 跳转开始前触发，你可以组织这次跳转
    - end: 跳转结束触发
    - notfound: 跳转目标未找到触发
- [跳转参数option](./core/option.md)
    - **[option.param](./core/match.md)**: 路由参数
    - option.current: 目标路由节点
    - option.previous: 跳转前路由节点

-------

- [※manager.is](https://leeluolee.github.io/stateman/?doc=API&lang=zh#stateman-文档-api-statemanis): 判断当前URL是否满足传入的stateName
- [※manager.encode](https://leeluolee.github.io/stateman/?doc=API&lang=zh#stateman-文档-api-statemanencode): 根据状态名和参数编码URL
- [※manager.decode](https://leeluolee.github.io/stateman/?doc=API&lang=zh#stateman-文档-api-statemandecode):从URL解码状态和参数
- [※manager.stop](https://leeluolee.github.io/stateman/?doc=API&lang=zh#stateman-文档-api-statemanstop): 停止路由



