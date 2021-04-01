#### redo log日志模块

redo log是InnoDB存储引擎层的日志，又称重做日志文件，用于记录事务操作的变化，记录的是数据修改之后的值，不管事务是否提交都会记录下来。在实例和介质失败（media failure）时，redo log文件就能派上用场，如数据库掉电，InnoDB存储引擎会使用redo log恢复到掉电前的时刻，以此来保证数据的完整性。

在一条更新语句进行执行的时候，InnoDB引擎会把更新记录写到redo log日志中，然后更新内存，此时算是语句执行完了，然后在空闲的时候或者是按照设定的更新策略将redo log中的内容更新到磁盘中，这里涉及到`WAL`即`Write Ahead logging`技术，他的关键点是先写日志，再写磁盘。

有了redo log日志，那么在数据库进行异常重启的时候，可以根据redo log日志进行恢复，也就达到了`crash-safe`。



redo log日志的大小是固定的，即记录满了以后就从头循环写。

![image](https://www.linuxidc.com/upload/2018_11/181121105137361.jpg)

图片来自极客时间，该图展示了一组4个文件的redo log日志，checkpoint之前表示擦除完了的，即可以进行写的，擦除之前会更新到磁盘中，write pos是指写的位置，当write pos和checkpoint相遇的时候表明redo log已经满了，这个时候数据库停止进行数据库更新语句的执行，转而进行redo log日志同步到磁盘中。