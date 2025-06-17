---
layout: post
title: Java Lock接口中的 Condition 使用
date: 2025-06-17
categories: code
tags: Java
typora-root-url: ../../victorchow.github.io
---

>   Condition 是 Java Lock 接口中的一个重要组件，用于线程间的协调和通信

# Java Lock 接口中的 Condition 使用

## 基本概念
- **创建方式**：通过 `Lock` 接口的 `newCondition()` 方法创建 `Condition` 对象
- **核心方法**：
  - `await()`：使当前线程等待，并释放锁，直到被唤醒
  - `signal()`：唤醒一个等待的线程
  - `signalAll()`：唤醒所有等待的线程

## 使用场景
`Condition` 常用于以下场景：

- **生产者-消费者模式**：实现数据的生产和消费同步
- **线程间协作**：控制多个线程的执行顺序或状态

## 代码示例
简单的生产者-消费者示例：

```java
public class ProducerConsumerExample {
    private final Lock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private boolean dataAvailable = false;

    public void produce() {
        lock.lock();
        try {
            System.out.println("Producer: Producing data...");
            dataAvailable = true;
            condition.signalAll(); // 唤醒所有等待的消费者
            System.out.println("Producer: Data produced and signaled.");
        } finally {
            lock.unlock();
        }
    }

    public void consume() throws InterruptedException {
        lock.lock();
        try {
            while (!dataAvailable) {
                System.out.println("Consumer: Waiting for data...");
                condition.await(); // 等待数据
                System.out.println("Consumer: Awoken from wait.");
            }
            System.out.println("Consumer: Consuming data...");
            dataAvailable = false;
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        ProducerConsumerExample example = new ProducerConsumerExample();

        // 消费者线程
        new Thread(() -> {
            try {
                example.consume();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }).start();

        // 生产者线程
        new Thread(() -> {
            example.produce();
        }).start();
    }
}
```

## 日志
```
Consumer: Waiting for data...
Producer: Producing data...
Producer: Data produced and signaled.
Consumer: Awoken from wait.
Consumer: Consuming data...
```

## 代码解析

### consume 方法

- **获取锁**：通过 `lock.lock()` 确保线程安全
- **检查条件**：使用 `while (!dataAvailable)` 循环检查数据是否可用
- **等待数据**：若数据不可用，打印 `"Consumer: Waiting for data..."`，调用 `condition.await()` **释放锁**并等待
- **被唤醒**：被 `signalAll()` 唤醒（需要重新获取锁），打印 `"Consumer: Awoken from wait."`
- **消费数据**：打印 `"Consumer: Consuming data..."`，并将 `dataAvailable` 置为 `false`
- **释放锁**：在 `finally` 块中调用 `lock.unlock()`

### produce 方法

- **获取锁**：通过 `lock.lock()` 确保线程安全
- **生产数据**：打印日志 `"Producer: Producing data..."`，并将 `dataAvailable` 置为 `true`。
- **唤醒消费者**：调用 `condition.signalAll()` 唤醒所有等待的线程（真正尝试唤醒是在 unlock 之后），并打印 `"Producer: Data produced and signaled."`
- **释放锁**：在 `finally` 块中调用 `lock.unlock()`

## 注意

所有操作必须在 `lock()` 和 `unlock()` 之间进行，否则会报错 IllegalMonitorStateException



