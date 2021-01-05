```
Mybatis 动态 sql 可以让我们在 Xml 映射⽂件内，以标签的形式编写动态 sql，完成逻辑判断和
动态拼接 sql 的功能，Mybatis 提供了 9 种动态 sql 标签
trim|where|set|foreach|if|choose|when|otherwise|bind 。
其执⾏原理为，使⽤ OGNL 从 sql 参数对象中计算表达式的值，根据表达式的值动态拼接 sql，以此
来完成动态 sql 的功能。
```

