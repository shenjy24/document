## MongoDB简介

### 1. 文档型数据库

MongoDB的每条记录是格式为键值对的文档，类似 JSON 对象。

使用文档有如下有点：

- 文档（即对象）对应于许多编程语言中的原生数据类型。
- 内嵌的文档和数组可以减少复杂的join操作。
- 动态的schema支持流畅的多态性 (fluent polymorphism)



#### 集合/视图/按需实例化视图 (Collections/Views/On-Demand Materialized Views)

MongoDB 将文档存储于 collections，collections 类似于关系型数据库中的表。

除了 collections，MongoDB 支持：

- 只读视图
- 按需实例化视图



### 2. 关键特性

#### 2.1 高性能

MongoDB 提供高性能的数据持久化服务。

- 支持内嵌 (embedded) 数据模型减少IO活动。
- 索引支持更快的查询，能够包含内嵌的文档和数组的键。



#### 2.2 丰富的查询语言 (query language)

MongoDB 提供丰富的查询语言来支持 CRUD 操作。

- 数据聚合
- 全文检索和地理空间查询



#### 2.3 高可用

MongoDB的复制工具，称为副本集 (replica set)，提供：

- 自动故障转移
- 数据冗余

副本集是一组维护相同数据集的 MongoDB 服务器，提供数据冗余和增加数据可用性。



#### 2.4  水平可伸缩性

- 通过分片(sharding)，可以将数据分布在一组机器上。
- 从3.4版本开始，支持基于分片键 (shard key) 创建数据区 (zones of data)。在均衡的集群中，MongoDB仅将区域覆盖的读写定向到区域内的那些分片。



#### 2.5 支持多种存储引擎

- WiredTiger Storage Engine (including support for [Encryption at Rest](https://docs.mongodb.com/manual/core/security-encryption-at-rest/))
- In-Memory Storage Engine

此外，MongoDB提供可插拔的存储引擎API，允许第三方为MongoDB开发存储引擎。



### 3. 视图 (Views)

#### 3.1 概述

MongoDB视图是可查询的对象，其内容由其他集合或视图上的聚合管道定义。MongoDB不会将视图的内容持久化到磁盘，也不支持针对视图的写入操作。

例如：

- 在员工数据集合上创建一个视图以排除任何私人或个人信息（PII）。 应用程序可以在视图中查询不包含任何PII的员工数据。
- 在收集的传感器数据的集合上创建一个视图，以添加计算的字段和度量。 应用程序可以使用简单的查找操作来查询数据。
- 创建一个视图，将分别包含库存和订单历史记录的两个集合连接起来。 应用程序可以查询连接的数据，而无需管理或了解底层的复杂管道。

当客户端查询视图时，MongoDB会将客户端查询追加到底层管道，并将该组合管道的结果返回给客户端。



#### 3.2 创建视图

- db.createCollection()
- db.createView()

注：创建视图时，view 和 collection 需要在相同的 database。



### 4. 索引

#### 4.1 默认 _id 索引



### 5. Replication

MongoDB中的副本集 (replica set) 是一组维护相同数据集的 mongod 进程。 副本集提供数据冗余和高可用的功能。



#### 5.1 数据冗余和数据可用性

在多个数据库服务器中都存有数据备份，提高了数据的容错性，避免单点故障问题。

在某些场景下，副本可以提高读容量，因为客户端可以发送读操作到不同的服务器上。



#### 5.2 复制操作

副本集一般包含多个数据承载节点和一个可选的仲裁节点。数据承载节点中，有且仅有一个主节点，其他节点均为从节点。

主节点接收所有的写操作。 



## 要点记录

1. MongoDB无须手动创建database，用到时会自动创建。

2. MongoDB无须手动创建 connections，第一次存储数据时会自动创建。

3. find语句的条件格式也是类似于JSON格式。

   ```
   db.inventory.find( { status: "D" } );
   db.inventory.find( { size: { h: 14, w: 21, uom: "cm" } } )
   ```

4. 指定返回的字段，`db.collection.find(<query document>, <projection document>)`

   - `<field>: 1` to include a field in the returned documents
   - `<field>: 0` to exclude a field in the returned documents

   ```
   //返回 _id, item, status
   db.inventory.find( { }, { item: 1, status: 1 } );
   ```

5. 文档校验

   默认情况下，collections 不要求存储的文档具有一致的 schema 。从3.2版本开始，可以强制使用 文档校验规则 来保证 collections 存储schema一致的文档。

6. collections 都被赋予一个不可修改的 UUID，在副本集的所有成员以及分片集合中的每个分片上都保持一致。

7. BSON文档最大只能16MB。

