---
title: http缓存机制
date: 2019-09-16 07:15:21
tags:
---
## 使用缓存的意义

重用已获取的资源能够有效的提升网站与应用的性能。Web 缓存能够减少延迟与网络阻塞，进而减少显示某个资源所用的时间。借助 HTTP 缓存，Web 站点变得更具有响应性。

## 常见请求header

``` http
GET /index.html HTTP/1.1 #请求的方法，URI和http版本
Host: developer.mozilla.org:80 #请求的主机和端口
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9) Gecko/20100101 Firefox/50.0 #userAgent
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8 #客户端接受的类型
Accept-Language: en-US,en;q=0.5 #客户端接受的语言
Accept-Encoding: gzip, deflate, br #客户端接受的压缩编码类型
Referer: https://developer.mozilla.org/testpage.html #请求来源
Connection: keep-alive #http请求的特点
Cookie: #客户端发起请求所携带的cookie
If-Modified-Since: Mon, 18 Jul 2016 02:36:04 GMT #客户端缓存资源的时间(上一次请求响应的last-modified)
If-None-Match: "c561c68d0ba9" #客户端缓存的资源的etag(上一次请求响应的etag)
Cache-Control: max-age=0 #缓存时间，单位为秒
```

## 强制缓存和协商缓存

强制缓存和协商缓存(对比缓存)可以共同存在，但强制缓存的优先级高于协商缓存。

### 强制缓存

当header中有expires(HTTP/1.0)或者cache-cootrol(HTTP/1.1)字段时，当缓存有效时，则会直接从缓存中读取对应资源，不会去请求服务器的资源（如果缓存无效的话，还是会请求服务器资源）。

### 协商缓存

服务器设置response header中的etag和last-modified字段。客户端发起请求时，会通过if-none-match和if-modified-since两个字段告诉服务器，如果资源尚未过期，服务器会响应304，告知客户端使用缓存；如果服务器的资源有更新或者缓存失效，那么服务器会返回新的资源，响应200。
协商缓存有etag/if-none-match和last-modified/if-modified-since两套规则，其中etag/if-none-match的优先级高于last-modified/if-modifed-since。无论怎样，协商缓存都会和服务器进行交互。
