---
title: 【移动端web调试】title2  
author: Maid  
date: 2020-12-31 15:35  
categories: categories  
tags: [tags]  

---
<a id="toc"></a>
<details><summary>目录</summary>

- [日志输出](#日志输出)
  - [vconsole](#vconsole)
    - [安装](#安装)
    - [引用](#引用)
    - [eruda](#eruda)
    - [安装](#安装-1)
- [真机测试](#真机测试)
    - [remotedebug-ios-webkit-adapter](#remotedebug-ios-webkit-adapter)
    - [安装](#安装-2)
    - [移动端准备](#移动端准备)
    - [启动服务](#启动服务)
    - [Fiddler](#fiddler)
- [补充](#补充)

</details>

---



# 日志输出
## vconsole
[回到目录👆](#toc)
 
[官方地址](https://github.com/Tencent/vConsole)
### 安装

```bash
npm install vconsole
```


### 引用

```html
<script src="path/to/vconsole.min.js"></script>
<script>
  // init vConsole
  var vConsole = new VConsole();
  console.log('Hello world');
</script>
```

或者node方式：

```js
import Vconsole from 'vconsole';
new Vconsole();
```

TS：
```ts
import 'path/to/vconsole.min.d.ts';
```

### eruda
[回到目录👆](#toc)

[官方文档](https://github.com/liriliri/eruda/blob/master/doc/README_CN.md)

### 安装
可以直接使用cdn：
```html
<script src="//cdn.jsdelivr.net/npm/eruda"></script>
<script>eruda.init();</script>
```



# 真机测试
### remotedebug-ios-webkit-adapter
[回到目录👆](#toc)

[官方文档](https://github.com/RemoteDebug/remotedebug-ios-webkit-adapter)

`remotedebug-ios-webkit-adapter`可以使用 `Chrome DevTool` 调试ios设备

### 安装
[回到目录👆](#toc)

首先需要安装`ios-webkit-debug-proxy`

```bash
scoop bucket add extras
scoop install ios-webkit-debug-proxy
```

安装`remotedebug-ios-webkit-adapter` 

```bash
npm install remotedebug-ios-webkit-adapter -g
```
### 移动端准备
[回到目录👆](#toc)
 
打开`IOS设置 => Safari 首选项 => 开启 "Web检查"`
然后安装`iTunes`，在IOS设备上"信任此设备"

### 启动服务
[回到目录👆](#toc)

```bash
remotedebug_ios_webkit_adapter --port=9000
```
然后打开 <mark> Chrome </mark> (建议使用Chrome，edge可能不会响应)，输入：[chrome://inspect/#devices](chrome://inspect/#devices)，选择要调试的页面点击inspect就能够进行调试

### Fiddler
[<sub>回到目录👆<sub>](#toc)

查看 [`Fiddler`](./Fiddler.md) 页面

---
# 补充 
[<font style="font-size:14px">`回到目录👆`</font>](#toc)

- ios设备进行调试时，每当前台改动一次，可能都需要新建一个页面重新访问。不然在当前页面刷新的话，Safari可能会保留有之前的缓存。




