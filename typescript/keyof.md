# `keyof`
## `keyof` 类型操作符
对一个对象类型使用 `keyof` 操作符，会返回该对象属性名组成的一个字符串或者数字字面量的联合。
```typescript
type Point = { x: number; y: number }
type P = keyof Point
```
这个例子中类型 `P` 就等同于 `"x" | "y"`
```typescript
type Arrayish = { [n: number]: unknown };
type A = keyof Arrayish;
// type A = number

type Mapish = { [k: string]: boolean };
type M = keyof Mapish;
// type M = string | number
```
注意在这个例子中，M 是 `string | number`，这是因为 JavaScript 对象的属性名会被强制转为一个字符串，所以 `obj[0]` 和 `obj["0"]` 是一样的。

## 数字字面量联合类型
在一开始我们也说了，keyof 也可能返回一个数字字面量的联合类型，那什么时候会返回数字字面量联合类型呢，我们可以尝试构建这样一个对象：

```typescript
const NumericObject = {
  [1]: "冴羽一号",
  [2]: "冴羽二号",
  [3]: "冴羽三号"
};

type result = keyof typeof NumericObject

// typeof NumbericObject 的结果为：
// {
//   1: string;
//   2: string;
//   3: string;
// }
// 所以最终的结果为：
// type result = 1 | 2 | 3
```

## 实战
我们希望获取一个对象给定属性名的值，为此，我们需要确保我们不会获取 obj 上不存在的属性。所以我们在两个类型之间建立一个约束：
```Typescript
function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
  return obj[key]
}

let x = { a: 1, b: 2, c: 3, d: 4 }

getProperty(x, "a")
getProperty(x, "m")
// Argument of type '"m"' is not assignable to parameter of type '"a" | "b" | "c" | "d"'.

```
