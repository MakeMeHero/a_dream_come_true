ThreadPoolExecutor 3 个最重要的参数： 

corePoolSize : 核⼼线程数线程数定义了最⼩可以同时运⾏的线程数量。 

maximumPoolSize : 当队列中存放的任务达到队列容量的时候，当前可以同时运⾏的线程数 量变为最⼤线程数。 workQueue : 当新任务来的时候会先判断当前运⾏的线程数量是否达到核⼼线程数，如果达到 的话，新任务就会被存放在队列中。 

ThreadPoolExecutor 其他常⻅参数: 

1. keepAliveTime :当线程池中的线程数量⼤于 corePoolSize 的时候，如果这时没有新的任 务提交，核⼼线程外的线程不会⽴即销毁，⽽是会等待，直到等待的时间超过了 keepAliveTime 才会被回收销毁；

2. unit : keepAliveTime 参数的时间单位。

3. threadFactory :executor 创建新线程的时候会⽤到。 

4. handler :饱和策略。关于饱和策略下⾯单独介绍⼀下。 

  ThreadPoolExecutor 饱和策略 ThreadPoolExecutor 饱和策略定义: 如果当前同时运⾏的线程数量达到最⼤线程数量并且队列也已经被放满了任 时， ThreadPoolTaskExecutor 定义⼀些策略: 

  ThreadPoolExecutor.AbortPolicy ：抛出 RejectedExecutionException 来拒绝新任 务的处理。 ThreadPoolExecutor.CallerRunsPolicy ：调⽤执⾏⾃⼰的线程运⾏任务。您不会任务请 求。但是这种策略会降低对于新任务提交速度，影响程序的整体性能。另外，这个策略喜欢增加 队列容量。如果您的应⽤程序可以承受此延迟并且你不能任务丢弃任何⼀个任务请求的话，你可 以选择这个策略。 

  ThreadPoolExecutor.DiscardPolicy ： 不处理新任务，直接丢弃掉。 ThreadPoolExecutor.DiscardOldestPolicy ： 此策略将丢弃最早的未处理的任务请求。 