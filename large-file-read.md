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
    12.8 mb  //可以看出内存飙升


## 使用分块 chunk 或 生成器 yield 解决

### 分块读取

    // chunk 循环整个文件, 1.5G
    function readlargefile2($file) {
        $i = 0;
        $lines = [];
        $fp = fopen($file, 'r');

        $num = 10;
        $chunk = 4096 * 3;
        $readData = '';
        $fs = sprintf("%u", filesize($file));

        $max = (intval($fs) == PHP_INT_MAX) ? PHP_INT_MAX : filesize($file);

        for ($len = 0; $len < $max; $len += $chunk)
        {
            /*
            $readData = fread($fp, $chunk);
            $i = $i + substr_count ($readData, PHP_EOL);
            unset ($readData);
            */

            $readData = fread($fp, $chunk);
            if (preg_match_all ("/\d{4}-\d{2}-\d{2}/", $readData, $parts)) {
                //$lines = array_merge($lines, $parts[0]);
                $i = $i + count($parts[0]);
            }
        }

        fclose($fp);
        //echo $data; 
        //print_r(count($lines));
        echo $i;
        echo PHP_EOL;
    }

**The above example will output:**

    397.27 kb    // 执行前内存峰值
    5564474      // 逻辑处理结果
    419.38 kb    // 执行后内存峰值
    2.7169878482819  // 耗时


### yeild 生成器

    function readlargefile($path) {
            $lines = [];
            $handle = fopen($path, 'r');

            while(!feof($handle) && ($buffer = fgets($handle)) !== false) {
                yield trim($buffer);
            }

            fclose($handle);
     }

    $log1 = '/data/www/infp_crm_admin/storage/logs/pc.log';
    $log2 = '/data/www/infp_crm_admin/storage/logs/laravel.log';


    $iterator = readlargefile($log1);
    $i = 0;

    foreach ($iterator as $iteration) {
        //
        //$i = $i + 1;
        if (preg_match_all ("/\d{4}-\d{2}-\d{2}/", $iteration, $parts)) {
            $i = $i + count($parts[0]);
        }
    }
    echo $i . PHP_EOL;
    
**The above example will output:**

    391.39 kb // 执行前内存峰值
    5568542   // 逻辑处理结果
    3.52 mb   // 执行后内存峰值
    4.0124680995941  // 耗时
    
**总结, 文件1.5G 大小, 如果遍历整个文件, 分块效率稍微高yeild一筹, 关键看处理逻辑, 分块有个缺点, 会导致字符串断裂, 所以在匹配IP或日期之类的数据时, 可能不准, yeild 内存峰值略高, 不过可以接受**


### 用fseek 实现tail函数

    function tail1($file) {
       $fp = fopen($file, "r");
       $line = 10;
       $pos = -2;
       $t = " ";
       $data = "";
       while ($line > 0)
       {
           while ($t != "\n")
           {
               fseek($fp, $pos, SEEK_END);
               $t = fgetc($fp);
               $pos--;
           }
           $t = " ";
           $data = fgets($fp) . $data;
           $line--;
       }
       fclose($fp);
       echo $data;
    }
    
    399.37 kb  // 执行前峰值
    399.37 kb  // 执行后峰值
    0.0068020820617676  // 耗时
    
    
    function tail($file) {
        $lines = [];
        $fp = fopen($file, 'r');

        $num = 10;
        $chunk = 4096;
        $readData = '';
        $fs = sprintf("%u", filesize($file));

        $max = (intval($fs) == PHP_INT_MAX) ? PHP_INT_MAX : filesize($file);

        for ($len = 0; $len < $max; $len += $chunk)
        {
            $seekSize = ($max - $len > $chunk) ? $chunk : $max - $len;
            fseek($fp, ($len + $seekSize) * -1, SEEK_END);
            $readData = fread($fp, $seekSize) . $readData;
            if (substr_count($readData, "\n") >= $num + 1)
            {
                preg_match("!(.*?\n){" . ($num) . "}$!", $readData, $match);
                $data = $match[0];
                break;
            }
        }
        fclose($fp);
        echo $data; 
    }

    399.37 kb  // 执行前峰值
    399.37 kb  // 执行后峰值
    0.00037598609924316  // 耗时
    
    // 一个思路, 直接使用不正确, 缺损数据
    // 使用递增因子做offset , 来读取块, 有点memcache存数据的意思
    function tail3 ($file, $n, $base = 5) {
       assert($n > 0);
       $fp = fopen ($file, 'r');
       $pos = $n + 1;
       $lines = array();
       while (count($lines) <= $n)
       {
           try
           {
               fseek($fp, -$pos, SEEK_END);
           }
           catch (Exception $e)
           {
               fseek(0);
               break;
           }
           $pos *= $base;
           while (!feof($fp))
           {
               array_unshift($lines, fgets($fp));
           }
       }
    
       print_r(array_slice($lines, 0, $n));
    }
    
**一般我们 fseek 检索文件指针来处理大文件的逻辑操作, 主要用到  数据平均分块思想,  生成器yeild 或者 步长因子（类似memcahce存数据)**
