==实例化 ==
1.实例化一个 Bean，也就是我们常说的 new。
==IOC 依赖注入==
2. 按照 Spring 上下文对实例化的 Bean 进行配置，也就是 IOC 注入。
setBeanName 实现 

3. 如果这个 Bean 已经实现了 BeanNameAware 接口，会调用它实现的 setBeanName(String) 方法，此处传递的就是 Spring 配置文件中 Bean 的 id 值
BeanFactoryAware 实现 

4. 如果这个 Bean 已经实现了 BeanFactoryAware 接口，会调用它实现的 setBeanFactory， setBeanFactory(BeanFactory)传递的是 Spring 工厂自身（可以用这个方式来获取其它 Bean， 只需在 Spring 配置文件中配置一个普通的 Bean 就可以）。 
ApplicationContextAware 实现

5. 如果这个 Bean 已经实现了 ApplicationContextAware 接口，会调用
  setApplicationContext(ApplicationContext)方法，传入 Spring 上下文（同样这个方式也
  可以实现步骤 4 的内容，但比 4 更好，因为 ApplicationContext 是 BeanFactory 的子接
  口，有更多的实现方法）
postProcessBeforeInitialization 接口实现-初始化预处理

6. 如果这个 Bean 关联了 BeanPostProcessor 接口，将会调用
  postProcessBeforeInitialization(Object obj, String s)方法，BeanPostProcessor 经常被用
  作是 Bean 内容的更改，并且由于这个是在 Bean 初始化结束时调用那个的方法，也可以被应
  用于内存或缓存技术。
init-method

7. 如果 Bean 在 Spring 配置文件中配置了 init-method 属性会自动调用其配置的初始化方法。
postProcessAfterInitialization

8. 如果这个 Bean 关联了 BeanPostProcessor 接口，将会调用
  postProcessAfterInitialization(Object obj, String s)方法。
  ==注：以上工作完成以后就可以应用这个 Bean ==了，那这个 Bean 是一个 Singleton 的，所以一
  般情况下我们调用同一个 id 的 Bean 会是在内容地址相同的实例，当然在 Spring 配置文件中
  也可以配置非 Singleton。
Destroy 过期自动清理阶段

9. ==当 Bean 不再需要时，会经过清理阶段，如果 Bean 实现了 DisposableBean 这个接口，会调
  用那个其实现的 destroy()方法==；
destroy-method 自配置清理

10. ==最后，如果这个 Bean 的 Spring 配置中配置了 destroy-method 属性，会自动调用其配置的
  销毁方法。==

  ![](E:\学习资料总结\MS题总结\09spring\assets/QQ截图20210108204354.png)
