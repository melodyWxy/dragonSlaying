<!--
 * @Author: 六弦
 * @LastEditors: 六弦
 * @Date: 2021-04-19 18:53:11
 * @LastEditTime: 2021-04-19 19:21:53
 * @FilePath: /codeAll/pub/tukong/PLC/README.md
-->

# PLC
package lib check

## 依赖
1. yarn
2. npm 
3. retirejs

## 设计
以 yarn audit输出为基准格式，抹平三方面的差异


## 核心代码

### run shell
```js
// lib-shellJS
module.exports = function shellExec(cmdStr){
    return new Promise((succCb, failCb)=> {
        return shell.exec(cmdStr, function(code, stdout, stderr ){
            const result = {
                code, 
                stdout, 
                stderr
            }
            if(code !== 0) {
                failCb(result);
            }else{
                succCb(result);
            }
            return result;
        })
   })
}
```
