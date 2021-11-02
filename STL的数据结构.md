# STL的数据结构

### Multiset

#### 数据结构

STL中的set和multiset通过平衡二叉树实现。

**平衡二叉树定义**
平衡二叉树（Balanced Binary Tree），它是一棵空树，或者是具有以下性质：

- 它的左右两个子树的深度差的绝对值不超过1
- 它的左右两个子树也分别是平衡二叉树

**链接：排序二叉树定义**

- 若左子树不空，则左子树上所有节点的值均小于它的根节点的值；
- 若右子树不空，则右子树上所有节点的值均大于它的根节点的值；
- 左、右子树也分别为二叉排序树。

#### 操作函数

##### 构造、析构、拷贝函数

| operator              | action                                                     |
| --------------------- | ---------------------------------------------------------- |
| set c                 |                                                            |
| set c(op)             |                                                            |
| set c2(c1)            |                                                            |
| set c(begin, end)     | 在区间[begin, end)内的所有元素产生一个set/multiset         |
| set c(begin, end, op) | 以op的大小规则在[begin, end)内所有元素产生一个set/multiset |
| c.~set()              | 调用set的析构函数，销毁set                                 |

#####  非变动性函数

| operator     | action                     |
| ------------ | -------------------------- |
| c.size()     | 返回当前的元素数量         |
| c.empty()    | 判断大小是否为零           |
| c.max_size() | 返回能容纳的元素的最大数量 |

##### 特殊的搜寻函数

​	sets和multisets在元素快速搜寻方面做了优化设计，提供了特殊的搜寻函数，所以应优先使用这些搜寻函数，可获得对数复杂度，而非STL的线性复杂度。比如在1000个元素搜寻，对数复杂度平均十次，而线性复杂度平均需要500次。

| operator          | action                                                |
| ----------------- | ----------------------------------------------------- |
| count(elem)       | 返回元素值为elem的个数                                |
| find(elem)        | 返回元素值为elem的第一个元素的位置，如果没有返回end() |
| lower_bound(elem) | 元素值>=elem的第一个元素的位置                        |
| upper_bound(elem) | 元素值>elem的第一个元素的位置                         |
| equal_range(elem) | 元素值==elem的区间                                    |

##### 赋值函数

| operator     | action                 |
| ------------ | ---------------------- |
| c1==c2       | 将c2的元素全部赋值给c1 |
| c1.swap(c2)  | 将c1和c2的元素互换     |
| swap(c1, c2) | 同上                   |

##### 迭代器相关元素

sets和multisets的迭代器是双向迭代器，对迭代器操作而言，所有的元素都被视为常数，可以确保你不会人为改变元素值，从而打乱既定顺序，所以无法调用变动性算法，如remove()。

| operator   | action                                                     |
| ---------- | ---------------------------------------------------------- |
| c.begin()  | 返回一个随机存取迭代器，指向第一个元素                     |
| c.end()    | 返回一个随机存取迭代器，指向最后一个元素的下一个位置       |
| c.rbegin() | 返回一个逆向迭代器，指向逆向迭代的第一个元素               |
| c.rend()   | 返回一个逆向迭代器，指向逆向迭代的最后一个元素的下一个位置 |

##### 安插和删除元素

必须保证参数有效，迭代器必须指向有效位置，序列起点不能位于中点之后，不能从空指针删除元素。

| operator             | action                                                       |
| -------------------- | ------------------------------------------------------------ |
| c.insert(elem)       | 插入一个elem的副本(浅拷贝)，返回新元素的位置，无论插入成功与否 |
| c.insert(pos, elem)  | 安插一个elem元素副本，返回新元素位置，pos为收索起点，加快搜索速度 |
| c.insert(begin, end) | 将[begin, end)中的所有元素安插到c，没有返回值                |
| c.erase(elem)        |                                                              |
| c.erase(beg,end)     | 移除区间[beg,end)所有元素，无返回值。                        |
| c.clear()            | 容器清空                                                     |



### unorderd_map







### unordered_set





### Vector

| 常用的方法                         | 实现的功能                   |
| ---------------------------------- | ---------------------------- |
| iterator begin();                  | 返回指向首元素的指针         |
| iterator end();                    | 返回指向末元素的后一个的指针 |
| size_type size() const;            | 返回vector的长度             |
| size_type capacity() const;        | 返回vector的容量             |
| bool empty() const;                | 返回vector是否为空           |
| void push_back(const T& x);        | 在vector尾部插入元素         |
| void pop_back();                   | 删除最后一个元素             |
| iterator erase(iterator position); | 删除某个指针指向的元素       |
| void resize(size_type new_size);   | 改变vector长度               |
| void clear();                      | 清除vector                   |

```cpp
#include <iostream>
#include <vector>
using namespace std;

void print(vector<int> nums)
{
    for (int num : nums)
        cout << num << " ";
    cout << endl;
}

int main()
{
    vector<int> nums{1, 2, 3, 4, 5, 6};
    vector<int>::iterator it = nums.begin()+3;
    nums.erase(it);
    cout << nums[3] << endl; // 5
    print(nums); // 1 2 3 5 6
    cout << nums.size() << endl; // 5
    nums.resize(3);
    print(nums); // 1 2 3
    nums.pop_back();
    print(nums); // 1 2
    vector<int>::iterator it1 = nums.begin()+2;
    nums.erase(it1);
    print(nums); // 2
}
```

### Stack

stack的底层使用list实现，没有迭代器，不可以遍历

| 常用的方法                      | 实现的功能     |
| ------------------------------- | -------------- |
| bool empty() const;             | 返回栈是否为空 |
| size_type size() const;         | 返回栈的大小   |
| reference top();                | 返回栈顶元素   |
| void push(const value_type& x); | 元素压栈       |
| void pop();                     | 元素出栈       |





### Queue

queue底层使用list实现，没有迭代器，不可以遍历

| 常用的方法                      | 实现的功能       |
| ------------------------------- | ---------------- |
| bool empty() const;             | 返回队列是否为空 |
| size_type size() const;         | 返回队列的大小   |
| reference front();              | 返回队首元素     |
| reference back();               | 返回队尾元素     |
| void push(const value_type& x); | 元素入队         |
| void pop();                     | 元素出队         |



### Set

set中所有元素会根据键值自动排序，set中不允许有相同键值元素。

set底层使用红黑树实现，不是线性的数据结构，采用二叉平衡搜索机制，set的迭代器是const类型，不能写入修改。

set中默认升序，遍历采用迭代器，迭代器重载过操作符对而二叉树实现中序遍历。

[C++中set用法详解_Donny's Blog-CSDN博客_c++ set](https://blog.csdn.net/yas12345678/article/details/52601454)

| 常用的方法                              | 实现的功能                                                   |
| --------------------------------------- | ------------------------------------------------------------ |
| iterator begin() const;                 | 返回指向首元素的指针                                         |
| iterator end() const;                   | 返回指向末元素的后一个的指针                                 |
| size_type size() const;                 | 返回set的长度                                                |
| max_size();                             | 返回set容器可能包含的元素最大个数                            |
| void swap(set<Key, Compare, Alloc>& x); | 交换两个set之间的元素                                        |
| bool empty() const;                     | 返回set是否为空                                              |
| count();                                | 在set中出现的次数，0或者1                                    |
| erase(iterator)                         | 删除迭代器指向的数值                                         |
| erase(first, second)                    | 删除迭代器first和second之间的值                              |
| erase(key_value)                        | 删除键值的值                                                 |
| find(key_value)                         | 返回定位器，如果没有找到就是end()                            |
| insert(key_value)                       | 插入键值对                                                   |
| insert(first, second)                   | 将定位器first到second之间的元素插入到set中                   |
| lower_bound(key_value)                  | 返回第一个大于等于key_value的定位器                          |
| upper_bound(key_value)                  | 返回最后一个大于等于key_value的定位器返回最后一个大于等于key_value的定位器 |



###  Map

| 常用的方法              | 实现的功能                                                   |
| ----------------------- | ------------------------------------------------------------ |
| iterator begin();       | 返回指向首元素的指针                                         |
| iterator end();         | 返回指向末元素的后一个的指针                                 |
| iterator rbegin();      | 和end()一样                                                  |
| iterator rend();        | 返回首元素的前一个的指针                                     |
| size_type size() const; | 返回map的长度                                                |
| bool empty() const;     | 返回map是否为空                                              |
| count();                | 在map中出现的次数，也是0或者1                                |
| erase(iterator)         | 删除迭代器指向的数值                                         |
| erase(key_value)        | 删除键值的值                                                 |
| find(key_value)         | 返回定位器，如果没有找到就是end()                            |
| insert(key_value)       | 插入键值对                                                   |
| insert(first, second)   | 将定位器first到second之间的元素插入到set中                   |
| lower_bound(key_value)  | 返回第一个大于等于key_value的定位器                          |
| upper_bound(key_value)  | 返回最后一个大于等于key_value的定位器返回最后一个大于等于key_value的定位器 |
