JDK7之前资源的关闭姿势：

```java
/**
 * jdk7以前关闭流的方式
 *
 * @author hetiantian
 * */
public class CloseResourceBefore7 {
    private static final String FileName = "file.txt";

    public static void main(String[] args) throws IOException {
        FileInputStream inputStream = null;

        try {
            inputStream = new FileInputStream(FileName);
            char c1 = (char) inputStream.read();
            System.out.println("c1=" + c1);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (inputStream != null) {
                inputStream.close();
            }
        }
    }
}
```

复制代码JDK7及以后关闭资源的正确姿势：try-with-resource
Resource的定义：所有实现了 [java.lang.AutoCloseable](https://docs.oracle.com/javase/8/docs/api/java/lang/AutoCloseable.html)接口（其中，它包括实现了[java.io.Closeable](https://docs.oracle.com/javase/8/docs/api/java/io/Closeable.html)的所有对象），可以使用作为资源。
简单Demo进行证实：
实现java.lang.AutoCloseable接口的Resource类：

```java
/**
 * 资源类
 * 
 * @author hetiantian
 * */
public class Resource implements AutoCloseable {
    public void sayHello() {
        System.out.println("hello");
    }

    @Override
    public void close() throws Exception {
        System.out.println("Resource is closed");
    }
}
```

复制代码测试类CloseResourceIn7.java

```java
/**
 * jdk7及以后关闭流的方式
 *
 * @author hetiantian
 * */
public class CloseResourceIn7 {
    public static void main(String[] args) {
        try(Resource resource = new Resource()) {
            resource.sayHello();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

复制代码打印结果：
hello
Resource is closed
复制代码当存在多个打开资源的时候：
资源二Resource2.java

```java
/**
 * 资源2
 * 
 * @author hetiantian
 * */
public class Resource2 implements AutoCloseable {
    public void sayhello() {
        System.out.println("Resource say hello");
    }

    @Override
    public void close() throws Exception {
        System.out.println("Resource2 is closed");
    }
}
```

复制代码测试类CloseResourceIn7.java

```java
/**
 * jdk7及以后关闭流的方式
 *
 * @author hetiantian
 * */
public class CloseResourceIn7 {
    public static void main(String[] args) {
        try(Resource resource = new Resource(); Resource2 resource2 = new Resource2()) {
            resource.sayHello();
            resource2.sayhello();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

复制代码打印结果：

```
hello
Resource say hello
Resource2 is closed
Resource is closed
```

复制代码即使资源很多，代码也可以写的很简洁，如果用jdk7之前的方式去关闭资源，那么资源越多，用fianl关闭资源时嵌套也就越多
那么它的底层原理又是怎样的呢，由皮皮甜独家揭秘优雅关闭资源背后的密码
查看编译的class文件CloseResourceIn7.class：

```java
public class CloseResourceIn7 {
    public CloseResourceIn7() {
    }

    public static void main(String[] args) {
        try {
            Resource resource = new Resource();
            Throwable var2 = null;

            try {
                resource.sayHello();
            } catch (Throwable var12) {
                var2 = var12;
                throw var12;
            } finally {
                if (resource != null) {
                    if (var2 != null) {
                        try {
                            resource.close();
                        } catch (Throwable var11) {
                            var2.addSuppressed(var11);
                        }
                    } else {
                        resource.close();
                    }
                }

            }
        } catch (Exception var14) {
            var14.printStackTrace();
        }

    }
}
```

复制代码可以发现编译以后生成了try-catch-finally语句块
finally中的var2.addSuppressed(var11);是不是有疑问🤔️
其实这么做是为了处理异常屏蔽的
我们将代码修改一下
资源Resource.java

```java
/**
 * 资源类
 *
 * @author hetiantian
 * */
public class Resource implements AutoCloseable {
    public void sayHello() throws Exception {
        throw new Exception("Resource throw Exception");
    }

    @Override
    public void close() throws Exception {
        throw new Exception("Close method throw Exception");
    }
}
```

复制代码两个方法里面都抛出异常
测试类CloseResourceIn7.java

```java
/**
 * jdk7及以后关闭流的方式
 *
 * @author hetiantian
 * */
public class CloseResourceIn7 {

    public static void main(String[] args) {
        try {
            errorTest();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static void errorTest() throws Exception {
        Resource resource = null;
        try {
            resource = new Resource();
            resource.sayHello();
        }

        finally {
            if (resource != null) {
                resource.close();
            }
        }
    }
}
```

复制代码打印结果：

```java
java.lang.Exception: Close method throw Exception
	at com.shuwen.Resource.close(Resource.java:15)
	at com.shuwen.CloseResourceIn7.errorTest(CloseResourceIn7.java:27)
	at com.shuwen.CloseResourceIn7.main(CloseResourceIn7.java:12)
```

复制代码只打印了最后出现的异常【异常屏蔽】这样会给开发人员排查错误带来一定的困难
我们换成try-with-resource方法实现CloseResourceIn7.java

```java
/**
 * jdk7及以后关闭流的方式
 *
 * @author hetiantian
 * */
public class CloseResourceIn7 {

    public static void main(String[] args) {
        try {
            errorTest();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static void errorTest() throws Exception {
        try(Resource resource = new Resource()) {
            resource.sayHello();
        }

    }
}
```

复制代码打印信息：

```java
java.lang.Exception: Resource throw Exception
	at com.shuwen.Resource.sayHello(Resource.java:10)
	at com.shuwen.CloseResourceIn7.errorTest(CloseResourceIn7.java:20)
	at com.shuwen.CloseResourceIn7.main(CloseResourceIn7.java:12)
	Suppressed: java.lang.Exception: Close method throw Exception
		at com.shuwen.Resource.close(Resource.java:15)
		at com.shuwen.CloseResourceIn7.errorTest(CloseResourceIn7.java:21)
		... 1 more
```

复制代码可以发现，异常信息中多了一个Suppressed的提示，告诉我们这个异常其实由两个异常组成，Close method throw Exception这个异常是被Suppressed【屏蔽】的异常