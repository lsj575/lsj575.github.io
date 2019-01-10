---
title: MySQL中的时间字段的几种数据类型比较
date: 2018-08-12 00:10:08
tags: [MySQL]
---

# 1.序言

​	最近在项目开发时，对于MySQL数据库中的有关时间的字段该选用何种类型，引发了一些争论。所以做了一些简单的研究，看了一些blog，和官方文档。最后做出一个自己的总结。

<!-- more -->

# 2.类型比较

## INT

​	INT类型一般用于存储Unix时间戳。使用一个简单的 INT 列保存 Unix 时间是最普通的方法。使用 INT，你可以确保你要保存的数字可以快速、可靠地插入到表中。

- 占用4个字节

- 建立索引之后，查询速度快

- 条件范围搜索可以使用使用between

- 不能使用MySQL提供的时间函数




  	如果只是存储 Unix 时间戳（并且在它的合法日期范围内），而且不打算在它上面使用任何基于日期的查询，我觉得使用 INT 是可以的。它执行简单数值比较查询时非常快，因为只是在处理简单的数字。

__结论：__适合需要进行大量时间范围查询的数据表



## TIMESTAMP和DATETIME

​	Datetime 和 Timestamp 都保存日期和时间信息，同时，使用人类可读的日期形式，如 "2018-01-01"。

> TIMESTAMP: A four-byte integer representing seconds UTC since the epoch ('1970-01-01 00:00:00' UTC)

​	TIMESTAMP：一个四字节的整数，从1970-01-01 00:00:00开始算起。

​	Timestamp 和 Datetime 的核心不同点主要在于 MySQL在内部如何表示这些信息：**两种都以二进制而非字符串形式存储**，但在表示日期/时间部分时 Timestamp （4 字节） 比 Datetime （5字节，从MySQL 5.6.4版本起，之前为8个字节） 少使用 1 字节。当保存毫秒部分时两种都使用额外的空间 （1到3 字节）。

- 非分数部分的DATETIME编码（5.6.4版本之后）：

  ```
   1 bit  sign           (1= non-negative, 0= negative)
  17 bits year*13+month  (year 0-9999, month 0-12)
   5 bits day            (0-31)
   5 bits hour           (0-23)
   6 bits minute         (0-59)
   6 bits second         (0-59)
  ---------------------------
  40 bits = 5 bytes
  ```



Timestamp 节省的 1 字节是有代价的：你只能存储从 '1970-01-01 00:00:01.000000' 到 '2038-01-19 03:14:07.999999' 之间的时间。而 Datetime 允许你存储从 '1000-01-01 00:00:00.000000' 到 '9999-12-31 23:59:59.999999' 之间的任何时间。

另一个重要的差别是MySQL使用**服务器的时区**转换Timestamp值到它的 UTC 等价值再保存。当获取值是它会再次进行时区转换，所以你得回了你“原始的”日期/时间值。Datetime 列不会被数据库改变。无论时区怎样配置，每次都会保存和获取到同样的值。

在5.6版本之后，datetime开始可以使用current time作为默认值，所以不再有timestamp比datetime更适合作为需要随插入和更新来变更时间的类型的说法。



__结论：__Datetime 是大多数场景下的最佳选择，理由是

> 无需任何转换即是人类可读的。
>
> 不会因为时区变换产生问题。
>
> 只比TIMESTAMP多用 1 字节
>
> 支持更大的日期范围（从 1000 年到 9999 年）

# 3.总结

​	没有完美的数据类型，只要最合适的数据类型。这么多类型同时存在一定是有其道理的，了解清楚了不同类型的差异，就能选择最合适的类型。



参考：

[1]: https://www.jb51.net/article/125715.htm 解析MySQL中存储时间日期类型的选择问题

[2]: https://dev.mysql.com/doc/internals/en/date-and-time-data-type-representation.html Date and Time Data Type Representation

[3]: https://www.jianshu.com/p/ec77b0631886 MySQL中你应该使用什么数据类型表示时间？

