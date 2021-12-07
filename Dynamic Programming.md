# 动态规划

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

