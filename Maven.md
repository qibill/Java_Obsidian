---
tags:
  - 项目构建
---
### 导入本地jar


```
mvn install:install-file -Dfile=D:/ojdbc6-11.2.0.3.jar -DgroupId=com.biosan -DartifactId=ojdbc6 -Dversion=0.1 -Dpackaging=jar
```

### maven依赖冲突如何解决
1. 查找依赖冲突
	- idea自带的maven依赖管理工具
	- 通过maven 命令 dependency:tree -Dverbose
2. 解决冲突
	- dependencyManagement标签
	- exclusion标签