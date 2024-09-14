*   修饰代码块 底层实现，通过 monitorenter & monitorexit 标志代码块为同步代码块。
*   修饰方法 底层实现，通过 ACC\_SYNCHRONIZED 标志方法是同步方法。
*   修饰类 class 对象时，实际锁在类的实例上面。
*   单例模式
```java
public class Singleton {

    private static volatile Singleton instance = null;

    private Singleton(){}

    public static Singleton getInstance(){
    if (null == instance) {
        synchronized (Singleton.class) {
            if (null == instance) {
            instance = new Singleton();
            }
        }
      }
        return instance;
        }
}
```
#### 四种状态
- 无锁
- 偏向锁
- 轻量级锁
- 重量级锁

通过 synchronized 加锁，第一个线程获取的锁为偏向锁，这时有其他线程参与锁竞争，升级为轻量级锁，其他线程通过循环的方式尝试获得锁，称自旋锁。若果自旋的次数达到一定的阈值，则升级为重量级锁。
需要注意的是，在第二个线程获取锁时，会先判断第一个线程是否仍然存活，如果不存活，不会升级为轻量级锁。

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
