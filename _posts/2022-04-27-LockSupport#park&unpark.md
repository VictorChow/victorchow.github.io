---
layout: post
title: LockSupport 的 park、unpark
date: 2022-04-27
categories: code
tags: Java
typora-root-url: ../../victorchow.github.io
---

> 实现线程挂起和唤醒，并对比 Object#wait()、Thread#sleep()

在 AQS 或者 BlockingQueue 中常用的实现线程挂起和唤醒的方法就是 LockSupport 的 park 和 unpark 方法

## park

LockSupport 类中提供的 park 相关方法有 6 个，根据是否指定 blocker 分为两类，指定 blocker 的优点在于排查问题的时候能够知道 park 的原因，所以推荐使用带有 blocker 的 park 方法

```java
//不带blocker的方法
public static void park();
public static void parkNanos(long nanos);
public static void parkUntil(long deadline);
//带blocker的方法
public static void park(Object blocker);
public static void parkNanos(Object blocker, long nanos);
public static void parkUntil(Object blocker, long deadline);
```

* ### park(blocker)

  对当前线程执行阻塞操作，直到获取到可用许可后才解除阻塞

  ```java
  LockSupport.park(BLOCKER);
  ```

  利用 jstack 查看线程状态，可见当前线程状态处为 **WAITING**，同时 `parking to wait for` 我们传入的 blocker 对象，如果使用了不指定 blocker 的 park 方法，则不显示该行信息：

  ```
  "main" #1 prio=5 os_prio=31 cpu=106.83ms elapsed=15.68s tid=0x00007f93b9008800 nid=0x2603 waiting on condition  [0x000070000d65b000]
     java.lang.Thread.State: WAITING (parking)
  	at jdk.internal.misc.Unsafe.park(java.base@17.0.2/Native Method)
  	- parking to wait for  <0x0000000787e93ba0> (a java.lang.Object)
  	at java.util.concurrent.locks.LockSupport.park(java.base@17.0.2/LockSupport.java:211)
  ```

  令线程唤醒的方式常用的有两种：

  * 使用 unpark 方法

    ```java
    LockSupport.unpark(thread);
    ```

  * 调用 park 线程的 `interrupt()` 方法

    ```java
    thread.interrupt();
    ```

* ### parkNanos(blocker, nanos)

  挂起当前线程，过期时间到了会恢复，注意 nanos 时间单位为纳秒（1 ms = 1,000,000 ns）

* ### parkUntil(blocker,  deadline)

  挂起当前线程，指定的 deadline 到了会恢复，注意 nanos 时间单位为纳秒（1 ms = 1,000,000 ns）

## unpark

将指定线程的许可置为可用，用于唤醒该线程

```java
LockSupport.unpark(thread);
```

线程调用 park 时其实就是去获取许可，如果能成功获取到许可则能够往下执行，否则阻塞直到成功获取许可为止，而当线程调用 unpark 时则是释放许可，供线程去获取，park、unpark 方式的执行顺序不影响唤醒，也不会报错，也就是说可以先调用 unpark 再调用 park，此时的 park 不会阻塞：

```java
LockSupport.unpark(Thread.currentThread());
LockSupport.park(BLOCKER);
```

对于这个许可，仅仅是「有」和「无」的区别，多次调用 unpark 并不会累加许可，park 方法会等待许可并释放掉许可，注意下面两个场景：

```java
//先调用unpark两次，再调用park，线程不会阻塞
LockSupport.unpark(Thread.currentThread());
LockSupport.unpark(Thread.currentThread());
LockSupport.park(BLOCKER);
```

```java
//先调用unpark两次，此时许可不会累加，仅表示有许可
LockSupport.unpark(Thread.currentThread());
LockSupport.unpark(Thread.currentThread());
//这里不会阻塞，会释放许可
LockSupport.park(BLOCKER);
//这里会阻塞，因为许可被上一行的park释放了，许可从「有」变成了「无」
LockSupport.park(BLOCKER);
```

## 对比

* ### Object#wait()

  - `Object.wait()` 方法需要在 synchronized 块中执行，`LockSupport.park()` 可以在任意地方执行
  - `Object.wait()` 方法声明抛出了中断异常，调用者需要捕获或者再抛出，`LockSupport.park()` 不需要捕获中断异常
  - `Object.wait()` 没有设置超时时需要另一个线程执行 `notify()` 来唤醒，但不一定继续执行后面逻辑（notify 随机唤醒一个等待池中的对象进入锁池、notifyAll 唤醒等待池中的所有线程进入锁池），`LockSupport.park()` 没有设置超时时需要另一个线程执行 `unpark()` 或者 `interrupt()` 来唤醒，一定会继续执行后面逻辑
  - `Object.wait()` 之前如果执行 `notify()` 会抛出 **IllegalMonitorStateException** 异常，而 park 和 unpark 不会抛异常

* ### Thread#sleep()

  * `Thread.sleep()` 和 `LockSupport.park()` 都是阻塞当前线程的执行，且都不会释放当前线程占有的锁资源

  * `Thread.sleep()` 无法从外部唤醒，只能等待过期时间到，`LockSupport.park()` 方法可以被另一个线程调用 `LockSupport.unpark()` 方法唤醒

  * `Thread.sleep()` 方法声明上抛出了中断异常，所以需要处理 **InterruptedException**，`LockSupport.park()` 方法声明上没有抛出异常，不需要处理
