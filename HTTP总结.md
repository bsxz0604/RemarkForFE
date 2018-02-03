# HTTP协议
#http协议 #curl  #http2
 
## 定义
HTTP( HyperText Transfer Protocol )超文本传输协议 ，是一种用于分布式、协作式和超媒体信息系统的*应用层*协议。设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法。通过HTTP或者HTTPS协议请求的资源由统一资源标识符（Uniform Resource Identifiers，URI）来标识。

## TCP/IP & HTTP
1. HTTP是如何建立连接的
首先我们要看下熟知的七层网络模型(计算机网络中的七层模型毕竟是理想中的情况，现实是很少有应用实现了七层模型，一般都是整合其中两个或多个，实现一个四层或者五层的模型)和TCP/IP的四层网络模型（TCP处于传输层，IP属于网络(际)层  ps:关于TCP/IP究竟是几层协议，目前为止没有定论，四层和五层都有，这里不展开讨论 ），如下图

![网络模型](https://github.com/bsxz0604/RemarkForYouDontKnowJs/blob/master/HTTP/网络模型.jpeg)

在这里我们研究的是HTTP，自然要知道它所处在哪个模型中，答案是应用层
因为HTTP是基于TCP/IP开发的协议，看过HTTP协议的同学肯定都知道，有句话概述HTTP协议为无差错的协议，按序传输，未分段的数据流，这其实说的就是TCP协议。
2. 当你在浏览器输入一个URL的时候，其中发生了什么？
	* 获取主机名
	* DNS 缓存/ 解析 获取服务器IP + 端口 (浏览器缓存，系统缓存，路由器缓存，IPS服务器缓存，根域名服务器缓存，顶级域名服务器缓存，主域名服务器缓存)
	* 连接到服务器 （这里其实是TCP连接）
	* 通过TCP信道发送一个HTTP请求
	* 服务器读取一个HTTP请求
	* 服务器查找所需资源并通过TCP信道返回资源
	* 关闭TCP连接

## 版本变迁
1. HTTP/0.9    已过时,只接受 GET 一种请求方法，没有在通讯中指定版本号，且不支持请求头.
2. HTTP/1.0    第一个在通讯中指定版本号的HTTP 协议版本。
3. *HTTP/1.1*     继承了HTTP1.0简单的特点，持久连接被默认建立，并能很好地配合代理服务器工作。还支持以管道方式在同时发送多个请求，以便降低线路负载，提高传输速度。
4. *HTTP/2.0*    2015年5月作为互联网标准正式发布。

## HTTP客户端请求和服务端响应（目前主流的HTTP1.1和HTTP2
* 请求组成
	1. 起始行(start line)
	请求方法 + 请求URI + 协议版本
	2. 报文首部（Header）
	3. 空行
	4. 报文主体
* 响应组成
	1. 起始行(start line)
	协议版本 + 状态码 + 状态嘛的原因短语
	2. 报文首部（Header）
	3. 空行
	4. 报文主体
* 请求方法
	* GET    —    获取资源
	* HEAD  —    获得报文首部
	* POST  --    传输实体文本
	* PUT    --    传输文件
	* DELETE    —    删除文件
	* CONNECT    —    要求用隧道协议连接代理
	* OPTIONS    —    询问支持的方法
	* TRACE    —    追踪路径
* 首部文件  *这一块图解HTTP的第六章有详细内容*
	* 通用首部
		1. 信息：Connection/Date/MIME-Version/Trailer/Update/Via
		2. 缓存：Cache-Control/Pragma
	* 请求首部
		1. 信息：Client-IP/From/Host/Referer/UA-Color/UA-CPU/UA-Disp/UA-OS/UA-Pixels/User-Agent
		2. Accept：Accept/Accept-Charset/Accept-Encoding/Accept-Language/TE
		3. 条件请求：Expect/If-Match/If-Modified-Since/If-None-Match/If-Range/If-Unmodified-Since/Range
		4. 安全请求：Authorization/Cookie/Cookie2
		5. 代理请求：Max-Forward/Proxy-Authorization/Proxy-Connection
	* 响应首部
		1. 信息：Age/Public/Retry-After/Server/Title/Warning
		2. 协商：Accept-Ranges/Vary
		3. 安全响应：Proxy-Authorization/Set-Cookie/Set-Cookie2/WWW-Authenticate
	* 实体首部
		1. 信息：Allow/Location
		2. 内容：Content-Base/Content-Encoding/Content-Language/Content-Length/Content-Location/Content-MD5/Content-Range/Content-Type
		3. 实体缓存：ETag/Expires/Last-Modified
* 响应状态码
	* 100  Continue  继续，一般在发送post请求时，已发送了http header之后服务端将返回此信息，表示确认，之后发送具体参数信息
	* 200  OK   正常返回信息
	* 201  Created  请求成功并且服务器创建了新的资源
	* 202  Accepted  服务器已接受请求，但尚未处理
	* 206  Partial Content  响应报文包含了多个范围内的内容
	* 301  Moved Permanently  请求的网页已永久移动到新位置。
	* 302 Found  临时性重定向。
	* 303 See Other  临时性重定向，且总是使用 GET 请求新的 URI。
	* 304  Not Modified  自从上次请求后，请求的网页未修改过。
	* 400 Bad Request  服务器无法理解请求的格式，客户端不应当尝试再次使用相同的内容发起请求。
	* 401 Unauthorized  请求未授权。
	* 403 Forbidden  禁止访问。
	* 404 Not Found  找不到如何与 URI 相匹配的资源。
	* 500 Internal Server Error  最常见的服务器端错误。
	* 503 Service Unavailable 服务器端暂时无法处理请求（可能是过载或维护）。

## 各版本简介
### HTTP/1.0
* 早先的HTTP/1.0是一种无状态、无连接的应用层协议。规定浏览器和服务器保持短暂的连接，浏览器的每次请求都需要与服务器建立一个TCP连接，服务器处理完成后立即断开TCP连接（无连接），服务器不跟踪每个客户端也不记录过去的请求（无状态）
* 这种无状态性可以借助cookie/session机制来做身份认证和状态记录。而下面两个问题就比较麻烦了。
	1. 首先，无连接的特性导致最大的性能缺陷就是无法复用连接。每次发送请求的时候，都需要进行一次TCP的连接，而TCP的连接释放过程又是比较费事的。这种无连接的特性会使得网络的利用率非常低。
	2. 其次就是就是队头阻塞（head of line blocking）。由于HTTP1.0规定下一个请求必须在前一个请求响应到达之前才能发送。假设前一个请求响应一直不到达，那么下一个请求就不发送，同样的后面的请求也给阻塞了。
### HTTP/1.1
* 长连接，通过设置*Keep-Alive*可以保持HTTP连接不断开，避免了每次客户端与服务器请求都要重复建立释放建立TCP连接，提高了网络的利用率。可以在请求头中携带Connection: false来告知服务器关闭请求。
* 支持请求管道化（pipelining）。 基于长连接，使得请求管线化成为可能。管线化使得请求能够并行传输。举个例子来说，假如响应的主体是一个html页面，页面中包含了很多img，这个时候keep-alive就起了很大的作用，能够进行并行发送多个请求。（客户端依据域名来向服务器建立连接，一般PC浏览器会针对单个域名的服务器同时建立6 ~ 8个连接，手机端一般控制在4 ~ 6个。这也是为什么很多大型网站设置不同的静态资源CDN域名来加载资源。）
需要注意的是，服务器必须按照客户端请求的先后顺序依次回送相应的结果，以保证客户端能够区分出每次请求的响应内容。
也就是说，HTTP管道化可以让我们把先进先出队列从客户端（请求队列）迁移到服务端（响应队列）。

![http请求图](https://github.com/bsxz0604/RemarkForYouDontKnowJs/blob/master/HTTP/http请求图.png)

如图所示，客户端同时发了两个请求分别来获取html和css，假如说服务器的css资源先准备就绪，服务器也会先发送html再发送css。
同时，管道化技术只是使得客户端能够往一个服务器同时发送一组请求，假若客户端想往这个相同的服务器发起另一组请求，也必须等待上一组请求全部响应完毕。
可见，*HTTP1.1解决队头阻塞（head of line blocking）还不彻底*。
* 缓存处理，支持断点传输，以及增加了Host字段（使得一个服务器能够用来创建多个Web站点）。
### HTTP/2.0
[HTTP/2 is the future of the Web](https://http2.akamai.com/demo)
 这是 Akamai 公司建立的一个官方的演示，用以说明 HTTP/2 相比于之前的 HTTP/1.1 在性能上的大幅度提升。 同时请求 379 张图片，从Load time 的对比可以看出 HTTP/2 在速度上的优势。
顺口提一句 HTTP/2 协议是从 SPDY 演变而来，SPDY 已经完成了使命并很快就会退出历史舞台（例如 Chrome 将在「2016 年初结束对 SPDY 的支持」；Nginx 已经正式支持 HTTP/2 后，也不再支持 SPDY）
* 二进制分帧
HTTP2.0通过在应用层和传输层之间增加一个二进制分帧层，突破了HTTP1.1的性能限制、改进传输性能。

![二进制分帧](https://github.com/bsxz0604/RemarkForYouDontKnowJs/blob/master/HTTP/二进制分帧.jpeg)

可见，虽然HTTP2.0的协议和HTTP1.x协议之间的规范完全不同了，但是实际上HTTP2.0并没有改变HTTP1.x的语义。 
简单来说，HTTP2.0只是把原来HTTP1.x的header和body部分用frame重新封装了一层而已。
* 多路复用
下面是几个概念：
	* 流（stream）：已建立连接上的双向字节流。
	* 消息：与逻辑消息对应的完整的一系列数据帧。
	* 帧（frame）：HTTP2.0通信的最小单位，每个帧包含帧首部，至少也会标识出当前帧所属的流（stream id）。

![多路复用](https://github.com/bsxz0604/RemarkForYouDontKnowJs/blob/master/HTTP/多路复用.jpeg)

从图中可见，所有的HTTP2.0通信都在一个连接上完成，这个连接可以承载任意数量的双向数据流。
每个数据流以消息的形式发送，而消息由一或多个帧组成。这些帧可以乱序发送，然后再根据每个帧首部的流标识符（stream id）重新组装。
举个例子，每个请求是一个数据流，数据流以消息的方式发送，而消息又分为多个帧，帧首部记录着stream id用来标识所属的数据流，不同属的帧可以在连接中随机混杂在一起。接收方可以根据stream id将帧再归属到各自不同的请求当中去。
* 请求优先级
多路复用导致所有资源都是并行发送，可能会导致关键请求被阻塞。那么就需要「优先级」的概念了，这样就可以对重要的文件进行先传输，加速页面的渲染。
* 首部压缩
在HTTP1.x中，首部元数据都是以纯文本的形式发送的，通常会给每个请求增加500~800字节的负荷。
HTTP/2.0规定了在客户端和服务器端会使用并且维护「首部表」来跟踪和存储之前发送的键值对，对于相同的头部，不必再通过请求发送，只需发送一次。
事实上,如果请求中不包含首部（例如对同一资源的轮询请求），那么首部开销就是零字节。此时所有首部都自动使用之前请求发送的首部。
如果首部发生变化了，那么只需要发送变化了数据在Headers帧里面，新增或修改的首部帧会被追加到“首部表”。首部表在 HTTP2.0的连接存续期内始终存在,由客户端和服务器共同渐进地更新。

![首部压缩](https://github.com/bsxz0604/RemarkForYouDontKnowJs/blob/master/HTTP/首部压缩.png)

比如说cookie，默认情况下，浏览器会在每次请求的时候，把cookie附在header上面发送给服务器。（由于cookie比较大且每次都重复发送，一般不存储信息，只是用来做状态记录和身份认证）
* 服务器推送
服务器除了对最初请求的响应外，服务器还可以额外的向客户端推送资源，而无需客户端明确的请求。
在  [HTTP/2官网](https://http2.github.io/) 可以找到更多有关 HTTP/2 协议的资料。
* HTTP/2 的协议协商机制？
浏览器	服务器	协商结果
不支持 HTTP/2	不支持 HTTP/2	不协商，使用 HTTP/1.1
不支持 HTTP/2	支持 HTTP/2		不协商，使用 HTTP/1.1
支持 HTTP/2		不支持 HTTP/2	协商，使用 HTTP/1.1
支持 HTTP/2		支持 HTTP/2		协商，使用 HTTP/2

## HTTPS
1. HTTPS, 全称Hyper Text Transfer Protocol Secure。相比HTTP，多了一个Secure,这是由TLS（SSL）提供的。 当然也可以理解为：HTTPS = HTTP + 加密 + 认证 + 完整性保护。
2. SSL(Secure Sockets Layer 安全套接层),及其继任者传输层安全（Transport Layer Security，TLS）是为网络通信提供安全及数据完整性的一种安全协议。TLS 与 SSL 在传输层对网络连接进行加密。
[图解SSL/TLS协议 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2014/09/illustration-ssl.html) 这一篇阮老师的内容写的不错.就是时间有点久了，目前TLS 1.3已经上线了，感兴趣的可以了解下，这个也属于性能优化的一部分。（这里就大致的说下：TLS 1.2：需要两次往返才能完成握手，然后才能发送请求。通过移动网络访问网站可以在加载时间上增加超过半秒的时间。TLS 1.3：初始握手会减半，只需要一次往返…）
3. 传输过程：
	* HTTP => TCP => IP
	* HTTP => SSL/TLS => TCP => IP

## CURL
这个其实是题外话，因为自己调试很少用到CURL，了解了后才发现这东西真的很好用，所以也就带在这里写了进去，当给自己记录下
用法也很简单   `curl [options...] <url>`
具体的options可以用curl -help 去查看，这里我就说我几个会用到的
* -H/--header <line>	自定义头信息传递给服务器
* -X/--request <command>	指定什么命令
* -d/--data <data>	HTTP POST方式传送数据
* -e/--referer	来源网址


## 总结
如果没有时间看，直接看这里和第一张图，我尽可能的总结了我觉得好的东西。
* HTTP是一种无状态、无连接的*应用层*协议
* 现在主流的在使用的是HTTP/1.1 （特点：持久链接，增加缓存处理，支持断点传输）和 HTTP/2（特点：二进制分帧，首部压缩，多路复用，服务端推送）
* HTTPS  是由TLS（SSL）提供安全的HTTP协议


## 文档参考
总结这篇学习笔记也是从很多大神的文章中和书里找到的，感兴趣的同学可以去看下，更深的研究。欢迎大家一起来讨论学习。当然我写的有问题的地方，也麻烦各位帮助指出,谢谢。
1. 《图解 HTTP》和《HTTP 权威指南》 我是先看了图解HTTP之后读的文章。最后粗看了一下HTTP 权威指南，这本书很优秀，但是其实比较厚，所以一上来阅读比较困难，建议看图解HTTP，图片可爱，介绍的也清楚，关键这本书页数少….
2. [http2讲解 · GitBook](https://www.gitbook.com/book/ye11ow/http2-explained/details)  中文翻译…
3. [HTTP1.0 HTTP1.1 HTTP2.0 主要特性对比 - 前端的那些事儿 - SegmentFault 思否](https://segmentfault.com/a/1190000013028798)
4. Mark Nottingham 在 Velocity Beijing 2015  的 speech  ,关于 HTTP/2 下的前端性能优化相关  [HTTP/2 for Front-End Developers](https://www.mnot.net/talks/h2fe/)
5. [专题 | JerryQu 的小站](https://imququ.com/post/series.html)
6. [HTTP1.0 HTTP1.1 HTTP2.0 主要特性对比 - 前端的那些事儿 - SegmentFault 思否](https://segmentfault.com/a/1190000013028798)
