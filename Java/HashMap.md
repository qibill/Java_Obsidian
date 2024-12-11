## put方法逻辑
1. 如果HashMap未被初始化，则初始化（延迟加载）
2. 对Key求Hash值，然后再计算下标
3. 如果没有碰撞，直接放入桶中
4. 如果碰撞了，以链表的方式链接到后面
5. 如果链表长度超过阀值(**TREEIFY_THRESHOLD** = *8*)，就把链表转成红黑树
6. 如果链表长度低于阀值(**UNTREEIFY_THRESHOLD** = *6*)，就把红黑树转回链表
7. 如果节点已经存在就替换旧值
8. 如果桶满了（容量16*加载因子0.75），就需要扩容resize(扩容2倍后重排)


```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        //1. 如果HashMap未被初始化，则初始化
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        //2. 对Key求Hash值，然后再计算下标
        if ((p = tab[i = (n - 1) & hash]) == null)
            //3. 如果没有碰撞，直接放入桶中
            tab[i] = newNode(hash, key, value, null);
        else {
            Node<K,V> e; K k;
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {
                        //4. 如果碰撞了，以链表的方式链接到后面
                        p.next = newNode(hash, key, value, null);
                        //5. 如果链表长度超过阀值(**TREEIFY_THRESHOLD** = *8*)，就把链表转成红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    //7. 如果节点已经存在就替换旧值
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        //8. 如果桶满了（容量16*加载因子0.75），就需要扩容resize(扩容2倍后重排)
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```


## 扩容机制
```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;//首次初始化后table为Null
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;//默认构造器的情况下为0
    int newCap, newThr = 0;
    if (oldCap > 0) {//table扩容过
         //当前table容量大于最大值得时候返回当前table
         if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
        //table的容量乘以2，threshold的值也乘以2           
        newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
    //使用带有初始容量的构造器时，table容量为初始化得到的threshold
    newCap = oldThr;
    else {  //默认构造器下进行扩容  
         // zero initial threshold signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
    //使用带有初始容量的构造器在此处进行扩容
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            HashMap.Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                // help gc
                oldTab[j] = null;
                if (e.next == null)
                    // 当前index没有发生hash冲突，直接对2取模，即移位运算hash &（2^n -1）
                    // 扩容都是按照2的幂次方扩容，因此newCap = 2^n
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof HashMap.TreeNode)
                    // 当前index对应的节点为红黑树，这里篇幅比较长且需要了解其数据结构跟算法，因此不进行详解，当树的个数小于等于UNTREEIFY_THRESHOLD则转成链表
                    ((HashMap.TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    // 把当前index对应的链表分成两个链表，减少扩容的迁移量
                    HashMap.Node<K,V> loHead = null, loTail = null;
                    HashMap.Node<K,V> hiHead = null, hiTail = null;
                    HashMap.Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            // 扩容后不需要移动的链表
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            // 扩容后需要移动的链表
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        // help gc
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        // help gc
                        hiTail.next = null;
                        // 扩容长度为当前index位置+旧的容量
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

## 有效减少碰撞
1. 扰动函数： 促使元素位置分布均匀，减少碰撞机率
2. 使用final对象，并采用合适的equals()和hashCoe()方法
 
### jdk1.7与jdk1.8中HashMap区别

1. 最重要的一点是底层结构不一样，1.7是数组+链表，1.8则是数组+链表+红黑树结构;

2. jdk1.7中当哈希表为空时，会先调用inflateTable()初始化一个数组；而1.8则是直接调用resize()扩容;

3. 插入键值对的put方法的区别，1.8中会将节点插入到链表尾部，而1.7中是采用头插；（防止环型链表）

4. jdk1.7中的hash函数对哈希值的计算直接使用key的hashCode值，而1.8中则是采用key的hashCode异或上key的hashCode进行无符号右移16位的结果，避免了只靠低位数据来计算哈希时导致的冲突，计算结果由高低位结合决定，使元素分布更均匀；

![image](https://img-blog.csdn.net/20180721235229659?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3YxMjM0MTE3Mzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

5. 扩容时1.8会保持原链表的顺序，而1.7会颠倒链表的顺序；而且1.8是在元素插入后检测是否需要扩容，1.7则是在元素插入前；
6. jdk1.8是扩容时通过```hash&cap==0```将链表分散，无需改变hash值，而1.7是通过更新hashSeed来修改hash值达到分散的目的；

7. 扩容策略：1.7中是只要不小于阈值就直接扩容2倍；而1.8的扩容策略会更优化，当数组容量未达到64时，以2倍进行扩容，超过64之后若桶中元素个数不小于7就将链表转换为红黑树，但如果红黑树中的元素个数小于6就会还原为链表，当红黑树中元素不小于32的时候才会再次扩容。


