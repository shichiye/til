# 防抖函数
```javascript
function debounce(fn, delay, immediate = false, resultCallBack) {
  // 保存上一次执行的定时器
  let timer = null
  let isInvoke = true

  const _debounce = function(...args) {
    // 如果上一次的定时器存在，则取消上一次的定时器
    if (timer) clearTimeout(timer)

    // 判断是否需要立即执行
    if (immediate && isInvoke) {
      const result = fn.apply(this, args)
      if (resultCallBack) resultCallBack(result)
      isInvoke = false
    } else {
      // 延迟执行
      timer = setTimeout(() => {
        // 执行外部传入的函数
        const result = fn.apply(this, args)
        if (resultCallBack) resultCallBack(result)
        timer = null
        isInvoke = true
      }, delay)
    }
  }

  // 封装取消功能
  _debounce.cancel = function() {
    if (timer) clearTimeout(timer)
    timer = null
    isInvoke = true
  }

  return _debounce
}
```