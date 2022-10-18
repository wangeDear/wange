
# 思维导图
<iframe id="embed_dom" name="embed_dom" frameborder="0" style="display:block;width:640px; height:480px;" src="https://www.processon.com/embed/6344dd2e0e3e740c57fdd769"></iframe>

## CAP理论

1998年，由加州大学计算机科学家提出。
一共三项指标，分布式系统最复杂的就是同步所有节点的状态，三项指标只能同时满足其中两项
- Consistency 一致性
	- 弱一致性，对于在某一节点发生了写操作，其他节点无法感知到更新，导致各个节点的数据不能及时同步，出现数据不一致的情况
	- 强一致性，单实例数据库、[[Mysql事务隔离级别]]，当数据发生变更时其他节点能立马感知到
	- 最终一致性，数据发生变更后，在一定时间之后同步到其他节点
- Availability 可用性
	- 在请求未发生故障的节点，在一定时间范围内仍能返回相应结果
- Partition tolerance 分区容错性
	- 分布式系统容错备份
![[Drawing 2022-10-11 15.05.45.excalidraw]]

[四万字32图，Kafka知识体系保姆级教程宝典_五分钟学大数据的技术博客_51CTO博客](https://blog.51cto.com/u_14932245/4340967)