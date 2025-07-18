``` toc

```
[SLA vs SLO vs SLI](https://blog.csdn.net/lquarius/article/details/120244396)
# 第一部分 概览
## 第1章 介绍
#### 确保长期关注研发工作
- SRE的运维工作限制在50%以内，剩余时间花在研发项目上
- 每8-12小时的oncall期间最多只处理2个紧急事件，否则
	- 每个问题就不能被详细调查清楚
	- SRE没有时间从中学习
- 事故复盘：根因、预防和优化解决 ==> “对事不对人”，目的是尽早发现和解决，而不是通过流程去绕过和掩盖
#### 保障SLO前提下最大化迭代速度
- 错误预算：1-可靠性目标 ==> 保障可靠性需求的同时加快功能上线速度
- 监控系统不应该依赖人来分析告警信息，应该由系统自动分析，仅当需要用户执行某种操作时才通知用户
- 可靠性
#### 监控系统
- 监控系统的三类输出
	- alert
	- 工单
	- 日志
#### 应急事件处理
- 可靠性 = func(MTTF, MTTR)
>MTTF 平均失败时间
>MTTR 平均恢复时间
- 做好预案+SOP通常可以使MTTR降低3倍以上
## 第2章 Google 生产环境：SRE视角
#### 硬件
![[Pasted image 20250703115301.png]]
- 每个DC内的物理服务器需要能够互相网络通信 ==> 使用一个虚拟网络交换机Jupiter（用Clos链接方式将几百台交换机连接起来），有几万个虚拟端口，在最大的一个DC内提供1.3Pb/s的交叉带宽
#### 管理物理服务器
- **Borg**：在集群层面管理任务的编排工作
- Borg启动任务时会为每一个实例安排一台物理服务器并执行具体的程序启动它，同时不断监控这些实例，如有异常则终止并重启它。

	每个任务在配置文件中声明所需的具体资源（核数/CPU等），Borg将任务实例合理分配在不同物理服务器上。
	
	Borg不会把某个任务的全部实例都运行在某一机柜上。
- 任务实例与机器没有一对一的固定对应关系 ==> Borg启动某一任务时会给任务实例分配一个名字+编号 ==> BNS（Borg名称解析系统）：将名称转换成IP:port
#### 存储
- 有任务实例的本地硬盘，集群级别的存储系统做作为永久性存储
#### 网络
- 全球负载均衡系统
	- 负载均衡DNS请求
	- 用户服务层面负载均衡
	- RPC层面负载均衡