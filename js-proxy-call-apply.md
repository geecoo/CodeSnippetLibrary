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
   
* ## 闭包 用法 ##

> 闭包就是一个函数引用另外一个函数的变量，因为变量被引用着所以不会被回收

