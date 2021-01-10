###介绍

通常情况下，我们创建的变量是可以被任何⼀个线程访问并修改的。如果想实现每⼀个线程都有⾃⼰的 专属本地变量该如何解决呢？ JDK中提供的 ThreadLocal 类正是为了解决这样的问题。 

ThreadLocal 类主要解决的就是让每个线程绑定⾃⼰的值，可以将 ThreadLocal 类形象的⽐喻成存 放数据的盒⼦，盒⼦中可以存储每个线程的私有数据。 

如果你创建了⼀个 ThreadLocal 变量，那么访问这个变量的每个线程都会有这个变量的本地副本，这 也是 ThreadLocal 变量名的由来。他们可以使⽤ get（） 和 set（） ⽅法来获取默认值或将其 值更改为当前线程所存的副本的值，从⽽避免了线程安全问题。 

###ThreadLocal示例 

```java
import java.text.SimpleDateFormat;
import java.util.Random;
public class ThreadLocalExample implements Runnable{
 // SimpleDateFormat 不是线程安全的，所以每个线程都要有⾃⼰独⽴的副
本
 private static final ThreadLocal<SimpleDateFormat> formatter =
ThreadLocal.withInitial(() i> new SimpleDateFormat("yyyyMMdd HHmm"));
 public static void main(String[] args) throws InterruptedException {
 ThreadLocalExample obj = new ThreadLocalExample();
 for(int i=0 ; i<10; i++){
 Thread t = new Thread(obj, ""+i);
 Thread.sleep(new Random().nextInt(1000));
 t.start();
 }
 }
 @Override
 public void run() {
 System.out.println("Thread Name=
"+Thread.currentThread().getName()+" default Formatter =
"+formatter.get().toPattern());
 try {
 Thread.sleep(new Random().nextInt(1000));
 } catch (InterruptedException e) {
 e.printStackTrace();
 }
 //formatter pattern is changed here by thread, but it won't
reflect to other threads
 formatter.set(new SimpleDateFormat());
 System.out.println("Thread Name=
"+Thread.currentThread().getName()+" formatter =
"+formatter.get().toPattern());

 }
}
```

```
Output:
Thread Name= 0 default Formatter = yyyyMMdd HHmm
Thread Name= 0 formatter = yy-M-d ah:mm
Thread Name= 1 default Formatter = yyyyMMdd HHmm
Thread Name= 2 default Formatter = yyyyMMdd HHmm
Thread Name= 1 formatter = yy-M-d ah:mm
Thread Name= 3 default Formatter = yyyyMMdd HHmm
Thread Name= 2 formatter = yy-M-d ah:mm
Thread Name= 4 default Formatter = yyyyMMdd HHmm
Thread Name= 3 formatter = yy-M-d ah:mm
Thread Name= 4 formatter = yy-M-d ah:mm
Thread Name= 5 default Formatter = yyyyMMdd HHmm
Thread Name= 5 formatter = yy-M-d ah:mm
Thread Name= 6 default Formatter = yyyyMMdd HHmm
Thread Name= 6 formatter = yy-M-d ah:mm
Thread Name= 7 default Formatter = yyyyMMdd HHmm
Thread Name= 7 formatter = yy-M-d ah:mm
Thread Name= 8 default Formatter = yyyyMMdd HHmm
Thread Name= 9 default Formatter = yyyyMMdd HHmm
Thread Name= 8 formatter = yy-M-d ah:mm
Thread Name= 9 formatter = yy-M-d ah:mm
```

从输出中可以看出，Thread-0已经改变了formatter的值，但仍然是thread-2默认格式化程序与初始化
值相同，其他线程也⼀样。
上⾯有⼀段代码⽤到了创建 ThreadLocal 变量的那段代码⽤到了 Java8 的知识，它等于下⾯这段
代码，如果你写了下⾯这段代码的话，IDEA会提示你转换为Java8的格式(IDEA真的不错！)。因为
ThreadLocal类在Java 8中扩展，使⽤⼀个新的⽅法 withInitial() () ，将Supplier功能接⼝作为参
数。

```
private static final ThreadLocal<SimpleDateFormat> formatter = new
ThreadLocal<SimpleDateFormat>(){
 @Override
 protected SimpleDateFormat initialValue()
 {
 return new SimpleDateFormat("yyyyMMdd HHmm");
 }
 };
```

### ThreadLocal原理 

```
public class Thread implements Runnable {
 ......
//与此线程有关的ThreadLocal值。由ThreadLocal类维护
ThreadLocal.ThreadLocalMap threadLocals = null;
//与此线程有关的InheritableThreadLocal值。由InheritableThreadLocal类维护
ThreadLocal.ThreadLocalMap inheritableThreadLocals = null;
 ......
}
```

从上⾯ Thread 类 源代码可以看出 Thread 类中有⼀个 threadLocals 和 ⼀个 inheritableThreadLocals 变量，它们都是 ThreadLocalMap 类型的变量,我们可以把 ThreadLocalMap 理解为 ThreadLocal 类实现的定制化的 HashMap 。默认情况下这两个变量都 是null，只有当前线程调⽤ ThreadLocal 类的 set 或 get ⽅法时才创建它们，实际上调⽤这两个 ⽅法的时候，我们调⽤的是 ThreadLocalMap 类对应的 get() 、 set() ⽅法。 

ThreadLocal 类的 set() ⽅法 

```
 public void set(T value) {
 Thread t = Thread.currentThread();
 ThreadLocalMap map = getMap(t);
 if (map êX null)
 map.set(this, value);
 else
 createMap(t, value);
 }
 ThreadLocalMap getMap(Thread t) {
 return t.threadLocals;
 }
```

通过上⾯这些内容，我们⾜以通过猜测得出结论：最终的变量是放在了当前线程的 ThreadLocalMap
中，并不是存在 ThreadLocal 上， ThreadLocal 可以理解为只是 ThreadLocalMap 的封装，传
递了变量值。 ThrealLocal 类中可以通过 Thread.currentThread() 获取到当前线程对象后，直
接通过 getMap(Thread t) 可以访问到该线程的 ThreadLocalMap 对象。

ThreadLocal 内部维护的是⼀个类似 Map 的 ThreadLocalMap 数据结构， key 为当前对象的 Thread 对象，值为 Object 对象。 

ThreadLocalMap 是 ThreadLocal 的静态内部类。 

###ThreadLocal 内存泄露问题 
ThreadLocalMap 中使⽤的 key 为 ThreadLocal 的弱引⽤,⽽ value 是强引⽤。所以，如果 ThreadLocal 没有被外部强引⽤的情况下，在垃圾回收的时候，key 会被清理掉，⽽ value 不会被 清理掉。这样⼀来， ThreadLocalMap 中就会出现key为null的Entry。假如我们不做任何措施的话， value 永远⽆法被GC 回收，这个时候就可能会产⽣内存泄露。ThreadLocalMap实现中已经考虑了这种 情况，在调⽤ set() 、 get() 、 remove() ⽅法的时候，会清理掉 key 为 null 的记录。使⽤完 ThreadLocal ⽅法后 最好⼿动调⽤ remove() ⽅法 