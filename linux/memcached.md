## 主题 memcached

> [memcached协议](https://github.com/memcached/memcached/blob/master/doc/protocol.txt)

> [命令wiki](https://github.com/memcached/memcached/wiki/Commands)
  
### 介绍
Memcached 是一个高性能的分布式内存对象缓存系统, 用于动态Web应用以减轻数据库负载. 
它通过在内存中缓存数据和对象来减少读取数据库的次数, 从而提供动态数据库、提高网站的访问速度。
Memcached基于一个存储键/值对的hashmap. 其守护进程(daemon )是用C写的, 但是客户端可以用任何语言来编写，并通过memcached协议与守护进程通信

### 特点
1. 协议简单

2. 基于libevent的事件处理

3. 内置内存存储方式

4. 采用不互相通信的分布式

### 缺点

1. 重启memcached服务器或者操作系统会使数据消失

2. 内存容量达到指定值之后，就会使用LRU算法自动删除不使用或者很少使用的缓存

* memcached 安装

  移步 centos库, 里面有脚本



### 启动参数
1. -d 选项是启动一个守护进程                                                                                                                               
2. -m 是分配给Memcache使用的内存数量, 默认64MB                                                                                                              
3. -u 是运行Memcache的用户, 不要用root, 指定用户如 www                                                                                                                                
4. -l 是监听的服务器IP地址，如果有多个地址的话，指定多次，如指定了服务器的IP地址192.168.12.23                                                            
5. -p 是设置Memcached监听的TCP端口, 默认11211, 最好是1024以上的端口                                                                                                                   
6. -c 选项是最大运行的并发连接数，默认是1024, 按照你服务器的负载量来设定                                                                                                          
7. -P 是设置保存Memcache的pid文件路径，如指定目录 /var/memcached/memcached.pid 

8. -s 以socket方式来访问memcached, memcached的socket文件路径,启动服务时, 去掉参数 -l 和 -p

9. -e 自定义Escape character, 退出命令符好, 默认 ^] (Ctrl + ])

### telnet 127.0.0.1 11211

```
<command name> <key> <flags> <exptime> <bytes>
<data block>

<command name>：set/add/replace
<key>：查找关键字
<flags>： 整型参数(十进制数, 以存储16位或32位存储, 看版本)，客户使用它存储额外信息
<flags> is an arbitrary 16-bit unsigned integer (written out in
  decimal) that the server stores along with the data and sends back
  when the item is retrieved. Clients may use this as a bit field to
  store data-specific information; this field is opaque to the server.
  Note that in memcached 1.2.1 and higher, flags may be 32-bits, instead
  of 16, but you might want to restrict yourself to 16 bits for
  compatibility with older versions.
  
<exptime>：该数据的存活时间（以秒为单位，0 表示永远） 
<bytes>：存储字节数
<data block>：存储的数据块（可直接理解为key-value结构中的value）
```

1. set添加或者编辑 ( 注意 cas命令, Check And Set or Compare And Swap)
```
set name 0 0 5
linux
STORED
```

2. get数据
```
get name
VALUE name 0 5
linux
END
```

3. add 数据, 不能添加已存在的key
```
add name 0 0 5
linux
NOT_STORED
```

4. delete 数据
```
delete name
DELETED
```

5. replace 数据
```
replace name 0 0 5
unixx
STORED
```
6. stats 打印当前服务器各项指标值
7. flush_all  清掉所有缓存数据

### 退出 telnet

```
Escape character is '^]'

Ctrl + ]  然后输入 quit

脚本里如何退出
$ echo "" | telnet 127.0.0.1 11211
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
Connection closed by foreign host.

```

## 其它

[通读缓存回掉](http://php.net/manual/zh/memcached.callbacks.read-through.php)

[结果回调](http://php.net/manual/zh/memcached.callbacks.result.php)

## cas_token 在PHP 7 中失效的问题

```
 解决方案
 function memcacheGet($key, $cb = null, &$cas = null) {
   global $mc;
   if(empty($mc)) return false;

   if(defined('Memcached::GET_EXTENDED')) {
      //Incompatible change in php7, 增加了 flag参数
      $_o = $mc->get($key, $cb, Memcached::GET_EXTENDED);                                                                                                                            
      $o = $_o['value'];
      $cas = $_o['cas'];
   } else { 
      $o = $m->get($key, $cb, $cas);
   } 
    return $o;
} 
```

[失效问题看这里 1](https://github.com/php-memcached-dev/php-memcached/pull/214)

[失效问题看这里 2](http://php.net/manual/zh/memcached.get.php)

## flags 参数详解
[flags详解](https://blog.csdn.net/yanhui_wei/article/details/8138874)

## cas 可用秒杀场景 (系统级（Memcache自身提供）的冲突检测机制（乐观锁）), 关于秒杀方案，请看我的另一篇文章

```
防止死循环, 可以加个安全次数限制
$security_loop = 0;
do {                                                                                                                                           $security_loop++;
      if ($security_loop > 10) {
        break;
      }
      
     /* 获取ip列表以及它的标记 */
     $ips = memcacheGet('ip_block', null, $cas);
     /* 如果列表不存在， 创建并进行一个原子添加（如果其他客户端已经添加， 这里就返回false）*/
     if ($mc->getResultCode() == Memcached::RES_NOTFOUND) {
         $ips = array($_SERVER['REQUEST_TIME']);
         $mc->add('ip_block', $ips);
     /* 其他情况下，添加ip到列表中， 并以cas方式去存储， 这样当其他客户端修改过， 则返回false */
         echo "1." . $mc->getResultMessage();
     } else {
         $ips[] = $_SERVER['REQUEST_TIME'];
         $mc->cas($cas, 'ip_block', $ips);
 
         echo "2." . $mc->getResultMessage();
     }
} while ($mc->getResultCode() != Memcached::RES_SUCCESS);

```

## memcache作为队列使用
> 定义四个key, start_$qname , end_$qname, lock_key, item_$qname_%d, 每新增一个值, end_$qname自增1, item_$qname_%d格式化, 作为新增的那个值的key, 每次pop一个值, 就将start_$qname 自增1
https://github.com/meetrajesh/php-memqueue/blob/master/memqueue.php

## memcache 命中率的问题

执行 stats 命令

cmd_get：   总共向memcached服务器请求的次数

get_hits：  响应成功的次数

get_misses：失败的次数

##### 命中率越高越好

## memcache 雪崩

1. 缓存失效（重启、增加删除节点、缓存周期性的失效）

2. 失效后, 第一个进程去数据库获取新数据, 假如包括SQL+程序逻辑耗时5S、

3. 这5S内, 第二个、第三个...第N个都只是获取到已失效的缓存, 于是也都连接数据库...

4. 结果显而易见, 数据库锁表 -> 数据库累计大量进程 -> 直至数据库挂掉

解决方案

1. 不同的key, 设置不同的过期时间, 让缓存失效的时间点尽量均匀

2. 凌晨定时更新一批常用的缓存

3. 利用memcache加key的方式增加mutex key (互斥锁) 

   https://timyang.net/programming/memcache-mutex/
   
   https://blog.csdn.net/rainkid/article/details/6802867
   
4. 利用gearman

   http://lists.danga.com/pipermail/memcached/2007-July/004858.html
   
   http://lists.danga.com/pipermail/memcached/2007-July/004863.html
   
5. PHP lock
   https://code.google.com/p/phplock/

6. memcached作者Brad Fitzpatrick用Go开发了替代版 groupcache，比memcached更多功能，一个亮点就是解决了雪崩的问题。

   groupcache 项目地址：https://github.com/golang/groupcache

## 缓存穿透

> 一般的缓存系统，都是按照key去缓存查询，如果不存在对应的value，就应该去后端系统查找（比如DB）。如果key对应的value是一定不存在的，并且对该key并发请求量很大，就会对后端系统造成很大的压力

1. 对查询结果为空的情况也进行缓存，缓存时间设置短一点，或者该key对应的数据insert了之后清理缓存

2. 如果key有一定规则, 则采取规则验证机制、过滤无效key

3. 对一定不存在的key进行过滤。可以把所有的可能存在的key放到一个大的Bitmap中，查询时通过该bitmap过滤

   方案, 采用布隆过滤器, 如果允许小概率误判 ! [详情 点击](https://blog.csdn.net/qianshangding0708/article/details/48030057)

## 数据分布式存储原理

[分布式缓存实现原理](https://www.cnblogs.com/weixing/p/5522903.html)

* 余数计算分散法(取模法)

> CRC($key)%N

算法描述: 客户端首先根据key来计算CRC，然后结果对服务器数进行取模得到memcached服务器节点

1. 当选择到的服务器无法连接的时候,一种解决办法是将尝试的连接次数加到key后面,然后重新进行hash，这种做法也叫rehash

2. 第二个问题也是这种方法的致命的缺点,尽管余数计算分散发相当简单,数据分散也很优秀,当添加或者移除服务器的时候,缓存重组的代价相当大

* Consistent Hashing算法(哈希一致性算法)

> Consistent Hashing算法描述：首先求出memcached服务器节点的哈希值，并将其分配到0\~2^32的圆上, 这个圆我们可以把它叫做值域，然后用同样的方法求出存储数据键的哈希值，并映射到圆上。然后从数据映射到的位置开始顺时针查找，将数据保存到找到的第一个服务器上，如果超过0~2^32仍找不到，就会保存在第一台memcached服务器上

* 优化的Consistent Hashing算法(虚拟节点的思想以解决key分布不均匀的问题)
> 使用consistent Hashing最大限度的抑制了键的重新分配，且有的consistent Hashing的实现方式还采用了虚拟节点的思想。问题起源于使用一般hash函数的话，服务器的映射地点的分布非常不均匀，从而导致数据库访问倾斜，大量的key被映射到同一台服务器上。为了避免这个问题，引入了虚拟节点的机制，为每台服务器计算出多个hash值，每个值对应环上的一个节点位置，这种节点叫虚拟节点。而key的映射方式不变，就是多了层从虚拟节点再映射到物理机的过程。这种优化下尽管物理机很少的情况下，只要虚拟节点足够多，也能够使用得key分布的相对均匀

## slab内存管理
[内存管理和slab结构图](https://blog.csdn.net/yxnyxnyxnyxnyxn/article/details/7869900)

## 缓存算法

1. FIFO算法：First in First out

   先进先出. 原则：一个数据最先进入缓存中，则应该最早淘汰掉。也就是说，当缓存满的时候，应当把最先进入缓存的数据给淘汰掉

2. LFU算法：Least Frequently Used 

   最不经常使用算法

3. LRU算法：Least Recently Used

   近期最少使用算法. 请查看：[Memcached之你真正理解LRU吗](https://blog.csdn.net/qianshangding0708/article/details/47980697)

   LRU和LFU的区别. LFU算法是根据在一段时间里数据项被使用的次数选择出最少使用的数据项，即根据使用次数的差异来决定. 而LRU是根据使用时间的差异来决定的.

