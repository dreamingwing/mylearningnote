## OSI七层网络协议

1. 物理层->传输比特数据
2. 数据链路层->将比特数据包装成贞
3. 网络层->将网络地址转化为对应的物理地址，路由的选择，包装数据成为数据包
4. 传输层->解决传输质量问题，决定发送速率
5. 会话层
6. 表示层->解决不同系统之间的不同
7. 应用层

## TCP/IP协议

1. 链路层
2. 网络层
3. 传输层
4. 应用层

## TCP三次握手

![TCP ä¸æ¬¡æ¡æçå¹²è´§](http://static2.iocoder.cn/04edb57536fc9bbd3ea096cafa5d3630)

- 第一次握手：Client 将标志位 `SYN=1` ，随机产生一个值 `seq=J` ，并将该数据包发送给 Server 。此时，Client 进入SYN_SENT 状态，等待 Server 确认。

- 第二次握手：Server 收到数据包后由标志位 `SYN=1` 知道Client请求建立连接，Server 将标志位 `SYN` 和 `ACK` 都置为 1 ，`ack=J+1`，随机产生一个值 `seq=K` ，并将该数据包发送给 Client 以确认连接请求，Server 进入 `SYN_RCVD` 状态。此时，Server 进入 SYC_RCVD 状态。

- 第三次握手：Client 收到确认后，检查ack是否为J+1

  是否为 1 。

  - 如果正确，则将标志位 `ACK` 置为 1 ，`ack=K+1` ，并将该数据包发送给 Server 。此时，Client 进入 ESTABLISHED 状态。
  - Server 检查 `ack` 是否为 `K+1` ，`ACK` 是否为 1 ，如果正确则连接建立成功。此时 Server 进入 ESTABLISHED 状态，完成三次握手，随后 Client 与 Server 之间可以开始传输数据了。

  仔细看来，Client 会发起两次数据包，分别是 `SYNC` 和 `ACK` ；Server 会发起一次数据包，包含 `SYNC` 和 `ACK`。也就是说，三次握手的过程中，Client 和 Server 互相做了一次 `SYNC` 和 `ACK` 。

## 四次挥手

![TCP åæ¬¡æ¥æçå¹²è´§](http://static2.iocoder.cn/2cf7b8d1689ae6e920ef1b2c3eafb8b2)

- 第一次挥手：Client 发送一个 `FIN=M` ，用来关闭 Client 到 Server 的数据传送。此时，Client 进入 FIN_WAIT_1 状态。
- 第二次挥手，Server 收到 `FIN` 后，发送一个 `ACK` 给 Client ，确认序号为 `M+1`（与 `SYN` 相同，一个 `FIN`占用一个序号）。此时，Server 进入 CLOSE_WAIT 状态。注意，TCP 链接处于**半关闭**状态，即客户端已经没有要发送的数据了，但服务端若发送数据，则客户端仍要接收。
- 第三次挥手，Server 发送一个 `FIN=N` ，用来关闭 Server 到 Client 的数据传送。此时 Server 进入 LAST_ACK 状态。
- 第四次挥手，Client 收到 `FIN` 后，此时 Client 进入 TIME_WAIT 状态。接着，Client 发送一个 `ACK` 给 Server ，确认序号为 `N+1` 。Server 接收到后，此时 Server 进入 CLOSED 状态，完成四次挥手。

