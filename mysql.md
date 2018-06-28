* ## 内存表 临时表 ##

1. 内存表
  内存表的数据结构建立在磁盘上, 数据存放在内存,对所有用户的连接是可见的，这使得它非常适合做缓存, 服务器重启，数据丢失, 数据结构还在
  不支持text/blob类型
  
```
create table tmp2(id int not null)  ENGINE=memory
```


2. 临时表
   临时表,数据结构和数据都放在内存, 如果不指定引擎,则用默认引擎, 
   当前连接可见, 当这个连接关闭的时候，会自动drop, 不同的连接,可以使用相同的临时表名
   
```
create temporary table tmp_table (   name varchar(10) not null,   value integer not null   ) 

```
总结

1. memory临时表由于表的增大 可能会转变为myisam临时表——其转化临界点由max_heap_table_size 和tmp_table_size系统变量的 较小值 决定的
2. 当不需要临时表时, 必须显式 truncate table tb; drop table tb;


