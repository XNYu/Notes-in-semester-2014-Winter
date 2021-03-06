#Ch5 Software Architecture Styles
##一、风格的简介
建筑有其风格（一系列特点），决定了它们有不同的质量和对材料不同的要求  
软件同样如此，每个特征代表一系列特征，同时对软件的构造有要求

* 体系结构的风格是**一系列系统**的共性特点
* 体系结构是**组织结构**上的共性特点

在风格中，组件被弱化，重点关注连接件和决策

*将风格划分到不同层面可能是没有道理的，不过确实有些风格只在特定的层面上有意义*  
设计模式与风格会有共性
##二、模块级别的风格
由连接件与设计决策的不同，产生了不同的风格
> ######例：KWIC系统
> 与大二不同，关注点发生变化，增加了其它质量  
> 见ppt
###1.Call&Return风格
####主程序与子程序风格
* 功能分解，逐步精化的思想
* 功能分解结束后分配数据
* 正确性很容易控制  
*建立在绝对不违反风格的前提之下*
* 数据一般在底层共享
* 严格的单线程控制
* 子系统的结构非常明显
* 严格的层次关系仅限于逻辑层次  
物理包不受此限制
####面向对象式风格
#####中间阶段：数据结构为中心风格（Data driven）
* 既要**列举**功能，又要**列举**数据（注意不是分解）
* 再做分配，分配时以数据与功能的关联性为中心做决策
#####面向对象式
* 列举类/对象与职责/协作
* 再把职责分配给对象
* 进行封装，暴露接口
* 只要设计模块时符合此思想即可，具体是否以OO的方式实现并不重要
* 完全的面向对象式在保证正确性上很困难  
此时产生了Controller，但这是**反面向对象**的
* 对以数据为中心的应用进行有效的封装
* 解决了公共耦合的问题，提高了可修改性  
模块内部可自由修改
* 系统更易开发
* 容易产生**重入**的问题
####两者的风格比较
* 不同的思考方式  
将数据分配给功能VS将行为分配给数据/将职责分配给对象
###2.Pipe and Filter风格
* 此风格不涉及任何控制权的关系，只代表数据的流向
* 管道负责两个过滤器间的联系
* 每个过滤器不知道数据会被管道交给哪个过滤器
* 由于没有控制权的转移，理论上所有过滤器的运行是并发的
* 接口可以任意修改，只要保证数据正确  
因为没有任何调用  
*数据的修改需要改变管道*
* **完全满足OCP原则**
* 连接件往往都是显式的  
可以认为每一个pipe构成流水线
* Pipe的工作通常不复杂  
如果管道的工作复杂度超过过滤器，这样的管道/过滤器设计是失败的
* 适用于对**同一批数据**做**增量型处理**
####限制
* 过滤器之间不共享数据
* 每个过滤器都不知道其前趋与后继  
*设计时要存在先后观念，但开发时关注运行时表现，不能因顺序造成行为的差异*
* 对开发人员而言，最终的正确性不能依赖于顺序  
极端情况下开发时也不考虑顺序
####优点
* 易于理解
* 支持复用
* 过滤器之间互相独立
* 易于扩展
* 可进行特定的路径分析
* 支持并发  
*但不一定是并发的*
####缺点
* 交互非常差  
因为没有全局信息
* 消耗额外的数据传输空间
* 管道的复杂性可能超过过滤器  
这种情况下，要么是设计不合适，要么是此风格不适用
####特化
* 流水线  
严格规定线性的过滤器序列
* 批处理  
要求每个过滤器获取所有数据后再执行
###3.隐式调用风格
* 所有模块之间最终仍然以调用的方式实现，但调用的方式采用**事件机制**
* 不同模块之间不存在任何过程调用
> ######三种风格的比较
> 见ppt
> 
> * 不同的风格在不同方面有特长，选择哪个要根据项目的需求
> * 每个风格是一组规范，不能轻易违反  
> 选择了一种风格，就要保持前后的一致性

* 增加了广播系统  
本身不承担任何需求，只将调用机制交给自己进行处理
* 行为
	* 声明事件
	* 订阅事件
	* 抛出事件
	* 监听事件
####规则与约定
* 组件之间不直接相互调用
* 每个事件的抛出者并不知道接收者是谁  
某些情况下可能会增加**截流**和**争听**的机制
* 不能假定事件的接收顺序
* 不能假定事件一定会有人接收

这些约定保证了组件之间的完全独立
####优点
* 易于复用
* 易于测试
* 易于演化
* 易于诊断
####缺点
正确性太难保证

*隐式调用风格适合于松散耦合系统（交互不多），但使用还是要慎重*
###4.分层风格
* 上一层调用下一层，下一层为上一层提供服务
* 上下层的接口是稳定的  
接口可以抽象出来
* 只要接口不变，层内可以随意变化
* 一般复杂系统往往倾向于使用分层风格
* 通过逐层抽象，将复杂的东西分解为层与层的叠加  
非常有利于系统的加强
* 并不是所有系统都可以分层
* 由于逐层调用的原因，难免会牺牲一些性能
* 有特例允许做跨层调用
####MPS与分层风格的区别
* **核心区别**：横切与纵切  
主程序子程序风格中，结构的联合不能构成层的概念，而一层一定是完整的
* 传递数据与传递控制
###5.MVC风格
* 以程序调用、内存访问、消息等方式作为连接件
* 特别适合于Web  
因为可以由不同的View共享同一个Model
* 可以实现视图与控制器的任意插接
* 与大多数现代的图形工具界面不兼容
* 在Web程序中不需要模型变更的通知
####非Web MVC与分层的区别
#####分层风格
* 抽象层次不同
* 层间协议稳定
* 可重用、可修改
#####MVC
* MVC和着力点在技术上  
HTML、Servelet、Java Bean
* 使用控制者、观察者、策略模式
* 现代所讲的MVC是三个主题的切分，但并不十分完整
###6.存储库（黑板）风格
* 黑板本身也是一个组件
* 连接件可以是程序调用，也可是内存访问  
倾向于将它们封装起来
####约束
* 所有agent相互独立
* 每个agent都依赖于共享的数据区
####消极与积极
#####存储库风格（拉模式）
存储库功能非常小，agent以轮询的方式定时扫描数据库
#####黑板风格（推模式）
同样类似隐式调用风格维护一个目录，在数据被改变，满足某个条件后通知相关的agent  
####应用
所有以数据为中心的应用都可以使用存储库风格

* 数据库
* 专家系统
* 编程环境
####优点
* 能够存储大量数据
* 中心区可以做集中处理
* 并发、安全与控制
####缺点
* 不是所有系统都能归纳出数据模型
* 中心会成为瓶颈点
* 数据演化的代价非常大  
即使是敏捷软件开发时，数据模型也是无法逐步重构的
###7.解释器风格（略）
* 以解释型语言的方式完成一项任务
* 配置式编程与产生型变成
* 通常有一个状态机  
用来解释程序与数据的结合
###8.例：移动机器人
见ppt
###9.总结
事件、分层、blackboard、pipe and filter特别适合复杂系统  
复杂到一定程度后，简单的风格无法解决问题  
**风格从低到高演化的过程就是逐渐*****解耦*****的过程**  
*耦合解除太多后，影响范围不可控，可修改性、可开发性增加，而可测试性与正确性就难以保证*  
使用隐式调用和黑板风格后，一般要加个监视器，把问题及早发现，及早解决  
Pipe and Filter可以通过加一个Master Control来解决
##三、进程级别的风格
###1.进程风格的设计理念
考虑时间与空间是否处于耦合状态  
进程通信是否同时发生？相互需要了解逻辑空间？
###2.Point-to-Point风格
* Socket通信
* RPC通信
* 同一设备上的消息机制
###3.发布—订阅机制
* 与隐式调用类似，但这里的事件可以跨网络
##四、物理单元级别的风格
C/S与D/S是物理单元的两种基本风格
###1.C/S风格
* 客户端知道服务端的地址
* 服务端不知道客户端的数量与身份
* 客户端之间彼此不知道
* 连接件可以是典型的网络通信和RPC等
* 服务器先初始化，之后等待客户端的连接
* 客户端主动发起连接
###2.三层（N层）风格
Layer是功能、逻辑的划分，而tier是服务器结构的划分

* 将数据吞吐负载与业务处理的负载分离，减缓压力
* *在Web 2.0时代，正在努力恢复胖客户端*
###3.Peer-to-Peer风格
* C/S的扩大化，分散服务器的压力
* 每一个端既是服务器，又是客户端
* 需要广播的心跳机制进行服务器的动态更新与发现  
P2P软件的核心就是广播与控制机制
* P2P软件的“Server”更多地是Monitor的作用
* 但这不是标准的分布式系统，只是将其扩大化
###4.分布式风格
* 客户端不知道服务端在哪里
* 分布式计算——网格计算——服务计算——云计算一脉相承
* 通过中间件，使得底层的分布透明化
##五、不同风格的综合使用
* Systems are *not usually developed according to a single, consistent idiom.*  
* Variations may occur at different levels of refinement/abstraction.