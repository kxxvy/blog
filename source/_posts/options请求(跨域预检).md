---
title: options请求(跨域预检)
date: 2023-05-24
tags: [跨域]
---

## 背景

我们公司前端和后端都从 0 到 1 开始搭建项目

在接口联调的过程中遇到了一个奇怪的问题

明明调用了一次接口

可在浏览器上却可以看到两次 options 请求

<!-- more -->

## 概述

> options 请求就说预检请求，可用于检查服务器允许的 http 方法。当发起跨域请求时，由于安全原因，触发一定条件时浏览器会在正式请求之前自动先发起 OPTIONS 请求，即 CORS 预检请求，服务器若接受该跨域请求，浏览器才继续发起正式请求。

### 一、什么是 options 请求

> HTTP 的 OPTIONS 方法用于获取目的资源所支持的通信选项。客户端可以对特定的 URL 使用 OPTIONS 方法，也可以对整站（通过将 URL 设置为"\*"）使用该方法。（简而言之，就是可以用 options 请求去嗅探某个请求在对应的服务器中都支持哪种请求方法）。

**原因**

> 这是因为在跨域的情况下，在浏览器发起"复杂请求"时主动发起的。跨域共享标准规范要求，对那些可能对服务器数据产生副作用的 HTTP 请求方法（特别是 GET 以外的 HTTP 请求，或者搭配某些 MINE 类型的 POST 请求），浏览器必须首先使用 OPTIONS 方法发起一个预检请求（preflight request），从而获知服务端是否允许该跨域请求。服务器确认允许之后，才发起实际的 HTTP 请求。

#### 二、简单请求和复杂请求

> 某些请求不会触发 CORS 预检请求，这样的请求一般称为"简单请求"，而会触发预检的请求则是"复杂请求"。

**简单请求**

- 请求方式为 GET、HEAD、POST 时的请求；
- 认为设置规范集合之内的首部字段，如 Accept/Accept-Language/Content-Language/Content-Type/DPR/Downlink/Save-Data/Viewport-Width/Width；
- Content-Type 的值仅限于下列三者之一，即 application/x-www-form-urlencoded、multipart/form-data、text-plain；
- 请求中的任意 XMLHttpRequestUpload 对象均没有注册任何事件监听器；
- 请求中没有使用 ReadableStream 对象。

**复杂请求**

- PUT/DELETE/CONNECT/OPTIONS/TRACE/PATCH；
- 人为设置了以下集合之外的首部字段，即简单请求外的字段；
- Content-Type 的值不属于下列之一，即 application/x-www-form-urlencoded、multiart/form-data、text-plain。

### 三、options 关键字

**request header 的关键字**

|            关键字段            |                      作用                      |
| :----------------------------: | :--------------------------------------------: |
| Access-Control-Request-Method  |      告知服务器，实际请求将使用 POST 方法      |
| Access-Control-Request-Headers | 告知服务器，实际请求将携带的自定义请求首部字段 |

**response header 的关键字**

|           关键字段           |                     作用                     |
| :--------------------------: | :------------------------------------------: |
| Access-Control-Allow-Methods |   表明服务器允许客户端使用什么方法发起请求   |
| Access-Control-Allow-Origin  | 允许跨域请求的域名，如果要允许所有域名则为\* |
| Access-Control-Allow-Headers |     将实际请求所携带的首部字段告诉服务器     |
|    Access-Control-Max-Age    |       指定了预检请求的结果能被缓存多久       |

### 四、options 请求优化

> 当发起跨域请求时，简单请求只发起一次请求；复杂请求则需要两次，先发起 options 请求，确认目标资源是否支持跨域，浏览器会根据服务端响应的 header 自动处理剩余的请求，如果响应支持跨域，则继续发出正常请求；不支持的话，会在控制台显示错误。所以，当触发预检时，跨域请求便会发送两次请求，增加请求次数，同时，也延迟了请求真正发起的时间，会严重地影响性能。

**优化 options 请求的两种方法**

- 方法一：用其它的跨域方式做跨域请求，将复杂请求转化为简单请求，比如 JSONP 等；
- 方法二：对 options 请求进行缓存。服务端设置 Access-Control-Max-Age 字段，那么当第一次请求该 URL 时会发出 OPTIONS 请求，浏览器会根据返回的 Access-Control-Max-Age 字段缓存该请求的 OPTIONS 预检请求的响应结果（具体缓存时间还取决于浏览器的支持的默认最大值，取两者最小值，一般为 10 分钟）。在缓存有效期内，该资源的请求（URL 和 header 字段都相同的情况下）不会再触发预检。（chrome 打开控制台可以看到，当服务器响应 Access-Control-Max-Age 时只有第一次请求会有预检，后面不会了。注意开启缓存，去掉 disable cache 勾选。）
