* ## 内存表 临时表 ##

1. 内存表
  内存表的数据结构建立在磁盘上, 数据存放在内存,
  
```
create table tmp2(id int not null)  ENGINE=memory
```


2. 临时表
   临时表,数据结构和数据都放在内存, 如果不指定引擎,则用默认引擎, 
   
```
create temporary table tmp_table (   name varchar(10) not null,   value integer not null   ) 

```
