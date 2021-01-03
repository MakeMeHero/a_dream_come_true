## 可变性

简单的来说：String 类中使⽤ final 关键字修饰字符数组来保存字符串， private final char value[] ，所以 String 对象是不可变的 

⽽ StringBuilder 与 StringBuffer 都继承⾃ AbstractStringBuilder 类，在 AbstractStringBuilder 中也是使⽤字符数组保存字符串 char[]value 但是没有⽤ final 关键字修 饰，所以这两种对象都是可变的。 

## 使用场景

1. 操作少量的数据: 适⽤ String
2. 单线程操作字符串缓冲区下操作⼤量数据: 适⽤ StringBuilder 
3. 多线程操作字符串缓冲区下操作⼤量数据: 适⽤ StringBuffer 