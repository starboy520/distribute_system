1. 这篇论文的名字` On Desigin and Deploying Internet-Scale Services`

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
