---
title: Flink集群架构
date: 2023-04-30 16:49:05
tags: Flink
categories: 大数据
---

## 概述

> 本章介绍

<!--more-->

## 正文

![image-20230501102605329](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102605329.png)

![image-20230501102614581](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102614581.png)

# Flink   JobManager 

Flink 架构中的 JobManager 是负责协调分布式执行的核心组件。它主要负责调度、协调任务以及管理作业的生命周期。JobManager 包含以下几个关键部分：

1. JobGraph 生成和优化：
JobManager 接收客户端提交的作业，将作业表示为 JobGraph。JobGraph 是 Flink 作业的逻辑表示，它包含作业的所有顶点（JobVertex）以及顶点之间的边（ResultPartition 和 InputGate）。JobManager 会对 JobGraph 进行优化，例如处理链式任务以提高性能。

2. Task Scheduling：
JobManager 负责将 JobGraph 中的任务分配给 TaskManager 执行。它根据作业的配置（如并行度和资源要求）以及集群的资源状况（如可用 TaskManager 和插槽）来决定任务的分配。

3. 状态和 Checkpoint 管理：
JobManager 负责协调作业的状态管理和 checkpoint。在启用 checkpoint 时，JobManager 会触发 checkpoint 操作，并确保 TaskManager 上的任务将其状态保存到状态后端。同时，JobManager 会跟踪作业的全局状态，以便在故障发生时执行故障恢复。

4. 故障恢复：
当作业执行过程中出现故障时，JobManager 负责恢复作业。它会找到最近的成功 checkpoint，并从 TaskManager 获取已保存的状态。然后，JobManager 会重启作业，恢复到故障发生前的状态。

5. 作业状态监控：
JobManager 提供了作业的状态监控功能，例如作业的运行状态、任务执行进度、资源使用情况等。用户可以通过 Web UI 或 REST API 获取作业的状态信息。

6. 作业取消和停止：
JobManager 负责处理来自用户的作业取消和停止请求。当接收到取消请求时，JobManager 会停止作业的执行，并释放相关资源。当接收到停止请求时，JobManager 会触发一个保存点，然后停止作业，这样作业可以在以后从保存点恢复。

总之，Flink 中的 JobManager 负责调度、协调任务以及管理作业的生命周期。它包括 JobGraph 生成和优化、任务调度、状态和 checkpoint 管理、故障恢复、作业状态监控以及作业取消和停止等关键部分。

# Flink Checkpoint

在 Flink 中，Checkpoint 是一种故障恢复机制，用于确保作业在发生故障时能够从最近的一致状态恢复。当启用 Checkpoint 时，Flink 会定期保存作业的状态快照，这些快照包括所有操作符的状态以及流处理过程中的中间结果。Checkpoint 使得 Flink 可以提供“恢复到一致状态”的故障恢复保证，从而实现准确且可靠的数据处理。

以下是有关 Checkpoint 的一些关键概念：

1. 触发 Checkpoint：
Flink 的 Checkpoint 是定期触发的。JobManager 根据配置的 Checkpoint 间隔自动触发 Checkpoint，然后将 Checkpoint 请求广播到所有的 TaskManager 上运行的任务。此时，每个任务会开始保存其状态。

2. 状态存储：
在 Flink 中，状态分为两类：Keyed State 和 Operator State。Keyed State 是基于键的状态，例如 MapState、ValueState 等。Operator State 是操作符级别的状态，例如 Kafka Connector 的消费者偏移量。在 Checkpoint 过程中，任务会将这些状态保存到状态后端，例如内存、文件系统或 RocksDB。

3. 状态后端：
Flink 支持多种状态后端，用于存储 Checkpoint 的状态数据。常见的状态后端包括 MemoryStateBackend、FsStateBackend 和 RocksDBStateBackend。不同的状态后端在性能、可扩展性和持久性方面有所不同，用户可以根据应用程序的需求选择合适的状态后端。

4. 水印和事件时间：
在基于事件时间处理的 Flink 作业中，水印（Watermark）是一种用于表示时间进展的机制。水印对于 Checkpoint 机制至关重要，因为它们确保了事件时间的一致性。在 Checkpoint 过程中，任务会保存当前的水印信息，以便在故障恢复时能够正确处理事件时间。

5. 故障恢复：
当作业发生故障时，Flink 会从最近的 Checkpoint 恢复。JobManager 会选择最近完成的 Checkpoint，然后将其状态数据分发给 TaskManager 上的任务。任务会从 Checkpoint 中恢复其状态，并从中断的位置继续执行。

总之，Checkpoint 是 Flink 中的一种故障恢复机制，通过定期保存作业的状态快照来确保作业在发生故障时能够从最近的一致状态恢复。Checkpoint 涉及到触发 Checkpoint、状态存储、状态后端、水印和事件时间以及故障恢复等关键概念。

![image-20230501102624354](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102624354.png)

# RM

在分布式计算领域，RM（ResourceManager）通常指资源管理器。资源管理器负责管理分布式系统中计算资源的分配和调度。对于大规模数据处理框架（如 Apache Hadoop 和 Apache Flink），资源管理器是一个关键组件，它确保了作业在集群中的高效执行。

在 Apache Hadoop 生态系统中，YARN（Yet Another Resource Negotiator）是一个广泛使用的资源管理器。YARN 负责管理 Hadoop 集群中的资源，为运行在集群上的各种应用程序（如 Hadoop MapReduce、Apache Spark、Apache Flink 等）提供资源分配和调度。

对于 Apache Flink，其可以与 YARN 集成，充当 Flink 作业的资源管理器。此外，Flink 还可以与其他资源管理器集成，如 Kubernetes 和 Apache Mesos。这些资源管理器可以为 Flink 提供动态的资源分配、弹性伸缩和故障恢复能力。

总之，RM（ResourceManager）是分布式计算领域中负责管理计算资源的组件。对于大规模数据处理框架，资源管理器是一个关键组件，确保了作业在集群中的高效执行。

![image-20230501102630519](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102630519.png)

![image-20230501102639162](https://cdn.jsdelivr.net/gh/AlitaAlice/image/img/image-20230501102639162.png)