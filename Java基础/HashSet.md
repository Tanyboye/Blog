- **HashSet如何保证所有数据无重复**

  HashSet内部维护了一个HashMap,添加元素时把元素作为key保存到hashmap，由于hashmap的key不能重复，以此来保证HashSet的数据无重复，由于hashmap比较时是调用的对象的equals方法，所以比较对象相同实际上是通过equals方法。

  ```java
  public boolean add(E e) {
      return map.put(e, PRESENT)==null;
  }
  ```

  ```Java
  if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
  ```