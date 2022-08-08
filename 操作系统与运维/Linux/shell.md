---
title: 【shell】  
author: Maid  
date: 2020-12-28 22:01  
categories: shell  
tags: [shell]  

---

<details><summary>目录</summary>

- [.bashrc](#bashrc)
  - [termux中自定义快捷命令](#termux中自定义快捷命令)

</details>

---

## .bashrc
`.bashrc`位于`~/.bashrc`, 它可以简写命令.  
例如:
```bash
alias cls='clear'
```
保存后 `source .bashrc`
以后再输入`cls`执行的就是`clear`

### termux中自定义快捷命令
[参考链接](https://blog.csdn.net/qq_41433232/article/details/107528060)  
同理也是修改`~/.bashrc`  
只不过需要先修改`../usr/etc/profile`文件, 添加代码:
```bash
if test -f .bashrc ; then
  source .bashrc
fi
```