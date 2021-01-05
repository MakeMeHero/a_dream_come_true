comparable接⼝实际上是出⾃java.lang包 它有⼀个 compareTo(Object obj) ⽅法⽤来排序 

comparator接⼝实际上是出⾃ java.util 包它有⼀个 compare(Object obj1, Object obj2) ⽅法⽤来排序 

⼀般我们需要对⼀个集合使⽤⾃定义排序时，我们就要重写 compareTo() ⽅法或 compare() ⽅法， 当我们需要对某⼀个集合实现两种排序⽅式，⽐如⼀个song对象中的歌名和歌⼿名分别采⽤⼀种排序⽅ 法的话，我们可以重写 compareTo() ⽅法和使⽤⾃制的Comparator⽅法或者以两个Comparator来实现 歌名排序和歌星名排序，第⼆种代表我们只能使⽤两个参数版的 Collections.sort() . 

Comparator定制排序 

```java
ArrayList<Integer> arrayList = new ArrayList<Integer>();
 arrayList.add(-1);
 arrayList.add(3);
 arrayList.add(3);
 arrayList.add(-5);
 arrayList.add(7);
 arrayList.add(4);
 arrayList.add(-9);
 arrayList.add(-7);
 System.out.println("原始数组:");
 System.out.println(arrayList);
 // void reverse(List list)：反转
 Collections.reverse(arrayList);
 System.out.println("Collections.reverse(arrayList):");
 System.out.println(arrayList);
 // void sort(List list),按⾃然排序的升序排序
 Collections.sort(arrayList);
 System.out.println("Collections.sort(arrayList):");
 System.out.println(arrayList);
 // 定制排序的⽤法
 Collections.sort(arrayList, new Comparator<Integer>() {
 @Override
 public int compare(Integer o1, Integer o2) {
 return o2.compareTo(o1);
 }
 });
 System.out.println("定制排序后：");
 System.out.println(arrayList);
```

Output: 

```java
原始数组:
[-1, 3, 3, -5, 7, 4, -9, -7]
Collections.reverse(arrayList):
[-7, -9, 4, 7, -5, 3, 3, -1]
Collections.sort(arrayList):
[-9, -7, -5, -1, 3, 3, 4, 7]
定制排序后：
[7, 4, 3, 3, -1, -5, -7, -9]
```

重写compareTo⽅法实现按年龄来排序 

```java
// person对象没有实现Comparable接⼝，所以必须实现，这样才不会出错，才可
以使treemap中的数据按顺序排列
// 前⾯⼀个例⼦的String类已经默认实现了Comparable接⼝，详细可以查看
String类的API⽂档，另外其他
// 像Integer类等都已经实现了Comparable接⼝，所以不需要另外实现了
public class Person implements Comparable<Person> {
 private String name;
 private int age;
 public Person(String name, int age) {
 super();
 this.name = name;
 this.age = age;
 }
 public String getName() {
 return name;
 }
 public void setName(String name) {
 this.name = name;
 }
 public int getAge() {
 return age;
 }
 public void setAge(int age) {
 this.age = age;
 }
 /**
Output：
2.2.14 集合框架底层数据结构总结
Collection
1. List
Arraylist： Object数组
Vector： Object数组
LinkedList： 双向链表(JDK1.6之前为循环链表，JDK1.7取消了循环)
 * TODO重写compareTo⽅法实现按年龄来排序
 */
 @Override
 public int compareTo(Person o) {
 // TODO Auto-generated method stub
 if (this.age > o.getAge()) {
 return 1;
 } else if (this.age < o.getAge()) {
 return -1;
 }
 return age;
 }
}

```

```java
public static void main(String[] args) {
 TreeMap<Person, String> pdata = new TreeMap<Person, String>();
 pdata.put(new Person("张三", 30), "zhangsan");
 pdata.put(new Person("李四", 20), "lisi");
 pdata.put(new Person("王五", 10), "wangwu");
 pdata.put(new Person("⼩红", 5), "xiaohong");
 // 得到key的值的同时得到key所对应的值
 Set<Person> keys = pdata.keySet();
 for (Person key : keys) {
 System.out.println(key.getAge() + "-" + key.getName());
 }
 }
```

Output： 

```
5-⼩红
10-王五
20-李四
30-张三
```

