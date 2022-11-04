---
title: javaIO流
top: false
cover: false
toc: true
mathjax: true
date: 2022-10-12 15:09:30
password:
summary:
tags:
categories:
---

### 绝对路径和相对路径

绝对路径：从盘符开始的完整路径
相对路径：相对于你当前的项目目录的路径，在开发中常用此路径

> 1、路径是不区分大小写
> 2、路径中的文件名称分隔符 windows 使用反斜杠,反斜杠是转义字符,两个反斜杠代表一个普通的反斜杠

  <!-- more -->

## IO 流

以内存为中心
数据流入内存为输入流（InputStream），数据流出内存为输出流（outputStream）
以内存动作为中心，内存输入为读 read，内存输出为写 write
根据数据的类型分为：字节流 和 字符流。

- 字节流：适合读取 视频 声音 图片等二进制文件 。java 中的所有字节流都以 stream 结尾 字节流读取文本会出现乱码情况

* 字符流适合纯文本文件所有字符流都含有 Reader 或者 Writer
  复制图片 字符流 = 字节流 + 编码表

````java
//        复制图片
//        指定数据源
        FileInputStream fileInputStream = new FileInputStream("E:\\file.png");
//        复制目标地址
        FileOutputStream fileOutputStream = new FileOutputStream("E:\\fileCopy.png");
//        读数据
        byte[] bytes = new byte[1024];
        int len;
//        读取
        while ((len = fileInputStream.read(bytes))!=-1){
            fileOutputStream.write(bytes,0,len);
        }
        fileInputStream.close();
        fileOutputStream.close();
    }
```

````
