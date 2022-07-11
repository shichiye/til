# Vue中如何进行依赖收集

使用观察者模式，每一个属性和每一个对象都有一个对应的dep，存放它所依赖的 watcher，当属性变化时，会触发所有的 watcher，调用 watcher 的 update 方法。

watcher有三种，分别是 渲染watcher，计算属性watcher和用户watcher
