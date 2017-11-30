# 2017/11/23
## 准备工作
1.什么是[HAR-validator](https://www.npmjs.com/package/har-validator)?
>>使用JSON模式的极速HTTP存档(HAR)验证程序。
>>注意：v2.0.0此模块默认为基于Promise的API。对于具有向后兼容性v1.x的异步/回调API还提供

# 2017/11/24
(HAR介绍) http://blog.csdn.net/euyy1029/article/details/52350736

### 2017/11/27
# 第一步，解读 README.md
# 1.async.md
[async.md](https://github.com/2015015413suyuanyuan/har-validator/edit/master/docs/async.md) 


# 2.Callback.md
[callback.md](https://github.com/2015015413suyuanyuan/har-validator/blob/master/docs/callback.md) 

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
