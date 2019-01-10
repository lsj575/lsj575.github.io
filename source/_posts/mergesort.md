---
title: 归并排序
date: 2018-09-24 21:55:02
categories: 算法
tags: [算法, 排序]
description: 归并排序的Go语言实现版本
---

​        归并排序（MERGE-SORT）是建立在归并操作上的一种有效的排序算法,该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为二路归并。

<!--more-->

- Go代码（从小到大排序）:

  ```go
  func mergeSort(arr []int, l int, r int)  {
      // 递归到最后的返回条件
      if l >= r {    // 当片段的最左索引超过最右索引
          return
      }

      mid := (l + r) / 2
      mergeSort(arr, l, mid)
      mergeSort(arr, mid+1, r)
      // 简单优化，若左片段的最大值已经小于右片段的最小值，表明此时已经为有序
      if arr[mid] > arr[mid+1]{
          merge(arr, l, mid, r)
      }
  }

  func merge(arr []int, l int, mid int, r int)  {
      var aux = make([]int, r-l+1)
      // 创建一个临时数组，存放当前待排序的片段
      for i := l; i <= r; i++ {
          aux[i-l] = arr[i]
      }

      i := l
      j := mid + 1
      // 合并两个片段
      for k := l; k <= r; k++ {
          if i > mid {
              arr[k] = aux[j-l]
              j++
          } else if j > r {
              arr[k] = aux[i-l]
              i++
          } else if aux[i-l] < aux[j-l] {
              arr[k] = aux[i-l]
              i++
          } else if aux[i-l] > aux[j-l] {
              arr[k] = aux[j-l]
              j++
          }
      }
  }
  ```

  ​