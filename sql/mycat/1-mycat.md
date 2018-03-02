* https://github.com/MyCATApache/Mycat-Server


https://github.com/MyCATApache/Mycat-Server
* http://www.mycat.io/
* 从零开始 http://songwie.com/teachs?searthstr=Mycat&start=0&limit=100
* 从零开始 http://songwie.com/articlelist/11


# MyCat：开源分布式数据库中间件

* [MyCat：开源分布式数据库中间件-CSDN.NET ](http://www.csdn.net/article/2015-07-16/2825228)

* MyCat
  * 解决数据存储和业务规模迅速增长情况下的数据瓶颈问题
  * 通过MyCat统一管理所有的数据源
    * 引入连接复用解决多应用竞争问题
  * 独创ER关系分片
  * 采用全局分片技术，每个节点同时并发插入和更新数据，每个节点都可以读取数据
* 技术原理
  * 拦截用户发送过来的SQL语句
    * 分片分析
    * 路由分析
    * 读写分离分析
    * 缓存分析

# Mycat 分布式事务的实现

* [Mycat 分布式事务的实现 ](http://mp.weixin.qq.com/s/ocL4MOzdjvHydtavzRos2w)

* ACID
* XA规范
  * 分布式事务处理模型
    * 应用服务AP
    * 事务管理器TM：交易中间件
    * 资源管理器RM：数据库
    * 通信资源管理器CRM：消息中间件
  * 全局事务
    * 分布式事务处理环境中，多个数据库共同完成一个工作
* 二阶段提交
  * 协调者向所有参与者询问是否可以执行提交操作
  * 参与者将undo和redo信息写入日志
  * 参与者响应协调者
    * 参与者事务执行成功，返回同意
    * 参与者事务执行失败，返回中止
  * 缺点
    * 同步阻塞：参与者都是事务阻塞
    * 单点故障：协调者单点故障
    * 数据不一致
* 三阶段提交
  * 三个阶段
    * CanCommit阶段：协调者向参与者发送Commit请求，参与者通过则返回Yes，否则返回No
    * PreCommit阶段：协调者根据参与者的反应情况来决定是否可以记录事务的PreCommit
      * 所有参与者响应Yes，执行事务
      * 有任何一个参与者发送No或超时，则事务中断
    * DoCommit阶段
      * 执行提交
        * 收到ACK响应，从预提交进入提交状态，向参与者发送DoCommit请求
        * 参与者执行正式事务提交
        * 事务提交后，向协调者发送ACK响应
      * 中断事务
        * 向参与者发送abort请求
        * 参与者利用undo信息来执行事务回滚操作
        * 完成回滚，向协调者发送ACK消息