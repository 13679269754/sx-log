又见删库...
=======

[mp.weixin.qq.com](https://mp.weixin.qq.com/s?__biz=MjM5NzAzMTY4NQ==&mid=2653933338&idx=1&sn=d74a84626505d6a116384d4552a2fa91&scene=21#wechat_redirect)叶金荣 老叶茶馆

*** ** * ** ***


这两天，香港上市公司微盟(HK2013)因"删库"事件停运，已经过了36小时还在努力抢修数据的工作中。作为一位老DBA，我们一起来回顾和尝试反思下这个事件。

### 0. 事件回顾


2020.2.23日 18:56，员工通过VPN登入服务器并实施破坏。   
2020.2.23日 19时，系统监控报告故障并启动应急方案。   
2020.2.24日 微盟公司向警方报案。   
2020.2.25日 7时，恢复部分生产环境和数据，并预计到凌晨0点能完成恢复，并向新用户恢复业务，但老用户预计还要到2月28日晚上才能恢复。

### 1. 为什么会发生"删库"


从官方发布的公告来看，是因为运维部的核心员工刻意进行的破坏，也就是说，这是人为的、恶意的、有计划的破坏行为，而不是我们最常见的误操作或黑客入侵所致。

不过，从我的经验来看，这起事件未必是真的人为破坏，具体分析就不贴了。总之，我对官方的公告存疑。不过也不能改变人为破坏这个事实，就看公安机关怎么定性了。

我们要做的是，进行反思和预防此类事件一再发生，这也是本文的用意。

此外这种意外事故受害的除了公司、员工，更无辜的是客户，我们祝福微盟能救回更多数据，将损失最小化。

### 2. 事故恢复的速度如何


从上面的回顾时间点来看，我认为恢复的速度并不算快。

我经过侧面了解，这起事件主要的影响是数据库的主备库都被删了，并且执行的是类似"rm -fr /"这样的操作。这种行为，基本上只能通过其他备库，或物理备份来恢复了。

从事后恢复情况来看，应该是没有更多可用的备库了，但备份数据应该是还有的，所以才需要花费这么长时间。

此外，备份数据恢复完后，通常还需要有一个校验核对的过程，所以一般会先发公告安抚客户的情绪。

不过新旧用户恢复服务的时间并不同，我们由此甚至可以猜测，备份机制可能不合理，新数据的备份更及时，旧数据的备份有延误，或者比如因为旧数据的量太大了导致延迟更久。

这次更糟糕的事，赶上特殊情况，大家都在家远程办公，协同起来肯定更慢，也影响了恢复速度，真是祸不单行。幸运的是，听说腾讯云已有多位技术专家参与了拯救工作，希望能尽快恢复。

### 3. 事件反思和预防


这次的事件，不同于常见的黑客入侵或误操作，而是源于内部发起的破坏，这种是最可怕、最难防范的行为。

我相信绝超过80%甚至90%的中小型公司，都无法避免这个问题。毕竟中小型公司的人员规模有限，想要进行非常细致的权限分级也不太现实，更容易因此降低工作效率和员工的积极性。

尽管如此，我们也尝试做点什么来预防此类事件再次发生。

#### 首先，是权限分级


我们知道，为了提高工作效率，会部署自动化运维工具。但这样一来，也极大增加了误操作带来的风险。本次事件中，短时间内造成大面积服务器故障，基本可以断定是因为工具批量分发命令导致的。

所以，一定要进行权限分级，也包括业务范围分级。例如可以尝试以下方案：

a. 角色分级。   
区分业务运维、系统运维、网络运维、DBA等多重角色，每个角色都只能接触自己所负责的那票业务服务器，以及相应可执行的权限。   
例如，业务运维、网络运维、DBA等都不能执行系统层的rm指令，系统运维也不能执行数据库的指令。

b. 权限分级。   
区分一级执行权限、二级执行权限及审批权限。   
例如，我们可以实施这样一套方案，一级权限的人发起某个操作请求，有审批权限的审核校验这个命令是否合理，再由二级权限的人去真正实施，这样基本可以防范人为破坏了，除非最后落地时是由同一个人来承担所有角色，或者嫌麻烦绕过这个规范。

分级措施想做到位，就得有足够的人员，公司上市的目的就是通过融资以改善运营状况，该招人就招人吧。

#### 其次，备份、备份、备份


备份的重要性无需多言。

但其实，不只是做了备份就可以的，还有几点要注意的。   
a. 除了本地备份，还应该有异地备份，并且要区分本地备份和异地备份责任人的权限，交由不同等级的人管理，防止恶意破坏时，把全套备份都一把火烧了。   
b. 除了逻辑备份外，还应该有物理备份，物理备份恢复起来会更快一些。   
c. 除了备份，还应该做好备份校验，确保备份的有效性，也就是随机抽取备份集进行恢复测试，确保备份文件的可用性（我多年运维从业经历，仅有一次比较严重的故障，就是栽在没及时进行备份恢复测试校验）。

#### 最后，做好防灾演练


防灾演练的确比较难做，毕竟没几个人敢真的在线上全盘执行"rm -fr /"这样的操作。

不过依然可以模拟各种可能的情况，以及不同情况的组合，再针对这些情况制定不同的预案，然后在开发、测试环境尝试进行演练。

而且要不定期的进行演练，让各个岗位的责任人熟悉整套流程。就像在日本，中小学总是不定期进行防灾演练一样，演练次数多了，真遇到问题时，自然就不慌了，恢复起来也会更快。

最后的最后，多给员工一些必要的关怀和培训吧。还有，作为管理者，对负责后端的运维部门也多给些重视，运维部门一旦出个事故，是真的有可能会搞垮一家上市公司的，这并不是没有前车之鉴。

#### 延伸阅读

* [MySQL数据安全策略](http://mp.weixin.qq.com/s?__biz=MjM5NzAzMTY4NQ==&mid=405129803&idx=1&sn=6c03712e91c59bfdf7a41cb8cff9360d&chksm=3b3150210c46d9373e13188a53b0d0ef69c0e15744c0fe9389cb0865789cd784aadb61860d91&scene=21#wechat_redirect)

* [我猜你一定达不到要求的《MySQL安全策略》](http://mp.weixin.qq.com/s?__biz=MjM5NzAzMTY4NQ==&mid=2653930385&idx=1&sn=4314ef01817ed05fa58ea245fa833070&chksm=bd3b59fb8a4cd0ed3b2ca4312ea0907eb4cb858753f3afd340be4203aca2fc0fd09255e5a337&scene=21#wechat_redirect)

* [简单几招提高MySQL安全性](http://mp.weixin.qq.com/s?__biz=MjM5NzAzMTY4NQ==&mid=2653931327&idx=1&sn=5efb74a1da94524452a997f793f277e7&chksm=bd3b5d558a4cd4436eda517d718da43d72c85645396d754eac8061f5ddc5f8abaf4794888272&scene=21#wechat_redirect)

我的新课程《**MySQL性能优化**》已经在腾讯课堂发布，本课程讲解读几个MySQL性能优化的核心要素：**合理利用索引，降低锁影响，提高事务并发度**。

目前已有几个录播视频，并且可以回看5-15分钟不等。

![](https://image.cubox.pro/cardImg/2024022715235079970/25405.jpg?imageMogr2/quality/90/ignore-error/1)

