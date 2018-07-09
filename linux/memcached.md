## 主题 memcached

* memcached 安装
略过

* 启动参数
1. -d 选项是启动一个守护进程                                                                                                                               
2. -m 是分配给Memcache使用的内存数量, 默认64MB                                                                                                              
3. -u 是运行Memcache的用户, 不要用root, 指定用户如 www                                                                                                                                
4. -l 是监听的服务器IP地址，如果有多个地址的话，指定多次，如指定了服务器的IP地址192.168.12.23                                                            
5. -p 是设置Memcached监听的TCP端口, 默认11211, 最好是1024以上的端口                                                                                                                   
6. -c 选项是最大运行的并发连接数，默认是1024, 按照你服务器的负载量来设定                                                                                                          
7. -P 是设置保存Memcache的pid文件路径，如指定目录 /var/memcached/memcached.pid 

8. -s 以socket方式来访问memcached, memcached的socket文件路径,启动服务时, 去掉参数 -l 和 -p

* telnet 127.0.0.1 11211

```
<command name> <key> <flags> <exptime> <bytes>
<data block>

<command name>：set/add/replace
<key>：查找关键字
<flags>：整型参数，客户机使用它存储关于键值对的额外信息
<exptime>：该数据的存活时间（以秒为单位，0 表示永远） 
<bytes>：存储字节数
<data block>：存储的数据块（可直接理解为key-value结构中的value）
```

1. set添加或者编辑
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


