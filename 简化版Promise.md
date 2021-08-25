
```javascript
const PENDING = 'pending'
const RESOLVED = 'resolved'
const REJECTED = 'rejected'

function MyPromise(fn) {
    const that = this //因为代码可能会异步执行，用于获取正确的 this 对象
    that.state = PENDING
    that.value = null //value 变量用于保存 resolve 或者 reject 中传入的值
    
    // resolvedCallbacks 和 rejectedCallbacks 用于保存 then 中的回调
    that.resolvedCallbacks = []
    that.rejectedCallbacks = []
    
    function resolve(value) {
      if (that.state === PENDING) {
        that.state = RESOLVED
        that.value = value
        that.resolvedCallbacks.map(cb => cb(that.value))
      }
    }
    
    function reject(value) {
      if (that.state === PENDING) {
        that.state = REJECTED
        that.value = value
        that.rejectedCallbacks.map(cb => cb(that.value))
      }
    }
    
    try {
      fn(resolve, reject)
    } catch (e) {
      reject(e)
    }

}

MyPromise.prototype.then = function(onFulfilled, onRejected) {
  const that = this
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
  onRejected =
    typeof onRejected === 'function'
      ? onRejected
      : r => {
          throw r
        }
  if (that.state === PENDING) {
    that.resolvedCallbacks.push(onFulfilled)
    that.rejectedCallbacks.push(onRejected)
  }
  if (that.state === RESOLVED) {
    onFulfilled(that.value)
  }
  if (that.state === REJECTED) {
    onRejected(that.value)
  }
}
// 使用
new MyPromise((resolve, reject) => {
  setTimeout(() => {
    resolve(1)
  }, 0)
}).then(value => {
  console.log(value)
})
```
