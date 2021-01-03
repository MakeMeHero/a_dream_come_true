⼤多数情况下，对象在新⽣代中 eden 区分配。当 eden 区没有⾜够空间进⾏分配时，虚拟机将发起⼀ 次Minor GC。 

新⽣代GC（Minor GC）:指发⽣新⽣代的的垃圾收集动作，Minor GC⾮常频繁，回收速度⼀般也 ⽐᫾快。 

⽼年代GC（Major GC/Full GC）:指发⽣在⽼年代的GC，出现了Major GC经常会伴随⾄少⼀次的 Minor GC（并⾮绝对），Major GC的速度⼀般会⽐Minor GC的慢10倍以上。 