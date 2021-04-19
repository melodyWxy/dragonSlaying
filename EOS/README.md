<!--
 * @Author: 六弦
 * @LastEditors: 六弦
 * @Date: 2021-04-19 18:02:17
 * @LastEditTime: 2021-04-19 18:52:22
 * @FilePath: /codeAll/pub/tukong/EOS/README.md
-->
# EOS
线上错误监控系统


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



### 源码追踪 - sourceMap


### 开发者追踪 - git api


