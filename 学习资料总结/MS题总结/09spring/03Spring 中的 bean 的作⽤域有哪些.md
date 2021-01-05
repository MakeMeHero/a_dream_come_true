singleton : 唯⼀ bean 实例，Spring 中的 bean 默认都是单例的。 

prototype : 每次请求都会创建⼀个新的 bean 实例。

 request : 每⼀次HTTP请求都会产⽣⼀个新的bean，该bean仅在当前HTTP request内有效。 

session : 每⼀次HTTP请求都会产⽣⼀个新的 bean，该bean仅在当前 HTTP session 内有效。

 global-session： 全局session作⽤域，仅仅在基于portlet的web应⽤中才有意义，Spring5已 经没有了。Portlet是能够⽣成语义代码(例如：HTML)⽚段的⼩型Java Web插件。它们基于 portlet容器，可以像servlet⼀样处理HTTP请求。但是，与 servlet 不同，每个 portlet 都有 不同的会话 