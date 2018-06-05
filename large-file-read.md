*PHP在处理大文件时, 会超级吃内存, 导致内存占用飙升, 我们知道CPU使用率和内存是衡量程序效率的重要因素, 当任何一个达到服务器的极限时, 都会出现问题.*

###  代码

    memory.php
    
    function convert($size) {
        $unit=array('b','kb','mb','gb','tb','pb');
        return @round($size/pow(1024,($i=floor(log($size,1024)))),2).' '.$unit[$i];
    }
    
    print convert(memory_get_peak_usage()) . PHP_EOL;
    
    
    
## 直接读取文件

    require "memory.php";

    print convert(memory_get_peak_usage()) . PHP_EOL;

    function readlargefile($path) {
        $lines = [];
        $handle = fopen($path, 'r');

        while(!feof($handle)) {
            $lines[] = trim(fgets($handle));
        }

        fclose($handle);
        return $lines;
    }

    readlargefile("shakespeare.txt");

    print convert(memory_get_peak_usage()) . PHP_EOL;
    
**The above example will output:**

    387.15 kb
    12.8 mb
    
    
