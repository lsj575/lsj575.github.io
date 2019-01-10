---
title: PHP链式操作
date: 2018-10-22 08:53:28
categories: PHP
tags: [PHP]
---

​        我们经常会在各种的PHP框架中看到链式操作，最常见的是在对数据库的操作中

<!-- more -->

```php
$result = $this->table($table)
    ->alias('fd')
    ->join('user u', 'u.id = fd.user_id')
    ->order($order)
    ->paginate();
```

​        那么它是如何实现的呢？

​        首先我们自己建一个database类

```php
class Database
{
    function where($where)
    {
      
    }
	
    function order($order)
    {
      
    }
  
    function limit($limit)
    {
      
    }
}
```

​        如果我们要查数据库的话，就得这么写

```php
$db = new Database();
$db->where("id = 1");
$db->order("id desc");
$db->limit(10)
```

​        可以看到这样写特别麻烦，要分成多行，所以我们选择采用链式操作。

​        将Database类进行修改

```php
class Database
{
	function where($where)
    {
        return $this;
    }
	
  	function order($order)
    {
        return $this;
    }
  
  	function limit($limit)
    {
        return $this;
    }
}
```

​        每个方法返回都$this，就可以使用链式操作了。

```php
$db = new Database();
$db->where("id = 1")->order("id desc")->limit(10);
```

