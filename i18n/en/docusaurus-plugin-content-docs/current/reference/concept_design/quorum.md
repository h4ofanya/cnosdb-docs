---
title: Quorum Algorithm
order: 2
---

# Quorum Algorithm

Quorum algorithm is a voting algorithm commonly used in distributed systems to ensure data redundancy and eventual consistency. Quorum is used to ensure that if some participants fail, we can still collect votes from the surviving participants and continue to execute the algorithm. A Quorum represents the minimum number of votes needed to perform an action, typically a majority of participants. The core idea behind Quorum is that even if participants fail or happen to be separated by network partitions, at least one of them can act as an arbiter to ensure the accuracy of the protocol.When "W + R > N", the latest data can be determined by timestamp, version number, etc. In other words, under the combination of parameters that meet this condition, the strong consistency of data can be realized.Quorum 表示执行操作所需的最小票数，通常为多数派的参与者。Quorum 背后的核心思想就是，即使参与者发生故障或恰好被网络分区隔开，也至少有一个参与者可以充当仲裁者，以确保协议的准确性。

### Basic Principle of Quorum Algorithm

There are three parameters in Quorum URW algorithm: N, R, W.

Parameter N is also called a copy factor, meaning a number of copies of data throughout the cluster.

Parameter R is the level of read consistency, which means successful reading from R copies will be considered a successful reading operation.

Parameter W is the level of writing consistency, which means successfully written from W copies, will be considered a successful writing operation.

N, R and W parameters can achieve different consistency levels under different combination conditions:

1. 当 「W + R > N」时，利用时间戳、版本号等手段即可确定出最新的数据。换言之在满足该条件的参数组合下，可以实现数据的强一致性。
2. When "W + R < = N" is not possible, it can only guarantee final consistency, i.e. system reading may obtain old data.
3. When "W = N, R = 1, so-called Write All Read One" is the scenario of the CP model in CAP theory.
4. When "W \<N, R = 1" is the scene of the AP model in CAP theory.

### Modalities for Data Consistency Assurance

Data are required to write N when writing, taking into account that there may be write failures in reality, loss of data copies caused by machine failures, and inconsistencies in multiple copies of data, such as write-and-end.

1. The hinted-handoff mechanism is a machine received to write requests. When a remote replaica is written in failure, it is stored in the hinted-handoff queue of the machine; this opportunity regularly sends the contents of the hinted-handoff queue to a remote node to achieve final consistency in the data. Typically, the hinted-handoff queue has a capacity limit, and overcapacity writes will fail.通常hinted-handoff队列会有一个容量限制，超过容量写入将会响应失败。
2. Reading repair mechanism If two data are found to be inconsistent when read, repairs are made to the final consistency of data based on version number, timestamp or other copy information; reading and repairs are usually used in the CP model.
3. It is because of the large cost of the system that the antitropy mechanism is different when it is implemented, different systems choose to realize it according to different particle size. Some systems routinely view only whether one data block content is lost without carsandra, while some systems validate each content within the periodic calibration system, such as liak. In content-based consistency implementations, this data structure is usually built on Merkel Tree.In this distributed algorithm of Quorum, the deletion operation is a special operation to deal with the problem that there may be no resurrection of old data. For example, three copies, two successful deletions of one unsuccessful, and the failure to delete successful data processing may be used as valid data synchronization to cause the resurrection of deleted data; in order to address this situation, marker deletion is usually used when deleted, and is later really deleted from disk, which is often referred to as a tombstone mechanism.
4. The anti-entry anti-entropy mechanism is similar to a background checker, where there is a lack of data between copies and where values exist and are not consistent and then repaired. This mechanism is usually costly for the system, and most systems have a configuration switch when implemented, which is determined by users whether to open.这种机制通常对系统开销巨大，多数系统在实现的时候都会有个配置开关，由用户决定是否开启。

反熵机制正是由于系统开销巨大所以在实现的时候，不同系统会选择按照不同粒度实现方式不同。有些系统常规情况只查看一个数据块内容是否有丢失，而不关心数据块内部内容是否一致例如cassandra,有一些系统则会周期性校验系统内部每条内容是否一致例如riak。在基于内容的一致性实现中通常借助于默克尔树(Merkle Tree)这种数据结构。
