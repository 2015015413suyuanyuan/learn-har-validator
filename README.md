# 2017/11/23
## 准备工作
1.什么是HAR-validattor?
>>使用JSON模式的极速HTTP存档(HAR)验证程序。
>>注意：v2.0.0此模块默认为基于Promise的API。对于具有向后兼容性v1.x的异步/回调API还提供

# 2017/11/24
http://blog.csdn.net/euyy1029/article/details/52350736

### 2017/11/27
# 第一步，解读 README.md
# 1.async.md

>首先对于Async 有一个认识：
>>Async is a utility module which provides straight-forward, powerful functions for working with asynchronous JavaScript. Although >>originally designed for use with Node.js and installable via npm install --save async, it can also be used directly in the browser.

(Async是一个实用程序模块，它为异步JavaScript提供了直接，强大的功能。虽然最初设计用于Node.js并npm install --save async可以通过安装，但也可以直接在浏览器中使用。)

// for use with Node-style callbacks...
//  用于Node风格的回调
```
var obj = {dev: "/dev.json", test: "/test.json", prod: "/prod.json"};
var configs = {};
 
async.forEachOf(obj, (value, key, callback) => {
    fs.readFile(__dirname + value, "utf8", (err, data) => {
        if (err) return callback(err);
        try {
            configs[key] = JSON.parse(data);
        } catch (e) {
            return callback(e);
        }
        callback();
    });
}, err => {
    if (err) console.error(err.message);
    // configs is now a map of JSON data
    doSomethingWith(configs);
});


// ...or ES2017 async functions
//  ...或ES2017异步功能
async.mapLimit(urls, 5, async function(url) {
    const response = await fetch(url)
    return response.body
}, (err, results) => {
    if (err) throw err
    // results is now an array of the response bodies
    console.log(results)
})
```
>Async.md的正文：
>>async API

```
import * as validate from 'har-validator/lib/async'
import { request, response } from 'har-validator/lib/async'
```

>>在async.md文件中，有很多afterRequest(data)/beforeRequest(data)等等，例如下面的：
### validate.beforeRequest(data)

> Returns `true` or `false`.

- **data**: `Object` *(Required)*
  a `["afterRequest"]`(https://github.com/ahmadnassri/har-spec/blob/master/versions/1.2.md#cache) objects

```js
let isValid = validate.beforeRequest(data)
```
//通过传入数据给方法，validate自动会对数据格式进行验证，从而返回true/false来告诉我们数据格式的正确与否。

### 那么它们是什么意思呢？

他们是`HAR(HTTP Archive)`规范

HAR（HTTP Archive），是一个用来储存HTTP请求/响应信息的通用文件格式，基于JSON。这个格式的出现可以使HTTP监测工具以一种通用的格式导出所收集的数据，这些数据可以被其他支持HAR的HTTP分析工具（包括Firebug，httpwatch，Fiddler等）所使用，来分析网站的性能瓶颈。目前HAR规范最新版本为HAR 1.2。HAR文件必须是UTF-8编码，有无BOM无所谓。

>>>更多的关于async的介绍： http://blog.csdn.net/marujunyy/article/details/8695205

# 2.Callback.rd
>callback.rd正文
>>callback API

每一个的格式类似如下，只是方法的名字不同：
```
validate.afterRequest(data [, callback])

data: Object (Required) a afterRequest" objects
callback: Function callback function with signature of (err, valid)

validate.afterRequest(data, (err, valid) => {
  if (err) console.error(err.errors)

  if (valid) console.log('✔️')
})

```

传入数据的格式要对应上，每一个方法后面都有一个callback，用于错误处理。

# 第二步：解读 *.js文件

# async.js
```
var Ajv = require('ajv')//引入ajv，json验证器
var HARError = require('./error')//引入同一目录下error.js，错误处理
var schemas = require('har-schema')//引入har-schema,JSON Schema for HTTP Archive (HAR),Compatible with any JSON Schema validation tool.

var ajv

function validate (name, data, next) {
  data = data || {}

  // validator config  验证器配置
  ajv = ajv || new Ajv({
    allErrors: true,
    schemas: schemas//添加HAR模式验证,基于JSON格式（已经引入'har-schema'）
  })

  var validate = ajv.getSchema(name + '.json')//得到验证模式

  var valid = validate(data)//验证结果

  // callback? 是否是一个回调函数，是回调函数，如果验证结果为false，抛出错误，如果是true，返回next(null,valid)
  if (typeof next === 'function') {
    return next(!valid ? new HARError(validate.errors) : null, valid)
  }
 //如果不是回调函数，直接返回验证结果
 return valid
}
```
>ajv

前面说到，HAR是基于JSON的，所有这里我们引入ajv用来检测是否为合格的JSON.
AJV是JSON模式验证器。

我们所用到的方法：

.getSchema(String key) -> Function<Object data>
Retrieve compiled schema previously added with addSchema by the key passed to addSchema or by its full reference (id). The returned validating function has schema property with the reference to the original schema.


检索先前addSchema通过传递给addSchema它的完整引用（id）的键所添加的编译模式。返回的验证函数具有schema对原始模式的引用的属性。

>async.js中下面的代码中暴露function的方法一样，举出两个例子说明问题：
```
exports.afterRequest = function (data, next) {
  return validate('afterRequest', data, next)
}
/*
afterRequest方法中传入形参：数据data和回调函数next
返回是刚刚定义的验证的结果，所需要匹配的格式是afterReuest，数据是传入的data
如果next是函数，如果验证结果为false，抛出错误，如果是true，返回next(null,valid)，可以通过next回调函数进一步对数据进行处理
如果next不是函数，直接返回验证结果
*/

exports.beforeRequest = function (data, next) {
  return validate('beforeRequest', data, next)
}
```


## error.js

错误处理

官方API 介绍 ：http://nodejs.cn/api/errors.html

```
function HARError (errors) {
  var message = 'validation failed'

  this.name = 'HARError'
  this.message = message
  this.errors = errors

  if (typeof Error.captureStackTrace === 'function') {
    Error.captureStackTrace(this, this.constructor)
    
  } else {
    this.stack = (new Error(message)).stack 
    //error.stack 属性是一个字符串，描述代码中 Error 被实例化的位置。
 }
}

HARError.prototype = Error.prototype

module.exports = HARError
```
```
Error.captureStackTrace(targetObject[, constructorOpt])#

查看英文版 / 查看英文md文件 / 编辑中文md文件

targetObject <Object>
constructorOpt <Function>
在 targetObject 上创建一个 .stack 属性，当访问时返回一个表示代码中调用 Error.captureStackTrace() 的位置的字符串。

const myObject = {};
Error.captureStackTrace(myObject);
myObject.stack;  // 类似 `new Error().stack`
${myObject.name}: ${myObject.message} 会作为该堆栈跟踪的第一行。

可选的 constructorOpt 参数接受一个函数。 如果提供了，则 constructorOpt 之上包括自身在内的全部栈帧都会被生成的堆栈跟踪省略。

constructorOpt 参数用在向最终用户隐藏错误生成的具体细节时非常有用。例如：

function MyError() {
  Error.captureStackTrace(this, MyError);
}

// 没传入 MyError 到 captureStackTrace，MyError 帧会显示在 .stack 属性。
// 通过传入构造函数，可以省略该帧，且保留其下面的所有帧。
new MyError().stack;
```

# 第三步 运行测试
安装har-validator 
引入相关文件、数据、API
运行即可，用他的测试文件就可以。演示
