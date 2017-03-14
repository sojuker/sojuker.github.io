> The Deferred object, introduced in jQuery 1.5, is a chainable utility object created by calling the jQuery.Deferred() method. It can register multiple callbacks into callback queues, invoke callback queues, and relay the success or failure state of any synchronous or asynchronous function.

## Callbacks

deferred 对象内部由两个 jQuery.Callbacks 实例组成，如下：

```
// fire,     add,       callbacksInstance,                   convert2State
[ "resolve", "done",    new jQuery.Callbacks("once memory"), "resolved" ],
[ "reject",  "fail",    new jQuery.Callbacks("once memory"), "rejected" ],
```

第一个队列用于存储 `deferred` 实例 `fulfill` 时的回调队列（Callbacks）。
队列的 `fire()` 方法通过 `deferred.resolve()` 间接调用。
队列的 `add()` 方法通过 `deferred.promise().done()` 间接调用。

第二个队列同理。

## Callbacks 队列的初始化状态

```javascript
list.add(function () {
    state = status;
}, tuples[i ^ 1][2].disable);
```

我们可以发现，deferred 对象的 doneList 已经存在两个回调函数，
第一个函数用于改变当前 deferred 对象的状态，
第二个函数禁用 failList，
这样，就实现了上面提到的 CommonJS Promises/A 理念，避免 deferred 对象的状态多次变化。

## API

- `deferred.resolve()`
- `deferred.reject()`
- `deferred.notify()`
- `promise.done()`
- `promise.fail()`
- `promise.progress()`
- `promise.then()`

调用 `deferred.promise()` ，返回值就是 promise 对象。

## 标准使用

利用函数的封装特性，在函数中创建 `deferred` 对象，并返回 `deferred.promise()`

```javascript
function createPromise(){
    var deferred = new jQuery.Deferred();
    // 函数体代码
    return deferred.promise();
}
```

## How to implement `promise.then`

```javascript
Promise.prototype.then = function(fulfilledCallback, rejectedCallback){
    var promise = this;
    return new Promise(function(resolve, reject){
        promise.done(function(result){
            // bypass
            if (!fulfilledCallback) {
                resolve(result);
                return;
            }

            var returned;
            try {
                returned = fulfilledCallback.call(null, result);

                // if the return value is a promise
                if (returned && typeof returned.then === "function") {
                    returned.then(function(_result){
                        resolve(_result);
                    }, function(_err){
                        reject(_err);
                    });
                } else {
                    resolve(returned);
                }
            } catch (error) {
                reject(error);
            }
        });

        // same as above
        promise.fail(function(err){});
    });
};

```

## how to Implement `Promise.all`

```javascript
Promise.all = function(){
    var promiseArr = arguments.slice();
    var remaining  = promiseArr.length;
    var resolveValues = [];

    return new Promise(function(resolve, reject){
        promiseArr.forEach(function(promise, index){
            promise.then(function(result){
                resolveValues[index] = result;
                remaining--;
                if (remaining === 0) {
                    resolve(resolveValues);
                }
            }, function(error){
                reject(error);
            });
        });
    });
};
```