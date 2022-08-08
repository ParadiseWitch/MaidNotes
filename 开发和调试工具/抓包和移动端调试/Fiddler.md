---
title: 【移动端调试文档】  
author: Maid  
date: 2020-12-30 17:37  
categories: 移动端调试  
tags: [Fiddler]  [抓包]
---

<details><summary>目录</summary>

- [Fiddler](#fiddler)
- [下载Fiddler](#下载fiddler)
- [配置](#配置)
  - [Fiddler配置](#fiddler配置)
  - [移动端设备配置](#移动端设备配置)
- [更多操作](#更多操作)
  - [手动修改请求](#手动修改请求)
  - [断点调试](#断点调试)
  - [urlreplace](#urlreplace)

</details>

---

# Fiddler
移动端设备调试时使用Fiddler，可以让移动端设备的请求通过Fiddler进行代理。这样在无法修改移动端hosts的情况下，可以让移动端所有的请求和响应都经过PC端开发机的Fiddler完成请求。除此之外，Fiddler还可以抓包，对请求进行修改断点调试功能，是一款非常强大的工具。

# 下载Fiddler
- [Fiddler官网下载](https://www.telerik.com/download/fiddler)  

# 配置
## Fiddler配置
[官方教程](https://docs.telerik.com/fiddler/configure-fiddler/tasks/usefiddlerasreverseproxy)

在`Connections`配置一下Fiddler的端口，这里使用默认的8888，勾选上`Allow remote computers to connect`  

![](https://gitee.com/Maiiiiiid/picture_bed/raw/master/aigei/Fiddler移动端抓包配置.png)

在`General`下取消IPv6的勾选  
![](https://gitee.com/Maiiiiiid/picture_bed/raw/master/aigei/Fiddler移动端抓包.png)

## 移动端设备配置
移动端要与PC开发机端处于同一网络下  
设置网络：  
服务器是开发机在局域网中的ip  
端口填写上面设置的端口，这里是8888 

![](https://img-blog.csdn.net/20170927174922717?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbW1tbW1tbV8ybml1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)  


移动端打开浏览器，输入地址：开发机的ip:端口，例如[192.169.7.111:8888]()
点击`FiddlerRoot certificate`，下载并安装证书


---

# 更多操作
## 手动修改请求
很多时候为了调试可能需要修改请求信息，对于一个已经发送的请求，可以`F2`或者右键`Unlock for Editing`让请求进入可以编辑的状态，然后在右边的`Inspectors`中对想编辑的头信息右键Edit  

![](https://gitee.com/Maiiiiiid/picture_bed/raw/master/aigei/Fiddler移动端调试.png)  

就可以进行编辑，编辑完成之后，对请求右键`Replay -> Reissue Request` 重新发送编辑后的请求

## 断点调试
这种方式会拦截终端部分请求，可以在服务器接收到请求前就能修改请求信息  
在右下方的命令输入栏输入命令：
```
bpu www.baidu.com
```
> bpu是断点命令，后面的字符串表示url中包含该字符串的请求都会被拦截  
> 
![](https://gitee.com/Maiiiiiid/picture_bed/raw/master/aigei/20201230172345.png)  

进入断点的请求会显示这样的图标  

![](https://gitee.com/Maiiiiiid/picture_bed/raw/master/aigei/20201230172551.png)

在右侧你可以修改请求。黄色按钮表示返回响应后断点，绿色按钮表示直接通过。

![](https://gitee.com/Maiiiiiid/picture_bed/raw/master/aigei/20201230172941.png)

再次在右下方的命令输入栏输入命令 `bpu` 后就会取消断点

## urlreplace 
`urlreplace` 命令可以替换请求的url  
```
urlreplace oldstr newstr
```
再次输入`urlreplace`可以取消
> 注：`urlreplace` 不支持正则