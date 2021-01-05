synchronized关键字和volatile关键字⽐

 volatile关键字是线程同步的轻量级实现，所以volatile性能肯定⽐synchronized关键字要好。 

但是volatile关键字只能⽤于变量⽽synchronized关键字可以修饰⽅法以及代码块。 synchronized关键字在JavaSE1.6之后进⾏了主要包括为了减少获得锁和释放锁带来的性能消耗 ⽽引⼊的偏向锁和轻量级锁以及其它各种优化之后执⾏效率有了显著提升，实际开发中使⽤ synchronized 关键字的场景还是更多⼀些。

 多线程访问volatile关键字不会发⽣阻塞，⽽synchronized关键字可能会发⽣阻塞

 volatile关键字能保证数据的可⻅性，但不能保证数据的原⼦性。synchronized关键字两者都能 保证。

 volatile关键字主要⽤于解决变量在多个线程之间的可⻅性，⽽ synchronized关键字解决的是 多个线程之间访问资源的同步性。 