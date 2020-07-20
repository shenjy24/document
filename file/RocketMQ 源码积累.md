### Broker启动

#### 初始化BrokerController

1. 加载 store/config 中的 json 文件，其中 consumeOffset 记录了消费队列不同 queueId 已消费的偏移量，此处的偏移量类似于数组的坐标，可以理解为消费到第几条消息，消费队列中的消息结构固定为20字节，分别为8字节的 commitlog 物理偏移量、4字节的消息长度、8字节tag hashcode。由此可见，ConsumeQueue可以看作是对 commitlog 的一种索引结构，由于同一 broker 都使用同一个 commitlog 文件，如果直接在 commitlog 中检索性能会比较差，通过 ConsumeQueue 来索引消息提高性能。

2. 加载 store 目录下的 commitlog, consumequeue, index 目录下的文件到内存中。

   ```
   commitlog的文件目录格式为：consumequeue/${topic}/文件名为偏移量的文件
   consumequeue的文件目录格式为：consumequeue/${topic}/${queueId}(多个)/文件名为偏移量的文件
   ```

3. 启动底层 Netty 通信服务；创建多个线程池，不同功能在不同的线程池中执行，好处在于资源隔离，一个线程池出问题只影响到单个功能，不会造成大面积的功能故障；注册处理器，每种请求对应特定的处理器以及线程池。