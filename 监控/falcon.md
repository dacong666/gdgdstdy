```toc

```
![](https://i-blog.csdnimg.cn/blog_migrate/dcb2ec95556bfba7fd0ab75e91a47096.png)
![[Pasted image 20250517214615.png]]
![](https://i-blog.csdnimg.cn/blog_migrate/482b301ab2d4410ccfa3a559fcc13ed7.png)

## 核心架构
#### agent：数据采集
- 在目标机器，采集指标（CPU、内存、磁盘等）并上报
- CPU/内存/磁盘等指标直接通过/proc文件系统读取
#### transfer：数据转发
- 接收agent数据，对指标用一致性哈希分发给后端存储（judge/graph）==> 每个指标到达相对固定的judge/graph节点
#### judge：告警判断
- 实时计算指标阈值，触发告警（将告警事件写入redis，由alarm定期读取并发送）
- 如有合并规则，则会合并告警
- judge对每条规则维护一个滑动窗口队列，存储最近的历史数据点
#### graph：绘图数据存储
- 时序数据存储，使用环形数据库（RRD，Round-Robin Database）
>RRD：固定大小的存储空间循环覆盖旧数据
#### alarm：告警
- 告警去重、通知