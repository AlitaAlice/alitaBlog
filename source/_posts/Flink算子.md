---
title: Flink算子
date: 2023-04-30 20:32:11
tags:
categories:
---

## 概述

> 本章介绍

<!--more-->

## 正文

在 Flink 中，算子（Operator）是用于对数据流进行处理和转换的基本构建模块。算子接收输入数据流，对数据执行特定的操作（如过滤、转换、聚合等），然后将结果输出到下游算子。Flink 的算子可以连接起来形成一个数据处理流水线，从而构建复杂的流式计算任务。

以下是 Flink 中常见的一些算子及其示例：

1. map 算子：
`map` 算子用于对输入数据流中的每个元素进行转换。例如，下面的代码将输入数据流中的每个整数值加倍：

```java
DataStream<Integer> input = ...
DataStream<Integer> doubled = input.map(new MapFunction<Integer, Integer>() {
    @Override
    public Integer map(Integer value) {
        return value * 2;
    }
});
```

2. flatMap 算子：
`flatMap` 算子用于将输入数据流中的每个元素转换为零个、一个或多个输出元素。例如，下面的代码将输入数据流中的每个字符串根据空格拆分为多个单词：

```java
DataStream<String> input = ...
DataStream<String> words = input.flatMap(new FlatMapFunction<String, String>() {
    @Override
    public void flatMap(String value, Collector<String> out) {
        for (String word : value.split(" ")) {
            out.collect(word);
        }
    }
});
```

3. filter 算子：
`filter` 算子用于根据指定的条件过滤输入数据流中的元素。例如，下面的代码将输入数据流中的偶数值过滤出来：

```java
DataStream<Integer> input = ...
DataStream<Integer> evenNumbers = input.filter(new FilterFunction<Integer>() {
    @Override
    public boolean filter(Integer value) {
        return value % 2 == 0;
    }
});
```

4. keyBy 算子：
`keyBy` 算子用于将输入数据流按照指定的键进行分组，以便在下游算子中执行基于键的操作（如聚合或连接）。例如，下面的代码将输入数据流中的单词按照单词本身进行分组：

```java
DataStream<Tuple2<String, Integer>> input = ...
KeyedStream<Tuple2<String, Integer>> keyed = input.keyBy(tuple -> tuple.f0);
```

5. `min` 和 `minBy` 之间的区别。

  `min` 和 `minBy` 确实都是用来找到集合中最小值的元素。但是，它们在处理多个字段或属性的数据结构时有所不同。具体来说：

  1. `min`：在一个包含多个字段或属性的数据结构中，`min` 操作会根据用户指定的字段查找最小值。然后，它会返回一个包含该最小值的元素，但是其他字段的值可能会是错误的，因为 `min` 操作只关心指定的字段。实际上，这可能会导致返回的元素在原始数据集中不存在。

  2. `minBy`：与 `min` 操作类似，`minBy` 也会根据用户指定的字段查找最小值。然而，不同之处在于 `minBy` 会返回包含最小值的完整元素，这意味着其他字段的值也是正确的。因此，`minBy` 操作在需要保留元素中其他字段信息的情况下更为有用。

  以下是一个简单的示例，说明了这两种操作的区别：

  假设您有以下数据集：

| ID   | Value |
| ---- | ----- |
| 1    | 5     |
| 2    | 3     |
| 3    | 7     |

  - 使用 `min(Value)` 操作：您将查找 `Value` 字段的最小值。结果可能是 `{ID: ?, Value: 3}`，其中 `ID` 字段的值可能是错误的，因为 `min` 操作没有保留原始数据中的 `ID` 值。
  - 使用 `minBy(Value)` 操作：您将查找 `Value` 字段的最小值，并返回包含该最小值的完整元素。结果是 `{ID: 2, Value: 3}`，这是原始数据集中的一个实际元素。



6. window 算子：
    `window` 算子用于将输入数据流划分为有限的时间或数据量的窗口，以便在窗口范围内执行聚合或其他操作。例如，下面的代码将输入数据流划分为 10 秒的滚动窗口，并计算每个窗口内的单词出现次数：

```java
KeyedStream<Tuple2<String, Integer>> keyed = ...
DataStream<Tuple2<String, Integer>> wordCounts = keyed
    .window(TumblingProcessingTimeWindows.of(Time.seconds(10)))
    .sum(1);
```

这些示例仅展示了 Flink 中一些常见的算子。Flink 提供了丰富的算子库，可以处理各种复杂的数据处理任务。除了上面提到的算子之外，Flink 还包括以下一些常见的算子：

6. reduce 算子：
`reduce` 算子用于对输入数据流中的元素进行聚合操作，它根据提供的 reduce 函数将两个元素逐个组合成一个元素。例如，下面的代码计算输入数据流中整数值的总和：

```java
DataStream<Integer> input = ...
DataStream<Integer> sum = input.reduce(new ReduceFunction<Integer>() {
    @Override
    public Integer reduce(Integer value1, Integer value2) {
        return value1 + value2;
    }
});
```

7. union 算子：
`union` 算子用于将多个数据流合并为一个数据流。它的输入可以是具有相同类型的多个数据流。例如，下面的代码将三个字符串数据流合并为一个数据流：

```java
DataStream<String> input1 = ...
DataStream<String> input2 = ...
DataStream<String> input3 = ...
DataStream<String> unionStream = input1.union(input2, input3);
```

8. connect 算子：
`connect` 算子用于将两个数据流连接起来，以便在下游的 `CoProcessFunction` 中对它们进行协同处理。`connect` 算子允许输入数据流具有不同的类型。例如，下面的代码将两个具有不同类型的数据流连接起来，然后在 `CoProcessFunction` 中分别处理它们：

```java
DataStream<Integer> input1 = ...
DataStream<String> input2 = ...
ConnectedStreams<Integer, String> connected = input1.connect(input2);

DataStream<Object> processed = connected.process(new CoProcessFunction<Integer, String, Object>() {
    @Override
    public void processElement1(Integer value, Context ctx, Collector<Object> out) {
        // 处理来自 input1 的数据
    }

    @Override
    public void processElement2(String value, Context ctx, Collector<Object> out) {
        // 处理来自 input2 的数据
    }
});
```

9. split 和 select 算子：
`split` 算子用于将输入数据流根据某种条件拆分为多个数据流，每个数据流包含满足特定条件的元素。`select` 算子用于从拆分的数据流中选择一个或多个数据流。例如，下面的代码将输入数据流根据整数值的奇偶性拆分为两个数据流：

```java
DataStream<Integer> input = ...
SplitStream<Integer> split = input.split(new OutputSelector<Integer>() {
    @Override
    public Iterable<String> select(Integer value) {
        return value % 2 == 0 ? Collections.singleton("even") : Collections.singleton("odd");
    }
});

DataStream<Integer> evenNumbers = split.select("even");
DataStream<Integer> oddNumbers = split.select("odd");
```

10. join 算子：
`join` 算子用于将两个数据流按照某个键进行连接。例如，下面的代码根据 ID 将两个数据流连接起来：

```java
DataStream<Tuple2<Integer, String>> input1 = ...
DataStream<Tuple2<Integer, Double>> input2 = ...

DataStream<Tuple3<Integer, String, Double>> joined = input1.join(input2)
    .where(new KeySelector<Tuple2<Integer, String>, Integer>() {
        @Override
        public Integer getKey(Tuple2<Integer, String> value) {
            return value.f0;
        }
    })
    .equalTo(new KeySelector<Tuple2<Integer, Double>, Integer>() {
        @Override
        public Integer getKey(Tuple2<Integer, Double> value) {
            return value.f0;
        }
    })
    .window(TumblingProcessingTimeWindows.of(Time.seconds(10)))
    .apply(new JoinFunction<Tuple2<Integer, String>, Tuple2<Integer, Double>, Tuple3<Integer, String, Double>>() {
        @Override
        public Tuple3<Integer, String, Double> join(Tuple2<Integer, String> first, Tuple2<Integer, Double> second) {
            return new Tuple3<>(first.f0, first.f1, second.f1);
        }
    });
```

11. cogroup 算子：
`cogroup` 算子用于将两个数据流按照某个键进行分组，然后在同一组内的元素上执行用户定义的函数。例如，下面的代码根据 ID 将两个数据流进行分组并计算每组内的元素数量：

```java
DataStream<Tuple2<Integer, String>> input1 = ...
DataStream<Tuple2<Integer, String>> input2 = ...

DataStream<Tuple3<Integer, Integer, Integer>> cogrouped = input1.coGroup(input2)
    .where(new KeySelector<Tuple2<Integer, String>, Integer>() {
        @Override
        public Integer getKey(Tuple2<Integer, String> value) {
            return value.f0;
        }
    })
    .equalTo(new KeySelector<Tuple2<Integer, String>, Integer>() {
        @Override
        public Integer getKey(Tuple2<Integer, String> value) {
            return value.f0;
        }
    })
    .window(TumblingProcessingTimeWindows.of(Time.seconds(10)))
    .apply(new CoGroupFunction<Tuple2<Integer, String>, Tuple2<Integer, String>, Tuple3<Integer, Integer, Integer>>() {
        @Override
        public void coGroup(Iterable<Tuple2<Integer, String>> first, Iterable<Tuple2<Integer, String>> second, Collector<Tuple3<Integer, Integer, Integer>> out) {
            int key = -1;
            int count1 = 0;
            int count2 = 0;

            for (Tuple2<Integer, String> t : first) {
                key = t.f0;
                count1++;
            }

            for (Tuple2<Integer, String> t : second) {
                key = t.f0;
                count2++;
            }

            out.collect(new Tuple3<>(key, count1, count2));
        }
    });
```

12. broadcast 算子：
`broadcast` 算子用于将一个数据流广播到另一个数据流的所有并行实例中。这可以用于某些需要共享全局信息的场景。例如，我们可以将一个规则数据流广播到主数据流中的所有并行任务中，以便每个任务都可以访问这些规则。

```java
DataStream<String> mainStream = ...
DataStream<String> ruleStream = ...

BroadcastStream<String> broadcastRuleStream = ruleStream.broadcast();

DataStream<String> resultStream = mainStream
    .connect(broadcastRuleStream)
    .process(new BroadcastProcessFunction<String, String, String>() {
        private List<String> rules = new ArrayList<>();

        @Override
        public void processElement(String value, ReadOnlyContext ctx, Collector<String> out) {
            // 主数据流中的元素与规则数据流中的规则进行比较，并进行处理
            for (String rule : rules) {
                // 对 value 进行处理，例如过滤或转换
            }
        }

        @Override
        public void processBroadcastElement(String rule, Context ctx, Collector<String> out) {
            // 更新规则列表
            rules.add(rule);
        }
    });
```

13. iterate 算子：
`iterate` 算子用于对输入数据流中的元素进行迭代计算。这可以用于实现如 PageRank 等迭代算法。例如，下面的代码展示了如何使用 iterate 算子计算数据流中所有整数的累积和：

```java
DataStream<Integer> input = ...

IterativeStream<Integer> iteration = input.iterate(5000L);  // 设置迭代的超时时间

DataStream<Integer> iterationBody = iteration
    .map(new MapFunction<Integer, Tuple2<Integer, Integer>>() {
        @Override
        public Tuple2<Integer, Integer> map(Integer value) {
            return new Tuple2<>(value, 1);
        }
    })
    .keyBy(0)
    .reduce(new ReduceFunction<Tuple2<Integer, Integer>>() {
        @Override
        public Tuple2<Integer, Integer> reduce(Tuple2<Integer, Integer> value1, Tuple2<Integer, Integer> value2) {
            return new Tuple2<>(value1.f0 + value2.f0, value1.f1 + value2.f1);
        }
    })
    .filter(new FilterFunction<Tuple2<Integer, Integer>>() {
        @Override
        public boolean filter(Tuple2<Integer, Integer> value) {
            return value.f1 < 100;
        }
    });

iteration.closeWith(iterationBody);
```

这些只是 Flink 提供的一些常用算子的示例。通过将这些算子以及其他算子组合在一起，您可以构建复杂的流式数据处理任务以满足各种实际应用场景的需求。根据您的应用场景和需求，您可能会发现 Flink 提供的其他算子也非常有用。您可以参考 Flink 官方文档以获取更多关于算子的信息。

Flink 1.13 版本中文文档（当前最新的中文文档）：

- 数据流转换操作（算子）：https://ci.apache.org/projects/flink/flink-docs-release-1.13/zh/docs/dev/datastream/operators/

请注意，Flink 官方文档的中文版本可能不如英文版本及时更新。因此，如果您想了解最新的 Flink 功能和信息，建议您查阅英文文档。这是 Flink 英文官方文档关于算子的链接：

Flink 1.14 版本英文文档（当前最新的英文文档）：

- DataStream Transformations（算子）：https://ci.apache.org/projects/flink/flink-docs-release-1.14/docs/dev/datastream/operators/