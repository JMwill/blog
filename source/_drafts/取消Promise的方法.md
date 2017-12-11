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

### 配合 Generator 获得取消能力

```js
function runWithCancel(fn, ...args) {
  const gen = fn(...args)
  let cancelled, cancel;
  const promise = new Promise((resolve, reject) => {
    // 从我们的 fn 函数中返回定义的取消函数
    cancel = () => {
      cancelled = true
      reject({
        reason: 'cancelled'
      })
    }

    let value

    onFulfilled()

    function onFulfilled(res) {
      if (!cancelled) {
        let result
        try {
          result = gen.next(res)
        } catch (e) {
          return reject(e)
        }
        next(result)
        return null
      }
    }

    function onRejected(err) {
      var result
      try {
        result = gen.throw(err)
      } catch (e) {
        return reject(e);
      }
      next(result)
    }

    function next({
      done,
      value
    }) {
      if (done) {
        return resolve(value)
      }
      return value.then(onFulfilled, onRejected)
    }
  })

  return {
    promise,
    cancel
  }
}

// 使用
function* updateUser() {
  // 这里是假设函数返回的都是 Promise 对象
  // 否则需要额外的调整
  const data = yield fetchData()
  const userData = yield updateUserData(data)
  const userAddress = yield updateUserAddress(userData)
  const marketingData = yield updateMarketingData(userAddress)
  return marketingData
}

const { promise, cancel } = runWithCancel(updateUser)

cancel()
```

配合 Generator，能够使得 Promise 得以取消，而且可以通过 Generator 获得更多的拓展性，不过 Generator 本身的特性就需要更多的学习与理解才可以得心应手地应用。

### 配合 async / wait 使用

```js
async function updateUser(token) {
  let cancelled = false

  token.cancel = () => {
    cancelled = true
  }

  const data = await wrapWithCancel(fetchData)()
  const userData = await wrapWithCancel(updateUserData)(data)
  const userAddress = await wrapWithCancel(updateUserAddress)(userData)
  const marketingData = await wrapWithCancel(updateMarketingData)(userAddress)

  if (cancelled) {
    throw { reason: 'cancelled' }
  }

  function wrapWithCancel(fn) {
    return data => {
      if (!cancelled) {
        return fn(data)
      }
    }
  }
}

const token = {}
const promise = updateUser(token)

token.cancel()
```

因为 async 是隐式返回 Promise 对象，不能入侵式地修改，所以需要将取消方法赋值给外部的变量

> 摘录自：[http://blog.bloomca.me/2017/12/04/how-to-cancel-your-promise.html][1]

[1]: http://blog.bloomca.me/2017/12/04/how-to-cancel-your-promise.html
