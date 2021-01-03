① 两者都是可重⼊锁 两者都是可重⼊锁。“可重⼊锁”概念是：⾃⼰可以再次获取⾃⼰的内部锁。⽐如⼀个线程获得了某个对 象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果不 可锁重⼊的话，就会造成死锁。同⼀个线程每次获取锁，锁的计数器都⾃增1，所以要等到锁的计数器 下降为0时才能释放锁。 

② synchronized 依赖于 JVM ⽽ ReentrantLock 依赖于 API synchronized 是依赖于 JVM 实现的，前⾯我们也讲到了 虚拟机团队在 JDK1.6 为 synchronized 关 键字进⾏了很多优化，但是这些优化都是在虚拟机层⾯实现的，并没有直接暴露给我们。 ReentrantLock 是 JDK 层⾯实现的（也就是 API 层⾯，需要 lock() 和 unlock() ⽅法配合 try/finally 语句块来完成），所以我们可以通过查看它的源代码，来看它是如何实现的。 

③ ReentrantLock ⽐ synchronized 增加了⼀些⾼级功能 相⽐synchronized，ReentrantLock增加了⼀些⾼级功能。主要来说主要有三点：①等待可中断；②可 实现公平锁；③可实现选择性通知（锁可以绑定多个条件） ReentrantLock提供了⼀种能够中断等待锁的线程的机制，通过lock.lockInterruptibly()来实 现这个机制。也就是说正在等待的线程可以选择放弃等待，改为处理其他事情。 

ReentrantLock可以指定是公平锁还是⾮公平锁。⽽synchronized只能是⾮公平锁。所谓的公平 锁就是先等待的线程先获得锁。 ReentrantLock默认情况是⾮公平的，可以通过 ReentrantLock 类的 ReentrantLock(boolean fair) 构造⽅法来制定是否是公平的。

 synchronized关键字与wait()和notify()/notifyAll()⽅法相结合可以实现等待/通知机制， ReentrantLock类当然也可以实现，但是需要借助于Condition接⼝与newCondition() ⽅法。 Condition是JDK1.5之后才有的，它具有很好的灵活性，⽐如可以实现多路通知功能也就是在⼀ 个Lock对象中可以创建多个Condition实例（即对象监视器），线程对象可以注册在指定的 Condition中，从⽽可以有选择性的进⾏线程通知，在调度线程上更加灵活。 在使⽤ notify()/notifyAll()⽅法进⾏通知时，被通知的线程是由 JVM 选择的，⽤ReentrantLock类结 合Condition实例可以实现“选择性通知” ，这个功能⾮常重要，⽽且是Condition接⼝默认提供 的。⽽synchronized关键字就相当于整个Lock对象中只有⼀个Condition实例，所有的线程都注 册在它⼀个身上。如果执⾏notifyAll()⽅法的话就会通知所有处于等待状态的线程这样会造成 很⼤的效率问题，⽽Condition实例的signalAll()⽅法 只会唤醒注册在该Condition实例中的所 有等待线程。 如果你想使⽤上述功能，那么选择ReentrantLock是⼀个不错的选择。 