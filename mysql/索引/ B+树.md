# B+树

## 怎样的索引的数据结构是最好的

MYSQL 的数据是持久化的，索引和数据都是保存到磁盘上的，即使设备断电了，数据也不会丢失。

但是磁盘是一个读取速度比内存慢上万倍，甚至几十万倍的设备。

磁盘读写的最小单位是扇区，扇区的大小只有 512B 大小，操作系统一次会读写多个扇区，所以操作系统的最小读写单位是块（Block）。Linux 中的块大小为 4KB，也就是一次磁盘 I/O 操作会直接读写 8 个扇区。

由于数据库的索引是保存到磁盘上的，因此当我们通过索引查找某行数据的时候，就需要先从磁盘读取索引到内存，再通过索引从磁盘中找到某行数据，然后读入到内存，也就是说查询过程中会发生多次磁盘 I/O，而磁盘 I/O 次数越多，所消耗的时间也就越大。

所以，我们希望索引的数据结构能在尽可能少的磁盘的 I/O 操作中完成查询工作，因为磁盘 I/O 操作越少，所消耗的时间也就越小。

另外，MySQL 是支持范围查找的，所以索引的数据结构不仅要能高效地查询某一个记录，而且也要能高效地执行范围查找。


所以，要设计一个适合 MySQL 索引的数据结构，至少满足以下要求：

能在尽可能少的磁盘的 I/O 操作中完成查询工作；
要能高效地查询某一个记录，也要能高效地执行范围查找；
分析完要求后，我们针对每一个数据结构分析一下。

## 二分查找树

二叉查找树解决了连续结构插入新元素开销很大的问题，同时又保持着天然的二分结构。

但是二叉查找树由于存在退化成链表的可能性，会使得查询操作的时间复杂度从 O(logn) 升为 O(n)。

而且会随着插入的元素越多，树的高度也变高，意味着需要磁盘 IO 操作的次数就越多，这样导致查询性能严重下降，再加上不能范围查询，所以不适合作为数据库的索引结构。

## 自平衡二叉树

为了解决二叉查找树会在极端情况下退化成链表的问题，后面就有人提出平衡二叉查找树（AVL 树）。

主要是在二叉查找树的基础上增加了一些条件约束：每个节点的左子树和右子树的高度差不能超过 1。也就是说节点的左子树和右子树仍然为平衡二叉树，这样查询操作的时间复杂度就会一直维持在 O(logn) 。

不管平衡二叉查找树还是红黑树，都会随着插入的元素增多，而导致树的高度变高，这就意味着磁盘 I/O 操作次数多，会影响整体数据查询的效率。

## B树

B 树的每一个节点最多可以包括 M 个子节点，M 称为 B 树的阶，所以 B 树就是一个多叉树，从而降低树的高度。

而如果同样的节点数量在平衡二叉树的场景下，树的高度就会很高，意味着磁盘 I/O 操作会更多。所以，B 树在数据查询中比平衡二叉树效率要高。

但是 B 树的每个节点都包含数据（索引+记录），而用户的记录数据的大小很有可能远远超过了索引数据，这就需要花费更多的磁盘 I/O 操作次数来读到「有用的索引数据」。

## B+树

B+树就是对B树做了一个升级，MySQL中索引的数据结构就是采用了B+树

B+树 与 B树 的区别是：
* 叶子节点才会存放实际的数据（索引+记录），非叶子节点只存放索引
* 所有索引都会在叶子节点出现，叶子节点之间构成一个有序链表
* 非叶子节点的索引也会同时存在在子节点中，并且是在子节点中所有索引的最大（或最小）
* 非叶子节点中有多少个子节点，就有多少个索引

### 单点查询

B树在进行单个索引查询时，最快可以在O(1)时间内查到，从平均数据来看会比B+树快一点

但是B树查询波动会比较大，有的时候访问到了非叶子节点就查到了索引数据，而有的时候需要访问到叶子节点才能查到实际的数据

B+ 树的非叶子节点不存放实际的记录数据，仅存放索引，因此数据量相同的情况下，相比存储即存索引又存记录的 B 树，B+树的非叶子节点可以存放更多的索引，因此 B+ 树可以比 B 树更「矮胖」，查询底层节点的磁盘 I/O次数会更少

### 插入和删除效率

B+ 树的插入也是一样，有冗余节点，插入可能存在节点的分裂（如果节点饱和），但是最多只涉及树的一条路径。而且 B+ 树会自动平衡，不需要像更多复杂的算法，类似红黑树的旋转操作等。

因此，B+ 树的插入和删除效率更高。

### 范围查询

因为 B+ 树所有叶子节点间还有一个链表进行连接，这种设计对范围查找非常有帮助。

## MySQL 中的 B+ 树

但是 Innodb 使用的 B+ 树有一些特别的点，比如：
* B+ 树的叶子节点之间是用「双向链表」进行连接，这样的好处是既能向右遍历，也能向左遍历。
* B+ 树点节点内容是数据页，数据页里存放了用户的记录以及各种信息，每个数据页默认大小是 16 KB。

Innodb 根据索引类型不同，分为聚集和二级索引。他们区别在于，聚集索引的叶子节点存放的是实际数据，所有完整的用户记录都存放在聚集索引的叶子节点，而二级索引的叶子节点存放的是主键值，而不是实际数据。



