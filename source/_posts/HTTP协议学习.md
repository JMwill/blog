title: HTTP协议学习
date: 2015-10-16 21:11:44
tags:
    - 笔记
    - web
---

## HTTP协议

HTTP协议属于应用层的协议，由于简捷、快速等原因适用于web，对于HTTP协议来说，它需要建立在能提供可靠交付的底层协议上，而TCP/IP协议正好满足，于是，HTTP协议基本都是建立在TCP/IP上。同时，HTTP协议属于无状态协议，无状态表明，协议对于事务处理没有记忆能力，一次链接就是一次链接，与上一次以及下一次链接没有关系。

<!--more-->

## HTTP请求

HTTP请求是进行web开发时需要去了解的概念，其中请求由三部分组成，分别是：请求行、消息报头、请求正文。一个普通的请求例子：`GET /index.html HTTP/1.1 (CRLF)`。

请求行以一个方法符号开头，以空格分开，后面跟着请求的URI和协议的版本，格式如下：`Method Request-URI HTTP-Version CRLF`。其中 **Method**表示请求方法；**Request-URI**是一个统一资源标识符；**HTTP-Version**表示请求的HTTP协议版本；**CRLF**表示回车和换行（除了作为结尾的CRLF外，不允许出现单独的CR或LF字符）

各种请求方法：

+ GET     请求获取Request-URI所标识的资源
+ POST    在Request-URI所标识的资源后附加新的数据
+ HEAD    请求获取由Request-URI所标识的资源的响应消息报头
+ PUT     请求服务器存储一个资源，并用Request-URI作为其标识
+ DELETE  请求服务器删除Request-URI所标识的资源
+ TRACE   请求服务器回送收到的请求信息，主要用于测试或诊断
+ CONNECT 保留将来使用
+ OPTIONS 请求查询服务器的性能，或者查询与资源相关的选项和需求

## HTTP响应

HTTP响应从服务器发出，也是由三个部分组成，分别是：状态行、消息报头、响应正文，格式为`HTTP-Version Status-Code Reason-Phrase CRLF`，其中，**HTTP-Version**表示服务器HTTP协议的版本；**Status-Code**表示服务器发回的响应状态代码；**Reason-Phrase**表示状态代码的文本描述。

其中，响应状态码中我们最熟悉的莫过于404，这就是一个HTTP响应状态码，一般代表请求不存在，而常见的响应状态码可以分为5类（详细的状态码解释在[这里][http status code]）

- 1xx：指示信息--表示请求已接收，继续处理
- 2xx：成功--表示请求已被成功接收、理解、接受
- 3xx：重定向--要完成请求必须进行更进一步的操作
- 4xx：客户端错误--请求有语法错误或请求无法实现
- 5xx：服务器端错误--服务器未能实现合法的请求

## HTTP消息报头

HTTP消息由客户端到服务器的请求和服务器到客户端的响应组成。请求消息和响应消息都是由开始行（对于请求消息，开始行就是请求行，对于响应消息，开始行就是状态行），消息报头（可选），空行（只有CRLF的行），消息正文（可选）组成。

HTTP消息报头包括普通报头、请求报头、响应报头、实体报头。
每一个报头域都是由名字+":"+空格+值 组成，消息报头域的名字是与大小写无关的。

在这里记录一下每种报头的作用：

1. 普通报头（在普通报头中，有少数报头域用于所有的请求和响应消息，但并不用于被传输的实体，只用于传输的消息。）
    * Cache-Control: 用于指定缓存指令，HTTP1.0使用的类似的报头域为**Pragma**。请求时的缓存指令包括：no-cache（用于指示请求或响应消息不能缓存）、no-store、max-age、max-stale、min-fresh、only-if-cached;响应时的缓存指令包括：public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age、s-maxage.
    * Date：普通报头域表示消息产生的日期和时间
    * Connection：普通报头域允许发送指定连接的选项。例如指定连接是连续，或者指定“close”选项，通知服务器，在响应完成后，关闭连接
2. 请求报头
    * Accept：用于指定客户端接受哪些类型的信息。如：image/gif，表明客户端希望接受GIF图象格式的资源
    * Accept-Charset：用于指定客户端接受的字符集。如果在请求消息中没有设置这个域，缺省是任何字符集都可以接受。
    * Accept-Encoding：类似于Accept，但是它是用于指定可接受的内容编码
    * Accept-Language：类似于Accept，但是它是用于指定一种自然语言。
    * Authorization：主要用于证明客户端有权查看某个资源。当浏览器访问一个页面时，如果收到服务器的响应代码为401（未授权），可以发送一个包含Authorization请求报头域的请求，要求服务器对其进行验证。
    * Host：主要用于指定被请求资源的Internet主机和端口号，发送请求时，该报头域是必需的。
    * User-Agent：允许客户端将它的操作系统、浏览器和其它属性告诉服务器。
3. 响应报头（响应报头允许服务器传递不能放在状态行中的附加响应信息，以及关于服务器的信息和对Request-URI所标识的资源进行下一步访问的信息。）
    * Location：用于重定向接受者到一个新的位置。
    * Server：包含了服务器用来处理请求的软件信息。
4. 实体报头 （请求和响应消息都可以传送一个实体。一个实体由实体报头域和实体正文组成，但并不是说实体报头域和实体正文要在一起发送，可以只发送实体报头域。实体报头定义了关于实体正文和请求所标识的资源的元信息。）
    * Content-Encoding：被用作媒体类型的修饰符，它的值指示了已经被应用到实体正文的附加内容的编码，因而要获得Content-Type报头域中所引用的媒体类型，必须采用相应的解码机制。Content-Encoding这样用于记录文档的压缩方法。
    * Content-Language：描述了资源所用的自然语言。
    * Content-Length：用于指明实体正文的长度。
    * Content-Type：用于指明发送给接收者的实体正文的媒体类型
    * Last-Modified：用于指示资源的最后修改日期和时间
    * Expires：给出响应过期的日期和时间，指定页面过期的时间。

关于HTTP协议就简单介绍到这里，本文章参考于：[这里][article reference]

[http status code]:         https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81
[article reference]:        http://blog.csdn.net/gueter/article/details/1524447