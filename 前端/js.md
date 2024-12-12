
## JavaScript中textContent、innerText和innerHTML的用法以及区别
```js
<div class="btm3"> 
    <div>Viserion</div>
</div>
```


1. 设置标签中的文本内容,应该使用textContent属性,谷歌,火狐支持,IE8不支持
2. 设置标签中的文本内容,应该使用innerText属性,谷歌,火狐,IE8都支持
3. 如果这个属性在浏览器中不支持,那么这个属性的类型是undefined
4. 判断这个属性的类型 是不是undefined,就知道浏览器是否支持


```
Viserion
```



1. 如果使用innerText主要是设置文本的,设置标签内容,是没有标签的效果的
2. innerHTML是可以设置文本内容
3.innerHTML主要的作用是在标签中设置新的html标签内容,是有标签效果的
4. 想要设置标签内容,使用innerHTML,想要设置文本内容,innerText或者textContent,或者innerHTML,推荐用innerHTML
5. innerText可以获取标签中间的文本内容,但是标签中如果还有标签,那么最里面的标签的文本内容也能获取.---获取不到标签的,文本可以获取
6.innerHTML才是真正的获取标签中间的所有内容


```js
<div>Viserion</div>
```

