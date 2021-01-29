### 使用概述



简单示例：

```java
public class Driver {

    private final static int N = 5;
    //startSignal的作用是让主线程完成work1后，其他子线程再执行任务
    private final CountDownLatch startSignal = new CountDownLatch(1);
    //doneSignal的作用是让子线程都完成任务后，再执行主线程的work2任务
    private final CountDownLatch doneSignal = new CountDownLatch(N);

    public void drive() throws InterruptedException {
        for (int i = 0; i < N; ++i) {
            new Thread(new Worker(startSignal, doneSignal)).start();
        }

        doWork1();
        startSignal.countDown();    // let all threads proceed
        doneSignal.await();         // wait for all to finish
        doWork2();
    }

    private void doWork1() {
        try {
            System.out.printf("%s doWork1 start:%d%n", Thread.currentThread().getName(), System.currentTimeMillis());
            Thread.sleep(new Random().nextInt(5000));
            System.out.printf("%s doWork1 end:%d%n", Thread.currentThread().getName(), System.currentTimeMillis());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    private void doWork2() {
        try {
            System.out.printf("%s doWork2 start:%d%n", Thread.currentThread().getName(), System.currentTimeMillis());
            Thread.sleep(new Random().nextInt(5000));
            System.out.printf("%s doWork2 end:%d%n", Thread.currentThread().getName(), System.currentTimeMillis());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        try {
            Driver driver = new Driver();
            driver.drive();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

public class Worker implements Runnable {

    private final CountDownLatch startSignal;
    private final CountDownLatch doneSignal;

    public Worker(CountDownLatch startSignal, CountDownLatch doneSignal) {
        this.startSignal = startSignal;
        this.doneSignal = doneSignal;
    }

    @Override
    public void run() {
        try {
            startSignal.await();
            doWork();
            doneSignal.countDown();
        } catch (InterruptedException ex) {
            ex.printStackTrace();
        }
    }

    private void doWork() {
        try {
            System.out.printf("%s start:%d%n", Thread.currentThread().getName(), System.currentTimeMillis());
            Thread.sleep(new Random().nextInt(5000));
            System.out.printf("%s end:%d%n", Thread.currentThread().getName(), System.currentTimeMillis());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

### 源码分析
#### 数据结构
CountDownLatch只有一个sync的属性，sync的类型是CountDownLatch内部类Sync同步器，Sync继承自AQS，CountDownLatch正是通过该同步器实现
相应的线程同步功能，其源码如下：
```java
private static final class Sync extends AbstractQueuedSynchronizer {
    private static final long serialVersionUID = 4982264981922014374L;

    Sync(int count) {
        setState(count);
    }

    int getCount() {
        return getState();
    }

    protected int tryAcquireShared(int acquires) {
        return (getState() == 0) ? 1 : -1;
    }

    protected boolean tryReleaseShared(int releases) {
        // Decrement count; signal when transition to zero
        for (;;) {
            int c = getState();
            if (c == 0)
                return false;
            int nextc = c-1;
            if (compareAndSetState(c, nextc))
                return nextc == 0;
        }
    }
}
```

#### 核心方法
##### 构造函数
```java
public CountDownLatch(int count) {
	if (count < 0) throw new IllegalArgumentException("count < 0");
    //count即为共享变量state的值
	this.sync = new Sync(count);
}
```
