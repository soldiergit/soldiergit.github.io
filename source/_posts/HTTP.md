---
title: HTTP学习笔记
tags: [基础知识, 计算机基础, HTTP]
index_img: /resource/img/HTTP.jpg
date: 2020-11-17 09:28:56
---

## 一、HTTP方法
客户端发送的**请求报文**第一行为请求行，包含了方法字段。如**GET请求**：
```html
GET http://www.example.com/ HTTP/1.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
Cache-Control: max-age=0
Host: www.example.com
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947+gzip"
Proxy-Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 xxx

param1=1&param2=2
```

### GET
> 获取资源

现在大多数网络请求中，绝大部分使用的是GET方法。

### HEAD
> 获取报文首部

与GET方法类型，但不会返回报文主体部分。主要用于确认URL的有效性以及资源更新的日期时间等。

### POST
> 传输实体主体

POST主要同于传输数据，而GET主要用来回去资源。

### ~~PUT~~
> 上传文件

由于自身不带验证机制，任何人都可以上传文件，因此存在安全性问题，一般不使用该方法。

### PATCH
> 对资源进行部分修改

PUT也可以用于修改资源，但是只能完全替换原始资源；PATCH允许部分修改。

### DELETE
> 删除文件

与PUT功能相反，并且同样不带验证机制。

### OPTIONS
> 查询支持的方法

查询指定的URL能够支持的方法。会返回 `Allow：GET，POST，HEAD，OPTIONS` 这样的内容。

### CONNECT
> 要求在与代理服务器通信是建立隧道

使用[SSL](https://baike.baidu.com/item/ssl)和[TLS](https://baike.baidu.com/item/TLS)协议把通信内容加密后经网络隧道传输。

### ~~TRACE~~
> 追踪路径

服务器会将通信路径返回给客户端。通常不会使用 TRACE，并且它容易受到 [XST 攻击（Cross-Site Tracing，跨站追踪）](https://zhuanlan.zhihu.com/p/61990354)。

## 二、HTTP状态码
服务器返回的**响应报文**中第一行为状态行，包含了状态码以及原因短语，用来告知客户端请求的结果。

状态码 | 类别 | 含义
:-: | :-: | :-:
1XX | Informational（信息性状态码） | 接收的请求正在处理| 
2XX | Success（成功状态码） | 请求正常处理完毕|
3XX | Redirection（重定向状态码） | 需要进行附加操作以完成请求|
4XX | Client Error（客户端错误状态码） | 客户端无法处理请求|
5XX | Server Error（服务器错误状态码） | 服务器处理请求出错|

### 1XX 信息
 - **100 Continue**：表明到目前为止都很正常，客户端可以继续发送请求或者忽略这个响应。
 
### 2XX 成功
 - **200 OK**
 - **204 No Content**：请求已经成功处理，但是返回的响应保卫不包含实体的主体部分。一般只需要从客户端往服务器发送信息，而不需要返回数据时使用。
 - **206 Partial Content**：表示客户端进行了范围请求，响应报文包含有Content-Range指定范围的实体内容。

### 3XX 重定向
 - **301 Moved Permanently**：永久性重定向
 - **302 Found**：临时性重定向
 - **303 See Other**：和302有着相同的功能，但是303明确要求客户端应该采用GET方法获取资源。
 - **304 Not Modified**：如果请求报文首部包含一些条件，例如：If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回304状态码。
 - **307 Temporary Redirect**：临时重定向，与302的含义类似，但是307要求浏览器不会把重定向请求的POST方法改为GET方法。

### 4XX 客户端错误
 - **400 Bad Request**：请求报文中存在语法错误。
 - **401 Unauthorized**：该状态码表示发送的请求需要有认证信息（BASIC认证、DIGEST认证）。如果之前已进行过一次请求，则表示用户认证失败。
 - **403 Forbidden**：请求被拒绝。
 - **404 Not Found**：找不到页面。

### 5XX 服务器错误
 - **500 Internal Server Error**：服务器正在执行请求时发生错误。
 - **503 Service Unavailable**：服务器暂时处于超负载或正在停机维护，现在无法处理请求。

## 三、HTTPS
HTTP有以下安全性问题：
1. 使用明文进行通信，内容可能会被窃听；
2. 不验证通信方的身份，通信方的身份有可能遭遇伪装；
3. 无法证明报文的完整性，报文有可能遭篡改。

<font color=#FF000>HTTPS并不是新协议</font>，而是让HTTP先和SSL通信，再由SSL和TCP通信，也就是说HTTPS使用了隧道进行通信。
通过使用SSL，HTTPS具备了以下**特点**：
 - 加密（防窃听）
 - 认证（防伪装）
 - 完整性保护（防篡改）

### HTTPS的加密算法
#### 1.对称加密 
加密和解密使用同一密钥。
 - **优点**：运算速度快；
 - **缺点**：无法安全地将密钥传输给通信方。
![](/resource/img/SymmetricEncryption.png)

#### 2.非对称加密
又称为公开密钥加密，加密和解密使用不同的密钥。
公开密钥所有人都可以获取，通信发送方获得接收方的公开密钥后，就可以使用公开密钥进行加密，接收方收到通信内容后使用私钥解密。
 - **优点**：可以更安全地将公开密钥传输给通信发送方；
 - **缺点**：运算速度慢。
![](/resource/img/PublicKeyEncryption.png)

#### 3.HTTPS采用的加密方式
上面提到的**对称加密**方式的传输效率更高，但是无法安全地将密钥Secret Key传输给通信方。而**非对称加密**方式可以保证传输的安全性，因此我们可以利用非对称加密方式将Secret Key传输给通信方。HTTPS采用混合的加密机制，正是利用了上面提到的方案：
 - 使用**非对称加密**方式，传输**对称加密**方式所需要的Secret Key，从而保证安全性；
 - 获取到Secret Key后，再使用**对称加密**方式进行通信，从而保证效率。

### HTTPS的缺点
 - 因为需要进行加密解密等过程，所以速度会更慢；
 - 需要支付证书授权的高额费用。

## 四、网络传输层协议
### UDP和TCP的特点
 - **用户数据报协议 UDP**：是无连接的，尽最大可能交付，没有拥塞控制，<font color=#FF000>面向报文</font>，支持一对一、一对多、多对一、多对多的交互通信。
![](/resource/img/UDP.jpg)
 - **传输控制协议 TCP**：是面向连接的，提供可靠交付，有流量控制、拥塞控制，提供全双工通信，<font color=#FF000>面向字节流</font>，只能一对一。
![](/resource/img/TCP.png)

### TCP的三次握手
假设A为客户端，B为服务器端。
 - 首先B处于LISTEN（监听）状态，等待客户端的连接请求；
 - A想B发送连接请求报文：同步SYN=1、确认ACK=0、选择一个初始序号X；
 - B收到连接请求报文后，若同意，则向A发送连接确认报文：同步SYN=1、确认ACK=1、确认号为X+1、选择一个初始序号Y；
 - A收的B的连接确认后，还要向B发出确认：确认号为Y+1、序号为X+1；
 - B收到A的确认后，连接建立。

#### TCP三次握手的原因
第三次握手是为了防止失效的连接请求到达服务器，让服务器错误打开连接。

### TCP的四次挥手
以下描述不讨论序号和确认号，因为序号和确认号的规则比较简单。并且不讨论ACK，因为确认ACK在连接建立之后都为1。
 - A发送连接释放报文：终止FIN=1；
 - B收到之后发出确认，此时TCP处于半关闭状态，B能向A发送数据但A不能向B发送数据；
 - 当B不再需要连接时，发出连接释放报文：终止FIN=1；
 - A收到后发出确认，进入TIME-WAIT状态，等待2MSL（最大报文存活时间）后释放连接；
 - B收到A的确认后释放连接。

#### TCP四次挥手的原因
客户端发送了FIN连接释放报文后，服务器收到了这个报文，就进入了CLOSE-WAIT状态。这个状态是为了让服务器端发送还未传送完毕的数据，传送完毕之后，服务器会发送FIN连接释放报文。

### TCP的流量控制
流量控制是为了控制发送方发送速率，保证接收方来得及接收。
接收方发送的确认报文中的窗口字段可以用来控制发送方窗口大小，从而影响发送方的发送效率。将窗口字段设为0，则发送方不能发送数据。

### TCP的拥塞控制
如果网络出现拥塞，分组将会丢失，此时发送方会继续重传，从而导致网络拥塞程度更高。因此当出现拥塞是，应当控制发送方的速率。这一点和流量控制很像，但是出发点不同。流量控制是为了让接收方能来得及接收，而拥塞控制是为了降低整个网络的拥塞程度。
<font color=#FF000>TCP主要通过四个算法来进行拥塞控制</font>：慢开始、拥塞避免、快重传、快恢复。
