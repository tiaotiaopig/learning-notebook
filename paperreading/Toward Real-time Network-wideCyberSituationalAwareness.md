# Toward Real-time Network-wide Cyber Situational Awareness

## site

> Jirsik T, Celeda P. Toward real-time network-wide cyber situational awareness[C]//NOMS 2018-2018 IEEE/IFIP Network Operations and Management Symposium. IEEE, 2018: 1-7.

## idea

利用一个分布式数据流处理系统做实时大数据处理，解决网络中数据量过大，响应速度慢，缺乏上下文和统一视图的问题，提供了原型框架的实现版本。（利用框架做了一个具体的系统）

## architecture

### 主要架构

![framework](https://i.loli.net/2021/05/29/IM8abVe7EoqhYcX.png)

通过探针采集到的各种数据，会先送到统一化系统（**IPFIXCol**），处理成相同的格式（json），（编码和转化）

统一化的文件，会被送到消息系统进行分发，高效分配到分布式流处理框架（**Apache Kafka**）

分布式流处理系统，并行化处理和可扩展性，高速处理，实现实时分析（**Spark**）

新一代数据库（**Elastic Stack** comprising of Logstash, Elasticsearch, and Kibana ）支持大容量存储和高级查询

### 主要技术

![技术栈](https://i.loli.net/2021/05/29/DkLu4MfIdlrTZ9U.png)

### content

+ **Network Perception**

  网络感知就是收集关于网络状态，属性，动态的信息，主要是通过探针来获取

  探针主要有两种，主动探针和被动探针

  * 被动探针，收集通过某一观测点的数据
  * 主动探针，基于响应，主动搜集数据

  有两种源数据，网络本身（设备类型，网络位置，链路，路由），网络流量（谁和谁连接，多久）

  使用主动探针，观测网络本身，关于网络基础设施的信息，主要通过简单网络管理协议（SNMP）或者中心化的网络设备日志收集

  使用被动探针，观测网络流量，可以得知网络元素的实际行为

  网络感知可获取的数据包括网络包，IP流，日志

  网络包分析，可以获得网络流量的最详细的信息，但是包数量太多，分析压力大，特别是网络规模，而不是主机级别

  IP流分析，主要用于网络级别的流量监控

  IP流，是一种网络连接的抽象，被定义为：在一段时间内，通过观测点的具有共同特性的包的集合

  通过抽象，需要分析的数据量，比起数据包分析来说，大大降低

+ **Network Comprehension**

  网络理解就是从网络原始数据中，获取高级关系，包括关键节点，网络设施的瓶颈，高流量者，恶意行为，漏洞等

  数据分析，就是存储在收集器的数据的查询和响应的集合

+ **Requirements**

  实现有效的网络态势感知，需要多种信息

  不同工具使用不同的分析语言和方法，影响工作流和数据理解

  原始数据携带的信息可能重复，矛盾

  网络管理员在使用的过程中，可能要在不同方法来回切换，分析的多样性会阻碍决策