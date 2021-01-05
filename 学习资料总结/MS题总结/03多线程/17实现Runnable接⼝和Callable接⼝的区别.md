Runnable ⾃Java 1.0以来⼀直存在，但 Callable 仅在Java 1.5中引⼊,⽬的就是为了来处 理 Runnable 不⽀持的⽤例。 Runnable 接⼝不会返回结果或抛出检查异常，但是 Callable 接⼝可 以。所以，如果任务不需要返回结果或抛出异常推荐使⽤ Runnable 接⼝，这样代码看起来会更加简 洁。 

⼯具类 Executors 可以实现 Runnable 对象和 Callable 对象之间的相互转换。 （ Executors.callable（Runnable task ）或 Executors.callable（Runnable task， Object resule） ）。 