#### 简单示例

```java
public class PromiseTest {
    final static DefaultEventExecutor wang = new DefaultEventExecutor();
    final static DefaultEventExecutor li = new DefaultEventExecutor();

    public static void main(String[] args) {
        wang.execute(() -> System.out.println(Thread.currentThread().getName() + " 1.这是一道简单的题"));

        wang.execute(() -> {
            final Promise<Integer> promise = wang.newPromise();
            promise.addListener(future -> System.out.println(Thread.currentThread().getName() + " 复杂题执行结果: " + future.getNow()));
            li.execute(() -> {
                System.out.println(Thread.currentThread().getName() + " 2.这是一道复杂的题");
                promise.setSuccess(10);
            });
        });

        wang.execute(() -> System.out.println(Thread.currentThread().getName() + " 3.这是一道简单的题"));
    }
}
```

