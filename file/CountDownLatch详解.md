#### 简单示例

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

