# Vue如何检测数据的变化
`Vue2` 中并没有使用 `defineProperty` 来检测我们的数组，因为性能差。所以 `Vue2` 就采用重写数组的方法（原型链 + 函数劫持）来实现（7个变异的方法来改变原数组）。缺陷在于不能检测到索引更改和数组长度的更改。数组中的每个元素也会再次被监听。
