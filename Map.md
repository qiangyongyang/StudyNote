# HashMap:

**一.hashmap的主要参数都有哪些？**

​	1.容量capadcity：数组长度：；默认值为16

​	2.极限容量，表示hashmap能承受的最大桶容量为2的30次方，超过这个容量将不再扩容。碰撞

​	3.负载因子（loadfactor，默认0.75）

​	4.阈值，算法为capacity*loadfactory，大致当map中entry数量大于此阈值时进行扩容（1.8）

**二.hashmap的数据结构是什么样子的？**

​	*1.数组+链表（java7）：*

​		每个链表的结点是嵌套类 Entry 的实例，Entry 包含四个属性：key, value, hash 值和用于链表的next.

​		``````static class Entry<K,V> implements Map.Entry<K,V> {``````     

​			`````` final K key; ``````   

​			`````` V value;    ``````              

​			``````Entry<K,V> next;    ``````    

​			``` int hash; }```

​	*2.数组+链表+红黑树（java8）：*    

​	         根据 Java7 HashMap 的介绍，我们知道，查找的时候，根据 hash 值我们能够快速定位到数组的具体下 

​        标，但是之后的话，需要顺着链表一个个比较下去才能找到我们需要的，时间复杂度取决于链表的长度。                    

​               为了降低这部分的开销，在 Java8 中，当链表中的元素超过了 8 个以后，会将链表转换为红黑树，在这些         

​	位置进行查找的时候可以降低时间复杂度为 O(logN)。   

**三.hash算法：**

​	*1.计算key的哈希值：*

​		key的hashcode值先右移16位，再与hashcode值进行异或操作，即不求进位只求按位相加的值

​	*2.计算在数组中的位置：*

​		上面算出的值对数组容量取模。

​       $\color{red}{通过扰动函数最终的目的是让散列分布地更加均匀。}$

**四.存取过程：**

​	*1.存过程：（java7）*：

​		1.1 判断如果为空table，先对table进行构造:

​			在第一个元素插入 HashMap 的时候做一次数组的初始化，就是先确定初始的数组大小**(2的n次方)**，并计算数组扩容的阈值。

​		1.2 判断key是否为null：

​			1.2.1为null也可以存，null的key一定放在table的0号位置
        	1.3 算出key的hash值：   int hash = hash(key)
                       根据hash值算出在table中的位置

​                1.4 放入K\V，遍历链表,如果位置上存在相同key，进行替换value为新的，且将替换的旧的value返回；若不存在，则插在链表首部。**（java8是插入尾部）**

​		1.5 增加一个entry，有两种情况：1、如果此位置存在entry，将此位置变为插入的entry，且将插入 entry的next节点变为原来的entry**（存在相同key）**；2、如果此位置不存在entry则直接插入新的entry 。**（不存在相同key）**  

​    *2.取过程：（java7):*

​		1.1 判断key是否为null：

​			1.2.1key为null，在table的0号位置取
        	1.2 算出key的hash值：   int hash = hash(key)
                       根据hash值算出在table中的位置

​		1.3 遍历链表，寻找key相等的那个Entry

   *3.存过程：（java8）*：

​		插入时需要判断结点是链表节点还是红黑树结点？链表则插入尾部：红黑树的插入方法

​		插入完成后需要判断链表中的个数是否大于8？转化成红黑树：继续

*4.取过程：（java8)  :*

​                4.1 计算 key 的 hash 值，根据 hash 值找到对应数组下标: hash & (length-1)

​		4.2 判断数组该位置处的元素是否刚好就是我们要找的，如果不是，走第三步

​		4.3判断该元素类型是否是 TreeNode，如果是，用红黑树的方法取数据，如果不是，走第                    	  	                四步

​		4.4遍历链表，直到找到相等(==或equals)的 key

**五.扩容和碰撞：**

​	1.当数组中的元素大于阈值。

​	2.当插入时发生碰撞

​	 $\color{red}{二者缺一不可。}$







# 		ConcurrentHashMap

**一.JDK1.7：**

- CocurrentHashMap 是由 Segment 数组和 HashEntry 数组和链表组成
- 通过把整个Map分为N个Segment，可以提供相同的线程安全，但是效率提升N倍，默认提升16倍。(读操作不加锁，由于HashEntry的value变量是 volatile的，也能保证读取到最新的值。)


- Segment 是基于重入锁（ReentrantLock）：一个数据段竞争锁。每个 HashEntry 一个链表结构的元素，利用 Hash 算法得到索引确定归属的数据段，也就是对应到在修改时需要竞争获取的锁。ConcurrentHashMap 支持 CurrencyLevel（**16**）的线程并发。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment
- 核心数据如 value，以及链表都是 volatile 修饰的，保证了获取时的可见性
- 首先是通过 key 定位到 Segment，之后在对应的 Segment 中进行具体的 put 操作如下：
  - 将当前 Segment 中的 table 通过 key 的 hashcode 定位到 HashEntry。
  - 遍历该 HashEntry，如果不为空则判断传入的  key 和当前遍历的 key 是否相等，相等则覆盖旧的 value
  - 不为空则需要新建一个 HashEntry 并加入到 Segment 中，同时会先判断是否需要扩容
  - 最后会解除在 1 中所获取当前 Segment 的锁。
- 虽然 HashEntry 中的 value 是用 volatile 关键词修饰的，但是并不能保证并发的原子性，所以 put 操作时仍然需要加锁处理

首先第一步的时候会尝试获取锁，如果获取失败肯定就有其他线程存在竞争，则利用 scanAndLockForPut() 自旋获取锁。

- 尝试自旋获取锁
- 如果重试的次数达到了 MAX_SCAN_RETRIES 则改为阻塞锁获取，保证能获取成功。最后解除当前 Segment 的锁



**二.JDK1.8:**

- CocurrentHashMap 是由 数组+链表+红黑树组成
- CocurrentHashMap 抛弃了原有的 Segment 分段锁，采用了 `CAS + synchronized` 对数组元素加锁来保证并发安全性。其中的 `val next` 都用了 volatile 修饰，保证了可见性。CAS有3个操作数，内存值 V、旧的预期值 A、要修改的新值 B。当且仅当预期值 A 和内存值 V 相同时，将内存值V修改为 B，否则什么都不做。








#		TreeMap

1.本质上是一颗红黑树，通过实现了SortMap接口，能够把它保存的键值对根据 key 排序，从而保证 TreeMap 中所有键值对处于有序状态。

2.在TreeMap的put()的实现方法中主要分为两个步骤:

​	2.1 构建**排序二叉树：**

​		2.1.1、以根节点为初始节点进行检索。

​		2.1.2、与当前节点进行比对，若新增节点值较大，则以当前节点的右子节点作为新的当前节点。否则以

​			     当前节点的左子节点作为新的当前节点。

​		2.1.3、循环递归2步骤知道检索出合适的叶子节点为止。

​		2.1.4、将新增节点与3步骤中找到的节点进行比对，如果新增节点较大，则添加为右子节点；否则添加为

​			    左子节点

​	2.2 通过**左旋，右旋，变色**平衡二叉树。



A:自然排序：要在自定义类中实现Comparerable<T>接口  ，并且重写compareTo方法

B:比较器排序：在自定义类中实现Comparetor<t>接口，重写compare方法

# 		

#		LinkedListHashMap


1.本质为HashMap+双向链表

2.通过**插入排序**（就是你 put 的时候的顺序是什么，取出来的时候就是什么样子）和**访问排序**（改变排序把访问过的放到底部）让键值有序。

 



​		



​		

​	











