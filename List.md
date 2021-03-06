					# 			List

**一.ArrayLIst：**

​	ArrayList只不过是对数组的包装，因为数组在内存中分配时必须指定长度，且一旦分配好后便无法再增加长度，即不可能在原数组后面再接上一段的。ArrayList之所以可以一直往里添加，是因为它内部做了处理。当底层数组填满后，它会再分配一个更大的新的数组**（1.5倍）**，把原数组里的元素拷贝过来，然后把原数组抛弃掉。使用新的数组作为底层数组来继续存储。

​	所以ArrayList就实现了RandomAccess（随机访问）接口，而LInkedList就没有。

**二.LinkedList：**

​	LinkedList也实现了List接口，也可以按索引访问元素，表面上用起来感觉差不多，但是其底层却有天壤之别。与数组一下子分配好指定长度的空间备用不同，**链表不会预先分配空间**。而是在每次添加一个元素时临时专门为它自己分配一个空间。因为内存空间的分配是由操作系统完成的，可以说**每次分配的位置都是随机的**，并没有确定的规律。所以说链表的每个元素都在完全不同的内存地址上，那我们该如何找到它们呢？唯一的做法就是把每个元素的内存地址都要保存起来。怎么保存呢？那就**让上一个元素除了存储具体的数据之外，也存储一份下一个元素在内存中的地址**。整个就像前后按顺序依次相连的一条链，我们只要保存第一个元素的内存地址，就可以顺藤摸瓜找到所有的元素。

​	可见按索引访问链表元素时，必须从头一个个遍历，而且链表越长，位置越靠后，所需花费的时间就越长。所以按索引访问链表元素的时间复杂度就是O(n)，n为链表的长度。也说明了**链表不支持随机访问**。

​	所以ArrayList就实现了RandomAccess（随机访问）接口，而LInkedList就没有。