# 输入URL到页面呈现发生了什么？ - 网络篇
网络请求
1. 构建请求
- 浏览器会构建请求行
// 请求方法是GET，路径为根路径，HTTP协议版本为1.1
GET / HTTP/1.1
2. DNS解析
- 由于我们输入的是域名，而域名无法被计算机识别， 所以我们需要把这个域名转换成对应的IP地址，这个时候发送端会把域名传给DNS，然后dns把对应的IP地址传回，然后发送端接收到IP地址可以发送访问请求
- 当然，值得注意的是，浏览器提供了DNS数据缓存功能。即如果一个域名已经解析过，那会把解析的结果缓存下来，下次处理直接走缓存，不需要经过 DNS解析。
3. 查找强缓存
- 先检查强缓存， 如果命中直接使用， 否则进入下一关。 
4. 建立TCP连接 
- 通过三次握手建立客户端和服务器之间的连接
- 进行数据传输。 这里有一个重要的机制， 就是接受方接收到数据包后必须向发送方确认， 如果发送方没有接到这个确认消息， 就判定为数据包丢失， 并重新发送该数据包。 当然， 发送的过程中还有一个优化策略， 就是把大的数据包拆成一个个小包， 一次传输到接受方， 接收方按照这个小包的顺序把他们
5. 发送HTTP请求
现在TCP连接建立完毕，浏览器可以和服务器开始通信，即开始发送 HTTP 请求报文。浏览器发 HTTP 请求报文要携带三样东西:请求行、请求头和请求体。
6. 网络响应
HTTP 请求到达服务器，服务器进行对应的处理。最后要把数据传给浏览器，也就是返回网络响应。
跟请求部分类似，网络响应具有三个部分:响应行、响应头和响应体。
响应行类似下面这样:
```
HTTP/1.1 200 OK
```
由`HTTP协议版本`、`状态码`和`状态描述`组成。
响应头包含了服务器及其返回数据的一些信息, 服务器生成数据的时间、返回的数据类型以及对即将写入的Cookie信息。
响应完成之后怎么办？TCP 连接就断开了吗？
不一定。这时候要判断Connection字段, 如果请求头或响应头中包含Connection: Keep-Alive，表示建立了持久连接，这样TCP连接会一直保持，之后请求统一站点的资源会复用这个连接。
否则断开TCP连接, 请求-响应流程结束。
7. 生成渲染树
客户端请求到从服务端传来的数据后， 首先接收到HTML， 进行HTML语法解析， 而后结合DOM规则生成DOM树
然后处理样式， 首先进行css解析生成样式树， 然后和DOM树结合生成Render树， Render与布局相结合便可形成完整的Render树
形成Render树之后浏览器进行页面绘制， 并且展示给用户

# http版本之间的区别
- HTTP / 0.9 只支持GET请求方式， 没有请求头的概念
- HTTP /1.0 增加了请求方式， 请求头和响应头 
- HTTP / 1.1 串联请求多个文件， 支持大文件断点续传
- HTTP /2 多路复用， 可以并行传输文件请求
## HTTP 1.0 和 HTTP 1.1 的一些区别
1. 缓存处理
   1.0 主要采取 Expire、If-Modify-Since
   1.1 引入了 etag/If-None-Match
2. 带宽优化及网络连接的使用
   1.0 存在浪费带宽，比如客户端只需要某个对象的某些部分，但服务器将整个对象传输过来
   1.1 在头部增加了 Content-range 字段，允许只请求资源的某个部分(状态码 206)
3. 错误通知的管理
   1.1 新增 24 个错误状态响应码
4. Host 头处理
   1.0 中认为每台服务器都绑定一个唯一的 IP 地址，URL 中不传主机名
   但现在虚拟主机技术发展，一台物理服务器可以存在多个虚拟主机共享一个 IP
   1.1 的请求消息和响应消息都支持 HOST 头域，如果没有会报错(状态码 400)
5. 状态管理
   1.0 中由于http是无状态协议， 导致在请求数据后服务端无法记住用户， 每次请求数据都需要登录之类的操作
   1.1 中引入cookie， 可以在请求数据后响应报文中添加一个叫做Set-Cookie的首部字段信息， 通知客户端保存cookie， 当下次客户端在此往该服务端发送请求时， 客户端会自动在请求报文中加入cookie值后发出去
6. 持久化连接
   1.0 中每次请求http都需要断开tcp连接
   1.1 中默认添加了Connection: keep-alive字段可以支持持久化连接
## HTTP 1.1 如何解决 HTTP 的队头阻塞问题？
HTTP 队头阻塞的根本原因在于 HTTP 基于请求-响应的模型，在同一个 TCP 长连接中，前面的请求没有得到响应，后面的请求就会被阻塞
1. 并发连接：对于一个域名允许分配多个长连接，那么相当于增加了任务队列，不至于一个队伍的任务阻塞其它所有任务
2. 域名分片：分出二级域名，指向同一台服务器，使得能够并发的连接数增加了
## HTTP2.0有哪些改进？
- 头部压缩
  尤其对于GET请求， 请求报文几乎全是请求头， HTTP2针对头部字段， 采用了HPACK的压缩算法对请求头进行压缩
- 多路复用
  HTTP2采用二进制格式传输，取代了HTTP1.x的文本格式，二进制格式解析更高效。多路复用代替了HTTP1.x的序列和阻塞机制，所有的相同域名请求都通过同一个TCP连接并发完成。在HTTP1.x中，并发多个请求需要多个TCP连接，浏览器为了控制资源会有6-8个TCP连接都限制。
  HTTP2中
  - 同域名下所有通信都在单个连接上完成，消除了因多个 TCP 连接而带来的延时和内存消耗。
  - 单个连接上可以并行交错的请求和响应，之间互不干扰
- 二进制分帧
  HTTP把报文全部换成二进制格式， 方便机器的解析
  Headers帧存放头部字段， Data帧存放请求体数据， 分帧后服务器看到的是一堆乱序的二进制， 不存在先后顺序， 也不会排队等待， 也就没有了HTTP队头阻塞的问题
  通信双方都可以给对方发送二进制帧， 这种二进制帧的双向传输的序列， 也叫做流
- 服务器推送
  在HTTP/2中， 服务器已经不再是完全被动地接收请求，响应请求
  服务器也能新建 stream 给客户端发消息，当 TCP 连接建立之后，比如浏览器请求一个 HTML 文件，服务器就可以在返回 HTML 的基础上，将 HTML 中引用到的其他资源文件一起返回给客户端，减少客户端的等待
  