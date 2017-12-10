title: 取消Promise的方法
tags:
  - JavaScript
  - Promise
  - Code
---

## 普通使用

一般情况下，在异步调用时想要让调用串行执行，目前都会使用 Promise 来组织调用：

```js
function updateUser() {
  return fetchData()
    .then(updateUserData)
    .then(updateUserAddress)
    .then(updateMarketingData)
}
```

而 Promise 本身并不支持取消调用，在某些场合下，当需要取消调用时，则不得不借助其他的方式实现

### 借用库的力量（Bluebird）

```js
import Promise from 'bluebird'

function updateUser() {
  return new Promise((resolve, reject, onCancel) => {
    let cancelled = false

    // 使用 Bluebird 提供的标准以外的取消方式
    // http://bluebirdjs.com/docs/api/promise.config.html

    onCancel(() => {
      cancelled = true
      reject({ reason: 'cancelled' })
    })

    return fetchData()
      .then(wrapWithCancel(updateUserData))
      .then(wrapWithCancel(updateUserAddress))
      .then(wrapWithCancel(updateMarketingData))
      .then(resolve)
      .then(reject)

    function wrapWithCancel(fn) {
      return (data) => {
        if (!cancelled) {
          return fn(data)
        }
      }
    }
  })
}

const promise = updateUser()
promise.cancel()
```

### 纯 Promise 实现

```js
function updateUser() {
  let resolve, reject, cancelled
  const promise = new Promise((resolveFromPromise, rejectFromPromise) => {
    resolve = resolveFromPromise
    reject = rejectFromPromise
  })

  fetchData()
    .then(wrapWithCancel(updateUserData))
    .then(wrapWithCancel(updateUserAddress))
    .then(wrapWithCancel(updateMarketingData))
    .then(resolve)
    .then(reject)

  return {
    promise,
    cancel: () => {
      cancelled: true
      reject({ reason: 'cancelled' })
    }
  }

  function wrapWithCancel(fn) {
    return (data) => {
      if (!cancelled) {
        return fn(data)
      }
    }
  }
}

const { promise, cancel } = updateUser()

cancel()
```

相比于 Bluebird 实现上自带有 cancel 方法，使用原生实现的话则需要自己来返回一个取消方法。这种方式实际上相当于为 Promise 打了一个补丁。对于少量需要取消的情况而言这种方式快捷简单