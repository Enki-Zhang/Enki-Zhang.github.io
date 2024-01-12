<!-- ---
title: JUC源码学习系列02
top: false
cover: false
toc: true
mathjax: true
date: 2024-01-02 20:13:46
password:
summary:
tags:
categories:
--- -->

# ReentrantLock 学习

ReentrantLock 最特别的点在于他的可重入特性，允许同一个线程多次获得同一个锁，而不被阻塞。
内部维护了表示锁占用状态的 state 变量，当同一个线程来获取锁时，state 会+1 并继续获得锁，以此达到锁可重入效果，同时若获取失败进入到 CLH 队列中等待，ReentrantLock 释放锁的次数和重入锁的次数相同才能够释放。

## 内部类

- Sync
  Sync 类在 ReentrantLock 内部负责实现具体的锁逻辑，提供了对锁的获取、释放、判断、持有线程的管理等方法

```java
abstract static class Sync extends AbstractQueuedSynchronizer {
        private static final long serialVersionUID = -5179523762034025860L;

        /**
         * Performs {@link Lock#lock}. The main reason for subclassing
         * is to allow fast path for nonfair version.
         */
        abstract void lock();

        /**
         * Performs non-fair tryLock.  tryAcquire is implemented in
         * subclasses, but both need nonfair try for trylock method.
         * 获取非公平锁
         * acquires 表示尝试获取锁的次数。
         */
        final boolean nonfairTryAcquire(int acquires) {
            //当前线程
            final Thread current = Thread.currentThread();
            //当前锁被持有的次数
            int c = getState();
            //尝试通过 compareAndSetState 设置锁状态为 acquires 并设置当前线程为锁的独占线程
            if (c == 0) {
                if (compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            //当前线程==已经拥有锁的线程
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0) // overflow
                    throw new Error("Maximum lock count exceeded");
                    //设置当前锁持有的次数
                setState(nextc);
                return true;
            }
            return false;
        }
/**
 * 释放锁
 * */
        protected final boolean tryRelease(int releases) {
            //减去持有的次数
            int c = getState() - releases;
            //当前线程非占有锁的线程
            if (Thread.currentThread() != getExclusiveOwnerThread())
                throw new IllegalMonitorStateException();
            boolean free = false;
            //当前线程state为0 即可释放锁
            if (c == 0) {
                free = true;
                setExclusiveOwnerThread(null);
            }
            //刷新锁的持有次数
            setState(c);
            return free;
        }
// 判断当前线程是否持有锁。
        protected final boolean isHeldExclusively() {
            // While we must in general read state before owner,
            // we don't need to do so to check if current thread is owner
            return getExclusiveOwnerThread() == Thread.currentThread();
        }
//返回与当前锁相关联的条件对象。
        final ConditionObject newCondition() {
            return new ConditionObject();
        }

        // Methods relayed from outer class
//返回持有锁的线程
        final Thread getOwner() {
            return getState() == 0 ? null : getExclusiveOwnerThread();
        }
//获取当前线程持有该锁的计数。
        final int getHoldCount() {
            return isHeldExclusively() ? getState() : 0;
        }
//判断锁是否被任何线程持有
        final boolean isLocked() {
            return getState() != 0;
        }

        /**
         * Reconstitutes the instance from a stream (that is, deserializes it).
         */
        private void readObject(java.io.ObjectInputStream s)
            throws java.io.IOException, ClassNotFoundException {
            s.defaultReadObject();
            setState(0); // reset to unlocked state
        }
    }
```

- NonfairSync

```java
 static final class NonfairSync extends Sync {
        private static final long serialVersionUID = 7316153563782823691L;

        /**
         * Performs lock.  Try immediate barge, backing up to normal
         * acquire on failure.
         */
        final void lock() {
            //当锁空闲是，进行获取锁，并将当前线程设为独占
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
            //获取锁失败，按照同步队列的机制进行等待获取锁
                acquire(1);
        }
//调用Sync中的非公平方式获取锁
        protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }
    }
```

> 每次获取锁的尝试，并不按照等待队列的顺序进行，先尝试，尝试失败在排队。

- FairSyn

```java
 static final class FairSync extends Sync {
        private static final long serialVersionUID = -3000897897090466540L;

        final void lock() {
            acquire(1);
        }

        /**
         * Fair version of tryAcquire.  Don't grant access unless
         * recursive call or no waiters or is first.
         */
        protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                //锁空闲，先问一下是不是有人排队，没有则获取，有继续排队
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                        //没人排队等候，自己持有并独占
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            //当前线程已经拿到锁，state次数累加
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
    }
```

- 构造函数

```java
//默认为非公平锁
 public ReentrantLock() {
        sync = new NonfairSync();
    }

//参数为true 设置为公平锁
    public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```
