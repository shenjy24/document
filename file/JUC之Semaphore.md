#### 简单示例

```java
public class SemaphoreExample extends Thread {

    private Semaphore semaphore;

    public SemaphoreExample(String threadName, Semaphore semaphore) {
        super(threadName);
        this.semaphore = semaphore;
    }

    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(2);
        for (int i = 0; i < 5; i++) {
            SemaphoreExample example = new SemaphoreExample("Thread-" + i, semaphore);
            example.start();
        }
    }

    @Override
    public void run() {
        try {
            semaphore.acquire();
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " : " + new Date());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            semaphore.release();
        }
    }
}
```

