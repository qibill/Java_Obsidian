#### Object的finalize()方法的作用是否与C++的析构函数作用相同
不同
- 析构函数调用确定，finalize不确定
- finalize将未被引用的对象放置与F-Queue队列
- 方法执行随时可能会被终止
- 给予对象最后一次重生的机会

#### 强引用、软引用，弱引用，虚引用

引用类型 | 被垃圾回收时间 | 用途 | 生存时间
---|---|---|---
强引用 | 从来不会 | 对象的一般状态 | JVM停止运行时终止
软引用 | 在内存不足时 | 对象缓存 | 内存不足时终止
弱引用 | 在垃圾回收时 | 对象缓存 | gc运行后终止
虚引用 | Unknown | 标记、哨兵 | UnKnown

