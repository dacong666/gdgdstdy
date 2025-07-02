``` toc
```
[参考文章](https://blog.csdn.net/shengzhu1/article/details/72598311?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-2-72598311-blog-123642852.235%5Ev43%5Epc_blog_bottom_relevance_base9&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-2-72598311-blog-123642852.235%5Ev43%5Epc_blog_bottom_relevance_base9&utm_relevant_index=5)
# TCP协议
## TCP报文
#### 长度
tcp报文 = tcp header + tcp数据

header的最大长度为__60字节__，一共__六层__
- 前五层固定长度每层4字节 (32bit) ==> 五层共20字节
- 第六层可选项+填充最大为60-20=40字节

![[Pasted image 20250318152822.png]]
#### 字段含义
###### 序列号
==> 解决包乱序问题
###### 确认应答号
==> 解决丢包问题
###### 6位标志位
1. URG 紧急指针标志，为1时有效，为0时无效
2. ACK 确认序号标志，为1时有效，为0时忽略`确认应答号`
3. PSH 为1时提示接受端立刻从TCP缓冲区读走数据
4. RST 复位报文，要求断开、重建连接
5. SYN 同步报文，请求建立连接
6. FIN 结束报文，通知对方本端要关闭

###### 16位校验和
对整个TCP报文数据进行checksum计算，由发送端计算，接收端校验，如结果不一致则丢弃

	发送方：原码相加 ，并将高位叠加到低位，取反，得到反码求和结果，放入校验和  
	接收方：将所有原码 相加，高位叠加，如全为1，则正确
###### 16位紧急指针
URG=1时生效，是一个偏移量，与`序号`相加表示紧急数据最后一个字节的序号

# 状态机
## 三次握手
![](https://img2020.cnblogs.com/blog/916005/202011/916005-20201110000506806-148773323.png)

#### SYN攻击
两次握手之后的TCP连接称为**半连接**，Server处于`SYN_RCVD`状态。

三次握手时，内核会维护两个队列：
- 半连接队列，SYN队列
- 全连接队列，accept队列

SYN攻击是Client在短时间内**伪造大量不存在的IP地址**，并向Server不断发送SYN包，Server回复确认包等待Client确认，由于源地址不存在，Server需要不断重发直至超时，这些伪造的SYN包**长时间占用SYN队列导致正常的SYN请求因队列满而被丢弃**，从而引起网络堵塞甚至系统瘫痪。
>[!faq]- 如何解决？
>
## 四次挥手
#### 1. time_wait
在TCP 四次挥手阶段，在第四次挥手之后(i.e. client对server的FIN&ACK回复了ACK之后)，client会处于time_wait状态至少2MSL。

 > [!faq]- time_wait至少2MSL的原因
 > 1. 保证最后一个ACK能到server
>如果server没收到4th挥手的ACK可以重试一次3rd ==> 2MSL
>2. 防止下一个连接中出现本次连接的旧报文