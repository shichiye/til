# IIFE 立即调用函数表达式
## 使用
```javascript
(function () {
  statements
})()
```
主要包含两个部分

第一部分是()里的一个匿名函数，函数拥有独立的词法作用域，这样做避免了外界访问此IIFE中的变量，而且也不回污染全局作用域。

第二部分再一次使用()执行这个函数表达式。

将IIFE分配给一个变量，可以存储IIFE执行后的返回结果。
```javascript
 var result = (function () {
    var name = "shichiye"
    return name
})();
// IIFE 执行后返回的结果：
console.log(result) // "Barry" 
```
## 防御性分号
防御性分号是用来防止两个Javascript文件合并时产生的问题。
比如说，文件一：
```javascript
var foo = bar
```
文件二：
```javascript
(function() {
  // ...
})()
```
合并后会变成：
```javascript
var foo = bar
(function() {
  // ...
})()
```
这样bar会当作一个接收函数类型参数的函数，而防御性分号就可以解决这个问题：
文件二：
```javascript
;(function() {

})()
```
合并后：
```javascript
var foo = bar;
(function() {
  // ...
})()
```