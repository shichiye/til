# Proxy-Reflect
## 使用 `defineProperty` 监听对象

利用 `Object.defineProperty` 的存储属性描述符来监听对象

```js
const obj = {
  name: 'scy',
  age: 26
}


Object.keys(obj).forEach(key => {

  let value = obj[key]

  Object.defineProperty(obj, key, {
    get: function() {
      console.log(`监听到${key}属性被读取`)
      return value
    },
    set: function(newValue) {
      console.log(`监听到${key}属性被设置`)
      value = newValue
    }
  })
})

console.log(obj.name)

obj.name = 'lzy'
obj.age = 18
```
### 缺点
1. `Object.defineProperty` 的设计初衷，并不是为了去监听一个对象中的属性变化的，初衷是为了定义普通的属性
2. 如果想监听除了设置和读取之外的操作，比如新增属性、删除属性，那么 `Object.defineProperty` 就不能满足需求

## 使用 `Proxy` 监听对象
### Proxy基本使用
ES6中，新增了一个Proxy类，用于帮助我们创建一个代理，如果想监听一个对象的相关操作，那么我们可以先创建一个Proxy对象，之后对该对象的所有操作，都通过代理来完成，代理对象可以监听我们想要对原对象做的操作。

```js
const obj = {
  name: 'scy',
  age: 18
}

const objProxy = new Proxy(obj, {
  // 获取值时的捕获器
  get: function(target, key) {
    console.log(`监听到${key}属性被读取`)
    return target[key]
  },

  // 设置值时的捕获器
  set: function(target, key, newValue) {
    console.log(`监听到${key}属性被设置`)
    target[key] = newValue
  }

  // 监听in的捕获器
  has(target, key) {
    console.log(`监听到${key}属性的in操作`)
    return key in target
  }

  // 监听delete的捕获器
  deleteProperty(target, key) {
    console.log(`监听到${key}属性的delete操作`)
    delete target[key]
  }
})
```

## Reflect
Reflect也是ES6新增的一个API，它是一个对象，字面意思是反射，提供了很多操作对象的方法，有点像Object中操作对象的方法

但是有Object可以操作对象，那么为什么还需要 `Reflect` 对象呢
* 在早期ECMA规范中没有考虑到对 对象本身 的操作如何设计会更加规范，所以将这些API放到了Object上面，但是这些操作放到Object上并不合适
* 另外包含一些 in delete 命令式的操作符，看起来有一些奇怪
* 所以在ES6中新增了 Reflect ，规范了对对象的操作

```js
const obj = {
  name: 'scy',
  age:18
}

const objObject = new Proxy(obj, {
  get: function(target, key, receiver) {
    console.log(`监听到${key}属性被读取`)
    return Reflect.get(target, key)
  },
  set: function(target, key, newValue, receiver) {
    console.log(`监听到${key}属性被设置`)
    Reflect.set(target, key, newValue)
  }
})

```
