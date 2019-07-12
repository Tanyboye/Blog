- Synchronized流程

![synchronized加锁流程](https://tva2.sinaimg.cn/large/007DFXDhly1g4x61w4chtj30yg0g3jsa.jpg)



![](https://tva1.sinaimg.cn/large/007DFXDhly1g4x63ojwkjj30gm07xweo.jpg)





- 偏向锁：无实际竞争，且将来只有第一个申请锁的线程会使用锁。
- 轻量级锁：无实际竞争，多个线程交替使用锁；允许短时间的锁竞争。
- 重量级锁：有实际竞争，且锁竞争时间长。



无锁状态  =》 锁标志位为01，偏向状态为1（此时为无锁状态，且可设置偏向锁） =》利用CAS操作将Thread ID修改为当前线程ID =》执行同步代码 =》新线程到来，尝试利用CAS操作将Thread ID修改为当前线程ID =》修改失败（此时Thread ID已经是其他线程的ID） =》当到达全局安全点（safepoint）时获得偏向锁的线程被挂起，撤销偏向锁（锁标志位为01，偏向状态为0） =》 同时偏向锁升级为轻量级锁，锁标志位置为00，在线程对应的栈帧中建立一个名为锁记录（Lock Record）的空间，将对象头中的Mark Word的拷贝到线程的Lock Record（为了后面的解锁） =》使用CAS操作尝试将对象头的Mark Word更新为指向Lock Record的指针  =》然恢复被挂起的线程继续往下执行同步代码 =》当前线程在栈帧中建立锁记录（Lock Record）的空间，将对象头中的Mark Word的拷贝到线程的Lock Record=》使用CAS操作尝试将对象头的Mark Word更新为指向当前线程Lock Record的指针   =》在此期间如果之前的线程执行完同步代码块，则释放锁，使用CAS操作尝试把当前的Mark Word替换成线程中复制的Displaced Mark Word对象（如果失败表明已膨胀为重量级锁，并且需要唤醒正在等待的线程）  =》如果之前的线程执行完同步代码块，并释放锁后，当前线程就有可能获取轻量级锁（使用CAS操作尝试将对象头的Mark Word更新为指向当前线程Lock Record的指针），其他线程同理 =》也可能有很多线程一起竞争，或一个线程长时间占用锁，导致CAS操作失败，进而膨胀为重量级锁（轻量级锁适用于线程交替执行或短时间竞争）=》将对象的mark word为指向一个堆中monitor对象的指针

