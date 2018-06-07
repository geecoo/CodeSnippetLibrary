### 某知识点的思路提点

* **INPUT框实现多标签**

    [Bootstrap Tagsinput](https://github.com/bootstrap-tagsinput/bootstrap-tagsinput)
  
* INPUT框实现自动完成

    [Jquery UI Autocomplete](http://jqueryui.com/autocomplete/)
    
    [jQuery Autocompleter](https://github.com/ArtemFitiskin/jquery-autocompleter)

* **laravel 相关**

    修改模板后缀 [链接1](https://segmentfault.com/a/1190000006912155)
    
    在AppServiceProvider 中 增加 View::addExtension('html', 'blade');
    
    自定义分页模板

    \Illuminate\Pagination\LengthAwarePaginator::defaultView('partials.paginator');
    
* **JS扫码枪输入SN码**

    关键思路, 监听keydown事件, 同一输入框多次扫描, 扫描枪不会自动删除之前的数据, 所以我用oldkey来替换, 扫描完后,自动跳到下一个input框
    
    ```
    $('.JS-sn-code').on({
  
        'focus': function (e) {
            e = e || event;
            e.preventDefault();
            if ($(this).val()) {
                $(this).select(); 
            } 
        },
        
        'keydown': function (e) {
            e = e || event;
            var keyCode = e.keyCode ? e.keyCode : e.which ? e.which : e.charCode;
            
            // 监听backspace
            if (keyCode == "8") {
                $(this).data('oldkey', '');
                // 其他逻辑
            }
            
            // 监听回车
            if(keyCode == "13") {
                e.preventDefault();
                // 获取上一次保存的SN
                var oldkey = $(this).data('oldkey');
                var newkey = $(this).val();
                console.info("old key:" + oldkey);

                if (oldkey && newkey.substring(oldkey.length).length > 0) {
                    newkey = newkey.substring(oldkey.length); 
                }

                // 保存新的SN码
                $(this).data('oldkey', newkey);
                $(this).val(newkey);
                GArr.CurID = $(this).attr('id');
                console.info("新SN码:" + newkey);
            }      
        }
        
    })
    ```
    
    
    
