## Promise实现

### Promise特点

+ 是构造函数，通过`new Promise`创建实例，接受一个函数作为参数。
+ Promise有三种状态值，分别为：`Pending,Fulfilled,Rejected`，实例化Promise时接受的函数有两个参数，分别为`resolve`和`reject`，默认情况下Promise的状态为`Pending`，当调用`resolve`时，Promise的状态变为`Fulfilled`，当调用`reject`时，Promise的状态变为`Rejected`。
+ Promise能够进行链式数据传递，通过`then`接受两个函数作为参数，第一个函数接收`resolve`出来的值，第二个函数接收`reject`出来的值，`then`的执行是异步的，会返回一个Promise，其中携带处理好的值，后续可以继续接`then`，形成链式数据传递。

### 代码展示

#### 1. Promise构造函数，实现初始状态

```
class MyPromise{
  constructor(handler){
    if(typeof handler !== 'function'){
      throw new Error('the handler should be a function')
      return
    }

    // 初始状态
    this._value = undefined
    this._status = 'pending'

    // 创建实例时传入的函数
    handler(_resolve, _reject)
  }
}
```

用类的写法创建一个Promise构造函数，设置初始值为`undefined`，初始状态为`pending`。

#### 2. 实现resolve和reject

```
class MyPromise{
  constructor(handler){
    if(typeof handler !== 'function'){
      throw new Error('the handler should be a function')
      return
    }

    this._value = undefined
    this._status = 'pending'

    handler(this._resolve.bind(this), this._reject.bind(this))
  }

  _resolve(value){
    // 设置新状态
    this._value = value
    this._status = 'fulfilled'
  }

  _reject(error){
    this._value = error
    this._status = 'rejected'
  }
}
```
在类中创建`_resolve`和`reject`两个函数，当调用这两个函数的时候会改变Promise的状态和挂载的值

#### 3. 实现then
```
class MyPromise{
  constructor(handler){
    if(typeof handler !== 'function'){
      throw new Error('the handler should be a function')
      return
    }

    this._value = undefined
    this._status = 'pending'

    // then回调函数数组
    this.onFulfilledArr = []
    this.onRejectedArr = []

    handler(this._resolve.bind(this), this._reject.bind(this))
  }

  _resolve(value){
    // 设置新状态
    this._value = value
    this._status = 'fulfilled'
    let callback
    while(callback = this.onFulfilledArr.shift()){
      callback && callback()
    }
  }

  _reject(error){
    this._value = error
    this._status = 'rejected'
    let callback
    while(callback = this.onRejectedArr.shift()){
      callback && callback()
    }
  }

  then(onFulfilled, onRejected){
    // 为避免_resolve或_reject可能被放到setTimeout或setInterval中而导致状态不改变，故将回调函数放到回调函数数组，等状态改变的时候再执行
    switch(this._status){
      case 'pending':
        this.onFulfilledArr.push(onFulfilled)
        this.onRejectedArr.push(onRejected)
        break
      case 'fulfilled':
        this._value = onFulfilled(this._value)
        break
      case 'rejected':
        this._value = onRejected(this._value)
        break
    }
  }
}
```

#### 4. 实现then异步执行
```
  _resolve(value){
    // 设置新状态
    this._value = value
    this._status = 'fulfilled'
    const run = () => {
      let callback
      while(callback = this.onFulfilledArr.shift()){
        callback && callback(value)
      }
    }
    let ob = new MutationObserver(run)
    ob.observe(document.body, {
      attributes: true
    })
    document.body.setAttribute('cb', Math.random())
  }

  _reject(error){
    this._value = error
    this._status = 'rejected'
    const run = () => {
      let callback
      while(callback = this.onRejectedArr.shift()){
        callback && callback(error)
      }
    }
    let ob = new MutationObserver(run)
    ob.observe(document.body, {
      attributes: true
    })
    document.body.setAttribute('cb', Math.random())
  }

  then(onFulfilled, onRejected){
      this.onFulfilledArr.push(onFulfilled)
      this.onRejectedArr.push(onRejected)
  }
```
改造`_resolve,_reject和then`，利用MutationObserver订阅器来执行then的回调函数，实现异步执行，因为需要异步执行回调函数，所以`then`中每次都需要将回调函数放到回调函数数组中。

#### 5. then返回Promise并实现链式数据传递
```
  then(onFulfilled, onRejected){
    return new Promise((resolve, reject) => {
      this.onFulfilledArr.push(val => {
        let res = onFulfilled && onFulfilled(val)
        if(res instanceof myPromise){
          return res.then(resolve)
        }
        resolve(res)
      })
      this.onRejectedArr.push(err => {
        onRejected && onRejected(err)
        reject(err)
      })
    })
  }
```