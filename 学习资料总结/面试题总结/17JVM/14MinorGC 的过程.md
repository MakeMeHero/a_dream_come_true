# 骨干

## MinorGC 采用复制算法。 

### 1：eden、servicorFrom 复制到 ServicorTo，年龄+1  

首先，把 Eden 和 ServivorFrom 区域中存活的对象复制到 ServicorTo 区域（如果有对象的年 龄以及达到了老年的标准，则赋值到老年代区），同时把这些对象的年龄+1（如果 ServicorTo 不 够位置了就放到老年区）； 

### 2：清空 eden、servicorFrom 

然后，清空 Eden 和 ServicorFrom 中的对象； 

###3：ServicorTo 和 ServicorFrom 互换 

最后，ServicorTo 和 ServicorFrom 互换，原 ServicorTo 成为下一次 GC 时的 ServicorFrom 区。 