# 2017/11/23
## 准备工作
1.什么是[HAR-validator](https://www.npmjs.com/package/har-validator)?
>>使用JSON模式的极速HTTP存档(HAR)验证程序。
>>注意：v2.0.0此模块默认为基于Promise的API。对于具有向后兼容性v1.x的异步/回调API还提供

(HAR介绍) http://blog.csdn.net/euyy1029/article/details/52350736

### 2017/11/27
# 第一步，解读 README.md
## 1.async.md
[async.md](https://github.com/2015015413suyuanyuan/har-validator/blob/master/docs/async.md) 


## 2.Callback.md


[callback.md](https://github.com/2015015413suyuanyuan/har-validator/blob/master/docs/callback.md) 

# 第二步：解读 *.js文件  
## callback.js  
[async.js](https://github.com/2015015413suyuanyuan/har-validator/blob/master/lib/async.js)

## error.js

[error.js](https://github.com/2015015413suyuanyuan/har-validator/blob/master/lib/error.js)

# 第三步 运行

## 1.安装har-validator 

>npm init  
>npm install har-validator -S 

## 2.写一段小程序运行



## 在运行的过程中出现了一个问题：  
程序的执行结果和我预期的结果不一样。请教老师以后我发现问题出现在这里：
>Note: as of v2.0.0 this module defaults to Promise based API. For backward compatibility with v1.x an async/callback API is also provided

>async API  
>callback API  
>Promise API (default)  

虽然它支持3种形式的调用，但是我们安装的har-validator是5.0版本，在2.0.0以后默认支持的就是Promise，虽然它向后支持callback  async形式的调用，但是我们安装的版本较高，所以她默认支持的就是promise，所以每次运行成功或者失败的结果都是返回一个promise对象。  

### 如何来解决这个问题？老师给我提供的两种方案：  

### 1.修改 node_moudules 里面  package.json  入口改下(改变它的入口参数，变为我们想要的调用方式)

### 图一： 

![h](http://a3.qpic.cn/psb?/V13ms87p1D0LH1/SfpLPr7oX5b9*ZYigGs8IMcfdMlwuiaFUCLCngN1DAs!/b/dPIAAAAAAAAA&bo=YAKIAGACiAARADc!&rf=viewer_4)

### 图二：

![h](http://a1.qpic.cn/psb?/V13ms87p1D0LH1/hvbjTaxmHzmIqkkWNuROtFsSfPHagcaapOF9AMq0asM!/b/dPMAAAAAAAAA&bo=gwIBAoMCAQIRADc!&rf=viewer_4)

### 图三：

![h](http://a3.qpic.cn/psb?/V13ms87p1D0LH1/nVbvGWp7svDQozc5aeBwTQxv8Mt0aNMQfpmPlch1Dn4!/b/dPIAAAAAAAAA&bo=zgJsAc4CbAERADc!&rf=viewer_4)

### 图四：

![h](http://a3.qpic.cn/psb?/V13ms87p1D0LH1/liiunLrGQUriwnBgsNQuF*BvzoGtuWw4DqiXKxKUnPc!/b/dPIAAAAAAAAA&bo=owKUAaMClAERADc!&rf=viewer_4)


### 2.rm- rf node_modules  
package.json  修改版本号 回到1.0（改变它的版本号，在旧的版本中默认支持的可能就是async形式的）  

```   
~/har-validator/test/fixtures/request(master*) » rm -rf node_modules     //删除以后缺少了它的依赖模块，运行出错 
------------------------------------------------------------
~/har-validator/test/fixtures/request(master*) » node yun.js                                            wangding@OFFICE
module.js:529
    throw err;
    ^

 Error: Cannot find module 'ajv' 
at Function.Module._resolveFilename (module.js:527:15)
    at Function.Module._load (module.js:476:23)
    at Module.require (module.js:568:17)
    at require (internal/module.js:11:18)
    at Object.<anonymous> (/home/wangding/har-validator/lib/promise.js:1:73)
    at Module._compile (module.js:624:30)
    at Object.Module._extensions..js (module.js:635:10)
    at Module.load (module.js:545:32)
    at tryModuleLoad (module.js:508:12)
    at Function.Module._load (module.js:500:3)
------------------------------------------------------------
```

```
~/har-validator/test/fixtures/request(master*) » vi package.json      //修改到1.0.0版本后，运行npm install 这样就会下载1.0.0版本
------------------------------------------------------------
~/har-validator/test/fixtures/request(master*) » npm install                                            wangding@OFFICE
npm WARN request@1.0.0 No description
npm WARN request@1.0.0 No repository field.

added 16 packages in 24.484s
------------------------------------------------------------
```

```
~/har-validator/test/fixtures/request(master*) » node yun.js     //再次运行，就不再是返回值就不是promise对象
true

//查看一下新下载的node_modules中模块的版本号
~/har-validator/test/fixtures/request/node_modules(master*) ? ll                                        wangding@OFFICE
总用量 0

drwxrwxr-x. 4 wangding wangding  80 12月  2 10:18 har-validator

------------------------------------------------------------
~/har-validator/test/fixtures/request/node_modules(master*) ? cd har-validator                          wangding@OFFICE
------------------------------------------------------------
~/har-validator/test/fixtures/request/node_modules/har-validator(master*) ? ll                          wangding@OFFICE
总用量 20K
drwxrwxr-x. 2 wangding wangding   27 12月  2 10:18 bin
drwxrwxr-x. 3 wangding wangding   53 12月  2 10:18 lib
-rw-rw-r--. 1 wangding wangding  755 6月  22 2015 LICENSE
-rw-rw-r--. 1 wangding wangding 2.2K 12月  2 10:18 package.json
-rw-rw-r--. 1 wangding wangding 8.8K 6月  22 2015 README.md
------------------------------------------------------------
~/har-validator/test/fixtures/request/node_modules/har-validator(master*) ? cat package.json            wangding@OFFICE
{
  "_from": "har-validator@^1.0.0",
  "_id": "har-validator@1.8.0",
  "_inBundle": false,
  "_integrity": "sha1-2DhCsOtMQ1lgrrEIoGejqpTA7rI=",
  "_location": "/har-validator",
  "_phantomChildren": {},
  "_requested": {
    "type": "range",
    "registry": true,
    "raw": "har-validator@^1.0.0",
    "name": "har-validator",
    "escapedName": "har-validator",
    "rawSpec": "^1.0.0",
    "saveSpec": null,
    "fetchSpec": "^1.0.0"
  },
  "_requiredBy": [
    "/"
  ],
  "_resolved": "https://registry.npmjs.org/har-validator/-/har-validator-1.8.0.tgz",
  "_shasum": "d83842b0eb4c435960aeb108a067a3aa94c0eeb2",
  "_spec": "har-validator@^1.0.0",
  "_where": "/home/wangding/har-validator/test/fixtures/request",
  "author": {
    "name": "Ahmad Nassri",
    "email": "ahmad@ahmadnassri.com",
    "url": "https://www.ahmadnassri.com/"
  },
  "bin": {
    "har-validator": "bin/har-validator"
  },
  "bugs": {
    "url": "https://github.com/ahmadnassri/har-validator/issues"
  },
  "bundleDependencies": false,
  "dependencies": {
    "bluebird": "^2.9.30",
    "chalk": "^1.0.0",
    "commander": "^2.8.1",
    "is-my-json-valid": "^2.12.0"
  },
  "deprecated": false,
  "description": "Extremely fast HTTP Archive (HAR) validator using JSON Schema",
  "devDependencies": {
    "codeclimate-test-reporter": "0.0.4",
    "echint": "^1.3.0",
    "istanbul": "^0.3.15",
    "mocha": "^2.2.5",
    "require-directory": "^2.1.1",
    "should": "^7.0.1",
    "standard": "^4.3.1"
  },
  "echint": {
    "ignore": [
      "coverage/**"
    ]
  },
  "engines": {
    "node": ">=0.10"
  },
  "files": [
    "bin",
    "lib"
  ],
  "homepage": "https://github.com/ahmadnassri/har-validator",
  "keywords": [
    "har",
    "http",
    "archive",
    "validate",
    "validator"
  ],
  "license": "ISC",
  "main": "lib/index",//旧版本入口是index,而不再是promise
  "name": "har-validator",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/ahmadnassri/har-validator.git"
  },
  "scripts": {
    "codeclimate": "codeclimate < coverage/lcov.info",
    "coverage": "istanbul cover --dir coverage _mocha -- -R dot",
    "posttest": "npm run coverage",
    "pretest": "standard && echint",
    "test": "mocha"
  },
  "version": "1.8.0"//版本号现在是1.8.0版本
}
```

```
~/har-validator/test/fixtures/request/node_modules/har-validator/lib(master*) » ll                      wangding@OFFICE
总用量 12K
-rw-rw-r--. 1 wangding wangding  186 4月   2 2015 error.js
-rw-rw-r--. 1 wangding wangding  776 6月  11 2015 index.js
drwxrwxr-x. 2 wangding wangding 4.0K 12月  2 10:18 schemas

```
>在这个版本我们可以看到它的文件只有error错误处理的和入口的，但是从我们现在5.0.0版本中我们可以看到它向后支持async的js,这是就是版本的更替，同时也有新版本的promise.js，默认的入口函数。

