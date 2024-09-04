因为**Spring的Bean扫描和Spring-MVC的Bean扫描是分开的**, 两者的Bean位于两个不同的Application, 而且**Spring-MVC的Bean扫描要早于Spring的Bean扫描**, 所以当Controller Bean生成完成后, 再执行Spring的Bean扫描,Spring会发现要被AOP代理的Controller Bean已经在容器中存在, 配置AOP就无效了.

### 解决方法
在spring-mvc.xml里开启AOP自动代理

```
    <!-- 使用Annotation自动注册Bean,扫描@Controller和@ControllerAdvice-->
    <context:component-scan base-package="com.biosan.controller, com.biosan.handler, com.biosan.aop">
        <!-- base-package 如果多个，用“,”分隔 -->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!--控制器增强，使一个Contoller成为全局的异常处理类，类中用@ExceptionHandler方法注解的方法可以处理所有Controller发生的异常-->
        <context:include-filter type="annotation" expression="org.springframework.web.bind.annotation.ControllerAdvice"/>
    </context:component-scan>
    <!-- 配置注解驱动 -->
    <mvc:annotation-driven/>

    <!-- 启动@aspectj的自动代理支持-->
    <aop:aspectj-autoproxy/>
```
