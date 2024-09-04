![简图](https://img-blog.csdnimg.cn/20190728183400981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3lhbmdiYWdnaW8=,size_16,color_FFFFFF,t_70)

  

```java

Protocol protocol = ExtensionLoader.getExtensionLoader(Protocol.class).getExtension("myProtocol");

```

```mermaid

sequenceDiagram

  

Title: 简单逻辑

  

ExtensionLoader->> ExtensionLoader(T): getExtensionLoader(Class<T> type)

ExtensionLoader(T)->>T: getExtension(String name)

```

```java

public static <T> ExtensionLoader<T> getExtensionLoader(Class<T> type) {

if (type == null) {

throw new IllegalArgumentException("Extension type == null");

}

if (!type.isInterface()) {

throw new IllegalArgumentException("Extension type (" + type + ") is not an interface!");

}

if (!withExtensionAnnotation(type)) {

throw new IllegalArgumentException("Extension type (" + type +

") is not an extension, because it is NOT annotated with @" + SPI.class.getSimpleName() + "!");

}

  

ExtensionLoader<T> loader = (ExtensionLoader<T>) EXTENSION_LOADERS.get(type);

if (loader == null) {

EXTENSION_LOADERS.putIfAbsent(type, new ExtensionLoader<T>(type));

loader = (ExtensionLoader<T>) EXTENSION_LOADERS.get(type);

}

return loader;

}

```

```mermaid

sequenceDiagram

  

Title: ExtensionLoader.getExtensionLoader(Class<T> type)

participant ExtensionLoader

participant EXTENSION_LOADERS

ExtensionLoader->> EXTENSION_LOADERS: init()

Note over EXTENSION_LOADERS: ConcurrentMap 静态缓存

Note right of ExtensionLoader: 开始

EXTENSION_LOADERS->> ExtensionLoader(T): get(type)

ExtensionLoader(T)-->> ExtensionLoader: 返回 ExtensionLoader<T> loader

loop if （loader == null)

ExtensionLoader(T)->> ExtensionLoader(T): new ExtensionLoader<T>(type)

ExtensionLoader(T)->> EXTENSION_LOADERS: putIfAbsent(type, new ExtensionLoader<T>(type)）

EXTENSION_LOADERS->> ExtensionLoader(T): get(type)

EXTENSION_LOADERS-->> ExtensionLoader: 返回 ExtensionLoader<T> loader

end

```