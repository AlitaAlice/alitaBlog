---
title:  DataFlowModel及DataStream Api
date: 2023-05-01 20:08:27
tags:
categories:
---

## 概述

> 本章介绍

<!--more-->

## 正文

# 分布式流处理模型 dataFlowModel

![image-20230501200843828](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501200843828.png)

![image-20230501200938899](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501200938899.png)

![image-20230501201039489](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501201039489.png)

这里存在数据洗牌的操作，个人理解其实就是数据重新划分到不同的task执行。如keyBy操作

在 Flink 中，shuffle 数据洗牌是一种数据重分区的过程，它将数据重新分配到不同的任务（Task）上以进行并行处理。在数据处理过程中，某些操作需要将数据在各个任务之间进行重新划分，以实现负载均衡和高效处理。Shuffle 过程就是在 Flink 的任务之间重新分配数据的过程。

Shuffle 主要发生在 Flink 算子之间，尤其是在那些需要在不同任务之间交换数据的算子中。例如，group by、join 和 reduce 等操作都可能需要 shuffle 数据洗牌。在 Flink 中，shuffle 是通过网络进行的，因此需要注意其对性能的影响。尽可能地减少 shuffle 过程可以提高 Flink 作业的性能。

以下是一些 Flink 算子中可能发生 shuffle 的例子：

1. KeyBy 算子：根据指定的 key 对数据进行分组。在这个过程中，具有相同 key 的数据元素需要被发送到同一个任务上进行处理，这就需要对数据进行 shuffle 操作。
2. GroupBy 算子：与 KeyBy 类似，GroupBy 也需要将具有相同 key 的数据元素发送到同一个任务上进行处理。为了实现这一点，需要对数据进行 shuffle。
3. Join 算子：当执行连接操作时，需要将两个数据集中具有相同 key 的元素配对。为了实现这一点，需要根据连接 key 对数据进行 shuffle。

值得注意的是，Flink 使用一种名为 Data Exchange 的技术来实现 shuffle 过程。数据交换技术提供了多种数据重分区策略，如 hash 分区、range 分区等。这些策略可以根据不同的场景和需求选择，并影响 Flink 作业的性能。

# DataStream Api实践原理

![image-20230501201808300](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501201808300.png)

![image-20230501202031248](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501202031248.png)

![image-20230501202245536](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501202245536.png)

![image-20230501203504996](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501203504996.png)

![image-20230501203905563](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501203905563.png)

![image-20230501203937915](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501203937915.png)

![image-20230501204005915](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501204005915.png)

![image-20230501220705056](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501220705056.png)

![image-20230501221043566](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501221043566.png)

![image-20230501221207349](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501221207349.png)

![image-20230501222121763](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501222121763.png)

![image-20230501223156765](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501223156765.png)

![image-20230501223241673](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501223241673.png)

![image-20230501223601382](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501223601382.png)

![image-20230501223841653](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501223841653.png)