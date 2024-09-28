# 八股

## 基础知识

### 小林coding

https://xiaolincoding.com

### javaguide

基础版

https://javaguide.cn

知识星球

https://wx.zsxq.com/group/48418884588288/topic/118811814512212

## C++

https://www.zhihu.com/column/c_1431591081293426688

https://www.nowcoder.com/discuss/454697528508870656

https://zhuanlan.zhihu.com/p/470874027

## 计网

### tcp

- 面向连接
- 可靠
- 字节流

序列号解决乱序问题

确认序列号解决丢包问题

### tcp和udp的区别

可靠性

- tcp保证数据可靠到达
- udp不保证数据能够可靠交付
  - 除了quic

面向对象个数

- tcp：一对一
- udp：一对一、一对多

面向连接性

- tcp传输数据前需要建立连接
- udp不面向连接，即刻发送

首部开销不同

- tcp 20个字节
- udp 8个字节

传输方式

- tcp流式传播，没有边界
- udp以包的形式传播，有边界

分片不同

- tdp基于mss（tcp）
- udp基于mtu（ip）

应用场景

- tcp：fts，http/https
- udp：dns，音视频通信

为什么三次握手，不是两次、四次

- 避免历史连接
  - 服务端的syn_recv状态保证了其能够对历史连接发送ack让客户端知晓这是历史连接
  - 如果是两次握手，此时服务端处于estabished状态，已经为历史连接建立一个连接并发送数据，浪费服务器资源

- 保证双方发送和接收能力
  - 三次握手确认双方的初始序列号
  - 四次握手其中的第二步和第三步可以被优化为一步，因此为三次握手
- 避免资源浪费
  - 如果只是两次，服务端无法保证客户端接收到自己发送的ack就建立了连接，会接收到无用的syn报文建立冗余的连接，造成资源浪费