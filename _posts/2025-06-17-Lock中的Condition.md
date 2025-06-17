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

### 工作原理
1. **线程 A 获取锁**  
   - 线程 A 调用 `lock.lock()`，成功获取锁，此时其他线程无法获取该锁，因为 ReentrantLock 是互斥的

2. **线程 A 调用 `condition.await()`**  
   - 在持有锁的情况下，线程 A 调用 `condition.await()`
   - `await()` 的行为是 **原子性地释放锁**，并将线程 A 设置为等待状态
   - 这意味着锁在 `await()` 调用时被释放，其他线程现在可以竞争获取这个锁

3. **线程 B 获取锁**  
   - 因为线程 A 释放了锁，线程 B 可以调用 `lock.lock()` 获取锁
   - 获取锁后，线程 B 可以修改共享状态，然后调用 `condition.signal()` 或 `condition.signalAll()` 来唤醒等待的线程（例如线程 A）

4. **线程 B 调用 `condition.signal()`**  
   - 调用 `signal()` 会通知线程 A 从等待状态转为就绪状态，但线程 A **并不会立即执行**
   - 线程 A 需要等待线程 B 释放锁，然后重新竞争获取锁，只有在成功重新获取锁后，线程 A 才会从 `await()` 返回并继续执行

5. **线程 B 释放锁，线程 A 恢复**  
   - 线程 B 在完成操作后调用 `lock.unlock()` 释放锁
   - 线程 A 重新获取锁，然后从 `await()` 开始，继续执行后续代码

## 注意

所有操作必须在 `lock()` 和 `unlock()` 之间进行，否则会报错 IllegalMonitorStateException



