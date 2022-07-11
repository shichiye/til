# 如何理解Vue的模版编译原理

1. 将模版变成ast语法树
2. 对ast语法树进行优化，标记静态节点
3. 代码生成，拼接render函数字符串

## 详细
Vue 的编译模块包含 4 个目录：
```js
compiler-core
compiler-dom // 浏览器
compiler-sfc // 单文件组件
compiler-ssr // 服务端渲染
```

其中 compiler-core 模块是 Vue 编译的核心模块，并且是平台无关的。而剩下的三个都是在 compiler-core 的基础上针对不同的平台作了适配处理。

Vue 的编译分为三个阶段，分别是：parse、transform、codegen。

其中 parse 阶段将模板字符串转化为语法抽象树 AST。transform 阶段则是对 AST 进行了一些转换处理。codegen 阶段根据 AST 生成对应的 render 函数字符串。


