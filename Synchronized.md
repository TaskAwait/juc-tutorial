# Synchronized

## 对象锁

方法锁：默认锁为 this，当前对象实例

同步代码块：手动指定锁对象，可以指定 this，也可以创建对象，将对象当做锁

## 代码块形式

### 示例一：手动指定锁对象，将 this (当前实例) 当做锁对象

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class SynchronizedObjectLock implements Runnable {

    static SynchronizedObjectLock instance = new SynchronizedObjectLock();

    @Override
    public void run() {
        synchronized (this) {
            log.info("我是线程: {}", Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.info("{} 结束", Thread.currentThread().getName());
        }
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(instance);
        Thread t2 = new Thread(instance);

        t1.start();
        t2.start();
    }
}
```

输出结果

```java
[Thread-0] 我是线程: Thread-0
[Thread-0] hread-0 结束
[Thread-1] 我是线程: Thread-1
[Thread-1] hread-1 结束
```

### 示例二：创建两把锁，分别提供给不同的同步代码块

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class SynchronizedObjectLock2 implements Runnable {

    static SynchronizedObjectLock2 instance = new SynchronizedObjectLock2();

    // 创建 2把锁
    Object block1 = new Object();
    Object block2 = new Object();

    @Override
    public void run() {
        synchronized (block1) {
            log.info("block1锁, 我是线程: {}", Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.info("block1锁, {} 结束", Thread.currentThread().getName());
        }

        synchronized (block2) {
            log.info("block2锁, 我是线程: {}", Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.info("block2锁, {} 结束", Thread.currentThread().getName());
        }
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(instance);
        Thread t2 = new Thread(instance);

        t1.start();
        t2.start();
    }
}
```

输出结果

```java
[Thread-0] block1锁, 我是线程: Thread-0
[Thread-0] block1锁, Thread-0 结束
[Thread-0] block2锁, 我是线程: Thread-0
[Thread-1] block1锁, 我是线程: Thread-1
[Thread-0] block2锁, Thread-0 结束
[Thread-1] block1锁, Thread-1 结束
[Thread-1] block2锁, 我是线程: Thread-1
[Thread-1] block2锁, Thread-1 结束
```

由于不同的代码块获取的是不同的锁对象，因此不同的同步代码块执行完毕后就会释放锁，其他线程就能获取到锁执行同步代码块内部代码。

