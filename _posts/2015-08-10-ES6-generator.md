## generator function

ES6 引入的新概念和对应语法，`generator function` 是指其返回值为 `generator` 的一类函。
其形式与一般函数不同，必须在 `function` 关键字后直接跟一个 `*` 来表示该函数是一个 `generator function`。
`generator function` 函数内部支持 `yield` 操作符。如下

```javascript
function* () {
    var res = [];
    var a = Promise.resolve(1);
    var b = Promise.resolve(2);
    res[1] = yield a;
    res[2] = yield b;

    console.log(res);
}
```

## yield

> [rv] = yield [expression];
> - The yield keyword is used to pause and resume a generator function
> - `expression`
> Defines the value to return from the generator function via the iterator protocol.
> If omitted, undefined is returned instead.
> - `rv`
> Lets the generator capture the value of expression for use the next time it resumes execution.

yield 操作符可以分三个部分

- 右侧表达式的值，同时暂停 `generator function` 的执行
- 左侧应用 `yield [expression]` 后的返回值，同时恢复 `generator function` 的执行
- 从 expression --> rv 的数据转换过程，以其右侧表达式值作为输入，以左侧返回值为输出

## generator

> 调用 `generator function` 函数，程序将返回一个 `generator`。

`generator` 遵循 `iterator protocal`，该协议定义了三个方法

- `next()`
- `throw()`
- `return()`

`generator` 通过调用 `next()` 来实现 `generator` 的遍历，
最后得到 `generator function` 的返回值或者一个错误。
其中 `yield` 操作符划分了遍历的每个阶段。
当函数执行遇到 `yield` 操作符，遍历的一个阶段结束，暂停函数执行，并返回 yield 右侧表达式的值，
调用 `next()` 恢复函数执行，并以输入值作为对应 `yield` 表达式的值。

由开发者对返回值进行数据处理（可同步，可异步），得到新数据 `handledData`，然后调用 `next(handledData)`，从原有中断位置继续执行遍历，并将处理后的数据作为 `yield` 操作的返回值。如此反复，直到 `generator` 完成遍历，即 `generator function` 函数执行完成。

## `next()`

`next()` 函数返回一个对象，该对象有且仅有两个属性

- `value`，表示对应 `yield` 操作符处其右侧表达式的值
- `done`，表示遍历器是否已完成遍历，`boolean` 值

## 4. 实际应用

我们期望应该如下：

```javascript
autoRun(function* () {
    var res = [];
    var a = Promise.resolve(1);
    var b = Promise.resolve(2);
    res[1] = yield a;
    res[2] = yield b;

    console.log(res);
});
```

## 5. thunk

从我们最熟悉的API说起。

> `fs.readFile(filename[, options], callback)`

thunk 是一个约定。thunk 是指一个函数，其有且仅有一个入参，且该参数是一个函数。
该函数的第一入参是 `error` 对象，第二入参是一个正常数据，与上面 `fs.readFile` API 中的 `callback` 相同。

以 `fs.readFile` 为例，将其改成 thunk 函数：

```javascript
var readFileThunk = fs.readFile.bind(fs, 'a.text', 'utf8');
readFileThunk(function callback(err, fileContent) {
    // doSomething;
});
```

如果约定， `yield` 操作符右侧表达式的值都是一个 thunk。那么，也就是说，`next()` 返回值的 `value` 属性都是一个 thunk。

```javascript
function autoRun(gen) {
    var iterator = gen();

    function next(err, data) {
        var result = iterator.next(data);

        if (result.done) {
            return;
        } else {
            result.value(next);
        }
    }

    next(null, null);
}

autoRun(gen);
```

## 6. 异步编程

```
var fs = require('fs');
var thunkify = require('thunkify');
var co = require('co');
var readFile = thunkify(fs.readFile);

var gen = function* () {
    var r1 = yield readFile('/etc/fstab');
    console.log(r1.toString());
    var r2 = yield readFile('/etc/shells');
    console.log(r2.toString());
};

co(gen);
```