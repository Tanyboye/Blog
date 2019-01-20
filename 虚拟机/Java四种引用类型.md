java有4种引用类型，从强到弱分别是：  
强引用，软引用，弱引用，虚引用

目的：
- 可以让程序员通过代码的方式决定某些对象的生命周期（在何时被回收）。
- 有利于JVM进行垃圾回收。

#### 强引用（StrongReference）
一个对象有强引用时永远不会被垃圾回收，JVM宁愿抛出OutOfMemory错误也不会回收这种对象。
```
Object object = new Object();
```
可以通过将引用赋值为null，来让JVM在合适的时间就会回收该对象。

#### 软引用（SoftReference）
一个对象具有软引用，只有在内存空间不够时，JVM才会回收这种对象。
```
Object object = new Object();
SoftReference aSoftRef=new SoftReference(object);
```
SoftReference对象保存一个软引用对象，可以通过get()方法获得一个强引用对象，如果此软引用对象被JVM回收，get()方法返回null。但这个SoftReference对象已经不再具有存在的价值,如果不回收就会存在内存泄露，所以通常和ReferenceQueue一起使用。如果SoftReference保存的软引用对象已经被JVM回收，那么JVM会把这个SoftReference对象加到ReferenceQueue中，然后我们可以清除SoftReference对象，避免内存泄露。
```
Object object = new Object();
ReferenceQueue queue = new  ReferenceQueue();  
SoftReference  ref=new  SoftReference(object, queue); 
```
软引用通常使用在缓存中。

#### 弱引用（WeakReference）
当JVM进行垃圾回收时，无论内存是否充足，都会回收弱引用对象。
```
Object object = new Object();
ReferenceQueue queue = new ReferenceQueue ();
WeakReference wr = new WeakReference (object, queue);
```

#### 虚引用（PhantomReference）
假设一个对象仅持有虚引用，那么它就和没有引用一样，会被垃圾回收器回收。  
虚引用并不会决定对象的生命周期。  
虚引用主要用来跟踪对象被垃圾回收器回收的活动。  
虚引用必须和引用队列 （ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，假设发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用（PhantomReference）增加到引用队列中。
```
Object object = new Object();
ReferenceQueue queue = new ReferenceQueue ();
PhantomReference pr = new PhantomReference (object, queue);
```

#### 参考
[https://www.cnblogs.com/huajiezh/p/5835618.html](https://www.cnblogs.com/huajiezh/p/5835618.html)  
