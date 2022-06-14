# computed
## 用法

接受一个 getter 函数，返回一个只读的响应式 ref 对象，即 getter 函数的返回值。它也可以接受一个带有 get 和 set 函数的对象来创建一个可写的 ref 对象

## 原理

计算属性 `computed` 是在计算复杂逻辑时的一把利器，它会基于它的响应式依赖进行缓存，只有在依赖发生改变时重新求值，起到一定的性能优化作用。

计算属性会计算出一个新的属性，并挂载到Vue实例上。

### computed 函数
#### 函数签名
根据computed api的描述，可以知道computed接受一个函数或者对象类型的参数，看看它的函数签名
```js
export function computed<T>(
  getter: ComputedGetter<T>,
  debugOptions?: DebuggerOptions
): ComputedRef<T>
export function computed<T>(
  options: WritableComputedOptions<T>,
  debugOptions?: DebuggerOptions
): WritableComputedRef<T>
export function computed<T>(
  getterOrOptions: ComputedGetter<T> | WritableComputedOptions<T>,
  debugOptions?: DebuggerOptions,
  isSSR = false
)
```
第一个函数 接受`ComputedGetter`类型的 getter 函数，并返回`ComputedRef`类型的 不可变响应式ref对象

第二个函数 接受一个 `WritableComputedOptions`类型options对象，返回一个`WritableComputedRef`类型的 可写的响应式对象

第三个函数 参数则是最宽泛的情况，getterOrOptions

#### 签名中的类型定义
```javascript
export interface ComputedRef<T = any> extends WritableComputedRef<T> {
  readonly value: T
  [ComputedRefSymbol]: true
}

export interface WritableComputedRef<T> extends Ref<T> {
  readonly effect: ReactiveEffect<T>
}

export type ComputedGetter<T> = (...args: any[]) => T
export type ComputedSetter<T> = (v: T) => void

export interface WritableComputedOptions<T> {
  get: ComputedGetter<T>
  set: ComputedSetter<T>
}
```
从类型定义上看`ComputedRef`和`WritableComputedRef`都是继承自`Ref类型`，即computed返回的是一个ref类型的响应式对象

#### 函数体逻辑
```javascript
export const isFunction = (val: unknown): val is Function =>
  typeof val === 'function'

export function computed<T>(
  getterOrOptions: ComputedGetter<T> | WritableComputedOptions<T>,
  debugOptions?: DebuggerOptions,
  isSSR = false
) {
  let getter: ComputedGetter<T>
  let setter: ComputedSetter<T>

  // 判断getterOrOptions是否为一个函数
  const onlyGetter = isFunction(getterOrOptions)
  if (onlyGetter) {
    getter = getterOrOptions
    // 如果只有getter，在开发环境下调用setter函数会发出警告
    setter = __DEV__
      ? () => {
          console.warn('Write operation failed: computed value is readonly')
        }
      : NOOP
  } else {
    getter = getterOrOptions.get
    setter = getterOrOptions.set
  }

  // 传入getter、setter、是否只读
  const cRef = new ComputedRefImpl(getter, setter, onlyGetter || !setter, isSSR)

  // 如果是在开发环境下effect对象注入传入的收集和触发钩子
  if (__DEV__ && debugOptions && !isSSR) {
    cRef.effect.onTrack = debugOptions.onTrack
    cRef.effect.onTrigger = debugOptions.onTrigger
  }

  return cRef as any
}
```
在这个函数中，会判断传入的数据是一个getter函数还是一个options对象，如果是函数的话只能是一个getter函数，此时赋值给getter，并且在dev环境中访问setter会发出警告；如果是对象的话，computed就会将它作为一个带有get、set属性的对象处理，赋值给对应的getter和setter。最后会返回一个`ComputedRefImpl`类的实例对象

#### ComputedRefImpl类
```javascript
// computed对象
export class ComputedRefImpl<T> {
  // 当前computed的effect集合
  public dep?: Dep = undefined

  // 缓存值
  private _value!: T
  // 放置effect对象
  public readonly effect: ReactiveEffect<T>

  // ref标识
  public readonly __v_isRef = true

  // isReadonly标识
  public readonly [ReactiveFlags.IS_READONLY]: boolean

  // 当前值是否是脏数据
  public _dirty = true
  public _cacheable: boolean

  constructor(
    getter: ComputedGetter<T>,
    private readonly _setter: ComputedSetter<T>,
    isReadonly: boolean,
    isSSR: boolean
  ) {
    // 创建effect对象，将当前getter当作监听函数，并附加调度器
    this.effect = new ReactiveEffect(getter, () => {
      // 如果当前不是脏数据
      if (!this._dirty) {
        // 设置为脏数据
        this._dirty = true
        // 触发修改
        triggerRefValue(this)
      }
    })
    this.effect.computed = this
    this.effect.active = this._cacheable = !isSSR
    // 设置只读标识
    this[ReactiveFlags.IS_READONLY] = isReadonly
  }

  get value() {
    // the computed ref may get wrapped by other proxies e.g. readonly() #3376
    const self = toRaw(this)
    // 收集依赖
    trackRefValue(self)
    // 如果当前是脏数据
    if (self._dirty || !self._cacheable) {
      // 更改脏数据标识状态
      self._dirty = false
      // 执行收集函数，更新缓存
      self._value = self.effect.run()!
    }
    // 如果不是脏数据直接返回缓存值
    return self._value
  }

  set value(newValue: T) {
    this._setter(newValue)
  }
}
```

