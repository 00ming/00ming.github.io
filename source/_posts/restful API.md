---
title: RESTful API
date: 2018-09-18 15:30:43
tags: API
---
# RESTful API
表现层状态转换（REST，英文：Representational State Transfer）是Roy Thomas Fielding博士于2000年在他的博士论文 中提出来的一种万维网软件架构风格，目的是便于不同软件/程序在网络（例如互联网）中互相传递信息。表现层状态转换（REST，英文：Representational State Transfer）是根基于超文本传输协议(HTTP)之上而确定的一组约束和属性，是一种设计提供万维网络服务的软件构建风格。匹配或兼容于这种架构风格(简称为 REST 或 RESTful)的网络服务，允许客户端发出以统一资源标识符访问和操作网络资源的请求，而与预先定义好的无状态操作集一致化。==需要注意的是，REST是设计风格而不是标准==.

[要理解RESTful架构，最好的方法就是去理解Representational State Transfer这个词组到底是什么意思，它的每一个词代表了什么涵义](http://www.ruanyifeng.com/blog/2011/09/restful.html)
<!-- more -->
## 
### 看http method就知道干什么
##### GET
* 安全且幂等 (幂等的介绍可以参考:[编程中的幂等性 — HTTP幂等性](https://www.cnblogs.com/duhuo/p/4245202.html))
* 获取表示
* 可以被缓存
##### POST
* 不安全且不幂等
* 使用服务端管理的（自动产生）的实例号创建资源
* 创建子资源
* 部分更新资源
* 如果没有被修改，则不更新资源（[乐观锁](https://baike.baidu.com/item/%E4%B9%90%E8%A7%82%E9%94%81/7146502)）
##### PUT
* 不安全但幂等
* 用客户端管理的实例号创建一个资源  
* 通过替换的方式更新资源  
* 如果未被修改，则更新资源（乐观锁）
##### DELETE
* 不安全但幂等
* 删除资源

##### POST 和 PUT 都可以用于创建和修改资源，它们的区别是什么呢？
最大的区别就是 `PUT` 是幂等 而 `POST`不是
这就决定的他们是使用上的区别

在创建资源时，`PUT`可以指定资源路径，`POST`无法指定资源路径。
因而，`PUT`是幂等的操作，即重复操作不会产生变化，10次`PUT` 的创建请求与1次`PUT` 的创建请求相同，只会创建一个资源，其实后面9次的请求只是对已创建资源的更新，且更新内容与原内容相同，所以不会产生变化。相当于`x = 5`执行多少次`x`的值都是5

`POST` 的重复操作截然不同，10次`POST`请求将会创建10个资源。相当于`x ++`,每次执行`x`的值都会变化
    


简单来说
```js
GET     获取一个资源 
POST    添加一个资源 
PUT     修改一个资源 
DELETE  删除一个资源 
```
### 看Url就知道要什么
REST 是面向资源的，这个概念非常重要，而资源是通过 URI 进行暴露。
http method是动词表示要做什么,URL中只使用名词来指定资源且推荐用复数,表示动作的对象.
在知乎上看到这么一段[原文](https://www.zhihu.com/question/28557115/answer/47846156)
```js
GET /rest/api/getDogs --> GET /rest/api/dogs 获取所有小狗狗 
GET /rest/api/addDogs --> POST /rest/api/dogs 添加一个小狗狗 
GET /rest/api/editDogs/:dog_id --> PUT /rest/api/dogs/:dog_id 修改一个小狗狗 
GET /rest/api/deleteDogs/:dog_id --> DELETE /rest/api/dogs/:dog_id 删除一个小狗狗
```
`/rest/api/dogs/`只表示资源的地址,通过http method来确定操作
### 看http status code就知道结果如何

使用正确的HTTP Status Code表示访问状态：[HTTP/1.1: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)


设计方法可以学习阮一峰老师的
[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

### 参考文章
- [理解RESTFul架构 | 小毛的胡思乱想](https://mccxj.github.io/blog/20130530_introduce-to-rest.html)
- [怎样用通俗的语言解释REST，以及RESTful？ - 知乎](https://www.zhihu.com/question/28557115)
- [Restful API 以及对 Put/Post 区别理解 - CSDN博客](https://blog.csdn.net/maxmao1024/article/details/79591726)
- [如何给老婆解释什么是RESTful](https://zhuanlan.zhihu.com/p/30396391)