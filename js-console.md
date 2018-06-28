* 打印日志

```
console.log('普通');
console.info('信息');
console.error('错误');
console.warn('警告');

控制台会有不同的图标, 方便标识不同的错误信息
不同浏览器, 表现得方式有点区别, 基本忽略
```

* 占位符用法, 支持 字符(%s)、整数(%d 或 %i)、浮点数(%f)和对象(%o)
```
console.info("%d年%d月%d日",2011,3,26);
console.log("%d年%d月%d日",2011,3,26);
```

* 信息分组

```
console.group("第一组信息");
    console.log("第一组第一条");
    console.log("第一组第二条");
console.groupEnd();
console.group("第二组信息");
   console.log("第二组第一条");
   console.log("第二组第二条");
console.groupEnd();

和 console.groupCollapsed() 类似
```


* 打印对象信息
```
var log = {name: 'error', show: function () { console.info('test');}};
console.dir(log);
```
* 将对象和数组以表格的形式打印
```
console.table()
```

* 显示某个节点的内容, 打印原生对象的html代码, JQ对象的打印还是对象结构
```
var info = document.getElementById('info');
console.dirxml(info);
```

* 判断变量是否是真

```
console.assert(expression, object[, object...])
console.assert(year == 2018);
console.assert(year == 2018, '此处的值将会输出');
```

* 输出执行到该行的次数
```
console.count([label])  可选参数 label 可以输出在次数之前

for (var i = 0; i < 5; i++) {
   console.count('count');
}
// count: 1, ... count: 5
```

* 追踪函数的调用轨迹
```
console.trace();
```
* 计时功能
```
console.time("timer1");
@TODO 
console.timeEnd("timer1");
```

* 性能分析
```
console.profile('性能分析器');
@TODO 
console.profileEnd();
看火狐
```
* 显示此刻使用的内存信息（这是一个属性而不是方法）

```
for (var i = 0; i < 10; i++) {
    console.memory
}
```

* 清空控制台的内容
```
console.clear() ; 或者 ctrl+L
```

* $0 可以在控制台输出在Elements面板选中的页面元素
* $_ 表示上一次在控制台键入的命令，你也可以用快捷键"上方向键"来达到同样的效果
* $x 可以用xPath的语法来获取页面上的元素
* console.debug() 用于输出输出debug的信息
* console.timeStamp() 用于标记运行时的timeline信息

