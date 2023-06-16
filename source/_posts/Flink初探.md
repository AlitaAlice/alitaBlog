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

在 Apache Flink 中，反压（Backpressure）是一种流处理系统中的流量控制机制。它用于处理生产者与消费者之间速度不匹配的情况，以确保消费者能够有效地处理生产者发送的数据。

通常，当数据流从生产者到消费者传输时，生产者可能比消费者更快地产生数据。这可能导致消费者无法及时处理所有的数据，从而导致数据在系统中的积压增加，造成延迟和资源浪费。

为了解决这个问题，反压模型引入了一种机制，使得生产者能够根据消费者的处理能力来适应发送数据的速度。

在 Flink 中，反压模型的基本思想是，当消费者无法及时处理数据时，生产者会收到一个反压信号，告知其减缓数据发送速率。生产者通过降低发送速率，使得消费者能够更好地跟上数据的处理，从而缓解系统的压力。

Flink 中的反压模型通常基于背压传递（Backpressure Propagation）机制，它通过在流图中的各个操作算子之间传递背压信号，使得整个流处理任务可以根据消费者的处理能力进行协调。

具体来说，当某个算子（例如消费者）无法及时处理数据时，它会向其上游的算子（例如生产者）发送背压信号，通知其减慢数据发送速率。上游算子接收到背压信号后，会相应地降低数据发送速率，以使整个系统保持平衡。

通过反压模型，Flink 可以在数据流处理中实现流量控制，避免生产者与消费者之间的速度失衡问题，提高整体系统的稳定性和性能。

需要注意的是，反压模型可能会引入一定的延迟，因为数据发送者需要根据消费者的处理能力来调整发送速率。因此，在设计和配置 Flink 作业时，需要根据具体的业务需求和系统资源来考虑反压的使用。

总结起来，Flink 中的反压模型是一种流处理系统中的流量控制机制，用于在生产者与消费者之间协调数据的发送和处理速度，以避免速度不匹配导致的问题。

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