---
title: MySQL 驱动包在6.0版本后出现的坑
date: 2018-08-03 14:00:41
tags: [Java,JDBC,MySQL,JAR]
---

近段时间在复习JDBC时遇到了一些新的问题，写下此篇文章记录一下

<!-- more -->

## 加载驱动的类在包内位置变更

按往常一样写好连接数据库的方法之后导入的包是：

```java
import com.mysql.jdbc.Driver
```

运行之后报错

> Loading class `com.mysql.jdbc.Driver`. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver`. The driver is automatically   registered via the SPI and manual loading of the driver class is generally unnecessary.

 意思是说在`com.mysql.jdbc.Driver`下的类已经被弃用了，新的driver类在`com.mysql.cj.jdbc.Driver`下。

 这是在6.0版本的驱动包后出现的，所以我们将路径改为新的路径即可。

## 新版本驱动在访问数据库时无法识别时区

报错信息为：

> Error querying database.  Cause: java.sql.SQLException: The server time zone value '�й���׼ʱ��' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.

服务器的时区值 xxx 是无法试别或者代表了多个时区。

用sql查看一下数据库时区

```sql
SELECT TIMEDIFF(NOW(), UTC_TIMESTAMP);
```

如果是中国标准时间，会输出08:00

> +----------------------------------------------------+
> | TIMEDIFF(NOW(), UTC_TIMESTAMP) |
> +----------------------------------------------------+
> | 08:00:00                                                |
> +----------------------------------------------------+

解决方案有两个第一个是修改JDBC连接数据库时的url值

```
jdbc:mysql://localhost:3306/jdbctest?serverTimezone=UTC
```

添加一个`serverTimezone`的参数表明时区，UTC代表东八区。

第二个方案是直接去修改数据库，输入

```sql
set global time_zone='+8:00';
```

​

​