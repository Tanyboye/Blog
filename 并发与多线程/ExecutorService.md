- **invokeAll()**

  invokeAll()方法会等待所有任务执行完成，此方法会阻塞线程，invokeAll()方法会返回Future集合，可以用来获取Callable返回的结果

- **invokeAny()**

  invokeAny()方法会让所有任务执行，并返回最快完成的任务结果，并停止其他任务，此方法会阻塞线程

- **execute()**

  在execute()方法中会通过addWorker()方法来新建一个线程并启动任务，会新建一个Worker类，Worker类本身是一个Runnable，在构造方法里面通过传入自身来创建一个Thread，然后启动线程start()，即会执行Worker类的run()方法，进一步调用了runWorker()方法，runWorker()是线程池里面的线程处理任务的核心方法，里面通过while循环，在第一次执行完任务后会通过task = getTask()一直等待获取任务并继续执行，getTask()方法采用自旋操作一直等待阻塞队列中的任务，直到队列中有任务后返回任务。