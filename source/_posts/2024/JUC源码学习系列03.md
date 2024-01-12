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

## synchronized 原理

synchronized 的锁存放于对象头中，其中的 Mark word 标记字段记录了锁的变化，Mark Word 存储对象的 HashCode 分代年龄 锁标志位。

- 加锁和解锁的过程
  通过锁监视器，同一个对象同一时间只与一个 monitor 相关联，当对象尝试获得锁时，monitorenter 会将 monitor 计数器+1，表示占有锁，其他线程在想要需要等待。
  如果该线程继续重入锁，则计数器继续+1.
  锁释放：进过 monitorexit 每次将计数器-1，一直减到 0 表示该线程释放锁。
  如果其他线程获取锁失败，会进去到同步队列进行阻塞，一直到锁被释放才能够尝试获取锁。
  ![Alt text](image-37.png)
  对象监视器，同步队列以及执行线程状态之间的关系
- 可重入锁
  统一进程再次获取相同锁的时候不会因为锁未释放而阻塞，会自动获取。

### synchronized 锁升级过程

总结而言： 偏向锁通过对比 Mark Word 解决加锁问题，避免执行 CAS 操作。而轻量级锁是通过用 CAS 操作和自旋来解决加锁问题，避免线程阻塞和唤醒而影响性能。重量级锁是将除了拥有锁的线程以外的线程都阻塞。

jdk1.6 之前的 synchronized 基于进入和退出 monitor 对象来同步代码块。这种锁称为重量级锁，为了提升获得锁和释放锁带来的性能增加了“偏向锁”和“轻量级锁”，锁随着竞争关系不断升级，同时这种锁升级却不能降级。

1. 偏向锁
   当同步代码块中只有一个线程在访问，不存在竞争的情况下，会使用偏向锁来偏向于第一个访问锁的进程，从而达到减少锁释放和获取所消耗的性能。
   偏向锁获取过程如下：

   1. 第一次访问，当前线程进入到同步代码块获得锁时，在对象头和栈帧中存储线程 ID 并设置锁标志位为偏向锁，以后带有相同线程 ID 的线程尝试获取锁时不需要进行加锁和解锁的过程。

   2. 进程访问同步代码块获取锁时，判断对象头中的标志位是否是偏向锁，且线程 ID 是否指向当前线程。若是则直接执行同步代码，否则若线程 ID 未指向当前线程，通过 CAS 竞争将 mark word 中的线程 Id 设置为当前线程之后，指向同步代码块，若竞争失败，会在全局安全点即（在这个时间点上没有字节码正在执行）上对进程进行挂起，偏向锁升级为轻量级锁。

   > 1. 偏向锁的释放，只在其他线程尝试竞争时才会释放。适用于无竞争状态
   >    竞争过程偏向锁是在无锁争用的情况下使用的，也就是同步开在当前线程没有执行完之前，没有其它线程会执行该同步块，一旦有了第二个线程的争用，偏向锁就会升级为轻量级锁，如果轻量级锁自旋到达阈值后，没有获取到锁，就会升级为重量级锁；(旋转的阈值在 1.6 之后引入了自适应阈值，并不是固定的)
   > 2. 这里的 CAS 竞争：它会尝试原子性地将对象头的锁状态从无锁（或其他状态）修改为自己的线程 ID，尝试获取锁的过程中可能会涉及多次循环尝试，直到成功获取锁。如果 CAS 竞争失败，代表当前线程无法直接获得偏向锁，因为有其他线程尝试获取同一个对象的锁

2. 当偏向锁有竞争时会升级为轻量级锁。当进程进入同步代码块中 JVM 在当前线程栈帧中创建存储锁记录的空间用于拷贝对象头的 mark word, 之后使用 CAS 将对象头中的 mark word 替换为指向锁记录的指针，若成功则获得轻量级锁，若失败则表示有竞争继续 cas 自旋操作。
   轻量级锁在释放时将锁记录中的 Mark word 替换会对象头，如果成功表示没有竞争, 失败则表示存在竞争需要进行锁膨胀为重量级锁

> 轻量级锁通过 CAS 自旋获取锁，避免了竞争过程中线程的阻塞，提高了执行效率。但自旋也会消耗 cpu。

### synchronized 和 volatile 有什么区别

synchronized 可以用在方法和代码块上，多用于实现多线程之间的同步，volatile 只能修饰变量，多用于解决变量之间的可见性。volatile 是轻量级的同步实现，能够保证数据的可见性 和 禁止指令重排序不能保证原子性 sys 可以保证原子性和可见性

> 参考：
>
> 1. https://segmentfault.com/a/1190000015979202
> 2. chatGPT
