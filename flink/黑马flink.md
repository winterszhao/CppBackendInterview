[黑马flink](https://www.bilibili.com/video/BV1xe411W7vx/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=7ea306ca6b9f6f9e53a14bccde10656f)

## 简介

**Flink：**分布式，高性能，**随时可用**（checkpoint快照）以及**准确**（Exactly once）的流处理计算框架，可以对**无界数据**（流处理）和**有界数据**（批处理）进行**有状态计算**(flink天然支持有状态计算)

**Flink基石：**

1. Checkpoint：基于Chandy-Lamport分布式一致性算法的快照
2. State：提供State API让便用户管理状态（中间或历史计算结果）：ValueState，ListState，MapState，BroadState
3. Time：Watermark，支持事件时间和处理事件的计算，基于事件事件可以解决数据迟到和乱序问题。
4. Window：提供丰富的窗口，基于时间，基于数量，session window，同样支持滚动和挂东窗口的计算。

流处理和批处理：

- 流处理：DataStream API，无界，实时性有要求，只需对经过程序的每条数据进行处理。
- 批处理：DataSet API，有界，持久，需要对全部数据进行访问。