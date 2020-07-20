#### 服务端

1. NioEventLoopGroup对象包含EventExecutor对象列表，EventExecutor为父接口，这里是NioEventLoop对象实例，NioEventLoop主要有selector，selectedKeys，executor，taskQueue等属性。

2. 绑定操作

   (1) 创建NioServerSocketChannel，并进行初始化：参数设置、