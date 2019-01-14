MVCC：多版本并发控制（Multiversion Currency Control）。MVCC机制，用来处理更多的并发问题。通过MVCC多版本并发控制。保证读不加锁，读写不冲突。

本质：通过版本号来避免加读锁。

#### MVCC原理
1.为了实现MVCC,innodb对每一行都加上两个隐含的列，其中一列存储行被更新的时间，另外一列存储行被删除的时间。但是innodb存储的并不是绝对的时间，而是与时间对应的数据库系统的版本号。（保存这两个额外系统版本号使大多数读操作可以不加锁）

2.每当一个事务开始的时候，innodb都会给这个事务分配一个递增的版本号。所以版本号也可以被认为是事务号。

3.对于每一个查询语句，innodb都会把这个查询语句的版本号同查询遇到的行的版本号进行对比，然后结合不同的事务等级来决定是否返回该行。

#### SELECT
Innodb检查每行数据，确保他们符合两个标准：    
1.InnoDB只查找版本早于当前事务版本的数据行（也就是数据行的版本必须小于等于事务的版本），这确保当前事务
  读取的行都是事务之前已经存在的，或者是由当前事务创建或修改的行。  
2.行的删除操作的版本一定是未定义的或者大于当前事务的版本号。确定了当前事务开始之前，行没有被删除。    
　
符合了以上两点则返回查询结果。

#### INSERT
InnoDB为每个新增行记录当前系统版本号作为创建ID。

id|name|创建时间(事务ID)|删除时间(事务ID)
:-:|:-:|:-:|:-:
1|小明|1|undefined
2|小红|1|undefined
3|小张|1|undefined


#### DELETE
InnoDB为每个删除行的记录当前系统版本号作为行的删除ID。

id|name|创建时间(事务ID)|删除时间(事务ID)
:-:|:-:|:-:|:-:
1|小明|1|2
2|小红|1|undefined
3|小张|1|undefined

#### UPDATE
InnoDB复制原有的一行数据。这个新行的版本号使用了系统版本号。它也把系统版本号作为了删除行的版本。

id|name|创建时间(事务ID)|删除时间(事务ID)
:-:|:-:|:-:|:-:
1|小明|1|2
2|小红|1|3
3|小张|1|undefined
2|小红|3|undefined

#### 参考
[https://blog.csdn.net/bigtree_3721/article/details/68942273](https://blog.csdn.net/bigtree_3721/article/details/68942273)
[https://www.cnblogs.com/chinesern/p/7592537.html](https://www.cnblogs.com/chinesern/p/7592537.html)