# regular-state介绍


regular-state是基于 [Regular](https://github.com/regularjs/regular)组件库 和 [Stateman](https://github.com/leeluolee/stateman)路由库的单页系统(SPA)框架

## 阅读建议

- 建议至少通读一次指南，实际开发后可以根据**[API清单](./docs/api.md)**和**[概念词汇索引](./docs/words.md)**查漏补缺, 
- 本指南提供了一个简单博客管理系统的DEMO，分别有两个版本
    + html5版
    + ssr(服务端渲染)版本
- 遇到问题请先查看**[常见问题QA](./qa.md)** , 无法解决再


## 特性

- **服务端渲染支持**
- 基于层级状态的**多层路由**支持, 可动态增删路由节点
- **异步路由**支持，每个生命周期都可以异步处理
- 支持**异步View**加载(依赖模块系统支持)
- **强大路径匹配**, 类express语法
- 三种方案(history/hashchange/iframe轮询)的**自动降级与升级**
- 低版本支持(需引入Promise Polyfill)


## 浏览器支持

**regular-state**可运行在支持ES5的浏览器，并且需要Promise的支持. 低级浏览器请使用类似[promise-polyfill](https://github.com/taylorhakes/promise-polyfill)的垫片脚本


## 贡献

- 文档使用markdown格式编写, 使用gitbook维护, 修改提交PR后，我会及时反馈

