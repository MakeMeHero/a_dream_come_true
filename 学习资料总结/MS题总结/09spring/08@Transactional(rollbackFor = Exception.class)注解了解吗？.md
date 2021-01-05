我们知道：Exception分为运⾏时异常RuntimeException和⾮运⾏时异常。事务管理对于企业应⽤来说 是⾄关重要的，即使出现异常情况，它也可以保证数据的⼀致性。 

当 @Transactional 注解作⽤于类上时，该类的所有 public ⽅法将都具有该类型的事务属性，同 时，我们也可以在⽅法级别使⽤该标注来覆盖类级别的定义。如果类或者⽅法加了这个注解，那么这个 类⾥⾯的⽅法抛出异常，就会回滚，数据库⾥⾯的数据也会回滚。

 在 @Transactional 注解中如果不配置 rollbackFor 属性,那么事物只会在遇 到 RuntimeException 的时候才会回滚,加上 rollbackFor=Exception.class ,可以让事物在遇到 ⾮运⾏时异常时也回滚。 