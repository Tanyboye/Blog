



```java
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

此算法保证容量为2的整数次幂。

hashmap的容量为int，共32位（bit），通过无符号右移再和本身“位或”操作，保证最高位的1后面的位全变为1，最后再让结果n+1，得到2的整数次幂的值。n = cap - 1，为了防止，cap已经是2的幂，返回的capacity将是这个cap的2倍。