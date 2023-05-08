---
title: Flink初探
date: 2023-04-30 16:09:01
tags: Flink
categories: 大数据
---

## 概述

> 本章介绍

<!--more-->

## 正文



![image-20230501102424386](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102424386.png)

![image-20230501102441377](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102441377.png)

![image-20230507161315751](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230507161315751.png)

![image-20230501102454656](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102454656.png)



# Flink算子

Flink中常见的算子可以分为以下几类：

1. Source：数据输入算子，用于获取数据源，例如从文件、Kafka、socket等读取数据。常用的source算子有File Source、Kafka Source等。
2. Transformation：数据转换算子，用于对输入数据进行处理和转换。包括Map、FlatMap、Filter、KeyBy、Reduce等。
3. Window：数据窗口算子，用于对输入数据进行分组和聚合，例如Tumbling Window、Sliding Window等。
4. Join：数据连接算子，用于将不同数据流的数据进行连接，例如Join、CoGroup等。
5. Sink：数据输出算子，用于将数据输出到指定的目的地，例如将数据输出到文件、Kafka、数据库等。常用的sink算子有File Sink、Kafka Sink等。



![image-20230501102504590](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102504590.png)

![image-20230501102515322](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102515322.png)

# Flink的序列化

Flink 使用对象序列化将数据对象转换为二进制格式，以便于在分布式系统中传输数据和在状态后端存储数据。序列化对于 Flink 的性能和可扩展性至关重要，因为在大规模数据处理过程中，低效的序列化可能导致大量的性能开销和内存占用。

Flink 支持多种序列化框架，例如 Java 默认的序列化机制、Kryo 序列化库以及 Avro 序列化框架。此外，Flink 也提供了一种高效的自定义序列化框架，称为 TypeSerializer。TypeSerializer 是 Flink 的核心序列化抽象，可以用于定制数据类型的序列化和反序列化过程。

TypeSerializer 为每种数据类型提供了一组方法，用于执行二进制序列化和反序列化。这些方法包括：

- `serialize()`: 将给定的数据对象序列化为二进制数据。
- `deserialize()`: 将二进制数据反序列化为数据对象。
- `copy()`: 创建数据对象的副本。
- `getLength()`: 返回序列化后的二进制数据的长度。
- `ensureCompatibility()`: 检查序列化器是否与另一个序列化器兼容。

在 Flink 中，内置类型（例如基本类型、数组和集合）通常具有高效的 TypeSerializer 实现。然而，对于自定义类型，建议您提供自己的 TypeSerializer 实现，以确保在 Flink 中高效地处理自定义类型的数据。具体的实现取决于您的数据类型和应用程序需求。通常，您需要权衡序列化性能、二进制数据大小和实现复杂性之间的关系，以实现最佳的性能和可维护性。

总之，Flink 中的对象序列化二进制存储是通过将数据对象转换为二进制格式来实现的，以便在分布式系统中传输数据和在状态后端存储数据。Flink 支持多种序列化框架，并提供了 TypeSerializer 抽象，使您能够为自定义数据类型定制高效的序列化和反序列化过程。