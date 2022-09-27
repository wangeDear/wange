---
categories:
  - 分类测试1
  - 分类测试11
---
### 两阶段提交

1. 一阶段本地事务、生成undo_log并提交
2. 全局提交或回滚

### 全局事务

多个模块（不同库之间）进行互相调用，进行统一的事务提交，通过代理datasource、connection（本地只有一个数据库连接与sharding jdbc不同）实现全局事务并记录undo log、进行全局事务提交或回滚

## 全局事务实现

### 获取全局事务xid

首先通过GlobalTransactionScanner将GlobalTransactional注解的class method增加GlobalTransactionalInterceptor拦截器，主要通过spring org.springframework.aop.framework.autoproxy.AbstractAutoProxyCreator#wrapIfNecessary，AdvisedSupport.addAdvisor方法添加GlobalTransactionalInterceptor，带有全局事务注解方法执行时都会进入该拦截器，io.seata.spring.annotation.GlobalTransactionalInterceptor#handleGlobalTransaction处理全局事务，transactionalTemplate.execute执行事务，io.seata.tm.api.TransactionalTemplate#beginTransaction开启全局事务，最终通过io.seata.tm.api.DefaultGlobalTransaction#begin(int, java.lang.String)成功开启事务DefaultTransactionManager远程调用seata server获取xid，整个事务绑定xid。

![](https://blog-1258875084.cos.ap-guangzhou.myqcloud.com/picGoImg20211207175217.png)

### dubbo接口传递xid

xid一次全局事务的唯一id，各个分支事务的id同一个xid，根据这个xid查找分支事务，commit或rollback时获取各分支事务进行提交或回滚。
AlibabaDubboTransactionPropagationFilter继承了com.alibaba.dubbo.rpc.Filter，当发生rpc调用时利用dubbo隐式传参的形式传递xid

![](https://s2.loli.net/2021/12/07/yKGTeaWFAR7uB3Y.png)

### undo_log、提交本地事务

statementProxy.execute
再通过ExecuteTemplate#execute执行sql，再调用io.seata.rm.datasource.exec.AbstractDMLBaseExecutor#doExecute区分是否自动提交，如果是自动提交则关闭自动提交，再执行executeAutoCommitFalse方法，查询beforeImage执行前镜像数据，再statementCallback.execute(statementProxy.getTargetStatement(), args);获取afterImage镜像数据，组装prepareUndoLog存如ConnectionContext中，在commit之前调用flushUndoLogs insert到undo log表中，并commit事务。

![](https://s2.loli.net/2021/12/07/FbsInlGqRzSx1dL.png)

### DatasourceProxy



### ConnectionProxy
