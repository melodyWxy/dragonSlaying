<!--
 * @Author: 六弦
 * @LastEditors: 六弦
 * @Date: 2021-04-19 18:53:11
 * @LastEditTime: 2021-04-29 15:19:36
 * @FilePath: /ty-eos/Users/liuxian/codeAll/pub/tukong/BDG/README.md
-->

# BDG
> Building door god —— 构建门神，即构建打包过程的检测方案。

主要涵盖三个方面： 
+ lint —— 代码校验
+ package lib check —— 包依赖安全检测；
+ building bulk check —— 构建大小检测;

**这些构建检测功能包从开发到落地的流程，同时要伴随着对应的规范落地** 

> 落地方案
1. 定规范: 
2. 开发对应的功能组件
    + 开发构建时集成到发布平台的流程卡点组件；
    + 本地开发时的包依赖(或者配置依赖) 结合git hoook(如hushy包)；
3. 推规范
    + 直接集成到项目模板； 
    + 对于老项目，一般按业务线辅助落地，渐进式推进；
4. 集成到发布平台

## lint
lint —— 代码校验，主要从两方面： 
1. 代码风格检测； 
2. 定制化lint插件检测；(如检测代码中是否含有location不规范跳转)

### 技术选型
+ eslint
+ tslint
+ 自定义lint-plugin开发



## package lib check

package lib check —— 包依赖安全检测，主要从两方面： 
1. 对项目依赖树的包版本分析，基于开源的漏洞库扫描已知的包版本漏洞
2. 对项目依赖树的许可证书进行分析，基于package.licence + 项目自定义白名单 扫描出无许可证或者不正常许可证的依赖。


### 技术选型
#### 开源项目
如果你的项目是开源的，那么 **snyk** 无疑是最佳选择，它可以完美的解决上述1的问题。snyk只对开源项目完全免费，对闭源项目每天只提供200次扫描机会。

#### 闭源项目
+ npm audit
    NPM集成功能，源于08年收购的nsp；
    原理： 基于整合的具备已知漏洞的包版本资料库，在npm上整合漏洞扫描的能力。
    前置条件：lock文件
    npm audit
    支持JSON/文本报表


+ yarn audit
    YARN集成功能
    原理： 基于具备已知漏洞的包版本资料库， 对yarn list进行扫描。
    前置： yarn.lock
    支持JSON/文本报表
    优点： 报表和JSON格式相对更完善。


+ retirejs
    一个开源的依赖监测工具，提供命令行和Grunt, Firfox, Chrome等插件。从NIST NVD，漏洞追踪系统，博客和邮件列表等收集漏洞数据。
    Retire.js的目标是帮助您检测具有已知漏洞的版本的使用。
    原理：具有已知漏洞的包版本资源库。
    Retire.js从四个角度去扫描：
    + Library
    + From version
    + Up to version	 //最新版本
    + Links
    缺点：漏洞库相对不完善。

### 解决项目兼容性问题
> 有的项目用的yarn，有的npm，有的cnpm等。。。
我们需要抹平用不同工具得到的result输出差异！

> 以 yarn audit --json 的结果数据结构为基准

### 设计
以 yarn audit输出为基准格式，抹平三方面的差异
```json
{
    //...other-property
    "currentResult": {
        "type": "auditSummary",
        "data": {
            "vulnerabilities": {
                "info": 0,
                "low": 0,
                "moderate": 0,
                "high": 0,
                "critical": 0
            },
            "dependencies": 29,
            "devDependencies": 0,
            "optionalDependencies": 0,
            "totalDependencies": 29
    }
    // ...other-property
}


```

### 核心代码

### run shell

