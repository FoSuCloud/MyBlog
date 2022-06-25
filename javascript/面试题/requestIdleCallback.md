## requestIdleCallback
* requestIdleCallBack和requestAnimationFrame有什么区别呢？
* requestAnimationFrame是每一帧都会执行，然而requestIdleCallback是在每一帧的最后，进行判断，如果有空闲时间则执行，没有就等待
* 也就是requestIdleCallback是使用浏览器的空闲时间来利用的

## 如果浏览器一直不空闲，requestIdleCallback就一直不执行？
* 不是，requestIdleCallback可以设置一个timeout来保证至少在这个时间内执行一次


## 兼容性
* 目前只有chrome,firefox支持这个api
* 针对兼容性，mdn给出了一个解决方案，在50ms内执行一次任务
```ts
if ((window as any).requestIdleCallback === undefined) {
  // On Safari, requestIdleCallback is not available, so we use replacement functions for `idleCallbacks`
  // See: https://developer.mozilla.org/en-US/docs/Web/API/Background_Tasks_API#falling_back_to_settimeout
  // eslint-disable-next-line @typescript-eslint/ban-types
  (window as any).requestIdleCallback = function (handler: Function) {
    let startTime = Date.now();
    return setTimeout(function () {
      handler({
        didTimeout: false,
        timeRemaining: function () {
          return Math.max(0, 50.0 - (Date.now() - startTime));
        }
      });
    }, 1);
  };

  (window as any).cancelIdleCallback = function (id: number) {
    clearTimeout(id);
  };
}
```
