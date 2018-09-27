---
title: promise
date: 2018-09-27 16:02:10
tags:
---
promise/‘prɔmis/ <br> n.诺言;指望<br>vt.允诺
大概意思就是这个一定会做完的,至于什么时候做完就再说吧
## 理解Promise
阮一峰在[《ECMAScript 6入门》](http://es6.ruanyifeng.com/)中是这样介绍`Promise`的

* `Promise` 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了`Promise`对象。
* 所谓`Promise`，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，`Promise`是一个对象，从它可以获取异步操作的消息。`Promise` 提供统一的 API，各种异步操作都可以用同样的方法进行处理。

<!-- more -->
`Promise`是用来处理异步操作的,以前我很肤浅的认为阻塞就是同步，非阻塞就是异步，现在发现我的理解是错的

我们得先了解什么是同步、异步、阻塞和非阻塞操作:
 * 同步和异步强调的是消息通信机制
 * 阻塞和非阻塞强调的是程序在等待调用结果（消息，返回值）时的状态

简单举几个例子
- 小明叫我写ppt一直盯着我写直到我写完 (同步+阻塞)
- 小明叫我写ppt,然后他去写代码了,还是不是看看我写完ppt没有(同步+非阻塞)
- 小明叫我写ppt还一直监督我不让我偷懒,我写完之后告诉我写完了不用监督我了(异步+阻塞)
- 小明叫我写ppt,然后他去写代码了,我告诉他我写完之前他都不用管我,等我写完再来检查(异步+非阻塞)

## Promise的特点
### 术语
- promise是一个包含了兼容promise规范then方法的对象或函数，
- thenable 是一个包含了then方法的对象或函数。
- value 是任何Javascript值。 (包括 undefined, thenable, promise等).
- exception 是由throw表达式抛出来的值。
- reason 是一个用于描述Promise被拒绝原因的值。

### 状态
`Promise`有3种状态,而且Promise对象的状态不受外界影响:
- pending（进行中）
- fulfilled（已成功）
- rejected（已失败）

只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。

### `then`方法
`Promise`通过链式调用`than`方法来改变他的状态,所以一个Promise必须提供一个`then`方法来获取其值或原因。

`promise.then(onFulfilled, onRejected)`
    1.其中`onFulfilled`和`onRejected`是成功和失败的回调函数,可以不填.
    2.他们只在`promise`状态改变后执行一次
    3.`then`必须返回一个`promise`

## ES6中用法
![img](http://pbyegcye6.bkt.clouddn.com/Promise.png)
Promise是一个构造函数，自己身上有all、reject、resolve这几个眼熟的方法，原型上有then、catch等同样很眼熟的方法。这么说用Promise new出来的对象肯定就有then、catch方法
```
var p = new Promise(function(resolve, reject){
    //做一些异步操作
    setTimeout(function(){
        console.log('执行完成');
        resolve('数据');
    }, 2000);
});
```
既然Promise是一个构造函数首先就先把对象new出来,这个构造函数接收`Function`作为参数且这个`Function`有2个参数,分别接收上提到`onFulfilled`和`onRejected`

### `catch`方法
`then`方法上面已经提到过,这里就不重复了.现在我们来讲讲`catch`方法
- 一看就知道是捕获异常,当执行 `resolve` 的回调（也就是上面 `then` 中的第一个参数）时，如果抛出异常了（代码出错了），那么也不会报错卡死 js，而是会进到这个 `catch` 方法中。
- 另一个特殊的用法是可以当做`than`方法的第二个参数来用,用来指定 `reject` 的回调,也就是术语中`reason`
###  `all`方法
Promise 的 all 方法提供了并行执行异步操作的能力，并且在所有异步操作执行完后才执行回调。
```
function a() {
  var p = new Promise(function (resolve, reject) {        //做一些异步操作
    setTimeout(function () {
      resolve('A');
    }, 1000);
  });
  return p;
}
function b() {
  var p = new Promise(function (resolve, reject) {        //做一些异步操作
    setTimeout(function () {
      resolve('B');
    }, 1000);
  });
  return p;
}
Promise
.all([a(), b()])
.then(function(results){
    console.log(results);
});
```
![img](http://pbyegcye6.bkt.clouddn.com/Promise2.png)
函数 a b 都是异步的,用`all()`方法让他们都执行完再一起回调,他们的resolve的参数组成数组作为回调函数的参数
### `race`方法
`race`方法和`all`方法调用的方式是一样的

```
function a() {
  var p = new Promise(function (resolve, reject) {        
    var res = getInformation1();//通过接口获取一些信息
    resolve(res);
  });
  return p;
}
function b() {
  var p = new Promise(function (resolve, reject) {        //做一些异步操作
    var res = getInformation2();//通过接口获取一些信息
    resolve(res);
  });
  return p;
}
Promise
.race([a(), b()])
.then(function(results){
    console.log(results);
});
```
和`all`一样,函数 a b都会执行,可是只要其中一个执行完,就会马上执行回调函数.值得注意的是其它没有执行完毕的异步操作仍然会继续执行，而不是停止。
值得注意的是:





一般我们用Promise的时候一般是包在一个函数中，在需要的时候去运行这个函数
```
function checkCouponCode () {
    let p = new Promise(function (resolve, reject) {
        _this.$refs.ruleForm.validateField('couponCode', err => {
            resolve(err)
        })
    })
    return p
}
```
下面是调用的方法
```
checkCouponCode().then(data => {
    if (data !== '') {
        callback(new Error('先输入或者选择优惠券编码'));
        this.showCouponTypeInfo = false;
    } else {
        if (value === '') {
            callback(new Error('请输入或选择优惠券批次号'));
        } else {
            // 验证优惠券
            ......
        }
    }
})
```
这是当年我写的一段垃圾代码,虽然能够执行但是可读性很差
```
function checkCouponCode () {
    let p = new Promise(function (resolve, reject) {
        _this.$refs.ruleForm.validateField('couponCode', err => {
            if (err === '') {
                resolve()
            } else {
                reject(err)
            }
        })
    })
    return p
}

checkCouponCode().then(() => {
  // 验证优惠券
}).catch(err => {
  // 判断错误的类型
})

```
这样改了后可读性就增加很多了
[Promises/A+规范](https://promisesaplus.com/)

