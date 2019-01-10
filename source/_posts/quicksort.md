---
title: 快速排序
date: 2018-09-24 22:17:02
categories: 算法
tags: [算法, 排序]
description: 快速排序的Go语言实现
---

​        快速排序（Quicksort）是对冒泡排序的一种改进。

​        快速排序由C. A. R. Hoare在1962年提出。它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

<!--more-->

- Go代码（从小到大）

  ```go
  // 交换两个数的位置
  func swap(i *int, j *int) {
      temp := *i
      *i = *j
      *j = temp
  }

  // 将片段分为大于v和小于v的两部分，并将两部分的分界索引值返回
  func Partition(arr []int, l int, r int) int {
      v := arr[l]

      j := l
      for i := l + 1; i <= r; i++ {
          if arr[i] < v {
              swap(&arr[j+1], &arr[i])
              j++
          }
      }
      swap(&arr[l], &arr[j])
      return j
  }

  // 递归
  func Sort(arr []int, l int, r int)  {
      if l >= r {
          return
      }

      p := Partition(arr, l, r)
      Sort(arr, l, p)
      Sort(arr, p+1, r)
  }
  ```

  ​