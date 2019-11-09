---
layout:post
title:HTTP，HTTPS
descriptions:HTTP、HTTPS相关原理
tag:安全
---


# HTTP，HTTPS

### 幂等性
- 指对于同一个接口的调用，一次或多次得到的结果是完全一样的。
### Request Method(常用)
| Method | Description                       | 幂等性 |
| ------ | --------------------------------- | ------ |
| GET    | 获取数据，理论上无Body            | 有     |
| POST   | 增加或修改数据，有Body            | 无     |
| PUT    | 修改数据 ，有Body                 | 有     |
| DELETE | 删除数据，不发送Body              | 有     |
| HEAD   | 获取数据，GET唯一的区别就是无Body | 有     |

###  Response Status Code
- 1XXXX : 临时性消息
- 2XXXX : 成功
- 3XXXX : 重定向
  - 301 : 永久性迁移重定向，表示旧地址A的资源已经被永久地移除了（这个资源不可访问了），搜索引擎在抓取新内容的同时也将旧的网址交换为重定向之后的网址。
  - 302 : 暂时性迁移重定向，表示旧地址A的资源还在（仍然可以访问），这个重定向只是临时地从旧地址A跳转到地址B，搜索引擎会抓取新的内容而保存旧的网址。
  - 304 : 内容无改变，body返回为空，可以使用之前的缓存
- 4XXXX : 客户端错误
  - 404(Page Not Found)
  - ....
- 5XXXX : 服务器错误
### 请求报文
- #### 请求行: Method Path Http Version
  如：GET /member HTTP/1.1

- #### Header（Http消息的元数据{meta data}）
- #### Body
### 响应报文
- #### 状态行 HttpVersion statuscode status message
    如：HTTP/1.1 200 OK
- #### Header
- #### Body

###Headers：Http消息的元数据{meta data}
  - Host:用于定位主机具体的服务器，如(gank.io)
  - Content-Type:Body内容类型

| Content-Type                      | Description                                                  | Sample |
| --------------------------------- | ------------------------------------------------------------ | ------ |
| application/json                  | json就不必多说了，用得都懂                                   | {}     |
| application/x-www-form-urlencoded | 普通表单格式                                                 |        |
| multipart/form-data               | multipart 用于多部分表单数据，一般用于二进制数据上传，Part之间使用 boundary 进行分割 |        |
| text/html                         | 富文本                                                       |        |

- Location:重定向地址，出现在Response Header中，表示重定向到的地址。
- User-Agent:用户代理，实际访问者，如PC浏览器，手机客户端，服务器可通过此信息判断实际设备，返回不同的界面。
- Range/Accept-Range: 指定Body的内容范围，如Range:bytes=0-30000
- Cookie/Set-Cookie: 发送Cookie / 设置Cookie
- Authorization: 授权信息
- Accept: 客户端能接受的数据类型。如text/html 等
- Accept-Charset:客户端能接受的字符集。如utf-8
- Accept-Encoding: 客户端能接受的编码类型。如gzip、Base64
- Content-Encoding: 压缩类型。如 gzip
- Cache
  - Cache-Control
    - no-cache:可以缓存，但需要先询问缓存是否失效
    - no-store:无缓存
    - max-age: 会给出一个时间，时间没到，无需询问服务器，直接使用缓存
  - Last Modified:最近修改时间
    - If-Modified-Since :确认据最近修改时间后无修改，直接使用缓存
  - Etag:指纹标记
    - If-None-Match:如果无匹配，用新的
 - Buffer:缓冲，备用（）
### Body：具体的数据内容

###:讲义

## 现代密码学
可用于文字或二进制内容的加密传输。
- #### 对称加密
  - 原理：使用**密钥**通过**加密算法**对数据进行转换，得到无意义的内容即为**密文**；使用**密钥**通过**解密算法**对密文进行逆向转换，得到原数据。
  - 经典算法：DES、AES
- #### 非对称加密
  -原理：使用**加密密钥**通过**加密算法**对数据进行加密；使用**解密密钥**通过**加密算法**对密文进行解密。
  - 经典算法：RSA、RSA2、DSA(DSA专门用作签名，速度很快)
  - 延伸用途：数字签名，使用私钥进行签名，保证数据的唯一性，公钥进行验证。
  - 如何保证安全性：非对称加密保证数据安全，签名防止篡改、伪造。
![image.png](https://upload-images.jianshu.io/upload_images/6850504-747ea6ad7cec3787.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**支付宝**使用的就是这种流程。
如上图所示，通过非对称加密算法进行签名实现了数据的安全性，但是同时有一个问题是，**数据增大了一倍**。
- #### [Base64](https://zh.wikipedia.org/wiki/Base64)

  - 概念：将二进制数据转换为由64个字符组成的字符串的编码算法。
  - 用途：纯文本（或字符串）的传输场景，比如只支持字符串传输的场景下实现图片的传输，可以先将图片Base64编码变成字符串之后再进行传输。
  - 特点：由于Base64由6个bit组成一个打印字符，所以对于二进制数据来说，3个字节:3 X 8 = 24bit = 4* 6bit = 4个打印字符。二进制数据经过Base64编码后，长度会增加1/3。

- ####[Url Encoding](https://zh.wikipedia.org/wiki/%E7%99%BE%E5%88%86%E5%8F%B7%E7%BC%96%E7%A0%81)
  - 将URL中的保留字符都使用%进行编码。
![image.png](https://upload-images.jianshu.io/upload_images/6850504-2916a3ed8d60a98c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 压缩与解压缩
- ####压缩：将数据转换一种方式来存储，减小占用空间。
- ####解压缩：将数据转换为可用格式。
- #### 常见压缩算法
  - DEFLATE(zip)
  - JPEG
  - MP3
- ####媒体数据的编解码
  - 图片的编码：将图片写成jpg，png等文件编码格式。
  - 图片的解码：将jpg、png等格式的图片解析成标准的图像数据格式。
  - 音频、视频的编解码

## 序列化与反序列化
  - 序列化：将数据对象（一般在内存中，如JVM中的对象）转换为字节序列的过程。
  - 反序列化：将字节序列转换为数据对象。
  - 目的：可以让内存中的对象进行传输。

## Hash
  - 概念：将任意数据转换为指定范围（通常很小）的数据，数据不可逆向
  - 作用：摘要、数字指纹
  - 经典算法：MD5、SHA1、SHA256等
  - 实际应用
    - 数据完整性验证
    - 数据唯一性验证，需要足够低的碰撞率
    - 快速查找：hashCode()、hashMap()
    - 保护数据
    如Web数据库不直接存储用户密码，只存储用户密码的**hashCode**。用户登录的时候再使用用户密码进行哈希和存储的hashCode进行校验。
    - 彩虹表：存储了常用密码哈希值，用来尝试破解用户密码。
      - 如何防止：可以使用加盐哈希来防止，**加盐**指随机一个固定的前缀或者后缀（盐的长度越长越安全），在做哈希校验时，将盐附加一起进行哈希算法，这样在不知道你的**盐值**的情况下，彩虹表就无效了。
#### 使用Hash签名的非对称加密
![image.png](https://upload-images.jianshu.io/upload_images/6850504-de2c4b591ad03453.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上图所示，**微信**使用该方式。

## 字符集
  - 定义：由整数向现实世界中的文字、符号的映射集合。
  - 分支
    - ASCII：128个字符，每个字符1字节
    - ISO-8859-1：对ASCII进行了扩充，每个字符1字节
    - Unicode：13万个字符，多字节（支持中文、韩文、日文等等）
      - UTF8：Unicode 的编码分支
      - UTF16：Unicode 的编码分支
    - GBK/GB2312/GB18030：中国自研标准，多字节，字符集+编码

## 登录和授权
- #### Cookie：客户端存储机制。
  - 工作机制
客户端存储服务器要求存储的数据，该数据服务器端并不进行存储，而是在放在Response Header里（Set-Cookie:xxxxx）要求客户端存储，客户端再次请求改服务器时，将存储的Cookie添加到Request Header中（Cookie:xxxxx），提交给服务器。![image.png](https://upload-images.jianshu.io/upload_images/6850504-1553f14d82a4560e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  - 作用
    - 会话管理
      - 登录状态管理
    Cookie-Session机制：服务器在收到客户端登录请求后，假如登录成功，生成会话ID（sessionid，用于存储客户端的登录状态）放在Response Header中（Set-Cookie:sessionid = 10001）返回给客户端；客户端在接下来的所有请求的Header中都加上sessionid（Cookie:sessionid=10001），服务器端收到后会检查该sessionid是否存在（已登录），这样就可以防止通过欺骗获取用户信息。
      - 购物车等
  - 个性化
      - 用户偏好管理、主题记录
  - Tracking：追踪用户行为
  - [XSS](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%B6%B2%E7%AB%99%E6%8C%87%E4%BB%A4%E7%A2%BC)
（Cross Site Scripting）：跨站脚本攻击，攻击者可以通过JS(或其他语言)获取到用户的Cookie、session等信息。可以通过将Cookie标记为HttpOnly（该属性可以防止Js读取用户Cookie）防止XSS攻击。
  - [XSRF](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0)
（Cross-site request forgery）跨站请求攻击，简单地说，是攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并运行一些操作。HTTP头中有一个Referer字段，这个字段用以标明请求来源于哪个地址。可以通过Referer字段去验证实际的请求发起方。
- ####Authorization
  
  - Basic：Authorization:Basic <Base64(username:password)>
    示例：api.github.com/notifications 
![image.png](https://upload-images.jianshu.io/upload_images/6850504-da995a4b93ab5fa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    安全风险
    - 数据被截获，用户名和密码直接Base64解码就全泄漏了，当然如果是HTTPS，数据是不容易被截获的。
  -Bearer（持票人）：Authorization:Bearer <bearer token>
    - OAuth2流程：（如微信登录）
![image.png](https://upload-images.jianshu.io/upload_images/6850504-b3cb0b3f6769f215.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    - 自家登录使用bearer token
    登录后服务器直接返回access_token，再次请求直接添加即可。
    - refresh token：refresh token 可以刷新access token，获取新的access token，并使旧的access token。假如当前使用的access_token失窃，可以使用refresh_token进行更换access_token；refresh_token

## [TCP/IP协议族](https://zh.wikipedia.org/wiki/TCP/IP%E5%8D%8F%E8%AE%AE%E6%97%8F)
TCP、IP协议族是由一系列网络协议所组成的网络分层模型。
为什么要分层：因为现实网络的不可靠性
分层：
  - Application Layer（应用层）：HTTP、FTP、SMTP、DNS等，是应用间传输数据的入口。
  - Transport Layer（传输层）：TCP、UDP等，负责传输分块、组装，保证传输的可靠性。
  - Internet Layer（网络层）：IP、ICMP，负责网络基础数据的传输。
  - Link Layer（数据链路层）：以太网、Wifi

## TCP连接
  - TCP 连接的建立与关闭
由于TCP连接是全双工通道（可在两个方向同时传递消息），所以两个方向都需要单独关闭连接。
    ![2751468781-5a07e9cb3a21b_articlex.png](https://upload-images.jianshu.io/upload_images/6850504-545c16a5907069c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  - 长连接
    - 不主动关闭连接
    - 心跳机制：间隔发送心跳数据包

## HTTPS（HyperText Transfer Protocol Secure）
常称为HTTP over TLS、HTTP over SSL或HTTP Secure
- 定义：HTTPS经由[HTTP](https://zh.wikipedia.org/wiki/HTTP "HTTP")进行通信，但利用[SSL/TLS](https://zh.wikipedia.org/wiki/%E4%BC%A0%E8%BE%93%E5%B1%82%E5%AE%89%E5%85%A8 "传输层安全")来加密数据包，实际是在HTTP（应用层）与TCP（传输层）之间加了一个安全层，用于实现加密传输
![image.png](https://upload-images.jianshu.io/upload_images/6850504-b500e525bb73b23f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 本质：首先，客户端和服务器通过**非对称加密**通信，协商出一个**对称加密**的**密钥Key**，然后在之后的每次通信都使用**对称加密**(密钥当就是协商出来的密钥)来进行。
- 连接大概过程
  - Client 向 Server请求建立TLS连接
  - 服务器回发服务器证书
  - 客户端验证证书，验证若通过则信任服务器
  - 客户端信任服务器后开始和服务器协商对称加密密钥
  - 使用对称密钥开始通信
####**实际流程如下图所示为单向验证流程**
可参考：[TLS/SSL握手过程](https://blog.csdn.net/hherima/article/details/52469674)
双向验证服务器也需要验证客户端证书
![image.png](https://upload-images.jianshu.io/upload_images/6850504-6c19b084f2bf6554.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 在Android中使用HTTPS
-  正常情况：直接使用
- 什么时候不行
  - 用的是自签名证书（比如用于内网的HTTPS）
  - 证书信息不全，缺乏证书机构信息
  - 手机操作系统过旧，没有安装最新加入的根证书
- 怎么办？[自己写证书验证过程](https://developer.android.com/training/articles/security-ssl)




