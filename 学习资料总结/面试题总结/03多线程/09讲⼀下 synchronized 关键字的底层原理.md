##synchronized 同步语句块的情况 

synchronized 同步语句块的实现使⽤的是 monitorenter 和 monitorexit 指令，其中 monitorenter 指令指向同步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置。 当执⾏ monitorenter 指令时，线程试图获取锁也就是获取 monitor(monitor对象存在于每个Java对象的对象 头中，synchronized 锁便是通过这种⽅式获取锁的，也是为什么Java中任意对象可以作为锁的原因) 的持有权。当计数器为0则可以成功获取，获取后将锁计数器设为1也就是加1。相应的在执⾏ monitorexit 指令后，将锁计数器设为0，表明锁被释放。如果获取对象锁失败，那当前线程就要阻塞 等待，直到锁被另外⼀个线程释放为⽌。 

## synchronized 修饰⽅法的的情况 

synchronized 修饰的⽅法并没有 monitorenter 指令和 monitorexit 指令，取得代之的确实是 ACC_SYNCHRONIZED 标识，该标识指明了该⽅法是⼀个同步⽅法，JVM 通过该 ACC_SYNCHRONIZED 访问 标志来辨别⼀个⽅法是否声明为同步⽅法，从⽽执⾏相应的同步调⽤。 