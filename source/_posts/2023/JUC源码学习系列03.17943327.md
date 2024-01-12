<!-- ---
title: JUC源码学习系列03
top: false
cover: false
toc: true
mathjax: true
date: 2024-01-03 10:59:06
password:
summary:
tags:
categories:
--- -->

# Synchronized

- 什么是 Synchronized：这是 Java 控制多线程访问共享资源的一种方式，是线程之间通信的一种方式。

- 什么同步代码块：同步代码块用于阻止多个线程同时对同一方法中的部分代码进行访问。
  理解起来，可以看成同步代码块相当于一个保护共享资源的一块区域，这个区域需要有特定身份的线程才能够进行访问，而这个身份便是锁。

- 什么是锁：锁是一个概念，Java 中任何对象都能够成为一把锁，且只要获得了被同步代码块认可的锁即可访问同步代码块中的共享资源。

一般而言 Synchronized 的形式为

```java
Synchronized(锁){
    //同步代码块
    //这里访问共享资源
}
```

总体而言，Synchronized 修改的地方分为方法和代码块上：
修饰代码块，我们知道锁定的便是代码块中的内容。
修饰方法，则可以分为修饰的是静态方法还是普通方法：

1. 静态方法：静态方法属于类，其锁住的便是整个 Class 对象。只要是同一个类便是同一个锁
2. 普通方法：锁住的是方法调用该方法的对象。只要是同一个类下的同一个方法实例便是同一个锁

```java
 public  void function() {
        synchronized(this) {
          //同步资源
        }
    }

public synchronized void function(){
//同步资源
}
```

这里的两种方法的锁都是当前的方法实例，不同在于方法内部的中用代码块形式，并用 this 表示锁。

> 这里简单回忆一下 this 在 Java 中的作用：
> this 一般而言表示对象的引用，this 一般用在方法中和代码块里，或者匿名内部类以及 lambda 表达式里。

```java
class SyncThread extends Thread {
    int count = 0;//作为共享资源进行访问；

    SyncThread() {

    }

    @Override
    public void run() {
        synchronized (this) {
            for (int i = 0; i < 3; i++) {
                try {
                    count++;
                    System.out.println("当前线程" + Thread.currentThread() + "start.." + "共享资源： " + count);
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) {
        SyncThread syncThread = new SyncThread();
        //使用的是同一个类下的同一个方法实例，即锁是同一个
        Thread thread = new Thread(syncThread, "t1");
        Thread thread1 = new Thread(syncThread, "t2");
        thread1.start();
        thread.start();
    }
}

//结果
当前线程Thread[t2,5,main]start..共享资源： 1
当前线程Thread[t2,5,main]start..共享资源： 2
当前线程Thread[t2,5,main]start..共享资源： 3
当前线程Thread[t1,5,main]start..共享资源： 4
当前线程Thread[t1,5,main]start..共享资源： 5
当前线程Thread[t1,5,main]start..共享资源： 6

```

```java
class SyncThread extends Thread {
    int count = 0;//作为共享资源进行访问；

    SyncThread() {

    }

    @Override
    public void run() {
        synchronized (this) {
            for (int i = 0; i < 3; i++) {
                try {
                    count++;
                    System.out.println("当前线程" + Thread.currentThread() + "start.." + "共享资源： " + count);
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) {
        SyncThread syncThread = new SyncThread();
        //使用的是不同对象下的同名方法实例，那么锁是不一样的
        Thread thread = new Thread(new SyncThread(), "t1");
        Thread thread1 = new Thread(syncThread, "t2");
        thread1.start();
        thread.start();
    }
}

//结果
当前线程Thread[t2,5,main]start..共享资源： 1
当前线程Thread[t1,5,main]start..共享资源： 1
当前线程Thread[t1,5,main]start..共享资源： 2
当前线程Thread[t2,5,main]start..共享资源： 2
当前线程Thread[t2,5,main]start..共享资源： 3
当前线程Thread[t1,5,main]start..共享资源： 3

```

- 对 Class 对象的锁

```java
class SyncThread extends Thread {
   static int count = 0;//作为共享资源进行访问；

    SyncThread() {

    }

    @Override
    public  void run() {
        synchronized(SyncThread.class){
            for (int i = 0; i < 3; i++) {
                try {
                    count++;
                    System.out.println("当前线程" + Thread.currentThread() + "start.." + "共享资源： " + count);
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) {
        SyncThread syncThread1 = new SyncThread();
//        SyncThread syncThread2 = new SyncThread();
        Thread thread1 = new Thread( new SyncThread(), "t1");
        Thread thread2 = new Thread(syncThread1, "t2");
        thread1.start();
        thread2.start();
    }
}

//结果
当前线程Thread[t1,5,main]start..共享资源： 2
当前线程Thread[t1,5,main]start..共享资源： 3
当前线程Thread[t2,5,main]start..共享资源： 4
当前线程Thread[t2,5,main]start..共享资源： 5
当前线程Thread[t2,5,main]start..共享资源： 6

```

> 类锁实际上是通过对象锁实现的，即类的 Class 对象锁。每个类只有一个 Class 对象，所以每个类只有一个类锁。

- 什么时候释放锁：
  对于 Synchronized 而言，当 synchronized 关键字保护的代码块执行完成时，锁会自动释放
