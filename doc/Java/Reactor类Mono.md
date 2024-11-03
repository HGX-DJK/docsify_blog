
**Mono** 是 Reactor 项目中的一个类，用于表示异步单值操作。Reactor 是一个响应式编程库，广泛应用于 Java 中的异步编程和非阻塞 I/O 操作。Mono 可以类比为一个可能（或将来）包含零个或一个值的异步计算结果。与 Flux（另一个 Reactor 类，表示多个值的异步流）不同，Mono 专注于单值或空结果的处理。下面详细讲解 Mono 的用法和常见操作。
**1. 创建 Mono**

* 使用 just

```java
Mono<string> mono = Mono.just("Hello，world")
```

just 方法用于创建一个包含指定数据的 Mono。

*使用 empty

```java
Mono<string> mono = Mono.empty()
```

empty 方法用于创建一个不包含任何数据的 Mono。

* 使用 fromCallable

```java
Mono<String> mono = Mono.fromCallable(() -> {
    // 你的计算或方法调用
    return "Result";
});
```

**fromCallable** 方法允许你传入一个 Callable，它会在订阅时被调用并返回一个值。

* 使用 create

```java
Mono<String> mono = Mono.create(sink -> {
    // 你的异步计算逻辑
    sink.success("Result");
    // 或者在发生错误时调用
    // sink.error(new RuntimeException("Error"));
});
```

create 方法允许你使用一个回调来异步产生一个值或错误。
**2. 操作 Mono**

* map

```java
Mono<String> mono = Mono.just("Hello")
                        .map(value -> value + ", World!");
```

map 用于同步地转换 Mono 中包含的值。

* flatMap

```java
Mono<String> mono = Mono.just("Hello")
                        .flatMap(value -> Mono.just(value + ", World!"));
```

flatMap 用于异步地转换 Mono 中包含的值，它返回一个新的 Mono。

* filter

```java
Mono<String> mono = Mono.just("Hello")
                        .filter(value -> value.length() > 3);

```

filter 用于根据一个谓词函数对 Mono 中的值进行筛选，如果值不符合条件，将返回一个空的 Mono。

* doOnNext

```java
Mono<String> mono = Mono.just("Hello")
                        .doOnNext(value -> {
                            // 处理值
                            System.out.println("Value: " + value);
                        });

```

doOnNext 用于在每次有值发出时执行一个回调操作。
**3. 订阅 Mono**

* subscribe

```java
Mono<String> mono = Mono.just("Hello");
mono.subscribe(value -> {
    System.out.println("Received: " + value);
});

```

subscribe 方法用于开始处理 Mono 并定义当有值发出时该如何处理。

* 带错误处理的 subscribe

```java
mono.subscribe(
    value -> {
        System.out.println("Received: " + value);
    },
    error -> {
        System.err.println("Error: " + error);
    }
);

```

* 带完成处理的 subscribe

```java
mono.subscribe(
    value -> {
        System.out.println("Received: " + value);
    },
    error -> {
        System.err.println("Error: " + error);
    },
    () -> {
        System.out.println("Completed");
    }
);

```

**4. 组合操作**

* then

```java
Mono<Void> result = Mono.just("Hello")
                        .then();
result.subscribe(() -> {
    System.out.println("Completed");
});

```

then 方法用于在当前 Mono 完成后执行一个新的操作，而忽略当前 Mono 的值

* zipWith

```java
Mono<String> mono1 = Mono.just("Hello");
Mono<String> mono2 = Mono.just("World");

Mono<String> result = mono1.zipWith(mono2, (v1, v2) -> v1 + ", " + v2);
result.subscribe(System.out::println);

```

zipWith 用于组合两个 Mono 的值。
