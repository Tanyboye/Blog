retry和goto类似

不是关键字，而是一种语法结构

retry:后面必须跟循环体，作用于后面紧跟的那一层循环

例子：

```java
public static void print(){
    for(int i = 0; i < 10; i++){
        for(int j = 0; j < 5; j++){
            System.out.print(i + "" + j + " ");
        }
        System.out.println();
    }
}
```

输出：

```
00 01 02 03 04 
10 11 12 13 14 
20 21 22 23 24 
30 31 32 33 34 
40 41 42 43 44 
50 51 52 53 54 
60 61 62 63 64 
70 71 72 73 74 
80 81 82 83 84 
90 91 92 93 94 
```

------

```Java
public static void print1(){
    retry:
    for(int i = 0; i < 10; i++){
        for(int j = 0; j < 5; j++){
            System.out.print(i + "" + j + " ");
            if(j == 3){
                System.out.println();
                continue retry;
            }
        }
        System.out.println();
    }
}
```

输出：

```
00 01 02 03 
10 11 12 13 
20 21 22 23 
30 31 32 33 
40 41 42 43 
50 51 52 53 
60 61 62 63 
70 71 72 73 
80 81 82 83 
90 91 92 93 
```

------

```Java
public static void print2(){
    retry:
    for(int i = 0; i < 10; i++){
        for(int j = 0; j < 5; j++){
            System.out.print(i + "" + j + " ");
            if(j == 3){
                System.out.println();
                break retry;
            }
        }
        System.out.println();
    }
}
```

输出：

``` 
00 01 02 03 
```



**参考**

[java retry的使用详解](https://blog.csdn.net/evalsys/article/details/80843457)