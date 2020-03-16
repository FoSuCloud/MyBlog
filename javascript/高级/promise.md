## 例题1
```
			const pro=new Promise((res,rej)=>{
				console.log('promise1')
			})
			console.log('1',pro)
			// 最终结果是
			// promise1
			// 1
			// Promise{pending},因为此时的promise还未执行，处于pending状态
```
* `注意:new function(){}的时候其实就是执行了一次函数，promise同理也会执行`
* `而创建的promise实例没有被执行，所以处于pending状态`

## promise内部执行resolve只是改变状态，不会立即执行resolve对应的函数
```
const promise = newPromise((resolve, reject) => {
  console.log(1);
  resolve('success');// 改变promise的状态为resolved并且保存参数
  console.log(2);
});
// 第一轮宏任务结束后，执行微任务，发现promise状态为resolved,就执行该then内容
promise.then(() => {
  console.log(3);
});
console.log(4);
```
* `1,2,4,3`

## 如果promise不调用resolve/reject改变pending状态的话不会执行.then内容
```
			const pro=new Promise((res,rej)=>{
				console.log(1)
				console.log(2)
			})
			pro.then(()=>{
				console.log(3)
			})
			console.log(4)
```
* `打印 1 2 4，因为没有调用resolve/reject方法，所以promise依旧处于pending状态，也就不会执行.then()`

## 进一步了解状态
```
const promise1 = new Promise((resolve, reject) => {
  console.log('promise1')
  resolve('resolve1')
})
const promise2 = promise1.then(res => {
  console.log(res)
})
console.log('1', promise1);
console.log('2', promise2);
```
* `promise1`
* `1,promise1 {resolve:resolve1}  因为promise1.then是微任务，还未执行，所以先执行此处`
* `然后因为new内部执行了resolve并且传递了参数，所以pending状态也改变为relsoved状态`
* `2,promise2 {pending},此时`+注意:promise2此时是一个新的promise!+`promise1.then还没有执行，还是处于pending状态`
* `最后第一轮宏任务执行完毕，执行微任务promise1.then，输出 resolve1`

## Promise.then(微任务)与setTimeout(宏任务)结合
```
const promise = newPromise((resolve, reject) => {
  console.log(1);
  setTimeout(() => {
    console.log("timerStart");
    resolve("success");
    console.log("timerEnd");
  }, 0);
  console.log(2);
});
promise.then((res) => {
  console.log(res);
});
console.log(4);
```
* 首先输出1,2,4,`因为调用了new,也就执行promise函数了，但是promise的状态还是pending未改变`
* `所以promise.then()不会执行，因为状态还是pending,然后继续本轮宏任务，输出4`
* `仔上一轮宏任务中，setTimeout被放入宏任务队列，此时执行，输出 timerStart,timerEnd`
* `注意，调用resolve了，所以promise状态改为resolve了，微任务promise.then会执行，输出success`

## 多个定时器和多个promise
* `执行顺序根据任务队列中的位置决定，分辨第几轮宏任务，还有promise被放入哪轮宏任务`
```
var timer1=setTimeout(() => {
  console.log('timer1');
  Promise.resolve().then(() => {
    console.log('promise')
  })
}, 0)
var timer2=setTimeout(() => {
  console.log('timer2')
}, 0)
console.log('start')
```
* `1. 首先timer1这个setTimeout进入宏任务队列，然后timer2也进入，记住:队列是先进先出的`
* `2. 然后执行本轮宏任务输出start,扫描没发现微任务，然后进入下一轮宏任务`
* `3. 第二轮宏任务开始，把timer1放入进程，开始执行，输出timer1,然后promise是微任务`
* `4. 第二轮宏任务同步代码执行完毕，扫描微任务，发现promise状态是resolve,然后执行，输出promise`
* `5. 然后把timer2放入进程，开始第三轮宏任务，打印 timer2`

## 抛出错误后本轮任务不会执行了，但是下一轮宏任务会继续执行！
```
			const promise1 = new Promise((resolve, reject) => {
			  setTimeout(() => {
				resolve('success')
			  }, 1000)
			})
			const promise2 = promise1.then(() => {
			  throw new Error('error!!!')
			  console.log("抛出错误后本轮任务不会继续执行，但是下一轮宏任务会继续执行！")
			})
			console.log('promise1', promise1)
			console.log('promise2', promise2)
			setTimeout(() => {
			  console.log('promise1', promise1)
			  console.log('promise2', promise2)
			}, 2000)
```
* `1. 首先执行promise1的new promise,所以把第一个setTimeout放入任务队列`
* `2. 因为promise1的状态还是pending,所以promise2不执行`
* `3. 打印promise1,{promise1 pending}; promise2,{promise2 pending}`
* `4. 把第二个setTimeout放入宏任务队列`
* `5. 执行第一个setTimeout,所以promise1的状态改为resolved,然后扫描本轮微任务，执行promise1.then`
* `6. 由于抛出了错误，所以new Error之后的本轮其他任务都不会执行!`
* `7. 执行第三轮宏任务，此时promise1,promise2都是resolved状态了！打印promise1,promise1 reolvesd;promise2,promise2 reolvesd`

## promise的特性一:一旦promise的状态从pending改为其他，那么就不会再改变！
```
const promise = newPromise((resolve, reject) => {
  resolve("success1");
  reject("error");
  resolve("success2");
});
promise
.then(res => {
    console.log("then: ", res);
  }).catch(err => {
    console.log("catch: ", err);
  })
```
* `promise的状态改为resolve("success1")，所以打印"then: success1"`
* `之后虽然又设置reject,resolve,但是都不会生效的`

## promise特性二:无论catch在哪个then之后都可以捕获到错误
```
const promise = newPromise((resolve, reject) => {
  reject("error");
  resolve("success2");
});
promise
.then(res => {
    console.log("then: ", res);
  }).then(res => {
    console.log("then: ", res);
  }).catch(err => {
    console.log("catch: ", err);
  }).then(res => {
    console.log("then: ", res);
  })
```
* `打印"catch: error"`
* `此外还输出then:undefined,这是因为catch之后还有一个then,那么默认catch返回的是一个promise`
* `但是catch没有返回，所以就是undefined`

## promise特性三：链式调用（上一个then返回的值是下一个then的参数）
```
Promise.resolve(1)
  .then(res => {
    console.log(res);
    return 2;
  })
  .catch(err => {
    return 3;
  })
  .then(res => {
    console.log(res);
  });
```
* `输出1,2，因为resolve(1),所以第一个then打印1，然后返回2`
* `因为返回了2，然后第二个then接受了2，打印2`
* `注意:catch是用于捕获错误的，没有触发错误就不会触发，也就不会传递3给第二个then`
* `return 2其实就相当于resolve(2)`
* `如果把return 2;改为reject(2),那么就会触发catch,然后catch返回3，第二个then打印3`
```
Promise.reject(1)
  .then(res => {
    console.log(res);
    return2;
  })
  .catch(err => {
    console.log(err);
    return3
  })
  .then(res => {
    console.log(res);
  });
```
* `此时打印的是1 3，因为第一个then没有触发`
* 再看一个
```
Promise.resolve().then(() => {
  returnnewError('error!!!')
}).then(res => {
  console.log("then: ", res)
}).catch(err => {
  console.log("catch: ", err)
})
```
* `因为触发了resolve,所以promise的状态一直是promise{reolved:''}`
* `所以第一个then相当于resolve(new Error('error')),所以触发的是第二个then`
* `打印then:error!`

### promise特性四：只要promise的状态被改变了一次并且传了值，那么之后调用n次then都是得到同一个值
```
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('timer')
    resolve('success')
  }, 1000)
})
const start = Date.now();
promise.then(res => {
  console.log(res, Date.now() - start)
})
promise.then(res => {
  console.log(res, Date.now() - start)
})
```
* `最后输出timer,success,1001;success,1001`
* `两个then任务都是在同一个宏任务队列周期触发的，所以时间应该差不多`
* `即使调用第二次，promise的状态依旧是{resolved success}`

## promise特性五：不能返回自身，否则会陷入死循环
```
const promise = Promise.resolve().then(() => {
  return promise;
})
promise.catch(console.err)
```
* `陷入了死循环，直接报错`

## promise特性六：如果then/catch中不是函数，那么将会发生值穿透
* 值穿透就是上一层的返回值会绕过本层then/catch，成为下一层的返回值
* 这是因为本层then/catch属于基本数据类型/对象，`then/catch内部应该是函数！`
```
Promise.resolve(1)
  .then(2)
  .then(Promise.resolve(3))
  .then(console.log)
```
* `首先promise的状态改为resolved 1`
* `然后第1个then是基本数据类型，所以直接看下一层`
* `第二个then其实就是一个返回promise resolve状态的promise对象，也是对象，所以看下一层`
* `第三个then是一个console.log打印函数，所以可以执行，打印出1 `

## promise特性七：上一层发生错误/reject的时候，如果下一层有fail函数则优先于catch
```
Promise.reject('err!!!')
  .then((res) => {
    console.log('success', res)
  }, (err) => {
    console.log('error', err)
  }).catch(err => {
    console.log('catch', err)
  })
```
* `首先promise的状态改为reject err`
* `因为上一层是reject,然后下一层存在fail(then的第二个参数)，并且存在catch`
* `那么此时优先执行fail函数，也就打印error err!`
---
* `注意:如果是本层的then的resolve触发了error,那么本层的fail函数不会生效，只会调用catch`
```
Promise.resolve()
  .then(function success (res) {
    throw new Error('error!!!')
  }, function fail1 (err) {
    console.log('fail1', err)
  }).catch(function fail2 (err) {
    console.log('fail2', err)
  })
```
* `此时打印 fail2 error!`

## promise的finally
* [22](https://mp.weixin.qq.com/s/9Xk-HBQFaIEpyH8FqxBi6g)