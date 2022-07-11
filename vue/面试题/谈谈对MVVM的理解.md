# 对MVVM的理解

## MVC
如果使用传统的MVC，大量的逻辑会耦合在 `Controller` 一层，维护困难

## MVVM
划分 `View` `ViewModel` `Model` 三层，`ViewModel` 层隐藏 `数据绑定` 和 `监听页面` （Controller）的逻辑

传统的 `MVVM` 不能手动得操作视图更新，但是 `Vue` 中有一个 `ref` 属性，可以去手动更新，所以 `Vue` 不是标准的 `MVVM`

`v-model` 就是 `ViewModel` 的一个体现
