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

   

