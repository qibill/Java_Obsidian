**singleton**表示在spring容器中的单例，通过spring容器获得该bean时总是返回唯一的实例

**prototype**表示每次获得bean都会生成一个新的对象

**request**表示在一次http请求内有效（只适用于web应用)

**session**表示在一个用户会话内有效(只适用于web应用)

**globalSession**表示在全局会话内有效〔只适用于web应用)

在多数情况，我们只会使用singleton和prototype两种scope，如果在spring配置文件内未指定scope属性，默认为singleton,
