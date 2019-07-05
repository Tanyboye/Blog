- **invokeAll()**

  invokeAll()方法会等待所有任务执行完成，此方法会阻塞线程，invokeAll()方法会返回Future集合，可以用来获取Callable返回的结果

- **invokeAny()**

  invokeAny()方法会让所有任务执行，并返回最快完成的任务结果，并停止其他任务，此方法会阻塞线程