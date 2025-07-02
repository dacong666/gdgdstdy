``` toc
```
## What
虚拟路由冗余协议，Virtual Router Redundancy Protocol
==> 用于提高网络可靠性的容错协议

在主机的下一跳设备出现故障时，及时将业务切换到备份设备，从而保障网络通信的连续性和可靠性。

![[Pasted image 20250310173257.png]]
## 工作原理
#### 三种状态机
1. initialize
- 不可用状态
- 设备启动时或检测到故障时会进入Initialize状态
2. master
- 承担转发工作 + 定期向整个虚拟内发送VRRP通告报文
3. backup
- 不承担转发工作 + 定期接受master发送的VRRP通告报文，以判断master的工作状态是否正常
#### 选举机制
VRRP备份组建立后，各设备会根据所配置的优先级来选举Master设备，当master发生故障时，其余设备会根据优先级选出新的master。

![[Pasted image 20250310174919.png]]
#### 原理
虚拟路由器的IP地址可以与其中一个路由器设备的IP相同，也可以与每台设备的IP都不同但处于同一个网段
1. Master设备发送ARP报文，将虚拟MAC地址通知给与它连接的设备/主机，承担报文转发工作
2. Master定期向备份组内其他设备发送VRRP通告报文，通告其配置信息（优先级等）和工作状况
3. 如果Master故障则重新选举Master，新Master会立即发送携带虚拟路由器的虚拟MAC地址和虚拟IP地址的ARP报文，刷新与它连接的设备/主机的MAC表项，将用户流量引到新Master来
4. 原Master故障恢复时，
若其为IP地址的拥有者（优先级=255），则将其切换至Master状态；
若其优先级<255，则先将其切换至Backup状态，其优先级仍为故障前的配置
5. 若Backup设备的优先级高于Master，则由Backup的工作方式（抢占式/非抢占式）决定是否重新选举Master

![[Pasted image 20250311173336.png]]
## 应用场景
#### 与上行接口状态联动
当Master的上行接口异常时，Master设备的优先级会降低，当低于Backup优先级时，Backup设备会成为Master

![[Pasted image 20250311173407.png]]

#### 与路由状态联动
当Master的上行路由异常时，Master设备的优先级会降低，当低于Backup优先级时，Backup设备会成为Master
![[Pasted image 20250311174046.png]]