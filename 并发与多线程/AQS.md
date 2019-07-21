| 结点状态  | 值   | 描述                                                         |
| :-------- | :--- | :----------------------------------------------------------- |
| CANCELLED | 1    | 取消状态。表示结点被中断或获取锁超时，需要移出队列           |
| SIGNAL    | -1   | 唤醒通知。表示有需要被唤醒的后驱节点（表示后驱结点需要被唤醒） |
| CONDITION | -2   | Condition专用。表示当前结点在Condition队列中，因为等待某个条件而被阻塞了 |
| PROPAGATE | -3   | 传播。适用于共享模式（比如连续的读操作结点可以依次进入临界区，设为PROPAGATE有助于实现这种迭代操作。） |
| INITIAL   | 0    | 默认。新结点会处于这种状态                                   |

```java
//等待获取锁
//通过自旋获取锁，如果锁已经被占用，则阻塞当前线程
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            //获得当前节点的先驱节点
            final Node p = node.predecessor();
            //如果当前节点的先驱节点是头结点，则尝试获取锁，获取锁成功后进行必要的设置
            //因为头节点是持有锁的节点，只有第2个节点才能获取锁
            if (p == head && tryAcquire(arg)) {
                setHead(node); //队列头指针用指向当前节点,设置头结点
                p.next = null; // 释放前驱节点，help GC
                failed = false; // 标识成功获取到锁
                return interrupted;
            }
            //当前线程是否需要被阻塞,如果需要，则阻塞当前线程
            //否则通过自旋重新尝试获取锁
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```



```java
//返回当前线程是否需要被阻塞
//核心功能是用来设置前驱节点的状态为SIGNAL，表示当前线程需要被唤醒（有需要唤醒的线程）
//同时删除已经等待超时或者被中断的节点（线程）
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    int ws = pred.waitStatus;
    if (ws == Node.SIGNAL)
        return true;
    if (ws > 0) {
        //如果当前线程的前驱节点状态为CANCELLED（ws > 0），
        //则表明该线程的前驱节点已经等待超时或者被中断了，
        //则需要从CLH队列中将该前驱节点删除掉，
        //删除掉所有需要删除的节点
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {
        //设置前驱节点的状态为SIGNAL，
        //并在接下来进行一次自旋操作尝试获取锁
        //如果获取不到。返回true，表示需要阻塞当前线程
        //设置设置前驱节点的状态为SIGNAL的意义在于表示当前线程需要被唤醒（有需要唤醒的线程）
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
    }
    return false;
}
```



```java
//唤醒在等待的线程（头结点的后驱节点线程）
private void unparkSuccessor(Node node) {

    int ws = node.waitStatus;
    //重置头结点的状态
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);
    Node s = node.next;
    //如果头结点的下一个节点被取消，找到第一个在等待唤醒的节点
    if (s == null || s.waitStatus > 0) {
        s = null;
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    //唤醒头结点的下一个节点
    if (s != null)
        LockSupport.unpark(s.thread);
}
```



```java
//超时获取锁
private boolean doAcquireNanos(int arg, long nanosTimeout)
        throws InterruptedException {
    if (nanosTimeout <= 0L)
        return false;
    //根据超时时间和当前时间计算出截止时间
    final long deadline = System.nanoTime() + nanosTimeout;
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return true;
            }
            //计算剩余时间，如果剩余时间<0,则表示超时
            nanosTimeout = deadline - System.nanoTime();
            if (nanosTimeout <= 0L)
                return false;
            //判断是否需要被阻塞，（并阻塞指定的世界）
            if (shouldParkAfterFailedAcquire(p, node) &&
                nanosTimeout > spinForTimeoutThreshold)
                LockSupport.parkNanos(this, nanosTimeout);
            if (Thread.interrupted())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```