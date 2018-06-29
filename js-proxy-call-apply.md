* ## $.proxy() 常用用法 ##


```
jQuery.proxy( function, context )
/**
* function将要改变上下文语境的函数。
* context函数的上下文语境(`this`)会被设置成这个 object 对象。
*/

jQuery.proxy( context, name )
/**
* context函数的上下文语境会被设置成这个 object 对象。
* name将要改变上下文语境的函数名(这个函数必须是前一个参数 ‘context’ **对象的属性)
*/

$('#myElement').click(function() {

    setTimeout($.proxy(function() {                 // 注意这里 $.proxy()

        $(this).addClass('aNewClass');  

    }, this), 1000);                                // this 已经传入到匿名函数里了, 代表对象  $('#myElement')
});



```

```
请看下面完整的例子

<input type="button" value="测试$.proxy()" id="proxy1">
<script type="text/javascript">
    var Control = {
        type: 'text',
        render: function () {
            console.info("I am " + this.type);
        }
    };

    $("#proxy1").on("click",  function () {
        setTimeout( $.proxy(Control.render, Control), 1000);
    });
    // I am text

    $("#proxy1").on("click",  function () {
        setTimeout( $.proxy(Control.render, this), 1000);
    });
    // I am button

    $("#proxy1").on("click",  function () {
        setTimeout( $.proxy(Control, 'render'), 1000);
    });
    // I am text
 </script>
```

* ## call apply 用法 ##

1. 每个函数都包含两个非继承而来的方法：call()方法和apply()方法

2. 相同点：这两个方法的作用是一样的, 只是传参不同
   改变函数体内部 this 的指向，以扩充函数赖以运行的作用域, 强行把别人的变成自己的
   
3. 实现了继承

   ```
    function Animal(name){   
      this.name = name;   
      this.showName = function(){   
        console.log(this.name);   
      }   
    }   

    function Cat(name){  
      Animal.call(this, name);  
    }   

    var cat = new Cat("Black Cat");   
    cat.showName(); //Black Cat
   ```
   
4. apply([thisObj [,argArray] ]); 调用一个对象的一个方法,  另一个对象替换当前对象

   call([thisObject[,arg1 [,arg2 [,...,argn]]]]); 应用某一对象的一个方法 , 用另一个对象替换当前对象
   
* ## 闭包 ##

> 

````
1. 第一个闭包例子

var db = (function() {

    var data = {};   // 创建一个隐藏的object, 这个object持有一些数据从外部是不能访问这个object的
    
    // 创建一个函数, 这个函数提供一些访问data的数据的方法
    return function(key, val) {
        if (val === undefined) { return data[key] } // get
        else { return data[key] = val } // set
     }
     
    // 我们可以调用这个匿名方法返回这个内部函数，它是一个闭包
})();

db('x'); // 返回 undefined
db('x', 1); // 设置data['x']为1
db('x'); // 返回 1
// 我们不可能访问data这个object本身
// 但是我们可以设置它的成员

2. 第二个闭包例子（特殊的不返回匿名函数的）
function closureExample(objID, text, timedelay) { 
    setTimeout(function() { 
        document.getElementById(objID).innerHTML = text; 
    }, timedelay); 
} 
closureExample(‘myDiv’, ‘Closure is created’, 500);
```

## 总结闭包 ##

1. 闭包是一种设计原则或者说编程技巧， 它通过分析上下文，来简化用户的调用，让用户在不知晓的情况下，达到他的目的

2. 不需要知道闭包具体实现细节, 如果需要知道才能用好的话，这个闭包设计的不合理

3. 某函数return一个匿名函数， 该匿名函数引用宿主函数的变量，因为变量被引用着所以不会被回收 （也有不return 匿名函数的闭包 setTimeout）


