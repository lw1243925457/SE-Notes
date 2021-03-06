# STL 容器笔面试总结
***
&ensp;&ensp;&ensp;&ensp;本文章不具体介绍容器该如何使用，只说一些可能在笔面试中可能会被问到的细节部分

## 序列式容器
### Vector
- vector<T>(size_t n, T val):可以自动填充
- 优缺点：vector的定位为一个动态数组，其优点的在能过随机存储和在尾部操作迅速；缺点是头部和中部操作较慢
- 与string的比较：string与vector是比较像的，不同点是vector不可以调用reverse来缩减容量，但string可以
- *插入和删除会使迭代器失效*
- pop_back：结束标记前移，调用destroy,只是调用其析构函数，没有调用free释放内存
- erase：先使用copy全局函数（STL算法里面的）将删除区间后面的元素向前移动，然后调用destroy析构无效区间
- insert:有三种情况
    - 1.剩余空间足够且插入位置后的元素数量大于插入元素数量：
        - 插入点后的元素整体后移（移动起始地址为原结束地址）
        - 插入点元素移动到原结束位置
        - 插入新元素
    - 2.剩余空间足够且插入位置后的元素数量小于插入元素数量：
        - 先将一个新元素放入结束地址
        - 将插入点元素后的旧元素整体后移到结束地址后
        - 插入新元素
    - 3.剩余空间不足：
        - 分配新的内存，开辟新空间、安装插入点前旧元素、新元素、插入点后新元素复制进行新的空间中，然后删除原空间、释放内存

##### 疑问？
- 1.在扩充中，内存的配置是重复分配还是在原继承上增加，不必将原来的数据复制到新开辟的空间中？
    - 回答：在扩充中不是在原空间的基础上进行扩充，而是重复分配内存，元素移动、释放空间

### Deque
- 优缺点：相对vector来说，多了一个在头部的迅速操作；它也是一个动态数组，但其结构相对来说有一点复杂，后面说；其也支持随机存储，但由于其的数据结构，导致存取中需要经过一个中间部分，所以随机存取和迭代操作相对于vector有一点慢；但其在头部和尾部能进行迅速的操作，在中部同vector一样较慢；还有就是Deques不支持reverse操作
- 对deque进行排序操作时，为了效率可以把其复制到vector中排序，排完以后复制回来
- deque的数据结构相比vector和List更为复杂，它可以说是一个真正的动态空间，不是vector的伪动态空间，其主控由一连续的有指针组成的的连续空间构成。每个指针指向一个缓冲区，而这个缓冲区就是放置数据的地方
- deque主控去有两个迭代器start和finish，每个迭代器有四个内容：开始位置，当前位置、结束位置、指向当前缓冲区的主控节点，还是比较复杂的，相比vector来说，其操作稍费时间

### List
- 优缺点：数据结构是双向链表；不支持数据的随机访问，也就是对于数据的读取比较慢；但其在在头部、中部、尾部的插入和删除操作比较快，因为改变指针即可；
- *插入和删除操作除了被删除的部分的指针失效外，其他的不失效*
- List是环状双向链表，其中在内部定义一个node的空白指针，则链表的结尾就是node，开始就是node的下一节点，这是一个编程技巧吧
- 使用alloc空间配置器，在此基础上封装了一个便于操作的空间分配，但其主要的思想也是把空间分配和对象的构造和析构分开
- insert：生成一个节点、节点前指向node，后指向node的前一个节点，node的前一个节点的前指针指向节点，node的后指针指向节点（此处的node的插入节点）
- erase：获取删除节点的前后指针，删除节点的前一个节点的前指针指向删除节点的后一个节点，删除节点后的节点的后指针指向删除节点前的节点，然后删除节点

### stack
- 以deque为底部容器封装实现，没有迭代器

### queue
- 以deque为底部容器封装实现，没有迭代器

### Set 和 Map
- 优缺点：内部的数据结构是红黑树，所以有点是搜索快，修改和删除慢

## 容器的插入问题
- vector插入元素会发生什么：
