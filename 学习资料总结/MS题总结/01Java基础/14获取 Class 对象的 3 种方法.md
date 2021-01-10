##调用某个对象的 getClass()方法 

Person p=new Person();

 Class clazz=p.getClass();

##调用某个类的 class 属性来获取该类对应的 Class 对象

Class clazz=Person.class; 

##使用 Class 类中的 forName()静态方法(最安全/性能最好) 

Class clazz=Class.forName("类的全路径"); (最常用) 