## Kafka

1. 一个topic可以有多个Consumer。
2. 每一个partition都有1个leader server，0或多个follower server。leader负责读写请求，而follower只是被动地复制leader。当leader宕机时，会从follower中选取一个成为leader.
3. 如果consumer具有相同的consumer group，则消息会集群消费；如果consumer都在不同的consumer group，则相当于广播消费。
4. kafka在一个partition中可以实现顺序消息，但是不同partition之间不支持。



