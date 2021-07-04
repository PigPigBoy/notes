## 定义
- n叉B+Tree最多有`n`个key，而BTree最多含有`n-1`个key
- `B+Tree的叶子节点保存所有key信息`，依key大小顺序排序。非叶子节点的key只起到索引作用，不存储数据信息。
- 所有非叶子节点都可以看做是key的索引部分

![](../others/pictures/B+Tree.jpg)
![](/others/pictures/BTree.jpg)

- B+树只有叶子节点保存了key的信息，即任何查询都要从root走到叶子，查询效率更稳定