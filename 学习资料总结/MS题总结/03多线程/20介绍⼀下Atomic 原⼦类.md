1. Atomic 翻译成中⽂是原⼦的意思。在我们这⾥ Atomic 是指⼀个操作是不可中断的。即使是在多个线程⼀起执⾏的时 候，⼀个操作⼀旦开始，就不会被其他线程⼲扰。

2. 所以，所谓原⼦类说简单点就是具有原⼦/原⼦操作特征的类。 

3. 并发包 java.util.concurrent 的原⼦类都存放在 java.util.concurrent.atomic 下 

   原理:

   AtomicInteger 类主要利⽤ CAS (compare and swap) + volatile 和 native ⽅法来保证原⼦操作， 从⽽避免 synchronized 的⾼开销，执⾏效率⼤为提升。

    CAS的原理是拿期望的值和原本的⼀个值作⽐较，如果相同则更新成新的值。UnSafe 类的 objectFieldOffset() ⽅法是⼀个本地⽅法，这个⽅法是⽤来拿到“原来的值”的内存地址，返回值是 valueOffset。另外 value 是⼀个volatile变量，在内存中可⻅，因此 JVM 可以保证任何时刻任何线 程总能拿到该变量的最新值。 