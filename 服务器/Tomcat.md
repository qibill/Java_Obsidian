### tomcat 请求url中带有特殊符号{} | 返回400的问题


在tomcat目录/conf/**catalina.properties**下找到并修改

**tomcat.util.http.parser.HttpParser.requestTargetAllow=|,{,}**