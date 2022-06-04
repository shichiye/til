# Vue中简单组件的执行
## Vue的三个核心模块
### 1. Reactivity Module 响应式模块
  创建Javascript响应对象，并可以跟踪其变化
### 2. Compiler Module 编译器模块
  获取HTML模版，并将它们编译成渲染函数
### 3. Renderer Module 渲染模块
  在网页上渲染组件，包含三个阶段
* Render Phase 渲染阶段（调用render函数，返回一个虚拟DOM节点）
* Mount Phase 挂载阶段（使用虚拟DOM节点，并调用DOM API来创建网页）
* Patch Phase 补丁阶段（渲染器将旧的虚拟节点和新的虚拟节点进行比较，并更新网页变化的部分）

## 简单组件的执行
组件中有一个模版template以及在模版内部使用的响应对象

首先，编译器模块将HTML转换为一个渲染函数render function

然后，使用响应式模块初始化响应对象

在渲染模块中，进入渲染阶段，调用引用了响应对象的render函数，并且跟踪这个响应对象的变化，返回一个虚拟DOM节点

接下来，进入挂载阶段，调用mount函数，使用虚拟DOM节点创建web页面

最后，如果我们的响应对象发生任何变化，渲染模块将再次调用render函数，创建一个新的虚拟DOM节点，新旧虚拟DOM节点发送到补丁阶段，根据需要比对，并更新网页
