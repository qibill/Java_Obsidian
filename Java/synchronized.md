#### 四种状态
- 无锁
- 偏向锁
- 轻量级锁
- 重量级锁


#### synchronized和ReentrantLock的区别
- synchronized是关键字，ReentrantLock是类
- ReentrantLock可以对获取锁的等待时间进行设置，避免死锁
- ReentrantLock可以获取各种锁的信息
- ReentrantLock可以灵活地实现多路通知
- 
![image](https://img-blog.csdnimg.cn/20210407110721756.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dxYWR4bW0=,size_16,color_FFFFFF,t_70)
- **机制：sync操作Mark Word，lock调用Unsafe类的park()方法**

---
### JMM

- 主内存
- 工作内存

#### 主内存
- 存储Java实例对象
- 包括成员变量、类信息、常量、静态变量等
- 属于数据共享的区域，多线程并发操作是=时会引发线程安全问题

#### 工作内存
- 存储当前方法的所有本地变量信息，本地变量对其他线程不可见
- 字节码行号指示器、Native方法信息
- 属于线程私有数据区域，不存在线程安全问题
