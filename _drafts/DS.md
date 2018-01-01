# Array

存储及随机访问一连串对象最有效率

Arrays的API

*   equals
*   fill
*   sort
*   binarySearch
*   System.arrayCopy

# Collection、Map

若编写程序时不知道究竟需要多少对象，需要在空间不足时自动扩增容量，则需要使用容器类库，array不适用。

Collection每个位置存储一个元素，Map存储的是键值对，像一个小型数据库。

![WX20180101-163108@2x](https://ws3.sinaimg.cn/large/006tNc79gy1fn16vqobecj31060jswg1.jpg)

## SparseArray

## Map

## HashMap

是一个连续的链表数组（Bucket也可能是红黑树）

| HashMap                                  | HashTable   |
| ---------------------------------------- | ----------- |
| 非同步                                      | 同步          |
| 允许null，但是get方法仍会返回null，所以判断是否存在key要用containsKey | 不允许null     |
| Iterator                                 | Enumeration |
|                                          |             |



### ArrayMap

使用两个数组进行存储

![ArrayMap](http://lvable.com/wp-content/uploads/2015/08/QQ%E6%88%AA%E5%9B%BE20150824001700.png)

达不到HashMap O(1)的查找时间（寻找HASH采用二分法查找）

# 并发

## ConcurrentHashMap

相比Collections.synchronizedMap，锁的粒度更小，只是锁住即将修改的Bucket就可以了。

## CopyOnWriteArrayList

通过增加写时复制语义来避免并发访问引起的问题，也就是说任何修改操作都会在底层创建一个列表的副本，已有的迭代器不会碰到意料之外的修改。

通过牺牲空间获得时间。

## BlockingQueue

