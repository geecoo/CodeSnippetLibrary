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


* 父子元素比较

```
$parent 变量是已生成的dom对象

1. 下面这条语句 JQeury 会转换为 $parent.find('child'), 转换过程中，性能损失一部分
$('.child', $parent) 

2. 最快 , find 会直接调用原生 getElementById , getElementByName, getElementByTagName 等
$parent.find('.child') 

3. 在jQuery内部，会使用$.sibling()和javascript的nextSibling()方法，一个个遍历节点, 性能损失一半
$parent.children('.child') 

4. jQuery内部使用Sizzle引擎，处理各种选择器。Sizzle引擎的选择顺序是从右到左，所以这条语句是先选.child，然后再一个个过滤出父元素#parent
$('#parent > .child') 

5. 比上一句又慢了一点点, 要筛选所有的子节点
$('#parent .child') 

6. jQuery内部会将这条语句转成$('#parent').find('.child')
$('.child', $('#parent')) 

开发时, 可先生成 dom元素对象 $parent, 再去操作子元素, 速度刚刚的
```


* 不要过度使用JQuery , 能原生, 还是要原生

* 做好缓存, 能链式就链式, JQuery 会缓存链式结果

* 事件的委托处理（EventDelegation） 

> .delegate()是当事件冒泡到指定的父元素时触发, .live()则是当事件冒泡到文档的根元素后触, 对动态添加的元素也生效, bind事件只对存在的元素生效

* 少改动DOM结构 
```
1. 不要频繁使用.append()、.insertBefore()和.insetAfter(), 像插入SQL一样, 拼接一下, 一次性插入
2. 如果你要对一个DOM元素进行大量处理，应该先用.detach()方法，把这个元素从DOM中取出来，处理完毕以后，再重新插回文档
```

