---
layout: post
title: Spring Gateway Filter 回调顺序
date: 2025-03-09
categories: code
tags: Java SpringCloud
typora-root-url: ../../victorchow.github.io
---

>   多个 Filter 的 doOnSuccess、doOnError 和 doFinally 执行顺序

在 Spring Cloud Gateway 中，多个 Filter 组成链时，doOnSuccess、doOnError 和 doFinally 的执行顺序由 Filter 嵌套和 Reactor 行为决定

## 执行顺序

假设 Filter 链为 **Filter1 -> Filter2 -> ... -> FilterN -> 代理服务**：

### 成功完成

- **doOnSuccess**：从 FilterN 到 Filter1（内向外）
- **doFinally**：从 Filter1 到 FilterN（外向内）

**示例（FilterA -> FilterB）**：

1. FilterB doOnSuccess
2. FilterA doOnSuccess
3. FilterA doFinally
4. FilterB doFinally

### 错误完成

- **doOnError**：从 FilterN 到 Filter1（内向外）
- **doFinally**：从 Filter1 到 FilterN（外向内）

**示例（FilterA -> FilterB）**：

1. FilterB doOnError
2. FilterA doOnError
3. FilterA doFinally
4. FilterB doFinally

## 验证样例

```java
public class MonoFilterChainTest {
    public static void main(String[] args) {
        testSuccessCase();
        testErrorCase();
    }

    static void testSuccessCase() {
        Mono<Void> proxy = Mono.empty()
            .doOnSuccess(v -> System.out.println("FilterB doOnSuccess"))
            .doFinally(s -> System.out.println("FilterB doFinally"))
            .doOnSuccess(v -> System.out.println("FilterA doOnSuccess"))
            .doFinally(s -> System.out.println("FilterA doFinally"));
        proxy.subscribe();
    }

    static void testErrorCase() {
        Mono<Void> proxy = Mono.error(new RuntimeException("Error"))
            .doOnError(e -> System.out.println("FilterB doOnError"))
            .doFinally(s -> System.out.println("FilterB doFinally"))
            .doOnError(e -> System.out.println("FilterA doOnError"))
            .doFinally(s -> System.out.println("FilterA doFinally"));
        proxy.subscribe();
    }
}
```

### 输出

- **成功**：FilterB doOnSuccess -> FilterA doOnSuccess -> FilterA doFinally -> FilterB doFinally
- **错误**：FilterB doOnError -> FilterA doOnError -> FilterA doFinally -> FilterB doFinally

## 总结

- 成功时：doOnSuccess 内向外，doFinally 外向内
- 错误时：doOnError 内向外，doFinally 外向内
