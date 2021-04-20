<!--
 * @Author: 六弦
 * @LastEditors: 六弦
 * @Date: 2021-04-19 18:02:17
 * @LastEditTime: 2021-04-20 16:15:15
 * @FilePath: /codeAll/pub/tukong/EOS/README.md
-->
# EOS
线上错误监控系统


<img src="https://melodyworld.oss-cn-beijing.aliyuncs.com/tulongshu/EOS%E5%85%A8%E9%93%BE%E8%B7%AF%E5%9B%BE.jpg" width="800" height="600"/>


## 核心代码

### 异常捕获

```js
    // js error, 异步error 捕获
    window.addEventListener('error', (event) => {
        // event.error; error对象 { massage, stack }
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
    const { SourceMapConsumer } = SourceMap;

    const rawSourceMap = JSON.parse(readFileSync('path/to/js/map/file', 'utf8'));

    SourceMapConsumer.with(rawSourceMap, null, consumer => {
    const pos = consumer.originalPositionFor({
        line: 1,
        column: 982
    });

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


### 开发者追踪 - git api


