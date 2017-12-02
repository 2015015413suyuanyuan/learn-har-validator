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
[async.md](https://github.com/2015015413suyuanyuan/har-validator/blob/master/docs/async.md) 


# 2.Callback.md
[callback.md](https://github.com/2015015413suyuanyuan/har-validator/blob/master/docs/callback.md) 

# 第二步：解读 *.js文件
[async.js](https://github.com/2015015413suyuanyuan/har-validator/blob/master/lib/async.js)

## error.js
[error.js](https://github.com/2015015413suyuanyuan/har-validator/blob/master/lib/error.js)
错误处理


# 第三步 运行
1.安装har-validator 
>npm init
>npm install har-validator -S
2.写一段小程序运行

## 在运行的过程中出现了一个问题：  
程序的执行结果和我预期的结果不一样。请教老师以后我发现问题出现在这里：
>Note: as of v2.0.0 this module defaults to Promise based API. For backward compatibility with v1.x an async/callback API >is also provided

>async API
>callback API
>Promise API (default)


