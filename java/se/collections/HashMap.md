### 插入数据的过程
1.插入a，计算hash=97，计算下标值idx=97%8=1，即在下标为1的位置插入a

2.插入b，计算hash=98，计算下标值idx=98%8=2，即在下标为2的位置插入b

3.插入i，计算hash=105，计算下标值idx=105%8=1，与a发生冲突，生成链表。在jdk7中，b放在a的前面，即最新插入的放在链表的头部；在jdk8中是把最新插入的放在链表的尾部。【jdk7把元素插在链表头部会存在<font color=red>并发死链</font>问题】

4.随着插入的元素越来越多，数组元素超过了阈值（0.75）会发生扩容，产生一个新的数组，重新计算桶下标。
- idx_a = 97 % 16 = 1
- idx_b = 98 % 16 = 2
- idx_i = 105 % 16 = 9

### 并发死链
- 贼牛逼，直接OOM，程序崩溃
- 只有在<font color=red>jdk7</font>+<font color=red>多线程</font>情况下存在
- 究其原因，还是在多线程情况下使用了非安全的map集合
- jdk8虽然调整了扩容算法，不再将元素插入链表头，而是保持与扩容器一样的顺序，但并不意味着在多线程下能否安全扩容，还会出现其他问题，比如扩容丢数据。