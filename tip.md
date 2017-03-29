## 2017-03-15

- Redux 问题：
  - Redux 中 actions 与 events 关系？另外`store.substribe(reducer)`+`store.dispatch(action)`与事件机制有何区别？
  - 合适采用 Redux，或者说 Redux 用来解决哪些问题？小应用就不可以采用吗？参考[https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)

- Arrow function VS closure VS `Function.prototype.bind()`: such as performance https://jsperf.com/arrow-function-vs-closure-vs-bind.

## 2017-03-16

- [React] React 多层嵌套组件 props 传递问题，通过 object spread operator `{...props}`可以简化写法，context 方式官方不推荐
- [Redux] `action.type`全局唯一

## 2017-03-29

- 生产代码的构建放到远程服务器相比各个开发者在本地构建，远程服务器可以每次构建之前安装最新的依赖，而各个开发者每次构建手动安装最新依赖麻烦，如果写成自动命令也比较耗时
