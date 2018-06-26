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
