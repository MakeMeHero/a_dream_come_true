⽀持当前事务的情况：

 TransactionDefinition.PROPAGATION_REQUIRED： 如果当前存在事务，则加⼊该事务；如果当 前没有事务，则创建⼀个新的事务。 

TransactionDefinition.PROPAGATION_SUPPORTS： 如果当前存在事务，则加⼊该事务；如果当 前没有事务，则以⾮事务的⽅式继续运⾏。 

TransactionDefinition.PROPAGATION_MANDATORY： 如果当前存在事务，则加⼊该事务；如果当 前没有事务，则抛出异常。（mandatory：强制性） 

不⽀持当前事务的情况：

 TransactionDefinition.PROPAGATION_REQUIRES_NEW： 创建⼀个新的事务，如果当前存在事 务，则把当前事务挂起。 

TransactionDefinition.PROPAGATION_NOT_SUPPORTED： 以⾮事务⽅式运⾏，如果当前存在事 务，则把当前事务挂起。

 TransactionDefinition.PROPAGATION_NEVER： 以⾮事务⽅式运⾏，如果当前存在事务，则抛出 异常。 

其他情况： 

TransactionDefinition.PROPAGATION_NESTED： 如果当前存在事务，则创建⼀个事务作为当前 事务的嵌套事务来运⾏；如果当前没有事务，则该取值等价于 TransactionDefinition.PROPAGATION_REQUIRED。 