# Algorithm in LeetCode

## 二叉树的三种遍历

二叉树的中序遍历是最常见的，因为二叉搜索树的中序遍历就是有序数组。

### 二叉树的结构体定义

```c++
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
```

遍历的实现如果使用递归的方式，就非常容易，与遍历的定义是一致的，不过迭代版本的实现就会复杂一些。

迭代写法的实现思路是通过栈来模拟递归的写法，通过这种方式来去掉递归调用，相对来说不太直观。其中，后序遍历的实现是最为复杂的，在 LeetCode 中是 hard 难度的，其余两个是 medium 难度。

递归写法的套路，这里为了减少复制，采用了辅助函数，参数中包含一个输出参数，并且在函数传参数时，要采用引用传参，这样就会直接在全局变量中修改，这是C++常用的一种传出结果的方式。

### 递归写法

```c++
void helper(TreeNode* root, vector<int>& res) {  // res 是结果
    // 递归终止条件
    if (!root) {
        return;
    }

    // 这里根据遍历的顺序来进行调整，即可实现三种遍历
    res.push_back(root->val);
    helper(root->left, res);
    helper(root->right, res);
}
```

#### 递归前序遍历

```c++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        helper(root, res);
        return res;
    }

    void helper(TreeNode* root, vector<int>& seq) {
        if (!root) {
            return;
        }
        seq.push_back(root->val);
        helper(root->left, seq);
        helper(root->right, seq);
    }
};
```

push_back(root->val);可以换成任意对于根结点的操作。

#### 递归中序遍历

```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
       vector<int> res;
       helper(root, res);
       return res; 
    }

    void helper(TreeNode* root, vector<int>& res) {
        if (!root) {
            return;
        }
        helper(root->left, res);
        res.push_back(root->val);
        helper(root->right, res);
    }
};
```

#### 递归后序遍历

```c++
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        helper(root, res);
        return res;
    }
    void helper(TreeNode* root, vector<int>& res) {
        if (!root) {
            return;
        }

        helper(root->left, res);
        helper(root->right, res);
        res.push_back(root->val);
    }
};
```

### 迭代写法(栈)

基本模板

```c++
stack<TreeNode*> s;
s.push(x);  // 初始化栈
while (!s.empty()) {  // 栈非空就继续遍历
    TreeNode* cur = s.top();
    s.pop();
    // do something
    
    s.push(x);  // 填充栈
}
```

#### 迭代前序遍历

```C++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        if (!root) {
            return {};
        }
        vector<int> res;
        stack<TreeNode*> s;
        s.push(root);
        while (!s.empty()) {
            TreeNode* cur = s.top();
            s.pop();
            res.push_back(cur->val);
            // 注意：先右后左
            if (cur->right) {
                s.push(cur->right);
            }
            if (cur->left) {
                s.push(cur->left);
            }
        }
        return res;
    }C
};
```

#### 迭代中序遍历

```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        if (!root) {
            return {};
        }
        vector<int> res;
        stack<TreeNode*> s;
        TreeNode* cur = root;
        while (cur || !s.empty()) {
            while (cur) { // 寻找最左侧的节点
                s.push(cur);
                cur = cur->left;
            }
            cur = s.top();
            s.pop();
            res.push_back(cur->val);
            cur = cur->right;
        }
        return res; 
    }
};
```

#### 迭代后序遍历

```c++
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        if (!root) {
            return {};
        }
        vector<int> res;
        stack<TreeNode*> s;
        s.push(root);
        while (!s.empty()) {
            TreeNode* cur = s.top();
            s.pop();
            res.push_back(cur->val);
            // 注意：先左后右
            if (cur->left) {
                s.push(cur->left);
            }

            if (cur->right) {
                s.push(cur->right);
            }
        }
        reverse(res.begin(), res.end());  // 翻转
        return res;
    }
};
```



[二叉树的遍历（深度优先+广度优先） - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1632481)





## 动态规划

Those who cannot remember the past are condemned to repeated it. 					--------------Dynamic Programming

**动态规划的核心问题就是记录下来之前已经解决了的子问题的解。**



### 动态规划的两种形式

记住子问题的解的问题有两种：

1. 自底向上
2. 自顶向上的备忘录写法

##### 例题：Fibonacci Sequence/ LeetCode 70. climb stairs

###### 递归写法（自底向上）

```c++
class Solution {
public:
    int climbStairs(int n) {
        if(n<=2)
            return n;
        return climbStairs(n-1)+climbStairs(n-2);
    }
};
```

递归就是自底向上，底下的大量节点需要被多次执行，尤其是n=1和n=2两个参数，因为每次执行都要保留上文返回的参数，所以空间的占用很高，时间开销也很高，如果将结果直接存储起来可以节省大量的时间。

###### 自顶向上的备忘录写法



```C++
class Solution {
public:
    int climbStairs(int n) {
        if(n<=2)
            return n;
        
        vector<int> dp(n+1, 1);
        for(int i(2); i<n+1; ++i)
        {
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
};
```

但是由于我们只需要最后一个元素的值，所以之前的值对于我们来说是没有用的，但是仍然占用着内存空间，所以这个程序还是可以优化的。只需要定义两个int变量来存放前两种情况的数值，不断累加，加到最后一次返回即可。

```C++
class Solution {
public:
    int climbStairs(int n) {
        if(n<=2)
            return n;
        
        int pre1 = 1, pre2 = 2, cur;
        for(int i(2); i<n; ++i)
        {
            cur = pre1 + pre2;
            pre1 = pre2;
            pre2 = cur;
        }
        return cur;
    }
};
```



###　动态规划原理

##### 最优子结构

用动态规划求解最优化问题的第一步是刻画最优解的结构， 如果一个问题的解的结构包含了其子问题的最优解，就称之为此问题具有最优子结构性质。因此，某个问题是否适合应用动态规划算法，该问题是否具有最优子结构的性质很重要。使用动态规划的时候，用子问题的最优解来构造原问题的最优解，因此必须考察最优解中用到的所有子问题。

##### 重叠子问题

在斐波那契数列的求解中，可以看到大量的重叠的子问题，比如在求6的斐波那契结果时，其实需要求5次2的斐波那契结果。如果在递归的时候需要大量地求解相同地子问题，就称之为具有重叠子问题性质。在动态规划求解的时候，就可以分配一段内存用来保存这些结果，重叠子问题求解的时候直接查表而不用调用函数递归。



### 动态规划的经典模型

##### 线性模型



##### 区间模型

区间模型的状态一般表示为`dp[i][j]`，表示的是在[i, j]区间上的最优解。通过状态转移方程计算出[i+1, j]或者[i, j+1]上的最优解，逐步扩大到全局的最优解。



##### 背包模型

**0/1 背包问题**

最基本的背包问题就是 01 背包问题：一共有 N 件物品，第 i（i 从 1 开始）件物品的重量为 w[i]，价值为 v[i]。在总重量不超过背包承载上限 W 的情况下，能够装入背包的最大价值是多少？

**完全背包问题**

完全背包与 01 背包不同就是每种物品可以有无限多个：一共有 N 种物品，每种物品有无限多个，第 i（i 从 1 开始）种物品的重量为 w[i]，价值为 v[i]。在总重量不超过背包承载上限 W 的情况下，能够装入背包的最大价值是多少？
可见 01 背包问题与完全背包问题主要区别就是物品是否可以重复选取。

###### 背包问题的特征

是否可以根据一个 target（直接给出或间接求出），target 可以是数字也可以是字符串，再给定一个数组 arrs，问：能否使用 arrs 中的元素做各种排列组合得到 target。

###### 背包问题的解法

**01 背包问题：**
如果是 01 背包，即数组中的元素不可重复使用，外循环遍历 arrs，内循环遍历 target，且内循环倒序:

**完全背包问题：**
（1）如果是完全背包，即数组中的元素可重复使用并且不考虑元素之间顺序，arrs 放在外循环（保证 arrs 按顺序），target在内循环。且内循环正序。
（2）如果组合问题需考虑元素之间的顺序，需将 target 放在外循环，将 arrs 放在内循环，且内循环正序。



### 常用状态转移方程

动态规划中的状态往往依赖于前一个阶段的状态或者前一个阶段的决策结果。一旦i的状态确定下来，那么i+1的状态也就是确定的了，所以解决动态规划的关键就是确定状态转移方程，只要有状态转移方程就可以通过迭代把整个dp数组填满。

常见的状态转移方程问题：[动态规划问题 - 经典模型的状态转移方程 - RunningSnail - 博客园 (cnblogs.com)](https://www.cnblogs.com/tgycoder/p/5037559.html)

##### 最长公共子串

```C++
dp[0][j] = 0; (0<=j<=m)
dp[i][0] = 0; (0<=i<=n)
dp[i][j] = dp[i-1][j-1] +1; (str1[i] == str2[j])
dp[i][j] = 0; (str1[i] != str2[j])
```



##### 最长公共子序列

子序列和子串的区别在于，子串必须是连续的，子序列只需要是顺序一致，并不需要一定是连续的。

```C++
dp[0][j] = 0; (0<=j<=m)
dp[i][0] = 0; (0<=i<=n)
dp[i][j] = dp[i-1][j-1] +1; (str1[i-1] == str2[j-1])
dp[i][j] = max(dp[i][j-1],dp[i-1][j]); (str1[i-1] != str2[j-1])
```



##### 最长递增/递减子序列

```c++
dp[1] = 1;
dp[i] = max{1,dp[j]+1|aj<ai && j<i}
```



##### 最大子序列和

假设有序列{a1,a2,...,an}，求子序列的和最大问题，我们用dp[i]表示以ai结尾的子序列的最大和。

```
dp[1] = a1; (a1>=0 && i == 1)
dp[i] = dp[i-1]+ai; (ai>=0 && i>=2)
dp[i] = 0; (dp[i-1] + ai <=0 && i>=2)
```



##### 树塔问题（动态问题）

给定一个数组`data[n][m]`构成一个数塔求从最上面走到最低端经过的路径和最大。可以假设`dp[i][j]`表示走到第i行第j列位置处的最大值，那么可以推出状态转移方程：

构成一个数塔求从最上面走到最低端经过的路径和最大。可以假设dp[i][j]表示走到第i行第j列位置处的最大值，那么可以推出状态转移方程：

```C++
dp[i][j] = max{dp[i-1][j-1],dp[i-1][j]} + data[i][j];
```



##### 0/1 背包问题

假设有N件物品和一个容量为V的背包。第i件物品的体积是v[i]，价值是c[i]，将哪些物品装入背包可使价值总和最大？

每一种物品都有两种可能即放入背包或者不放入背包。可以用`dp[i][j]`表示第i件物品放入容量为j的背包所得的最大价值，则状态转移方程可以推出如下：

```C++
dp[i][j]=max{dp[i-1][j-v[i]]+c[i],dp[i-1][j]};
```



#### LeetCode动态规划典型例题

[(5) Dynamic Programming Patterns - LeetCode Discuss](https://leetcode.com/discuss/general-discussion/458695/dynamic-programming-patterns#Minimum-(Maximum)-Path-to-Reach-a-Target)

###### 基本动态规划：一维

[70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

[198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

[413. 等差数列划分](https://leetcode-cn.com/problems/arithmetic-slices/)

###### 基本动态规划：二维

[64. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

[413. 等差数列划分](https://leetcode-cn.com/problems/arithmetic-slices/)

[542. 01 矩阵](https://leetcode-cn.com/problems/01-matrix/)

[221. 最大正方形](https://leetcode-cn.com/problems/maximal-square/)

###### 分割类

[279. 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

[91. 解码方法](https://leetcode-cn.com/problems/decode-ways/)

[139. 单词拆分](https://leetcode-cn.com/problems/word-break/)

###### 子序列问题

[300. 最长递增子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

[1143. 最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

###### 01背包问题

[416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

[494. 目标和](https://leetcode-cn.com/problems/target-sum/)

###### 完全背包问题

[139. 单词拆分](https://leetcode-cn.com/problems/word-break/)

[279. 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

[322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

[377. 组合总和 Ⅳ](https://leetcode-cn.com/problems/combination-sum-iv/)

[518. 零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)



## 剪枝

搜索算法中，很大一部分是通过使用DFS深度优先搜索和BFS广度优先搜索实现的，而这两种算法都是基于二叉搜索树实现的。

二叉树存在大量的分支，当数据的参数比较大的时候，二叉树的分支会大量增加，此时由于搜索过程会需要走大量不相关的搜索路径，所以产生了剪枝算法的思想。

剪枝：顾名思义通过删去一些不重要的节点，来减小搜索或者遍历的复杂度。

剪枝在很多算法中都有应用，比如决策树、神经网络、搜索、数据库涉及。

在决策树和神经网络中，剪枝可以有效缓解过拟合问题并减小计算复杂度；在搜索算法中，可以减小搜索范围，提高搜索效率。应用剪枝优化的核心问题是设计剪枝判断方法，即确定哪些枝条应当舍弃，哪些枝条应当保留的方法。

### 剪枝的三个原则：正确性、准确性、高效性

##### 正确性

剪枝不能把影响程序最终结果的几个分支剪掉，所以说剪枝最重要的前提是保证程序结果的正确性。

##### 准确性

在保证正确性的基础上,我们再对问题进行细致的分析之后，应该采用合适的判断手段,使除了最优解以外的枝条尽可能多的被剪去,以达到程序“最优化”的目的。 剪枝的准确性, 是衡量一个优化算法好坏的标准。

##### 高效性

设计剪枝和其他优化算法的根本目的，是为了减少搜索的次数，使得程序的运行时间减少。但是为了提高算法的准确性，又不得不增大算法判断的条件和次数，这样又会导致算法耗时的增加，于是就产生了矛盾。所以要在剪枝的时间复杂度优化找到一个平衡点，使得用于判断的时间和判断的效果都能达到理想的效果。

#### 剪枝的分类

##### 可行性剪枝

如果当前的状态和题意不符合，并且往后的情况都将与题意不符合，那么程序可以直接返回。

##### 排除等效冗余

当多个分支有同样的效果的时候，只需要选择其中一个分支进行遍历就可以达到效果。

##### 最优性剪枝

程序运行的过程中记录下最优解，之后的每一个解在运行过程中都与最优解比较，如果运行一半已经比最优解差，那么没有必要继续运行下去，立即停止搜索进行回溯。

##### 顺序剪枝

普遍来讲，搜索的顺序是不固定的，对一个问题来讲，算法可以进入搜索树的任意的一个子节点。但假如我们要搜索一个最小值，而非要从最大值存在的那个节点开搜，就可能存在搜索到最后才出解。而我们从最小的节点开搜很可能马上就出解。这就是顺序剪枝的一个应用。一般来讲，有单调性存在的搜索问题可以和贪心思想结合，进行顺序剪枝。

##### 记忆化

把每一次记录结果做一个记录，在搜索过程中发现同样的结果就直接返回。



## 千奇百怪的排序算法

### 排序算法的稳定性

假定在待排序的记录序列中，存在多个具有相同的关键字的记录，若经过排序，这些记录的相对次序保持不变，即在原序列中，r[i]=r[j]，且r[i]在r[j]之前，而在排序后的序列中，r[i]仍在r[j]之前，则称这种排序算法是稳定的。

![img](https://www.runoob.com/wp-content/uploads/2019/03/sort.png)

![img](https://www.runoob.com/wp-content/uploads/2019/03/0B319B38-B70E-4118-B897-74EFA7E368F9.png)

### 排序算法的时间复杂度

#### 时间复杂度为O(n^2)的排序算法

#### 冒泡排序

重复走访要排序的序列，两两比较顺序错误就交换，依此类推，直到走完要排序序列，这时最大（最小）数浮动到数列末尾。

#### 选择排序

在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

#### 插入排序

通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

### 时间复杂度为O(nlogn)的排序算法

#### 希尔排序

希尔排序是将待排序的数组元素按下标的一定增量分组 ，分成多个子序列，然后对各个子序列进行直接插入排序算法排序；然后依次缩减增量再进行排序，直到增量为1时，进行最后一次直接插入排序。

#### 堆排序

新建堆然后从堆中逐条取出顶元素放在最后，从后往前走一遍即可。

#### 快速排序

又称划分交换排序。快速排序使用分治法（Divide and conquer）策略来把一个序列（list）分为较小和较大的2个子序列，然后递归地排序两个子序列。

1. 挑选基准值：从数列中挑出一个元素，称为“基准”（pivot）。

2. 分割：重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（与基准值相等的数可以到任何一边）。在这个分割结束之后，对基准值的排序就已经完成。

3. 递归排序子序列：递归地将小于基准值元素的子序列和大于基准值元素的子序列排序。

#### 归并排序

1、分割：递归地把当前序列平均分割成两半。

2、集成：将上一步得到的有序子序列集成到一起（归并）。

### 时间复杂度为O(n)的排序

#### 计数排序

假设一个数组中出现过的最大值是max，最小值是min，那么构建一个[min, max]，长度为max-min+1的数组，原数组中的每个位置都能放一个元素组中的元素，在新数组中放置原数组中的那个元素出现过的次数，最后将这个给数组依次展开即可。

#### 桶排序

桶排序是计数排序的升级版。它利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。桶排序 (Bucket sort)的工作的原理：假设输入数据服从均匀分布，将数据分到有限数量的桶里，每个桶再分别排序（有可能再使用别的排序算法或是以递归方式继续使用桶排序进行排）。

#### 基数排序





### 代码部分

```c++
#include <vector>
#include <iostream>
#include <stdlib.h>
#include <algorithm>

using namespace std;

class Sort
{
public:
    void printVec(vector<int> &nums)
    {
        for (auto c : nums)
        {
            cout << c << " ";
        }
        cout << endl;
    }

    vector<int> bubbleSort(vector<int> &nums)
    {
        int n = nums.size();
        for (int i = 0; i < n; ++i)
        {
            bool flag = false;
            for (int j = n - 1; j > i; --j)
            {
                if (nums[j - 1] > nums[j])
                {
                    swap(nums[j - 1], nums[j]);
                    flag = true;
                }
            }
            if (!flag)
                return nums; // 意味着后面的没有顺序需要调换了
        }
        return nums;
    }

    vector<int> insertSort(vector<int> &nums)
    {
        int n = nums.size();
        for (int i = 1; i < n; ++i)
        {
            int cur = nums[i];
            int index = i - 1;
            while (index >= 0 && cur < nums[index])
            {
                nums[index + 1] = nums[index];
                index--;
            }
            nums[index + 1] = cur;
        }
        return nums;
    }

    vector<int> shellSort(vector<int> &nums)
    {
        const int INCRGAR = 3; // 先固定一个常量，用于gap = gap / INCRGAP
        int cur = 0;           // 还是插入排序思想中的用于存放当前的数字的变量，后面直接赋值给nums[index+1]
        int n = nums.size();
        int gap = n / INCRGAR + 1; // 需要保证gap至少为1
        while (gap)                // 只要gap不为零就一直循环，并且gap最后一次一定是1，即做一个插入排序
        {
            gap = gap / INCRGAR;
            for (int i = gap; i < n; i++)
            {
                int cur = nums[i];
                int index = i - gap;
                while (nums[index] > cur && index >= 0)
                {
                    nums[index + gap] = nums[index];
                    index -= gap;
                }
                nums[index + gap] = cur;
            }
            printVec(nums);
        }
        return nums;
    }

    vector<int> quickSort(vector<int> nums)
    {
        recursionPartition(nums, 0, nums.size() - 1);
        // 其实可以直接使用recursionPartition就算是一个递归快排，但是由于题目只让传参一个vector，所以再加一个函数
        return nums;
    }

    int partition(vector<int> &nums, int left, int right) // 一定要使用&nums作为引用，否则nums不会改变
    {
        int i = rand() % (right - left + 1) + left;
        swap(nums[left], nums[i]);
        // 这两步的原因，每次都使用最左边的数字作为pivot的话，对比起来耗时太大，leetcode中会超时
        // 在partition中随机抽一个作为pivot，把这个数字换到最右边
        int pivot = nums[left];
        // pivot的目的一个是用作基准值，另一个是存储nums[left]的值，因为这个值后面回直接被覆盖掉
        while (left < right)
        {
            while (left < right && nums[right] >= pivot)
                --right;
            nums[left] = nums[right];
            while (left < right && nums[left] <= pivot)
                ++left;
            nums[right] = nums[left];
        }
        nums[left] = pivot;

        return left;
    }

    void recursionPartition(vector<int> &nums, int left, int right) // 一定要使用&nums作为引用，否则nums不会改变
    {
        if (left < right)
        {
            int index = partition(nums, left, right);
            recursionPartition(nums, left, index - 1);
            recursionPartition(nums, index + 1, right);
        }
    }

    vector<int> selectSort(vector<int> &nums)
    {
        int n = nums.size();
        if (n <= 1)
            return nums;
        for (int i = 0; i < n; i++)
        {
            int index = findMin(nums, i);
            swap(nums[i], nums[index]);
        }
        return nums;
    }

    int findMin(vector<int> nums, int i)
    {
        int min = nums[i];
        int index = i;
        while (i < nums.size())
        {
            if (nums[i] < min)
            {
                min = nums[i];
                index = i;
            }
            ++i;
        }
        return index;
    }

    void heapAdjust(vector<int> &nums, int left, int right)
    {
        int dad = left;
        int son = 2 * left + 1; // heap排序的数据结构是一个完全二叉树，每个节点下面挂两个子节点
        while (son <= right)
        {
            if (son <= right - 1 && nums[son] < nums[son + 1])
                // 选取较大的来和父节点比较，如果较大的小于父节点，那么这两个子节点一定都小于父节点
                son++;
            if (nums[dad] > nums[son])
                // 如果父节点确实大于子节点，那么这个堆的顺序就是对的，就直接return
                return;
            else
            // 如果父节点没有大于子节点，那么父节点就去做子节点，并且继续向下对比孙节点，以此类推
            {
                swap(nums[dad], nums[son]);
                dad = son;
                son = 2 * dad + 1;
            }
        }
    }

    vector<int> heapSort(vector<int> &nums)
    {
        int n = nums.size();
        for (int i = (n / 2) - 1; i >= 0; i--) // 画一个完全二叉树的图就可以知道，最后一个父节点是(n/2)-1
        {
            heapAdjust(nums, i, n - 1);
            // printVec(nums);
        }
        for (int i = n - 1; i > 0; i--)
        {
            swap(nums[0], nums[i]);
            heapAdjust(nums, 0, i - 1);
        }
        return nums;
    }

    void merge(vector<int> &nums, int left, int mid, int right)
    // 实际上，left、mid、right三者将整个vector划分成两个区间：[left, mid] [mid+1, right]
    {
        int i = left;                       // 第一个区间的第一个指针
        int j = mid + 1;                    // 第二个区间的第一个指针
        vector<int> temp(right - left + 1); // 分配一个用于存放排好序数组的数组变量
        int k = 0;                          // 用于遍历temp数组
        while (i <= mid && j <= right)
        {
            if (nums[i] < nums[j])
                temp[k++] = nums[i++];
            else
                temp[k++] = nums[j++];
        }
        while (i <= mid)
            temp[k++] = nums[i++];
        while (j <= right)
            temp[k++] = nums[j++];

        for (int i = left, k = 0; i <= right; i++, k++)
        {
            nums[i] = temp[k];
        }
        temp.clear();
    }

    void mergeSort(vector<int> &nums, int left, int right)
    // 和下面的mergeSort名字一样，传参不一样，返回值也不一样，函数重载了
    {
        if (left < right)
        {
            int mid = left + (right - left) / 2;
            mergeSort(nums, left, mid);
            mergeSort(nums, mid + 1, right);
            merge(nums, left, mid, right);
        }
    }

    vector<int> mergeSort(vector<int> &nums)
    {
        int left = 0, right = nums.size() - 1;
        mergeSort(nums, left, right);
        return nums;
    }

    vector<int> bucketSort(vector<int> &nums)
    {
        int n = nums.size();
        if (n <= 1)
            return nums;
        int min = INT_MAX; // 2147483647
        int max = INT_MIN; //-2147483648
        for (auto c : nums)
        {
            if (c > max)
                max = c;
            if (c < min)
                min = c;
        }

        int bucketNum = (max - min) / n + 1;
        // 桶的数量是(max-min)/length+1;
        vector<vector<int>> buckets(bucketNum);
        for (int i = 0; i < n; i++)
        {
            int k = (nums[i] - min) / n;
            // 确定这个元素属于哪个桶
            buckets[k].push_back(nums[i]);
            // 把这个元素放到对应的桶里去
        }
        int count = 0;
        for (int i = 0; i < bucketNum; ++i)
        {
            if (!buckets[i].empty())
            {
                sort(buckets[i].begin(), buckets[i].end());
                for (int j = 0; j < buckets[i].size(); ++j)
                {
                    nums[count++] = buckets[i][j];
                }
            }
        }
        return nums;
    }
};

int main()
{
    vector<int> nums;
    for (int i = 0; i < 10; i++)
    {
        int num = rand() % 20;
        nums.push_back(num);
    }

    Sort sort;
    sort.printVec(nums);
    vector<int> res;
    //res = sort.bubbleSort(nums);
    //res = sort.insertSort(nums);
    //res = sort.shellSort(nums);
    //res = sort.quickSort(nums);
    //res = sort.selectSort(nums);
    //res = sort.heapSort(nums);
    //res = sort.mergeSort(nums);
    //res = sort.bucketSort(nums);
    sort.(nums.begin(), nums.end());

    sort.printVec(nums);
}
```



## Trie前缀树/字典树

Trie树，即字典树，又称之为单词查找树，是一种树形结构，是哈希树的变种。典型应用是用于统计和排序大量的字符串，经常被用于搜索引擎系统用于文本词频统计。优点是最大限度地减少无谓地字符串比较。

Trie的核心思想是空间换时间。利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的。

前缀树的3个基本性质：

1. 根节点不包含字符，除了根节点之外的每个节点都只包含一个字符。
2. 从根节点到某一节点，路径上经过的所有字符连接起来，为该节点对应的字符串。
3. 每个节点的所有子节点包含的字符都不相同。

字典树查询的时间复杂度是O(logL)，L是字符串的长度。

哈希表的查询效率是O(1)，但是对比字典树和哈希表要从多个方面比较。

如果哈希表的哈希函数选用的好，计算量少，并且哈希冲突比较少，那么单词查询的速度肯定是很快的。但是如果哈希函数计算量大，数据量大，冲突多，哈希表的性能就不能保证。 

哈希表不支持动态查询，当我们在搜索引擎中搜索的时候，往往都是动态搜索，打出一个字符已经会在匹配下一个字符，但是哈希表必须等待用户全部打完搜出来的结果才是有效的。

```cpp
#include <vector>
#include <iostream>
#include <set>
using namespace std;

class SolutionBacktracking // DFS加剪枝回溯的方法虽然可以，但是在大量重复样本的情况下会超时
{
protected:
    vector<string> res;
    set<string> s;
    int col, row;

    void dfs(vector<vector<char>> &board, string word, int index, int i, int j)
    {
        if (i < 0 || j < 0 || i >= row || j >= col || board[i][j] != word[index] || board[i][j] == '.')
            return;

        if (index == word.size() - 1)
        {
            s.insert(word);
            return;
        }

        char tmp = board[i][j];
        board[i][j] = '.';
        dfs(board, word, index + 1, i - 1, j);
        dfs(board, word, index + 1, i + 1, j);
        dfs(board, word, index + 1, i, j - 1);
        dfs(board, word, index + 1, i, j + 1);
        board[i][j] = tmp;
    }

    void find(vector<vector<char>> &board, string word)
    {
        for (int i(0); i < row; ++i)
            for (int j(0); j < col; ++j)
                if (board[i][j] == word[0])
                    dfs(board, word, 0, i, j);
                else
                    continue;
    }

public:
    vector<string> findWords(vector<vector<char>> &board, vector<string> &words)
    {
        row = board.size();
        col = board[0].size();
        for (string word : words)
            find(board, word);

        res.assign(s.begin(), s.end());
        return res;
    }
};

class TrieNode // 构造一个Trie树
{
public:
    string word = "";
    vector<TrieNode *> nodes;
    TrieNode() : nodes(26, 0) {}
    ~TrieNode()
    {
        for (const auto &node : nodes)
            delete node;
    }
};

class Solution
{
private:
    vector<string> res;
    int row, col;

    void dfs(vector<vector<char>> &board, TrieNode *root, int i, int j)
    {
        if (i < 0 || j < 0 || i >= row || j >= col)
            return;
        char tmp = board[i][j];
        if (board[i][j] == '.' || root->nodes[tmp - 'a'] == 0)
            return;
        board[i][j] = '.';
        root = root->nodes[tmp - 'a'];
        if (root->word != "")
        {
            res.push_back(root->word);
            root->word = "";
        }
        dfs(board, root, i + 1, j);
        dfs(board, root, i - 1, j);
        dfs(board, root, i, j + 1);
        dfs(board, root, i, j - 1);
        board[i][j] = tmp;
    }

public:
    vector<string> findWords(vector<vector<char>> &board, vector<string> &words)
    {
        row = board.size();
        col = board[0].size();
        if (!row || !col)
            return res;

        TrieNode *root = new TrieNode(); // 创建一个字典树节点

        for (string word : words)
        {
            TrieNode *cur = root;
            for (int i(0); i < word.size(); ++i)
            {
                int index = word[i] - 'a';
                if (!cur->nodes[index])
                    cur->nodes[index] = new TrieNode();
                cur = cur->nodes[index];
            }
            cur->word = word;
        }
        for (int i(0); i < row; ++i)
            for (int j(0); j < col; ++j)
                dfs(board, root, i, j);

        //delete root;
        return res;
    }
};

int main()
{
    vector<vector<char>> board = {
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
        {'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a', 'a'},
    };
    Solution sol;
    vector<string> wordList{"a", "aa", "aaa", "aaaa"};
    vector<string> res = sol.findWords(board, wordList);
    cout << res.size() << endl;
}
```



