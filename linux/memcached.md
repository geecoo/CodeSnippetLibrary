## 主题 memcached

> [memcached协议](https://github.com/memcached/memcached/blob/master/doc/protocol.txt)

> [命令](https://github.com/memcached/memcached/wiki/Commands)
  
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


