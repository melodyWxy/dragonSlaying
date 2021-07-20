

# CMC
> CMC(CLI MANAGE CLI)! 命令行集成管理工具！

[toc]

## 背景 
>小明是某大厂的程序员，他善于使用各种命令行工具来实现他的某些需求———但随着公司以及社区的命令行工具安装的越来越多，他开始烦恼于如何记忆他们、管理他们； 而小明正在烦恼的时候，却不知道隔壁业务线又产出了新的命令行工具。

 + 小明期望有一个统一的入口，来集成和管理所有命令
 + 小明期望能方便的统一观察所有公司内产出的命令行工具，而不是单独的一个个了解它们。

而小明期望的，就是CMC的存在意义。

## 概念

### 命令行管理工具
> CMC，是用来管理命令行工具的命令行工具。这么说可能有点绕，也许下面的例子可以让你明白:

@melody-core/melody-cli 是 音巢组织(作者所在的npm开源组织) 的CMC工具，它可以方便的集成与管理任意其他的命令行工具————比如@melody-core/leo,一个脚手架命令行工具。 <br>
@melody-core/melody-leo 是可以单独被安装和使用的，就像这样: 
```shell
    # 安装
    yarn global add @melody-core/leo
    # 使用它的命令去初始化一个项目
    leo init 
```

但我们更倾向于用melody-cli去管理和使用它,就像这样:

```shell
    # 使用melody-cli安装它，前提是安装了@melody-core/melody-cli即可。
    melody install @melody-core/melody-leo
    # 使用leo脚手架   安装后，leo已被集成为melody-cli的扩展命令
    melody leo init 
    # 更新leo
    melody update leo
    # 卸载leo
    melody remove leo
```
也就是说，melody本身是个命令行集成与管理工具，它本身只包含对套件(也就是它安装的命令行工具)的增删改查的功能，但它可以基于已安装的套件去扩展它的能力和命令。

### 套件
每个符合melody-cli套件规范的npm包，都可以被视作为它的套件。套件可以理解为插件，可以扩展melody的命令，从而扩展melody的功能。当然，它们本身也都可以被当作一个独立的包。


## 设计
于是CMC的功能设计就自然而然了：
+ 对套件的增删改查命令  =>  集成与管理功能
+ 来自于套件的扩展命令  =>  扩展能力基于安装的套件

于是它的命令设计就： 
### 基础命令 - 增删改查

| 命令 | 描述 |  具体使用 | 补充  |
| -- | ----- | ----- | -------------|
|  search |  查询套件 |  melody search [package] | 不填参数时，默认查找所有音巢组织官方套件 |
|  install | 安装套件 | melody install <package> | package必填，例：melody install @melody-core/leo |
|  remove | 删除套件 | melody remove <package> | package必填，例：melody remove @melody-core/leo |
|  update | 更新套件 | melody update [package] | 不填参数时，默认更新所有已安装的套件 |

### 扩展命令 
当安装了任意命令行套件之后，CMC就应该集成了套件的命令功能。<br>

当然，你可能想给CMC增加更便捷的功能，比如自动同步版本进行更新等。

## 核心技术实现

+ commander
>commander是一个开源的、强大的命令行开发依赖。有关它的时候，你可以从它的官方文档了解： https://github.com/tj/commander.js/blob/HEAD/Readme_zh-CN.md

用commander去开发一个命令行工具，将会大大提升你的效率。尤其是前端，因为你可能更为熟悉node技术栈。

## 开发自定义套件
那么基于commander开发的套件，都可以集成为CMC的子命令。


## CMC工具示例
也许你可以尝试使用@melody-core/melody-cli,你会发现集成/管理命令行工具将会变得极其简单。

```shell
# 全局安装它即可
yarn global add @melody-core/melody-cli

```


```shell
wxy@melodyWxydeMacBook-Pro dragonSlaying % melody
Usage: melody [options] [command]

Options:
  -v, --version      查看当前版本
  -h, --help         display help for command

Commands:
  search             列出官方所有套件
  install <package>  安装套件
  remove <package>   删除套件
  update [package]   更新套件
  leo                初始化项目的脚手架命令工具
  help [command]     display help for command

wxy@melodyWxydeMacBook-Pro dragonSlaying % melody search
检索完毕!
┌─────────┬────────────────────┬─────────┬──────────────────────────────┬──────────┐
│ (index) │       plugin       │ version │             desc             │ install  │
├─────────┼────────────────────┼─────────┼──────────────────────────────┼──────────┤
│    0    │ '@melody-core/leo' │ '1.0.8' │ '初始化项目的脚手架命令工具' │ '已安装' │


wxy@melodyWxydeMacBook-Pro dragonSlaying % melody update  @melody-core/leo     
检索完毕
套件: @melody-core/leo 已是最新版本，version: 1.0.8

```


















