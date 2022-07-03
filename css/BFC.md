# BFC 块级格式上下文
> BFC(Block Formatting Context)是一种块级格式上下文，是块盒子的布局过程发生的区域，规定了内部Block Level Box如何布局，即其子元素将如何定位，以及和其他元素的关系和相互作用。

**具有BFC特性的元素可以看作是隔离了的独立容器，容器里的元素不会在布局上影响到外面的元素，并且BFC具有普通容器所没有的一些特性**

## 触发BFC
* body根元素
* 浮动元素：float除none以外的值
* 绝对定位元素：position为absolute或fixed
* display为inline-block、table-cell或flex
* overflow除了visible以外的值
  
## BFC的特性及作用
### 同一BFC下外边距会发生折叠
这并不是CSS的bug，可以理解成一种规范，如果要避免这样的行为，可以将其放在不同的BFC容器中。

### BFC可以清除浮动
容器内元素浮动，脱离了文档流。如果触发了容器的BFC，那么容器将会包裹着浮动元素。

### BFC可以阻止元素被浮动元素覆盖

