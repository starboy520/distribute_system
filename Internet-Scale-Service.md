1. 这篇论文的名字`On Desigin and Deploying Internet-Scale Services`

   文章主要讲述是运维友好的系统的设计与开发

设计的三个初衷 

* Desigin for failure  任何组件都会失败
* KISS原则: 复杂设计会带来问题
* 一切自动化: 有人工参与的就会导致失败增加

## 应用整体设计 (Overall Application Desigin)
* Desigin For Failure: 系统是由很多组件组成的，组件需要合作，但是任何组件都可能失败，系统越大，失败越多，所以设计需要考虑这些
* Redundancy and failure recovery:  `security threat modeling ` 这是个什么鬼？意思是我们需要 考虑 任何可能的‘安全威胁’？
* Commondity hardware slice: 这段的主要意思我觉得跟经济化的意思吧， 类似google那种用旧机器，小机器集群，而不是像百度那种买大昂贵的服务器。 好处是
* Single-version sofeware： 
* Muliti-Tenancy: 这段的意思是平台化的思路， 对所有的业务方提供统一服务， 不需要物理隔离。我现在对这个持保留态度。

这些是服务设计的原则，下面是运维友好的服务需要的： 

* quick service health check: 感觉这跟服务的健康检查不一样， 更多是说在开发， check-in 之前的做好所有的健康检查（测试）.
* develop in the full environment:   跟上差不多，单测， 集成测试， 系统测试。 不过在目前的国内，又有多少公司能做大这一点？我在百度的第一个部门，是有这些的，所有的单测，集成测试，系统测试都是自我开发的，那个太赞了，可惜后来换了就没了。
* zero trust of underlying components: 开发时不要相信任何组件，需要考虑各种容错！
* Do not built the same functionality in multiple components: 这个没啥好说的，组建话，do not copy on paste. 原因无需多言
* One pod or cluster should not affect another: 内聚
* Allow （rare） emergency human intervention: 设计一个系统第一原则是大多数不要人工操作，但是恢复可能需要人工操作灰度步骤最好用脚本写出自动执行，而不是写文档让人工执行
* keep things simple and robust: 
* Enforce admission control at all levels: 准入控制， 流控啥的。优雅降级。
* Partition the service: 分库分表，一些原则
* Understand the network design:  什么鬼？
* Analyze throughput and latency: 
* Understand access pattern: what the impacts will the feature have on the rest of the infrastructure
* Versioning everything: 我们的目的是单一版本的软件运行， 但是在产品测试， 回滚等， 会同时有几个版本，
* keep the unit/functional tests from the last release: 这个很自然
* avoid single points of failure: load balance, 单点错误不能影响系统整体运行

## Automatic Management and Provisioning
自动化管理部署会涉及到架构设计的权衡，文章中以数据库主从同步来说明做到自动化需要的权衡, 同步/异步.

* Be restartable and redundant: 所有操作都可重操作，所有持久化状态都有冗余
* support geo-distribution: LDC吧，多机房，跨城市灾备
* Automatic provisioning and installing: 自动化部署，自动安装啥的
* Configuration and code as a unit: 配置和代码要关联，不要分开， 写好changelog， 定期扫描，确定安装部署失败
* Recovery as service level: 
* Never rely on local storage for non-recoveable information: 
* keep deployment simple: 避免复杂的安装部署操作
* Fail service regualarly: 生产环节测试异常， 断电， 断网，等等极端异常，没有经过生产环境的测试，容灾恢复方案可能都不会真正生效

## 依赖管理

* Expect latency: 外部系统的依赖一定要设置超时， 任何操作最好是幂等， 这样任何操作都可以retry， 
* Isolate failure: 防止级联失败， “fail fast”, 一旦发现服务挂了，就不要去请求了，防止把资源耗光
* Use shipping and proven components: 意思要用已经验证过的依赖服务， 最好不要当小白鼠. 稳定大过一切
* Implement inter-service monitoring and alerting: 系统内部需要有监控报警, 
* Decouple components: 在其他组件失败时，可降级，能继续工作。

## Release Cycle and Testing

1. 线上系统需要由足够冗余，即使新的service 出错， y可能快速恢复
2. 数据丢失， 状态相关的失败是不能容忍的，这是功能测试的范畴
3. 错误能被检测到，集成测试呗
这段还有一部分就是灰度发布， 逐级生效
另外一个反直觉的是，作者建议白天部署，而不是半夜。主要是白天解决问题的人多，夜里大家都睡了

* ship often: 经常发布release在错误率上并没有显著上升，反而在用户体验更好。
	* use production data to find problem:  "Quality assurance in a large-scale  system is a data-mining and visualization problem, not a testing problem"
	* Measureable release criteria:  制定制定的标准，并持续关注，监控。比如可用性99%，则就要持续关注，一旦触发红线，就要诊断解决
	* Tune goals in real time
	* Always collect the actual numbers: 收集真正的数据，而非概括性数据。
	* Minimize false positive： 数据错误会导致用户失去信任。
	* Analyze trends: 这边怎么感觉跟统计分析预测相关了。如果分析的数据和之前平常数据有很大出入， 则说明一定有问题
	* 
* Invest in engineering: 好的工程可以大大减少操作需求。经常犯的错误是花大规模时间在处理，而不愿意花时间去设计一个可用可扩展的架构
* Support version roll-back
* Maintain forward and backward compatibility: 向前兼容和向后兼容
* Single Server deployment: 单机可部署， 可测试。如果某些组件没法单机可以部署，那么需要写个测试模拟器.没有这个的话，单测就很难，全系统测试又是很难。。。
* Stress test for load： 压力测试呗
* xxxx
* test with real data:  复制用户的真实请求来进行线下模拟测试。由于线上用户的多样性，可能更准确测试问题，
* Test and develop in full environment: 


## Hardware Selection and Standardization
这块略

## Operation and capacity planning
这段主要讲容灾， 提前做好准备的一些best practice

* make the development team responsible: "you built it, you manage it"
* soft delete only: 两周左右的backup
* track resource allocation: ???? 
* make one change at a time: 这样容易找出问题
* make everything configurable: !!!

## auditing, monitoring and alerting
操作记录， 一旦有问题能第一时间知道做了什么，
报警也是一门艺术，每次报警需要准备爆出问题，否则，多了之后报警就容易被不关注，

* data is the most valuable asset: 


这篇文章感觉挺水的，不继续读下去了