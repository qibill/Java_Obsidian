### 1. Bean 的作用域

| 类别        | 说明                                        |
| --------- | ----------------------------------------- |
| singleton | 默认在 Spring 容器中仅存在一个实例                     |
| prototype | 每次调用 getBean() 都重新生成一个实例                  |
| request   | 为每个 HTTP 请求生成一个实例                         |
| session   | 同一个 HTTP session 使用一个实例，不同 session 使用不同实例 |

### 2. Bean 生命周期

简单来说四步：

*   实例化 Instantiation
*   属性赋值 Populate
*   初始化 Initialization
*   销毁 Destruction

在这四步的基础上面，Spring 提供了一些拓展点：

*   **Bean 自身的方法**: 这个包括了 Bean 本身调用的方法和通过配置文件中 &lt;bean&gt; 的 init-method 和 destroy-method 指定的方法
*   **Bean 级生命周期接口方法**: 这个包括了 BeanNameAware、BeanFactoryAware、InitializingBean 和 DiposableBean 这些接口的方法
*   **容器级生命周期接口方法**：这个包括了 InstantiationAwareBeanPostProcessor 和 BeanPostProcessor 这两个接口实现，一般称它们的实现类为“后处理器”。
*   **工厂后处理器接口方法**: 这个包括了 AspectJWeavingEnabler, ConfigurationClassPostProcessor, CustomAutowireConfigurer 等等非常有用的工厂后处理器接口的方法。工厂后处理器也是容器级的。在应用上下文装配配置文件之后立即调用。

### 3. Spring AOP

实现方式两种：

*   JDK 动态代理：带有接口的对象，在运行期实现
*   CGlib 静态代理：在编译期实现。

### 4. Spring 事务传播行为

默认 **PROPAGATION\_REQUIRED** ，如果存在一个事务，则支持当前事务。如果没有事务则开启一个新的事务。

![image](https://img-blog.csdnimg.cn/20200904160557690.png)

### 5. Spring IoC

![image](https://img-blog.csdnimg.cn/20200904160557786.png)

### 6. Spring MVC 工作流程

![image](https://img-blog.csdnimg.cn/20200904160557996.png)