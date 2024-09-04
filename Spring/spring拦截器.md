
```
	<mvc:interceptors>
		<mvc:interceptor>
			<mvc:mapping path="/**"/>
			<!-- <mvc:mapping path="/item/**"/> -->
			<bean class="com.taotao.portal.interceptor.Interceptor1"></bean>
		</mvc:interceptor>
				<mvc:interceptor>
			<mvc:mapping path="/**"/>
			<!-- <mvc:mapping path="/item/**"/> -->
			<bean class="com.taotao.portal.interceptor.Interceptor2"></bean>
		</mvc:interceptor>
	</mvc:interceptors>
```




### 第一个拦截器放行，第二个拦截器也放行：

这是第一个拦截器Interceptor1。。。preHandle
这是第二个拦截器Interceptor2。。。preHandle

这是第二个拦截器Interceptor2。。。postHandle
这是第一个拦截器Interceptor1。。。postHandle

这是第二个拦截器Interceptor2。。。afterCompletion
这是第一个拦截器Interceptor1。。。afterCompletion


### 第一个拦截器放行，第二个不放行：

==Springmvc规定：凡是preHandle返回true，afterCompletion必须执行。==

这是第一个拦截器Interceptor1。。。preHandle
这是第二个拦截器Interceptor2。。。preHandle
这是第一个拦截器Interceptor1。。。afterCompletion


### 拦截器1阻止，拦截器2阻止：
HandlerInterceptor1.........preHandle
