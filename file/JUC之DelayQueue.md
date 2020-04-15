## 简介

DelayQueue是一个无界阻塞队列，用于放置实现了Delayed接口的对象，只有在延迟期满时才能从中提取元素。该队列是有序的，即队列的头部是延迟期满后保存时间最长的Delayed 元素。

注意：不能将null元素放置到这种队列中。



## 示例

实体类需要实现Delayed接口

```java
@Data
public class DelayedElement implements Delayed {

    private final long delay;   //延迟时间
    private final long expire;  //到期时间
    private final String msg;   //数据
    private final long now;     //创建时间
    private boolean isPay;

    public DelayedElement(long delay, String msg) {
        this.delay = delay;
        this.msg = msg;
        //到期时间 = 当前时间+延迟时间
        this.expire = System.currentTimeMillis() + delay;
        this.now = System.currentTimeMillis();
        this.isPay = false;
    }

    /**
     * 需要实现的接口，延迟时间=过期时间-当前时间
     *
     * @param unit
     * @return
     */
    @Override
    public long getDelay(TimeUnit unit) {
        return unit.convert(this.expire - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
    }

    /**
     * 用于延迟队列内部比较排序   当前时间的延迟时间 - 比较对象的延迟时间
     *
     * @param o
     * @return
     */
    @Override
    public int compareTo(Delayed o) {
        return (int) (this.getDelay(TimeUnit.MILLISECONDS) - o.getDelay(TimeUnit.MILLISECONDS));
    }
}

```



示例场景：两个定时任务 + 自旋

1. 生产者：定期往队列中添加元素。
2. 消费者：模拟用户付款，真实场景一般都有唯一标识，通过唯一标识可以修改实体状态，这里仅仅做简单的判断。
3. 检验过期数据：自旋检测过期数据



```java
private static long[] delayArray = new long[]{1000, 2000, 3000, 4000, 5000};
private static DelayQueue<DelayedElement> delayQueue = new DelayQueue<>();

public static void main(String[] args) throws InterruptedException {
    produce();
    consume();
    startTimer();
}

public static void produce() {
    new Thread(() -> {
        Timer timer = new Timer();
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                int index = RandomUtils.nextInt(0, delayArray.length);
                DelayedElement element = new DelayedElement(delayArray[index], "element");
                delayQueue.add(element);
                System.out.println("下单【" + element + "】");
            }
        }, 0, 10000);
    }).start();
}

public static void consume() {
    new Thread(() -> {
        Timer timer = new Timer();
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                Iterator iterator = delayQueue.iterator();
                while (iterator.hasNext()) {
                    DelayedElement element = (DelayedElement) iterator.next();
                    if (null != element && 1000 == element.getDelay()) {
                        element.setPay(true);
                        iterator.remove();
                        System.out.println("付款【" + element + "】");
                    }
                }
            }
        }, 0, 1000);
    }).start();
}

public static void startTimer() throws InterruptedException {
    DelayedElement element = null;
    while (null != (element = delayQueue.take()) && !element.isPay()) {
        System.out.println("清除过期数据【" + element + "】");
    }
}
```

