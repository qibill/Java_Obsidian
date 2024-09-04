Jackson是一个简单基于Java应用库，Jackson可以轻松的将Java对象转换成json对象和xml文档，同样也可以将json、xml转换成Java对象。Jackson所依赖的jar包较少，简单易用并且性能也要相对高些，并且Jackson社区相对比较活跃，更新速度也比较快。
### 特点

---


- 容易使用 - jackson API提供了一个高层次外观，以简化常用的用例。

- 无需创建映射 - API提供了默认的映射大部分对象序列化。

- 性能高 - 快速，低内存占用，适合大型对象图表或系统。

- 干净的JSON - jackson创建一个干净和紧凑的JSON结果，这是让人很容易阅读。

- 不依赖 - 库不需要任何其他的库，除了JDK。

- 开源代码 - jackson是开源的，可以免费使用。


###  三种方式处理JSON

---
 提供了三种不同的方法来处理JSON

- 流式API - 读取并将JSON内容写入作为离散事件。 JsonParser读取数据，而JsonGenerator写入数据。它是三者中最有效的方法，是最低的开销和最快的读/写操作。它类似于Stax解析器XML。

- 树模型 - 准备JSON文件在内存里以树形式表示。 ObjectMapper构建JsonNode节点树。这是最灵活的方法。它类似于XML的DOM解析器。

- 数据绑定 - 转换JSON并从POJO（普通Java对象）使用属性访问或使用注释。它有两个类型。

     - 简单的数据绑定 - 转换JSON和Java Maps, - Lists, Strings, Numbers, Booleans 和null 对象。
     - 全部数据绑定 - 转换为JSON从任何JAVA类型。

ObjectMapper读/写JSON两种类型的数据绑定。数据绑定是最方便的方式是类似XML的JAXB解析器。


###  问题

---

#### 序列时对null与""的处理
```
    ObjectMapper mapper = new ObjectMapper();  
    //通过该方法对mapper对象进行设置，所有序列化的对象都将按改规则进行系列化  
    //Include.Include.ALWAYS 默认  
    //Include.NON_DEFAULT 属性为默认值不序列化  
    //Include.NON_EMPTY 属性为 空（“”）  或者为 NULL 都不序列化  
    //Include.NON_NULL 属性为NULL 不序列化  
    mapper.setSerializationInclusion(Include.NON_NULL);  
    User user = new User(1,"",null);  
    String outJson = mapper.writeValueAsString(user);  
    System.out.println(outJson);  
```

#### 空值序列化报错
控制台报错信息：
```
com.fasterxml.jackson.databind.JsonMappingException: No content to map due to end-of-input
 at [Source: java.io.StringReader@880ec60; line: 1, column: 1]
	at com.fasterxml.jackson.databind.JsonMappingException.from(JsonMappingException.java:164)
	at com.fasterxml.jackson.databind.ObjectMapper._initForReading(ObjectMapper.java:2931)
	at com.fasterxml.jackson.databind.ObjectMapper._readMapAndClose(ObjectMapper.java:2873)
	at com.fasterxml.jackson.databind.ObjectMapper.readValue(ObjectMapper.java:2034)
	at com.biosan.utils.JsonUtils.jsonToPojo(JsonUtils.java:53)
	at com.biosan.client.test.main(test.java:27)
```

#### 序列化属性改名
**单个**在属性前加 *@JsonProperty("改后的属性名")*
```
public class Patient {

	@JsonProperty("patientname")  
	private String mothername;// 母亲姓名
	
```
**所有的属性名**在pojo类前加 *@JsonNaming(UpperCaseStrategy.class)* 

```
@JsonNaming(UpperCaseStrategy.class) 
public class Patient {

```
UpperCaseStrategy.class 是自定义的策略，属性名大写。**extend PropertyNamingStrategyBase**。

```
/**
 * 属性命名策略-属性名大写
 * 
 * @author qibill
 */
public class UpperCaseStrategy extends PropertyNamingStrategyBase{
	
	@Override
	public String translate(String arg0) {
		return arg0.toUpperCase();
	}

}
```

