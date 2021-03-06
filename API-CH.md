#API参考

**注意**: 这个文档仅对bluebird 2.x版本有效，并非针对1.x版本的 - [这里是1.x的文档](https://github.com/petkaantonov/bluebird/blob/ca0f2f8b204df7015eb1f7b75ba8195a81bf0d7e/API.md)

- [核心](#core)
    - [`new Promise(Function<Function resolve, Function reject> resolver)`](#new-promisefunctionfunction-resolve-function-reject-resolver---promise)
    - [`.then([Function fulfilledHandler] [, Function rejectedHandler ])`](#thenfunction-fulfilledhandler--function-rejectedhandler----promise)
    - [`.spread([Function fulfilledHandler] [, Function rejectedHandler ])`](#spreadfunction-fulfilledhandler--function-rejectedhandler----promise)
    - [`.catch(Function handler)`](#catchfunction-handler---promise)
    - [`.catch([Function ErrorClass|Function predicate...], Function handler)`](#catchfunction-errorclassfunction-predicate-function-handler---promise)
    - [`.error( [rejectedHandler] )`](#error-rejectedhandler----promise)
    - [`.finally(Function handler)`](#finallyfunction-handler---promise)
    - [`.bind(dynamic thisArg)`](#binddynamic-thisarg---promise)
    - [`Promise.join(Promise|Thenable|value promises..., Function handler)`](#promisejoinpromisethenablevalue-promises-function-handler---promise)
    - [`Promise.try(Function fn [, Array<dynamic>|dynamic arguments] [, dynamic ctx] )`](#promisetryfunction-fn--arraydynamicdynamic-arguments--dynamic-ctx----promise)
    - [`Promise.method(Function fn)`](#promisemethodfunction-fn---function)
    - [`Promise.resolve(dynamic value)`](#promiseresolvedynamic-value---promise)
    - [`Promise.reject(dynamic reason)`](#promiserejectdynamic-reason---promise)
    - [`Promise.bind(dynamic thisArg)`](#promisebinddynamic-thisarg---promise)
- [同步检查](#synchronous-inspection)
    - [`.isFulfilled()`](#isfulfilled---boolean)
    - [`.isRejected()`](#isrejected---boolean)
    - [`.isPending()`](#ispending---boolean)
    - [`.value()`](#value---dynamic)
    - [`.reason()`](#reason---dynamic)
- [集合](#collections)
    - [`.all()`](#all---promise)
    - [`.props()`](#props---promise)
    - [`.settle()`](#settle---promise)
    - [`.any()`](#any---promise)
    - [`.race()`](#race---promise)
    - [`.some(int count)`](#someint-count---promise)
    - [`.map(Function mapper [, Object options])`](#mapfunction-mapper--object-options---promise)
        - [Option: `concurrency`](#option-concurrency)
    - [`.reduce(Function reducer [, dynamic initialValue])`](#reducefunction-reducer--dynamic-initialvalue---promise)
    - [`.filter(Function filterer [, Object options])`](#filterfunction-filterer--object-options---promise)
        - [Option: `concurrency`](#option-concurrency)
    - [`.each(Function iterator)`](#eachfunction-iterator---promise)
- [资源管理](#resource-management)
    - [`Promise.using(Promise|Disposer promise, Promise|Disposer promise ..., Function handler)`](#promiseusingpromisedisposer-promise-promisedisposer-promise--function-handler---promise)
    - [`.disposer(Function disposer)`](#disposerfunction-disposer---disposer)
- [函数Promise化](#promisification)
    - [`Promise.promisify(Function nodeFunction [, dynamic receiver])`](#promisepromisifyfunction-nodefunction--dynamic-receiver---function)
    - [`Promise.promisifyAll(Object target [, Object options])`](#promisepromisifyallobject-target--object-options---object)
        - [Option: `suffix`](#option-suffix)
        - [Option: `filter`](#option-filter)
        - [Option: `promisifier`](#option-promisifier)
    - [`Promise.fromNode(Function resolver)`](#promisefromnodefunction-resolver---promise)
    - [`.nodeify([Function callback] [, Object options])`](#nodeifyfunction-callback--object-options---promise)
        - [Option: `spread`](#option-spread)
- [计时器](#timers)
    - [`.delay(int ms)`](#delayint-ms---promise)
    - [`.timeout(int ms [, String message])`](#timeoutint-ms--string-message---promise)
        - [`Promise.delay([dynamic value], int ms)`](#promisedelaydynamic-value-int-ms---promise)
- [取消](#cancellation)
    - [`.cancellable()`](#cancellable---promise)
    - [`.uncancellable()`](#uncancellable---promise)
    - [`.cancel([Error reason])`](#cancelerror-reason---promise)
    - [`.isCancellable()`](#iscancellable---boolean)
- [Generators](#generators)
    - [`Promise.coroutine(GeneratorFunction generatorFunction)`](#promisecoroutinegeneratorfunction-generatorfunction---function)
    - [`Promise.coroutine.addYieldHandler(function handler)`](#promisecoroutineaddyieldhandlerfunction-handler---void)
- [Utility](#utility)
    - [`.tap(Function handler)`](#tapfunction-handler---promise)
    - [`.call(String propertyName [, dynamic arg...])`](#callstring-propertyname--dynamic-arg---promise)
    - [`.get(String propertyName|int index)`](#getstring-propertynameint-index---promise)
    - [`.return(dynamic value)`](#returndynamic-value---promise)
    - [`.throw(dynamic reason)`](#throwdynamic-reason---promise)
    - [`Promise.noConflict()`](#promisenoconflict---object)
    - [`Promise.setScheduler(Function scheduler)`](#promisesetschedulerfunction-scheduler---void)
    - [`.reflect()`](#reflect---promisepromiseinspection)
- [Built-in error types](#built-in-error-types)
    - [`OperationalError()`](#operationalerror)
    - [`TimeoutError()`](#timeouterror)
    - [`CancellationError()`](#cancellationerror)
    - [`AggregateError()`](#aggregateerror)
- [Error management configuration](#error-management-configuration)
    - [Global rejection events](#global-rejection-events)
    - [`Promise.onPossiblyUnhandledRejection(Function handler)`](#promiseonpossiblyunhandledrejectionfunction-handler---undefined)
    - [`Promise.onUnhandledRejectionHandled(Function handler)`](#promiseonunhandledrejectionhandledfunction-handler---undefined)
    - [`Promise.longStackTraces()`](#promiselongstacktraces---void)
    - [`.done([Function fulfilledHandler] [, Function rejectedHandler ])`](#donefunction-fulfilledhandler--function-rejectedhandler----void)
- [Progression migration](#progression-migration)
- [Deferred migration](#deferred-migration)

注意在API中，每个Promise方法都有一个静态的对偶函数。例如，`Promise.map(arr, fn)`和`Promise.resolve(arr).map(fn)`是等效的。
Note that every instance promise method in the API has a static counterpart. For example `Promise.map(arr, fn)` is the same as calling `Promise.resolve(arr).map(fn)`.

##核心

`Promise`实例的核心方法以及Promise类的静态方法

#####`new Promise(Function<Function resolve, Function reject> resolver)` -> `Promise`


创建一个新的promise对象。传入的函数将会收到两个类型为Function的参数`resolve`和`reject`，当promise对象内部的状态改变时，这两个函数可以被定义用于回调，以封装对象的状态。

Create a new promise. The passed in function will receive functions `resolve` and `reject` as its arguments which can be called to seal the fate of the created promise.


附：在NodeJS中，你不一定需要亲自去创建promise对象，具体可以参考[promisification](#promisification)
_Note: In Node.JS it is **very unlikely** that you will ever need to create promises yourself, see [promisification](#promisification)_

例子：
Example:

```js
function ajaxGetAsync(url) {
    return new Promise(function (resolve, reject) {
        var xhr = new XMLHttpRequest;
        xhr.addEventListener("error", reject);
        xhr.addEventListener("load", resolve);
        xhr.open("GET", url);
        xhr.send(null);
    });
}
```

如果传入了一个promise对象给`resolve`函数，那么现有的promise对象将会跟随这个promise对象的状态。
If you pass a promise object to the `resolve` function, the created promise will follow the state of that promise.

<hr>


当你无法直接开始执行调用链时，为了确保函数能够绝对严格地返回promise对象，你可以在调用链的开头声明一个返回了`new Promise`的函数
To make sure a function that returns a promise is following the implicit but critically important contract of promises, you can start a function with `new Promise` if you cannot start a chain immediately:

```js
function getConnection(urlString) {
    return new Promise(function(resolve) {
        //Without new Promise, this throwing will throw an actual exception
        var params = parse(urlString);
        resolve(getAdapter(params).getConnection());
    });
}
```

上面的例子中，`getConnection()`是一个返回promise对象的函数，但是不会产生异步错误。具体请看[`Promise.try`](#promisetryfunction-fn--arraydynamicdynamic-arguments--dynamic-ctx----promise) 以及 [`Promise.method`](#promisemethodfunction-fn---function)

The above ensures `getConnection()` fulfills the contract of a promise-returning function of never throwing a synchronous exception. Also see [`Promise.try`](#promisetryfunction-fn--arraydynamicdynamic-arguments--dynamic-ctx----promise) and [`Promise.method`](#promisemethodfunction-fn---function)


resolve函数将会被异步地回调（下面的例子只是为了作为例子，不适合实际使用）：

The resolver is called synchronously (the following is for documentation purposes and not idiomatic code):

```js
function getPromiseResolveFn() {
    var res;
    new Promise(function (resolve) {
        res = resolve;
    });
    // res is guaranteed to be set
    return res;
}
```


<hr>

#####`.then([Function fulfilledHandler] [, Function rejectedHandler ])` -> `Promise`


[Promises/A+ `.then()`](http://promises-aplus.github.io/promises-spec/).从当前的promise对象中返回一个新的promise对象。这个新的promise对象将会根据当前promise对象的终态（拒绝rejected或者解决resolved）调用不同的方法`fulfilledHandler`, `rejectedHandler`

[Promises/A+ `.then()`](http://promises-aplus.github.io/promises-spec/). Returns a new promise chained from this promise. The new promise will be rejected or resolved depending on the passed `fulfilledHandler`, `rejectedHandler` and the state of this promise.

例子：
Example:

```js
promptAsync("Which url to visit?").then(function(url) {
    return ajaxGetAsync(url);
}).then(function(contents) {
    alertAsync("The contents were: " + contents);
}).catch(function(e) {
    alertAsync("Exception " + e);
});
```

<hr>

#####`.spread([Function fulfilledHandler] [, Function rejectedHandler ])` -> `Promise`

与`.then`方法类似，但是上一个回调函数的结果值或者抛出的异常_必须_是一个数组，这个数组的成员将会依次作为参数传入
Like calling `.then`, but the fulfillment value or rejection reason _must be_ an array, which is flattened to the formal parameters of the handlers.

```js
Promise.delay(500).then(function() {
   return [fs.readFileAsync("file1.txt"),
           fs.readFileAsync("file2.txt")] ;
}).spread(function(file1text, file2text) {
    if (file1text !== file2text) {
        console.log("files are equal");
    }
    else {
        console.log("files are not equal");
    }
});
```

如果你想整合多个不相干但是同时进行的promise，请使用[`Promise.join()`](#promisejoinpromisethenablevalue-promises-function-handler---promise)


If you want to coordinate several discrete concurrent promises, use [`Promise.join()`](#promisejoinpromisethenablevalue-promises-function-handler---promise)

<hr>

#####`.catch(Function handler)` -> `Promise`


这是一个可以捕获所有异常的句柄，是`.then(null, handler)`的缩略写法。从`.then`调用链中产生的任何异常都会被传送到最近的`.catch`句柄中。

This is a catch-all exception handler, shortcut for calling `.then(null, handler)` on this promise. Any exception happening in a `.then`-chain will propagate to nearest `.catch` handler.

*在一些稍早的ECMAScript版本中，为了兼容性，请使用`.caught()`来替代`.catch()`*
*For compatibility with earlier ECMAScript version, an alias `.caught()` is provided for `.catch()`.*

<hr>

#####`.catch([Function ErrorClass|Function predicate...], Function handler)` -> `Promise`

`.catch`的扩展用法，类似Java或者C#的异常从句。你可以指定一些数字或者异常对象的构造器函数作为捕获句柄，而不是手动使用`instanceof` 或者 `.name === "SomeError"`。当捕获句柄是第一个与抛出的异常相匹配时，这个句柄将会被调用。

This extends `.catch` to work more like catch-clauses in languages like Java or C#. Instead of manually checking `instanceof` or `.name === "SomeError"`, you may specify a number of error constructors which are eligible for this catch handler. The catch handler that is first met that has eligible constructors specified, is the one that will be called.

例子:
Example:

```js
somePromise.then(function() {
    return a.b.c.d();
}).catch(TypeError, function(e) {
    //如果a被定义，而后续调用出错，调用链将会在这里终止，因为捕获了一个TypeError
    //If a is defined, will end up here because
    //it is a type error to reference property of undefined
}).catch(ReferenceError, function(e) {
    //如果a没有被定义，将会在这里终止，因为捕获了一个ReferenceError
    //Will end up here if a wasn't defined at all
}).catch(function(e) {
    //捕获余下的错误，这些错误既不是TypeError也不是ReferenceError
    //Generic catch-the rest, error wasn't TypeError nor
    //ReferenceError
});
```
也可以同时使用多个捕获句柄：
You may also add multiple filters for a catch handler:

```js
somePromise.then(function() {
    return a.b.c.d();
}).catch(TypeError, ReferenceError, function(e) {
    //程序自身出错将会停止在这里
}).catch(NetworkError, TimeoutError, function(e) {
    //网络错误将会停止在这里
}).catch(function(e) {
    //捕获剩下的其它错误
});
```

为了能使传入的异常参数的类型被判断为error，你需要让异常构造器的`.prototype`属性为`instanceof Error`
For a parameter to be considered a type of error that you want to filter, you need the constructor to have its `.prototype` property be `instanceof Error`.

下面是一个最简单的例子：
Such a constructor can be minimally created like so:

```js
function MyCustomError() {}
MyCustomError.prototype = Object.create(Error.prototype);
```

这样使用：
Using it:

```js
Promise.resolve().then(function() {
    throw new MyCustomError();
}).catch(MyCustomError, function(e) {
    //will end up here now
});
```

如果你想跟踪堆栈轨迹，或者让输出的字符串更简洁，应该像下面这样做：
However if you  want stack traces and cleaner string output, then you should do:

*在Node.js或者其他V8环境下，是支持使用`Error.captureStackTrace`的*

*in Node.js and other V8 environments, with support for `Error.captureStackTrace`*

```js
function MyCustomError(message) {
    this.message = message;
    this.name = "MyCustomError";
    Error.captureStackTrace(this, MyCustomError);
}
MyCustomError.prototype = Object.create(Error.prototype);
MyCustomError.prototype.constructor = MyCustomError;
```

使用CoffeeScript的`class`也可以达到同样的效果：
Using CoffeeScript's `class` for the same:

```coffee
class MyCustomError extends Error
  constructor: (@message) ->
    @name = "MyCustomError"
    Error.captureStackTrace(this, MyCustomError)
```

这个方法同样支持断言，如果传入的是一个判别函数，而不是异常的构造器，那么异常将会以参数的形式传入判别函数中，判别函数的返回值将会决定这个捕获句柄是否被调用。

This method also supports predicate-based filters. If you pass a
predicate function instead of an error constructor, the predicate will receive
the error as an argument. The return result of the predicate will be used
determine whether the error handler should be called.


Predicates should allow for very fine grained control over caught errors:
pattern matching, error-type sets with set operations and many other techniques
can be implemented on top of them.

下面是一个使用了断言来捕获异常的例子：
Example of using a predicate-based filter:

```js
var Promise = require("bluebird");
var request = Promise.promisify(require("request"));

function ClientError(e) {
    return e.code >= 400 && e.code < 500;
}

request("http://www.google.com").then(function(contents) {
    console.log(contents);
}).catch(ClientError, function(e) {
   //A client error like 400 Bad Request happened
});
```
*在一些稍早的ECMAScript版本中，为了兼容性，请使用`.caught()`来替代`.catch()`*
*For compatibility with earlier ECMAScript version, an alias `.caught()` is provided for `.catch()`.*

<hr>

#####`.error( [rejectedHandler] )` -> `Promise`

类似[`.catch`](#catchfunction-handler---promise)，但是它只捕获[operational errors](#operationalerror)，而不是捕获某一特定类型的异常
Like [`.catch`](#catchfunction-handler---promise) but instead of catching all types of exceptions, it only catches [operational errors](#operationalerror)

*注意，"errors"是一个类型为 Error 的对象，不是 string、numbers 或者其他类型，具体可以看这里：[a string is not an error](http://www.devthought.com/2011/12/22/a-string-is-not-an-error/)*
*Note, "errors" mean errors, as in objects that are `instanceof Error` - not strings, numbers and so on. See [a string is not an error](http://www.devthought.com/2011/12/22/a-string-is-not-an-error/).*

它与下面这种[`.catch`](#catchfunction-errorclassfunction-predicate-function-handler---promise)的使用方法是等效的：
It is equivalent to the following [`.catch`](#catchfunction-errorclassfunction-predicate-function-handler---promise) pattern:

```js
//假设 OperationalError 在全局中产生
function isOperationalError(e) {
    if (e == null) return false;
    return (e instanceof OperationalError) || (e.isOperational === true);
}

// 这种使用方法：
.catch(isOperationalError, function(e) {
    // ...
})

// 等效于下面：
.error(function(e) {
    // ...
});
```
例如，当一个被promise化的函数因为运行中的某些异常被终止执行，将会触发`.error()`。但是如果这个异常是被 **throws** 出来的，只有`.catch`才能捕获。
For example, if a promisified function errbacks the node-style callback with an error, that could be caught with `.error()`. However if the node-style callback **throws** an error, only `.catch` would catch that.

下面这个例子中，可以单独处理JSON产生的`SyntaxError`以及来自`fs`的文件系统异常，同时让其它未捕获的异常继续冒泡。
In the following example you might want to handle just the `SyntaxError` from JSON.parse and Filesystem errors from `fs` but let programmer errors bubble as unhandled rejections:

```js
var fs = Promise.promisifyAll(require("fs"));

fs.readFileAsync("myfile.json").then(JSON.parse).then(function (json) {
    console.log("Successful json")
}).catch(SyntaxError, function (e) {
    console.error("file contains invalid json");
}).error(function (e) {
    console.error("unable to read file, because: ", e.message);
});
```

因为现在不存在能捕获全部异常的句柄，所以如果你不小心输入了`console.lag`（导致产生了一个意料之外的异常），你将会看到：
Now, because there is no catch-all handler, if you typed `console.lag` (causes an error you don't expect), you will see:

```
Possibly unhandled TypeError: Object #<Console> has no method 'lag'
    at application.js:8:13
From previous event:
    at Object.<anonymous> (application.js:7:4)
    at Module._compile (module.js:449:26)
    at Object.Module._extensions..js (module.js:467:10)
    at Module.load (module.js:349:32)
    at Function.Module._load (module.js:305:12)
    at Function.Module.runMain (module.js:490:10)
    at startup (node.js:121:16)
    at node.js:761:3
```

*（如果你不想得到上面的东西，你需要使用[long stack traces](#promiselongstacktraces---void) ）*
*( If you don't get the above - you need to enable [long stack traces](#promiselongstacktraces---void) )*

如果文件中有不规范的JSON：
And if the file contains invalid JSON:

```
file contains invalid json
```

如果`fs`模块产生了异常，比如文件未找到：
And if the `fs` module causes an error like file not found:

```
unable to read file, because:  ENOENT, open 'not_there.txt'
```

<hr>

#####`.finally(Function handler)` -> `Promise`

传入一个句柄，无论promise调用链的情况如何，这个句柄都会在最后被调用。 `.finally()` 拥有特殊的语义，最后的返回值不能在句柄中被改变。
Pass a handler that will be called regardless of this promise's fate. Returns a new promise chained from this promise. There are special semantics for `.finally()` in that the final value cannot be modified from the handler.

*注意：使用 `.finally()` 做资源管理不是一个好的方法，具体请看 [resource management](#resource-management)*
*Note: using `.finally()` for resource management is not a good idea, see [resource management](#resource-management)*

考虑下面这种情况：
Consider the example:

```js
function anyway() {
    $("#ajax-loader-animation").hide();
}

function ajaxGetAsync(url) {
    return new Promise(function (resolve, reject) {
        var xhr = new XMLHttpRequest;
        xhr.addEventListener("error", reject);
        xhr.addEventListener("load", resolve);
        xhr.open("GET", url);
        xhr.send(null);
    }).then(anyway, anyway);
}
```

这个例子中，将不会产生预期的效果，因为 `then` 句柄实际上吞噬了异常，并且返回 `undefined` 给后续的调用链。
This example doesn't work as intended because the `then` handler actually swallows the exception and returns `undefined` for any further chainers.

解决方法就是使用 `.finally` ：
The situation can be fixed with `.finally`:

```js
function ajaxGetAsync(url) {
    return new Promise(function (resolve, reject) {
        var xhr = new XMLHttpRequest;
        xhr.addEventListener("error", reject);
        xhr.addEventListener("load", resolve);
        xhr.open("GET", url);
        xhr.send(null);
    }).finally(function() {
        $("#ajax-loader-animation").hide();
    });
}
```
现在动画在任何情况下都可以被隐藏，但是前面的异常或者返回值将会自动跳过finally，传输给下一个调用单元。这样更加符合同步情况下的 `finally` 关键字。
Now the animation is hidden but an exception or the actual return value will automatically skip the finally and propagate to further chainers. This is more in line with the synchronous `finally` keyword.

*为了兼容更早版本的ECMAscript，可以使用 `.lastly()` 来替代 `.finally()`*
*For compatibility with earlier ECMAScript version, an alias `.lastly()` is provided for `.finally()`.*

<hr>

#####`.bind(dynamic thisArg)` -> `Promise`

创建一个promise对象，这个对象与给定的 `thisArg` 绑定，对象内部的 `this` 将会指向被绑定的值。另外，由这个被绑定对象所产生的promise对象也会被绑定到 `thisArg`。
Create a promise that follows this promise, but is bound to the given `thisArg` value. A bound promise will call its handlers with the bound value set to `this`. Additionally promises derived from a bound promise will also be bound promises with the same `thisArg` binding as the original promise.

如果`thisArg`是一个promise对象或者可以异步调用then方法，那么在它的最终结果值产生之前，线程一直会被阻塞，直到获得执行结果。如果`thisArg`
If `thisArg` is a promise or thenable, its resolution will be awaited for and the bound value will be the promise's fulfillment value. If `thisArg` rejects
then the returned promise is rejected with the `thisArg's` rejection reason. Note that this means you cannot use `this` without checking inside catch handlers for promises that bind to promise because in case of rejection of `thisArg`, `this` will be `undefined`.

<hr>

由于没有箭头函数提供的词法`this`，在编写面向对象代码的时候，异步流程和同步流程之间的通信将会变得很麻烦。`.bind()`可以改善这种情况。
Without arrow functions that provide lexical `this`, the correspondence between async and sync code breaks down when writing object-oriented code. `.bind()` alleviates this.

参考：
Consider:

```js
MyClass.prototype.method = function() {
    try {
        var contents = fs.readFileSync(this.file);
        var url = urlParse(contents);
        var result = this.httpGetSync(url);
        var refined = this.refine(result);
        return this.writeRefinedSync(refined);
    }
    catch (e) {
        this.error(e.stack);
    }
};
```

上面的代码有更简洁的写法：
The above has a direct translation:


```js
MyClass.prototype.method = function() {
    return fs.readFileAsync(this.file).bind(this)
    .then(function(contents) {
        var url = urlParse(contents);
        return this.httpGetAsync(url);
    }).then(function(result) {
        var refined = this.refine(result);
        return this.writeRefinedAsync(refined);
    }).catch(function(e) {
        this.error(e.stack);
    });
};
```

`.bind()` 是promise中使用 `this` 最有效的方法。在上面的代码中，句柄不是一个闭包，因此甚至可以在需要的时候做变量提升。在从一个 promise 传递到另一个 promise 过程中几乎不需要额外的管理。
`.bind()` is the most efficient way of utilizing `this` with promises. The handler functions in the above code are not closures and can therefore even be hoisted out if needed. There is literally no overhead when propagating the bound value from one promise to another.

<hr>

`.bind()`还有另外一种用途 - 当需要在promise句柄之间传递信息的时候，不用额外地创建一个函数或者对象：
`.bind()` also has a useful side purpose - promise handlers don't need to share a function to use shared state:

```js
somethingAsync().bind({})
.spread(function (aValue, bValue) {
    this.aValue = aValue;
    this.bValue = bValue;
    return somethingElseAsync(aValue, bValue);
})
.then(function (cValue) {
    return this.aValue + this.bValue + cValue;
});
```

当没有使用`.bind()`的时候，或变成这样：
The above without `.bind()` could be achieved with:

```js
var scope = {};
somethingAsync()
.spread(function (aValue, bValue) {
    scope.aValue = aValue;
    scope.bValue = bValue;
    return somethingElseAsync(aValue, bValue);
})
.then(function (cValue) {
    return scope.aValue + scope.bValue + cValue;
});
```


但是，这里还是有一些细微的差别：
However, there are many differences when you look closer:

- 需要一环调用链，不能直接在句柄上下文中使用
- 如果没有使用`bind()`而是用后一种方法，那么额外的封装是必要的，以避免泄露`scope`。
- 句柄函数实际上变成了一个闭包，因此会造成性能问题以及不可访问性。
- Requires a statement so cannot be used in an expression context
- If not there already, an additional wrapper function is required to avoid leaking or sharing `scope`
- The handler functions are now closures, thus less efficient and not reusable

<hr>

注意bind只会沿着调用链生效。如果你在一个调用句柄的内部又创建了一个新的promise链，那么在这个新的链的内部，`this`是没有绑定的：
Note that bind is only propagated with promise transformation. If you create new promise chains inside a handler, those chains are not bound to the "upper" `this`:

```js
something().bind(var1).then(function() {
    // this在这里是var1
    return Promise.all(getStuff()).then(function(results) {
        //this在这里未定义
    });
}).then(function() {
    //this在这里是var1
});
```

实际上，如果你懂得使用bluebird的全部API，你*几乎不可能*像上面的例子一样需要在句柄中创建新的promise链。上面这个例子实际上可以被这样改写：
However, if you are utilizing the full bluebird API offering, you will *almost never* need to resort to nesting promises in the first place. The above should be written more like:

```js
something().bind(var1).then(function() {
    //this在这里是var1
    return getStuff();
}).map(function(result) {
    //this在这里是var1
}).then(function() {
    //this在这里是var1
});
```

关于在异步处理集合的方法（例如`map()`），可以参考[Stackoverflow上的一个回答](http://stackoverflow.com/a/19467053/995876)
Also see [this Stackoverflow answer](http://stackoverflow.com/a/19467053/995876) on a good example on how utilizing the collection instance methods like [`.map()`](#mapfunction-mapper--object-options---promise) can clean up code.

<hr>

如果你不想在一开始就绑定this，你可以像下面这样在调用链的尾部绑定：
If you don't want to return a bound promise to the consumers of a promise, you can rebind the chain at the end:

```js
MyClass.prototype.method = function() {
    return fs.readFileAsync(this.file).bind(this)
    .then(function(contents) {
        var url = urlParse(contents);
        return this.httpGetAsync(url);
    }).then(function(result) {
        var refined = this.refine(result);
        return this.writeRefinedAsync(refined);
    }).catch(function(e) {
        this.error(e.stack);
    }).bind(); //The `thisArg` is implicitly undefined - I.E. the default promise `this` value
    //this在一开始是默认值，在句末可以被绑定
};
```

重绑定操作可以像下面这样使用：
Rebinding can also be abused to do something gratuitous like this:

```js
Promise.resolve("my-element")
    .bind(document)
    .then(document.getElementById)
    .bind(console)
    .then(console.log);
```

上面的用法与`console.log(document.getElementById("my-element"));`是等效的。在这里`.bind()`是必要的，因为在浏览器环境下，上面的方法都不是独立的函数。
The above does `console.log(document.getElementById("my-element"));`. The `.bind()`s are necessary because in browser neither of the methods can be called as a stand-alone function.

<hr>

#####`Promise.join(Promise|Thenable|value promises..., Function handler)` -> `Promise`

用于协调多个并行的promise。当需要处理一个不定数量但是规格一致的多个promise时，`.all()`是较好的选择。但是当你需要协调固定数量的promise时，`Promise.join`是一种更加简单（以及更加优雅）的方法，比如下面这个例子：
For coordinating multiple concurrent discrete promises. While [`.all()`](#all---promise) is good for handling a dynamically sized list of uniform promises, `Promise.join` is much easier (and more performant) to use when you have a fixed amount of discrete promises that you want to coordinate concurrently, for example:

```js
var Promise = require("bluebird");
var join = Promise.join;

join(getPictures(), getComments(), getTweets(),
    function(pictures, comments, tweets) {
    console.log("in total: " + pictures.length + comments.length + tweets.length);
});
```

```js
var Promise = require("bluebird");
var fs = Promise.promisifyAll(require("fs"));
var pg = Promise.promisifyAll(require("pg"));
var join = Promise.join;
var connectionString = "postgres://username:password@localhost/database";

var fContents = fs.readFileAsync("file.txt", "utf8");
var fStat = fs.statAsync("file.txt");
var fSqlClient = pg.connectAsync(connectionString).spread(function(client, done) {
    client.close = done;
    return client;
});

join(fContents, fStat, fSqlClient, function(contents, stat, sqlClient) {
    var query = "                                                              \
        INSERT INTO files (byteSize, contents)                                 \
        VALUES ($1, $2)                                                        \
    ";
   return sqlClient.queryAsync(query, [stat.size, contents]).thenReturn(query);
})
.then(function(query) {
    console.log("Successfully ran the Query: " + query);
})
.finally(function() {
    // This is why you want to use Promise.using for resource management
    if (fSqlClient.isFulfilled()) {
        fSqlClient.value().close();
    }
});
```

*注意：在1.x和0.x版本中，`Promise.join` 类似 `Promise.all`，区别在于它直接在参数中获取值，而不是在数组中。这个特性现在过时，但是依然在部分地方被支持 - 当最后一个参数是函数类型并且可以立即执行时，这个语义将生效。*
*Note: In 1.x and 0.x `Promise.join` used to be a `Promise.all` that took the values in as arguments instead in an array. This behavior has been deprecated but is still supported partially - when the last argument is an immediate function value the new semantics will apply*

<hr>

#####`Promise.try(Function fn [, Array<dynamic>|dynamic arguments] [, dynamic ctx] )` -> `Promise`

从`Promise.try`启动一段调用链。任何异常将让返回的promise变成rejection（拒绝）状态。
Start the chain of promises with `Promise.try`. Any synchronous exceptions will be turned into rejections on the returned promise.

```js
function getUserById(id) {
    return Promise.try(function() {
        if (typeof id !== "number") {
            throw new Error("id must be a number");
        }
        return db.getUserById(id);
    });
}
```

使用这个函数时，将会在后续的`.catch`句柄中捕获全部的错误，而不是麻烦地同时去处理同步错误和异步错误。
Now if someone uses this function, they will catch all errors in their Promise `.catch` handlers instead of having to handle both synchronous and asynchronous exception flows.

注意第二个参数：如果它是一个真数组，它的值将会依次成为函数调用的各个参数。否则，它将被传递作为函数调用的第一个参数
Note about second argument: if it's specifically a true array, its values become respective arguments for the function call. Otherwise it is passed as is as the first argument for the function call.

*为了兼容早期的ECMAscript版本，可以使用 `Promise.attempt()` 来替代 `Promise.try()`。*
*For compatibility with earlier ECMAScript version, an alias `Promise.attempt()` is provided for `Promise.try()`.*

<hr>

#####`Promise.method(Function fn)` -> `Function`

返回一个根据给定`fn`生成的新的函数。这个新函数会返回状态为fullfilled（已完成）的promise，而原先的函数中，需要手动处理promise对象的返回值或者抛出异常的rejected状态。
Returns a new function that wraps the given function `fn`. The new function will always return a promise that is fulfilled with the original functions return values or rejected with thrown exceptions from the original function.

当函数有时需要异步返回或者异步抛出异常时，使用这个方法会方便很多。
This method is convenient when a function can sometimes return synchronously or throw synchronously.

下面是没有使用 `Promise.method` 的例子：
Example without using `Promise.method`:

```js
MyClass.prototype.method = function(input) {
    if (!this.isValid(input)) {
        return Promise.reject(new TypeError("input is not valid"));
    }

    if (this.cache(input)) {
        return Promise.resolve(this.someCachedValue);
    }

    return db.queryAsync(input).bind(this).then(function(value) {
        this.someCachedValue = value;
        return value;
    });
};
```

使用 `Promise.method` 后，就不再需要手动处理返回值或者抛出异常了。
Using the same function `Promise.method`, there is no need to manually wrap direct return or throw values into a promise:

```js
MyClass.prototype.method = Promise.method(function(input) {
    if (!this.isValid(input)) {
        throw new TypeError("input is not valid");
    }

    if (this.cache(input)) {
        return this.someCachedValue;
    }

    return db.queryAsync(input).bind(this).then(function(value) {
        this.someCachedValue = value;
        return value;
    });
});
```

<hr>

#####`Promise.resolve(dynamic value)` -> `Promise`

创建一个状态为resolved（已解决）的promise。如果传入的值已经是一个`Promise`,那么它将直接返回。如果`value`不可以继续调用（即返回值带有`.then()`方法），那么将会返回一个状态为fullfilled（已完成）的Promise，其带有的返回值就是`value`。如果`value`是可以被继续调用的（类Promise对象，比如jQuery的$.ajax），那么会返回一个Promise对象，状态根据具体执行状况而定。
Create a promise that is resolved with the given value. If `value` is already a trusted `Promise`, it is returned as is. If `value` is not a thenable, a fulfilled Promise is returned with `value` as its fulfillment value. If `value` is a thenable (Promise-like object, like those returned by jQuery's `$.ajax`), returns a trusted Promise that assimilates the state of the thenable.

例子：(`$`代表jQuery)
Example: (`$` is jQuery)

```js
Promise.resolve($.get("http://www.google.com")).then(function() {
    //Returning a thenable from a handler is automatically
    //cast to a trusted Promise as per Promises/A+ specification
    return $.post("http://www.yahoo.com");
}).then(function() {

}).catch(function(e) {
    //jQuery doesn't throw real errors so use catch-all
    console.log(e.statusText);
});
```

<hr>

#####`Promise.reject(dynamic reason)` -> `Promise`

产生一个状态为rejected（已拒绝）的promise对象，返回值带有`reason`
Create a promise that is rejected with the given `reason`.

<hr>

#####`Promise.bind(dynamic thisArg)` -> `Promise`

`Promise.resolve(undefined).bind(thisArg);`的语法糖。具体参考[`.bind()`](#binddynamic-thisarg---promise)。
Sugar for `Promise.resolve(undefined).bind(thisArg);`. See [`.bind()`](#binddynamic-thisarg---promise).

<hr>

##Synchronous inspection
##同步检查

有时候我们需要知道在某些特定时刻，一个promise对象的状态 - 使用`then()`来获得这个状态是非常不方便的，因为回调总是异步地执行。
Often it is known in certain code paths that a promise is guaranteed to be fulfilled at that point - it would then be extremely inconvenient to use `.then()` to get at the promise's value as the callback is always called asynchronously.

**注意**：在最近的Bluebird版本中，`.reason()`和`.value()`以及其他方便状态检查的方法被暴露出来，以便让下面这些例子更简洁。`Promise.settle`方法依然返回一个`PromiseInspection`数组。每一个promise对象现在同样是`PromiseInspection`，所以与状态检查有关的方法可以被用在任何promise对象上。

**Note**: In recent versions of Bluebird a design choice was made to expose `.reason()` and `.value()` as well as other inspection methods on promises directly in order to make the below use case easier to work with. The `Promise.settle` method still returns a `PromiseInspection` array as its result. Every promise is now also a `PromiseInspection` and inspection methods can be used on promises freely.

例如，如果你需要在promise链中使用之前产生的值，你可以像下面这样嵌套：

For example, if you need to use values of earlier promises in the chain, you could nest:


```js
// From Q Docs https://github.com/kriskowal/q/#chaining
// MIT License Copyright 2009–2014 Kristopher Michael Kowal.
function authenticate() {
    return getUsername().then(function (username) {
        return getUser(username);
    // chained because we will not need the user name in the next event
    //我们在此处嵌套了一个调用链，因为后面我们不再需要username了
    }).then(function (user) {
        // nested because we need both user and password next
        //我们在此处嵌套了一个调用链，因为我们同时需要user和password
        return getPassword().then(function (password) {
            if (user.passwordHash !== hash(password)) {
                throw new Error("Can't authenticate");
            }
        });
    });
}
```

或者你可以像下面这种巧妙的方法：在我们认证密码前，user promise的状态必然是“已完成”。

Or you could take advantage of the fact that if we reach password validation, then the user promise must be fulfilled:

```js
function authenticate() {
    var user = getUsername().then(function(username) {
        return getUser(username);
    });

    return user.then(function(user) {
        return getPassword();
    }).then(function(password) {
        // Guaranteed that user promise is fulfilled, so .value() can be called here
        if (user.value().passwordHash !== hash(password)) {
            throw new Error("Can't authenticate");
        }
    });
}
```


In the latter the indentation stays flat no matter how many previous variables you need, whereas with the former each additional previous value would require an additional nesting level.

#### The `PromiseInspection` Interface
#### `PromiseInspection`接口

这个接口在`Promise`或者`PromiseInspection `对象中都可以被调用，实际上是调用了`Promise.settle`。

This interface is implemented by `Promise` instances as well as `PromiseInspection` results returned by calling `Promise.settle`.

<hr>

#####`.isFulfilled()` -> `boolean`

检查这个promise对象的状态是否为fulfilled（已完成）。
See if this `promise` has been fulfilled.

<hr>

#####`.isRejected()` -> `boolean`

检查这个promise对象的状态是否为rejected（已拒绝）。
See if this `promise` has been rejected.

<hr>

#####`.isPending()` -> `boolean`
检查这个promise对象的状态是否为pending（等待中，既不是fulfilled也不是rejected）。
See if this `promise` is pending (not fulfilled or rejected).

<hr>

#####`.value()` -> `dynamic`

得到promise的完成值。如果promise未完成则抛出错误 - promise对象未完成的时候调用这个方法是错误的。
Get the fulfillment value of this promise. Throws an error if the promise isn't fulfilled - it is a bug to call this method on an unfulfilled promise.

在调用`.value()`之前，你应该检查这个promise对象的`.isFulfilled()` - 以保证调用这个方法的时候promise对象已经完成。
You should check if this promise is `.isFulfilled()` before calling `.value()` - or only call `.value()` in code paths where it's guaranteed that this promise is fulfilled.

<hr>

#####`.reason()` -> `dynamic`

得到promise对象拒绝的原因。当promise对象的状态不是rejected的时候抛出一个错误 - 在未被拒绝的promise对象下调用这个方法是错误的。
Get the rejection reason of this promise. Throws an error if the promise isn't rejected - it is a bug to call this method on an unrejected promise.

在调用`.reason()`之前，你应该检查这个对象的`.isRejected()` - 以确保当前对象的状态为rejected。
You should check if this promise is `.isRejected()` before calling `.reason()` - or only call `.reason()` in code paths where it's guaranteed that this promise is rejected.


##Collections
##集合

`Promise`类提供的用于处理promise集合的实例方法以及静态方法。
Methods of `Promise` instances and core static methods of the Promise class to deal with collections of promises or mixed promises and values.

所有的集合方法在Promise对象中都有一个静态的等价方法，比如，`somePromise.map(...)...` 与 `Promise.map(somePromise, ...)...`等价，`somePromise.all()` 与 `Promise.all(somePromise)` 等价。

All collection methods have a static equivalent on the Promise object, e.g. `somePromise.map(...)...` is same as `Promise.map(somePromise, ...)...`,
`somePromise.all()` is same as `Promise.all(somePromise)` and so on.

集合方法不会改变原始输入。数组中的空元素在处理时将会被认作 `undefined`。
None of the collection methods modify the original input. Holes in arrays are treated as if they were defined with the value `undefined`.

#####`.all()` -> `Promise`

传入一个由promise对象组成的数组（这个数组包含多个promise，当所有元素的状态均为完成时，这个实例的状态会变为完成）。这个promise的终值是一个由原数组中所有promise对象终值组成的数组。如果数组中任何一个promise对象的状态变为”拒绝”，那么最后传出的promise的状态也是“拒绝”，并带有拒因。

Given an array, or a promise of an array, which contains promises (or a mix of promises and values) return a promise that is fulfilled when all the items in the array are fulfilled. The promise's fulfillment value is an array with fulfillment values at respective positions to the original array. If any promise in the array rejects, the returned promise is rejected with the rejection reason.

```js
var files = [];
for (var i = 0; i < 100; ++i) {
    files.push(fs.writeFileAsync("file-" + i + ".txt", "", "utf-8"));
}
Promise.all(files).then(function() {
    console.log("all the files were created");
});
```

<hr>

#####`.props()` -> `Promise`

类似`.all()`，但是是针对对象属性的，而不是数组。当传入对象的所有属性状态均为“完成”时，它会返回一个promise，这个promise带有的终值是一个对象，这个对象的所有属性的值是原对象中，对应属性的终值。如果对象中任何一个属性的状态变为”拒绝”，那么最后传出的promise的状态也是“拒绝”，并带有拒因。

Like `.all()` but for object properties instead of array items. Returns a promise that is fulfilled when all the properties of the object are fulfilled. The promise's fulfillment value is an object with fulfillment values at respective keys to the original object. If any promise in the object rejects, the returned promise is rejected with the rejection reason.



If `object` is a trusted `Promise`, then it will be treated as a promise for object rather than for its properties. All other objects are treated for their properties as is returned by `Object.keys` - the object's own enumerable properties.

```js
Promise.props({
    pictures: getPictures(),
    comments: getComments(),
    tweets: getTweets()
}).then(function(result) {
    console.log(result.tweets, result.pictures, result.comments);
});
```

```js
var Promise = require("bluebird");
var fs = Promise.promisifyAll(require("fs"));
var _ = require("lodash");
var path = require("path");
var util = require("util");

function directorySizeInfo(root) {
    var counts = {dirs: 0, files: 0};
    var stats = (function reader(root) {
        return fs.readdirAsync(root).map(function(fileName) {
            var filePath = path.join(root, fileName);
            return fs.statAsync(filePath).then(function(stat) {
                stat.filePath = filePath;
                if (stat.isDirectory()) {
                    counts.dirs++;
                    return reader(filePath)
                }
                counts.files++;
                return stat;
            });
        }).then(_.flatten);
    })(root).then(_);

    var smallest = stats.call("min", "size").call("pick", "size", "filePath").call("value");
    var largest = stats.call("max", "size").call("pick", "size", "filePath").call("value");
    var totalSize = stats.call("pluck", "size").call("reduce", function(a, b) {
        return a + b;
    }, 0);

    return Promise.props({
        counts: counts,
        smallest: smallest,
        largest: largest,
        totalSize: totalSize
    });
}


directorySizeInfo(process.argv[2] || ".").then(function(sizeInfo) {
    console.log(util.format("                                                \n\
        %d directories, %d files                                             \n\
        Total size: %d bytes                                                 \n\
        Smallest file: %s with %d bytes                                      \n\
        Largest file: %s with %d bytes                                       \n\
    ", sizeInfo.counts.dirs, sizeInfo.counts.files, sizeInfo.totalSize,
        sizeInfo.smallest.filePath, sizeInfo.smallest.size,
        sizeInfo.largest.filePath, sizeInfo.largest.size));
});
```

注意，如果你不需要得到对象的具体属性值，那么使用[`Promise.join()`](#promisejoinpromisethenablevalue-promises-function-handler---promise)会更加方便：

Note that if you have no use for the result object other than retrieving the properties, it is more convenient to use [`Promise.join()`](#promisejoinpromisethenablevalue-promises-function-handler---promise):

```js
Promise.join(getPictures(), getComments(), getTweets(),
    function(pictures, comments, tweets) {
    console.log(pictures, comments, tweets);
});
```

<hr>

#####`.settle()` -> `Promise`

给定一个包含多个promise的数组，或者一个数组的promise对象，当数组中的所有元素状态为“已完成”或者“已拒绝”时，将会返回一个状态为“已完成“的对象。终值是一个由[`PromiseInspection`](#synchronous-inspection)实例组成的数组。
Given an array, or a promise of an array, which contains promises (or a mix of promises and values) return a promise that is fulfilled when all the items in the array are either fulfilled or rejected. The fulfillment value is an array of [`PromiseInspection`](#synchronous-inspection) instances at respective positions in relation to the input array.

当你有一个promise组成的数组，并且希望知道它们什么时候全部处理完毕 - 无论最终状态是”已完成“还是”已拒绝“，这个方法会很有用。
This method is useful for when you have an array of promises and you'd like to know when all of them resolve - either by fulfilling or rejecting. For example:

```js
var fs = Promise.promisifyAll(require("fs"));
// map array into array of promises
//遍历数组，将它们promise化
var files = ['a.txt', 'b.txt'].map(function(fileName) {
    return fs.readFileAsync(fileName, "utf8");
});
Promise.settle(files).then(function(results) {
    // results is a PromiseInspection array
    //results是一个PromiseInspection数组
    // this is reached once the operations are all done, regardless if
    //无论成功与否，这一步都会执行。
    // they're successful or not.
    var r = results[0];
    if (r.isFulfilled()) {  //检查是否为成功状态
        console.log(r.value()); //promise的终值
        r.reason(); //由于状态为成功，所以这里会抛出错误。
    } else if (r.isRejected()) { //检查是否为拒绝状态
        console.log(r.reason()); //拒因
        r.value(); //由于状态为拒绝，所以这里会抛出错误。
    }
});
```

<hr>

#####`.any()` -> `Promise`

类似参数`count`为1的`.some()`，但是终值不是一个数组，而是值本身。
Like `.some()`, with 1 as `count`. However, if the promise fulfills, the fulfillment value is not an array of 1 but the value directly.

<hr>

#####`.race()` -> `Promise`

给定一个数组，当其中任意一个元素状态变为“已完成”或者“已拒绝”时，都会立即返回一个promise，带有终值或者拒因。
Given an array, or a promise of an array, which contains promises (or a mix of promises and values) return a promise that is fulfilled or rejected as soon as a promise in the array is fulfilled or rejected with the respective rejection reason or fulfillment value.

在一般情况下更可能使用[`.any()`](#any---promise)。
You most likely want to use the [`.any()`](#any---promise) method.

<hr>


#####`.some(int count)` -> `Promise`

在多个promise对象中，创建一个“竞赛”，当这些promise中，有数量为`count`的promise的状态变为“已完成”时，将会返回一个终值，这个终值是一个数组，各元素为刚才“竞赛”中最快的几个promise的终值。

Initiate a competetive race between multiple promises or values (values will become immediately fulfilled promises). When `count` amount of promises have been fulfilled, the returned promise is fulfilled with an array that contains the fulfillment values of the winners in order of resolution.

下面这个例子对四个域名发起了ping，然后打印出最快的两个。
This example pings 4 nameservers, and logs the fastest 2 on console:

```js
Promise.some([
    ping("ns1.example.com"),
    ping("ns2.example.com"),
    ping("ns3.example.com"),
    ping("ns4.example.com")
], 2).spread(function(first, second) {
    console.log(first, second);
});
```

如果有太多的promise的状态为“已拒绝”，以至于最后无法完成，那么将会抛出一个[`AggregateError`](#aggregateerror)错误。

If too many promises are rejected so that the promise can never become fulfilled, it will be immediately rejected with an [`AggregateError`](#aggregateerror) of the rejection reasons in the order they were thrown in.

你可以在`Promise.AggregateError`中找到相关参考。

You can get a reference to `AggregateError` from `Promise.AggregateError`.

```js
//为了呈现这个错误已经将其全局化。
//For clarity assumes bluebird error types have been globalized
Promise.some(...)
    .then(...)
    .then(...)
    .catch(AggregateError, function(err) {
        err.forEach(function(e) {
            console.error(e.stack);
        });
    });
```

<hr>

#####`.map(Function mapper [, Object options])` -> `Promise`

遍历一个数组或者一个数组的promise，对每一个元素使用`mapper`函数（参数依次为`item`,`index`,`arrayLength`）。如果输入的数组中任意一个promise状态变为“拒绝”，那么最终返回的promise的状态也为“拒绝”。
Map an array, or a promise of an array, which contains promises (or a mix of promises and values) with the given `mapper` function with the signature `(item, index, arrayLength)` where `item` is the resolved value of a respective promise in the input array. If any promise in the input array is rejected the returned promise is rejected as well.

mapper函数会尽量快地被调用，即当数组中的元素对应的promise被解决时。这意味着由最终结果组成的数组的元素顺序不一定和输入时一样。同样地，`.map`可以被用于并发协作，而不像`.all().call("map", fn).all()`。
The mapper function for a given item is called as soon as possible, that is, when the promise for that item's index in the input array is fulfilled. This doesn't mean that the result array has items in random order, it means that `.map` can be used for concurrency coordination unlike `.all().call("map", fn).all()`.

例子：
Example (copy paste and run):

```js
var Promise = require("bluebird");
var join = Promise.join;
var fs = Promise.promisifyAll(require("fs"));
fs.readdirAsync(".").map(function(fileName) {
    var stat = fs.statAsync(fileName);
    var contents = fs.readFileAsync(fileName).catch(function ignore() {});
    return join(stat, contents, function(stat, contents) {
        return {
            stat: stat,
            fileName: fileName,
            contents: contents
        }
    });
// The return value of .map is a promise that is fulfilled with an array of the mapped values
//.map的返回值是一个带有终值的数组
// That means we only get here after all the files have been statted and their contents read
//这意味着只有当所有文件都被读取到内存时，这个函数才会返回到这里
// into memory. If you need to do more operations per file, they should be chained in the map
//如果你需要对每个文件做进一步操作，可以使用接下来的数组。
// callback for concurrency.
}).call("sort", function(a, b) {
    return a.fileName.localeCompare(b.fileName);
}).each(function(file) {
    var contentLength = file.stat.isDirectory() ? "(directory)" : file.contents.length + " bytes";
    console.log(file.fileName + " last modified " + file.stat.mtime + " " + contentLength)
});
```

map的静态用法：
Example of static map:

```js
var Promise = require("bluebird");
var join = Promise.join;
var fs = Promise.promisifyAll(require("fs"));

var fileNames = ["file1.json", "file2.json"];
Promise.map(fileNames, function(fileName) {
    return fs.readFileAsync(fileName)
        .then(JSON.parse)
        .catch(SyntaxError, function(e) {
            e.fileName = fileName;
            throw e;
        })
}).then(function(parsedJSONs) {
    console.log(parsedJSONs);
}).catch(SyntaxError, function(e) {
   console.log("Invalid JSON in file " + e.fileName + ": " + e.message);
});
```
######设置: `concurrency`
######Option: `concurrency`

你可以指定一个并发数：
You may optionally specify a concurrency limit:

```js
...map(..., {concurrency: 1});
```

并发数限制了同时执行的Promise数量。比如，如果 `concurrency` 是 `3` ，那么有且仅有3个Promise会执行，直到其中任意一个状态变为“已解决”
The concurrency limit applies to Promises returned by the mapper function and it basically limits the number of Promises created. For example, if `concurrency` is `3` and the mapper callback has been called enough so that there are three returned Promises currently pending, no further callbacks are called until one of the pending Promises resolves. So the mapper function will be called three times and it will be called again only after at least one of the Promises resolves.

下面这个例子中可以看出，读取20个文件时，限制或不限制并发数对于运行时间的影响：
Playing with the first example with and without limits, and seeing how it affects the duration when reading 20 files:

```js
var Promise = require("bluebird");
var join = Promise.join;
var fs = Promise.promisifyAll(require("fs"));
var concurrency = parseFloat(process.argv[2] || "Infinity");
console.time("reading files");
fs.readdirAsync(".").map(function(fileName) {
    var stat = fs.statAsync(fileName);
    var contents = fs.readFileAsync(fileName).catch(function ignore() {});
    return join(stat, contents, function(stat, contents) {
        return {
            stat: stat,
            fileName: fileName,
            contents: contents
        }
    });
// The return value of .map is a promise that is fulfilled with an array of the mapped values
//.map的返回值是参数数组中所有promise的终值
// That means we only get here after all the files have been statted and their contents read
//这意味着只有当所有文件都被读取到内存时，这个函数才会返回到这里
// into memory. If you need to do more operations per file, they should be chained in the map
// callback for concurrency.
//如果你需要对每个文件做进一步操作，可以使用接下来的数组。
}, {concurrency: concurrency}).call("sort", function(a, b) {
    return a.fileName.localeCompare(b.fileName);
}).then(function() {
    console.timeEnd("reading files");
});
```

```bash
$ sync && echo 3 > /proc/sys/vm/drop_caches
$ node test.js 1
reading files 35ms
$ sync && echo 3 > /proc/sys/vm/drop_caches
$ node test.js Infinity
reading files: 9ms
```

such concurrency



<hr>

#####`.reduce(Function reducer [, dynamic initialValue])` -> `Promise`

归约一个数组，或者一个数组的promise。规约函数会被传入四个参数：`(total, item, index, arrayLength)`。
Reduce an array, or a promise of an array, which contains promises (or a mix of promises and values) with the given `reducer` function with the signature `(total, item, index, arrayLength)` where `item` is the resolved value of a respective promise in the input array, and `total` is either the initial value, or the result of the previous iteration. If any promise in the input array is rejected the returned promise is rejected as well.

如果规约函数返回了一个promise或者其他含有`.then`方法的对象，那么在继续下一个循环之前，这个promise的结果会被延迟。
If the reducer function returns a promise or a thenable, the result for the promise is awaited for before continuing with next iteration.

下面是一个读取几个文件，并且把他们的内容加到一起的例子。每个文件的内容文本都是`10`。
Read given files sequentially while summing their contents as an integer. Each file contains just the text `10`.

```js
Promise.reduce(["file1.txt", "file2.txt", "file3.txt"], function(total, fileName) {
    return fs.readFileAsync(fileName, "utf8").then(function(contents) {
        return total + parseInt(contents, 10);
    });
}, 0).then(function(total) {
    //Total is 30
});
```


*如果 `initialValue` 为 `undefined`（或者某个promise的终值为 `undefined`）并且数组中只有一个元素，那么将不会产生回调，而是直接返回 `undefined`。如果数组是空的，同样不会产生回调，会直接返回 `initialValue`（可能为 `undefined`）。*
*If `initialValue` is `undefined` (or a promise that resolves to `undefined`) and the array contains only 1 item, the callback will not be called and `undefined` is returned. If the array is empty, the callback will not be called and `initialValue` is returned (which may be `undefined`).*

Reduce方法会尽快地执行归约函数，所以你不再会用`.all().call("reduce")`
Reduce will call the reducer as soon as possible, this is why you might want to use it over `.all().call("reduce")`.

<hr>

#####`.filter(Function filterer [, Object options])` -> `Promise`

一种让下面的过程更有效率的方法：
An efficient shortcut for doing:

```js
....map(function(value, index, length) {
    return [filterer(value, index, length), value];
}).then(function(values) {
    return values.filter(function(stuff) {
        return stuff[0] == true
    }).map(function(stuff) {
        return stuff[1];
    });
});
```

######配置：`concurrency`
######Option: `concurrency`
参考[`concurrency` limit option in `.map()`](#option-concurrency)
See [`concurrency` limit option in `.map()`](#option-concurrency)

<hr>

#####`.each(Function iterator)` -> `Promise`


Iterate over an array, or a promise of an array, which contains promises (or a mix of promises and values) with the given `iterator` function with the signature `(item, index, value)` where `item` is the resolved value of a respective promise in the input array. Iteration happens serially. If any promise in the input array is rejected the returned promise is rejected as well.

Resolves to the original array unmodified, this method is meant to be used for side effects. If the iterator function returns a promise or a thenable, then the result of the promise is awaited, before continuing with next iteration.

Example where you might want to utilize `.each`:

```js
// Source: http://jakearchibald.com/2014/es7-async-functions/
function loadStory() {
  return getJSON('story.json')
    .then(function(story) {
      addHtmlToPage(story.heading);
      return story.chapterURLs.map(getJSON);
    })
    .each(function(chapter) { addHtmlToPage(chapter.html); })
    .then(function() { addTextToPage("All done"); })
    .catch(function(err) { addTextToPage("Argh, broken: " + err.message); })
    .then(function() { document.querySelector('.spinner').style.display = 'none'; });
}
```

<hr>

##Resource management
##资源管理

没有漏洞地管理资源是非常具有挑战性的。但仅仅使用`.finally`是远远不够的，例如下面这样：

Managing resources properly without leaks can be surprisingly challenging. Simply using `.finally` is not enough as the following example demonstrates:

```js
function doStuff() {
    return Promise.all([
        connectionPool.getConnectionAsync(),
        fs.readFileAsync("file.sql", "utf8")
    ]).spread(function(connection, fileContents) {
        return connection.query(fileContents).finally(function() {
            connection.close();
        });
    }).then(function() {
        console.log("query successful and connection closed");
    });
}
```

这里有个非常细微的漏洞：多次执行这段代码会导致连接池溢出，以至于不得不重启服务器。这是因为读取文件时如果失败，`.spread`将不会被调用，进而导致数据库的连接没有被关闭。

It is very subtle but over time this code will exhaust the entire connection pool and the server needs to be restarted. This is because
reading the file may fail and then of course `.spread` is not called at all and thus the connection is not closed.

有一种解决方法：先读取文件，成功后再连接数据库，或者反之。但是这样的话，连接数据库和读取文件没有同时进行，就失去了异步处理的优势，与我们之前使用同步的方法没什么差别。

One could solve this by either reading the file first or connecting first, and only proceeding if the first step succeeds. However,
this would lose a lot of the benefits of using asynchronity and we might almost as well go back to using simple synchronous code.

为了保留并发特性的同时，保证没有资源泄露，我们可以使用[`using()`](#promiseusingpromisedisposer-promise-promisedisposer-promise--function-handler---promise):

We can do better, retaining concurrency and not leaking resources, by using [`using()`](#promiseusingpromisedisposer-promise-promisedisposer-promise--function-handler---promise):

```js
var using = Promise.using;

using(getConnection(),
      fs.readFileAsync("file.sql", "utf8"), function(connection, fileContents) {
    return connection.query(fileContents);
}).then(function() {
    console.log("query successful and connection closed");
});
```


#####`Promise.using(Promise|Disposer promise, Promise|Disposer promise ..., Function handler)` -> `Promise`



In conjunction with [`.disposer()`](#disposerfunction-disposer---disposer), `using` will make sure that no matter what, the specified disposer will be called when the promise returned by the callback passed to `using` has settled. The disposer is necessary because there is no standard interface in node for disposing resources.

Here is a simple example (where `getConnection()` [has been defined] to return a proper [`Disposer`](#disposerfunction-disposer---disposer)))


```js
using(getConnection(), function(connection) {
   // Don't leak the `connection` variable anywhere from here
   // it is only guaranteed to be open while the promise returned from
   // this callback is still pending
   return connection.queryAsync("SELECT * FROM TABLE");
   // Code that is chained from the promise created in the line above
   // still has access to `connection`
}).then(function(rows) {
    // The connection has been closed by now
    console.log(rows);
});
```

Using multiple resources:

```js
using(getConnection(), function(conn1) {
    return using(getConnection(), function(conn2) {
        // use conn1 and conn 2 here
    });
}).then(function() {
    // Both connections closed by now
})
```

The above can also be written as (with a caveat, see below)

```js
using(getConnection(), getConnection(), function(conn1, conn2) {
    // use conn1 and conn2
}).then(function() {
    // Both connections closed by now
})
```

However, if the second `getConnection()` throws **synchronously**, the first connection is leaked. This will not happen
when using APIs through bluebird promisified methods though. You can wrap functions that could throw in [`Promise.method`](#promisemethodfunction-fn---function) which will turn synchronous rejections into rejected promises.

Note that you can mix promises and disposers, so that you can acquire all the things you need in parallel instead of sequentially

```js
// The files don't need resource management but you should
// still start the process of reading them even before you have the connection
// instead of waiting for the connection

// The connection is always closed, no matter what fails at what point
using(readFile("1.txt"), readFile("2.txt"), getConnection(), function(txt1, txt2, conn) {
    // use conn and have access to txt1 and txt2
});
```
<hr>

#####`.disposer(Function disposer)` -> `Disposer`

当使用[`using()`](#promiseusingpromisedisposer-promise-promisedisposer-promise--function-handler---promise)时，用来声明“清理函数”的方法，“清理函数”是指当资源使用完毕之后，将资源清理的一系列流程。

A meta method used to specify the disposer method that cleans up a resource when using [`using()`](#promiseusingpromisedisposer-promise-promisedisposer-promise--function-handler---promise).

例子：

```js
// This function doesn't return a promise but a Disposer
// 这个函数返回的不是promise，而是一个disposer（处理器）
// so it's very hard to use it wrong (not passing it to `using`)
function getConnection() {
    return pool.getConnectionAsync().disposer(function(connection, promise) {
        connection.close();
    });
}
```

一个真实的例子：

```js
var pg = require("pg");
// uncomment if necessary
// var Promise = require("bluebird");
// Promise.promisifyAll(pg);

function getSqlConnection(connectionString) {
    var close;
    return pg.connectAsync(connectionString).spread(function(client, done) {
        close = done;
        return client;
    }).disposer(function() {
        if (close) close();
    });
}

module.exports = getSqlConnection;
```

另外一个真实例子：

```js
var mysql = require("mysql");
// uncomment if necessary
// var Promise = require("bluebird");
// Promise.promisifyAll(mysql);
// Promise.promisifyAll(require("mysql/lib/Connection").prototype);
// Promise.promisifyAll(require("mysql/lib/Pool").prototype);
var pool  = mysql.createPool({
    connectionLimit: 10,
    host: 'example.org',
    user: 'bob',
    password: 'secret'
});

function getSqlConnection() {
    return pool.getConnectionAsync().disposer(function(connection) {
        connection.release();
    });
}

module.exports = getSqlConnection;
```

The second argument passed to a disposer is the result promise of the using block, which you can inspect synchronously.


#### Note about disposers in node

#### 关于在node环境中的disposers（处理器）

如果disposer（处理器）返回了一个失败的promise，那么非常有可能是因为在处置资源的时候出现错误了。如果发生了这种情况，Bluebird面临两种选择 - 忽略这个错误，继续运行程序；或者抛出一个错误，并停止node.js进程。

If a disposer method throws or returns a rejected promise, its highly likely that it failed to dispose of the resource. In that case, Bluebird has two options - it can either ignore the error and continue with program execution or throw an exception (crashing the process in node.js).

我们最终选择了后者，因为资源被认为是“稀缺的”。比如，如果数据库连接没有被处置妥当，而bluebird忽略了它，那么连接池的资源将很快耗尽，进程将变成无用的（所有的请求、数据库查询，都会被永远阻塞）。由于Bluebird不知道该如何处理，所以对于我们来说，明智的选择是终止这个进程，而不是忽略它以至于让这个进程变为无用的。

In bluebird we've chosen to do the later because resources are typically scarce. For example, if a database connection cannot be disposed of and Bluebird ignores that, the connection pool will be quickly depleted and the process will become unusable (all requests that query the database will wait forever). Since Bluebird doesn't know how to handle that, the only sensible default is to crash the process. That way, rather than getting a useless process that cannot fulfill more requests, we can swap the faulty worker with a new one letting the OS clean up the resources for us.


因此，如果你对disposer抛出的错误使用了 `try...catch` 并且编写了处理这种错误的代码，但是代码依然没有完美地解决问题，那么让进程终止或许是你最好的选择。

As a result, if you anticipate thrown errors or promise rejections while disposing of the resource you should use a `try..catch` block (or Promise.try) and write the appropriate catch code to handle the errors. If its not possible to sensibly handle the error, letting the process crash is the next best option.

这也意味着disposer不应该包含任何与处置资源无关的代码。比如你不应该在disposer中写入提交或者回滚事务的代码，因为如果这些代码失败的话，disposer捕获不到错误。

This also means that disposers should not contain code that does anything other than resource disposal. For example, you cannot write code inside a disposer to commit or rollback a transaction, because there is no mechanism for the disposer to signal a failure of the commit or rollback action without crashing the process.

如果一定要处理事务的话，你可以参考下面的模式：

For transactions, you can use the following similar pattern instead:

```js
function withTransaction(fn) {
  return Promise.using(pool.acquireConnection(), function(connection) {
    var tx = connection.beginTransaction()
    return Promise
      .try(fn, tx)
      .then(function(res) { return connection.commit().thenReturn(res) },
            function(err) {
              return connection.rollback()
                     .catch(function(e) {/* 可以把错误加入到err中 */})
                     .thenThrow(err);
            });
  });
}

// 如果withTransaction函数成功地执行，那么事务会被自动提交
// If the withTransaction block completes successfully, the transaction is automatically committed
// 在此过程中如果出现了任何的错误和异常，都会自动回滚事务
// Any error or rejection will automatically roll it back

withTransaction(function(tx) {
    return tx.queryAsync(...).then(function() {
        return tx.queryAsync(...)
    }).then(function() {
        return tx.queryAsync(...)
    });
});
```

<hr>


##Promisification
##Promise化

Promise化是指将一个不符合promise规范的API改造成返回promise的API。

Promisification means converting an existing promise-unaware API to a promise-returning API.

在node里通常的做法是使用`promisifyAll `来改造API，然后就可以使用符合promise规范的方法了。比如：

The usual way to use promises in node is to `promisifyAll` some API and start exclusively calling promise returning versions of the APIs methods. E.g.

```js
var fs = require("fs");
Promise.promisifyAll(fs);
// Now you can use fs as if it was designed to use bluebird promises from the beginning
// 现在你可以使用promise化之后的fs模块

fs.readFileAsync("file.js", "utf8").then(...)
```
注意上面是一个特殊的例子，因为`fs`是一个单例对象。大多数的库可以通过获取这个库的类（即构造器函数），然后对`.prototype`调用`promisifyAll `来完成对其的promise化。只需要在整个应用的生命周期里执行一次，之后就可以使用这个库的promise方法 - 即在方法名上添加`Async`后缀。

Note that the above is an exceptional case because `fs` is a singleton instance. Most libraries can be promisified by requiring the library's classes (constructor functions) and calling promisifyAll on the `.prototype`. This only needs to be done once in the entire application's lifetime and after that you may use the library's methods exactly as they are documented, except by appending the `"Async"`-suffix to method calls and using the promise interface instead of the callback interface.

在`fs`模块中有一个例外，`fs.existAsync`的行为不符合预期，因为Node的`fs.exists`不会在第一个参数里回调错误。具体情况请看#418。现有的解决方法是使用`fs.statAsync`。

As a notable exception in `fs`, `fs.existsAsync` doesn't work as expected, because Node's `fs.exists` doesn't call back with error as first argument.  More at #418.  One possible workaround is using `fs.statAsync`.

下面是对于一些常用库的promise化的例子：

Some examples of the above practice applied to some popular libraries:

```js
// The most popular redis module
var Promise = require("bluebird");
Promise.promisifyAll(require("redis"));
```

```js
// The most popular mongodb module
var Promise = require("bluebird");
Promise.promisifyAll(require("mongodb"));
```

```js
// The most popular mysql module
var Promise = require("bluebird");
// Note that the library's classes are not properties of the main export
// so we require and promisifyAll them manually
Promise.promisifyAll(require("mysql/lib/Connection").prototype);
Promise.promisifyAll(require("mysql/lib/Pool").prototype);
```

```js
// Mongoose
var Promise = require("bluebird");
Promise.promisifyAll(require("mongoose"));
```

```js
// Mongojs
var Promise = require("bluebird");
Promise.promisifyAll([
   require("mongojs/lib/collection"),
   require("mongojs/lib/database"),
   require("mongojs/lib/cursor")
]);
```

```js
// Request
var Promise = require("bluebird");
Promise.promisifyAll(require("request"));
// Use request.getAsync(...) not request(..), it will not return a promise
```

```js
// mkdir
var Promise = require("bluebird");
Promise.promisifyAll(require("mkdirp"));
// Use mkdirp.mkdirpAsync not mkdirp(..), it will not return a promise
```

```js
// winston
var Promise = require("bluebird");
Promise.promisifyAll(require("winston"));
```

```js
// rimraf
var Promise = require("bluebird");
// The module isn't promisified but the function returned is
var rimrafAsync = Promise.promisify(require("rimraf"));
```

```js
// xml2js
var Promise = require("bluebird");
Promise.promisifyAll(require("xml2js"));
```

```js
// jsdom
var Promise = require("bluebird");
Promise.promisifyAll(require("jsdom"));
```

```js
// fs-extra
var Promise = require("bluebird");
Promise.promisifyAll(require("fs-extra"));
```

```js
// prompt
var Promise = require("bluebird");
Promise.promisifyAll(require("prompt"));
```

```js
// Nodemailer
var Promise = require("bluebird");
Promise.promisifyAll(require("nodemailer"));
```

```js
// ncp
var Promise = require("bluebird");
Promise.promisifyAll(require("ncp"));
```

```js
// pg
var Promise = require("bluebird");
Promise.promisifyAll(require("pg"));
```
上面的例子中，我们假设了这些库都是直接返回实例的。如果你的需求不是这样，那么可以对初始化后实例使用`Promise.promisifyAll `:

In all of the above cases the library made its classes available in one way or another. If this is not the case, you can still promisify by creating a throwaway instance:

```js
var ParanoidLib = require("...");
var throwAwayInstance = ParanoidLib.createInstance();
Promise.promisifyAll(Object.getPrototypeOf(throwAwayInstance));
// Like before, from this point on, all new instances + even the throwAwayInstance suddenly support promises
```

更详细的说明在[`Promise.promisifyAll()`](#promisepromisifyallobject-target--object-options---object)。

See also [`Promise.promisifyAll()`](#promisepromisifyallobject-target--object-options---object).

#####`Promise.promisify(Function nodeFunction [, dynamic receiver])` -> `Function`


Returns a function that will wrap the given `nodeFunction`. Instead of taking a callback, the returned function will return a promise whose fate is decided by the callback behavior of the given node function. The node function should conform to node.js convention of accepting a callback as last argument and calling that callback with error as the first argument and success value on the second argument.

If the `nodeFunction` calls its callback with multiple success values, the fulfillment value will be an array of them.

If you pass a `receiver`, the `nodeFunction` will be called as a method on the `receiver`.

Example of promisifying the asynchronous `readFile` of node.js `fs`-module:

```js
var readFile = Promise.promisify(require("fs").readFile);

readFile("myfile.js", "utf8").then(function(contents) {
    return eval(contents);
}).then(function(result) {
    console.log("The result of evaluating myfile.js", result);
}).catch(SyntaxError, function(e) {
    console.log("File had syntax error", e);
//Catch any other error
}).catch(function(e) {
    console.log("Error reading file", e);
});
```

Note that if the node function is a method of some object, you can pass the object as the second argument like so:

```js
var redisGet = Promise.promisify(redisClient.get, redisClient);
redisGet('foo').then(function() {
    //...
});
```

But this will also work:

```js
var getAsync = Promise.promisify(redisClient.get);
getAsync.call(redisClient, 'foo').then(function() {
    //...
});
```

**Tip**

Use [`.spread`](#spreadfunction-fulfilledhandler--function-rejectedhandler----promise) with APIs that have multiple success values:

```js
var Promise = require("bluebird");
var request = Promise.promisify(require('request'));
request("http://www.google.com").spread(function(response, body) {
    console.log(body);
}).catch(function(err) {
    console.error(err);
});
```

The above uses [request](https://github.com/mikeal/request) library which has a callback signature of multiple success values.

<hr>

#####`Promise.promisifyAll(Object target [, Object options])` -> `Object`

Promisifies the entire object by going through the object's properties and creating an async equivalent of each function on the object and its prototype chain. The promisified method name will be the original method name suffixed with `"Async"`. Any class properties of the object (which is the case for the main export of many modules) are also promisified, both static and instance methods. Class property is a property with a function value that has a non-empty `.prototype` object. Returns the input object.

Note that the original methods on the object are not overwritten but new methods are created with the `Async`-suffix. For example, if you `promisifyAll()` the node.js `fs` object use `fs.statAsync()` to call the promisified `stat` method.

Example:

```js
Promise.promisifyAll(require("redis"));

//Later on, all redis client instances have promise returning functions:

redisClient.hexistsAsync("myhash", "field").then(function(v) {

}).catch(function(e) {

});
```

It also works on singletons or specific instances:

```js
var fs = Promise.promisifyAll(require("fs"));

fs.readFileAsync("myfile.js", "utf8").then(function(contents) {
    console.log(contents);
}).catch(function(e) {
    console.error(e.stack);
});
```

See [promisification](#promisification) for more examples.

The entire prototype chain of the object is promisified on the object. Only enumerable are considered. If the object already has a promisified version of the method, it will be skipped. The target methods are assumed to conform to node.js callback convention of accepting a callback as last argument and calling that callback with error as the first argument and success value on the second argument. If the node method calls its callback with multiple success values, the fulfillment value will be an array of them.

If a method name already has an `"Async"`-suffix, it will be duplicated. E.g. `getAsync`'s promisified name is `getAsyncAsync`.

######Option: `suffix`

Optionally, you can define a custom suffix through the options object:

```js
var fs = Promise.promisifyAll(require("fs"), {suffix: "MySuffix"});
fs.readFileMySuffix(...).then(...);
```

All the above limitations apply to custom suffices:

- Choose the suffix carefully, it must not collide with anything
- PascalCase the suffix
- The suffix must be a valid JavaScript identifier using ASCII letters
- Always use the same suffix everywhere in your application, you could create a wrapper to make this easier:

```js
module.exports = function myPromisifyAll(target) {
    return Promise.promisifyAll(target, {suffix: "MySuffix"});
};
```

######Option: `filter`

Optionally, you can define a custom filter through the options object:

```js
Promise.promisifyAll(..., {
    filter: function(name, func, target, passesDefaultFilter) {
        // name = the property name to be promisified without suffix
        // func = the function
        // target = the target object where the promisified func will be put with name + suffix
        // passesDefaultFilter = whether the default filter would be passed
        // return boolean (return value is coerced, so not returning anything is same as returning false)

        return passesDefaultFilter && ...
    }
})
```

The default filter function is:

```js
function defaultFilter(name, func) {
    return util.isIdentifier(name) &&
        name.charAt(0) !== "_" &&
        !util.isClass(func);
}
```

(`util` is bluebird util, not node.js util)

######Option: `promisifier`

Optionally, you can define a custom promisifier, so you could promisifyAll e.g. the chrome APIs used in Chrome extensions.

The promisifier gets a reference to the original method and should return a function which returns a promise.

```js
function DOMPromisifier(originalMethod) {
    // return a function
    return function promisified() {
        var args = [].slice.call(arguments);
        // Needed so that the original method can be called with the correct receiver
        var self = this;
        // which returns a promise
        return new Promise(function(resolve, reject) {
            args.push(resolve, reject);
            originalMethod.apply(self, args);
        });
    };
}

// Promisify e.g. chrome.browserAction
Promise.promisifyAll(chrome.browserAction, {promisifier: DOMPromisifier});

// Later
chrome.browserAction.getTitleAsync({tabId: 1})
    .then(function(result) {

    });
```

Combining `filter` with `promisifier` for the restler module to promisify event emitter:

```js
var Promise = require("bluebird");
var restler = require("restler");
var methodNamesToPromisify = "get post put del head patch json postJson putJson".split(" ");

function EventEmitterPromisifier(originalMethod) {
    // return a function
    return function promisified() {
        var args = [].slice.call(arguments);
        // Needed so that the original method can be called with the correct receiver
        var self = this;
        // which returns a promise
        return new Promise(function(resolve, reject) {
            // We call the originalMethod here because if it throws,
            // it will reject the returned promise with the thrown error
            var emitter = originalMethod.apply(self, args);

            emitter
                .on("success", function(data, response) {
                    resolve([data, response]);
                })
                .on("fail", function(data, response) {
                    // Erroneous response like 400
                    resolve([data, response]);
                })
                .on("error", function(err) {
                    reject(err);
                })
                .on("abort", function() {
                    reject(new Promise.CancellationError());
                })
                .on("timeout", function() {
                    reject(new Promise.TimeoutError());
                });
        });
    };
};

Promise.promisifyAll(restler, {
    filter: function(name) {
        return methodNamesToPromisify.indexOf(name) > -1;
    },
    promisifier: EventEmitterPromisifier
});

// ...

// Later in some other file

var restler = require("restler");
restler.getAsync("http://...", ...,).spread(function(data, response) {

})
```

Using `defaultPromisifier` parameter to add enhancements on top of normal node
promisification:

```js
var fs = Promise.promisifyAll(require("fs"), {
    promisifier: function(originalFunction, defaultPromisifer) {
        var promisified = defaultPromisifier(originalFunction);

        return function() {
            // Enhance normal promisification by supporting promises as
            // arguments

            var args = [].slice.call(arguments);
            var self = this;
            return Promise.all(args).then(function(awaitedArgs) {
                return promisified.apply(self, awaitedArgs);
            });
        };
    }
});

// All promisified fs functions now await their arguments if they are promises
var version = fs.readFileAsync("package.json", "utf8").then(JSON.parse).get("version");
fs.writeFileAsync("the-version.txt", version, "utf8");
```
<hr>

#####`Promise.fromNode(Function resolver)` -> `Promise`

Returns a promise that is resolved by a node style callback function. This is the most fitting way to do on the fly promisification when libraries don't expose classes for automatic promisification by [`promisifyAll`](#promisepromisifyallobject-target--object-options---object).

The resolver function is passed a callback that expects to be called back according to error-first node conventions.

Using manual resolver:

```js
// TODO use real library that doesn't expose prototypes for promisification
var object = crummyLibrary.create();
Promise.fromNode(function(callback) {
    object.foo("firstArgument", callback);
}).then(function(result) {
    console.log(result);
})
```

The same can also be written with `.bind`:

```js
// TODO use real library that doesn't expose prototypes for promisification
var object = crummyLibrary.create();
Promise.fromNode(object.foo.bind(object, "firstArgument")).then(function(result) {
    console.log(result);
})
```

<hr>

#####`.nodeify([Function callback] [, Object options])` -> `Promise`
#####`.asCallback([Function callback] [, Object options])` -> `Promise`

Register a node-style callback on this promise. When this promise is either fulfilled or rejected, the node callback will be called back with the node.js convention where error reason is the first argument and success value is the second argument. The error argument will be `null` in case of success.

Returns back this promise instead of creating a new one. If the `callback` argument is not a function, this method does not do anything.

This can be used to create APIs that both accept node-style callbacks and return promises:

```js
function getDataFor(input, callback) {
    return dataFromDataBase(input).nodeify(callback);
}
```

The above function can then make everyone happy.

Promises:

```js
getDataFor("me").then(function(dataForMe) {
    console.log(dataForMe);
});
```

Normal callbacks:

```js
getDataFor("me", function(err, dataForMe) {
    if( err ) {
        console.error( err );
    }
    console.log(dataForMe);
});
```

Promises can be rejected with falsy values (or no value at all, equal to rejecting with `undefined`), however `.nodeify` will call the callback with an `Error` object if the promise's rejection reason is a falsy value. You can retrieve the original falsy value from the error's `.cause` property.

Example:

```js
Promise.reject(null).nodeify(function(err, result) {
    // If is executed
    if (err) {
        // Logs 'null'
        console.log(err.cause);
    }
});
```

There is no effect on peformance if the user doesn't actually pass a node-style callback function.

######Option: `spread`

Some nodebacks expect more than 1 success value but there is no mapping for this in the promise world. You may specify the option `spread` to call the nodeback with multiple values when the fulfillment value is an array:

```js
Promise.resolve([1,2,3]).nodeify(function(err, result) {
    // err == null
    // result is the array [1,2,3]
});

Promise.resolve([1,2,3]).nodeify(function(err, a, b, c) {
    // err == null
    // a == 1
    // b == 2
    // c == 3
}, {spread: true});

Promise.resolve(123).nodeify(function(err, a, b, c) {
    // err == null
    // a == 123
    // b == undefined
    // c == undefined
}, {spread: true});
```

<hr>


##Timers

Methods to delay and time promises out.

#####`.delay(int ms)` -> `Promise`

Same as calling [`Promise.delay(this, ms)`](#promisedelaydynamic-value-int-ms---promise). With the exception that if this promise is [bound](#binddynamic-thisarg---promise) to a value, the returned promise is bound to that value too.

<hr>

#####`.timeout(int ms [, String message])` -> `Promise`

Returns a [`cancellable`](#cancellable---promise) promise that will be fulfilled with this promise's fulfillment value or rejection reason. However, if this promise is not fulfilled or rejected within `ms` milliseconds, the returned promise is cancelled with a [`TimeoutError`](#timeouterror) as the cancellation reason.

You may specify a custom error message with the `message` parameter.

The example function `fetchContent` doesn't leave the ongoing http request in the background in case the request cancelled from outside, either manually or through a timeout.

```js
// Assumes TimeoutError and CancellationError are both globally available
function fetchContent() {
    var jqXHR = $.get("http://www.slowpage.com");
    // Resolve the jQuery promise into a bluebird promise
    return Promise.resolve(jqXHR)
        .cancellable()
        .catch(TimeoutError, CancellationError, function(e) {
            jqXHR.abort();
            // Don't swallow it
            throw e;
        })
}

function fetchContentWith5Retries(retries) {
    retries = retries || 0;
    return fetchContent()
        .then(function(result) {
            //..
        })
        .timeout(100)
        .catch(TimeoutError, function(e) {
            if (retries < 5) {
                return fetchContentWith5Retries(retries + 1);
            }
            else {
                throw new Error("couldn't fetch content after 5 timeouts");
            }
        })
```

<hr>

#####`Promise.delay([dynamic value], int ms)` -> `Promise`

Returns a promise that will be fulfilled with `value` (or `undefined`) after given `ms` milliseconds. If `value` is a promise, the delay will start counting down when it is fulfilled and the returned promise will be fulfilled with the fulfillment value of the `value` promise.

```js
Promise.delay(500).then(function() {
    console.log("500 ms passed");
    return "Hello world";
}).delay(500).then(function(helloWorldString) {
    console.log(helloWorldString);
    console.log("another 500 ms passed") ;
});
```

<hr>

##Cancellation

By default, a promise is not cancellable. A promise can be marked as cancellable with `.cancellable()`. A cancellable promise can be cancelled if it's not resolved. Cancelling a promise propagates to the farthest cancellable ancestor of the target promise that is still pending, and rejects that promise with the given reason, or [`CancellationError`](#cancellationerror) by default. The rejection will then propagate back to the original promise and to its descendants. This roughly follows the semantics described [here](https://github.com/promises-aplus/cancellation-spec/issues/7).

Promises marked with `.cancellable()` return cancellable promises automatically.

If you are the resolver for a promise, you can react to a cancel in your promise by catching the [`CancellationError`](#cancellationerror):

```js
function ajaxGetAsync(url) {
    var xhr = new XMLHttpRequest;
    return new Promise(function (resolve, reject) {
        xhr.addEventListener("error", reject);
        xhr.addEventListener("load", resolve);
        xhr.open("GET", url);
        xhr.send(null);
    }).cancellable().catch(Promise.CancellationError, function(e) {
        xhr.abort();
        throw e; //Don't swallow it
    });
}
```

<hr>

#####`.cancellable()` -> `Promise`

Marks this promise as cancellable. Promises by default are not cancellable after v0.11 and must be marked as such for [`.cancel()`](#cancelerror-reason---promise) to have any effect. Marking a promise as cancellable is infectious and you don't need to remark any descendant promise.

If you have code written prior v0.11 using cancellation, add calls to `.cancellable()` at the starts of promise chains that need to support
cancellation in themselves or somewhere in their descendants.

<hr>

#####`.uncancellable()` -> `Promise`

Create an uncancellable promise based on this promise.

<hr>

#####`.cancel([Error reason])` -> `Promise`

Cancel this promise with the given reason. The cancellation will propagate
to farthest cancellable ancestor promise which is still pending.

That ancestor will then be rejected with the given `reason`, or a [`CancellationError`](#cancellationerror) if it is not given. (get a reference from `Promise.CancellationError`) object as the rejection reason.

Promises are by default not cancellable. Use [`.cancellable()`](#cancellable---promise) to mark a promise as cancellable.

<hr>

#####`.isCancellable()` -> `boolean`

See if this promise can be cancelled.

<hr>

##Generators

Using ECMAScript6 generators feature to implement C# 5.0 `async/await` like syntax.

#####`Promise.coroutine(GeneratorFunction generatorFunction)` -> `Function`

Returns a function that can use `yield` to yield promises. Control is returned back to the generator when the yielded promise settles. This can lead to less verbose code when doing lots of sequential async calls with minimal processing in between. Node version greater than `0.11.2` is required and needs to be executed with the `--harmony-generators` (or `--harmony`) command-line switch. All io.js versions are directly supported.

```js
var Promise = require("bluebird");

function PingPong() {

}

PingPong.prototype.ping = Promise.coroutine(function* (val) {
    console.log("Ping?", val)
    yield Promise.delay(500)
    this.pong(val+1)
});

PingPong.prototype.pong = Promise.coroutine(function* (val) {
    console.log("Pong!", val)
    yield Promise.delay(500);
    this.ping(val+1)
});

var a = new PingPong();
a.ping(0);
```

Running the example with node version at least 0.11.2:

    $ node --harmony test.js
    Ping? 0
    Pong! 1
    Ping? 2
    Pong! 3
    Ping? 4
    Pong! 5
    Ping? 6
    Pong! 7
    Ping? 8
    ...

When called, the coroutine function will start an instance of the generator and returns a promise for its final value.

Doing `Promise.coroutine(function*() {})` is almost like using the C# `async` keyword to mark the function, with `yield` working as the `await` keyword. Promises are somewhat like `Task`s.

**Tip**

You are able to yield non-promise values by adding your own yield handler using  [`Promise.coroutine.addYieldHandler`](#promisecoroutineaddyieldhandlerfunction-handler---void)

<hr>

#####`Promise.coroutine.addYieldHandler(function handler)` -> `void`

By default you can only yield Promises and Thenables inside coroutines. You can use this function to add yielding support for arbitrary types.

For example, if you wanted `yield 500` to be same as `yield Promise.delay(500)`:

```js
Promise.coroutine.addYieldHandler(function(value) {
     if (typeof value === "number") return Promise.delay(value);
});
```

Yield handlers are called when you yield something that is not supported by default. The first yield handler to return a promise or a thenable will be used.
If no yield handler returns a promise or a thenable then an error is raised.

An example of implementing callback support with `addYieldHandler`:

*This is a demonstration of how powerful the feature is and not the recommended usage. For best performance you need to use `promisifyAll` and yield promises directly.*

```js
var Promise = require("bluebird");
var fs = require("fs");

var _ = (function() {
    var promise = null;
    Promise.coroutine.addYieldHandler(function(v) {
        if (v === void 0 && promise != null) {
            return promise;
        }
        promise = null;
    });
    return function() {
        var def = Promise.defer();
        promise = def.promise;
        return def.callback;
    };
})();


var readFileJSON = Promise.coroutine(function* (fileName) {
   var contents = yield fs.readFile(fileName, "utf8", _());
   return JSON.parse(contents);
});
```

An example of implementing thunks support with `addYieldHandler`:

*This is a demonstration of how powerful the feature is and not the recommended usage. For best performance you need to use `promisifyAll` and yield promises directly.*

```js
var Promise = require("bluebird");
var fs = require("fs");

Promise.coroutine.addYieldHandler(function(v) {
    if (typeof v === "function") {
        var def = Promise.defer();
        try { v(def.callback); } catch(e) { def.reject(e); }
        return def.promise;
    }
});

var readFileThunk = function(fileName, encoding) {
    return function(cb) {
        return fs.readFile(fileName, encoding, cb);
    };
};

var readFileJSON = Promise.coroutine(function* (fileName) {
   var contents = yield readFileThunk(fileName, "utf8");
   return JSON.parse(contents);
});
```

An example of handling promises in parallel by adding an `addYieldHandler` for arrays :

```js
var Promise = require("bluebird");
var fs = Promise.promisifyAll(require("fs"));

Promise.coroutine.addYieldHandler(function(yieldedValue) {
    if (Array.isArray(yieldedValue)) return Promise.all(yieldedValue);
});

var readFiles = Promise.coroutine(function* (fileNames) {
   var promises = [];

   fileNames.forEach(function (fileName) {
      promises.push(fs.readFileAsync(fileName, "utf8"));
   });

   return yield promises;
});
```

##Utility

Functions that could potentially be handy in some situations.

<hr>

#####`.tap(Function handler)` -> `Promise`

Like `.finally()` that is not called for rejections.

```js
getUser().tap(function(user) {
    //Like in finally, if you return a promise from the handler
    //the promise is awaited for before passing the original value through
    return recordStatsAsync();
}).then(function(user) {
    //user is the user from getUser(), not recordStatsAsync()
});
```

Common case includes adding logging to an existing promise chain:

```js
doSomething()
    .then(...)
    .then(...)
    .then(...)
    .then(...)
```

```js
doSomething()
    .then(...)
    .then(...)
    .tap(console.log)
    .then(...)
    .then(...)
```

*Note: in browsers it is necessary to call `.tap` with `console.log.bind(console)` because console methods can not be called as stand-alone functions.*

<hr>

#####`.call(String propertyName [, dynamic arg...])` -> `Promise`

This is a convenience method for doing:

```js
promise.then(function(obj) {
    return obj[propertyName].call(obj, arg...);
});
```

For example ([`.some()` is a built-in array method](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/some)):

```js
var Promise = require("bluebird");
var fs = Promise.promisifyAll(require("fs"));
var path = require("path");
var thisPath = process.argv[2] || ".";
var now = Date.now();

fs.readdirAsync(thisPath)
    .map(function(fileName) {
        return fs.statAsync(path.join(thisPath, fileName));
    })
    .call("some", function(stat) {
        return (now - new Date(stat.mtime)) < 10000;
    })
    .then(function(someFilesHaveBeenModifiedLessThanTenSecondsAgo) {
        console.log(someFilesHaveBeenModifiedLessThanTenSecondsAgo) ;
    });
```

Chaining lo-dash or underscore methods (Copy-pasteable example):

```js
var Promise = require("bluebird");
var pmap = Promise.map;
var props = Promise.props;
var _ = require("lodash");
var fs = Promise.promisifyAll(require("fs"));

function getTotalSize(paths) {
    return pmap(paths, function(path) {
        return fs.statAsync(path).get("size");
    }).reduce(function(a, b) {
        return a + b;
    }, 0);
}

fs.readdirAsync(".").then(_)
    .call("groupBy", function(fileName) {
        return fileName.charAt(0);
    })
    .call("map", function(fileNames, firstCh) {
        return props({
            firstCh: firstCh,
            count: fileNames.length,
            totalSize: getTotalSize(fileNames)
        });
    })
    // Since the currently wrapped array contains promises we need to unwrap it and call .all() before continuing the chain
    // If the currently wrapped thing was an object with properties that might be promises, we would call .props() instead
    .call("value").all().then(_)
    .call("sortBy", "count")
    .call("reverse")
    .call("map", function(data) {
        return data.count + " total files beginning with " + data.firstCh + " with total size of " + data.totalSize + " bytes";
    })
    .call("join", "\n")
    .then(console.log)
```

<hr>

#####`.get(String propertyName|int index)` -> `Promise`

This is a convenience method for doing:

```js
promise.then(function(obj) {
    return obj[propertyName];
});
```

For example:

```js
db.query("...")
    .get(0)
    .then(function(firstRow) {

    });
```

If `index` is negative, the indexed load will become `obj.length + index`. So that -1 can be used to read last item
in the array, -2 to read the second last and so on. For example:

```js
Promise.resolve([1,2,3]).get(-1).then(function(value) {
    console.log(value); // 3
});
```

If the `index` is still negative after `obj.length + index`, it will be clamped to 0.

When promisifying libraries (e.g. `request`) that call the callback with multiple arguments, the promisified version of that function will fulfill with an array of the arguments. `.get` can be a nifty short-hand to get the argument of interest.

For example, if you are only interested in the `body` when using `request`, using the normal `.spread()` pattern isn't the most convenient one:

```js
var Promise = require("bluebird");
var request = Promise.promisifyAll(require("request"));

request.getAsync("http://www.google.com").spread(function(response, body) {
    // ...
});
```

With `get`:

```js
var Promise = require("bluebird");
var request = Promise.promisifyAll(require("request"));

request.getAsync("http://www.google.com").get(1).then(function(body) {
    // ...
});
```

<hr>

#####`.return(dynamic value)` -> `Promise`

Convenience method for:

```js
.then(function() {
   return value;
});
```

in the case where `value` doesn't change its value.

That means `value` is bound at the time of calling `.return()` so this will not work as expected:

```js
function getData() {
    var data;

    return query().then(function(result) {
        data = result;
    }).return(data);
}
```

because `data` is `undefined` at the time `.return` is called.

Function that returns the full path of the written file:

```js
var Promise = require("bluebird");
var fs = Promise.promisifyAll(require("fs"));
var baseDir = process.argv[2] || ".";

function writeFile(path, contents) {
    var fullpath = require("path").join(baseDir, path);
    return fs.writeFileAsync(fullpath, contents).thenReturn(fullpath);
}

writeFile("test.txt", "this is text").then(function(fullPath) {
    console.log("Successfully file at: " + fullPath);
});
```

*For compatibility with earlier ECMAScript version, an alias `.thenReturn()` is provided for `.return()`.*

<hr>

#####`.throw(dynamic reason)` -> `Promise`

Convenience method for:

```js
.then(function() {
   throw reason;
});
```

Same limitations apply as with `.return()`.

*For compatibility with earlier ECMAScript version, an alias `.thenThrow()` is provided for `.throw()`.*

<hr>

#####`Promise.noConflict()` -> `Object`

This is relevant to browser environments with no module loader.

Release control of the `Promise` namespace to whatever it was before this library was loaded. Returns a reference to the library namespace so you can attach it to something else.

```html
<!-- the other promise library must be loaded first -->
<script type="text/javascript" src="/scripts/other_promise.js"></script>
<script type="text/javascript" src="/scripts/bluebird_debug.js"></script>
<script type="text/javascript">
//Release control right after
var Bluebird = Promise.noConflict();

//Cast a promise from some other Promise library using the Promise namespace to Bluebird:
var promise = Bluebird.resolve(new Promise());
</script>
```

<hr>

#####`Promise.setScheduler(Function scheduler)` -> `void`

Scheduler should be a function that asynchronously schedules the calling of the passed in function:

```js
// This is just an example of how to use the api, there is no reason to do this
Promise.setScheduler(function(fn) {
    setTimeout(fn, 0);
});
```

Setting a custom scheduler could be necessary when you need a faster way to schedule functions than bluebird does by default.

You can also use it as a hook:

```js
// This will synchronize bluebird promise queue flushing with angulars queue flushing
// Angular is also now responsible for choosing the actual scheduler
Promise.setScheduler(function(fn) {
    $rootScope.$evalAsync(fn);
});
```

<hr>

#####`.reflect()` -> `Promise<PromiseInspection>`

The `.reflect()` method returns a promise that is always successful when this promise is settled. Its fulfillment value is a `PromiseInspection` instance that reflects the resolution this promise. See [this issue](https://github.com/petkaantonov/bluebird/issues/346) for example usage.

##Built-in error types

Bluebird includes a few built-in error types for common usage. All error types have the same identity across different copies of bluebird
module so that pattern matching works in [`.catch`](#catchfunction-errorclassfunction-predicate-function-handler---promise). All error types have a constructor taking a message string as their first argument, with that message
becoming the `.message` property of the error object.

By default the error types need to be referenced from the Promise constructor, e.g. to get a reference to `TimeoutError`, do `var TimeoutError = Promise.TimeoutError`. However, for convenience you will probably want to just make the references global.

#####`OperationalError()`

Represents an error is an explicit promise rejection as opposed to a thrown error. For example, if an error is errbacked by a callback API promisified through [`promisify()`](#promisepromisifyfunction-nodefunction--dynamic-receiver---function) or [`promisifyAll()`](#promisepromisifyallobject-target--object-options---object)
and is not a typed error, it will be converted to a `OperationalError` which has the original error in the `.cause` property.

`OperationalError`s are caught in [`.error()`](#error-rejectedhandler----promise) handlers.

<hr>

#####`TimeoutError()`

Signals that an operation has timed out. Used as a custom cancellation reason in [`.timeout()`](#timeoutint-ms--string-message---promise).

<hr>

#####`CancellationError()`

Signals that an operation has been aborted or cancelled. The default reason used by [`.cancel()`](#cancelerror-reason---promise).

<hr>

#####`AggregateError()`

A collection of errors. `AggregateError` is an array-like object, with numeric indices and a `.length` property. It supports all generic array methods such as `.forEach` directly.

`AggregateError`s are caught in [`.error()`](#error-rejectedhandler----promise) handlers, even if the contained errors are not operational.

[`.some()`](#someint-count---promise) and [`.any()`](#any---promise) use `AggregateError` as rejection reason when they fail.


Example:

```js
//Assumes AggregateError has been made global
var err = new AggregateError();
err.push(new Error("first error"));
err.push(new Error("second error"));
throw err;
```

<hr>

##Error management configuration

The default approach of bluebird is to immediately log the stack trace when a possibly unhandled rejection happens. For majority of applications
this will work perfectly, however for some it will give false positives. Such applications can then use the hooks to implement
a more suitable error handling scheme. Any scheme can be implemented on top of these hooks, e.g.:

- Immediate logging to stderr (the default)
- Logging after the promise became GCd (requires a native node.js module)
- Showing a live list of rejected promises
- Using no hooks and using `.done()` to manually to mark end points where rejections will not be handled
- ...

If there is any rejection event hook registered, global or local, automatic logging is disabled.

<hr>

###Global rejection events

Starting from 2.7.0 all bluebird instances also fire rejection events globally so that applications can register one universal hook for them.

The global events are:

 - `"unhandledRejection"` (corresponds to the local [`Promise.onPossiblyUnhandledRejection`](#promiseonpossiblyunhandledrejectionfunction-handler---undefined))
 - `"rejectionHandled"` (corresponds to the local [`Promise.onUnhandledRejectionHandled`](#promiseonunhandledrejectionhandledfunction-handler---undefined))


Attaching global rejection event handlers in **node.js**:

```js
// NOTE: event name is camelCase as per node convention
process.on("unhandledRejection", function(reason, promise) {
    // See Promise.onPossiblyUnhandledRejection for parameter documentation
});

// NOTE: event name is camelCase as per node convention
process.on("rejectionHandled", function(promise) {
    // See Promise.onUnhandledRejectionHandled for parameter documentation
});
```

Attaching global rejection event handlers in **browsers**:

Using DOM3 `addEventListener` APIs (support starting from IE9+):

```js
// NOTE: event name is all lower case as per DOM convention
window.addEventListener("unhandledrejection", function(e) {
    // NOTE: e.preventDefault() must be manually called to prevent the default
    // action which is currently to log the stack trace to console.warn
    e.preventDefault();
    // NOTE: parameters are properties of the event detail property
    var reason = e.detail.reason;
    var promise = e.detail.promise;
    // See Promise.onPossiblyUnhandledRejection for parameter documentation
});

// NOTE: event name is all lower case as per DOM convention
window.addEventListener("rejectionhandled", function(e) {
    // NOTE: e.preventDefault() must be manually called prevent the default
    // action which is currently unset (but might be set to something in the future)
    e.preventDefault();
    // NOTE: parameters are properties of the event detail property
    var promise = e.detail.promise;
    // See Promise.onUnhandledRejectionHandled for parameter documentation
});
```

In Web Workers you may use `self.addEventListener(...)`.

Using legacy APIs (support starting from IE6+):

```js
// NOTE: event name is all lower case as per legacy convention
window.onunhandledrejection = function(reason, promise) {
    // See Promise.onPossiblyUnhandledRejection for parameter documentation
};

// NOTE: event name is all lower case as per legacy convention
window.onrejectionhandled = function(promise) {
    // See Promise.onUnhandledRejectionHandled for parameter documentation
};
```
<hr>


#####`Promise.onPossiblyUnhandledRejection(Function handler)` -> `undefined`

*Note: this hook is specific to the bluebird instance its called on, application developers should use [global rejection events](#global-rejection-events)*

Add `handler` as the handler to call when there is a possibly unhandled rejection. The default handler logs the error stack to stderr or `console.error` in browsers.

```js
Promise.onPossiblyUnhandledRejection(function(e, promise) {
    throw e;
});
```

Passing no value or a non-function will have the effect of removing any kind of handling for possibly unhandled rejections.

<hr>

#####`Promise.onUnhandledRejectionHandled(Function handler)` -> `undefined`

*Note: this hook is specific to the bluebird instance its called on, application developers should use [global rejection events](#global-rejection-events)*

Add `handler` as the handler to call when a rejected promise that was reported as "possibly unhandled rejection" became handled.

Together with `onPossiblyUnhandledRejection` these hooks can be used to implement a debugger that will show a list
of unhandled promise rejections updated in real time as promises become handled.

For example:

```js
var unhandledPromises = [];
Promise.onPossiblyUnhandledRejection(function(reason, promise) {
    unhandledPromises.push(promise);
    //Update some debugger UI
});

Promise.onUnhandledRejectionHandled(function(promise) {
    var index = unhandledPromises.indexOf(promise);
    unhandledPromises.splice(index, 1);
    //Update the debugger UI
});
```

<hr>

#####`Promise.longStackTraces()` -> `void`

Call this right after the library is loaded to enable long stack traces. Long stack traces cannot be disabled after being enabled, and cannot be enabled after promises have already been created. Long stack traces imply a substantial performance penalty, around 4-5x for throughput and 0.5x for latency.

Long stack traces are enabled by default in the debug build.

To enable them in all instances of bluebird in node.js, use the environment variable `BLUEBIRD_DEBUG`:

```
BLUEBIRD_DEBUG=1 node server.js
```

You should enable long stack traces if you want better debugging experience. For example:

```js
Promise.longStackTraces();
Promise.resolve().then(function outer() {
    return Promise.resolve().then(function inner() {
        return Promise.resolve().then(function evenMoreInner() {
            a.b.c.d()
        }).catch(function catcher(e) {
            console.error(e.stack);
        });
    });
});
```

Gives

    ReferenceError: a is not defined
        at evenMoreInner (<anonymous>:6:13)
    From previous event:
        at inner (<anonymous>:5:24)
    From previous event:
        at outer (<anonymous>:4:20)
    From previous event:
        at <anonymous>:3:9
        at Object.InjectedScript._evaluateOn (<anonymous>:581:39)
        at Object.InjectedScript._evaluateAndWrap (<anonymous>:540:52)
        at Object.InjectedScript.evaluate (<anonymous>:459:21)

While with long stack traces disabled, you would get:

    ReferenceError: a is not defined
        at evenMoreInner (<anonymous>:6:13)
        at tryCatch1 (<anonymous>:41:19)
        at Promise$_resolvePromise [as _resolvePromise] (<anonymous>:1739:13)
        at Promise$_resolveLast [as _resolveLast] (<anonymous>:1520:14)
        at Async$_consumeFunctionBuffer [as _consumeFunctionBuffer] (<anonymous>:560:33)
        at Async$consumeFunctionBuffer (<anonymous>:515:14)
        at MutationObserver.Promise$_Deferred (<anonymous>:433:17)

On client side, long stack traces currently only work in Firefox and Chrome.

<hr>

#####`.done([Function fulfilledHandler] [, Function rejectedHandler ])` -> `void`

Like `.then()`, but any unhandled rejection that ends up here will be thrown as an error. Note that generally Bluebird is smart enough to figure out unhandled rejections on its own so `.done` is rarely required. As explained in the error management section, using `.done` is more of a coding style choice with Bluebird, and is used to explicitly mark the end of a promise chain.
<hr>

##Progression migration

Progression is deprecated as there are composability and chaining issues with APIs that use promise progression handlers. This API is kept for backwards compatibility and for interoperability between libraries for now. As other libraries move away from the progression API since it really has little to do with promises, so will Bluebird. Implementing the common use case of progress bars can be accomplished using a pattern similar to [IProgress](http://blogs.msdn.com/b/dotnet/archive/2012/06/06/async-in-4-5-enabling-progress-and-cancellation-in-async-apis.aspx) in C#.

For old code that still uses it, see [the progression docs in the deprecated API documentation](/deprecated_apis.md#progression).

Using jQuery before:

```js
Promise.resolve($.get(...))
    .progressed(function() {
        // ...
    })
    .then(function() {
        // ...
    })
    .catch(function(e) {
        // ...
    })
```

Using jQuery after:

```js
Promise.resolve($.get(...).progress(function() {
        // ...
    }))
    .then(function() {
        // ...
    })
    .catch(function(e) {
        // ...
    })
```

Implementing general progress interfaces like in C#:

```js
function returnsPromiseWithProgress(progressHandler) {
    return doFirstAction().tap(function() {
        progressHandler(0.33);
    }).then(doSecondAction).tap(function() {
        progressHandler(0.66);
    }).then(doThirdAction).tap(function() {
        progressHandler(1.00);
    });
}

returnsPromiseWithProgress(function(progress) {
    ui.progressbar.setWidth((progress * 200) + "px"); // update with on client side
}).then(function(value) { // action complete
   // entire chain is complete.
}).catch(function(e) {
    // error
});
```

Another example using `coroutine`:

```js
var doNothing = function() {};
var progressSupportingCoroutine = Promise.coroutine(function* (progress) {
        progress = typeof progress === "function" ? progress : doNothing;
        var first = yield getFirstValue();
        // 33% done
        progress(0.33);
        var second = yield getSecondValue();
        progress(0.67);
        var third = yield getThirdValue();
        progress(1);
        return [first, second, third];
});

var progressConsumingCoroutine = Promise.coroutine(function* () {
    var allValues = yield progressSupportingCoroutine(function(p) {
         ui.progressbar.setWidth((p * 200) + "px");
    });
    var second = allValues[1];
    // ...
});
```

##Deferred migration

Deferreds are deprecated in favor of the promise constructor. If you need deferreds for some reason, you can create them trivially using the constructor:

```js
function defer() {
    var resolve, reject;
    var promise = new Promise(function() {
        resolve = arguments[0];
        reject = arguments[1];
    });
    return {
        resolve: resolve,
        reject: reject,
        promise: promise
    };
}
```

For old code that still uses deferred objects, see [the deprecated API docs ](/deprecated_apis.md#promise-resolution).
