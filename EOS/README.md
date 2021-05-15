<!--
 * @Author: 六弦
 * @LastEditors: 六弦
 * @Date: 2021-04-19 18:02:17
 * @LastEditTime: 2021-05-15 16:53:06
 * @FilePath: /tuya-check-server/Users/liuxian/codeAll/pub/tukong/EOS/README.md
-->
# EOS
线上错误监控系统


<img src="https://melodyworld.oss-cn-beijing.aliyuncs.com/tulongshu/EOS%E5%85%A8%E9%93%BE%E8%B7%AF%E5%9B%BE.jpg" width="800" height="600"/>


如图所示，
1. 客户端运行静态资源代码(HTML, JS)发生错误时，通过异常捕获后的回调，上报到服务端(一般都会上报到运维那边的日志服务台)；
2. 然后服务端判断是ERROR类型，即可将其发送给EOS(也可以EOS主动获取)；
3. EOS对错误信息进错误源映射追踪，从而找到错误发生的源码位置；通过拿到源码文件及行列，通过git追踪到对应的最后一个commit提交者，或称“肇事者”；
4. EOS将错误源及时通知给"肇事者"。

### 意义：
第一时间发现并解决线上问题的根源，保障业务的稳定性。
## 核心代码

### 异常捕获

```js
    // js error, 异步error 捕获
    window.addEventListener('error', (event) => {
        // event.error; error对象 { massage, stack }  
        // 关键是event.error.stack一定要上报上去！
        // report...
    })
    // 静态资源异常捕获
    HTMLImageElement.prototype.onerror = (event) => {
        throw new Error(event);
    }

```



### 源码追踪 - sourceMap、


1. 打包出map文件和bundle；(线上不要上传map文件) 
 webpackconfig配置： 

    ```js
        devtool: 'source-map',
    ```
    附录： webpack——devtool里的7种SourceMap模式

    | 模式 | 解释 |
    | ---- | ----- | 
    | eval  |	每个module会封装到 eval 里包裹起来执行，并且会在末尾追加注释 //@ sourceURL. |
    |source-map	| 生成一个SourceMap文件. |
    |hidden-source-map	| 和 source-map 一样，但不会在 bundle 末尾追加注释. |
    |inline-source-map	| 生成一个 DataUrl 形式的 SourceMap 文件. |
    |eval-source-map  | 每个module会通过eval()来执行，并且生成一个DataUrl形式的SourceMap. |
    |cheap-source-map	| 生成一个没有列信息（column-mappings）的SourceMaps文件，不包含loader的 sourcemap（譬如 babel 的 sourcemap）|
    |cheap-module-source-map	| 生成一个没有列信息（column-mappings）的SourceMaps文件，同时 loader 的 sourcemap 也被简化为只包含对应行的。| 

    > 打包效果如图：

    <img src= "https://melodyworld.oss-cn-beijing.aliyuncs.com/tulongshu/souce-map-file.jpg" width="500" height="150"/>


2. 基于map文件和errorMsg错误源追踪
    ```js
    const fs = require('fs');
    const SourceMap = require('source-map');

    const { readFileSync } = fs;

    const rawSourceMap = JSON.parse(readFileSync('path/to/js/map/file', 'utf8'));

    // 真实报错的点在客户端error事件的event.error.stack中
    const mockErrorMsg = {
        line: 1,
        column: 982
    }

    SourceMap.SourceMapConsumer.with(rawSourceMap, null, consumer => {
    const pos = consumer.originalPositionFor(mockErrorMsg);
    console.log(pos);
    });
    /** 
     * 查找到的原始信息
    { 
        source: 'path/to/index.js',
        line: 8,
        column: 7,
        name: 'hello' 
    } 
     **/
    ```


### 开发者追踪 - git blame 

>如果只查文件中某一部分由谁所写： git blame 文件名 | grep "查找词"


```shell
    git blame [文件名] -L  a,b
```
+ -L 参数表示后面接的是行号(Line)， a,b代表查询文件的第a行到第b行之间的文件内容情况。
+ a: 代表从第a行到文件结尾,
+ b: 代表从文件开头到第b行。

例如：
```shell
    git blame ./EOS/README.md -L  3,3
```
输出：a098d7cc (六弦 2021-04-19 18:07:35 +0800 3)  * @LastEditors: 六弦


**node**
```js
    const shell = require('shelljs');
    shell.exec('git blame ./EOS/README.md -L  3,3', function(_code, stdout, _stderr ){
        console.log(stdout);
    })
    /**
     *  输出：
        * a098d7cc (六弦 2021-04-19 18:07:35 +0800 3)  * @LastEditors: 六弦
        * 
        **/
```

### 通知方式

目前以创建钉钉群、企业微信群机器人的方式较多，通过调用群机器人的api，将报错内容，源码位置，及源码最后的开发者信息发给群中并 @对应开发者。
这种方式的实现比较简单，直接上钉钉机器人官方文档示例： 

https://developers.dingtalk.com/document/app/custom-robot-access


## 基于Sentry的EOS
> 如果已经使用了Sentry，将减少埋点，源码追踪的过程。 因为sentry已经做了这两件事。

<img src="https://melodyworld.oss-cn-beijing.aliyuncs.com/headers/EOS_sentry.jpg" width="800" height="600"/>


### 前置条件

+ sentry权限账号
+ gitlab权限账号
+ 开发者通讯录userId | 手机号


### 完整解决式方案
<img src="https://melodyworld.oss-cn-beijing.aliyuncs.com/headers/eos-FINISH.jpg" width="1000" height="600">

## 现有的傻瓜式成熟方案

如果贵司用的阿里云产品，那么
https://help.aliyun.com/document_detail/102106.html


