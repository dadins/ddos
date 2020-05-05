# **SYN算法原理**

SYN防护算法有三个：
SYN重传算法
SafeReset算法
syncookie算法

## SYN重传算法：

```sh
原理：
TCP是一个可靠网络，在发出SYN请求之后，如果在指定时间未收到请求，则认为报文被丢弃，会重传该报文，重传间隔在RFC中有规定。

参考资料：
SYN包初次重传间隔：  3s(RFC 1122)   1s(RFC 6298 2.1)
```

## TCP SYN算法1（RESET算法）：
```sh
原理：
1. 客户端发送SYN;
2. ADS拦截回复一个错误的SYN+ACK（实际上只发一个ACK标记也可以，因为只检测一个ACK标志位）;
3. 客户端检测SYN+ACK发现错误，会回复一个RST包（ADS验证这个RST包，将源IP加入信任表）;
4. 客户端重传SYN包;

SYN算法1之后，之所以client会重传SYN(上面第4步)，是因为RFC规定了：
丢弃这个包，但并没有要求删除TCB、重传队列，所以连接还在，重传队列里面还是有数据的，定时器会重传队列里的报文。

参考资料：

RFC793 Page 66
If the state is SYN-SENT 
    then first check the ACK bit If the ACK bit is set If SEG.ACK =< ISS, 
    or SEG.ACK > SND.NXT, send a reset (unless the RST bit is set, 
    if so drop the segment and return) <SEQ=SEG.ACK><CTL=RST> and discard the segment. Return. 
    If SND.UNA =< SEG.ACK =< SND.NXT then the ACK is acceptable. 

TCP reset算法来源1：
    RFC 793
        page 33
            Figure 9
    
TCP reset算法来源2:
    RFC 793
        page 34
            Figure 10
```

## SYN 算法2（syncookie）:
```sh
原理：
1. 客户端发送SYN;
2. ADS拦截回复一个序列号特殊构造的SYN+ACK（正常的包）;
3. 客户端检测SYN+ACK正确，会回复一个ACK包（ADS验证这个ACK包，将源IP加入信任表）;
4. ADS发送一个RST包，中断连接;

SYN 算法2之后，client会不会重传SYN，全看客户端的实现，因为RFC规定，在ESTABLISHED之后，如果收到了RST包，会删除TCB，重传队列也要清空。RFC793 Page70
ESTABLISHED 
FIN-WAIT-1 
FIN-WAIT-2 
CLOSE-WAIT 
    If the RST bit is set then, any outstanding RECEIVEs and SEND should receive "reset" responses.
    All segment queues should be flushed. Users should also receive an unsolicited general "connection reset" signal.
    Enter the CLOSED state, delete the TCB, and return. 

 
 通常在ADS这种抗D设备上，采用Syncookie是没有问题的（因为会要求客户端重新建立连接），但是在服务器上启用syncookie就会存在问题：
 因为server端在收到syn报文之后，不会记录任何信息，所以某些TCP选项（timestamp等）不可用。具体请参考：
 https://blog.cloudflare.com/syn-packet-handling-in-the-wild/
```
