* 打印日志

```
console.log('普通');
console.info('信息');
console.error('错误');
console.warn('警告');

控制台会有不同的图标, 方便标识不同的错误信息
不同浏览器, 表现得方式有点区别, 基本忽略
```

* 占位符用法
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
```

* 打印对象信息
```
var log = {name: 'error', show: function () { console.info('test');}};
console.dir(log);
```

* 显示某个节点的内容, 打印对象原生的html代码
```
var info = document.getElementById('info');
console.dirxml(info);
```

