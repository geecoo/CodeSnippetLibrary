*  Jquery 版本问题

 > 1.x 系列 支持IE低版本,   根据 IE版本判断引入不同版本的浏览器, 用开源的cdn就好,  最新稳定版, 可能一些插件不支持
 
 
 *  最快的选择器: id选择器和元素标签选择器 
```
$('#id') 
$('form') 
$('input') 

JQuery 内部转换成原生JS写法, document.getElementById
```

* 较慢的选择器：class选择器 
> 性能取决于浏览器,  高级浏览器会 实现 getElementByClassName, 所以性能还不错

* 最慢的选择器：伪类选择器和属性选择器

```
$(':hidden') 
$('[attribute=value]') 
现代浏览器都有 querySelector()和querySelectorAll() , 所以性能提升不少
```

> ID选择器 >  标签选择器 > Class选择器 > 其他选择器
