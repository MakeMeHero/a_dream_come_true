在 Java 中，所有的异常都有⼀个共同的祖先 java.lang 包中的 Throwable 类。Throwable： 有两个 重要的⼦类：Exception（异常） 和 Error（错误） ，⼆者都是 Java 异常处理的重要⼦类，各⾃都 包含⼤量⼦类。 

Error（错误）:是程序⽆法处理的错误，表示运⾏应⽤程序中严重问题。⼤多数错误与代码编写者执 ⾏的操作⽆关，⽽表示代码运⾏时 JVM（Java 虚拟机）出现的问题。例如，Java 虚拟机运⾏错误 （Virtual MachineError），当 JVM 不再有继续执⾏操作所需的内存资源时，将出现 OutOfMemoryError。

这些异常发⽣时，Java 虚拟机（JVM）⼀般会选择线程终⽌。 这些错误表示故障发⽣于虚拟机⾃身、或者发⽣在虚拟机试图执⾏应⽤时，如 Java 虚拟机运⾏错误 （Virtual MachineError）、类定义错误（NoClassDefFoundError）等。这些错误是不可查的，因为它 们在应⽤程序的控制和处理能⼒之 外，⽽且绝⼤多数是程序运⾏时不允许出现的状况。对于设计合理 的应⽤程序来说，即使确实发⽣了错误，本质上也不应该试图去处理它所引起的异常状况。在 Java 中，错误通过 Error 的⼦类描述。

 Exception（异常）:是程序本身可以处理的异常。Exception 类有⼀个重要的⼦类 RuntimeException。RuntimeException 异常由 Java 虚拟机抛出。NullPointerException（要访问的 变量没有引⽤任何对象时，抛出该异常）、ArithmeticException（算术运算异常，⼀个整数除以 0 时，抛出该异常）和 ArrayIndexOutOfBoundsException （下标越界异常）。 注意：异常和错误的区别：异常能被程序本身处理，错误是⽆法处理。 

Throwable 类常⽤⽅法 

public string getMessage():返回异常发⽣时的简要描述

 public string toString():返回异常发⽣时的详细信息 

public string getLocalizedMessage():返回异常对象的本地化信息。使⽤ Throwable 的⼦类 覆盖这个⽅法，可以⽣成本地化信息。如果⼦类没有覆盖该⽅法，则该⽅法返回的信息与 getMessage（）返回的结果相同

 public void printStackTrace():在控制台上打印 Throwable 对象封装的异常信息 

异常处理总结

 try 块： ⽤于捕获异常。其后可接零个或多个 catch 块，如果没有 catch 块，则必须跟⼀个 finally 块。

 catch 块： ⽤于处理 try 捕获到的异常。 

finally 块： ⽆论是否捕获或处理异常，finally 块⾥的语句都会被执⾏。当在 try 块或 catch 块中遇到 return 语句时，finally 语句块将在⽅法返回之前被执⾏。 

在以下 4 种特殊情况下，finally 块不会被执⾏：

  1. 在 finally 语句块第⼀⾏发⽣了异常。 因为在其他⾏，finally 块还是会得到执⾏ 
  2. 在前⾯的代码中⽤了 System.exit(int)已退出程序。 exit 是带参函数 ；若该语句在异常语句 之后，finally 会执⾏ 
  3. 程序所在的线程死亡。 
  4. 关闭 CPU。 

 注意： 当 try 语句和 finally 语句中都有 return 语句时，在⽅法返回之前，finally 语句的内容 将被执⾏，并且 finally 语句的返回值将会覆盖原始的返回值。如下： 