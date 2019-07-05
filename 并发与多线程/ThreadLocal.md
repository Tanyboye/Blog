- **ThreadLocal中ThreadLocalMap里面enrty的key为什么要设置成弱引用？**

  将Entry的Key设置成弱引用，在配合线程池使用的情况下可能会有内存泄露的风险。之设计成弱引用的目的是为了更好地对ThreadLocal进行回收，当我们在代码中将ThreadLocal的强引用置为null后，这时候Entry中的ThreadLocal理应被回收了，但是如果Entry的key被设置成强引用则该ThreadLocal就不能被回收，这就是将其设置成弱引用的目的

  程序中通过ThreadLocal.get()获取线程私有变量。如果ThreadLocal不再使用（强引用不存在），Entry也应该被回收，但如果是key是强引用，则Entry不能被回收，如果是弱引用则key会被回收，同时会在ThreadLocalMap的getEntry函数或者set函数被调用时清除key为null的Entry，方便回收。

  参考：

  <https://www.cnblogs.com/xzwblog/p/7227509.html>

  <https://www.cnblogs.com/tiancai/p/9920901.html>

