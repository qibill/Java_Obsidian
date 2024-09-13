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

![image](https://pic4.zhimg.com/80/754a34e03cfaa40008de8e2b9c1b815c_720w.jpg?source=1940ef5c)

![image](https://pic4.zhimg.com/80/v2-baaf7d50702f6d0935820b9415ff364c_720w.jpg?source=1940ef5c)


![image](https://upload-images.jianshu.io/upload_images/4558491-dc3eebbd1d6c65f4.png?imageMogr2/auto-orient/strip|imageView2/2/w/823)