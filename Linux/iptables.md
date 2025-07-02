``` toc
```
## 基本概念
1. 链 chain
每个链是一个处理“阶段”，由一堆规则组成并按顺序匹配执行

| 链名            | 作用说明                           |
| ------------- | ------------------------------ |
| `PREROUTING`  | 数据包刚到主机（i.e. 网卡），还没路由前处理（DNAT） |
| `INPUT`       | 处理进入本地的包                       |
| `FORWARD`     | 处理需要转发（不进入本机）的包                |
| `OUTPUT`      | 处理本机发出的数据包                     |
| `POSTROUTING` | 处理路由完毕即将发出的数据包（SNAT）           |

2. 表 table
每张表包含不同的链 --> 不同阶段处理数据包

| 表名         | 用途                         | 链                               |
| ---------- | -------------------------- | ------------------------------- |
| `filter`   | 默认表，负责数据包的过滤（如防火墙）         | INPUT, FORWARD, OUTPUT          |
| `nat`      | 负责网络地址转换（如SNAT、DNAT）       | PREROUTING, POSTROUTING, OUTPUT |
| `mangle`   | 修改数据包的服务类型、TTL等标志位         | 五条链                             |
| `raw`      | 设置是否对该数据包进行状态跟踪（conntrack） | PREROUTING, OUTPUT              |
| `security` | SELinux用                   |                                 |
# 冷知识
iptables发生在tcpdump之后，所以即使有iptables filter，用tcpdump抓包依然能抓到
