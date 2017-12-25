---

layout: post
title: "Hive中的Join原理"
subtitle: "\"Introduction!\""
date: 2017-12-25
author: "fibears"
header-img: "img/in-post/header/hivejoin.jpg"
tags:
- Hive
- Join

---

## 简介

Hive 是基于 Hadooop 平台的数据仓库解决方案，它在 Hadoop 平台上搭建了一层SQL接口，从而可以规避繁琐的MapReduce开发过程。由于Hadoop是一个批处理且高延迟的计算框架，因此利用Hive可以很方便地完成对海量数据的查询和计算。虽然Hive可以处理大规模的数据，但是我们平时写 Hive 的时候经常碰到生成过多Jobs或者Jobs耗时很长的问题，此时需要考虑的是如何优化代码，提升运算效率。

当我们思考如何优化Hive性能时，需要将Hive代码看成MapReduce程序来解读，从更底层的角度思考如何优化。因此本文将主要介绍Hive中常用的Join操作的原理和优化技巧。

## MapReduce 过程简介

![mapreduce](/img/in-post/main/post-mapreduce-1.png)

如上图所示，MapReduce过程可以分成三个阶段：

- 阶段1(Map)：Map端的计算，如Input, Map, Partition, Sort ,Spill
- 阶段2(Shuffle)：Map端 -> Reduce端
- 阶段3(Reduce)：Reduce端的计算

从上图中可以看出，在MapReduce执行的过程中，Shuffle过程需要在节点之间拷贝数据，如果传输数据量过大会造成大量的网络开销。根据MapReduce的特点，我们可以看出提升Hive性能的两个要点是：尽可能地提升Job的运算效率和规避数据倾斜的问题。

## Hive中Join的原理

Hive 中Join操作可以根据执行的阶段分成两大类：Map阶段完成Join和Reduce阶段完成Join。

### Map阶段完成Join:Map Join

MapJoin使用场景为一个小表(默认为不大于25MB)和一个大表进行Join连接，该场景下会在客户端本地创建一个任务，用于扫码小表的数据，并将其转换成HashTable，最终在每个Map任务内存中都保存一份小表数据。接下来的步骤就是一个只有Map过程的MR，即执行Map任务扫描大表，同时根据大表的每一条记录去和HashTable进行关联，直接输出结果。

```sql
select /*+ MAPJOIN(a) */ a.vopenid,b.age
from table1 a join table2 b 
on a.vopenid=b.vopenid;
```

### Reduce阶段完成Join:Common Join

一般情况下，如果不设定MapJoin关键字或者不符合MapJoin的条件，那么Hive会将Join操作转换成Common Join，整个过程的三个阶段如下所示：

- Map阶段：读取原始数据，并输出key-value的数据，其中key为join操作的关联字段，然后按照key对数据进行排序
- Shuffle阶段：根据key的值做哈希处理，并将key-value的数据按照哈希值推送至不同的Reduce任务中，这样可以保证不同源表中的key能够位于同一个Reduce任务中
- Reduce阶段：根据key值完成Join操作

## Hive Join操作的性能优化

了解Join的原理后，我们需要思考的是如何提升运算效率。根据MapReduce的特点，我们可以看出提升Hive性能的两个要点是：提升Job的运算效率和规避数据倾斜的问题。

### 提升Job的运算效率

- 过滤条件置于ON的条件中：由于使用外关联时，如果将表的过滤条件写在where后面，则会先进行全表关联再过滤，增加了后续的计算量。

```sql
-- wrong 
select a.vopenid,b.water from a left join b on (a.key=b.key)
where a.dtstatdate>='20171222' and b.dtstatdate='20171223'
-- right
select a.vopenid,b.water from a left join b
on(a.key=b.key and a.dtstatdate>='20171222' 
and b.dtstatdate='20171223');
```

- 用group by+count来替代count distinct：由于count distinct操作需要用一个Reduce任务来处理，如果数据量过大的话会导致运算效率大大降低。

- 小表放置于Join操作符的左边

### 数据倾斜问题

常见的数据倾斜表现为任务进度长时间维持在99%，同时只有少量几个Reduce任务未完成。此时有几种常见的处理方法：

- 参数调节：set hive.groupby.skewindata=true(查询计划会生成两个MR进程，分步骤处理，从而实现负载均衡的目标)
- 过滤空值key对应的数据

```sql
select * from table1 a join table2 b 
on a.vopenid is not null and a.vopenid=b.vopenid;
```

- 将空值key转换成一个字符串加上随机数，从而将倾斜的数据分到不同的Reduce任务上

```sql
select * from table1 a left join table2 b
on case when a.vopenid is null then concat('random',rand()) else 
a.vopenid end =b.vopenid;
```

- 将关联字段的类型转换成一致的类型，避免Reduce任务分配不均

```sql
select * from table1 a left join table2
on a.vopenid=cast(b.roleid as string)
```


