# 源码导读

目前打包后的代码是 ES2015+，不支持 IE 11。

[Vue 官方时间表](https://github.com/vuejs/vue/projects/6)

**第一阶段** 大体结构

关于阅读顺序，我的建议是

1. 先读 reactivity，能最快了解 Vue 3 的新特性；
2. 再读 rumtime，理解组件和生命周期的实现；
3. 如果还有时间再读 compiler，理解编译优化过程。

另外如果你想省时间，可以直接看所有 `__tests__` 目录里的测试用例来了解 Vue 3 的所有功能。目前有不到 700 个测试用例。

## 代码结构

代码仓库中有个 packages 目录，里面是 Vue 3 的主要功能的实现，包括

- reactivity 目录：数据响应式系统，这是一个单独的系统，可以与任何框架配合使用。
- runtime-core 目录：与平台无关的运行时。其实现的功能有虚拟 DOM 渲染器、Vue 组件和 Vue 的各种API，我们可以利用这个 runtime 实现针对某个具体平台的高阶 runtime，比如自定义渲染器。
- runtime-dom 目录: 针对浏览器的 runtime。其功能包括处理原生 DOM API、DOM 事件和 DOM 属性等。
- runtime-test 目录: 一个专门为了测试而写的轻量级 runtime。由于这个 rumtime 「渲染」出的 DOM 树其实是一个 JS 对象，所以这个 runtime 可以用在所有 JS 环境里。你可以用它来测试渲染是否正确。它还可以用于序列化 DOM、触发 DOM 事件，以及记录某次更新中的 DOM 操作。
- server-renderer 目录: 用于 SSR。尚未实现。
- compiler-core 目录: 平台无关的编译器. 它既包含可扩展的基础功能，也包含所有平台无关的插件。
- compiler-dom 目录: 针对浏览器而写的编译器。
- shared 目录: 没有暴露任何 API，主要包含了一些平台无关的内部帮助方法。
- vue 目录: 用于构建「完整构建」版本，引用了上面提到的 runtime 和 compiler。

响应式原理 @vue/reactivity 模块

这是一个极其重要的模块，它是一个数据响应式系统。其暴露的主要 API 有

- ref（数据容器）
- reactive（基于 Proxy 实现的响应式数据）包装数据
- computed（计算数据）
- effect（副作用）定义数据变化后的回调
- ...

参考:

- https://juejin.im/post/5d977f47e51d4578453274b3

**第二阶段** 代码调试

- [调试](https://juejin.im/post/5d9ecf7a5188251b024e2665)

1. 开启 sourceMap
2. 配置 examples 并起服务
    - 手动执行 `http-server -p 8090 -o`
    - 自动执行通过 `rollup-plugin-server-io`
    - 右键执行 vscode 插件 Live Server

然后执行 `yarn dev reactivity` 后浏览器访问examples `reactive.html`

[Observer vs Pub-Sub pattern](https://hackernoon.com/observer-vs-pub-sub-pattern-50d3b27f838c)

参考:

- [基于发布-订阅模式分析vue3.0响应式原理](https://juejin.im/post/5da3e36be51d4578045a3597)
- [reactivity源码解析](https://juejin.im/post/5d9eea80e51d45781e0f5e02)
- [mount源码解析](https://juejin.im/post/5dba495ce51d452a2d10307c)
- https://github.com/KieSun/vue-interpretation
