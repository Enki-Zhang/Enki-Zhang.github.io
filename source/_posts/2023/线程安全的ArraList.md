---
title: 线程安全的ArrayList
top: false
cover: false
toc: true
mathjax: true
date: 2023-06-07 15:05:25
password:
summary:
tags:
categories:
---

## ArrayList 线程安全的方法

1. 使用 Vector 替代
2. 使用 CopyOnWriteArrayList 替代
3. 使⽤ Collections.synchronizedList 包装 ArrayList，然后操作包装后的 list
4. 在使⽤ ArrayList 时，应⽤程序通过同步机制去控制 ArrayList 的读写

## CopyOnWriteArrayList(写时复制数组)

修改：当有新数据添加后，将当前数组进行复制到另一个数组后，在新数组上进行元素操作，完成操作后用新数组替换旧数组。
当有多个线程来读取时，操作的是旧的数组不会产生线程安全现象。
缺陷：性能缺陷存在于数组的复制和覆盖，适合于读多写少的情况
