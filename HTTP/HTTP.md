## 快速开始

- [`[基础]` 什么是 HTTP？](#HTTP（HyperText_Transfer_Protocol，超文本传输协）)
- [`[5层结构]` TCP/IP 协议簇](#TCP/IP协议簇)
- [`[建立通信]` 三次握手与四次挥手](#三次握手与四次挥手)
- [`[建立通信]` URI 与 URL](#URI与URL)
- [`[基础]` 简单的 HTTP 协议](#简单的HTTP协议)
- [`[cookie]` 登录问题](#登录问题)
- [`[报文]` HTTP 报文信息](#HTTP报文信息)
- [`[报文]` 分块传输编码（Chunked_Transfer_Coding）](#分块传输编码（Chunked_Transfer_Coding）)
- [`[报文]` MIME（Multipurpose_Internet_Mail_Extensions）](#MIME（Multipurpose_Internet_Mail_Extensions）)
- [`[报文]` 范围请求](#范围请求)
- [`[报文]` 内容协商](#内容协商)
- [`[报文]` HTTP 状态码](#HTTP状态码)
- [`[]` 代理、网关、隧道](#代理、网关、隧道)

## HTTP（HyperText_Transfer_Protocol，超文本传输协）

参考：[https://developer.mozilla.org/zh-CN/docs/Web/HTTP](https://developer.mozilla.org/zh-CN/docs/Web/HTTP)

> 是一个用于传输超媒体文档（例如 HTML）的应用层协议。它是为 Web 浏览器与 Web 服务器之间的通信而设计的，但也可以用于其他目的。HTTP 遵循经典的客户端-服务端模型。HTTP 是无状态协议，这意味着服务器不会在两个请求之间保留任何数据（状态）。该协议虽然**通常基于 TCP/IP 层**，但可以在任何可靠的传输层上使用；也就是说，不像 UDP，它是一个不会静默丢失消息的协议。

HTTP 的诞生

- 很多文档之间相互关联形成超文本（HyperText）

- 连接成为相互参阅 www（World Wide Web，万维网）

www 构建技术：

1.HTML：超文本标记语言
2.HTTP：文档传输协议
3.URI：Universal Resource Identifier

HTTP/0.9 -> HTTP/1.0 -> [RFC2616 - Hypertext Transfer Protocol HTTP/1.1]()

## TCP/IP 协议簇

<div align="center"><img src="https://my-blog-to-use.oss-cn-beijing.aliyuncs.com/2019/7/%E4%BA%94%E5%B1%82%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84.png" width="75%" height="75%"/></div><br>

计算机与网络设备需要相互通信，双方就必须要基于相同的规则。我们把这种规则称之为协议（protocol）。

重点：分层协议

- 应用层：**应用层协议定义的是应用进程间通信和交互的规则。**比如域名系统 DNS、支持 www 应用的 HTTP，支持电子邮件 SMTP 协议等。应用层交互的数据单元称为报文。

```
会话层：不同主机各进程的会话
会话：建立连接并在其上有序的传输数据，也称为建立同步（SYN） - 断点续传

表示层：交换信息方式。编码、压缩、加解密等。
```

以上为资源子网，端到端的通信。

- 传输层：**为两台主机进程之间的通信提供"通用的"数据传输服务。**“通用的”是指多种应用可以使用同一个运输层服务。由于一台主机可以同时运行多个线程，因此运输层有复用和分用的功能。传输控制协议（TCP）和用户数据报协议（UDP）是该层两个不同协议。

```
复用：多个应用层进程可同时使用下面运输层的服务。
公用：与复用相反，运输层把收到的信息分别交付上面应用层中的相应进程。
```

以下为通信子网，点到点的传播，负责下一步到哪。

- 网络层：**网络层的任务就是选择合适的网间路由和交换结点，确保数据及时传送。**因为通信的两个计算机之间在网络中可能会经过多个数据链路、通信子网。

```
将运输层报文段（用户数据报）封装为分组和包传输。因为使用 IP 协议，分组也称为 IP 数据报。

路由器也需要在低三层基础上拆、打包。
```

- 链路层：**两台主机之间的数据传输，总是在一段一段的链路上传送的。这就需要专门的链路层协议。**

与 HTTP 关系密切的协议 : IP、TCP 和 DNS

**总结一下**

<div align="center">  <img src="https://my-blog-to-use.oss-cn-beijing.aliyuncs.com/2019/7/%E4%B8%83%E5%B1%82%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84%E5%9B%BE.png" width=""/></div><br>

## IP 协议

> TCP/IP 协议中向上只提供简单灵活的，无连接的，尽最大努力交付的数据报服务。

Internet Protocol 是 TCP/IP 体系中两个最主要的协议之一、网际层的核心。配套的有 ARP，RARP，ICMP，IGMP。

- IP 地址：结点被分配到的地址
- MAC 地址：网卡所属固定地址

### ARP 协议

> IP 地址解析为硬件地址

两台主机在一个子网络，发送一个包含目的主机 ip 地址数据报，在对方的 MAC 地址这一栏，填的是 FF:FF:FF:FF:FF:FF(广播地址)，所在子网络每台主机收到数据包并且取出 ip 与自身 ip 相比较，若相同返回 MAC 地址，否则丢弃。

ARP 的高速缓存可以大大减少网络上的通信量。可以直接从高速缓存中找到所需要的硬件地址而不需要再去广播方式发送 ARP 请求分组。

## TCP 协议

**TCP 把数据看成是一个无结构但是有序的字节流。字节流是指为了方便传输，将大块数据分割为报文段（segment）为单位的数据包管理。**

> 是面向连接的，有流量控制，拥塞控制，提供全双工通信，提供可靠交付，每一条 TCP 连接只能是点对点的（一对一）。

### [滑动窗口协议](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C%20-%20%E4%BC%A0%E8%BE%93%E5%B1%82.md#tcp-%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3)

> 提供流量控制、可靠交付。

窗口（Recipient2sender）是 TCP 缓存的一部分，暂时存放字节流。

Recipient 通过 TCP 报文段中的窗口字段告诉 sender 自己的窗口大小，sender 根据这个值和其他信息设置自己窗口大小。

- sender 滑动窗口协议允许 sender 发送多个分组而不需要等待确认，窗口大小为 N [N=已发送未确认+允许但尚未发送]
- Recipient 只会对窗口内最后一个按序到达的字节进行确认。sender 得到一个字节确认之后，该字节前所有字节都已被接受。将发送窗口向右滑动一定距离。

<div align="center"><img src="https://github.com/CyC2018/CS-Notes/raw/master/notes/pics/a3253deb-8d21-40a1-aae4-7d178e4aa319.jpg" width="75%" height="75%"/></div><br>

### [TCP 拥塞控制](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C%20-%20%E4%BC%A0%E8%BE%93%E5%B1%82.md#tcp-%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3)

> 慢开始、拥塞避免、快重传、快恢复

[TCP 拥塞控制](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C%20-%20%E4%BC%A0%E8%BE%93%E5%B1%82.md#tcp-%E6%8B%A5%E5%A1%9E%E6%8E%A7%E5%88%B6)

如果网络出现拥塞，分组将会丢失，若此时 sender 会继续重传，从而导致网络拥塞程度更高。

所以拥塞控制是为了降低整个网络的拥塞程度。与**TCP 流量控制（滑动窗口）**相同之处在于都是控制 sender 的速率。

<div align="center"><img src="https://github.com/CyC2018/CS-Notes/blob/master/notes/pics/910f613f-514f-4534-87dd-9b4699d59d31.png" width="75%" height="75%"/></div><br>

1.

- 慢开始：cwnd = 1， cwnd \*= 2
- 拥塞避免：cwnd`拥塞窗口状态变量`、ssthresh`慢开始门限`

```js
if (cwnd >= ssthresh) {
  cwnd += 1;
}
```

2.

- 快重传：比如已经接收到 01 和 02 字节，此时收到 04 字节，应发送对 02 的确认。
- 快恢复：

```js
if (只是丢失个别报文段，而不是网络拥塞) {
  ssthresh = cwnd / 2;
  cwnd = ssthresh
}
```

## 三次握手与四次挥手

可靠交付「在不可靠信道上可靠地传输信息」。如果信道是可靠的（即无论什么时候发送消息，对方一定能收到），或者你不关心是否要保证对方收到你的消息，那直接像 UDP 那样直接发送消息就可以了。

## URI 与 URL

<div align="center"><img src="https://github.com/CyC2018/CS-Notes/blob/master/notes/pics/8441b2c4-dca7-4d6b-8efb-f22efccaf331.png" width="75%" height="75%"/></div><br>

URI 像身份证号一样唯一标识了一个资源，URL 更像家庭住址一样，不仅唯一标识资源，还提供了定位该资源的信息。

URL 的格式：

`<scheme>://<host>「<hostname>:<port>」/<pathname><params>?<query>#<hash>`

`https://www.163.com:8080/index.html?r=admin&lang=zh-CN#news`

- host：DNS、IPv4 或 IPv6 地址名`著名 localhost 可以在电脑 host 文件上找到`
- query（查询字符串）：传入参数
- hash（片段标识符）：可以标记文档内某位置。RFC 并没有明确规定其使用方法

## 简单的 HTTP 协议

### C/S 通信

HTTP 协议和 TCP/IP 协议族内的其他众多的协议相同，用于 client 和 server 之间的通信。

这里的“客户端”可以是有 GUI（图形用户界面）的定制软件，也可以是浏览器，甚至可以是通过 SSH 访问服务器的命令行脚本。只要是客户端通过访问服务器调取计算或者存储资源的，统统都是 C/S 架构。所谓的 Browser-Server 架构其实是 C/S 架构的一种特殊的实现形式。

- HTTP（超文本传输协议）是应用层上的一种客户端/服务端模型的通信协议,它由请求和响应构成，且是无状态的「非 http2」。

```
不具备保存之前发送过的请求或响应的功能。也就是协议对于发送过的请求与响应都不能做持久化处理。减少服务器 cpu 及内存资源的消耗。
```

- HTTP 协议使用 URI 定位互联网上的任意资源。

```bash
如果不是访问特定资源而是对服务器本身发起请求，可以 用一个 * 来代替请求 URI。栗子如下：
OPTIONS * HTTP/1.1 # 查询 HTTP 服务器端支持 的 HTTP 方法种类
```

### 常用方法

GET：获取资源

HEAD：获得报文首部（不返回报文主体部分）

1. 确认 URL 的有效性
2. 资源更新的日期时间

POST：传输实体主体，主要目的并不是获取响应的主体内容。「与 PUT 都可以新增资源，关键在于幂等性「多次提前会创建多个资源」」

PUT：传输文件

1. 由于 HTTP/1.1 自身不带验证机制，任何人都可以上传文件，因此存在安全性问题，一般不使用该方法
2. 配合应用程序验证机制、架构设计采用 `REST（REpresentational State Transfer，表征状态转移）`标准的 web 网站，可能会使用 PUT

PATCH：对资源部分修改

1. PUT 也可以用于修改资源，但是只能完全替代原始资源，PATCH 允许部分修改

DELETE：删除文件 [与 PUT 功能相反，并且同样不带验证机制]

OPTIONS：查询请求的 URL 指定资源支持的方法

1. 会返回 Allow: GET, POST, HEAD, OPTIONS 这样的内容

CONNECT：要求在与代理服务器通信时建立隧道

1. 使用 SSL（Secure Sockets Layer，安全套接层）和 TLS（Transport Layer Security，传输层安全）协议把通信内容
   加密后经网络隧道传输。

TRACE；追踪路径 [服务器会将通信路径返回给客户端]

1. 发送请求时，在 Max-Forwards 首部字段中填入数值，每经过一个服务器就会减 1，当数值为 0 时就停止传输。
2. 通常不会使用 TRACE，并且它容易受到 XST 攻击（Cross-Site Tracing，跨站追踪）。

### 持久连接节省通信量

<div align="center">  <img src="https://github.com/dlm-wizard/dlm-wizard.github.io/blob/master/HTTP/assets/HTTP%E4%B8%80%E6%AC%A1%E9%80%9A%E4%BF%A1.png?raw=true" width=""/></div><br>

<div align="center">  <img src="https://github.com/dlm-wizard/dlm-wizard.github.io/blob/master/HTTP/assets/%E9%9D%9E%E6%8C%81%E4%B9%85%E8%BF%9E%E6%8E%A5.png?raw=true
" width=""/></div><br>

建立 TCP 连接：三次握手，断开 TCP 连接：四次挥手。包含多张图片的 html 页面，在发送请求访问 html 页面资源同时，也会请求该 html 界面里包含的其他资源。因此，每次请求都会造成无畏的 TCP 连接和断开，增加通信量的开销。

持久连接旨在建立 1 次 TCP 连接后进行多次请求和响应的交互。

持久连接的好处在于减少了 TCP 连接的重复建立和断开所造成的额 外开销，减轻了服务器端的负载。Web 性能优化。

在 HTTP/1.1 中，所有的连接默认都是持久连接。

### pipelining

pipelining：不用等待响应可以直接发送下一个请求。

keep-alive 使多数请求以管线化（pipelining）方式发送成为可能。

## 登录问题

### 无状态就是在两次请求之间服务器并不会保存任何的数据。

1. 登录就是用某种方法让服务器在多次请求之间能够识别出你
   而不是每次发请求都得带上用户名密码这样的识别身份的信息。从登录成功到登出的这个过程，服务器一直维护了一个可以识别出用户信息的数据结构，

2. 广义上来说，这个过程就叫做 session，也就是保持了一个会话

server 管理所有 client 状态成为负担。

## HTTP 报文信息

**请求报文（Client）**

- request line
- request headers

<div align="center">  <img src="https://github.com/CyC2018/CS-Notes/raw/master/notes/pics/HTTP_RequestMessageExample.png" width=""/></div><br>

**响应报文（Server）**

- status line
- response headers

<div align="center">  <img src="https://github.com/CyC2018/CS-Notes/raw/master/notes/pics/HTTP_ResponseMessageExample.png" width=""/></div><br>

> 报文本身是由多行（CR + LF 作换行符，分隔了报文首部与主体）数据构成的字符串文本

> > 通常并不一定有报文主体

### 报文首部字段

各种首部字段及其含义如下（不需要全记，仅供查阅）：

#### 通用首部字段

| 首部字段名        | 说明                                       |
| ----------------- | ------------------------------------------ |
| Cache-Control     | 控制缓存的行为                             |
| Connection        | 控制不再转发给代理的首部字段、管理持久连接 |
| Date              | 创建报文的日期时间                         |
| Pragma            | 报文指令                                   |
| Trailer           | 报文末端的首部一览                         |
| Transfer-Encoding | 指定报文主体的传输编码方式                 |
| Upgrade           | 升级为其他协议                             |
| Warning           | 错误通知                                   |

#

#### 请求首部字段

| 首部字段名          | 说明                                            |
| ------------------- | ----------------------------------------------- |
| Accept              | 用户代理可处理的媒体类型                        |
| Accept-Charset      | 优先的字符集                                    |
| Accept-Encoding     | 优先的内容编码                                  |
| Accept-Language     | 优先的语言（自然语言）                          |
| Authorization Web   | 认证信息                                        |
| Expect              | 期待服务器的特定行为                            |
| From                | 用户的电子邮箱地址                              |
| Host                | 请求资源所在服务器                              |
| If-Match            | 比较实体标记（ETag）                            |
| If-None-Match       | 比较实体标记（与 If-Match 相反）                |
| If-Modified-Since   | 比较资源的更新时间                              |
| If-Unmodified-Since | 比较资源的更新时间（与 If-Modified-Since 相反） |
| If-Range            | 资源未更新时发送实体 Byte 的范围请求            |
| Max-Forwards        | 最大传输逐跳数                                  |
| Proxy-Authorization | 代理服务器要求客户端的认证信息                  |
| Range               | 实体的字节范围请求                              |
| TE                  | 传输编码的优先级                                |
| User-Agent          | HTTP 客户端程序的信息                           |

#

#### 响应首部字段

| 首部字段名         | 说明                         |
| ------------------ | ---------------------------- |
| Accept-Ranges      | 是否接受字节范围请求         |
| Age                | 推算资源创建经过时间         |
| ETag               | 资源的匹配信息               |
| Location           | 令客户端重定向至指定 URI     |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
| Retry-After        | 对再次发起请求的时机要求     |
| Server             | HTTP 服务器的安装信息        |
| Vary               | 代理服务器缓存的管理信息     |
| WWW-Authenticate   | 服务器对客户端的认证信息     |

#

#### 实体首部字段

| 首部字段名       | 说明                   |
| ---------------- | ---------------------- |
| Allow            | 实体主体适用的编码方式 |
| Content-Encoding | 推算资源创建经过时间   |
| Content-Language | 实体主体的自然语言     |
| Content-Length   | 实体主体的大小         |
| Content-Location | 替代对应资源的 URI     |
| Content-MD5      | 实体主体的报文摘要     |
| Content-Range    | 实体主体的位置范围     |
| Content-Type     | 实体主体的媒体类型     |
| Expires          | 实体主体过期的日期时间 |
| Last-Modified    | 资源的最后修改日期时间 |

## 编码提升速率

HTTP 报文通过编码提升传输速率。注：编码会消耗更多的 CPU 资源。

**内容编码**，将实体主体进行压缩，从而减少传输的数据量

- gzip（GNU zip）
- compress（UNIX 标准压缩）
- deflate（zlib）
- identity（不进行编码）

请求 `Accept-Encoding 首部` 包含有它所支持的压缩算法，以及各自的优先级。server 从中选择一种，并使用该算法压缩实体主体，在 response `Content-Encoding 首部` 告知 client 选择的算法。

由于该内容协商过程是基于编码类型来选择资源的展现形式的，响应报文的 Vary 首部字段至少要包含 `Content-Encoding`。

- 报文（message）

```
HTTP 通信的基本单位。由 8 位组字节流（octet sequence）组成，通过 HTTP 通信传输。
octet：8 bit
```

- 实体主体（entity）

```
作为 请求、响应 的有效 payload 被传输。
内容由实体首部和实体主体组成
```

这里容易误解的是`实体头部`与`报文头部`，其实`实体头部`也是在报文主体内。不同的实体拥有不同的`实体头部`。

这个栗子里有多个实体，而在实体里，也有`实体头部`与`实体主体`，同样通过 `CR+LF` 分割

```
POST /upload HTTP/1.1
Host: example.com
Content-Length: xxx # 报文主体1 实体头部
Content-Type: multipart/form-data; boundary=AaBbCcDd

--AaBbCcDd
Content-Disposition: form-data; name="username" # 报文主体2 实体头部

RuphiLau
--AaBbCcDd
Content-Disposition: form-data; name="file"; filename="picture.jpg"
Content-Type: image/jpeg # 报文主体3 实体头部

...(picture.jpg的数据)...
```

综上我们可以发现，报文主体等于实体主体。只有当传输中进行编码操作时，实体主体内容发生变化，才导致它和报文主体产生差异。

## 分块传输编码（Chunked_Transfer_Coding）

在 HTTP 通信过程中，请求 编码实体资源未完全传输完成之前，浏览器无法显示请求页面。在传输大量数据时，通过把数据（实体主体）分割为 chunk，浏览器可以逐步展示页面。

- `Transfer-Encoding: chunked`

每块都会用 16 进制标记 chunk 大小，实体主体最后一块（大小为 0）会使用 `0(CR + LF)` 标记。

## MIME（Multipurpose_Internet_Mail_Extensions）

在 MIME 扩展中会使用一种称为多对象集合（Multipart）方法，来容纳不同类型的数据

- multipart/form-data

```
在 Web 表单文件上传时使用
```

- multipart/byteranges

```
响应报文包含了多个范围的内容时使用。
```

栗子：

```
Content-Type: multipart/form-data; boundary=AaBbCc

--AaBbCc Content-Disposition: form-data; name="field1" 　

Joe Blow

--AaBbCc Content-Disposition: form-data; name="pics"; filename="file1.txt" Content-Type: text/plain 　

...（file1.txt 的数据）...

--AaBbCc--
```

## 范围请求

如果出现网络中断，server 只发送了一部分数据，client 可以通过范围请求只请求 server 未发送的那部分数据，如断点续传。

请求成功的话 server 返回的响应 status line 状态码为 `206 Partial Content`。

```
# 请求报文
GET /baidu.jpg HTTP/1.1
Host: www.google.com
Range: bytes=0-1023

# 响应报文
HTTP/1.1 206 Partial Content
Content-Range: bytes 0-1023/146515
Content-Length: 1024

binary content here
```

## 内容协商

一个 `URL 资源` server 可以用多种 MIME 形式进行响应。对于某一个 client`（浏览器、App）` 只需要一种。so client 与 server 就使用内容协商机制来保证这个事情。

1. server 将自己 MIME 类型发送给 client，client 选择后， server 返回对应 MIME 类型。`多一次网络交互，对使用者要求较高，一般不用`。
2. 请求 中需要指明 `MIME`，server 返回指定的 `Content-Type`。提供不了那就 `406 Not Acceptable` 错误吧~

## HTTP 状态码

status code：HTTP 请求 server 处理状态。

状态码就那些，常用的记住就行了：

**1XX 信息**

- 100 Continue

```bash
该临时响应表明，迄今为止的所有内容都是可行的，client 应该继续请求。如果已经完成，则忽略它。
# 部分浏览器 post 接受到 100 再发送请求实体
```

**2XX 成功**

- 200 OK

```
请求已经被成功处理，成功含义来源于 HTTP Method
```

- 204 No content

```bash
请求已经成功处理，但是返回的响应报文不包含实体的主体部分。
# client 往 server 发送信息，而 client 不需要发送新信息内容的情况下使用
```

- 206 Partial Content

```bash
client 进行了范围请求，响应报文包含由 Content-Range 指定范围的实体内容
# 可能包含 If-Range request headers
```

- **3XX 重定向**

- 301 moved permanently

```bash
资源已被分配了新的 URL（永久性重定向），以后应使用 response headers Location 提示 URL
# 除非额外指定，否则该响应可缓存
```

- 302 found

```bash
资源临时被分配了新的 URL（临时性重定向），本次应使用 Location 提示
# Cache-Control、Expires 制定下，可以缓存这个响应
```

- 303 see other

```bash
同 302，但是 303 明确要求 client 使用 GET 方法获取资源

# 如当使用 POST 方法访问 CGI 程序，其执行后的处理结果是希望 client 能以 GET 方法重定向到另一个 URI
```

虽然 HTTP 协议规定 301、302 状态下重定向时不允许把 POST 方法改成 GET 方法，但是大多数浏览器都会在 301、302 和 303 状态下把 POST 改成 GET，删除请求报文实体，之后请求会再次发送。

- 307 temporary redirect

```
临时重定向，同 302，但是 307 要求浏览器不会把重定向请求的 POST 改为 GET
```

- 304 not modified

```bash
server 允许访问资源，但是不满足条件，响应不会包含报文实体。

# 请求报文首部包含一些条件，例如：If-Match，If-None-Match，If-Modified-Since，If-Unmodified-Since，If-Range。
```

**4XX 客户端错误**

- 400 bad

```
请求报文存在语法错误
```

- 401 unauthorized

```bash
请求报文需要认证信息（通过 HTTP 认证，如 BASIC 认证、DIGEST 认证）「request headers Authorization」。
# 如果请求包含 Authorization 证书，则表示用户认证失败
```

- 403 forbidden

```bash
表示对请求资源的访问被 server 拒绝
# 与 401 不同，Authorization 并没有帮助，而且这个请求也不应该被重复提交
```

- 404 not found

```
server 上未找到请求资源
```

**5XX 服务器错误**

- 500 internal sever error

```
server 遇到了不知道如何处理的情况
```

- 503 service unavailable

```
server 暂时处于超负载或正在停机维护，无法处理请求
```

## 代理、网关、隧道

[HTTP 代理原理及实现](https://imququ.com/post/web-proxy.html)

## 参考

[VPS，云服务器（云主机），虚拟主机有什么异同？](https://www.zhihu.com/question/19856629)
[服务器、虚拟主机、虚拟空间到底是个什么关系？](https://www.zhihu.com/question/24327357)
