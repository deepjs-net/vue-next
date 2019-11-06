# 源码导读

目前打包后的代码是 ES2015+，不支持 IE 11。

source: https://juejin.im/post/5d977f47e51d4578453274b3

[Vue 官方时间表](https://github.com/vuejs/vue/projects/6)

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

可以看出，新的 Vue 代码仓库是模块化的。接下来我们逐一来看看每个模块暴露的 API。

### @vue/reactivity 模块

这是一个极其重要的模块，它是一个数据响应式系统。其暴露的主要 API 有 ref（数据容器）、reactive（基于 Proxy 实现的响应式数据）、computed（计算数据）、effect（副作用） 等几部分：

很明显，这个模块就是 Composition API 的核心了，其中的 ref 和 reactive 应该重点掌握。
